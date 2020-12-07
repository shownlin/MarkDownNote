# 北京大學 - C++程序設計 (Coursera) 文件、模板、串流與String

第1～6週[筆記在這](/SXujAxaTR9KkZGFqBUt68g)
STL相關[筆記在這](/e00_PVmZQYK9N1CkbIZxtw)
C++11新特性[筆記在這](/wNsVLFPfTOie9ZYSktnirA)

## 第七週

1. 文件操作
   
   資料的層次：
   
   位元 bit
   位元組(字節) byte
   域/紀錄：
   例如：
	|     | 學生紀錄   |
	| --- | ------    |
	| int  | ID;      |
	| char | name[10];|
	| int  | age;     |
	| int  | rank;    |

   順序地將所有紀錄寫入一個文件 $\Rightarrow$ 順序文件
   
   文件和流：
   順序文件 -- 一個有限char構成的順序char stream
   C++標準庫中： ifstream(讀取)、ofstream(寫入)和fstream(讀取+寫入)共3個類
   $\Rightarrow$ 用於文件操作 -- 統稱為文件串流類別
   
   關係： istream $\rightarrow$(衍生) ifstream
   		 ostream $\rightarrow$(衍生) ofstream
		 istream + ostream $\rightarrow$(多重繼承) iosfstream $\rightarrow$ fstream
   
   使用/創建文件的基本流程：
   
   1. 打開文件：
      * 通過指定文件名，創立文件和文件串流物件的關聯
	  * 指名文件的使用方式

   2. 讀寫文件
      利用讀/寫指針進行相應位置的操作

   3. 關閉文件

   建立順序文件範例：
   ```c
   #include <fstream>	// 包含頭文件
   ofstream outFile("clinets.dat", ios::out|ios::binary);	// 打開文件
   ```
   其中ofstream建構子的第一個參數clinets.dat是文件名，
   第二個則是打開文件並建立文件的選項，有
   * ios::out 輸出到文件，刪除原有內容
   * ios:app 輸出到文件，保留原有內容，總是在尾部添加
   * ios::binary 以二進制文件格式打開文件

   也可以先創建ofstream物件，再用open函數打開：
   ```c
   ofstream fout;
   fout.open("test.out", ios::out::|ios::binary);
   ```
   判斷打開是否成功：
   ```c
   if(!fout)
   {
   	cerr << "File open error!" << endl;
   }
   ```
   文件名可以給出絕對路徑，也可以給相對路徑
   沒有交代路徑信息，就是在當前文件夾下找文件
   
   文件的讀寫指針：
   * 對於輸入的文件，有一個讀指針
   * 對於輸出的文件，有一個寫指針
   * 對於輸入輸出的文件，有一個讀寫指針
   * 標示文件操作的當前位置
     該指針在哪裡 $\Rightarrow$ 讀寫指針就在哪裡進行
   ```c
   ofstream fout("a1.out", ios::app);
   long location = fout.tellp();	// 取得寫指針的位置
   location = 10L;
   fout.seekp(location);			// 將寫指針移動到第10個byte處
   fout.seekp(location, ios::beg);	// 從頭數location
   fout.seekp(location, ios::cur);	// 從當前位置數location
   fout.seekp(location, ios::end);	// 從尾部數location
   ```
   注意：location可以是負值，並且要是Long類型(長整數)
   
   讀指針與上述寫指針的操作差不多，差別在於tellp換成tellg，seekp換成seekg，
   並且利用ios::in選項打開：
      ```c
   ifstream fin("a1.in", ios::in);
   long location = fin.tellg();	// 取得讀指針的位置
   location = 10L;
   fin.seekg(location);			// 將讀指針移動到第10個byte處
   fin.seekg(location, ios::beg);	// 從頭數location
   fin.seekg(location, ios::cur);	// 從當前位置數location
   fin.seekg(location, ios::end);	// 從尾部數location
   ```
   
   二進制文件讀寫
   ```c
   int x = 10;
   fout.seekp(20, ios::beg);
   fout.write((const char *)(&x), sizeof(int));	// 此處是把int轉成字串寫入
   
   fin.seekg(0, ios::beg);
   fin.read((char *)(&x), sizeof(int));
   ```
   
   二進制文件讀寫，直接寫二進位資料，用記事本看未必正確。
   
   具體例子：
   ```c
   // 下面的程序從鍵盤輸入幾個學生的姓名的成績
   // 並以二進制文件形式存起來
   
   # include <iostream>
   # include <fstream>
   # include <cstring>
   
   using namespace std;
   
   class CStudent
   {
   	public:
			char szName[20];
			int nScore;
   };
   
   int main()
   {
   	CStudent s;
		ofstream OutFile("students.dat", ios::out|ios::binary);
		while(cin >> s.szName >> s.nScore)
		{
			if(stricmp(s.szName, "exit") == 0)	// 若輸入名字為exit則結束
				break;
			OutFile.write((char *) & s, sizeof(s));
		}
		OutFile.close();
		return 0;
   } 
   ```
   
   若輸入：
   ```
   Tom 60
   Jack 80
   Jane 40
   exit 0
   ```
   
   用記事本打開(此處是繁體中文windows為例)，會呈現:
   ```
   Tom     7e苨    Ha <   Jack    7e苨    Ha P   Jane    7e苨    Ha (   
   ```
   共72個Byte
   解釋：
   在文本文件/二進制文件下打開文件的區別：
   * 在Unix/Linux下，二者一致，沒有區別
   * 在Windows下，文本文件是以"\r\n"作為換行符
     $\Rightarrow$ 讀出時，系統會將0x0d0a只讀入0x0a
	 $\Rightarrow$ 寫入時，對於0x0a系統會自動寫入0x0d
	 
	 
   ```c
   //下面的程序將students.dat文件的內容讀出並顯示
   #include <iostream>
   #include <fstream>
   
   using namespace std;
   
   class CStudent
   {
   	public:
			char szName[20];
			int nScore;
   };
   
   int main()
   {
   	CStudent s;
		ifstream inFile("students.dat", ios::in|ios::binary);
		if(!inFile)
		{
			cerr << "error" << endl;
			return 0;
		}
		while(inFile.read((char *) & s, sizeof(s)))	// 一次讀s的大小
		{
			int nReadedBytes = inFile.gcount();	//看剛才讀了多少個位元組
			cout << s.szName << " " << s.nScore << endl;
		}
		inFile.close();
		return 0;
   }
   ```
   
   同時進行讀寫操作的範例(教學裡的)：
   ```c
	//下面的程序將students.dat文件的Jane的名字改成Mike
	#include <iostream>
	#include <fstream>

	using namespace std;

	class CStudent
	{
		public:
			char szName[20];
			int nScore;
	};

	int main()
	{
		CStudent s;
		fstream ioFile("students.dat", ios::in|ios::out|ios::binary);
		if(!ioFile)
		{
			cerr << "error" << endl;
			return 0;
		}
		ioFile.seekp(2 * sizeof(s), ios::beg);	// 定位指針到第三個紀錄
		ioFile.write("Mike", strlen("Mike") + 1);
		ioFile.seekg(0, ios::beg);	// 定位讀指針到開頭

		while(ioFile.read((char *) & s, sizeof(s)))
			cout << s.szName << " " << s.nScore << endl;

		ioFile.close();
		return 0;
	}   
   ```
   
   同時進行讀寫操作的範例(我自己改的)：
   ```c
	//下面的程序將students.dat文件的Jane的名字改成Mike
	#include <iostream>
	#include <fstream>
	#include <cstring>

	using namespace std;

	class CStudent
	{
		public:
			char szName[20];
			int nScore;
	};

	int main()
	{
		CStudent s;
		fstream ioFile("students.dat", ios::in|ios::out|ios::binary);
		if(!ioFile)
		{
			cerr << "error" << endl;
			return 0;
		}
		while(ioFile.read((char *) & s, sizeof(s)))	// 一次讀s的大小
		{
			if(strcmp(s.szName, "Jane") == 0)
			{
				strcpy(s.szName, "Mike");
				ioFile.seekp((long)ioFile.tellg()-sizeof(s), ios::beg);
				ioFile.write((char *) & s, sizeof(s));
				ioFile.seekg(0, ios::beg);
				break;
			}	
		}

		while(ioFile.read((char *) & s, sizeof(s)))	// 一次讀s的大小
		{
			cout << s.szName << " " << s.nScore << endl;
		}

		ioFile.close();
		return 0;
	}
   ```
   
   顯式關閉文件：
   ifstream fin("test.dat", ios::in);
   fin.close();
   
   ofstream fout("test.dat", ios::out);
   fout.close();
   
   例子：mycopy程序，文件拷貝
   ```c
   // 用法示例
   // mycopy src.dat dest.dat
   // 將src.dat拷貝到dest.dat
   // 如果dest.dat原來就有內容，則原來的文件會被覆蓋
   
   #include <iostream>
   #include <fstream>
   
   using namespace std;
   int main(int argc, char * argv[])
   {
   	if(argc != 3)
		{
			cerr << "File name missing!" << endl;
			return 0;
		}
		ifstream inFile(argv[1], ios::binary|ios::in);	// 打開文件用於讀
		if(!inFile)
		{
			cerr << "Source file open error." << endl;
			return 0;
		}
		
		ostream outFile(argc[2], ios::binary|ios::out)	// 打開文件用於寫
		
		if(!outFile)
		{
			cout << "New file open error." << endl;
			inFile.close();	// 打開的文件一定要關閉
			return 0;
		}
		char c;
		while(inFile.get(c))	// 每次讀取一個char
			outFile.put(c); 	// 每次寫入一個char
		outFile.close();
		inFile.close();
		return 0;
   }
   ```
   
