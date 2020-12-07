# 北京大學 - C++程序設計 (Coursera) STL相關

第1～6週[筆記在這](/SXujAxaTR9KkZGFqBUt68g)
第7週[筆記在這](/JPMUh5LvSVeEo0hfWmK7AA)
C++11新特性[筆記在這](/wNsVLFPfTOie9ZYSktnirA)

## 第八週

1. STL概述

   C++ 語言的核心優勢之一就是便於軟體的重用(reuse)
   C++ 中有兩個方面體現重用：
   1. 物件導向的思想：繼承和多態，標準類別庫
   2. 泛型程式設計(generic programming)的思想：模板機制，以及標準模板庫STL

   STL將一些常用的資料結構(比如linked list、array、binary tree)和演算法(比如sort、find)寫成template，以後則不論資料結構裡放的是什麼物件，演算法針對什麼樣的物件，都不必個別重新實作。
   
   標準模板庫(Standard Template Library)就是一種常用資料結構和演算法的集合，且效率非常高。
   
   STL中的基本概念：
   * 容器(Container)：可容納各種資料類型的通用資料結構，是類別模板(class template)。
   * 疊代器(Iterator)：可用於依次序存取容器中元素，類似於指針(pointer)。
   * 演算法(Alogorithm)：用來操作容器中的元素的函數模板(function template)。
     - sort()用來對一個vector中的資料進行排序
     - find()來搜索一個lsit中的物件
     演算法本身與它們操作的資料類型無關，因此它們可以再從簡單陣列到高度複雜容器的任何資料結構上使用。

   舉個例子：
   ```c
   int array[100];
   ```
   該陣列就是容器，而int * 類型的指針變數就可以做為疊代器，sort演算法就可以用於在容器上，對其進行排序：
   ```c
   sort(array, array + 70);	// 將前70個元素進行排序
   // array跟array+70都是int *類型(即int的指針)
   ```
   
   ### 容器概述
   可以用於存放各種類型的資料（基本類型的變數、物件等）的資料結構，都是類別模板，分為三種：
   1. 順序容器
      vector, deque, list
   2. 關聯容器
      set, multiset, map, multimap
   3. 容器適配器
      stack, queue, priority_queue
	  
   物件被插入容器中時，被插入的適物件的一個複製品。
   許多演算法，比如排序、查找，要求對容器中的元素進行比較，有的容器本身就是排序的，所以放入容器的物件所屬的類別，往往還應該重載'=='和'<'運算子。
   
   順序容器：
   
   容器並非排序的，元素的插入位置和元素的值無關。
   有vetor、deque、list三種
   * vector ```#include <vector>```
     動態陣列。元素在內存中連續存放。隨機存取任何元素都能在O(1)時間完成。
	 在「尾端」增刪元素具有較佳的性能(大部分情況下是常數時間)。
	 因為vector的實作是預先分配2^n個連續空間，然而當空間用完時又需要新增資料時，需要拷貝原本的資料到另一個更大的連續空間中，此時需要O(n)的時間。
	 另外，在非尾端的地方插入/刪除元素時(中間)，需要把後面的全部元素作後移/前移，此時時間也是O(n)的。
   * deque ```#linclude <deque>```
     雙向佇列。元素在內存連續存放，隨機元素存取任何元素都能在常數時間完成(但次於vector)。
	 在兩端增刪元素具有較佳的性能(大部分情況下是常數時間)。
	 其實作具有一個head指針指向第一個元素，tail指針指向最後一個元素的後一個，而前後都有預留一些增加元素的空間。
	 與vector相同，只有在空間用完又需要在頭尾增加資料時才需要O(n)的時間。
	 由於tail指針不一定要在head之後(即尾端資料在內存中可以是放到head之前的)，要檢查是否有越界(指超過分配的連續內存空間最尾端)是需要時間的，因此隨機存取略遜於vector。
   * list ```#include <list>```
     雙項鏈節串列。元素在內存不連續存放。
	 在任何位置增刪元素都能在常數時間完成，但不支援隨機存取。
	 
   關聯容器：
   
   容器是排序的，插入任何元素，都按相應的排序規則來確定其位置(由元素的「值」來決定)。
   在「查找」時具有非常好的性能。
   通常以平衡二元樹(balanced binary tree)實現，插入和搜尋的時間都是O(log n)。
   * set / multiset ```#include <set>```
     set即集合。
	 set中部允許相同元素，multiset中允許存在相同的元素。
   * map / multimap ```#include <map>```
     map與set不同在於map中存放的元素友且僅有兩個成員變數，一個名為first，另一個名為second。
	 map根據first值隊元素進行從小到大排序，並可以快速地根據first來檢查元素。
	 map與multimap的不同在於是否允許相同first值的元素。
   
   容器適配器：
   
   * stack ```#include <stack>```
     堆疊。是項的有限序列，並滿足序列中被刪除、檢索和修改的項只能是最近插入序列的項(棧頂的項)。
	 後進先出。
	 實作上有一個top指針指項stack中最頂端(最後增加)的項，bottom指向最底端(最早增加)的項。
	 支援push增加元素到頂端、pop從頂端移除元素。
   * queue ```#linclude <queue>```
     佇列。插入只可以在尾部進行，刪除和修改只允許從頭部進行。
	 先進先出。
	 實作上有一個front指向第一個元素，rear指向最尾端元素後一格。
	 支援push增加元素到尾部、pop從頭部刪除元素。
   * priority_queue ```#include <queue>```
     優先權佇列。
	 最高優先級元素總是第一個出列。
	 
   順序容器和關聯容器中都有的成員函數：
   | 函數 | 功能 |
   | -------- | -------- |
   | begin | 返回指向容器中第一個元素的疊代器 |
   | end | 返回指向容器中最後一個元素「後面一個」位置的疊代器 |
   | rbegin | 返回指向容器中最後一個元素的疊代器 |
   | rend | 返回指向容器中第一個元素「前面一個」位置的疊代器 |
   | erase | 從容器中刪除一個或幾個元素 |
   | clear | 從容器中刪除所有元素 |

   順序容器的常用成員函數：
   | 函數 | 功能 |
   | -------- | -------- |
   | front | 返回容器中第一個元素的引用 |
   | back | 返回容器中最後一個元素的引用 |
   | push_back |刪除容器末尾的元素 |
   | rease | 刪除疊代器指向的元素(可能會使該疊代器失效)，或刪除一個區間，返回被刪除元素「後面的那個元素」的疊代器。 |
   
   疊代器：
   
   * 用於指向順序容器和關聯容器中的元素
   * 疊代器用法和指針類似
   * 有const和非const兩種
   * 通過疊代器可以讀取它指向的元素
   * 通過非const疊代器還能夠修改其指向的元素
   
   定義一個容器類的疊代器的方法可以是：
   ```容器類名::iterator 變數名;```
   或：
   ```容器類名::const_iterator 變數名;```
   訪問一個疊代器指向的元素：
   ```* 疊代器變數名```
   
   疊代器上可以執行++操作，以使其指向容器中的下一個元素。
   如果疊代器到達了容器中最後一個元素的後面，此時再使用它，就會出錯，類似於使用NULL或未初始化的指針一樣。
   
   疊代器範例：
   ```c++
   #include <vector>
   #include <iostream>
   using namespace std;
   
   int main()
   {
   	vector<int> v;	// 一個存放int元素的陣列，一開始裡面沒有元素
		v.push_back(1);
		v.push_back(2);
		v.push_back(3);
		v.push_back(4);
		vector<int>::const_iterator i;	// 常數疊代器
		for(i = v.begin(); i != v.end(); ++i)
			cout << * i << ", ";
		cout << endl;
		
		vector<int>::reverse_iterator r;	// 反向疊代器，跟正向的疊代器類型不兼容
		for(r = v.rbegin(); r != r.rend(); ++r)
			cout << * r << ", ";
		cout << endl;
		
		vector<int>::iterator j;	// 非常數疊代器
		for(j = j.begin(); j != j.end(); ++j)
				* j = 100; // 可以透過此疊代器去修改容器中元素的值
		for(j = j.begin(); j != j.end(); ++j)
			cout << * j << ", ";
   }
   ```
   輸出結果：
   ```
   1, 2, 3, 4, 
   4, 3, 2, 1, 
   100, 100, 100, 100, 
   ```
   
   雙向疊代器：
   若p和p1都是雙向疊代器，則可以對p、p1進行以下操作：
   | 操作             | 功能                    |
   | ---------------- | ----------------------- |
   | ```++p, p++```         | 使p指向容器中下一個元素 |
   | ```--p, p--```        | 使p指向容器中上一個元素 |
   | * p              | 取p指向的元素           |
   | p = p1           | 賦值                    |
   | p == p1, p != p1 | 判斷是否相等、不等      |
   
   隨機訪問疊代器：
   若p和p1都是隨機訪問疊代器， 
   則雙向疊代器的所有操作p、p1都可以使用:star:
   另外還有：
   
   | 操作                             | 功能                                      |
   | -------------------------------- | ----------------------------------------- |
   | p += i                           | 將p向後移動i個元素                        |
   | p -= i                           | 將p向前移動i個元素                        |
   | p + i                            | 值為：指向p後面的第i個元素的疊代器        |
   | p - i                            | 值為：指向p前面的第i個元素的疊代器        |
   | p[i]                             | 值為：p所指元素後面的第i個元素的引用      |
   | p < p1, p <= p1, p > p1, p >= p1 | 比較前後順序，若p < p1為true代表p在p1之前 |
   
   至於是雙向還是隨機訪問，是根據容器本身決定的：
   | 容器           | 容器上的疊代器種類 |
   | -------------- | ------------------ |
   | vector         | 隨機訪問           |
   | deque          | 隨機訪問           |
   | list           | 雙向               |
   | set / multiset | 雙向               |
   | map / multimap | 雙向               |
   | stack          | 不支援疊代器       |
   | queue          | 不支援疊代器       |
   | priority+queue | 不支援疊代器       |
   
   注意：有的演算法，例如sort、binary_search需要通過隨機訪問疊代器來訪問容器中的元素，那麼list及關聯容器就不支援該演算法。
   
   vector的疊代器是隨機訪問疊代器，
   遍歷vector可以有以下幾種做法(deque亦然)：
   ```c++
   vector<int> v(100)
   int i;
   
   for (i = 0; i < v.size; ++i)
   	cout << v[i];	// 根據下標隨機訪問
	
   vector<int>::const_iterator ii;
   for (ii = v.begin(); ii != v.end(); ++i)
   	cout << * ii;
	
   // 隨機訪問疊代器可以比大小
   for (ii = v.begin(); ii < v.end(); ++i)
   	cout << * ii;
	
   // 若要間隔一個輸出
   ii = v.begin();
   while(ii < v.end())
   {
   	cout << * ii;
		ii = ii + 2;
   }
   ```
   
   list的疊代器是雙向疊代器，正確遍歷list的方法：
   ```c++
   list<int> v;
   list<int>::const_iterator ii;
   
   for(ii = v.begin(); ii != v.end(); ++i)
   	cout << * ii;
   ```
   錯誤的做法```ii < v.end()```
   雙向疊代器不支持<，list也沒有[]成員函數，因此：
   ```c
   for(int i = 0; i < v.size(); ++i)
   	cout << * v[i];
   ```
   也是不對的。
   
   ### 演算法簡介
   
   演算法就是一個個函數模板，大多數在標頭檔```<algorithm>```中定義。
   STL中提供能在各種容器中通用的演算法比如查找，排序等。
   * 演算法通過疊代器來操作容器中的元素。許多眼演算法可以對容器中的一個局部區間進行操作，因此需要兩個參數，一個是起始元素疊代器，一個是終止元素的「後面一個」元素的疊代器。
     比如：排序和查找
   
   * 有的演算法返回一個疊代器。比如find()算法，在容器中查找一個元素，並返回一個指向該元素的疊代器。
   * 演算法可以處理容器，也可以處理陣列。
   
   演算法範例：find()
   ```c++
   template<class InIt, class T>
   InIt find(InIt first, InIt last, const T& val);
   ```
   
   * first和last這兩個參數都是容器的疊代器，他們給出了容器中的查找區間起點和終點```[first, last)```，區間的起點是位於查找範圍之中的，而終點不是(左閉右開)。
     find在```[first, last)```查找等於val的元素。
   
   * 用 == 運算子判斷相等
   
   * 函數返回值是一個疊代器。如果找到則該疊代器指向被找到的元素。
     如果找不到，則該疊代器等於last。
   
   * 複雜度O(n)，順序查找
   
   例子：
   ```c++
   #include <vector>
   #include <algorithm>
   #include <iostream>
   
   using namespace std;
   
   int main()
   {
   	int array[10] = {10, 20, 30, 40};
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);
	vvector<int>::iterator p;
	p = find(v.begin(), v.end(), 3);
	if(p != v.end())
		cout << * p << endl;	// 輸出3
		
	p = find(v.begin(), v.end(), 9);
	if(p == v.end())
		cout << "not found" << endl;
		
	p = find(v.begin(), v.end() - 2, 1); // 整個容器：[1, 2, 3, 4]，查找區間：[2, 3)
	if(p != v.end())	// 找不到1，因此iterator停在last
		cout << * p << endl;	// 輸出3
		
	int * pp = find(array, array + 4, 20);	// 陣列名是疊代器
	cout << * pp << endl;
   }
   ```
   
   以上程式碼執行結果為：
   ```
   3
   not found
   3
   20
   ```
   
   STL中"大"和"小"的概念
   關聯容器內部的元素是從小到大排序的
   有些演算法要求其操作的區間是從小到大排序的，稱為「有序區間演算法」，例如：binary_search
   有些演算法會對區間進行從小到大排序，稱為「排序演算法」，例如：sort。
   還有一些其他演算法會用到"大"、"小"的概念
   
   註：大小的概念可以自己定義，例如整數按個位數字排序
   
   使用STL時，在缺省的情況下(沒有自己定義大小的情況)，以下三個說法等價：
   (1) x比y小
   (2) 表示式"x < y"為真
   (3) y比x大
   
   * 有時，「x和y相等」等價於「x == y為真」
     例：在未排序的區間上進行的演算法，如順序查找find()
   
   * 有時，「x和y相等」等價於「x < y 和 y < x同時為假」
     例：有序區間演算法，如binary_search、關聯容器自身的成員函數find(跟algorithm中定義的不同)
   
   STL中"相等"的概念：
   ```c++
   #include <iostream>
   #include <algorithm>
   
   using namespace std;
   
   class A
   {
   	int v;
		public:
				A(int n):v(n){}
				bool operator<(const A & a2) const
				{
					cout << v << "v" << a2.v << "?" << endl;
					return false;	// 總是不成立，代表沒有任何一個A會小於另一個A
				}
				bool operator==(const A & a2) const
				{
					cout << v << "==" << a2.v << "?" << endl;
					return v == a2.v;
				}
   };
   
   int main()
   {
   	A a[] = {A(1), A(2), A(3), A(4), A(5)};
		cout << binary_search(a, a+4, A(9));	// 二分搜尋法
		return 0;
   }
   ```
   
   
   想法上A(9)應該是找不到的，應該返回0，
   但是以上程式執行後輸出：
   ```
   3<9?
   2<9?
   1<9?
   9<1?
   1
   ```
   
   原因是運行的過程中，運算子'=='並沒有被呼叫，binary_search在判斷時用的是operator<。
   所以過程如下：
      ```
   3<9?		// 不成立，所以binary_search往前找{A(1),A(2)}
   2<9?		// 不成立，所以binary_search往前找{A(1)}
   1<9?		// 不成立，已經沒有其他元素，binary_search考慮A(1)是否就是A(9)
   9<1?		// 不成立，且A(1)和A(9)之間互相不小於對方，binary_search認為A(1)就是A(9)
   1		// 返回true
   ```
   
