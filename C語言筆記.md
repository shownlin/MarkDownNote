# C語言筆記

## Makefile
valgrind:
用來找Memory leak，Valgrind是GNU/Linux上相當知名的記憶體檢查軟體，在Ubuntu下面可以直接用sudo apt-get install valgrind安裝。但Valgrind新版已經不支援Mac了，可用clang的AddressSanitizer 來[代替](https://michaelchen.tech/data-structures-in-c/practice/)。

gcc的參數：
|   參數flag   | 功能                                          |
|:------------:| --------------------------------------------- |
| -o filename  | 指定輸出檔名                                  |
|     -O2      | 做第二級最佳化，可以從0～4，0為完全不做最佳化 |
|      -g      | 編入除錯資訊(要使用GDB除錯一定要加)           |
|    -Wall     | 顯示所有的警告訊息，另外可以用-w和-W          |
|     -lm      | 有用到math.h時要加入這個參數                  |
|      -w      | 忽略所有的警告訊息                            |
|      -W      | 只顯示compiler認為會出錯的警告                |
|      -c      | 只做編譯(不做連結)                            |
|      -S      | 輸出組譯碼                                    |
|      -E      | 將預處理結果顯示                              |
|    -ansi     | 程式要求依據ansi c標準                        |
|   -Dmacro    | 使定義巨集(marco)為有效                       |
| -Dmarco=defn | 使定義巨集(marco)為defn                       |
|  -Wa,option  | 將選項(option)傳給組譯器                      |
|  -wl,option  | 將選項(option)傳給連結器                      |
|      -I      | 加include檔案的搜尋路徑                       |
|      -L      | 追加library檔案的搜尋路徑                     |


簡單範例(利用 make out=檔案名.c 來編譯)：
```
CC = gcc
MEM_CHECK = valgrind
RM = rm
RMFLAG = -rf
TARGET = $(out)

all: run

check: compile
	$(MEM_CHECK) ./$(TARGET)
	echo $$?

run: compile
	./$(TARGET)
	echo $$?

compile:
	$(CC) -Wall  -g -o $(TARGET) $(TARGET).c

clean:
	$(RM) $(RMFLAG) *.exe

```

## 預處理器(C language preprocessor)

用來避免標頭檔被二次插入造成重複宣告：
```c
#ifdef 識別符號
程式段1
#else
程式段2
#endif
```
識別符號就是標頭檔名稱，標識的命名規則一般是頭文件名全大寫，前後加下劃線，並把文件名中的「.」也變成下劃線，如：stdio.h
```c
#ifndef _STDIO_H_
#define _STDIO_H_
```

### 內定義的巨集

ANSI C定義了許多可在C語序中使用的預定義的巨集：

| 巨集名稱       | 功能                                  |
| -------------- | ------------------------------------- |
| ```__DATE__``` | 以「MMM DD YYYY」格式表示當前日期。   |
| ```__TIME__``` | 以「HH:MM:SS」格式表示當前時間。      |
| ```__FILE__``` | 表示當前檔案名稱。                    |
| ```__LINE__``` | 表示此行程式碼在文件中的第幾行。      |
| ```__STDC__``` | 當編譯器符合ANSI標準時，它被定義爲1。 |

## C語言預處理器指令

有以下這幾種：
* #include
* #define
* #undef
* #ifdef
* #ifndef
* #if
* #else
* #elif
* #endif
* #error
* #pragma

C語言預處理器是編譯器在編譯之前轉換代碼的微處理器。它之所以被稱爲微預處理器，因爲它允許我們添加巨集(marco)。
:star:注意：預處理器指令在編譯之前執行。

## marco

巨集的作用就是在使用巨集的地方，直接換上預定義的程式碼。
```#define```用來定義巨集，相對地，```#undef```用來取消巨集。
巨集由#define指令定義。有兩種類型的巨集：

* 類似物件的巨集
* 類似函數的巨集

1. 類似物件的巨集是一種被值替換的標識符。它廣泛用於表示數字常數。
   例如：
   ```c
   #define PI 3.14
   ```

   當然也可以用來定義一個型態，如：
   ```c
   #define uint32_t usigned int
   ```
   但不建議用來定義指標，因為預處理器只是幫你在該處展開相對應的程式碼，如：
   ```c
   #define int_ptr int*
   
   int_ptr p1, p2;
   ```
   則p1會是指標，而p2只是一個int型態的變數而已，因為會被展開成：
   ```c
   int *p1, p2
   ```
   
2. 類似函數的巨集看起來像函數調用。
   例如：
   ```c
   #define MIN(a,b) ((a)<(b)?(a):(b))
   ```
   此處，MIN是巨集的名稱。
   還有數值的SWAP也可以用巨集來寫：
   ```c
   #define SWAP(x, y) \  
    (y) = (x) + (y); \  
    (x) = (y) - (x); \  
    (y) = (y) - (x);  
   ```
   當巨集函數的內容跨越多行時，每行結尾必須使用```\```，
   :star:用巨集定義函數，每個傳入的參數都必須要用括號```()```刮起來，以避免不必要之錯誤，在函數最外層也要刮起來。
   如果參數沒刮，運算式中的優先順序可能會被影響，例如：
   ```c
   #define MUL(x, y) (x * y)
   ```
   則遇到
   ```c
   MUL(10 + 2, 5);
   ```
   原本預期應該要得到60，但是因為程式被展開成：
   ```c
   (10 + 2 * 5)
   ```
   反而得到20的結果。
   如果外層沒刮起來也是同樣的道理。
   
   另外，巨集函數的傳入參數必須小心不要傳入重複的輸入項目，以免發生副作用，如：
   ```c
   #define pow(a) ((a) * (a))
   ```
   則遇到：
   ```c
   int x = 10;
   int y = pow(x++);
   ```
   會被進一步展開成：
   ```c
   int y = ((x++) * (x++));
   ```
   重複用了兩次遞增運算子，造成編譯錯誤。
   
   最後，跨越多行的define強烈建議使用do while(0)包起來，如上面的swap，就可以改成：
   ```c
   #define SWAP(x, y) do{\  
    (y) = (x) + (y); \  
    (x) = (y) - (x); \  
    (y) = (y) - (x); \
	} while(0)
   ```
   小心while後面不要不小心加上```;```分號
   
   參考資料：[這裡](http://catforcode.com/define-and-macro/)
   
## #字號在define中的特殊用途(井字)

1. ```＃```:在巨集展開的時候會將#後面的參數替換成字串，即加上雙引號，如：
   ```c
   ＃define p(exp) printf(#exp);
   ```
   調用p(asdfsadf)的時候會將#exp換成"asdfsadf"

2. ```##```:將前後兩個的單詞拼接在一起。例如《The C Programming Language》中的例子：
   ```c
   #define cat(x,y) x##y
   ```
   調用cat(var, 123)展開後成為var123。
   ##的作用是合併項目，例如若項目是a與b，
   巨集中撰寫ab是不會分別展開的，因為項目必須使用空白區隔，這時可以撰寫a##b，這麼一來，a與b會分別展開後合併。
   要注意，展開是真正意義上的程式碼展開，而不是字串的拼接。

3. ```#@```:將值序列變為一個字符，即加上單引號：
   ```c
   #define ch(c) #@c
   ```
   調用ch(a)展開後成為'a'.
   

其中關於第二點，##的用法有一些特別的例子，如：
```c
#include "stdio.h"

#define F(n, a) int f##n() {return a;}
F(test, 0);
F(function, 1);
F(rrr, 2);

int main() {
    printf("%d\n", ftest());
    printf("%d\n", ffunction());
    printf("%d\n", frrr());
    
    return 0;
}
```
輸出：
```
0
1
2
```
理由：
```c
F(test, 0);
n被當成一個參數，被展開成一個連結項目：int ftest()
而a則是被0替換，
另外兩個也是同理。
```

## #include指令
include指令用於將給定文件的程式碼導入(黏貼)到當前文件中。
它用來包括系統定義和用戶定義的標頭檔。如果未找到能包含的文件，則編譯器會顯示錯誤。

有兩種變體：
```c
#include <header.h>

#include "header.h"
```
第一種用尖括號刮起來，代表編譯器自帶的系統標頭檔，例如```#include<stdio.h> ```，stdio就是編譯器自帶的標頭檔，而非使用者自己定義的。

第二種使用雙引號刮起來，代表使用者自定義的檔案優先，例如：
```#include "my.h"```，假如目前的工作環境是在D:\Projects\tmp\，則編譯時，預處理器會去尋找D:\Projects\tmp\my.h這個檔案，如果找不到，還是會去對應的編譯器引用目錄裡面找my.h這個檔案。

## #define和#undef的用法
#undef預處理指令用於取消通過#define定義的常數或巨集。
一個簡單的例子：
```c
#include <stdio.h>  
#define PI 3.14  
#undef PI  
main() {  
   printf("%f",PI);  
}
```
編譯會出現：
```
Compile Time Error: 'PI' undeclared
```
```#define```也可以在compile時透過編譯器的選項```-D```給定：
```
gcc -DYES \marcroP.c
```
則以下程式碼：
```c
#include "stdio.h"

int main()
{
#ifdef YES
        printf("I'm happy!");
#else
        printf("I love you!");
#endif
        return 0;
}
```
印出：
```
I'm happy!
```

## #ifdef、#else和#endif指令
#ifdef預處理程序指令檢查巨集是否由#define定義。
如果是，則執行代碼，否則#else代碼執行(如果存在)。
例子：
```c
#include <stdio.h>  

#define NOINPUT  
void main() {
    int a = 0;
#ifdef NOINPUT  
    a = 2;
#else  
    printf("Enter a:");
    scanf("%d", &a);
#endif         
    printf("Value of a: %d\n", a);
}
```
印出：
```
Value of a: 2
```

## #ifndef指令
與#ifdef相反，檢查巨集是否爲未由#define定義。如果是，則執行代碼，否則#else代碼執行(如果存在)。

## #if指令
如果#if的條件爲假，#else預處理程序指令會計算表達式或條件。 它可以與#if，#elif，#ifdef和#ifndef指令一起使用。
語法：
```c
#if expression  
    //if code  
#else  
    //else code  
#endif
```

搭配#elif的語法：
```c
#if expression  
    //if code  
#elif expression  
    //elif code  
#else  
    //else code  
#endif
```
例子：
```c
#include "stdio.h"
#define NUMBER 100

void main() {

#if (NUMBER==10)
    printf("Value of Number is: 10");
#else
    printf("Value of Number is: %d", NUMBER);
#endif
}
```
印出：
```
Value of Number is: 100
```
切記，上述程式中的NUMBER只能是由define所定義的，因為這是必須在預處理階段就完成的if和else判斷。

## #error指令用法
#error預處理程序指令用於指示錯誤。如果找到#error指令編譯器將發出致命錯誤，並且並且終止接下來的編譯過程。
```c
#include "stdio.h"

#ifndef PI  
#error First include then compile  
#else  
void main() {
    float a = 1000.999;
    printf("b = %f\n", a);
}
#endif
```
編譯結果：
```
Compile Time Error: First include then compile
```
上述的程式要求必須先#define PI才可以正常編譯。

## #pragma預處理指令
#pragma預處理指令用於向編譯器提供其他信息，是最複雜的預處理指令，因為不同的編譯器可能有不同的使用方式。
pragma指令由編譯器用於提供機器或操作系統功能。

不同的編譯器可以提供不同的#pragma指令的使用，常用的有：

```#pragma pack```
用來指定struct結構內部資料的儲存對齊方式的預處理指令，會直接影響 struct 結構所使用的記憶體空間大小，以及每個內部變數的放置位置，在處理低階資料結構（例如網路封包）時時常會需要使用到這個語法。

範例：
```c
#include "stdio.h"

typedef struct {
  unsigned char      v1;
  unsigned int       v2;
  unsigned long long v3;
} myStrA;

void main() {
  // 輸出每個變數類型的大小
  printf("Size of unsinged char = %d\n", sizeof(unsigned char));
  printf("Size of unsigned int = %d\n", sizeof(unsigned int));
  printf("Size of unsigned long long = %d\n", sizeof(unsigned long long));
  printf("Size of myStrA = %d\n", sizeof(myStrA));

  // 建立方便辨識的測試資料
  myStrA a;
  a.v1 = 0x11;
  a.v2 = 0x22334455;
  a.v3 = 0x66778899AABBCCDD;

  // 輸出 myStrA 內部實際的資料
  unsigned char *ptr = (unsigned char *) &a;
  int i, s = sizeof(myStrA);
  printf("a = ");
  for (i = 0; i < s; ++i) {
    printf("%02X ", ptr[i]);
  }
  printf("\n");
}
```
使用 gcc 按照一般的的方式編譯並執行：
```
gcc -o pack pack.c
./pack
```
輸出：
```
Size of unsinged char = 1
Size of unsigned int = 4
Size of unsigned long long = 8
Size of myStrA = 16
a = 11 04 01 00 55 44 33 22 DD CC BB AA 99 88 77 66
```
理論上它們只需要1 + 4 + 8 = 13個位元組就夠了，不過我們用sizeof查看myStrA大小的結果卻是16，多出了 3 個位元組。
因為被編譯器給自動對齊了，這就是所謂的記憶體對齊（alignment），用以加快CPU抓資料的速度。

以gcc來說```#pragma pack```可設定最大可使用的記憶體封裝長度，
如果要在封裝記憶體時，完全不要留空洞，可以將記憶體封裝長度設為1：
```c
// 加入 pack 設定
#pragma pack(1)
typedef struct {
  unsigned char      v1;
  unsigned int       v2;
  unsigned long long v3;
} myStrA;
```
輸出結果：
```
Size of unsinged char = 1
Size of unsigned int = 4
Size of unsigned long long = 8
Size of myStrA = 13
a = 11 55 44 33 22 DD CC BB AA 99 88 77 66
```
這個用法特別在要寫讀某些特殊檔案如：BMP之類的圖檔等等，要根據規格刻file head時特別有用。
另外，這個```#pragma pack```的語法也同樣適用於 union 與 class。
參考資料：[這裡](https://blog.gtwang.org/programming/c-language-pragma-pack-tutorial-and-examples/)

## malloc、free、calloc 與 realloc

需要```#include "stdlib.h"```
分配空間範例：
```c
int *p1 = malloc(sizeof(int) * 5); // 分配5個int的大小
int *p2 = calloc(5, sizeof(int)); // 分配5個int的大小，並初始化為0
```
收回空間範例：
```c
free(p1);	// 釋放p1所指的空間
```
重新分配空間大小：
```c
int *arr1 = calloc(99, sizeof(int)); // arr1分配99格int的空間大小，並初始化為0
int *arr2 = realloc(arr1, sizeof(int) * size * 2); // 將arr1複製到arr2分配的新空間，並釋放arr1
```
其中，arr1的舊指標不能再使用，必須使用arr2的新位址。

另外，若有某個空間地址只能被唯一一個指標所指，可以用restrict來標記，給compiler做優化：
```c
int *restrict p = calloc(1, sizeof(int));
```
則p指標所指位置不能經由其他指標來存取。

## assert斷言

需要```#include "assert.h"```
當自己寫的函式庫要提供他人使用時，適當的利用維護敘述，可以建立安全的使用介面，避免他人因為使用不當，而造成不可預期的後果。
例如：
```c
assert(iTotalNumber < 1000);
```
當執行到此行時，若iTotalNumber < 1000為true則程式繼續往下執行。
若為false則跳出中斷並報錯。

## extern "C"

通常放在標頭檔中，如：
```c
#ifndef _STACK_H_
#define _STACK_H_

#ifdef __cplusplus
extern "C" {
#endif
/*...*/
#ifdef __cplusplus
}

#endif
#endif /* _STACK_H_ */
```

extern是C/C++語言中表明函數和全域變數作用範圍（可見性）的關鍵字，該關鍵字告訴編譯器，其聲明的函數和變數可以在本模組或其它模組中使用。
例如：
```c
extern int a;
```
僅僅是一個變數的宣告，其並不是在定義變數a，並未為a分配記憶體空間。
變數a在所有模組中作為一種全域變數只能被定義一次，否則會出現link錯誤。

通常，如果某個模組B.c要引用該模組A.c中定義的全域變數和函數時只需包含模組A.h即可。
這樣，模組B中調用模組A中的函數時，在編譯階段，模組B雖然找不到該函數，但是並不會報錯；它會在link階段中從模組A編譯生成的目標代碼中找到此函數。

而與extern對應的關鍵字是static，被它修飾的全域變數和函數只能在本模組中使用。例如在A.c中有定義一個Static變數，則該變數不能在其他模組B、模組C...之中被使用。

extern "C"是代表在link階段時，要以C語言的方式來尋找函數或變數，原因在於C和C++經過compiler之後會有不同的名稱。
如c++的標頭檔中：
```
int foo( int x, int y );
```
則link階段時某個引用該標頭檔的編譯器會去尋找_foo_int_int這樣的名稱，這是C++的作法。
然而C語言的compiler則不會這樣重新命名，因為沒有函數重載的功能。

使用的時機：
1. 模組A是c++寫的，模組B是c寫的，則在A需要include B.h時，需要
```c
extern "C"
{
	#include "cExample.h"
}
```
2. 模組A是c++寫的，模組B是c寫的，則在B需要include A.h時，需要把A.h中的extern "C"移除掉，因為C的compiler看不懂。
   那為什麼我們可以在C的標頭檔中放extern "C"呢？
   其實，是因為我們加上了：
   ```c
   #ifdef __cplusplus
   extern "C" {
   #endif
   ```
   __cplusplus是C++ 中預設的巨集，當這份程式是C++ 所寫時，則__cplusplus有定義。
   此時才會執行```extern "C" {```這行，更多詳細extern請看[這裡](https://b8807053.pixnet.net/blog/post/3612202)
   
## static
有四種用法：
1. static出現在variable之前，且該variable宣告在某個function之中
2. static出現在variable之前，且該variable並不是宣告在某個function中
3. static出現在class的member variable之前(C++ only)
4. static出現在class的member function之前(C++ only)

### static出現在variable之前，且該variable宣告在某個function之中：
一般在funcion裡面宣告的變數在funcion被呼叫結束之時也會跟著消失。
但如果我們在某個變數之前加上static，該變數就不會因為function結束而消失。
例子：計算這個function被呼叫幾次

### static出現在variable之前，且該variable並不是宣告在某個function中
解釋extern的範例中，會遇到變數在不同檔案中要共用，只要include某個檔案以後，就可以使用其中的變數。
然而加上static關鍵字的全域變數，就可以限定只能在該份「檔案」中被使用，而不是整個程式。
如a.c中內容如下：
```c
#include <iostream>
using namespace std;
static bool debug = true;
void show_debug_in_a() {
    cout << debug << endl;
}
```
則b.c去include a.h也不會看到debug這個變數。

### static出現在class的member variable之前(C++ only)
被static修飾的variable並不屬於某個instance，而是屬於這個class，所有以此class生成出來的instance都共用這個variable。
例子：計算class總共生成了多少個instance。

### static出現在class的member function之前(C++ only)
static出現在member function的意思是該function並不屬於某個instance，而是屬於這個class。
所以此class生成出來的instance都共用這個function。
並且，即便沒有產生任何instance，也隨時可以利用class的名稱來執行這個function。
C++用法可以參考[C++筆記上](/SXujAxaTR9KkZGFqBUt68g?view#第三週)

static詳細請看[這裡](https://medium.com/@alan81920/c-c-中的-static-extern-的變數-9b42d000688f)

## 全域變數 global
全域變數是指直接宣告在（主）函式之外的變數，這個變數在整個程式之中都可見。
例如：
```c
const double PI = 3.14159;

doule area(double r)
{
    return r * r * PI;
}

int main(void)
{
    // .....
    return 0;
}
```
其中PI是全域變數，其scope為整份程式。

全域變數最好只用來定義一些常數，或者是確實具有全域概念的變數，不應為了方便而草率地將變數設為全域變數。

## extern關鍵字

當要跨 .c 檔，使用定義在另一個 .c 檔案頂層範圍中的變數不可以直接拿來使用，例如：

foo.c：
```c
double v = 1000;
// ... 其他定義
```

則在另一份 .c 檔要使用這個v時，例如在main.c中：
```c
#include <stdio.h>

int main()
{ 
    extern double v;
    printf("%f\n", v);

    return 0; 
}
```
必須宣告double變數v為extern，代表編譯器可以在其他地方找到v的定義。

:star:但是若 foo.c 中的v被宣告為static，則v在 main.c 中不可見。
且extern不可以與定義同時連用，若要替extern的變數賦值，須寫在extern下一行之後。

## 不定參數
C++中如果函式想要能接受不定長度的引數（Variable-length argument），基本上可以使用vector定義參數，而呼叫方使用vector收集引數後，再來呼叫函式，或是利用initializer_list。
在C中則要使用[「不定參數」](https://openhome.cc/Gossip/CGossip/Variable-lengthArgument.html)。

在C中要使用不定參數，需```#include <stdarg.h>```
不定長度引數使用幾個識別字來建立不定長度引數：
* va_list
  一個特殊的型態（type），在va_start、va_arg與va_end三個巨集（macro）中當作參數使用。
* va_start
  啟始不定長度引數的巨集，第一個引數是va_list，第二個引數是最後一個具名參數。
* va_arg
  讀取不定長度引數的巨集。
* va_end
  終止不定長度引數的巨集。
  

在宣告不定長度引數時，函式定義時 ... 前至少要有一個具名參數，之後使用 ... 表示將使用不定長度引數，例如：
```c
void foo(int, ...);
```

在使用va_arg巨集取出引數內容時，必須指定將以何種資料型態取出，例如：
```c
va_arg(num_list, double);
```
其中num_list是va_list型態之變數，而double是此串資料中每個元素的型態。

我們可以在具名參數的最後一個參數來傳遞共有幾個不定參數，如：
```c
#include <stdio.h>
#include <stdarg.h>

void foo(int, ...);

int main(void) {
    double x = 1.1, y = 2.1, z = 3.9;
    double a = 0.1, b = 0.2, c = 0.3;

    puts("三個參數：");
    foo(3, x, y, z);

    puts("六個參數：");
    foo(6, x, y, z, a, b, c);

    return 0;
}

void foo(int len, ...) {
    va_list args;
    va_start(args, len);

    for(int j = 0; j < len; j++) {
        printf("%.1f\n", va_arg(args, double));
    }

    va_end(args);
}
```
以上程式輸出：
```
三個參數：
1.1
2.1
3.9
六個參數：
1.1
2.1
3.9
0.1
0.2
0.3
```
其中va_start(args, len)是規定的格式，args是va_list型態變數，len則是最後一個具名參數。
在此陳述句之後，args代表了整串不定參數，每次使用va_arg可以將參數取出。
在參數取用完畢後，記得加上va_end(args)來關閉。

然而，va_start只規定第二個參數是最後一個具名參數，沒有規定必須是長度或元素個數之類，如：
```c
#include <stdio.h>
#include <stdarg.h>

void print_positive_ints(char *, ...);

int main(void) {

    print_positive_ints("Hello!", 1, 2, 3, 4, 5, -1);

    return 0;
}

void print_positive_ints(char msg[], ...) {
    va_list args;
    va_start(args, msg);
    printf("Message recived: %s\n", msg);

    for(int arg = va_arg(args, int); arg > 0; arg = va_arg(args, int)) {
        printf("%d\n", arg);
    }
    va_end(args);
}
```
以上執行結果為：
```
Message recived: Hello!
1
2
3
4
5
```
也可以利用這種給予事先規範好的結束符號來判斷。
在C++中也可以使用```#include <cstdarg> ```來達到相同作法，但不建議。

## 字串與數字的轉換
需要```#include "stdlib.h"```

參考資料請按[這裡](http://tw.gitbook.net/c_standard_library/stdlib_h.html)

字串轉數字：

| 函數                                                                      | 功能                                                           |
| ------------------------------------------------------------------------- | -------------------------------------------------------------- |
| ```double atof(const char *str)```                                        | 轉換參數str指向的字符串到浮點數（double類型）。                |
| ```int atoi(const char *str)```                                           | 轉換參數str指向的字符串為整數（int型）                         |
| ```long int atol(const char *str)```                                      | 轉換字符串參數str指向一個長整型（類型為long int）              |
| ```double strtod(const char *str, char **endptr)```                       | 轉換參數str指向的字符串到浮點數（double類型）。                |
| ```long int strtol(const char *str, char **endptr, int base)```           | 字符串轉換成一個長整型（類型為long int）參數str指向。          |
| ```unsigned long int strtoul(const char *str, char **endptr, int base)``` | 轉換字符串參數str指向一個無符號長整型（unsigned long型整數）。 |

```c=
使用這類函數時，要先 #include <stdlib.h> 或 #include <cstdlib>

1. atof：將字串轉為倍精度浮點數
double atof ( const char * str );
ex:
 char buffer[] = "2.675";
 double f = atof(buffer);

2. atoi：將字串轉為整數
 int atoi ( const char * str );
ex:
 char buffer[] = "23";
 int i = atoi(buffer);

3. atol：將字串轉為長整數
long int atol ( const char * str );
ex:
 char buffer[] = "23";
 long int i = atol(buffer);

4. strtod： 將字串轉為倍精度浮點數
double strtod ( const char * str, char ** endptr );
ex: 
 char buffer[] = "1011.113";
 double a = strtod(buffer, NULL);

5. strtol：將字串視為任意進制，轉為長整數
long int strtol ( const char * str, char ** endptr, int base );
ex: 
 char buffer[] = "1011";
 long a = strtol(buffer, NULL，2); 
// 將 "1011" 視為2進制，轉完之後 a 即為 11

6. strtoul：將字串視為任意進制，轉為無號長整數
unsigned long int strtoul ( const char * str, char ** endptr, int base );
ex: 
 char *buffer = "1011";
 unsigned long a = strtoul(buffer, NULL，2); 
// 將 "1011" 視為2進制，轉完之後 a 即為 11

 7. itoa: 整數轉為任意進制字串 (非標準函式）
char* itoa(int value, char* string, int radix);
ex: 
char buffer[200];
int value = 234;
itoa(value, buffer, 2); // 將 234 轉為存成２進制之字串

8. ltoa：長整數轉為任意進制字串 (非標準函式)
char* ltoa(long int value, char* string, int radix);
ex: 
char buffer[200];
lnt value = 234;
ltoa(value, buffer, 2); // 將 234 轉為存成２進制之字串
```


數字轉字串：

可以使用itoa，但是標準C語言不包含這個函式。
可以使用sprintf：
```c
#include <stdio.h>    // printf(), sprintf()

int main() {
    char str[10];
    int num = 961406;

    sprintf(str, "%d", num);
    printf("str = %s\n", str);

    return 0;
}
```
以上程式執行結果：
```
str = 961406
```
sprintf會把格式化後的字串，送到第一個參數所指的空間(必須是char類型指針)。

### 接著討論atoi(ASCII to integer)和itoa(integer to ASCII)兩者的實作：

atoi：
```c
int atoi(char *s)
{
    int sum = 0;
    for(int i = 0; s[i] != '\0';i++)
	{
        //注意要扣掉'0'
        sum = sum * 10 + s[i] - '0';
    }
    return sum;
};
```

itoa：
```c
void itoa(int n, char *s)
{
    int flag = 1; // flag用來記錄是正數還是負數
    if (n < 0)
	{
        n = -n;
        flag = 0;
    }
    
	int i = 0;
    
	while(n != 0)
	{
        //注意要把'0'加回來
        s[i++] = n % 10 + '0';	// 是%10所以使從個位數開始，作完之後要反轉整個字串
        n = n / 10;
    }
    if(!flag)
        s[i++] = '-';
    s[i] = '\0';	// 結尾記得補'\0'
    reverse(s);
}

void reverse(char *s)
{
	for(int i = 0, j = strlen(s)-1; i < j; i++, j--)
	// 兩個指標，一個從前面，一個從後面，兩者交錯的話就停止
	{
        int c = s[i];
        s[i] = s[j];
        s[j] = c;
    }
}
```

## memset函數
用來初始化陣列、字串或把陣列清0時好用：

需要```#include "string.h"```

函式宣告長這樣：
```c
void * memset(void *str, int c, size_t n)
```
使用示範：
```c
#include <stdio.h>
#include <string.h>

int main ()
{
   char str[50];

   strcpy(str,"This is string.h library function");
   puts(str);

   memset(str,'$',7);
   puts(str);
   
   return(0);
}
```
輸出：
```
This is string.h library function
$$$$$$$ string.h library function
```
也可以用在陣列清0上面，如：
```c
#include "stdio.h"
#include "string.h"


int main()
{
	int a[10];
	for(int i = 0; i < 10; ++i)
	{
		a[i] = i;
		printf("%d ", a[i]);
	}
	
	printf("\n");
	memset(a, 0, sizeof(a));
	for(int i=0; i<10; ++i)
		printf("%d ", a[i]);

	return 0;
}
```
輸出：
```
0 1 2 3 4 5 6 7 8 9
0 0 0 0 0 0 0 0 0 0
```

## memcpy
需要```include "memory.h"```，函數如下：
```void * memcpy ( void * destination, const void * source, size_t num )```
作用是複製一個array內容至另一個array，
參數destination為目標陣列的指針，放在第一個參數；source指針作為第二個參數，是要複製的來源；
最後一個參數num則是要複製的byte數。

## 字串長度
需要```#include <string.h>```
```c
size_t strlen( const char *str );
```
:star:strlen返回的長度不包括'\0'

## strcpy和strncpy
需要```#include <string.h>```

用來複製字串，在C語言中若是使用```char *```或是```char[]```，則複製時應該使用深複製，
格式如下：
```c
char *strcpy( char *restrict dest, const char *restrict src );
char *strncpy( char *restrict dest, const char *restrict src, size_t count );
```
其中dest目標字串，src是來源字串，count是要複製幾個字(從開頭算)。
用法如下：
```c
#include <stdio.h>
#include <string.h>
#define LEN 80

int main(void) {
    char buf[LEN] = "Justin Lin";

    int lenOfName1 = strlen(buf) + 1;
    char name1[lenOfName1];
    strcpy(name1, buf);
    printf("名稱：%s\n", name1);    
    
    int lenOfName2 = strlen(buf) + 1 - 5;
    char name2[lenOfName2];
    strncpy(name2, buf, lenOfName2);
    printf("部分名稱：%s", name2);

    return 0;
}
```
結果顯示：
```
名稱：Justin Lin
部分名稱：Justin
```

## strcat和strncat串接字串
需要```#include <string.h>```

函式如下：
```c
char *strcat( char *restrict dest, const char *restrict src );
char *strncat( char *restrict dest, const char *restrict src, size_t count );
```

若要串接兩個字串，則要使用 strcat，若要串接部份字串，可以使用 strncat：
```c
#include <stdio.h>
#include <string.h>

int main(void) {
    char str1[] = "xyz";
    char str2[] = "abc";

    int len = strlen(str1) + strlen(str2) + 1;
    char concated[len];
    memset(concated, '\0', len);

    strcat(concated, str1);
    strcat(concated, str2);

    printf("串接後：%s\n", concated);

    return 0;
}
```
結果顯示：
```
串接後：xyzabc
```

## restrict關鍵字

可以用在接收指標參數，與volatile相反，restrict告知編譯器，其所修飾的變量可以被優化，只能用於指針，表明所修飾的指針變量，是所指數據的唯一訪問者。

如果有一個程式如下：
```c
#include <stdio.h>
#include <stdlib.h>

int add(int * p1, int * p2)
{	
	*p1 = 1;
	*p2 = 2;
	return *p1 + *p2;
}

int main(void) {
	int * restrict p1 = (int *)malloc(sizeof(int));
	*p1 = 10;
	int * p2 = p1;
	int a = add(p1, p2);
	printf("p1=%d p2=%d add(p1+p2)=%d", *p1, *p2 , a);
    return 0;
}
```
執行結果為：
```
p1=2 p2=2 add(p1+p2)=4
```
顯而易見的，因為p1和p2是指到同一個地方。
然而當我們在參數列使用restrict關鍵字之後，表明只有這個指標可以訪問這個地址：
```c
#include <stdio.h>
#include <stdlib.h>

int add(int * restrict p1, int * restrict p2)
{	
	*p1 = 1;
	*p2 = 2;
	return *p1 + *p2;
}

int main(void) {
	int * restrict p1 = (int *)malloc(sizeof(int));
	*p1 = 10;
	int * p2 = p1;
	int a = add(p1, p2);
	printf("p1=%d p2=%d add(p1+p2)=%d", *p1, *p2 , a);
    return 0;
}
```
執行結果為：
```
p1=2 p2=2 add(p1+p2)=3
```
雖然p1和p2都被改成了2。但是在函數內相加的結果卻為3，主要是因為跟volatile相反，執行時先將值放在暫存器，直到函式結束才write back。


## C語言的執行緒(thread)
需要```#include <pthread.h>```

格式：
```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg)
```
其中，thread是一個thread的指標，通常thread是在main thread中建立的的一個變數，而attr有各種屬性可用，一樣是要創建一個pthread_attr_t變數，如：PTHREAD_CREATE_JOINABLE、PTHREAD_CREATE_DETACHED之類，沒特別需求通常放NULL就可以了。
start_routine則是函式指標，是要執行緒去執行的thread body，arg則可以放入該函式所需的參數，如果函式所需要的參數大於1個，必須使用struct先封裝起來。
例子：
```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

typedef struct ss
{
	int num;
	char * buff;
}ss;

// 子執行緒函數
void* child(void* data) {
  ss * args = (ss*) data; // 取得輸入資料
  char * str = args -> buff;
  for(int i = 0;i < args -> num; ++i) {
    printf("%s\n", str); // 每秒輸出文字
    sleep(1);
  }
  pthread_exit(NULL); // 離開子執行緒
}

// 主程式
int main() {
  pthread_t t; // 宣告 pthread 變數
  ss arg = {.num = 3, .buff="Child"};	// 封裝要傳送的參數
  pthread_create(&t, NULL, child, &arg); // 建立子執行緒

  // 主執行緒工作
  for(int i = 0;i < 3;++i) {
    printf("Master\n"); // 每秒輸出文字
    sleep(1);
  }

  pthread_join(t, NULL); // 等待子執行緒執行完成
  return 0;
}
```
執行結果：
```
Master
Child
Master
Child
Master
Child
```

參考資料：https://blog.gtwang.org/programming/pthread-multithreading-programming-in-c-tutorial/

## volatile
其語法和const一樣。作用是告訴編譯器其所修飾的變量，不可被優化。

因此編譯後的程式每次需要儲存或讀取這個變數的時候，都會直接從變數位址中讀取資料。

如果沒有volatile關鍵字，則編譯器可能最佳化讀取和儲存，可能暫時使用暫存器中的值，如果這個變數由別的程式更新了的話，將出現不一致的現象。

例如：
```c
static int foo;
 
void bar(void)
{
    foo = 0;
 
    while (foo != 255);
}
```
該程式會一直輪詢直到foo == 255為止。

若沒有替foo加上volatile關鍵字，則編譯器會最佳化成：
```c
void bar_optimized(void)
{
    foo = 0;
 
    while (true);
}
```
因此而變成無窮迴圈。

## gcc內建處理二進位函數
以下介紹的函數都是非標準的函數，只能在GCC底下使用，不過一般的比賽環境都是支援的，所以熟記起來可以增加寫位元運算的效率

1. ```c
   int __builtin_ffs (unsigned int x)
   int __builtin_ffsl (unsigned long)
   int __builtin_ffsll (unsigned long long)
   ```
   返回右起第一個1的位置
   Returns one plus the index of the least significant 1-bit of x, or if x is zero, returns zero.
   
2. ```c
   int __builtin_clz (unsigned int x)
   int __builtin_clzl (unsigned long)
   int __builtin_clzll (unsigned long long)
   ```
   返回左起第一個1之前0的個數
   Returns the number of leading 0-bits in x, starting at the most significant bit position. If x is 0, the result is undefined.

3. ```c
   int __builtin_ctz (unsigned int x)
   int __builtin_ctzl (unsigned long)
   int __builtin_ctzll (unsigned long long)
   ```
   返回右起第一個1之後的0的個數
   Returns the number of trailing 0-bits in x, starting at the least significant bit position. If x is 0, the result is undefined.

4. ```c
   int __builtin_popcount (unsigned int x)
   int __builtin_popcountl (unsigned long)
   int __builtin_popcountll (unsigned long long)
   ```
   返回1的個數
   Returns the number of 1-bits in x.

5. ```c
   int __builtin_parity (unsigned int x)
   int __builtin_parityl (unsigned long)
   int __builtin_parityll (unsigned long long)
   ```
   返回1的個數的奇偶性(1的個數 mod 2的值)
   Returns the parity of x, i.e. the number of 1-bits in x modulo 2. 

6. ```c
   uint16_t __builtin_bswap16 (uint16_t x)
   uint32_t __builtin_bswap32 (uint32_t x)
   ```
   按照Byte翻轉x，並返回其結果，例如用來將Little endian轉換成Big endian。

7. ```c
   int __builtin_types_compatible_p(type1, type2)
   ```
   判斷兩個變數的type是否相同，如果相同返回 1，不同返回 0。

例子：如何計算一個數最左邊的bit是在第幾位數
如```int num = 40```，二進位為101000，由最右邊為第0位開始算起，最左邊為1的bit應該在第5位。
```c
#include "stdio.h"

int main()
{
    int num = 40;
    printf("highest bit pos: %I64d", (sizeof(num) << 3) - 1 -__builtin_clz(num));
    // 其中sizeof()返回4，__builtin_clz()返回6，所以32-6-1=5
    return 0;
}
```
其中sizeof()返回的是byte大小，int的大小為4，換算成bit大小需要乘上8，即32個bits。
而```__builtin_clz```可以計算出左邊0的個數，故32-左邊0的個數，再扣掉1我們要的答案(扣1是因為最右邊從第0位開始)，即為5。

例子：將0x12345678轉換成0x78563412，也就是Little endian轉換成Big endian。
```c
int main(void) {
	unsigned int i = 0x12345678;
	unsigned int j = __builtin_bswap32(i);
	printf("%x", j);
	return 0;
}
```

這種內建函數其實非常多，這邊有附上一個GCC內建函數的列表，有興趣的朋友可以參考
https://gcc.gnu.org/onlinedocs/gcc/Other-Builtins.html

當你在GCC環境下，想直接用01寫二進位的東西，其實有簡單的方法:
```c
cout<<0b1010101;
cout<<0b1010101LL;
```
這樣編譯器應該會警告你說這是GCC內建的東西(C++14以後的版本會支援它)，但是還是會正確執行，都是85，個人覺得蠻方便的

如果你是用C++11，可以用stoi,stol,stoll,stoul,stoull等函數來把二進位字串轉成int,long,long long,unsigned long,unsigned long long等，可以設定他用二進位轉換，像是這樣:
```c
cout<<stoi("1010101",NULL,2);
cout<<stoll("1010101",NULL,2);
```

另外有些編譯器會在```<algorithm>```實作std::__lg(n)這個函數，他會回傳$\lfloor log_2(n)\rfloor$的值，可以參考這個
http://stackoverflow.com/questions/40434664/what-is-std-lg

## gets、scanf、fgets之比較
首先，scanf使用語法如下：
```c
int input;

printf("請輸入數字：");
scanf("%d", &input);

// 如果是字串
char str[20]

printf("請輸入字串：");
scanf("%s", str);	// 第二個參數要的是pointer，str是陣列所以不用加上&
```
在程式中先宣告了一個整數變數input，使用 scanf() 函式時，
若輸入的數值為整數，則使用格式指定字 %d，若輸入的是其他資料型態，
則必須使用對應的格式指定字，如果是double，特別注意要使用 %lf 來指定。

scanf還可以指定可接受的字元集合，例如若只想接受1到5的字元，則可以如下：
```c
printf("請輸入 1 到 5 的字元：");
scanf("%[1-5]", buf);
printf("輸入的字元為 %s\n", buf);

fflush(stdin); // 清除輸入緩衝區

printf("請輸入 XYZ 任一字元：");
scanf("%[XYZ]", buf);
printf("輸入的字元為 %s\n", buf);
```

scanf不能接受空格、製表字元Tab、回車等，遇到空格時就結束接受。
gets則能夠接受空格、製表字元Tab和回車等，遇回車或EOF(end of file)時都會結束接受。

假設我們希望用scanf來取得整行文字直到換行呢？
```
scanf("%[^\n]", buf)
```

假設我們希望能擷取一串input最後面的數字呢？
例如希望能達到：
```
Input  : This is the value 100 
Output : Input value read : a = 100 
```

我們當然可以寫成：
```c
#include <stdio.h> 
int main() 
{ 
    int a; 
    scanf("This is the value %d", &a); 
    printf("Input value read : a = %d", a); 
    return 0; 
} 
```

但是假設我們不知道數字前面的string是什麼呢？(假設我們只知道有四個單字組成的句子)
可以用以下寫法：
```c
int main() 
{ 
    int a; 
    scanf("%*s %*s %*s %*s %d", &a); 
    printf("Input value read : a=%d",a); 
    return 0; 
} 
```

```%*s```代表我們要忽略前面的字串，直到遇到下個空白或換行，所以針對上述例子我們需要四個```%*s```

:star:fgets vs gets：

fgets在讀取字串會把輸入的「Enter」也作為字串一部分，通過gets或scanf輸入字串時不會包含：
fgets結尾 -> '\n' '\0'
gets結尾 -> '\0'

fgets的使用方式為：
```c
fgets(str, sizeof(buf), stdin);
```

另外還有fscanf()可以用，必須在第一個參數放入一個FILE類型的指標：
```c
FILE* ptr = fopen("abc.txt","r"); 
while (fscanf(ptr,"%*s %*s %s ",buf)==1) 
        printf("%s\n", buf); 
```

## 檔案操作fopen

放在```#include "stdio.h"```之中

函數fopen如下：
```FILE *fopen(const char *filename, const char *mode)```
fopen()可以打開檔案，然後將處理檔案的必要資訊儲存給回傳的FILE的結構，總共需要兩個字串參數，第一個字串為檔案名稱，第二個字串則是開啟模式。

| mode | 描述                                                                                             |
|:----:| ------------------------------------------------------------------------------------------------ |
| "r"  | 打開一個文件進行讀取。該文件必須存在。                                                           |
| "w"  | 創建一個空的書面文件。如果已經存在具有相同名稱的文件，其內容被刪除的文件被認為是一個新的空文件。 |
| "a"  | 附加到文件中。寫入操作的資料追加在文件的末尾處。如果檔案尚不存在就建立一個新的文件。             |
| "r+" | 打開更新文件讀取和寫入。該文件必須存在。                                                         |
| "w+" | 新建一個空文件，讀取和寫入。                                                                     |
| "a+" | 打開一個文件，用以讀取和追加。                                                                   |
| "rb" | 同"r"，但是用二進制打開。                                                                        |
| "wb" | 同"w"，但是用二進制打開。                                                                        |
| "ab" | 同"a"，但是用二進制打開。                                                                        |

有沒有加上b的差別：
* 文字檔案：這類檔案以文字的ASCII碼形式儲存在計算機中。它是以"行"為基本結構的一種資訊組織和儲存方式。 
* 二進位制檔案：這類檔案以文字的二進位制形式儲存在計算機中，使用者一般不能直接讀懂它們，只有通過相應的軟體才能將其顯示出來。二進位制檔案一般是可執行程式、圖形、影象、聲音等等。

### fwrite用法
用來寫入檔案，函數如下：
```size_t fwrite ( const void * ptr, size_t size, size_t count, FILE * stream )```
fwrite將陣列或結構的內容寫進檔案中，共需四個參數，
第一個參數為陣列或結構的指標，第二個參數為陣列或結構的大小，第三個參數為陣列的元素數量，如果是結構就等同1個陣列元素，第四個參數為指向結構FILE的指標。

例子：
```c
#include <stdio.h>

int main()
{

    FILE *pFile;

    char buffer[] = {'H', 'e', 'y'};

    pFile = fopen("write.txt", "w");

    if (NULL == pFile)
    {

        printf("open failure");

        return 1;
    }
    else
    {

        fwrite(buffer, sizeof(buffer), 1, pFile);
    }

    fclose(pFile);

    return 0;
}

```

## fread用法
```fread()```將檔案的內容寫進陣列或結構中，共需四個參數，
第一個參數為陣列或結構的指標，第二個參數為陣列或結構的大小，第三個參數為陣列的元素數量，如果是結構就等同1個陣列元素，第四個參數為指向結構FILE的指標。
以下程式示範用fread取得檔案中儲存的資料，然後印在螢幕上：
```c
#include <stdio.h>
#include <stdlib.h>
 
struct addressList {
    char nickname[30];
    int age;
    int sex;
    char relation[30];
};
 
int main(void)
{
    FILE *fPtr;
    struct addressList entry = {"", 0, 0, ""}; 
     
    fPtr = fopen("address.dat", "rb");
    if (!fPtr) {
        printf("檔案開啟失敗...\n");
        exit(1);
    }
    
	fread(&entry, sizeof(struct addressList), 1, fPtr);
	while(!feof(fPtr))
	{	
        printf("%s - %d - %s - %s\n", entry.nickname, entry.age, entry.sex ? "m" : "f", entry.relation);
		fread(&entry, sizeof(struct addressList), 1, fPtr);
	}
	
    fclose(fPtr);
     
    return 0;
}
```
其中，```feof(fPtr)```用來判斷fPtr的檔案是否已經讀取到最後，
只有當檔案位置指標```(fp－>_ptr)```到了檔案末尾，然後再次發生讀/寫操作時，標誌位```(fp->_flag)```才會被置為含有```_IOEOF```。
然後再呼叫```feof()```，才會得到檔案結束的資訊，所以必須寫成上述結構才不會發生多讀取一次的情況。

注意：上述的address.dat是用下列程式碼寫入的：
```c
#include <stdio.h>
#include <stdlib.h>
 
struct addressList {
    char nickname[30];
    int age;
    int sex;
    char relation[30];
};
 
int main(void)
{
    FILE *fPtr;
    struct addressList entry = {"jeremy lin", 32, 1, "single"}; 
     
    fPtr = fopen("address.txt", "wb");
    if (!fPtr) {
        printf("檔案開啟失敗...\n");
        exit(1);
    }
     
    fwrite(&entry, 1, sizeof(entry), fPtr);
     
    fclose(fPtr);
     
    return 0;
}
```

## fprintf用法
若是單純要寫入格式化之字串，可使用fprintf，用法同printf，但第一個參數須給定已由fopen開啟的```FILE```指標：

例子：
```c
#include <stdio.h>
#include <stdlib.h>

int main()
{
   FILE * fp;

   fp = fopen ("file.txt", "w+");
   fprintf(fp, "%s %s %s %d", "We", "are", "in", 2012);
   
   fclose(fp);
   
   return(0);
}
```

相對應的，也有fscanf、fgets、fputs等等的用法：

| 函數    | 功能                                                       |
| ------- | ---------------------------------------------------------- |
| fprintf | 同printf，但第一個參數須給定指向結構FILE的指標。           |
| fscanf  | 同scanf，但第一個參數須給定指向結構FILE的指標。            |
| fgets   | 同gets取得一行文字，但第一個參數須給定指向結構FILE的指標。 |
| fputs   | 同puts，但第一個參數須給定指向結構FILE的指標。             |

## freopen用法
```freopen()```可以打開檔案，然後將處理檔案的必要資訊儲存給回傳的FILE的結構，總共需要三個參數，前兩個為字串，第一個字串為檔案名稱，第二個字串則是開啟模式，第三個參數為串流流向。
例如：
```c
freopen("oldname.txt", "w", stdout);
```
則以後每次ptintf內容都會輸出到oldname.txt當中。

## pointer to pointer的用法
很多時候我們想要在callee中實現malloc，而該分配空間的指針是由caller透過參數傳入。
如以下例子：
```c
#include "stdio.h"
#include "stdlib.h"

void GetMemory(char *p) {
	p = (char *)malloc(100);
}

void test() {
	char *str = NULL;
	GetMemory(str);
	strcpy(str,"hello world");
	printf(str);
}

int main() {
	void (* t)(void) = test;
	test();
	return 0;
}
```
可以注意到在test傳入GetMemory的指標p，在GetMemory中被malloc，但是此程式會出錯！
原因在於```*p```本身是call by value，僅僅是一個str指針的複製，如果直接對p進行malloc後賦值並不會真正改變test手上的那個指針。
因此我們必須使用pointer to pointer，將指針層級拉高一級：
```c
#include "stdio.h"
#include "stdlib.h"

void GetMemory(char **p) {
	*p = (char *)malloc(100);
}

void test() {
	char *str=NULL;
	GetMemory(&str);
	strcpy(str,"hello world");
	printf(str);
	free(str);
}

int main() {
	void (* t)(void) = test;
	test();
	return 0;
}
```
執行結果才會正確。

## uint8_t / uint16_t / uint32_t /uint64_t

參考[這裡](https://blog.csdn.net/Mary19920410/article/details/71518130)和[這裡](https://openhome.cc/Gossip/CGossip/Datatype.html)

在C99標準中定義了這些資料類型，需要使用時必須```#incldue "stdint.h"```
分別對應：


| 類型      | 原始型態             | 對應格式化輸出                                               |
| --------- | -------------------- | ------------------------------------------------------------ |
| uint8_t   | unsigned signed char | 沒有專門輸出char或unsigned char之數值的格式，它們用 d 或 u。 |
| uint16_t  | unsigned short       | %hu                                                          |
| uint32_t  | unsigned int         | %u                                                           |
| uint64_t  | unsigned long long   | %lu                                                          |
| intptr_t  | int                  | %d                                                           |
| uintptr_t | unsigned int         | %u                                                           |

* uint8_t實際上是一個char。所以輸出uint8_t類型的變量實際上輸出其對應的字符，而不是數值
* 在 d u o x X 這些格式字元的前面，可以寫一個字元 h 表示 short 資料型態，或是加一個字元 l (小寫的 L) 表示 long 資料型態
* 在 f e E g G 這些格式字元的前面，可以寫一個字元 L 表示 long double 資料型態
* intptr_t在不同的平臺是不一樣的，始終與地址位數相同，因此用來存放地址，即地址。可以參考[這裡](https://www.itread01.com/content/1545184622.html)

有號整數的型態名稱為 int8_t、int16_t、int32_t、int64_t，顧名思義，使用的長度分別為 8 位元、16 位元、32 位元與 64 位元，舉例來說，int32可儲存的整數範圍為 -2147483648 到 2147483647。

## Big-Endian和Little-Endian之測試
參考資料為[這裡](https://blog.gtwang.org/programming/difference-between-big-endian-and-little-endian-implementation-in-c/)
```c
#include "stdio.h"
#include "stdlib.h"
#include "stdint.h"

typedef union endian endian;

union endian
{
	uint32_t l;
	uint8_t c[4];
};

int main()
{
	endian et;
	et.l = 0x12345678;
	if(et.c[0] == 0x78)
		puts("Little Endian");
	else if(et.c[0] == 0x12)
		puts("Big Endian");
	else
		puts("Unkown");

	printf("\n%u 在系統中的順序為：\n", et.l);
	for(int i = 0; i < sizeof(uint32_t); ++i)
		printf("%p : 0x%02X\n", &et.c[i], et.c[i]);

	printf("\n若是動態分配？\n");
	endian * dynamic_et = (endian *)malloc(sizeof(endian));
	dynamic_et->l = 0x12345678;
	if(dynamic_et->c[0] == 0x78)
		puts("Little Endian");
	else if(dynamic_et->c[0] == 0x12)
		puts("Big Endian");
	else
		puts("Unkown");
	free(dynamic_et);

	return 0;
}
```
印出結果(在windows和intel上)：
```
Little Endian

12345678 在系統中的順序為：
000000000061FDFC : 0x78
000000000061FDFD : 0x56
000000000061FDFE : 0x34
000000000061FDFF : 0x12

若是動態分配？
Little Endian
```

## Stack和Heap
Stack的記憶體位址配置是從高到低，而Heap的記憶體位址配置是從低到高。

測試程式：
```c
printf("Stack在memory的成長方向為？\n");
int first_stack = 0;
int second_stack = 0;
if(&second_stack > &first_stack) 
	puts("由下而上增長 (後分配的在記憶體較大位置)");
else if(&second_stack < &first_stack)
	puts("由上而下增長 (後分配的在記憶體較小位置)");
else
	puts("Unkown");

printf("\nHeap在memory的成長方向為？\n");
int * first_heap = (int *)malloc(sizeof(int));
int * second_heap = (int *)malloc(sizeof(int));
if(second_heap > first_heap) 
	puts("由下而上增長 (後分配的在記憶體較大位置)");
else if(second_heap < first_heap)
	puts("由上而下增長 (後分配的在記憶體較小位置)");
else
	puts("Unkown");
free(first_heap);
free(second_heap);
```
印出結果：
```
Stack在memory的成長方向為？
由上而下增長 (後分配的在記憶體較小位置)

Heap在memory的成長方向為？
由下而上增長 (後分配的在記憶體較大位置)
```
![](https://i.imgur.com/rjuu0Cf.png)

## argsort

如何實現numpy的argsort？讓我們可以追蹤哪個數字被排到哪個位置去了。
參考程式：[C++版本](https://blog.csdn.net/m_buddy/article/details/86262717)

若有一個整數陣列nums，希望得到一組idx，
使得```idx[i]```代表```nums[i]```在有序陣列中應該待在哪個位置，例如：
```
num[0] = 5, num[1] = 3, num[2] = 1, num[3] = 2, num[4] = 2, num[5] = 7
若排好序應該為：1 2 2 3 5 7

則argsort應返回如下陣列：
idx[0] = 4, idx[1] = 3, idx[2] = 0, idx[3] = 1, idx[4] = 2, idx[5] = 5
```

基本想法：
其實就是拿index下去排列而已，而使用的時候只要```nums[idx[i]]```就可以得到正確排序結果。

```c
int * argsort(const int *nums, const int N)
{
	auto int cmp(const void *, const void *);
	
	int *index_array = (int *)malloc(sizeof(int) * N);
	for(int i = 0; i < N; ++i)
		index_array[i] = i;
	
	qsort(index_array, N, sizeof(int), cmp);

	int cmp(const void *a, const void *b)
	{
		int pos1 = *(int *)a;
		int pos2 = *(int *)b;
		if(nums[pos1] < nums[pos2])
			return -1;
		return 1;
	}
	return index_array;
}
```
註：上述程式只能在GCC中被編譯，因為用到了Nested Functions，[參考1](https://www.geeksforgeeks.org/nested-functions-c/)、[參考2](https://gcc.gnu.org/onlinedocs/gcc/Nested-Functions.html)

## bit-field（位元欄）

在C語言中的struct結構可以自定義不同型態變數的位元長度，稱為位元欄。
例如你希望可以只用1個byte（8個bits）來完成9 * 9乘法表：
```c
struct {
    unsigned char i:4;
    unsigned char j:4;
} var;

int main()
{
	for(var.i=2; var.i<=9; ++var.i)
	{
		for(var.j=1; var.j<=9; ++var.j)
			printf("%d * %d = %2d\n", var.i, var.j, var.i*var.j);
		puts("");
	}
}
```
利用bit-field的方式，不但可以在運算式中使用 + - * /，也可以直接放到布林運算式直接取用，可以節省記憶體空間。

考慮以下程式：
```c
#include <stdbool.h>
#include <stdio.h>

bool is_one(int i) { return i == 1; }

int main() {
    struct { signed int a : 1; } obj = { .a = 1 };
    puts(is_one(obj.a) ? "one" : "not one");
    return 0;
}
```
答案輸出為：
```
not one
```
因為1個bit又是signed，只能在2's complement中表示 0 跟 -1，沒有1的可能。

如果題目的int並沒有說是 signed 或 unsigned，
那麼 bit-field 宣告之後究竟該是 signed / unsigned 是由編譯器所決定的。

### Linux 核心: BUILD_BUG_ON_ZERO()之應用

在 Linux 核心原始程式碼裡頭，有個標頭檔linux/build_bug.h內部實作是:
```
Linux 核心: BUILD_BUG_ON_ZERO()

/*
 * Force a compilation error if condition is true, but also produce a
 * result (of value 0 and type size_t), so the expression can be used
 * e.g. in a structure initializer (or where-ever else comma expressions
 * aren't permitted).
 */
#define BUILD_BUG_ON_ZERO(e) (sizeof(struct { int:(-!!(e)); }))
```

這個macro是用來檢查是否會有compilation error，e 是個判斷式，當它經由這個macro結果為true，則可以在compile-time的時候被告知有錯。(因為宣告發生在compile-time)


然後```int:(-!!(e))```是代表什麼意思？

* ```!!(e)```：對e 做兩次 ```NOT```，確保結果一定是 0 或 1
* ```-!!(e)```：對```!!(e)``` 乘上 -1，因此結果會是 0 或 -1

因此，當 e 這個判斷式是有錯的，```!!(e) = 1```，而```-!!(e) = -1```，代表宣告一個 structure 中有一個 int ，其中32 bits中有 -1 bit的bit field。

反之當 e 是沒有錯的，則會宣告int有 0 bit的bit field。

-1 的bit field很明顯是非法的 (constant expression must be non-negative integer)，那麼 0 bit的bit field呢？

首先zero-width bit field有個規定是必須unnamed ，再來zero-width bit field宣告不會使用到任何空間，但是會強制structure中下一個bit field對齊到下一個unit的boundary：

舉例來說：
```c
#include "stdio.h"
struct foo {
    int a : 3;
    int b : 2;
    int : 0; /* Force alignment to next boundary */
    int c : 4;
    int d : 3;
};

int main() {
    int i[2] = {0xFFFA, 0xFFAA};
    struct foo *f = (struct foo *) &i;
    printf("a = %d\nb = %d\nc = %d\nd = %d\n", f->a, f->b, f->c, f->d);
    return 0;
}
```
輸出結果為：
```
a = 2
b = -1
c = -6
d = 2
```
這裡因為宣告一個int的zero-width bit-field，所以要對齊的長度為32 bit，c 和 d 超出 0xFFFA的範圍，被強迫對齊下一個word：
```
i = 1111 1111 1111 1010     1010 1010 1001 0001

                            padding & start from here
                            ↓
    1111 1111 1111 1010     1010 1010 1010 1010 
                 b baaa                ddd cccc

   |  ←  int 32 bits  →  |  ←  int 32 bits  →  |
```
要注意的是這邊範例為Little Endian，按順序，a 應該被放在最小的記憶體位置，
而因為```int : 0```，導致 d 和 c 必須被擠去下一個```i[1]```的地方對齊。

### enum

列舉 (enum)：也有人稱作為 "狀態機"，因為列舉常常被人拿來當作狀態判斷的使用。
enum 所佔的記憶體為 32 位元 (bit)，這是在預設的情況底下。

因為 enum 可以更改型別，共有 byte、sbyte、short、ushort、int、uint、long、ulong。
所以佔據的記憶體容量必須看你是使用什麼型別而定，不過系統預設為 int。

舉例：
```c
// 其中 direction.North 就會被預設為 0, direction.South 就是 1, 依此類推
enum direction
{
    North,
    South,
    East,
    West
};
```
取值時可以用：
```c
enum direction dest = East;
 
// 轉換為 int, 其值為 0
int i = (int)dest;
```

列舉同樣可用 typedef 簡化型別名稱，如下例：
```c
#include <stdio.h>

/* Foreward declaration. */
typedef enum direction Direction;

enum direction {
    North = 5,
    South = 4,
    East,
    West
};

int main(void)
{
    Direction dest = West;
    printf("%d\n", dest);
    return 0;
}
```
上述程式碼印出：
```
5
```
因為從 South 開始從4遞增，後面的 East 在 5 的位置，而 West 就是6，以此類推。
需要注意的是，列舉不具有型別安全，例如：
```c
typedef enum direction_t direction_t;

enum direction_t {
    DIRECTION_NORTH,
    DIRECTION_SOUTH,
    DIRECTION_EAST,
    DIRECTION_WEST,
};

int main(void)
{
    /* Wrongly correct. */
    direction_t d = 6;

    return 0;
}
```
觀察上述程式，6 已經超出 direction_t 的範圍了，但是這段程式碼依然不會報錯，
原因如最開頭所講：列舉在內部是以 int 儲存的。

解決辦法就是包成結構體，如[參考資料](https://michaelchen.tech/c-programming/enumeration/)。

## 關於printf和scanf的回傳值

printf的回傳值是它總共印出了多少個字元，例如：
```c
int main()
{
    char st[] = "CODING";
 
    printf("While printing ");
    printf(", the value returned by printf() is : %d",
                                    printf("%s", st));
 
    return 0;
}
```
由於st有6個字元，故結果會是：
```
While printing CODING, the value returned by printf() is : 6
```

scanf則是回傳它成功的接收了幾個input放到參數所給的指標裡，
如果沒有半個，那就是回傳EOF，其中EOF是由巨集定義成-1。
看看下面的例子：
```c
#include <stdio.h>
int main()
{
    char a[100], b[100], c[100];
 
    // scanf() with one input
    printf("\n First scanf() returns : %d",
                            scanf("%s", a));
 
    // scanf() with two inputs
    printf("\n Second scanf() returns : %d",
                       scanf("%s%s", a, b));
 
    // scanf() with three inputs
    printf("\n Third scanf() returns : %d", 
                  scanf("%s%s%s", a, b, c));
 
    return 0;
}
```
若我們輸入：
```
Hey!
welcome to
geeks for geeks
```
則output會是：
```
First scanf() returns : 1
Second scanf() returns : 2
Third scanf() returns : 3
```
因為第一個scanf成功接收了一個字串"Hey!"放到a之中，
第二個Scanf成功街收了一個字串"welcome"和一個字串"to"，分別放到a與b之中，
第三個Scanf也是以此類推。

## %n的用法

在scanf中，若我們使用%n，如：
```c
scanf("%d%d%n", &a, &b, &check)
```
假設input為：
```
10 20
```
則a = 10, b = 20
那check則會被assign成「到%n出現為止，前免掃過了幾個字元？」
由於我們的input為'1'、'0'、' '、'2'、'0'，注意中間有個space，所以check是5。

## \a、\b和\r的用法

在printf中，\a會使電腦發出beep的一聲，
\b則會讓當前的打印光標倒退一格
\r則是會使光標完全倒退到該列的第一格。

以下為舉例：
```c
#include <stdio.h>
int main(void){
    printf("Hello World!\n");
    printf("Goodbye \a");
    printf("Hi \r");
    printf("Yo\b");
    printf("What? \t");
    printf("pewpew");
    return 0;
}
```
則電腦會再發出嗶一聲後，印出：
```
Hello World!
YWhat?  pewpew
```
不過實際上真正的輸出，因為有\b和\r，還是要depend on輸出設備的設定。

## 在sscanf中可以使用Regular Expression

函數原型：
```c
int sscanf ( const char * str, const char * format, ...);
```

功能說明如下：
```
str : 被剖析的字串
format: 剖析格式

在 format 字串中，以 % 起頭者為剖析段落，通常在剖析完成後會指定給後面的變數，其格式語法如下：

剖析段落的語法：%[*][width][modifiers]type

  % 代表變數開始

  * 代表省略不放入變數中

  width 代表最大讀取寬度

  modifier 可以是 {h|I|L} 之一
  說明 : 其中 h 代表 2 byte 的變數 (像 short int)，
              l 代表 4 byte 的變數 (像 long int)，
              L 代表 8 byte 的變數 (像 long double)

  type 則可以是 c, d,e,E,f,g,G,o, s, u, x, X 等基本型態，
       也可以是類似 Regular Expression 的表達式。
  說明: c : 字元 (char); 
        d : 整數 (Decimal integer); 
        f : 浮點數 (Floating Point); 
        e,E : 科學記號 (Scientific notation); 
        g,G : 取浮點數或科學記號當中短的那個; 
        o : 八進位 (Octal Integer); 
        u : 無號數 (unsigned integer); 
        x, X : 十六進位 (Hexadecimal integer)
```
參考資料：[這裡](http://programmermagazine.github.io/201312/htm/article2.html)

## 在printf中.*的用法

例如：
```
#include<stdio.h> 
int main() 
{ 
    char *s = "Geeks Quiz"; 
    int n = 7; 
    printf("%.*s", n, s); 
    return 0; 
} 
```
會印出：
```
Geeks Q
```
因為```.*```在printf中代表被format的精度不在字串中直接定義，而是由參數列的整數來決定。

## typeof

在gcc中可以使用typeof來獲取參數的類型，然後就可以定義相同的類型變量。
typeof的參數可以是兩種形式：表示式或類型。

用在類型的例子：
```c
typeof(int *) a , b;
```
則 a 和 b 兩個變數都會是int的指標類型。

用在表示式的例子：
```
int (*fun_array [])(double, char) = {function1, function2};
typeof(fun_array[0]) x;
```
則 x 在這邊會是一個整數類型的變數，因為typeof獲取了fun_array這個函數指標陣列的第0個函數的返回值。

如果在一個被包含在ISO C程序中的標頭檔中使用該關鍵字，請使用__typeof__代替typeof。
例如在ideone上面執行就要用__typeof__。

用typeof也可以讓MACRO變得更好用，例如原本我們寫：
```c
#define int_p int *

int_p a, b;
```
則 a 會是```int *```的類型，而 b 則會是```int```類型，與我們的預期不相符。
此時改用：
```c
#define int_p typeof(int *)

int_p a, b;
```
就可以解決問題。

也可以用來避免因為重複調用遞增遞減運算子而造成的錯誤，如原本的巨集：
```c
#define pow(a) ((a) * (a))

int main()
{
	int num = 10;
	int num_pow = pow(a++);	// error，重複調用遞增運算子
	printf("%d\n", num_pow);
	return 0;
}
```

改為：
```c
#include "stdio.h"
#define pow(a) ({typeof(a) _a = a; _a * _a;})

int main()
{
	int num = 10;
	int num_pow = pow(num++);
	printf("%d\n", num_pow);
	return 0;
}
```
便可正確執行。

在pow這個MACRO中，大括號裡表達式的值為最後一條陳述式的值，然後用小括號將大括號括起來就可以給其他變數賦值了。如果拿掉MACRO語句中的最外層小括號的話，會報錯。

## Deque實作
```c
#include "stdio.h"
#include "stdlib.h"
#include "stdbool.h"

typedef struct node node;
struct node
{
	int val;
	node *next;
	node *prev;
};

struct deque
{
	node *front;
	node *back;
	size_t size;
} deq = {.front = NULL, .back = NULL, .size = 0};

bool isEmpty()
{
	return deq.size == 0;
}

void push(int data)
{
	node *new = (node *)malloc(sizeof(node));
	new->val = data;
	new->next = deq.front;
	new->prev = NULL;
	if(isEmpty())
		deq.front = deq.back = new;
	else
	{
		deq.front->prev = new;
		deq.front = new;
	}
	++deq.size;
}

void inject(int data)
{
	node *new = (node *)malloc(sizeof(node));
	new->val = data;
	new->next = NULL;
	new->prev = deq.back;
	if(isEmpty())
		deq.front = deq.back = new;
	else
	{
		deq.back->next = new;
		deq.back = new;
	}
	++deq.size;
}

void pop()
{
	if(!isEmpty())
	{
		node *tmp = deq.front;
		deq.front = tmp->next;
		deq.front->prev = NULL;
		free(tmp);
		--deq.size;
	}
}

void eject()
{
	if(!isEmpty())
	{
		node *tmp = deq.back;
		deq.back = tmp->prev;
		deq.back->next = NULL;
		free(tmp);
		--deq.size;
	}
}

void print_list()
{
	node *curr = deq.front;
	while(curr)
	{
		printf("%d ", curr->val);
		curr = curr->next;
	}
	printf("\n");
}

void print_revlist()
{
	node *curr = deq.back;
	while(curr)
	{
		printf("%d ", curr->val);
		curr = curr->prev;
	}
	printf("\n");
}

void free_list()
{
	node *tmp;
	node * curr = deq.front;
	while(curr)
	{
		tmp = curr;
		curr = curr->next;
		free(tmp);
	}
}

int main(void) {
	push(123);
	push(456);
	inject(789);
	inject(500);
	print_list();
	print_revlist();
	pop();
	eject();
	print_list();
	free_list();
	return 0;
}
```