2. 函數模板

   泛型程式設計
   Generic Programming
   演算法實現時不指定具體要操作的資料類型
   
   泛型：演算法實現一遍 $\Rightarrow$ 適用於多種資料結構
   優勢：減少重複代碼的編寫
   大量編寫模板，使用模板的程式設計
   * 函數模板
   * 類型模板
   
   ### 函數模板
   
   為了交換兩個int變數的值，需要編寫如下Swap函數：
   ```c
   void Swap(int & x, int & y)
   {
   	int tmp = x;
		x = y;
		y = tmp;
   }
   ```
   但為了交換兩個double變數的值，還需要編寫如下Swap函數：
   ```c
   void Swap(double & x, double & y)
   {
   	double tmp = x;
		x = y;
		y = tmp;
   }
   ```
   
   能否只寫一個Swap就能滿足各種類型的變數交換？
   
   用函數模板解決
   ```
   template<class 類型參數1, class 類型參數2, ...>
   返回值類型 模板名(參數表)
   {
   	函數體
   }
   ```
   
   具體例子：
   ```c
   template <class　Ｔ>
   void Swap(T & x, T & y)
   {
   	T tmp = x;
		x = y;
		y = tmp;
   }
   ```
   
   T在使用時可以是int，也可以是double，在此處我們不需要顯式的定義出來。
   ```c
   int main()
   {
   	int n = 1, m = 2;
		SWap(n, m);	// compiler自動生成void Swap(int &, int &)函數
		double f = 1.2, g = 2.3;
		Swap(f, g);	// compiler自動生成void Swap(double &, double &)函數
		retrun 0;
   }
   ```
   
   函數模板中可以有不只一個類型參數
   ```c
   template<class T1, class T2>
   T2 print(T1 arg1, T2 arg2)
   {
   	cout << arg1 << " " arg2 << endl;
		return arg2;
   }
   ```
   
   例子：求陣列最大元素的MaxElement函數模板
   ```c
   template <class T>
   T MaxElement(T a[], int size)	// size是陣列元素個數
   {
   	T tmpMax = a[0];
		for(int i = 1; i < size; ++i)
			if(tmpMax < a[i])
				tmpMax = a[i];
		return tmpMax;
   }
   ```
   
   函數模板本身也是可以重載(over loading)的，只要參數表不同即可
   例：
   ```c
   template <class T1, class T2>
   void print(T1 arg1, T2 arg2)
   {
   	cout << arg1 << " " << arg2 << endl;
   }
   
   template <class T>
   void print(T arg1, T arg2)
   {
   	cout << arg1 << " " << arg2 << endl;
   }
   ```
   
   C++編譯器遵循以下優先順序：
   Step 1: 先找參數完全匹配的普通函數(非由模板實例化而得的參數)
   Step 2: 再找參數完全匹配的模板函數
   Step 3: 再找引數經過自動類型轉換後能夠匹配的普通函數
   Step 4: 上面的都找不到，則報錯
   
   
   函數模板呼叫順序的實際例子：
   ```c
   template <class T>
   T Max(T a, T b)
   {
   	cout << "Template Max 1" << endl;
		return 0;
   }
   
   template<class T, class T2>
   T Max(T a, T2 b)
   {
   	cout << "Template Max 2" << endl;
		return 0;
   }
   
   double Max(double a, double b)
   {
   	cout << "MyMax 2" << endl;
		return 0;
   }
   
   int main()
   {
   	int i = 4, j = 5;
		Max(1.2, 3.4);	// 呼叫Max(double, double)函數
		Max(i, j);		// 呼叫第一個T Max(T a, T b)模板生成的函數
		Max(1.2, 3);	// 呼叫第二個T Max(T a, T2 b)模板生成的函數
		return 0;
   }
   ```
   
   以上程式輸出：
   ```
   MyMax 2
   Template Max 1
   Template Max 2
   ```
   
   賦值兼容原則引起函數模板中類型參數的二義性
   ```c
   template <class T>
   T myFunction(T arg1, T arg2)
   {
   	cout << arg1 << " " << arg2 << '\n'
		return arg1;
   }
   
   ...
   
   myFunction(5, 7);	// ok: replace T with int
   myFunction(5.8, 8.4);	// ok: replace T with double
   myFunction(5, 8.4);	// error: replace T with int or double? 二義性
   ```
   
   因此在函數模板中使用多個類型參數，可以避免二義性：
   ```c
   template <class T1, class T2>
   T1 myFunction(T1 arg1, T2 arg2)
   {
   	cout << arg1 << " " << arg2 << '\n'
		return arg1;
   }
   
   ...
   
   myFunction(5, 7);	// ok: replace T with int
   myFunction(5.8, 8.4);	// ok: replace T with double
   myFunction(5, 8.4);	// ok: replace T1 with int and T2 with double
   ```
   