2. 順序容器vector
   
   * 可變長的動態陣列
   * 必須```#include <vector>```
   * 支援隨機訪問疊代器
     - 根據下標隨機訪問某個元素時間為常數
     - 在尾部添加速度很快
     - 在中間插入慢
   * 所有STL演算法都能對vector操作
   
   建構函數：
   
   | 成員函數                              | 作用                                                          |
   | ------------------------------------- | ------------------------------------------------------------- |
   | vector()                              | 無參構造函數                                                  |
   | vector(int n)                         | 將如慶初始化成有n個元素                                       |
   | vector(int n, const T & val)          | 假定元素類型是T，將容器初始化成有n個元素，每個元素的值都是val |
   | vector(iterator first, iterator last) | 將容器初始化為與別的容器上區間```[first, last)``` 一致的內容  |

   其他常用函數：
   
   | 成員函數                      | 作用                         |
   | ----------------------------- | ---------------------------- |
   | void pop_back();              | 刪除容器末尾的元素           |
   | void push_back(const T & val) | 將val添加到容器末尾          |
   | int size();                   | 返回容器中元素的個數         |
   | T & font();                   | 返回容器中第一個元素的引用   |
   | T & back()                    | 返回容器中最後一個元素的引用 |

   ```c++
   #include <iostream>
   #include <vector>
   using namespace std;
   int main()
   {
	   int i;
	   int a[5] = {1, 2, 3, 4, 5};
	   vector<int> v(5);	// 有5個元素的vector，初始值0
	   cout << v.end() - v.begin() << endl;	// 輸出5
	   
	   for( i = 0; i < v.size(); ++i)
	   	v[i] = i;	// 可以用下標訪問
	   v.at(4) = 100;	// 也可以用at訪問，此時會檢查是否超過boundary
	   
	   for( i = 0; i < v.size(); ++i)
		cout << v[i] << ",";
	   cout << endl;
	   
	   vector<int> v2(a, a + 5); // 建構函數，把陣列a的內容copy給v2
	   
	   v2.insert(v2.begin() + 2, 13); // 在begin()+2位置插入13
	   
	   for( i = 0; i < v2.size(); ++i)
	   	cout << v2.at(i) << ",";
   }
   ```
   以上程式碼執行結果：
   ```
   5
   0,1,2,3,100
   1,2,13,3,4,5
   ```
   
   二維動態陣列：
   ```c++
   vector<vector<int>> v(3)
   // v有3個元素
   // 美個元素都是vector<int>容器
   ```
   
   例子：
   ```c++
   #include <iostream>
   #include <vector>
   
   using namespace std;
   
   int main()
   {
	   vector<vector<int>> v(3);
	   for(int i = 0; i < v.size(); ++i)
	   	for(int j = 0; j < 4; ++j)
	   		v[i].push_back(j);
			
	   for(int i = 0; i < v.size(); ++i){
	   	for(int j = 0; j < v[i].size(); ++j)
	   		cout << v[i][j] << “ ”;
	   	cout << endl;
	   }
	   return 0;
   }
   ```
   輸出：
   ```
   0 1 2 3
   0 1 2 3
   0 1 2 3
   ```
   
