# C++特別筆記
## 不定長度引數
可以用兩種方法達到：
1. 使用 vector 定義參數，而呼叫方使用 vector 收集引數後，再來呼叫函式
2. 定義參數型態為initializer_list，透過清單初始化（list initialization）達成。
以上兩種方法都差不多，可以參考如下程式碼：
```c
#include <iostream>
#include <initializer_list> 
using namespace std; 

void foo(initializer_list<double>); 

int main() { 
    foo({1.1, 2.2, 3.3});

    return 0; 
} 

void foo(initializer_list<double> args) { 
    for(auto arg : args) {
       cout << arg << endl; 
    } 
}
```
3. 也可以使用C風格的不定參數，詳細參考[C語言筆記](/PiQ7fSpdRRW_4-PHIJSpvQ)。
4. 利用C++的可變參數模板
詳細參考[此處](https://openhome.cc/Gossip/CppGossip/VariadicTemplate.html)

如：
```c
template <typename ... Types>
void foo(Types ... params)
{
    cout << sizeof...(Types) << " "
         << sizeof...(params) << endl;
}
```
其中```typename```之後接續了省略語法```...```，
這可以看成```Types```代表不定長度的```typename T1, typename T2, ...```。

Types被稱為模版參數包（template parameter pack），
宣告參數時```Types ... params```代表了不定長度的```T1 t1, T2 t2, ...```，
params被稱為函式參數包（function parameter pack）。

註：```sizeof...(Types)```的...必須在括號前

可以使用sizeof... 來得知實際呼叫時的型態數量或引數數量，這個值是編譯時期推斷得知的，根據範例中呼叫的方式，在編譯時期 foo 會被實例出 foo(int)、foo(int, const char*) 與 foo(int, const char*, double) 版本。

如果呼叫時的引數是同一型態，一個簡單的方式是展開為陣列、vector 等型態。例如：
```c
#include <iostream> 
#include <vector> 
using namespace std; 

template <typename T, typename ... Ts>
T sum(T first, Ts ... rest) {
    vector<T> nums = {rest...};
    T r = first;
    for(auto n : nums) {
        r += n;
    }
    return r;
}

int main() { 
    cout << sum(1, 2, 3) << endl;
    cout << sum(1, 2, 3, 4, 5) << endl;
    return 0; 
} 
```
以上程式輸出：
```
6
15
```
T被實例化成int，Ts也被實例化成int，而rest是函數參數包。
在編譯時期，上面的範例會產生sum(int, int, int)與sum(int, int, int, int, int)兩個版本。
其中，{rest...}用來解開參數包，解開之意是指{rest...}會分別產生{p1, p2, p3}與{p1, p2, p3, p4, p5}（p1 等名稱代表參數）。

如果實際上傳遞的引數型態各不相同，必須使用遞迴來解決。例如：
```c
#include <iostream> 
using namespace std; 

template <typename T>
void print(T p) {
    cout << p << endl;
}

template <typename T, typename ...Ts>
void print(T first, Ts ... rest) {
    cout << first << " ";
    print(rest...);
}

int main() { 
    print(1);
    print(2, "你好");
    print(3, "安0.0安", 3.14);

    return 0; 
} 
```
以上程式碼輸出：
```
1
2 你好
3 安0.0安 3.14
```
其中，
print(1)會用第一個模板實例化print(int)函數；
print(1, "2")會先實例化第二個模板成print(int, const char*)版本，
然後print(rest..)的部份會解開為print("2")，這又會用第一個模板實例出 print(const char*)，
print(3, "安0.0安", 3.14)也是以此類推。

## extern關鍵字
https://xyz.cinc.biz/2013/04/c-extern.html

## Structred binding
例如以往要把pair中的元素分別取出，需要寫：
```c
pair<int, float> p{1, 3.14};
int x = p.first;
float y = p.second;
```
C++17可以直接寫成：
```c
const auto& [x, y] = p;	// must be auto
```
另外也可以使用std::tie
```c
int a = 3, b = 4;
auto [x, y] = std::tie(a, b)
```
相當於
```c
int & x = a;
int & y = b;
```
也可以用再檢查set的插入是否成功，以往需要寫成：
```c
set<int> s;
auto kv = s.insert(x);
auto it = kv.firstl;	// iterator
bool success = kv.second;	// inserted or not
```
現在可以直接使用：
```c
set<int> s;
const [it, success] = s.insert(x);
```

另外，需要注意的是，
structrued binding可以拆的有pair、array等等，但不能拆vector，因為vector的長度並不固定。

如：
```c
vector<pair<string, int>> v1{{"abc", 1}, {"bbb", 2}};
auto [k, v] = v[0];	// ok
vector<array<int, 3>> v2{{1,2,3}};
auto [a, b, c] = v2[0];	// ok
vector<vector<int>> v3{{1,2,3}};
auto [x, y, z] = v3[0];	// compile error
```

結合range based的話(參考C++11新特性筆記)，可以達成以下效果：
```c
map<string, int> m;

for (auto && [key, val] : m)
	cout << key << " " << val << endl;
```

## std::string_view用法

可以用來看一個string物件，但不能修改它，效率很高。
其實就是：
```c
std::string_view = const char* + length
```

好處：輕量話、不用拷貝、支持STL
壞處：不可改、有生命週期問題、不可以作字串聯接(rope need)

例如：
```c
string s(100, 'a');	// 100個a的字串
string ss = s.substr(10, 20);	// O(n) copy，第10到第19
```
如果ss不需要被修改，可以改用string_view：
```c
string_view v(s);	// no copy, viewing into s
string_view vv = v.substr(10, 20)	// O(1) no copy
```
刷題時若需要大量檢查子字串，盡量使用string_view來實現。

## template of template
巢狀template，主要是用在template的typename本身也是模板class時所使用的。
通常我們一般template都是這樣使用的：
```c
template<typename DType>
class CData
{
public:
    std::vector<DType> mContent;
};
```
在上面的類別CData裡，是用一個std::vector 來儲存資料，而裡面的型別則是DType；使用的時候，基本上會是：
```c
CData<int> x;
```
但是，如果希望可以讓使用者決定要用哪種STL容器（container）時該怎麼辦呢？比較直覺的方法，就是寫成：
```c
template<typename DType, typename TContainer>
class CData
{
public:
    TContainer<DType> mContent;
};
```
希望使用時可以如下：
```c
CData<int,std::vector> x;
// 或是使用deque
CData<int,std::deque> x;
```
但這樣做是行不通的，因為vector本身也是一個帶有template的類別。
因此使用巢狀template的用處就在此，將模板類別改為如下：
```c
template<typename DType,
            template<typename T> class TContainer>
class CData
{
public:
    TContainer<DType> mContent;
};
```
在這邊，就是很明確地告訴編譯器，TContainer 是一個template的類別，而他有一個template的參數。這樣一來，就可以任意地套用符合形式的容器了。

不過，如果是要使用STL的vector或deque的話，由於它們實際上有兩個template參數（參考），所以直接拿來用會因為template參數不相同、而有編譯階段的錯誤，
還要再加上```allocator<T>```的模板才行：
```c
template<typename DType, template<typename E, typename = std::allocator<E>> class TContainer>
class CData
{
public:
    TContainer<DType> mContent;
};
```
註：模板參數可以有預設值，跟函數參數一樣使用'='號

此時，上述的程式碼大意就是說，這個類別CData有兩個模板參數DType和TContainer，
而TContainer本身也是含有兩個模板參數的一個模板類別，
其中我們使用哪種型別傳進去TContainer建構子來建立這個mContent？
看到```TContainer<DType> mContent;```這行，
我們是把DType用來當成TContainer模板的第一個E參數，而```std::allocator<E>```則是它的第二個模板參數，that's all！

參考資料[[1]](https://kheresy.wordpress.com/2014/10/16/c-template-of-template/)、[[2]](https://liam.page/2018/03/16/keywords-typename-and-class-in-Cxx/)

## explicit關鍵字

用來標註一個建構子或函式是不是可以接受隱式的轉換，最常用的兩種用途：

### 加在 constructor 前面
最基本最常見的應該是在說一個類別為了避免手殘在沒有意想到的地方發生隱式轉換(implicit conversion)，所以要求在constructor面前掛上explicit關鍵字，表示這個構建方式需要明確宣告。
```c
class C
{
prviate:
	int n_;
	char *s_;
public:
	C(int n): n_{n}{}
	explicit C(const char* s)
	{
		strcpy(s_, s);
	}
};

int main()
{
	C c1= 10;
	C c2{"abcd"};
}
```
可以看到，沒有標上explicit的constructor吃了一個int，所以main()裡頭的c1可以順利地用copy-initialization的方式宣告。
但吃字串常量的constructor被標記成explicit，所以沒辦法直接以C cabc = "abc"的方式宣告。

### 物件轉型
物件轉型函式如```operator bool()```、```operator int()```等等，
如果加上了explicit關鍵字：
```c
class IsEven
{
public:
	IsEven(int n): n_{n}{}
	operator bool() const { return n_ % 2 == 0;}
	int n_;
};

int main()
{
	IsEven i0{0}, i1{1}, i2{2};
	if(i0 && i2)
		cout << i0.n_ << " and " << i2.n_ << "are even numbers!";
	if(!i1)
		cout << i1.n_ << "isn't an even number!";
	return 0;
```
IsEven類別加了一個 bool轉型operator，所以這些IsEven的實例們就可以丟到if裡面各種判斷運算，即contextually converted to bool的功能。
然而，當我們加上explicit時，就必須加上明確利用```static_cast<bool>```來轉型才可以。
例如以下例子：
```c
class C
{
prviate:
	int n_;
public:
	C(int n): n_{n}{}
	operator double() const { return 10.; }
	explicit operator const char* () const { return "abc"; }
};

int main()
{
	C i2{2};
	
	double d2 = i2;
	
	const char * s2 = static_cast<const char *>(i2);
	
	cout << d2 << endl;	// 印出10
	cout << s2 << endl;	// 印出"abc"
}
```

## const與指標的關係
1. ```const char *pContent```
   相當於```const (char) *pContent```，
   指針本身是常量不可變。
2. ```char * const pContent```
   相當於```(char*) const pContent```，也可以寫成```const (char*) pContent```，
   指標所指向的內容是常量不可變。
3. ```char const *pContent```
   相當於```(char) const *pContent```，
   指針本身是常量不可變。
4. ```const char* const pContent```
   表示指標本身和指標內容兩者皆為常量不可變。
  
判別方法：
利用const在```*```號左邊還是右邊來決定，
在左邊的const就是用來修飾指標所指向的變數，即指標指向為常量；
在右邊的const則是修飾指標本身，即指標本身是常量。 

用在函數回傳值：
```
const int * fun1()

int* const fun2()
```
其中，fun1的回傳值是一個指向const int的指標，且指標本身可以修改所指對象，但不可透過此指標去修改int的值。
fun2的回傳值是一個指向int的指標，且指標本身不可變。
因此：
```c
operator const char* () const { return "abc"; }
```
指得是回傳值的```char *```指標所指內容不可變，且這個函式本身是const函數(該物件被宣告為const時仍可以執行的函數)。

## mutable
參考資料：[這裡](https://liam.page/2017/05/25/the-mutable-keyword-in-Cxx/)

用途有兩種：
1. 讓const成員函數可以修改類別內宣告為mutable的變數
2. 讓lambda表示式可以修改capture by value的變數值

### 第一種用法：

原本的const成員函數是不允許修改任何成員變數的，而C++把const分成兩種概念：
一種是二進制的常數，也就是絕對的「常數」，在任何情況下都不可修改（除非用const_cast）。
而C++自從引入mutable的概念之後，C++可以有邏輯層次的const，也就是對一個常數實例(const instance)來說，從外部觀察，它是常數而不可修改；但是內部可以有非常數的狀態。

例子：
```c
class HashTable {
 public:
    // ...前略
    string lookup(const string& key) const
    {
        if (key == last_key_) {
            return last_value_;
        }

        string value{this->lookupInternal(key)};

        last_key_   = key;
        last_value_ = value;

        return value;
    }

 private:
    mutable string last_key_;
    mutable string last_value_;
};
```

這裡，我們用了last_key_和last_value把最新一次查詢的hash值cache起來，如果下一次又查詢到相同的key，可以加快查詢速度
原本的lookup成員函數是宣告為const，這很正常，因為查詢hash table的值本來就不該去修改hash value。
但是，另一方面，last_key_和last_value_從邏輯上說，修改它們的值，外部是的使用者是感覺不到的，
因此也就不會破壞邏輯上的const。為了解決這一矛盾，我們用mutable來修飾last_key_和last_value_，以便在lookup函數中更新被cache起來的鍵值。

### 第二種用法：

在Lambda表示式的設計中，要抓Lambda函數體外的變數有幾種使用Lambda Capture Clause的方式：
1. ```[=]```(Capture by Value)
2. ```[&]```(Caputre by Reference)

其中按值擷取(Caputre by Value)的方式不允使用者在Lambda函數的函數體中修改捕獲的變數。

而以mutable修飾Lambda函數，則可以打破這種限制。
例子：
```c
int func()
{
	int x{0};
	auto f1 = [=]() mutable {x = 42;};  // okay，創建了一個函數類型的實例
	auto f2 = [=]()         {x = 42;};  // error，不允許修改Caputre by Value的變數
	return x;	//
}
```
上述例子的返回值為0，原因在於雖然使用了mutable使得x可以被修改，但是```x = 42```改的是Lambda內部的x，外部func的x不受影響。