3. 類型模板
   
   定義一批相似的類別 $\Rightarrow$ 定義類別模板 $\Rightarrow$ 生成不同的類別
   例如：
   | 陣列 | 陣列類別 |
   | :--------: | :--------: |
   | 元素可以是： | 提供的基本操作：|
   |整數、學生、字串 | len()、getElement(int index)、setElement(int index) |
   
   
   問題的提出：
   對於這些陣列類別
   * 除了元素的類型不同之外，其他的完全相同
   
   類別模板
   * 在定義類別的時候給它一個/多個參數
   * 這個/些參數表示不同的資料類型

   在呼叫類別模板時，指定參數，由編譯系統根據參數提供的資料類型自動產生相應的類別模板。
   
   類模板的寫法如下：
   ```
   template <類型參數表>
   class 類別模板名稱
   {
   	成員函數和成員變數
   };
   ```
   
   類型參數表的寫法就是：
   ```
   class 類型參數1、class 類型參數2
   ```
   
   類別模板裡的成員函數，如在類別模板外面定義時，寫法如下：
   ```c
   template <參數表>
   返回值類型 類別模板名稱<類型參數名列表>::成員函數名(參數表)
   {
   	......
   }
   ```
   
   用類別模板定義對弈物件的寫法如下:
   ```
   類別模板名稱<真實類型參數表> 物件名(建構函數實際參數表);
   ```
   
   如果類別模板有無參建構函數，那麼也可以只寫：
   ```
   類別模板名稱<真實類型參數表> 物件名;
   ```
   
   具體例子：
   Pair類別模板
   ```c
   template <class T1, class T2>
   class Pair
   {
   	public:
			T1 key; // 關鍵字
			T2 value; // 值
			Pair(T1 k, T2 v):key(k), value(v){};
			bool operator<(const Pair<T1, T2> & p) const;
   };
   
   template<class T1, calss T2>
   bool Pair<T1, T2>::operator<(const Pair<T1, T2 & p>) const
   // Pair的成員函數operator<
   {
   	return key < p.key;
   }
   ```
   
   而Pair類別模板的使用：
   ```c
   int main()
   {
   	pair<string, int> student("Tom", 19);
		// 實例化出一個類別Pari<string, int>
		cout << student.key << " " << student.value;
		return 0;
   }
   ```
   以上輸出結果：
   ```
   Tom 19
   ```
   
   使用類別模板宣告物件：
   
   編譯器由類別模板生成類別的過程叫做類別模板的實例化
   * 編譯器自動用具體的資料類型 $\Rightarrow$ 替換模板中的類型參數，生成模板類別的代碼
   
   由「類別模板」實例化得到的類別叫「模板類別」:star::star::star:
   * 為類型參數指定的資料類型不同，得到的模板類型不同

   注意：同一個類別模板的兩個模板類別是不兼容的
   ```
   Pair<string , int> * p;
   Pair<string, double> a;
   p = & a; // wrong
   ```
   
   另外，函數模板也作為類別模板的成員：
   ```c
   #include <iostream>
   using namespace std;
   template <class T>
   class A
   {
   	public:
			template <class T2>
			void Func(T2 t){ cout << t;}	// 成員函數模板
   };
   
   int main()
   {
   	A<int> a;
		a.Func('K');	// 成員函數模板 Func被實例化成voud Func(char t)
		return 0;
   }
   ```
   
   注意：若函數模板改為
   ```
   template <class T>
   voud Func(T t){cout << t;}
   ```
   將報錯"declaration of 'class T' shadows template parm 'class T'"
   兩者的模板參數名不能相同
   
   類別模板與非類型參數：
   類別模板的參數宣告中可以包括「非類型參數」
   ```
   template <class T, int elementsNumber>
   ```
   其中elementsNumber為非類型參數，它有指定的類型int
   * 非類型參數：用來說明類別模板中的屬性
   * 類型參數：用來說明類別模板中的屬性類型，成員操作的參數類型和返回值類型

   例如：
   ```c
   template <class T, int size>
   class CArray
   {
   	T array[size];
		public:
			void Print()
			{
				for(int i = 0; i < size; ++ i)
					cout << array[i] << endl;
			}
   };
   ```
   
   ```
   CArray<double, 40> a2;
   CArray<int, 50> a3;
   ```
   注意：
   CArray<int, 40>和CArray<int , 50>完全是兩個類別
   這兩個類別的物件之間不能互相賦值
   
   類別模板與繼承有以下幾種：
     
   | 父類別 | 衍生出 | 子類別 |
   | :---: | :---: | :---: |
   | 類別模板 | $\Rightarrow$ | 模板類別 |
   | 模板類別 | $\Rightarrow$ | 類別模板 |
   | 普通類別 | $\Rightarrow$ | 類別模板 |
   | 模板類別 | $\Rightarrow$ | 普通類別 |
   
   註：模板類別即類別模板中類型/非類型參數實例化後的類別
   
	```graphviz
	digraph hierarchy {

					nodesep=1.0 // increases the separation between nodes

					node [color=Red] //All nodes will this shape and colour
					edge [color=Blue] //All the lines look like this

					類別模板->模板類別
					模板類別->類別模板
					普通類別->類別模板
					模板類別->普通類別
	}
	```

   (1)類別模板從類別模板衍生
   ```c++
   template <class T1, class T2>
   class A
   {
   	T1 v1, T2, v2;
   };
   
   template <class T1,  calss T2>
   class B: public A<T2, T1>
   {
   	T1 v3, T2 v4;
   };
   
   template <class T>
   class C: public B<T, T>
   {
   	T v5;
   };
   
   int main()
   {
   	B<int, doublie> obj1;
		C<int> obj2;
		return 0;
   }
   ```
   
   (2)類別模板從模板類別衍生
   ```c++
   template <class T1, class T2>
   class A
   {
   	T1 v1, T2 v2;
   };
   
   template<class T>
   class B: public A<int , double>
   // 已經先指定了A的T1和T2
   {
   	T v;
   };
   
   int main()
   {
   	B<char> obj1;
		return 0;
   }
   ```
   自動生成兩個模板類別：```A<int, double>和B<char>```
   
   (3)類別模板從普通類別衍生
   ```c++
   class A
   {
   	int v1;
   };
   
   template <class T>
   class B: public A
   {
   	T v;
   };
   
   int main()
   {
   	B<char> obj1;
		return 0;
   }
   ```
   
   (4)普通類別從模板類別衍生出來
   ```c++
   class A
   {
   	T v1;
	int n;
   };
   
   class B:public A<int>
   // 已經指定了A的T是何種類型
   {
   	double v;
   };
   
   int main()
   {
   	B obj1;
		return 0;
   }
   ```
   
   問題：能不能從類別模板衍生出普通類別？
   Ans: 不行，因為類別模板需要指定資料型別，若是在普通類別的定義中先定義了資料型別，代表是從模板類別衍生出普通類別(已經實例化)，而非類別模板。
   