3. List和Deque
   
   ### list容器：
   * 雙向鏈結串列 ```#include <list>```
   * 在任何位置插入/刪除都是常數時間
   * 不支援根據下標隨機存取元素
   * 具有所有順序容器都有的成員函數
   
   還支援8個成員函數：
   | 成員函數   | 作用                                                                           |
   | ---------- | ------------------------------------------------------------------------------ |
   | push_front | 在鏈表「最前面」插入                                                               |
   | pop_front  | 刪除鏈表「最前面」的元素                                                           |
   | sort       | 排序(list不支持STL的演算法sort)                                                |
   | remove     | 刪除和指定值相等的所有元素                                                     |
   | unique     | 刪除所有和前一個元素相同的元素                                                 |
   | merge      | 合併兩個鏈表，並清空被合併的鏈表。                                             |
   | reverse    | 顛倒鏈表                                                                       |
   | splice     | 在指定位置前面插入另一鏈表中的一個或多個元素，並在另一鏈表中刪除被插入的元素。 |

   
   list容器的疊代器不支持完全隨機訪問 $\Rightarrow$ 所以不能用標準庫中sort函數對它進行排序
   list自己的sort成員函數：
   ```c++
   list<T> classname
   classname.sort(compare);	// compare函數可以自己定義
   classname.sort();	// 無參數版本，按<排序
   ```
   list容器只能使用雙向疊代器
   $\Rightarrow$ (疊代器本身而非其指向的值)不支援大於/小於比較運算子、[]運算子和隨機移動(即類似"list的疊代器+2"的操作)
   
   ```c++
   #include <list>
   #include <iostream>
   #include <algorithm>
   
   using namespace std;
   
   class A	// 定義類別A，並以友元重載、==和<<
   {
	   private:
	   	int n;
	   public:
		   A(int n_)
		   {
		   n=n_;
		   }
		   friend bool operator<(const A & a1, const A & a2);
		   friend bool operator==(const A & a1, const A & a2);
		   friend bool operator<<(ostream & o, const A & a2);
   };
   
   bool operator<(const A & a1, const A & a2)
   {
	   return a1.n<a2.n;
   }
   
   bool operator==(const A & a1, const A & a2)
   {
	   return a1.n==a2.n;
   }
   
   bool operator<<(ostream & o, const A & a2)
   {
	   o << a.n;
	   return o;
   }
   
   // 定義函數模板PrintList，打印列表中的對向
   template <class T>
   void PrintList(const list<T> & lst)
   {
	   int tmp = lst.size();
	   if(tmp > 0)
	   {
		   //typename用來說明list<T>::const_iterator是個類型
		   typename list<T>::const_iterator i;
		   i = lst.begin();
		   for(i = lst.begin(); i != lst.end(); ++i)
			cout << * i << ",";
	   }	// 與其他順序容器不同，list只能使用雙向疊代器
   }	// 因此不支持大於/小於比較運算子、[]運算子和隨機移動
   ```
   
   關於typename的用法：[參考wiki](https://zh.wikipedia.org/wiki/Typename)，主要是用於template中指定某個陳述句是class的宣告而非變數名稱，以消除歧義，如：```T::bar * p```中的bar究竟是某個class還是某個變數名稱，若是class則此陳述句是用來宣告一個指針，若是變數名稱則此陳述句是作乘法運算。
   
   上述程式碼搭配以下main函數：
   ```c
   int main()
   {
	   list<A> lst1, lst2;
	   lst1.push_back(1); lst1.push_back(3);
	   lst1.push_back(2); lst1.push_back(4); lst1.push_back(2);
	   lst2.push_back(10); lst2.push_front(20);
	   lst2.push_back(30); lst2.push_back(30);
	   lst2.push_back(30); lst2.push_front(40); lst2.push_back(40);
	   cout << "1) ";	PrintList(lst1); cout << endl;
	   cout << "2) ";	PrintList(lst2); cout << endl;
	   lst2.sort();	// list容器的sort函數
	   cout << "3) ";	PrintList(lst2); cout << endl;
   ```
   上述程式碼執行結果輸出：
   ```
   1) 1,3,2,4,2,
   2) 40,20,10,30,30,30,40,
   3) 10,20,30,30,30,40,40,
   ```
   
   接著進續進行：
   ```c
   lst2.pop_front();
   cout << "4) "; PrintList( lst2); cout << endl;
   lst1.remove(2); // 刪除所有和A(2)相等的元素
   cout << "5) "; PrintList( lst1); cout << endl;
   lst2.unique(); // 刪除所有和前一個元素相等的元素
   cout << "6) "; PrintList( lst2); cout << endl;
   lst1.merge (lst2); // 合併lst2到lst1並清空lst2
   cout << "7) "; PrintList( lst1); cout << endl;
   cout << "8) "; PrintList( lst2); cout << endl;
   lst1.reverse();
   cout << "9) "; PrintList( lst1); cout << endl;
   ```
   輸出為：
   ```
   4) 20,30,30,30,40,40,
   5) 1,3,4,
   6) 20,30,40,
   7) 1,3,4,20,30,40,
   8)
   9) 40,30,20,4,3,1,
   ```
   
   再繼續進行：
   ```c
	   lst2.push_back (100); lst2.push_back (200);	// lst1: 40,30,20,4,3,1
	   lst2.push_back (300); lst2.push_back (400);	// lst2: 100,200,300,400
	   
	   list<A>::iterator p1, p2, p3;
	   
	   p1 = find(lst1.begin(), lst1.end(), 3);	// p1指向lst1中的3
	   p2 = find(lst2.begin(), lst2.end(), 200);	// p2指向lst2中的200
	   p3 = find(lst2.begin(), lst2.end(), 400);	// p3指向lst2中的400
	   
	   // 將[p2, p3)插入p1之前，並從lst2中刪除[p2, p3)
	   lst1.splice(p1, lst2, p2, p3);
	   
	   cout << "11) "; PrintList( lst1); cout << endl;
	   cout << "12) "; PrintList( lst2); cout << endl;
   }
   ```
   
   輸出：
   ```
   11) 40,30,20,4,200,300,3,1,
   12) 100,400,
   ```
   
   ### deque容器：
   * 雙向佇列
   * 必須包含標頭檔```#include <deque>```
   * 所有適用於vector的操作 $\Rightarrow$ 都適用於deque
   * deque還有```push_front```(將元素插入到容器的頭部)和```pop_front```(刪除頭部的元素)操作
   * deque本身沒有sort成員函數，因為它可以使用```<algorithm>```中的sort函數(支援隨機訪問疊代器)
   
3. 函數物件

   若一個類別重載了運算子```()```，則該類別的物件就成為函數物件。
   ```c++
   class CMyAverage    // 函數物件類別
   {
       public:
           double operator() (int a1, int a2, int a3)
           {
               return (double)(a1 + a2 + a3) / 3;
           }
   };
   
   CMyAverage average;    // 函數物件
   cout << average(3, 2, 3);    // average.operator()(3, 2, 3)
   ```
   輸出```2.66667```
   
   函數物件在STL裡是有很廣泛地應用的，如Dev C++中的Accumulate源代碼1：
   ```c
   template<tempname _InputIterator, typename _TP>
   _TP accumulate(_InputIterator __first, _InputIterator __last, _TP __init)
   {
       for(; __first != __last; ++__first)
           __init = __init + *__first;
       return __init;
   }
   // typename等價於class
   ```
   
   Dev C++中的Accumulate源代碼2：
   ```c++
   template<tempname _InputIterator, typename _TP, typename _BinaryOperation>
   _TP accumulate(_InputIterator __first, _InputIterator __last, _TP __init, _BinaryOperation __binary_op)
   {
       for(; __first != __last; ++__first)
           __init = __binary_op(__init, *__first);
       return __init;
   }
   ```
   呼叫accumlate時，和__binary_op對應的參數可以是個函數或是函數物件
   
   函數物件的應用實例：
   ```c++
   #include <iostream>
   #include <vector>
   #include <algorithm>
   #include <numeric>
   #include <functional>
   
   using namespace std;
   
   int sumSquares(int total, int value)
   {
       return total + value * value;
   }
   
   template <class T>
   void PrintInterval(T first, T last) // 輸出區間[first, last)中的元素
   {
       for(;first != last; ++first)
       cout << * first << " ";
       cout << endl;
   }
   
   template<class T>
   class SumPowers
   {
       private:
           int power;
       public:
           SumPowers(int p): power(p) { }
           
           const T operator() (const T & total, const T & value)
           {
               // 計算value的power次方，加到total上
               T v = value;
               for(int i = 0; i < power - 1; ++i)
               v = v * value;
               return total + v;
           }
   };
   
   int main()
   {
       const int SIZE = 10;
       int a1[ ] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
       
       vector<int> v(a1, a1 + SIZE);
       cout << "1) "; PrintInterval(v.begin(), v.end());
       
       int result = accumulate(v.begin(), v.end(), 0, sumSquares);
       cout << "2) 平方和：" << result << endl;
       
       result = accumulate(v.begin(),v.end(), 0, SumPowers<int>(3));
       cout << "3) 立方和：" << result << endl;
       
       result = accumulate(v.begin(),v.end(), 0, SumPowers<int>(4));
       cout << "4) 4次方和：" << result;
       return 0;
   }
   ```
   以上程式執行後輸出：
   ```
   1) 1 2 3 4 5 6 7 8 9 10
   2) 平方和：385
   3) 立方和：3025
   4) 4次方和：25333
   ```
   
   其中
   ```c++
   int result = accumulate(v.begin(), v.end(), 0, sumSquares);
   ```
   這個陳述句，會實例化出：
   ```c++
   int accumulate(vector<int>::iterator first, vector<int>::iterator last, int init, int(* op)(int, int))
   {
       for (; first != last; ++first)
       init = op(init, *first);
       return init;
   }
   // 原本的_TP指定為int，_BinaryOperation指定為函數指針(因為傳進來的是函數名)
   // 函數指針名稱op，返回int類型，接受兩整數參數(int, int)
   ```
   
   接著：
   ```c++
   accumulate(v.begin(),v.end(),0,SumPowers<int>(3)); 
   ```
   會實例化出：
   ```c++
   int accumulate(vector<int>::iterator first,vector<int>::iterator last, int init, SumPowers<int> op)
   {
       for ( ; first != last; ++first)
           init = op(init, *first);
       return init;
   }
   ```
   上述```SumPowers<int>(3)```是一個「函數物件」
   ```op(init, *first)```則是調用了SumPower的成員函數```operator()```
   同理，```SumPowers<int>(4)```也是相同的原理。
   
   此處可以發現函數物件的好處，假如我們要各種不同的次方，若是使用一般函數，無法傳遞我們需要的次方數，除非我們用一個全域變數來存，或是編寫不同的function。
   然而，函數物件可以利用建構子和重載operator()來傳遞，不需要一直重複編寫功能差不多的函數，並且保持了整體程式很好的局部性。
   
   STL中有些函數物件的類別模板，以下模板可以用來生成函數物件：
   * equal_to
   * greatre
   * less
   * 其他更多...
   
   這些都是template，然後它們又都實現了operator()這個成員函數，所以稱之為函數物件類別模板。
   以上類別模板都在標頭檔```<functional>```中定義。
   
   舉例：
   ```c
   template <class T>
   struct greater: public binary_function<T, T, bool>
   {
       bool operator(const T & x, const T & y) const
       {
            return x > y;
       }
   };
   ```
   
   若有某個關聯容器或是演算法使用了greatrt作為比較器的話，那麼這些容器和演算法就會呼叫operator()，並傳進x和y當作參數。
   當此函數回傳值為true，則容器和演算法會認為x是小於y的，然而實際上當x > y表示式為true時才是true。
   也就是跟傳統意義上的「大」是相反的，所以greater有什麼用呢？
   
   list有兩個sort成員函數
   * ```void sort();```
      無參數版本，將list中的元素案"<"規定的比較方法升序排列。

   * ```c++
     template <class Compare>
     void sort(Compare op);
     ```
     將list中的元素按op規定的比較方法升序排列，即要比較x、y大小時，看```op(x, y)```的回傳值決定，為true則認為x小於y。
     具體例子：
     ```c++
     #include <list>
     #include <iostream>
     
     using namespace std;
     
     class MyLess
     {
         public:
             bool operator()(const int & c1, const int & c2)
             {
                 // 個位數小的就誰小
                 return (c1 % 10) < (c2 % 10);
             }
     };
     
     template <class T>
     void Print(T first, T last)
     {
         for(; first != last ; ++first )
             cout << * first << ",";
     }
     
     int main()
     {
         const int SIZE = 5;
         int a[SIZE] = {5, 21, 14, 2, 3};
         list<int> lst(a, a + SIZE);
         
         lst.sort(MyLess());    // 個位數從小排到大
         Print(lst.begin(), lst.end());
         cout << endl;
         
         lst.sort(greater<int>()); // greater<int>()是個物件，越大的數對此list來說越小，要排越前面
         Print(lst.begin(), lst.end());
         cout << endl;
         return 0;
     }
     ```
     上述例子輸出：
     ```
     21,2,3,14,5,
     21,14,5,3,2,
     ```
   
   ### 在STL中使用自定義的「大」、「小」關係
   關聯容器和STL中許多演算法，都是可以用函數或函數物件自定義比較器(comparator)的，
   在自定義了比較器op的情況下，以下三種說法是等價的：
   
   (1) x小於y
   (2) op(x, y)返回值為true
   (3) y大於x
   
   例題：
   寫出MyMax模板
   ```c++
   #include <iostream>
   #include <iterator>
   
   using namespace std;
   
   class MyLess // 誰的個位數小誰就算小，函數物件
   {
       public:
           bool operator() (int a1, int a2)
           {
               if((a1 % 10) < (a2 % 10))
                   return true;
               else
                   return false;
           }
   };
   
   bool MyCompare(int a1, int a2)    // 誰的個位數大誰就算大，普通函數
   {
       if((a1 % 10) < (a2 % 10))
           return false;
       else
           return true;
   }
   
   int main()
   {
       int a[] = {35, 7, 13, 19, 12};
       cout << * MyMax(a, a + 5, MyLess()) << endl;    // 印出個位數最大的數
       cout << * MyMax(a, a + 5, MyCompare) << endl;    // 印出個位數最小的數
       return 0;
   }
   ```
   要求輸出為：
   ```
   19
   12
   ```
   
   Ans:
   ```c++
   template <class T, class Pred>
   T MyMax(T first, T last, Pred myless)
   {
       T tmpMax = first;
       for(; first != last; ++first)
       if(myless(* tmpMax, * first))
           tmpMax = first;
       return tmpMax;
   };
   ```
   
   我自己的解答：
   ```c++
   template<typename T, typename _BinaryOperation>
   T * MyMax(T * a1, T * a2, _BinaryOperation op)
   {
       sort(a1, a2, op);
       return a2 - 1;
   }
   ```
   
   
---

## 第九週

1. Set和Multiset
   
   set、multiset、map、multimap
   * 內部元素有序排列，新元素插入的位置取決於它的值，搜尋速度快
   * 除了各容器都有的函數外，還支持以下成員函數：
     
     | 成員函數    | 功能                                                     |
     | ----------- | -------------------------------------------------------- |
     | find        | 查找等於某個值的元素(x < y和y < x同時不成立即為相等)     |
     | lower_bound | 尋找某個下界                                             |
     | upper_bound | 尋找某個上界                                             |
     | equal_range | 同時尋找上界和下界                                       |
     | count       | 計算等於某個值的元素個數(x < y和y < x同時不成立即為相等) |
     | insert      | 用以插入一個元素或一個區間                               |

   預備知識：pair模板
   ```c++
   template<class _T1,  class _T2>
   struct pair
   {
       typedef _T1 first_type;
       typedef _T2 second_type;
       _T1 first;
       _T2 second;
       pair(): first(), second(){}
       pair(const _T1 & __a, const _T2 & __b): first(__a), second(__b){}
       template<class _U1, class_U2>
       pair(const pair<_U1, _U2> & __p): first(__p.first), second(__p.second){}  
   };
   ```
   map / multimap容器裡放著的都是pair模板類別的物件，且按first從小到大排序。
   第三個建構函數用法示例：
   ```c
   pair<int, int> p(pair<double, double>(5.5, 4.6));
   // 此時p.first = 5, p.second = 4
   ```
   
   ### multiset
   ```c++
   template<class Key, class Pred = less<Key>, class A = allocator<Key>>
   class multiset {......};
   ```
   註：template的參數也可以有預設值
   * Pred類型的變數決定了multiset中的元素，"一個比另一個小"是怎麼定義的。
     multiset執行的過程中，比較兩個元素x, y的大小的做法，就是生成一個Pred類型的變數，假定為op，若表示式op(x, y)返回值為true，則x比y小。
     op可以是函數指針或函數物件。
     Pred的預設值類型是```less<Key>```
   
   * less模板的定義：
     ```c
     template<class T>
     struct less: public binary_function<T, T, bool>
     {
         bool operator()(const T & x, const T & y) const
         {
             return x < y;
         }
     };
     // less模板是靠 < 來比大小的
     ```
   
   multiset的成員函數：
   | 成員函數                                             | 功能                                                                                                                     |
   | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
   | iterator find(const T & val);                        | 在容器中尋找值為val的值，返回其疊代器。如果找不到，返回end()。                                                           |
   | iterator insert(const T & val);                      | 將val插入到容器中並返回其疊代器。                                                                                        |
   | void insert(iterator first, iterator last);          | 將區間```[first, last)``` 差入容器。                                                                                     |
   | int count(const T & val);                            | 統計有多少個元素的值和val相等。                                                                                          |
   | iterator lower_bound(const T & val);                 | 尋找一個最大的位置it，使得```[begin(), it)```中所有的元素都比val小，即以val這個值作為下界，之後每次++it的值都會比val大。 |
   | iterator upper_bound(const T & val);                 | 尋找一個最小的位置it，使得```[it, end())```中所有元素都比val大，即以val這個值作為上界，可以用在終止迴圈的條件。          |
   | pair<iterator, iterator> equal_range(const T & val); | 同時求得lower_bound和upper_bound。                                                                                       |
   | iterator erase(iterator it);                         | 刪除it指向的元素，返回其後面的元素的疊代器(Visual studio 2010上如此，但是在C++標準中不是)。                              |

   multiset的用法：
   ```c++
   #include <set>
   using namespace std;
   
   class A { };
   
   int main()
   {
       multiset<A> a;
       a.insert(A()); //error
   } 
   ```
   
   why?
   ```multiset<A> a;```
   等價於：
   ```multiset<A, less<A>> a;```
   插入元素時，multiset會將被插入元素和已有元素進行比較。
   由於less模板是用'<'進行比較的，所以要求A的物件要能使用 < 運算子來進行比較，因此我們必須要重載operator<才行。
   
   ```c++
   #include <iostream>
   #include <set> // 使用multiset必須包含此標頭檔
   using namespace std;
   template <class T>
   void Print(T first, T last)
   {
       for(;first != last ; ++first) cout << * first << " ";
           cout << endl;
   }
   class A
   {
       private:
           int n;
       public:
           A(int n_ ) { n = n_; }
           friend bool operator< ( const A & a1, const A & a2 ) { return a1.n < a2.n; }
           friend ostream & operator<< ( ostream & o, const A & a2 ) { o << a2.n; return o; }
           friend class MyLess;
   };
   
   struct MyLess
   {
       bool operator()( const A & a1, const A & a2)    // 按個位數比大小
       {
           return ( a1.n % 10 ) < (a2.n % 10);
       }
   };
   
   typedef multiset<A> MSET1;    // MSET1用'<'比較大小
   typedef multiset<A, MyLess> MSET2;    // MSET2用MyLess::operator()比較大小
   
   int main()
   {
       const int SIZE = 6;
       A a[SIZE] = { 4, 22, 19, 8, 33, 40 };
       MSET1 m1;
       m1.insert(a, a + SIZE);
       m1.insert(22);
       cout << "1) " << m1.count(22) << endl; // 輸出 1) 2
       cout << "2) "; Print(m1.begin(), m1.end()); // 輸出 2) 4 8 19 22 22 33 40
       // m1元素：4 8 19 22 22 33 40
       
       MSET1::iterator pp = m1.find(19);
       if( pp != m1.end() ) // 若條件內為真表示找到
           cout << "found" << endl; // 輸出found
       cout << "3) "; cout << * m1.lower_bound(22) << "," << * m1.upper_bound(22) << endl;
       // 輸出 3) 22,33
       
       pp = m1.erase(m1.lower_bound(22),m1.upper_bound(22));
       // pp指向被刪元素的下一個元素
       
       cout << "4) "; Print(m1.begin(),m1.end()); //輸出 4) 4 8 19 33 40
       cout << "5) "; cout << * pp << endl; //輸出 5) 33
       
       MSET2 m2; // m2裡的元素按n的個位數從小排到大
       m2.insert(a,a+SIZE);
       cout << "6) "; Print(m2.begin(),m2.end()); // 輸出 6) 40 22 33 4 8 19
       return 0;
   }
   ```
   執行結果：
   ```
   1) 2
   2) 4 8 19 22 22 33 40
   3) 22,33
   4) 4 8 19 33 40
   5) 33
   6) 40 22 33 4 8 19
   ```
   
   ### set
   ```c++
   template<class Key, class Pred = less<Key>, class A = allocator<Key>>
   class set {......}
   ```
   插入set中已有的元素時，忽略插入(當a < b和b < a都不成立時)。
   
   ```c
   #include <iostream>
   #include <set>
   
   using namespace std;
   
   int main()
   {
       typedef set<int>::iterator IT;
       int a[5] = { 3, 4, 6, 1, 2 };
       set<int> st(a, a + 5); // st裡是1 2 3 4 6
       pair<IT, bool> result;  // 用途是接收set成員函數insert的返回值
       
       result = st.insert(5); // st變成1 2 3 4 5 6
       if( result.second ) // 插入成功則輸出被插入元素
           cout << * result.first << " inserted" << endl; // 輸出： 5 inserted
       if( st.insert(5).second )
           cout << * result.first << endl;
       else
           cout << * result.first << " already exists" << endl; // 輸出 5 already exists
       
       pair<IT, IT> bounds = st.equal_range(4);
       cout << * bounds.first << "," << * bounds.second ; // 輸出：4,5
       return 0;
   }
   ```
   輸出結果：
   ```
   输出结果：
   5 inserted
   5 already exists
   4,5
   ```
   
2. map和multimap

   ### multimap
   ```c++
   template<class Key, class T, class Pred = less<Key>, class A = allocator<T>>
   class multimap
   {
       ......
       typedef pair<const Key, T> value_type;
       ......
   }; // Key代表關鍵字的類型
   ```
   * myltimap中的元素由<關鍵字, 值>組成，每個元素都是一個pair物件，關鍵字就是first成員變數，其類型是Key
   * multimap中允許多個元素的關鍵字相同。
     元素按照first成員變數從小大道排序，預設情況下用```less<Key>```定義關鍵字的「小於」關係。
     
   具體例子：
   ```c++
   #include <iostream>
   #include <map>
   using namespace std;
   int main()
   {
       typedef multimap<int, double, less<int>> mmid;
       mmid pairs;
       cout << "1) " << pairs.count(15) << endl;
       
       // pairs.insert({15, 2.7}); 這個寫法也是允許的，會自動實例化符合的模板成員函數
       // template <class ValTy>
       // pair<iterator, bool> insert(ValTy&& Val);  其中&&表示右值引用，是對臨時對象產生引用，詳見C++11新特性筆記
       pairs.insert(mmid::value_type(15, 2.7));    // typedef pair<const Key, T> value_type;
       pairs.insert(mmid::value_type(15, 99.3));
       cout << "2) " << pairs.count(15) << endl;    // 求關鍵字等於某值的元素個數
       
       pairs.insert(mmid::value_type(30, 111.11));
       pairs.insert(mmid::value_type(10, 22.22));
       pairs.insert(mmid::value_type(25, 33.333));
       pairs.insert(mmid::value_type(20, 9.3));
       
       for( mmid::const_iterator i = pairs.begin(); i != pairs.end() ; ++i )
           cout << "(" << i->first << "," << i->second << ")" << ",";
   } 
   ```
   輸出：
   ```
   1) 0
   2) 2
   (10,22.22),(15,2.7),(15,99.3),(20,9.3),(25,33.333),(30,111.11)
   ```
   
   我的答案：
   ```c
   #include<iostream>
   #include<map>
   #include<string>
   
   using namespace std;
   
   class student
   {
       int id;
           string name;

       public:
           student(int _id, string _name): id(_id), name(_name){}
           /*bool operator<(const student & cmp_s)
           {
               return id < cmp_s.id;
           }*/
           operator int() { return id; }
           friend ostream & operator<<(ostream & o, const student & s);
   };
   
   ostream & operator<<(ostream & o, const student & s)
   {
       o << s.name << ' ' << s.id;
       return o;
   }

   int main()
   {
       freopen("multimapTest.in", "r", stdin);
       string arg, name;
       int id, score;

       multimap<int, student, less<int>> studentMap;

       typedef multimap<int, student, less<int>>::iterator IT;
       
       pair<IT, IT> it_range;
       multimap<int, student, less<int>>::iterator it;

       while(cin >> arg)
       {
           switch(arg[0])
           {
               case 'A':
                   cin >> name;
                   cin >> id;
                   cin >> score;
                   studentMap.insert(make_pair(score, student(id, name)));
                   break;
               case 'Q':
                   cin >> score;
                   it = studentMap.lower_bound(score);

                   if (studentMap.begin() != it--)
                   {
                       student minScore = it -> second;
                       score = it -> first;
                       it_range = studentMap.equal_range(score);
                       for (it = it_range.first; it != it_range.second; ++it)
                       {
                           if (minScore < it -> second)
                           minScore = it -> second;
                       }
                       cout << minScore << ' ' << score;
                   }
                   else
                       cout << "Nobody";
                   cout << endl;
                   break;
               default:
                   break;
           }
       }

       return 0;
   }
   ```
   
   課程解答：
   ```c
   #include <iostream>
   #include <map> // 使用multimap需要包含此標頭檔
   #include <string>
   using namespace std;
   class CStudent
   {
       public:
           struct CInfo // 類別的內部還可以定義類別
           {
               int id;
               string name;
           };
       int score;
       CInfo info; // 學生的其他訊息
   };
   
   typedef multimap<int, CStudent::CInfo> MAP_STD;
   
   int main()
   {
       MAP_STD mp;
       CStudent st;
       string cmd;
       
       while( cin >> cmd )
       {
           if( cmd == "Add")
           {
               cin >> st.info.name >> st.info.id >> st.score ;
               mp.insert(MAP_STD::value_type(st.score,st.info ));
               // 在multimap的定義裡面有typedef pair<const Key, T> value_type;
           }
           else if( cmd == "Query" )
           {
               int score;
               cin >> score;
               MAP_STD::iterator p = mp.lower_bound (score);
               if( p!= mp.begin())
               {
                   --p;
                   score = p->first; // 比要查詢分數低的最高分
                   MAP_STD::iterator maxp = p;
                   int maxId = p->second.id;
                   for( ; p != mp.begin() && p->first == score; --p)
                   {
                       // 遍歷所有成績和score相等的學生
                       if( p->second.id > maxId )
                       {
                           maxp = p;
                           maxId = p->second.id ;
                       }
                   }
                   if( p->first == score)
                   {
                       // 如果上面循環是因為p == mp.begin()而終止，則p指向的元素還需要再處理
                       if( p->second.id > maxId )
                       {
                           maxp = p;
                           maxId = p->second.id ;
                       }
                   }
                   cout << maxp->second.name <<" " << maxp->second.id << " " << maxp->first << endl;
               }
               else
               // lower_bound的結果就是begin，說明沒人分數比查詢分數低
                   cout << "Nobody" << endl;
           }
       }
       return 0;
   } 
   ```
   
   ### map
   
   ```c++
   template<class Key, class T, class Pred = less<Key>, class A = allocator<T>>
   class map
   {
       ......
       typedef pair<const Key, T> value_type;
       ......
   };
   ```
   map中的元素都是pair模板類別物件，關鍵字(first成員變數) 各不相同。
   元素按照關鍵字從小到大排列，缺省情況下用```less<Key>```，即'<'定義「小於」。
   
   
   map的```[]```成員函數：

   ```map[key]```會返回對關鍵字等於key的元素的值(second成員變數)之引用。
   若沒有關鍵字為key的元素，則會往map裡插入一個關鍵字為key的元素，其值用無參數建構子初始化，並返回其值的引用。
   
   如：
   ```
   map<int, double> pairs;
   ```
   則
   ```
   pairs[50] = 5; 
   ```
   會修改pairs中關鍵字為50之元素，使其值變成5。
   若不存在關鍵字等於50之元素，則插入此元素，並使其值變為5。
   
   map示例：
   
   ```c
   #include <iostream>
   #include <map>
   
   using namespace std;
   
   template <class Key, class Value>
   ostream & operator <<( ostream & o, const pair<Key,Value> & p)
   {
       o << "(" << p.first << "," << p.second << ")";
       return o;
   }
   
   int main() {
       typedef map<int, double,less<int>> mmid;
       mmid pairs;

       cout << "1) " << pairs.count(15) << endl;

       pairs.insert(mmid::value_type(15, 2.7));
       pairs.insert(make_pair(15, 99.3)); // make_pair生成一個pair物件，但此處因為前面已經有相同key存在，插入不成功
       // 一樣可以用pair<map<int , double>::iterator, bool>來確認插入有沒有成功
       cout << "2) " << pairs.count(15) << endl;

       pairs.insert(mmid::value_type(20, 9.3));
       mmid::iterator i;
       cout << "3) ";
       for( i = pairs.begin(); i != pairs.end(); ++i )
           cout << * i << ",";
       cout << endl;

       cout << "4) ";
       int n = pairs[40];    // 如果沒有關鍵字為40的元素，則插入一個
       for( i = pairs.begin(); i != pairs.end(); ++i )
           cout << * i << ",";
       cout << endl;

       cout << "5) ";
       pairs[15] = 6.28; // 把關鍵字為15的元素值改為6.28
       for( i = pairs.begin(); i != pairs.end();i ++ )
           cout << * i << ",";
       return 0;
   }
   ```
   
2. 容器適配器

   可以用某種「順序容器」來實現
   讓已有的順序容器以(stack / queue)的方式工作
   (1) stack：```#include <stack>```
       堆疊 -- 後進先出
   (2) queue：```#include <queue>```
       佇列 -- 先進先出
   (3) priority_queue：```#include <queue>```
       優先權佇列 -- 最高優先權元素總是第一個出列
   
   都有3個成員函數：
   * push：添加一個元素
   * top：返回堆疊頂部或佇列頭部元素的引用
   * pop：刪除一個元素

   容器適配器上沒有疊代器:star::star:
   $\Rightarrow$ STL中各種排序、搜尋、變序等演算法都不適用於容器適配器
   
   ### stack
   
   * 只能是後進先出的資料結構
   * 只能插入、刪除、訪問堆疊頂部的元素
   * 可用vector、list、deque來實現
     * 預設情況下，用deque實現
     * 用vector和deque實現，比用list實現性能好
   
   ```
   template<class T, class Cont = deque<T>>
   class stack
   {
       ......
   };
   ```
   
   stack中主要的三個成員函數：
   | 成員函數                | 功能                                                                     |
   | ----------------------- | ------------------------------------------------------------------------ |
   | void push(const T & x); | 將x壓入堆疊頂部                                                          |
   | void pop();             | 彈出(即刪除)堆疊頂部元素                                                 |
   | T & top();              | 返回堆疊頂部元素的引用，通過該函數可以讀取堆疊頂部的值，也可以進行修改。 |

   ### queue
   * 和stack基本類似，可以用list和deque實現
   * 預設情況下用deque實現
   ```
   tmeplate<class T, class Cont = deque<T>>
   class queue
   {
       ......
   };
   ```
   * 同樣也有push、pop、 top函數
     - push發生在隊伍尾端
     - pop和top發生在隊頭，先進先出
   
   ### priority_queue
   * 和queue類似，可以用vector和deque實現
   * 預設情況下用vector實現
   * priority_queue通常用「heap sort」技術實現，保證最大的元素總是在最前面。
     - 執行pop操作時，刪除的是最大的元素
     - 執行top操作時，返回的是最大元素的引用
   * 默認的元素比較器是```less<T>```
   
   priority_queue實際例子：
   ```c
   #include <queue>
   #include <iostream>
   
   using namespace std;
   
   int main()
   {
       priority_queue<double> priorities;
       priorities.push(3.2);
       priorities.push(9.8);
       priorities.push(5.4);
       while( !priorities.empty() )
       {
           cout << priorities.top() << " ";
           priorities.pop();
       }
       return 0;
   } // output: 9.8 5.4 3.2
   ```
   
3. 演算法

   STL演算法分類，可大致分為以下七類：
   
   * 不變序列演算法
   * 變值演算法
   * 刪除演算法
   * 變序演算法
   * 排序演算法
   * 有序區間演算法
   * 數值演算法
   
   演算法大多都是有兩個版本的over-loading
   (1) 用'=='判斷元素是否相等，或用'<'來比較大小
   (2) 多出一個類型參數'Pred'和函數參數"Pred op"：
       通過表示式"op(x, y)"的返回值：true / false
       $\Rightarrow$ 判斷x是否「等於」y，或者x是否「小於」y
   
   如下，有兩個版本的min_element：
   ```
   iterator min_element(iterator first, iterator last);
   iterator min_element(iterator firstm iterator last, Pred op);
   ```
   
   1. 不變序列演算法
      * 該類演算法部會修改演算法所作用的容器或物件
      * 適用於順序容器和關聯容器
      * 時間複雜度都是O(n)

   | 演算法名稱              | 功能                                                                                      |
   | ----------------------- | ----------------------------------------------------------------------------------------- |
   | min                     | 求兩個物件中較小的(可自定義比較器)                                                        |
   | max                     | 求兩個物件中較大的(可自定義比較器)                                                        |
   | min_element             | 求區間中的最小值(可自定義比較器)                                                          |
   | max_element             | 求區間中的最大值(可自定義比較器)                                                          |
   | for_each                | 對區間中的美個元素都作某種操作                                                            |
   | count                   | 計算區間中等於某個值的元素個數                                                            |
   | count_if                | 計算區間中符合某種條件的元素個數                                                          |
   | find                    | 在區間中搜尋某個等於給定值元素                                                            |
   | find_if                 | 在區間中搜尋某個符合條件的元素                                                            |
   | find_end                | 在區間中搜尋另一個區間最後一次出現的位置(可自定義比較器)                                  |
   | find_first_of           | 在區間中搜尋第一個出現在另一個區間的元素(可自定義比較器)                                  |
   | adjacent_find           | 在區間中搜尋第一次出現連續兩個相等元素的位置(可自定義比較器)，詳見string筆記(第七週第4節) |
   | search                  | 在區間中搜尋另一個區間第一次出現的位置(可自定義比較器)                                    |
   | search_n                | 在區間中搜尋第一次出現等於某值的連續n個元素(可自定義比較器)                               |
   | equal                   | 判斷兩區間是否相等(可自定義比較器)                                                        |
   | mismatch                | 逐個比較兩個區間的元素，返回第一次發生不相等的元素之位置(可自定義比較器)                  |
   | lexicographical_compare | 按字典序比較兩個區間的大小(可自定義比較器)                                                |

   ### find
   ```
   template<class InIt, class T>
   InIt find(InIt first, InIt last, const T& val); 
   ```
   返回區間```[first, last)```中的疊代器i，使得```* i == val```
   
   ### find_if
   ```
   template<class InIt, class Pred>
   InIt find_if(InIt first, InIt last, Pred pr); 
   ```
   返回區間```[first, last)```中的疊代器i，使得```pr(* i) == true```
   
   ### for_each
   ```
   template<class InIt, class Fun>
   Fun for_each(InIt first, InIt last, Fun f); 
   ```
   對區間```[first, last)```中的每個元素e，執行f(e)，要求f(e)不能改變e
   
   ### count
   ```
   template<class InIt, class T>
   size_t count(InIt first, InIt last, const T& val);
   ```
   計算```[first, last)```中等於val的元素個數(x == y為true算等於)
   
   ### count_if
   ```
   template<class InIt, class Pred>
   size_t count_if(InIt first, InIt last, Pred pr); 
   ```
   計算```[first, last)```中符合```pr(e) == true```的元素e的個數
   
   ### min_element
   ```
   template<class FwdIt>
   FwdIt min_element(FwdIt first, FwdIt last);
   ```
   返回```[first, last)```中最小的元素的疊代器，以'<'作比較器
   「最小」指的是沒有元素比她小，而不是它比別的不同元素都小:star:
   因為即便是```a != b```，a < b且b < a有可能同時都不成立
   
   ### max_element
   ```
   template<class FwdIt>
   FwdIt max_element(FwdIt first, FwdIt last);
   ```
   返回```[first, last)```中最大元素(不小於任何其他元素)的疊代器
   以'<'作比較器:star:
   
   例子：
   ```c
   #include <iostream>
   #include <algorithm>
   
   using namespace std;
   class A
   {
       public:
           int n;
           A(int i):n(i) { }
   };
   
   bool operator<( const A & a1, const A & a2)
   {
       cout << “< called” << endl;
       if( a1.n == 3 && a2.n == 7 )
           return true;
       return false;
   }
   
   int main()
   {
       A aa[] = { 3, 5, 7, 2, 1 };
       cout << min_element(aa, aa + 5) -> n << endl;
       cout << max_element(aa, aa + 5) -> n << endl;
       return 0;
   }
   ```
   輸出：
   ```
   < called
   < called
   < called
   < called
   3
   < called
   < called
   < called
   < called
   7
   ```
   原因：
   (1) 演算法一開始假定3是最小，依序比較後面的元素，而operator<皆回傳false，因此3到最後依然是最小。
   (2) 演算法一開始假定3是最大，依序比較後面的元素，而operator<到了7時回傳true，替代原本的3，繼續比較到最後，7是最大值。
   
   2. 變值算法
   * 此類演算法會修改源區間或目標區間元素的值
   * 值被修改的那個區間，不可以是屬於關聯容器的(避免破壞容器原本的有序性)
   
   | 演算法名稱      | 功能                                                                                                                                               |
   | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
   | for_each        | 對區間中每個元素都作某種操作                                                                                                                       |
   | copy            | 複製一個區間到別處                                                                                                                                 |
   | copy_backward   | 與copy功能一樣，但拷貝的方式是由後往前拷貝的，原因是擔心兩區間若有重疊時，可能會有原先在後面的值還沒被拷貝前就已經被前面拷貝過來的值給覆蓋的情況發生。 |
   | transform       | 將一個區間的元素變形後拷貝到另一個區間                                                                                                             |
   | swap_ranges     | 交換兩個區間的內容                                                                                                                                 |
   | fill            | 用某個值填充區間                                                                                                                                   |
   | fill_n          | 用某個值替換區間中的n個元素                                                                                                                        |
   | generate        | 用某個操作的結果填充區間                                                                                                                           |
   | generate_n      | 用某個操作的結果替換區間中的n個元素                                                                                                                |
   | replace         | 將區間中的某個值替換為另一個值                                                                                                                     |
   | replace_if      | 將區間中符合某種條件的值替換為另一個值                                                                                                             |
   | replace_copy    | 將一個區間拷貝到另一個區間，拷貝時某個值要換成新值拷貝過去                                                                                         |
   | replace_copy_if | 將一個區間拷貝到另一個區間，拷貝時符合某條件的值要換成新值拷貝過去                                                                                 |

   ### transform
   ```
   template<class InIt, class OutIt, class Unop>
   OutIt transform(InIt first, InIt last, OutIt x, Unop uop); 
   ```
   對```[first, last)```中的每個疊代器i：
   * 執行```uop(* I);```並將結果依次放入從x開始的地方
   * 要求```uop(* I);```不得改變```* I```的值(源區間不得變化)
   本模板返回值是個疊代器，即```x + (last - first)```。
   
   例子：
   ```c
   #include <vector>
   #include <iostream>
   #include <numeric>
   #include <list>
   #include <algorithm>
   #include <iterator>
   
   using namespace std;
   
   class CLessThen9
   {
       public:
           bool operator()( int n) { return n < 9; }
   };
   
   void outputSquare(int value ) { cout << value * value << " "; }    // 求平方
   int calculateCube(int value) { return value * value * value; }    // 求立方
   
   int main()
   {
       const int SIZE = 10;
       int a1[] = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
       int a2[] = { 100, 2, 8, 1, 50, 3, 8, 9, 10, 2 };
       vector<int> v(a1, a1 + SIZE);
       ostream_iterator<int> output(cout, " ");    // STL的類別模板，以後有東西交給output時，會等價於輸出到cout上
       // 且輸出的東西必須是一個個的整數，並且加上一個空格" "
       random_shuffle(v.begin(),v.end());    // 變序算法，隨機打亂順序
       
       cout << endl << "1) ";
       copy(v.begin(), v.end(), output);    // 透過copy輸出了1) 5 4 1 3 7 8 9 10 6 2
       // 1) 的結果是隨機的
       
       copy( a2, a2 + SIZE, v.begin());    // 把a2的內容拷貝到v去，要求v要有足夠的空間，否則會出錯
       cout << endl << "2）";
       cout << count(v.begin(), v.end(), 8);    // 計算8出現的次數 輸出2) 2
       
       cout << endl << "3）";
       cout << count_if(v.begin(), v.end(), CLessThen9()); // 利用函數物件CLessThen9，重載operator()，用以v中計算<9的元素個數
       // 輸出 3) 6
       
       cout << endl << "4) ";
       cout << * (min_element(v.begin(), v.end()));    // 輸出 4) 1
       
       cout << endl << "5) ";
       cout << * (max_element(v.begin(), v.end()));    // 輸出 5) 100
       
       cout << endl << "6) ";
       cout << accumulate(v.begin(), v.end(), 0); // 求和，從begin一路累加到end到初始值0上，輸出 6) 193
       
       cout << endl << "7) ";
       for_each(v.begin(), v.end(), outputSquare);    // 每個元素都求平方，同時輸出 7) 10000 4 64 1 2500 9 64 81 100 4
       
       vector<int> cubes(SIZE);    // 重開一個vector叫作cubes
       transform(a1, a1 + SIZE, cubes.begin(), calculateCube);    // 把a1的值變形後放到cubes中
       cout << endl << "8) ";
       copy(cubes.begin(), cubes.end(), output);    // 輸出 8) 1 8 27 64 125 216 343 512 729 1000
       
       return 0;
   }
   ```
   
   其中：
   
   ```ostream_iterator<int> output(cout , " ");```
   定義了一個```ostream_iterator<int>```物件，可以通過cout輸出以" "(空格)分隔的一個個整數
   
   ```copy (v.begin(), v.end(), output);```
   導致v的內容在cout上被輸出
   
   ### copy函數模板
   ```
   template<class InIt, class OutIt>
   OutIt copy(InIt first, InIt last, OutIt x);
   ```
   本函數對每個在區間```[0, last - first)```中的N執行一次
   ```* (x + N) = * (first + N)```，返回x + N
   
   對於```copy(v.begin(), v.end(), output);```
   - first和last的類型是```vector<int>::const_iterator```
   - output的類型是```ostream_iterator<int>```
   
   copy的源代碼：
   ```c++
   template<class _II, class _OI>
   inline _OI copy(_II _F, _II _L, _OI _X)
   {
       for (; _F != _L; ++_X, ++_F)
           *_X = *_F;
       return (_X);
   }
   ```
   
   假設有一程式，要求：
   ```c++
   #include <iostream>
   #include <fstream>
   #include <string>
   #include <algorithm>
   #include <iterator>
   
   using namespace std;
   
   int main()
   {
       int a[4] = { 1, 2, 3, 4 };
       My_ostream_iterator<int> oit(cout, "*");
       copy(a, a + 4, oit); // 輸出1*2*3*4
       ofstream oFile("test.txt", ios::out);
       My_ostream_iterator<int> oitf(oFile, "*");
       copy(a, a + 4, oitf); // 向test.txt文件中寫入1*2*3*4*
       oFile.close();
       return 0;
   }
   ```
   那要如何編寫My_ostream_iterator這個類別模板?
   
   思考：
   呼叫```copy(a, a + 4, oit)```時，copy函數模板實例化如下：
   ```c++
   My_ostream_iterator<int> copy(int * _F, int * _L, My_ostream_iterator<int> _X)
   {
       for (; _F != _L; ++_X, ++_F)
           *_X = *_F;
       return (_X);
   }
   ```
   
   需要對My_ostream_iterator重載：
   1. operator++，前置版本(作為一元運算子重載)
   2. operator*
   3. operator=
   
   其中，觀察上列函數，```*_X```的返回值必須是個引用，才能放在等號左邊。
   ```*_X = *_F;```返回X的引用後，等價```X.operator=(*_F)```，
   然後在operator=中輸出。
   
   Ans:
   ```c
   #include <iterator>
   template<class T>
   class My_ostream_iterator:public iterator<output_iterator_tag, T>
   {
       private:
           ostream & os;    // ostream無參數建構子為私有的，無法調用自行製造，這邊只能引用它
           string sep; // 分隔符
       public:
           My_ostream_iterator(ostream & o, string s): os(o), sep(s){ }
           
           void operator ++() { }; // ++只需要有定義即可，不需要做什麼
           
           My_ostream_iterator & operator*()    // 返回自己本身的引用才能放在等號左邊
           {
               return * this;
           }
           
           My_ostream_iterator & operator=( const T & val)
           {
               os << val << sep;
               return * this;
           }
   };
   ```
   
   簡單來說，這個例子解釋了copy是如何運作的，而且可以藉由運算子重載完全改變copy原本的作用。
   
4. 演算法(續)
   
   3. 刪除演算法
   
   * 刪除一個容器裡的某些元素
   * 刪除 -- 不會使容器裡的元素減少
     - 將所有應該被刪除的元素看作空位子
     - 用留下的元素從後往前移，依次去填空位
     - 元素往前一後，它原來的位置也就算是空位子
     - 也應由後面留下的元素來填上
     - 最後，沒有被填上的空位，維持其原來的值不變
   * 刪除演算法不應該作用於關聯容器

   | 演算法名稱     | 功能                                                                         |
   | -------------- | ---------------------------------------------------------------------------- |
   | remove         | 刪除區間中等於某個值的元素                                                   |
   | remove_if      | 刪除區間中滿足某種條件的元素                                                 |
   | remove_copy    | 拷貝區間到另一個區間，等於某個值的元素不拷貝。                               |
   | remove_copy_if | 拷貝區間到另一個區間，符合某種條件的元素不拷貝。                             |
   | unique         | 刪除區間中連續相等的元素，只留下一個(可自定義比較器)。                       |
   | uniqur_copy    | 拷貝區間到另一個區間，連續相等的元素只拷貝第一個到目標區間(可自定義比較器)。 |
   以上演算法複雜度都是O(n)的，
   unique最常用的方法是先sort過後。
   
   unique
   ```c
   template<class FwdIt>
   FwdIt unique(FwdIt first, FwdIt last); 
   ```
   * 此版本用 == 比較是否相等
   
   ```c
   template<class FwdIt, class Pred>
   FwdIt unique(FwdIt first, FwdIt last, Pred pr)
   ```
   * 用pr(x, y)為true說明x和y相等
   * 對```[first, last)```這個序列中連續相等的元素，只留下第一個
   * 返回值是疊代器，指向元素刪除後的區間的最後一個元素的後面一個位置
   
   具體例子：
   ```c++
   int main()
   {
       int a[5] = { 1, 2, 3, 2, 5 };
       int b[6] = { 1, 2, 3, 2, 5, 6 };
       ostream_iterator<int> oit(cout, ",");
       
       // 將2刪除(位置標示為空)
       int * p = remove(a, a+5, 2);
       
       // 嘗試輸出原本的陣列大小進行觀察
       cout << "1) "; copy(a, a+5, oit); cout << endl; // 輸出 1) 1,3,5,2,5,

       cout << "2) " << p - a << endl; // 輸出 2) 3，代表有效的元素實際上剩下三個
       
       vector<int> v(b,b+6);
       remove(v.begin(), v.end(),2);
       cout << "3) "; copy(v.begin(), v.end(), oit); cout << endl;
       // 輸出 3) 1,3,5,6,5,6,
       cout << "4) "; cout << v.size() << endl;
       // 實際上v中的元素沒有減少，輸出 4) 6，只是end的位置變了
       return 0;
   }
   ```
   其中，
   ```
   int * p = remove(a, a+5, 2);
   ```
   這句陳述的過程如下，以雙引號代表空位所在：
   
   ```
   step 1： 1  "2"  3   "2"   5        (兩個2被標記為空位)
   step 2： 1   3  "3"  "2"   5        (3往前遞補，原本的3變成空位)
   step 3： 1   3   5   "2"  "5"       (5往前遞補，原本的5變成空位，其餘不動，完成)
   strp 4： 疊代器返回2的位置
   ```
   為什麼是用移動代替真正意義上的刪除？
   因為remove也可以用在array上，不可能真的刪掉。
   
   4. 變序算法
   
   * 變序算法改變容器中元素的順序
   * 但是不改變元素的值
   * 變序算法不適用於關聯容器
   * 算法複雜度都是O($n$)的
   
   | 演算法名稱                   | 功能                                                                 |
   | ---------------------------- | -------------------------------------------------------------------- |
   | reverse                      | 顛倒區間的前後次序                                                   |
   | reverse_copy                 | 把一個區間顛倒後的結果拷貝到另一個區間，源區間不變。                 |
   | rotate                       | 將區間進行循環左移                                                   |
   | rotate_copy                  | 將區間以首尾相接的形式進行旋轉後的結果拷貝到另一個區間，源區間不變。 |
   | :star::star:next_permutation | 將區間改為下一個排列(可自定義比較器)                                 |
   | :star::star:prev_permutation | 將區間改為上一個排列(可自定義比較器)                                 |
   | random_shuffle               | 隨機打亂區間內元素的順序                                             |
   | partition                    | 把區間內滿足某個條件的元素移到前面，不滿足條件的元素移到後面。       |

   ### stable_patition
   把區間內滿足某個條件的元素移動到前面，
   不滿足該條件的移動到後面，
   而對這兩部分元素，分別保持他們原來的先後次序不變。
   
   ### rando_shuffle
   ```c
   template<class RanIt>
   void random_shuffle(RanIt first, RanIt last); 
   ```
   隨機打亂```[first, last)```中的元素，適用於能隨機訪問的容器。
   
   ### reverse
   ```c
   template<class BidIt>
   void reverse(BidIt first, BidIt last);
   ```
   顛倒區間```[first, last)```的次序
   
   ### next_permutation
   ```c
   template<class InIt>
   bool next_permutaion (Init first,Init last);
   ```
   求下一個排列，按字典順序依序升序窮舉，如123->132->213->231
   
   看例子：
   ```c++
   #include <iostream>
   #include <algorithm>
   #include <string>
   
   using namespace std;
   
   int main()
   {
       string str = "231";
       char szStr[] = "324";
       
       while (next_permutation(str.begin(), str.end()))
       // string可以作permutation
       {
           cout << str << endl;
       }
       
       cout << "****" << endl;
       
       while (next_permutation(szStr,szStr + 3))
       // char可以作permutation
       {
           cout << szStr << endl;
       }
       
       sort(str.begin(), str.end());    // string也可以sort，變成"123"
       cout << "****" << endl;
       
       while (next_permutation(str.begin(), str.end()))
       {
           cout << str << endl;
       }
       return 0;
   }
   ```
   以上執行結果輸出：
   ```
   312
   321
   ****
   342
   423
   432
   ****
   132
   213
   231
   312
   321
   ```
   
   如果是作用在整數陣列或者容器上面的例子：
   ```c++
   #include <iostream>
   #include <algorithm>
   #include <string>
   #include <list>
   #include <iterator>
   
   using namespace std;
   
   int main()
   {
       int a[] = { 8,7,10 };
       list<int> ls(a, a+3);
       while( next_permutation(ls.begin(), ls.end()))
       {
           list<int>::iterator i;
           for( i = ls.begin(); i != ls.end(); ++i)
           cout << * i << " ";
           cout << endl;
       }
       return 0;
   }
   ```
   輸出：
   ```
   8 10 7
   10 7 8
   10 8 7
   ```
   
   5. 排序演算法
   
   * 比前面的變序演算法複雜度更高，一般是O($nlog(n)$)
   * 排序演算法需要隨機訪問疊代器的支持
   * 不適用於關聯容器和list
   
   | Column 1          | Column 2                                                                                                      |
   | ----------------- | ------------------------------------------------------------------------------------------------------------- |
   | sort              | 將區間從小到大排序(可自定義比較器)                                                                            |
   | stable_sort       | 將區間大小從小到大排序，並保持相等元素間的相對次序(可自定義比較器)。                                          |
   | partial_sort      | 對區間部分排序，直到最小的n個元素就位(可自定義比較器)。                                                       |
   | partial_sort_copy | 將區間前n個元素的排序結果拷貝到別處，源區間不變(可自定義比較器)。                                             |
   | nth_element       | 對區間部分排序，使得第n小的元素(n從0開始算)就位，而且比它小的都在他前面，比它大的都在他後面(可自定義比較器)。 |
   | make_heap         | 使區間成為一個heap(堆疊)(可自定義比較器)                                                                      |
   | push_heap         | 將元素加入一個是heap的區間(可自定義比較器)                                                                    |
   | pop_heap          | 從heap區間刪除top元素(可自定義比較器)                                                                         |
   | sort_heap         | 將一個heap區間進行排序，排序結束後，該區間就是普通的有序區間，不再是heap了(可自定義比較器)。                  |

   ### sort
   ```c
   template<class RanIt>
   void sort(RanIt first, RanIt last); 
   ```
   按升序排列
   判斷x是否應比y靠前，就看x < y是否為true
   ```c
   template<class RanIt, class Pred>
   void sort(RanIt first, RanIt last, Pred pr);
   ```
   按升序排列
   判斷x是否應比y靠前，就看pr(x, y)是否為true
   pr為函數物件或函數指針
   
   ```c++
   #include <iostream>
   #include <algorithm>
   using namespace std;
   class MyLess
   {
       public:
           bool operator()( int n1,int n2)
           // n1個位數比n2個位數小則回傳true
           {
               return (n1 % 10) < ( n2 % 10);
           }
   };
   
   int main()
   {
       int a[] = { 14,2,9,111,78 };
       sort(a, a + 5, MyLess());
       
       int i;
       for( i = 0;i < 5;i ++)
           cout << a[i] << " ";
       cout << endl;
       
       sort(a, a+5, greater<int>());    // 當n1 > n2時，代表n1小，回傳true
       for( i = 0;i < 5;i ++)
           cout << a[i] << " ";
   }
   ```
   上述程式碼執行結果：
   ```
   111 2 14 78 9
   111 78 14 9 2
   ```
   分別為按個位數大小牌序、按降序排序
   
   sort實際上是Quick sort(快速排序法)，時間複雜度O($nlog(n)$)
   * 平均效能最佳
   * 但是最壞情況下，會達到O($n^2$)
   如果要保證「最壞情況下(worst case)」的性能，那麼可以使用
   * stable_sort
   * stable_sort實際上是Merge sort，特點是能保持相等元素之間的先後次序
   * Merge sort在有足夠儲存空間的情況下，複雜度為$nlog(n)$，否則為$n*log(n)*log(n)$
   
   排序演算法要求隨機存取疊代器的支持，所以list不能使用排序演算法，要使用list::sort
   
   6. 有序區間演算法
   
   * 要求所操作的區間是已經從小到大排好序的:star:
   * 需要隨機訪問疊代器的支持
   * 有序區間演算法不能用於關聯容器和list
   
   | 演算法名稱               | 功能                                           |
   | ------------------------ | ---------------------------------------------- |
   | binary_search            | 判斷區間中是否包含某個元素                     |
   | includes                 | 判斷是否一個區間中的每個元素，都在另一個區間中 |
   | lower_bound              | 搜尋最後一個不小於某值的元素的位置             |
   | upper_bound              | 搜尋第一個大於某值的元素的位置                 |
   | equal_range              | 同時獲取lower_bound和upper_bound               |
   | merge                    | 合併兩個有序區間到第三個區間                   |
   | set_union                | 將兩個有序區間的「聯集」拷貝到第三個區間       |
   | set_intersection         | 將兩個有序區間的「交集」拷貝到第三個區間       |
   | set_difference           | 將兩個有序區間的「差集」拷貝到第三個區間       |
   | set_symmetric_difference | 將兩個有序區間的「對稱差」拷貝到第三個區間     |
   | inplace_merge            | 將兩個連續的有序區間原地合併為一個有序區間     |

   ### binary_search
   二元搜尋法
   複雜度$O(log(n))$
   要求容器已經排好序且支持隨機訪問疊代器，返回是否找到(bool)
   這裡的相等指的是互相不小於對方即相等
   ```c
   template<class FwdIt, class T>
   bool binary_search(FwdIt first, FwdIt last, const T& val); 
   ```
   上面這個版本，比較兩個元素x和y時，使用x < y
   ```c
   template<class FwdIt, class T, class Pred>
   bool binary_search(FwdIt first, FwdIt last, const T& val, Pred pr);
   ```
   這個版本比較時，看pr(x, y)是否為true，為真代表x小於y
   
   例子：
   ```c++
   #include <vector>
   #include <bitset>
   #include <iostream>
   #include <numeric>
   #include <list>
   #include <algorithm>
   
   using namespace std;
   
   bool Greater10(int n)
   {
       return n > 10;
   }
   
   int main() {
       const int SIZE = 10;
       int a1[] = { 2, 8, 1, 50, 3, 100, 8, 9, 10, 2 };
       vector<int> v(a1, a1 + SIZE);
       ostream_iterator<int> output(cout, " ");
       vector<int>::iterator location;
       location = find(v.begin(), v.end(), 10);
       // location等於end代表沒找到10
       if( location != v.end())
       {
           cout << endl << "1) " << location - v.begin();    // 輸出所在下標
       }

       location = find_if( v.begin(),v.end(),Greater10);
       if( location != v.end())
           cout << endl << "2) " << location - v.begin();

       sort(v.begin(),v.end());    // 二元搜尋法之前要先排序
       if( binary_search(v.begin(), v.end(), 9))
       {
           cout << endl << "3) " << "9 found";
       }
       return 0;
   }
   ```
   執行結果：
   ```
   1) 8
   2) 3
   3) 9 found
   ```
   
   ### lower_bound
   複雜度$O(log(n))$
   ```c
   template<class FwdIt, class T>
   FwdIt lower_bound(FwdIt first, FwdIt last, const T& val); 
   ```
   要求```[first, last)```是有序的
   查找```[first, last)```中最大的位置FwdIt使得```[first, FwdIt)```中所有元素都比val小
   
   ### upper_bound
   複雜度$O(log(n))$
   ```c
   template<class FwdIt, class T>
   FwdIt upper_bound(FwdIt first, FwdIt last, const T& val); 
   ```
   要求```[first, last)```是有序的
   查找```[first, last)```中的最小位置FwdIt使得```[FwdIt, last)```中所有元素都比val大
   即val < 所有的元素
   
   ### equal_range
   複雜度$O(log(n))$
   ```c
   template<class FwdIt, class T>
   pair<FwdIt, FwdIt> equal_range(FwdIt first, FwdIt last, const T& val); 
   ```
   要求```[first, last)```是有序的
   返回值是一個pair，假設為p，則：
   - ```[first, p.first)``` 中的所有元素都比val小
   - ```[p.second, last)```中的所有元素都比val大
   - p.first就是lower_bound的結果
   - p.last就是 upper_bound的結果
   
   ### merge
   複雜度$O(n)$，兩個區間都得掃過一遍
   ```c
   template<class InIt1, class InIt2, class OutIt>
   OutIt merge(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x);
   ```
   用<作為比較器
   ```c
   template<class InIt1, class InIt2, class OutIt, class Pred>
   OutIt merge(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x, Pred pr);
   ```
   用pr作比較器
   把```[first1, last1), [first2, last2)```兩個升序序列合併，形成第3個升序序列，第3個升序序列以x開頭
   
   ### inludes
   複雜度$O(k*log(n))$
   k為要找的區間的元素個數
   ```c
   template<class InIt1, class InIt2>
   bool includes(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2);
   
   template<class InIt1, class InIt2, class Pred>
   bool includes(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, Pred pr);
   ```
   判斷```[first2, last2)```中的每個元素，是否「都在」```[first1, last1)```中
   - 第一個用<作為比較器
   - 第二個用pr作為比較器，pr(x, y) == true代表x和y相等
   
   這邊的存在指的是：
   $\exists y \in [first1, last1)$, $y\nless x$ *and* $x\nless y$, $\forall x \in [first2, last2)$
   
   ### set_difference
   ```c
   template<class InIt1, class InIt2, class OutIt>
   OutIt set_difference(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x);
   
   template<class InIt1, class InIt2, class OutIt, class Pred>
   OutIt set_difference(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x, Pred pr);
   ```
   求出```[first1, last1)```中，不在```[first2, last2)```中的元素，放到從x開始的地方。
   如果```[first1, last1)```裡有多個相等元素不在```[first2, last2)```中，則這多個元素也都會被放入x代表的目標區間裡
   註：「在」和「不在」的概念同inludes
   
   ### set_intersection
   ```c
   template<class InIt1, class InIt2, class OutIt>
   OutIt set_intersection(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x);
   
   template<class InIt1, class InIt2, class OutIt, class Pred>
   OutIt set_intersection(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x, Pred pr);
   ```
   求出```[first1, last1)```和```[first2, last2)```中共有的元素，放到從x開始的地方，
   若某個元素e在```[first1, last1)```裡出現n1次，在```[first2, last2)```裡出現n2次，
   則該元素在目標區間裡出現min(n1, n2)次。
   
   ### set_symmetric_difference
   ```c
   template<class InIt1, class InIt2, class OutIt>
   OutIt set_symmetric_difference(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x);
   
   template<class InIt1, class InIt2, class OutIt, class Pred>
   OutIt set_symmetric_difference(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x, Pred pr);
   ```
   把兩個區間裡相互不在另一個區間裡的元素放入x開始的地方
   
   ### set_union
   ```c
   template<class InIt1, class InIt2, class OutIt>
   OutIt set_union(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x); 
   ```
   用<比較大小
   ```c
   template<class InIt1, class InIt2, class OutIt, class Pred>
   OutIt set_union(InIt1 first1, InIt1 last1, InIt2 first2, InIt2 last2, OutIt x, Pred pr); 
   ```
   用pr比較大小
   求兩個區間的聯集，放到以x開始的位置，
   若某個元素e在```[first1, last1)```裡出現n1次，在```[first1, last1)```出現n2次，
   則該元素在目標區間裡出現max(n1, n2)次。
   
   ### bitset
   一個好用的類別模板
   需要```#include<bitset>```標頭檔
   
   ```c++
   template<size_t N>
   class bitset
   {
       ......
   };
   ```
   實際使用的時候，N是個整數常數
   如：
   - bitset<40> bst;
   - bst是一個由40位元組成的物件
   - 用bitset的函數可以方便地訪問任何一個位元

   :star:可以用字串初始化，如：```bitset<10> bst("1001001110")```
   :star:或是用無號長整數初始化：```bitset<10> b(512UL);```或```bitset<10> b(0xFFUL);```
   
   bitset的成員函數：
   ```c++
   bitset<N>& operator&=(const bitset<N>& rhs); // 指派
   bitset<N>& operator|=(const bitset<N>& rhs); // or
   bitset<N>& operator^=(const bitset<N>& rhs); // xor
   bitset<N>& operator<<=(size_t num);
   bitset<N>& operator>>=(size_t num);
   bitset<N>& set(); // 全部設為1
   bitset<N>& set(size_t pos, bool val = true); // 設置某位元，pos是要設置第幾位，val是要設置0或1
   bitset<N>& reset(); // 全部設成0
   bitset<N>& reset(size_t pos); // 某位元設成0
   bitset<N>& flip(); // 全部翻轉
   bitset<N>& flip(size_t pos); // 翻轉某位元
   reference operator[](size_t pos); // 返回對某位元的引用
   bool operator[](size_t pos) const; // 判斷某位元是否為1
   reference at(size_t pos);    // 同[]，但判斷是否越界
   bool at(size_t pos) const;
   unsigned long to_ulong() const; // 轉換成無號長整數
   string to_string() const; // 轉換成字串
   size_t count() const; // 計算1的個數
   size_t size() const; // 有幾個位元
   bool operator==(const bitset<N>& rhs) const;
   bool operator!=(const bitset<N>& rhs) const;
   bool test(size_t pos) const; // 測試某位元是否為1
   bool any() const; // 是否存在至少一個位元為1
   bool none() const; // 是否全部為0
   bitset<N> operator<<(size_t pos) const;
   bitset<N> operator>>(size_t pos) const;
   bitset<N> operator~();
   static const size_t bitset_size = N;
   ```
   注意：第0位在最「右」邊
   