4. string類別
   
   string類別是一個模板類別，它的定義如下：
   ```c
   typedef basic_string<char> string;
   ```
   (註：[typedef](https://zh.wikipedia.org/wiki/Typedef)是一個關鍵字。它用來對一個資料類型取一個別名，目的是為了使原始碼更易於閱讀和理解。)
   
   使用string類別要包含標頭檔```<string>```
   string物件的初始化：
   * string s1("Hello"); // 一個參數的建構子
   * string s2(8, 'x'); //兩個參數的建構子，代表8個x
   * string month = "March";
   
   不提供以char和int為參數的建構子
   錯誤的初始化方法：
   ```c++
   string error1 = 'c';	// error
   string error2('u');	// error
   string error3 = 22;	// error
   string error4(8);	// error
   ```
   但可以將char賦值給string物件
   ```c++
   string s;
   s = 'n';
   ```
   
   * 建構的string太長而無法表達 $\Rightarrow$ 會拋出length_error異常
   * string物件的長度用成員函數length()讀取
     ```c++
	 string s("hello");
	 cout << s.length() << endl;
	 ```
   * string支持串流讀取運算子
     ```c++
	 string stringObject;
	 cin >> stringObject;
	 ```
   * string支持getline函數
     ```c++
	 string s;
	 getline(cin, s);
	 ```
   * 用'='賦值'
     ```c++
	 string s1("cat"), s2;
	 s2 = s1;
	 ```
   * 用assign成員函數複製
     ```c++
	 string s1("cat"), s2;
	 s2.assign(s1);
	 ```
   * 用assign成員函數進行「部分複製」
     ```c++
	 string s1("catpig"), s2;
	 s2.assign(s1, 1 ,3)
	 //從s1中下標為1的char開始父至三個char給s2
	 ```
	 所以s2為"atp"
   
   * string還支持單個char的複製
     ```s2[5] = s1[3] = 'a'```
   * 逐個訪問string物件中的char
     ```c++
	 string s1("Hello");
	 for(int i = 0; i < s1.length(); ++i)
	 	cout << s1.at(i) endl;
	 ```
     註：成員函數at會作範圍檢查，如果超出範圍，會拋出out_of_range異常，而下標運算子不作範圍檢查
   * 用+運算子連接字串
     ```c++
	 string s1("good"), s2("morning!");
	 s1 += s2;
	 cout << s1;
	 ```
   * 用成員函數append連接字串
     ```c++
	 string s1("good"), s2("morning!");
	 s1.append(s2);
	 cout << s1;
	 s2.append(s1, 3, s1.size());	// s1.size(), s1字元數
	 cout << s2;
	 // 下標為3開始複製s1.size()個字元到s2後面
	 // 如果字串內沒有足夠字元，則複製到字串最後一個字元
	 ```
   * 用關係運算子比較string的大小
     - ==、>、>=、<、<=、!=
     - 返回值類型都是bool，成立返回true，否則反為false
     - 例如：
       ```c++
	   string s1("heelo"), s2("hello"), s3("hell");
	   bool b = (s1 == s2);
	   cout << b << endl;	// 印出1
	   b = (s1 == s3);
	   cout << b << endl;	// 印出0
	   b = (s1 > s3)
		   cout << b << endl// 印出1
	   ``` 
   * 成員函數substr()表示子字串
     ```c++
	 string s1("hello world"), s2;
	 s2 = s1.substr(4, 5);	// 從下標4開始5個char
	 cout << s2 << endl;	// 印出o wor
	 ```
   * 成員函數find()尋找string中的char
     ```c++
	 string s1("hello world");
	 s1.find("lo");
	 // 在s1中從前向後尋找"lo"第一次出現的地方
	 // 如果找到，返回"lo"開始的位置，即I所在的位置下標
	 // 如果找不到，返回string::npos (string中定義的靜態常數)
	 ```
   * 成員函數rfind()變成「從後往前」找
     ```c++
	 string s1("hello world");
	 s1.rfind("lo");
	 // 在s1中從前向後尋找"lo"第一次出現的地方
	 // 如果找到，返回"lo"開始的位置，即I所在的位置下標
	 // 如果找不到，返回string::npos
	 ```
   * 成員函數find_first_of()
     ```c++
	 string s1("hello world");
	 s1.find_first_of("abcd");
	 // 在s1中從前向後查找"abcd"中「任何一個字元」第一次出現的地方
	 // 如果找到，返回找到字母的位置；否則返回string::npos
	 ```
   * 成員函數find_last_of()
     ```c++
	 string s1("hello world");
	 s1.find_last_of("abcd");
	 // 在s1中查找"abcd"中任何一個字元「最後一次」出現的地方
	 // 如果找到，返回找到字母的位置；否則返回string::npos
	 ```
   * 成員函數find_first_not_of()
     ```c++
	 string s1("hello world");
	 s1.find_first_not_of("abcd");
	 // 在s1中從前向後查找「不在」"abcd"中的字母第一次出現的地方
	 // 如果找到，返回找到字母的位置；否則返回string::npos
	 ```
   * 成員函數find_last_not_of()
     ```c++
	 string s1("hello world");
	 s1.find_last_not_of("abcd");
	 // 在s1中「從後向前」查找「不在」"abcd"中的字母第一次出現的地方
	 // 如果找到，返回找到字母的位置；否則返回string::npos
	 ```
	 
   實例：
   ```c++
   string s1("hello worlld");	// "worlld"是故意的
   cout << s1.find("ll") << endl;	// 輸出2
   cout << s1.find("abc") << endl;	// 輸出4294967295
   cout << s1.rfind("ll") << endl;	// 輸出9
   cout << s1.rfind("abc") << endl;	// 輸出4294967295
   cout << s1.find_first_of("abcde") << endl;	// 輸出1，找到e
   cout << s1.find_first_of("abc") << endl;	// 輸出4294967295，abc都沒出現
   cout << s1.find_last_of("abcde") << endl;	// 輸出11，找到d
   cout << s1.find_last_of("abc") << endl;	// 輸出4294967295
   cout << s1.find_first_not_of("abcde") << endl;	// 輸出0，找到h
   cout << s1.find_first_not_of("hello world") << endl;	// 輸出4294967295
   cout << s1.find_last_not_of("abcde") << endl;	// 輸出10，找到worlld的第二個l
   cout << s1.find_last_not_of("hello world") << endl;	// 輸出4294967295
   ```
   
   替換string中的char：
   成員函數erase()
   ```c++
   string s1("hello worlld");
   s1.erase(5);
   cout << s1 << endl;
   cout << s1.length() << endl;
   cout << s1.size() << endl;
   // 去掉下標5及之後的字元
   ```
   
   以上程式碼輸出：
   ```c++
   hello
   5
   5   
   ```
   
   也可以只擦除一部分，如：
   ```
   string s1("hello worlld");
   s1.erase(5,4);
   cout << s1 << endl;
   // 輸出hellolld，其中" wor"被擦掉了
   ```
   
   成員函數find()也可以指定開始查找的位置：
   ```c++
   string s1("hello worlld");
   cout << s1.find("ll", 1) << endl;	// 輸出2
   cout << s1.find("ll", 2) << endl;	// 輸出2
   cout << s1.find("ll", 3) << endl;	// 輸出9
   // 分別從下標1, 2, 3開始查找"ll"
   ```
   
   成員函數replace()：
   ```c++
   string s1("hello world");
   s1.replace(2, 3, "haha");
   cout << s1;
   // 將s1中下標2開始的3個字元換成"haha"
   ```
   以上程式碼輸出
   ```
   hehaha world
   ```
   
   replace還可以指定替換字串的子字串：
   ```c++
   string s1("hello world");
   s1.replace(2, 3, "haha", 1, 2);
   cout << s1;
   // 將s1中下標2開始的3個字元
   // 換成"haha"中下標1開始的2個字元，也就是"ah"
   ```
   以上程式碼輸出
   ```
   heah world
   ```
   在string中插入字元：
   成員函數insert()
   ```c++
   string s1("hello world");
   string s2("show insert");
   s1.insert(5, s2); // 將s2整個插入s1下標5的位置
   cout << s1 << endl;
   s1.insert(2, s2, 5, 3);
   // 將s2中下標5開始的3個字元插入s1下標2的位置
   cout << s1 << endl;
   ```
   以上程式碼輸出
   ```
   helloshow inset world
   heinslloshow insert world
   ```
   
   轉換成C語言格式的```char *```字串：
   成員函數c_str()
   ```c++
   string s1("hello world");
   printf("%s\n", s1.c_str());
   // s1.c_str()返回傳統的const char * 類型字元陣列
   // 而且該字串以'\0'結尾
   ```
   
   也可以使用成員函數data()
   ```c++
   string s1("hello world");
   const char * p1 = s1.data();
   for(int i = 0; i < s1.length(); ++i)
   	printf("%c",*(p1+i));
   // s1.data() 返回一個char *類型的字串
   // 對s1的修改可能會使p1出錯
   ```
   
   成員函數copy()
   ```c++
   string s1("hello world");
   int len = s1.length();
   char * p2 = new char[len+1];
   s1.copy(p2, 5, 0);
   p2[5] = '\0';
   cout << p2 << endl;
   // s1.copy(p2, 5, 0)從s1下標0的字元開始，
   // 製作一個最長5個字元的字串副本並將其賦值給p2
   // 返回值表明實際賦值字串的長度
   ```
   以上程式碼輸出
   ```
   hello
   ```
   
   string的其他特性：
   - capacity(): 返回無需增加內存及可存放的字元數
   - maximum_size(): 返回string物件可存放的最大字元數
   - 成員函數length()和size()相同
   - empty(): 返回string物件是否為空字串，如：
     ```
	 string s1("hello"), s2("");
	 cout << s1.empty() << ", "<< s2.empty() << endl;
	 // 0, 1
   - resize(): 改變string物件的長度

5. 輸入輸出
   
   與輸入輸出串流操作相關的類別
	```graphviz
	digraph hierarchy {

					nodesep=1.0 // increases the separation between nodes

					node [color=Red,fontname=Courier,shape=box] //All nodes will this shape and colour
					edge [color=Blue, style=dashed] //All the lines look like this

					ios->{istream ostream}
					istream->{ifstream iostream}
					ostream->{iostream ofstream}
					iostream->fstream
	}
	```

   istream是用於輸入的串流類別，cin就是該類別的物件。
   ostream是用於輸出的串流類別，cout就是該類別的物件。
   ifstream是用於從文件讀取資料的類別。
   ofstream是用於向文件寫入資料的類別。
   iostream是既能用於輸入，又能用於輸出的別。
   fstream是既能從文件讀取資料，又能向文件寫入資料的類別。
   
   | 功能 | 物件 | 設備 |
   | :--------: | :---: | :--------: |
   | 輸入串流物件：|       |            |
   |  | cin     | 與標準輸入設備相連     |
   | 輸出串流物件：|       |            |
   |  | cout   | 與標準輸出設備相連      |
   |  | cerr   | 與標準錯誤輸出設備相連   |
   |  | clog   | 與標準錯誤輸出設備相連   |
   
   缺省情況下
   cerr << "Hello, world" << endl;
   clog << "Hello, world" << endl;
   和
   cout << "Hello, world" << endl; 一樣
   
   通過重定向，使得cout和cerr可以面相不同的地方輸出資料，這就能達到調適訊息和程序真正應該輸出結果分開輸出的目的。
   
   ### 標準串流物件：
   * cin對應標準輸入串流，用於從鍵盤讀取數據，也可以被重定向為從文件中讀取數據。
   * cout對應於標準輸出串流，用於向螢幕輸出數據，也可以被重定向為向文件寫入數據。
   * cerr對應於標準錯誤輸出串流，用於向螢幕輸出錯誤訊息。
   * clog對應於標準錯誤輸出串流，用於向螢幕輸出錯誤訊息。
   * cerr和clog的區別在於cerr不使用緩衝區(buffer)，直接向螢幕輸出訊息；
     而輸出到clog中的訊息會先被存放在buffer中，等到buffer放滿或者refresh時才輸出到螢幕上。

   ### 輸出重定向的例子：
   ```c++
   #include <iostream>
   using namespace std;
   int main()
   {
   	int x, y;
		cin >> x >> y;
		
		fPtr = freopen("test.txt", "w", stdout);	// 將標準輸出重定向到test.txt文件
		
		if (!fPtr)
		{
			// 重定向失敗，離開本程式
			return 1;
		}
		
		if(y == 0)	// 除數為0則在螢幕上輸出錯誤訊息，cerr並沒有被重定向
			cerr << "error!" << endl;
		else
			cout << x / y;	// 輸出結果到test.txt
		
		fclose(fPtr);
		return 0;
   }
   ```
   
   ### 輸入也可以重定向：
   ```c++
   #includde <iostream>
   using namespace std;
   int main()
   {
   	double f;
		int n;
		freopen("t.txt", "r", stdin);	// cin被改為從t.txt中讀取資料
		cin >> f >> n;
		cout << f << "," << n << endl;
		retuen 0;
   }
   ```
   假設t.txt中內容為：```3.14 123```
   則上述程式結果輸出：```3.14 123```
   
   ### 判斷輸入串流結束
   
   可以用如下方法判斷輸入串流結束：
   ```c
   int x;
   while(cin >> x)
   {
   	......
   }
   return 0;
   ```
   如果是從文件輸入，比如前面有：
   ```
   freopen("some.txt", "r", stdin);
   ```
   那麼讀取到文件尾部，輸入串流就算結束
   如果是從鍵盤輸入，則在單獨一行輸入Ctrl+Z代表輸入串流結束
   
   istream類別的成員函數：
   ```istream & getline(char * buf, int bufSize);```
   從輸入串流中讀取bufSize-1個字元到緩衝區buf，或讀到碰到'\n'為止(哪個先到算哪個)。
   
   ```istream & getline(char * buf, int bufSize, char delim);```
   從輸入串流中讀取bufSize-1個字元到緩衝區buf，或讀到碰到delim字元為止(哪個先到算哪個)。
   
   兩個函數都會自動在buf中讀入資料的結尾添加'\0'，
   '\n'或delim都不會被讀入buf，但會被從輸入串流中取走。如果輸入串流中'\n'或delim之前的字元數達到或超過了bufSize個，就導致讀入出錯，其結果就是：雖然本次讀入已經完成，但是之後的讀入就都會失敗了。
   
   可以用if(!cin.getline(...))判斷書入是否結束:star:
   
   ```bool eof();```判斷輸入流是否結束
   ```int peek()```返回下一個字元，但不從串流中去掉。
   
   ```istream() & puback(char c);``` 將字元c放回輸入串流
   ```istream & ignore(int nCount = 1, int delim = EOF);```從串流中刪掉最多nCount個字元，遇到EOF時結束(EOF的值通常是1)。
   
   get很常用，以下例子解釋：
   ```c
   #include <iostream>
   using namespace std;
   int main()
   {
   	int x;
		char buf[100];
		cin >> x;
		cin.getline(buf, 90);
		cout << buf << endl;
		return 0;
   }
   ```
   此時若標準串流輸入：
   ```12 abcd↵```

   ↵：代表輸入鍵(enter)
   此時12被讀到了x中
   輸出：
   ```abcd (空格+abcd，空格沒有被跳過去)```
   
   輸入：
   ```12↵```
   程序立即結束，沒有任何輸出(其實有換行)，也無法在鍵入下一行。
   
   因為>>運算子把12放到x中之後，並沒有把串流中的'\n'拿走，而getline讀到留在串流中的'\n'就會返回，此時的buf裡面只有唯一一個自動添加的'\n'字元。
   
## sstream用法：
包括字串轉int，int轉字串，或者分割、合併字串。
需要```#include <sstream>```

範例1：把int型態的數字轉成string
```c
#include <sstream>

using namespace std;

int main()
{
    stringstream s1;	// 宣告一個stringstream物件
    int number = 1234;
    string output;	// 要把number轉成字串型態的容器
    
    cout << "number=" << number << endl;	// 螢幕輸出number=1234;
    
	s1 << number; // 將以int宣告的number放入我們的stringstream中
    s1 >> output;
    
    cout<<"output="<<output<<endl;	// 顯示output=1234;
}
```

範例2：將string轉成int
```c
#include <sstream>

using namespace std;

int main()
{
    stringstream string_to_int;
    string s1 = "12345";
    int n1;

    string_to_int << s1;
    //也可以使用string_to_int.str(s1);
    //或者 s1 = string_to_int.str();
    
    string_to_int >> n1;

    cout << "s1=" << s1 << endl;//s1=12345
    cout << "n1=" << n1 << endl;//n1=12345
}
```

範例3清空字串串流：
有些時候我們會直接沿用之前用過的字串串流，若要拿來重複使用於型態轉換，必須小心確保字串有清空，以免失敗
```c
stringstream ss;

int number  = 12345678;
ss << number;

cout << "int to string" <<endl;
string convert_str;
ss >>  convert_str;

string numberStr = "654321";
int num=0;

ss << numberStr;
ss >> num;

cout << "str type:" << ss.str() <<endl; //印出stringstream中的字串
cout << "convert to num:" << num <<endl;
```
此時程式輸出：
```
int to string
str type:654321
convert to num:0
```
發現原先的字串仍在stringstream 中，並且也沒有寫入到num的資料。
解決辦法，必須先呼叫：
```c
ss.str("");
ss.clear();
```
這兩行基本上缺一不可，缺少了任一行就會導致某些情況下的錯誤。
1. 僅只用s1.str("");   未使用s1.clear();
```c
#include <sstream>
#include <iostream>

using namespace std;

int main()
{
    stringstream ss;
    int i1=77777;
    string s1;
    ss << i1;
    ss >> s1;
    
    cout << "i1 = "<<i1<<endl;
    cout << "s1 = "<<s1<<endl;

    //僅用ss.str("");初始化
    ss.str("");
    //////////////////////
    string s2="888888";
    int i2=0;
    ss << s2;
    ss >> i2;
    
    cout << "str type = " << ss.str() << endl;	// 這邊理論上會藉由ss<<s2寫入stringstream
    cout << "int type = " << i2 << endl;	// 理論上會藉由ss>>i2;寫到i2內部
}
```
輸出結果：
```
i1 = 77777
s1 = 77777
str type = 
int type = 0
```
有發現了嗎?我們初始化並沒有成功，s2並沒有成功的放入ss，而且ss也沒有成功的傳到i2內。
原因是出在當執行到```ss >> s1```這行時，我們的stringstream已經讀到EOF了。
在stringstream的情況如下：

執行完```ss >> s1```時的情況：```"77777"EOF```
當我們使用```ss.str("")```時：```""EOF```

它並不會把EOF的地方清空，會繼續保留，再次使用的時候就會發生錯誤了。

2. 僅只用ss.clear();
```c
#include <sstream>
#include <iostream>

using namespace std;

int main()
{
    stringstream ss;
    int i1 = 87;
    string s1;
    
    ss << i1;
    ss >> s1;
    
    cout << "i1 = " << i1 << endl;
    cout << "s1 = " << s1 << endl;
    
    ss.clear();
    
    string s2 = "877";
    int i2;
    
    ss << s2;
    ss >> i2;

    cout << "stringstream內部 = " << ss.str() << endl;
    cout << "s2 = " << s2 << endl;
    cout << "i2 = " << i2 << endl;
}
```
執行結果：
```
i1 = 87
s1 = 87
stringstream = 87877
str type = 877
int type = 877
```
這邊發現stringstream的內部有點奇怪！還保存著之前的結果，並不算完全的初始化。
來分析一下stringstream內部在程式內運行的結果：

執行完ss>>s1時的內部狀況：87EOF

此時有了EOF之後已經不能再操作了，接著使用ss.clear();
此時stringstream內部的情況：87(沒有EOF了可以繼續操作)

執行完ss<<s2時的內部狀況：87877EOF
參考資料來自[這邊](https://home.gamer.com.tw/creationDetail.php?sn=4114818)。

範例4：串接字串
```c
#include <iostream>
#include <sstream>
using namespace std;

int main() {
	stringstream ss;
	char a[] = "Hello";
	char b[] = "World!";
	ss << a << " " << b;
	const char * c = ss.str().c_str();
	cout << c;
	return 0;
}
```
輸出：
```
Hello World!
```
特別注意：
```ss.str()```會回傳一個string物件
```c_str()```是從string轉回c風格的字串，但是const的，如果需要移除const，請參考C++11新特性筆記：[const_cast](/wNsVLFPfTOie9ZYSktnirA?view#強制類型轉換)
```c
char * c = const_cast<char *>(ss.str().c_str());
```

