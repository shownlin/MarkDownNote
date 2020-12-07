# 北京大學 - C++程序設計 (Coursera) 上

第7週[筆記在這](/JPMUh5LvSVeEo0hfWmK7AA)
STL相關[筆記在這](/e00_PVmZQYK9N1CkbIZxtw)
C++11新特性[筆記在這](/wNsVLFPfTOie9ZYSktnirA)

## 第二週

1. 函數指針

   返回類型(* 指針名)(參數類型1, 參數類型2, ...)
   例如：
    ```c++
   int (* func_ptr)(int) = func; // make function pointer to point func
    ```
    用在C語言的qsort：
    ```c++
    void qsort(void *base, int nelem, unsigned int width, int(* pfCompare)(const void *, const void *))
    ```
    其中```int (* pfCompare)(const void * elem1, const void * elem2)```為一個函數指針
    如果elem1應排在elem2前面，應返回負整數
    反之則正整數，兩者皆可擇回傳0
    
    例如：
    ```c++
    int Mycompare(const void * elem1, const void * elem2)
    {
        unsigned  int * p1 , * p2;
        // 需要強制型別轉換，因為void類型compiler不知道資料大小
        p1 = (unsigned int *) elem1;
        p2 = (unsigned int *) elem2;
        return (* p1 % 10) - (* p2 % 10);
    } 
    ```
    使用在qsort時，只需要把函數名稱拿來直接用：
    ```c++
    int main()
    {
        int a[100],i;
        srand((unsigned) time(NULL));
        for(i=0;i<100;i++)
            a[i]=rand();       //亂數產生100個數字
        qsort((void *)a, 100, sizeof(a[0]), Mycompare);
        for(unsigned i = 0; i < 100; i++)
            printf(" %d\t", a[i]); //印出排序後的內容
    }
    ```
    
    而函數指針本身的用法，要呼叫其所指向的函數時，可以如同一般指針加*號，也可以當成函數名稱直接使用，例子：
    ```c++
    // 宣告函數指針
    int (* pName)(const void *, const void *);
    pName = Mycompare;
    unsigned int a[] = {1023, 2020};
    
    // 透過函數指針呼叫函數
    int result = (*pName)(a, a+1);    // 若加星號則括號不能拿掉
    int result = pName(a, a+1);       // 也可以當成函數名稱直接使用
    cout << result << endl;
    ```
    上面例子輸出：```3```

2. 命令列參數

    例如:
    notepad sample.txt
    copy file1.txt file2.txt
    則sample.txt、file1.txt和file2.txt都是參數傳給前面的command
    在C裡面如何得到再terminal執行時的參數？
    ```c++
    int main(int argc, char * argv[])
    {
        ...
    }
    ```
    argc：程式啟動時，command參數的個數，而程式本身的檔案名也算一個參數，因此argc的值至少為1。
    
    argv：指針陣列，其中的每個元素都是一個char*類型的指針，該指針指向一個string，而這個string中就存放著命令列參數。
    
    例如：
    **argv[0]** 指向的字串就是第一個命令列參數，即該程式本身的檔案名稱。
    **argv[1]** 則指向第二個參數，如copy file1.txt file2.txt中的file1.txt，以此類推。
    
    範例(sample.exe)：
    ```c
    #include <stdio.h>
    int main(int argc, char * argv[])
    {
        for(int i = 0;i < argc; ++i)
            printf("%s\n", argv[i]);
        return 0;
    }
    ```
    執行```sample para1 para2 s.txt 5 "hello world"```
    則輸出
    ```
        sample
        para1
        para2
        s.txt
        5
        hello world
    ```
3. 位元運算子(bitwise operator)

   有六種位元運算子
   | operator | name | unary/binary |
   | -------- | -------- | -------- |
   | & | and | binary |
   | \| | or | binary |
   | ^ | xor | binary |
   | ~ | complement  | unary |
   | << | 邏輯左移 | binary |
   | >> | 算數右移 | binary |

   例如：
   ```c
   // int的低8位清成0
   n &= 0xffffff00;
   // 判斷int的第7位是不是1(從右往左數第七位)
   if ((n & 0x80) == 0x80)
       return ture;
   // 數字交換
   int a = 5, b = 7;
   a ^= b;
   b ^= a;
   a ^= b;
   ```
   特別注意，右移相當於除以$2^n$，並將結果往小裡取整。
   ```c
   -25 >> 4 = -2 (原本結果為-1.xxx，取floor)
   -2 >> 4 = -1
   ```

4. 引用

   ```c++
   int n = 4;
   int & r = n; //r引用了n，r的類型是int &
   ```
   則r跟n是等價的，兩者的操作被綁在一起。
   
   使用引用時一定要初始化成某個引用變數，而且從一而終不可再改。
   只能引用變數，不能引用運算式(expression)。

   例如：
   ```c++
   void swap(int & a, int & b)
   {
       int tmp;
       tmp = a;
       a = b;
       b = tmp;
   }
   int n1, n2;
   swap(n1, n2);
   ```
   或是把引用當成返回值使用：
   ```c++
   int n =4;
   int & SetValue(){return n;}
   int main()
   {
       SetValue() = 40;
       cout << n;
       return 0;
   }
   ```
   相當於對n進行賦值。
   另外定義引用時若加上const，例如```const int & r = n```，則為常數引用。
   即不能通過該引用去修改其引用的內容。(read only)

5. const關鍵字和常數

   define也可以定義常數，但C++裡面建議用const。
   用法：
   * 常數：
     ```c++
     const char * SCHOOL_NAME = "Peking University"
     ```
     定義之後不可更改SCHOOL_NAME之內容
   * 常數指針：
     ```c++
     int n,m
     const int * p = & n;
     * p = 5; // 此處會出錯
     n = 4;
     p = & m; // 正確，還是可以修改p本身指向的定義
     ```
     不可以通過常數指針去修改其指向的「內容」，
     另外不可以把常數指針賦值給非常數指針，反過來可以，如：
     ```c++
     const int * p1;
     int * p2;
     p1 = p2; // ok
     p2 = p1; // error
     p2 = (int *) p1 // ok，必須先強制類型轉換，此時就可以透過p2去修改內容了
     ```
     函數內容為常數指針時，可以避免函數內部不小心改變參數指針所指地方的內容
     ```c++
     void MyPrintf(const char *p)
     {
         strcpy(p, "this"); // 會出錯
         printf("%s", p); // ok
     }
     ```
   * 常數引用：
     同上一節之內容(第四節)

6. 動態內存分配

   實際執行程式時按需要來進行空間分配，如C語言中的malloc，而C++裡則改用new。
   
   * 第一種用法，分配一個變數：
     ```c++
     P = new T;
     ```
     T是任意類型名稱，P是類型為T*的指針。
     動態分配出一片大小為sizeof(T) bytes的內存空間，並且將該內存空間的起始地址賦值給P。
     比如：
     ```c++
     int * pn;
     pn = new int; // 動態分配出一個int的空間給pn所指的地方
     * pn = 5;
     ```
   * 第二種用法，分配一個陣列：
     ```c++
     P = new T[N]
     ```
     T：任意類型名
     P：類型為T*的指針
     N：要分配的陣列元素個數，可以是整數表示式
     動態分配出一片大小為N * sizeof(T) bytes的內存空間，並將該內存空間的起始地址賦值給P。
     範例：
     ```c++
     int *pn
     int i = 5;
     pn = new int[i * 20];
     pn[0] = 20;
     pn[100] = 30; // 可以compile，但實際運行時會造成index越界
     ```
     
   * new運算子的返回值類型：
     ```
     new T;
     new T[n];
     ```
     兩者的返回類型都是T*
   
   new必須搭配使用delete運算子來進行空間釋放。
   用法：
   ```c++
   // delete pointer;
   int * p =new int;
   * p = 5;
   delete p; // ok，p所指的空間被釋放
   delete p; // error，被new出來的空間僅能被delete一次
   ```
   如果是陣列呢？
   ```c++
   // delete [] 指針
   int * p = new int[20];
   p[0] = 1;
   delete [] p;
   ```
   特別注意釋放new出來的陣列時，一定要加中括號，否則compile和執行時都不會出錯。
   但空間其實只釋放了指針所指的第一個元素所站的空間，並沒有完全的回收乾淨，
   最終造成其他process不能再利用該空間。

7. inline function、函數重載

   * inline function(內嵌函數、內聯函數)
     函數呼叫是有時間成本，如果函數本身只有幾行程式碼，又要執行多次的話，成本相較之下會很大。
     可以用inline function來減少函數呼叫的開銷。
     compiler處理inline function時，是將整個function的程式碼(body)插進呼叫處。
   
     範例：
     ```c++
     inline int Max(int a, int b)
     {
         if(a>b) return a;
         return b;
     }
     ```
     壞處是程式碼本身的體積大小可能會增加。
   
   * overload
     多個函數名字相同，但是參數個數或參數類型不相同，這叫做函數的重載(overloading)
     以下三個函數是重載關係：
     ```c++
     int Max(double f1, double f2){}
     int Max(int n1, int n2){}
     int Max(int n1, int n2, int n3){}
     ```
     函數重載的好處是讓函數命名變得比較簡單，
     編譯器會根據caller給的引數決定該調用哪個函數。
   
8. 預設參數

   C++中，定義函數的時候可以讓最右邊的連續若干個參數有缺省值。
   
   範例：
   ```c++
   void func(int x1, int x2 = 2, int x3 = 3){}
   func(10); // 相當於func(10,2,3)
   func(10, 8); // 相當於func(10,8,3)
   func(10,,8); // error，只能省略最右邊的連續若干個參數
   ```
   好處：
   提高程式碼本身的可擴充性，新增的內容如果需要新參數，然而原本其他有call這個function的程式不需要重新寫新的呼叫方法。
   
9. OOP(物件導向)
   
   結構化的程式設計思考：
   複雜的大問題 $\Rightarrow$ 分解/模組化 $\Rightarrow$ 若干子問題
   (即Top-down的方式)
   
   缺點：
   理解困難、修改困難、debug困難、reuse困難
   
   OOP把原本的結構化程式改成 class + class + ... + class
   即把原本的程式抽象化，將data structure + function封裝成class
   寫程式時，由一個main() function去呼叫各個class來使用。
   
   範例：
   ```c++
   class CRectangle
   {
       public:
           int w, h;
        void Init(int w_, int h_)
        {
            w = w_; h = h_;
        }
        int Area()
        {
            return w * h;
        }
        int Perimeter()
        {
            return 2 * (w + h);
        }
   }; // 結尾必須有分號
   
   int main()
   {
       int w, h;
       CRectangle r; // r是一個object
       cin >> w >> h;
       r.Init(w,h);
       cout << r.Area() << endl << r.Perimeter();
       return 0;
   }
   ```
   class定義的變數 $\Rightarrow$ class的instance $\Rightarrow$ "object"
   此過程稱為實體化。
   每一個object都有自己被分配的內存空間，
   object的大小 = 所有成員變數(又稱「屬性」，即attributes)的大小之和。
   且同一個class實體化出來的object之間不互相影響彼此的成員變數。
   object之間可以用'='進行賦值，但是不能使用==、!=、<、>、>=、<=來進行比較。
   (除非進行Operator Overloading)
   
   * 用法1：物件名.成員名
   ```c++
   CRectangle r1, r2;
   r1.w = 5;
   r2.Init(3, 4);
   ```
   
   * 用法2：指針->成員名
   ```c++
   CRectangle r1, r2;
   CRectangle * p1 = & r1;
   CRectangle * p2 = & r2;
   p1 -> w = 5;
   p2 -> Init(3, 4);
   ```
   即```p->member```等價於```*p.member```
   
   * 用法3：引用名.成員名
   ```c++
   CRectangle r2;
   CRectangle & rr = r2;
   rr.w = 5;
   rr.Init(3, 4); // rr的值變了，r2的值同時被改變
   ```
   
   * 可以把輸出結果的方式寫成一個function
   利用引用
   ```c++
   void PrintRectangle(CRectangle & r)
   {
       cout << r.Area() << "," << r.Perimeter();
   }
   CRectangle r3;
   r3.Init(3, 4);
   PrintRectangle(r3);
   ```
   
   * 可以把class的宣告和定義分開寫
   利用雙冒號，如：
   ```c++
   class CRectangle
   {
       public:
           int w, h;
        void Init(int w_, int h_);
        int Area();
        int Perimeter();
   }; 
   void CRectangle::Init(int w_, int h_)
   {
       w = w_; h = h_;
   }
   int CRectangle::Area()
   {
       return w * h;
   }
   int CRectangle::Perimeter()
   {
       return 2 * (w + h);
   }
   ```

10. class成員的可訪問範圍

    關鍵字 -- class member可被訪問的範圍分成下列幾項：
    * private：私用，只能在「成員函數內」被訪問
    * public：公有，可以在「任何地方」被訪問
    * protected：保護，被宣告為保護成員時，繼承它的class可以直接使用
    
    在沒有特別指定的情況下預設為private。

---

## 第三週

1. 內嵌成員函數、重載成員函數

   * inline + 成員函數
   * 整個函數體出現在class定義內部
   ```c++
   class B
   {
       inline void func1();
       void func2();
   };
   void B::func1(){}
   ```

   成員函數的重載和預設函數
   ```c++
   #include <iostream>
   using namespace std;
   class Location
   {
        private:
            int x, y;
        public:
            void init(int x = 0, int y = 0);
            void valueX(int val){x = val;}
            void valueX(){return x;}
   };
   void Location::init(int X, int Y)
   {
       x = X;
       y = Y;
   }
   int main()
   {
       Location A;
       A.init(5);
       A.valueX(5);
       cout << A.valueX(); // 輸出5
       return 0;
   }
   ```
   
   使用預設函數要注意避免函數重載時的二義性：
   ```c++
   class Location
   {
        private:
            int x, y;
        public:
            void init(inx = 0, int y =0);
            void valueX(int val = 0){x = val;}
            int valueX(){return x;}
   };
   
   Location A;
   A.valueX();    // error，compiler無法判斷呼叫哪個valueX
   ```
   
   注意：如果有兩個函數名字相同，參數表相同，但是返回值不同，這個不叫二義性，叫做重複定義。

2. 建構子(構造函數)

   * 名子與類別名相同，可以有參數，不能有返回值(void也不行)
   * 作用是對object進行initialization，如給成員變數賦予初值。
   * 如果是定義class時沒寫建構子，則compiler生成一個默認的無參數建構子(不做任何操作)。
   * 如果定義了建構子，則compiler不生成默認的無參數建構子，
   * object生成時建構子自動被呼叫，一旦生成object，就再也不能在其上執行建構子。
   * 一個class可以有多個構造函數。
   
   ```c++
   class Complex
   {
       private:
           double real, imag;
       public:
           void Set(double r, double i);
   };    //compiler自動生成建構子
   
   // 以下兩者都會造成默認建構子被呼叫
   Complex c1;
   Complex * pc = new Complex;
   ```
   
   一個基本的建構子範例：
   ```c++
   class Complex
   {
       private:
           double real, imag;
       public:
           void Complex(double r, double i = 0);
   };
   Complex::Complex(double r, double i)
   {
       real = r; imag = i;
   }
   
   Complex c1;    // error，缺少建構子的參數
   Complex * pc = new Compliex;    // error，缺少建構子的參數
   Complex c2; // ok
   Complex * pc = new Compliex(3, 4);    // ok
   ```
   
   可以有多個建構子，參數個數或類型不同。
   
   關於建構子在array中的使用：
   ```c++
   class CSample
   {
       int x;
       public:
       CSample()
       {
           cout << "Constructer 1 Called" << endl;
       }
       CSample(int n)
       {
           x = n;
           cout << "Constructer 2 Called" << endl;
       }
   };
   
   int main()
   {
       // 輸出Constructer 1 Called兩次
       CSample array1[2];
       
       // 輸出Constructer 2 Called兩次
       CSample array2[2] = {4,5};
       
       // array3[0]=3, array3[1]=0
       // 先輸出Constructer 2 Called再輸出Constructer 1 Called
       CSample array3[2] = {3};
       
       // 輸出Constructer 1 Called兩次
       CSample * array4 = new CSample[2];
       delete [] array4;
       return 0;
   }
   ```
   
   另一個在array中使用的例子：
   ```c++
   class Test
   {
       public:
           Test(int n){}           // (1)
           Test(int n, int m){}    // (2)
           Test(){}                // (3)
   };
   
   Test array1[3] = {1, Test(1, 2)};
   // 三個元素分別使用(1)、(2)、(3)進行初始化
   
   Test array1[3] = {Test(2, 3), Test(1, 2), 1};
   // 三個元素分別使用(2)、(2)、(1)進行初始化
   
   Test * pArray1[3] = {new Test(4), new Test(1, 2)};
   // 只用(1)、(2)分別對前兩個元素進行初始化，第三個所指的object沒有被生成
   ```
   
   小問題：
   假設 A 是一個class的名字，下面的程式碼生成了幾個class A的object？
   ```A * arr[4] = { new A(), NULL, new A() };```
   Ans: 2
   
3. 複製建構子(Copy constructor)

   基本型態：
   * 只有一個參數，即同類型物件的引用
   * 形如 ```X::X(X &)```或```X::X(const X &)```，二擇一
     後者能以常數物件作為參數
   * 如果沒有定義複製建構子，那麼compiler生成默認複製建構子，默認的複製建構子完成複製功能。

   例如：
   ```c++
   class Complex
   {
       private:
           double real, imag;
   };
   Complex c1;    // 調用缺省無參建構子
   Complex c2(c1);    //調用缺省的複製建構子，將c2初始化成和c1一樣
   ```
   
   如果定義自己的複製建構子，擇默認的複製建構子不被生成。
   ```c++
   class Complex
   {
       public:
           double real, imag;
           Complex(){}
           Complex(const Complex & c)
           {
               real = c.real;
               imag = c.imag;
               cout << "Copy Constructor called";
           }
   };
   Complex c1;
   Complex c2(c1); // 輸出Copy Constructor called
   ```
   
   注意：不允許有形如```X::X(X)```的建構子
   例如
   ```c++
   class CSample
   {
       CSample(CSample c)
       {
           // error，不允許出現這樣的建構子
       }
   }
   ```
   
   複製建構子起作用的三種情況：
   * 第一種：
     ```c++
     Complex c2(c1);
     Complex c2 = c1; // 初始化語句，非賦值語句
     ```
     
   * 第二種：
     如果有某函數有一個參數是class的object，那麼該函數被呼叫時，class A的copy constructor將被呼叫
     ```c++
     class A
     {
         public:
             A(){}
             A(A & a)
             {
                 cout << "Copy consturctor called" << endl;
             }
     };
     
     void Func(A a1){}
     
     int main()
     {
         A a2;
         Func(a2);    //輸出Copy consturctor called
         return 0;
     }
     ```
   * 第三種：
     如果函數的返回值是class A的object，則函數返回時，A的copy constructor將被呼叫
     ```c++
     class A
     {
         public:
             int v;
             A(int n){v = n;}
             A(A & a)
             {
                 v = a.v;
                 cout << "Copy consturctor called" << endl;
             }
     };
     
     A Func()
     {
         A b(4);
         return b;    //複製建構子被呼叫，所以回傳的值未必等於b
     }
     int main()
     {
         cout << Func().v << endl;
         return 0;
     }
     ```
     上述程式碼輸出Copy consturctor called。
     
   * 類型轉換建構子
     目的：實現類型的自動轉換
     特點：僅有一個參數，不是複製建構子
     編譯系統會自動呼叫 $\Rightarrow$ 轉換建構子 $\Rightarrow$ 建立一個臨時物件/臨時變數
     ```c++
     class Complex
     {
         pulic:
             double real, imag;
             Complex(int i)
             {
                 // 類型轉換建構子
                 cout << "IntConstructor called" << endl;
                 real = i; imag = 0;
             }
             Complex(double r, douple i)
             {
                 real = r; imag = i;
             }
     };
     
     int main()
     {
         Complex c1(7, 8);
         Complex c2 = 12;    // 這邊是初始化，不生成臨時物件，但依然調用類型轉換建構子
         c1 = 9;    // 9被自動轉換成一個臨時Complex物件
         cout << c1.real << "," << c1.imag << endl;
         return 0;
     }
     ```
     上述程式碼輸出：
     ```
     IntConstructor called
     IntConstructor called
     9,0
     ```

4. 解構子(Destructor)

   成員函數的一種
   * 名字與class名相同
   * 在前面加上'~'
   * 沒有參數和返回值
   * 一個class最多只有一個解構子
   
   object生命週期結束時 $\Rightarrow$ 自動被調用
   在object消亡前做善後工作 e.g.釋放分配的空間等等
   如果object是用new來產生，則利用delete來呼叫
   定義class時如果沒有定義Destructor，compiler自動生成一個默認的，但不涉及釋放user申請的內存釋放等清理工作
   
   ```c++
   class String
   {
       private:
           char * p;
       public:
           String()
           {
               p = new char[10];
           }
           ~String();
   };
   String::~String()
   {
       delete [] p;
   }
   ```
   
   解構子和陣列的關係如下：
   object陣列的每個element的destructor都會被呼叫
   ```c++
   class Ctest
   {
       public:
       ~Ctest()
       {
           cout << "destructor called" << endl;
       }
   };
   
   int main()
   {
       Ctest array[2];
       cout << "End Main" << endl;
       return 0;
   }
   ```
   以上程式碼輸出：
   ```
   End Main
   destructor called
   destructor called
   ```
   
   delete運算導致解構子被呼叫：
   ```c++
   Ctest * pTest;
   pTest = new Ctest;    // 建構子被呼叫
   delete pTest;         // 解構子被呼叫
   
   --
   
   pTest = new Ctest[3]; // 建構子被呼叫3次
   delete [] pTest;      // 解構子被呼叫3次
   ```
   
   以下一個例子詳解建構子和解構子之間的關係：
   ```c++
   class Demo
   {
       int id;
       pulic:
           Demo(int i)
           {
               id = i ;
               cout << "id = " << id << "Constructed" << endl;
           }
           ~Demo()
           {
               cout << "id = " << id << "Destructed" << endl;
           }
   };
   
   Demo d1(1);
   
   void Func()
   {
       //宣告static的變數屬於靜態存在期，特色是會一直存在，直到程式結束為止。
       static Demo d2(2);
       Demo d3(3);
       cout << "Func" << endl;
   }
   
   int main()
   {
       Demo d4(4);
       d4 = 6;    // 類型轉換建構子被呼叫，生成一個臨時object
       cout << "main" << endl;
       { Demo d5(5); }    // d5只在這個作用域有效，離開之後消亡
       Func();
       cout << "main ends" << endl;
       return 0;
   }
   ```
   以上程式碼輸出：
   ```
       id = 1 Constructed
       id = 4 Constructed
       id = 6 Constructed (臨時物件生成)
       id = 6 Destructed (臨時物件消亡)
       main
       id = 5 Constructed
       id = 5 Destructed
       id = 2 Constructed
       id = 3 Constructed
       Func
       id = 3 Constructed (2會繼續存在，但離開Func之後3會先消亡)
       main ends
       id = 6 Destructed (由於d4.id已經被改成6，此處是id=6不是4)
       id = 2 Destructed
       id = 1 Destructed (先被構造的後消亡，照順序)
   ```

5. 靜態成員變數、靜態成員函數

   靜態成員：在陳述前面加了satic關鍵字的成員，獨立於物件而存在
   例如：
   ```c++
   class CRectangle
   {
       private:
           int w, h;
           static int nTotalArea;
           static int nTotalNumber;    //靜態成員變數
       public:
           CRectangle(int W_, int h_);
           ~CRectangle();
           static void PrintTotal();    //靜態成員函數
   };
   ```
   普通的成員變數每個object有各自的一份，而static成員總共只有一份，由所有object共享。
   另外，當使用sizeof()計算大小時，不會把靜態成員考慮進去。
   例如：
   ```c++
   class CMyclass
   {
       int n;
       static int s;
   };
   
   printf("%d", sizeof(CMylass));    //印出4
   ```
   
   普通成員函數必須具體作用於某個object，而static函數並不具體作用於某個object。
   i.e. 不需要通過物件就能訪問此函數
   
   如何訪問靜態成員？
   * 類別名::成員名
     ```CRectangle::PrintTotal();```
   * 物件名::成員名
     ```c++
     Crectangle r;
     r.PrintTotal();
     ```
   * 指針->成員名
     ```c++
     CRectangle * p = & r;
     p -> PrintTotal();
     ```
   * 引用.成員名
     ```c++
     CRectangle & ref = r;
     int n = ref.nTotalNumber;
     ```
   (1) 靜態成員變數本質上是全域變數，那怕一個instance都不存在，class的靜態成員也會存在。
   靜態成員函數本質上是全域函數。
   
   (2) 靜態成員函數本質上是全域函數。
   
   (3) 靜態成員的目的是將某些class緊密相關的global variable和函數封裝到class裡面，看上去像一個整體，易於整理和維護。
   
   ```c++
   class CRectangle
   {
       private:
           int w, h;
           static int nTotalArea;
           static int nTotalNumber;    //靜態成員變數
       public:
           CRectangle(int W_, int h_);
           ~CRectangle();
           static void PrintTotal();    //靜態成員函數
   };
   
   CRectangle::CRectangle(int w_, int h_)
   {
       w = w_;
       h = h_;
       nTotalNumber ++;
       nTotalArea += w * h;
   }
   
   CRectangle::~CRectangle()
   {
       nTotalNumber --;
       nTotlaArea -= w * h;
   }
   
   void CRectangle::PrintTotal()
   {
       cout << nTotalNumber << "," << nTotalArea << endl;
   }
   
   int CReatangle::nTotalNumver = 0;
   int Crectangle::nTotalArea = 0;
   // 必須在定義class的文件中對靜態成員變數進行一次陳述或初始化
   // 否則compile能通過，link不能通過
   
   int main()
   {
       CRectangle r1(3. 3), r2(2, 2);
       cout << CRectangle::nTotalNumber; // error，私有
       CRectangle::PrintTotal();    // 輸出 2,13
       r1.PrintTotal();    // 輸出 2,13
       return 0;
   }
   ```
   
   注意：static成員必須在class定義外初始化，不可在class內部賦予初值，但是有一種例外，就是宣告為 const 的常數。
   另外，在靜態成員函數中，不能訪問非靜態成員變數，也不能調用非靜態成員函數。
   ```c++
   void CRectangle::PrintTotal()
   {
       cout << w << "," << nTotalNumber << "," << nTotalArea << endl; // wrong
   }
   
   int main()
   {
       CRectangle::PrintTotal(); // 解釋不通，w到底屬於哪個物件的？
   }
   ```
   
   剛剛的CRectangle類別寫法有缺陷，在哪裡？
   ```c++
   CRectangle::CRectangle(int w_, int h_)
   {
       w = w_;
       h = h_;
       nTotalNumber ++;
       nTotalArea += w * h;
   }
   
   CRectangle::~CRectangle()
   {
       nTotalNumber --;
       nTotlaArea -= w * h;
   }
   
   void CRectangle::PrintTotal()
   {
       cout << nTotalNumber << "," << nTotalArea << endl;
   }
   ```
   在使用CRectangle時，有時是呼叫copy constructor來生成臨時且隱藏的CRectangle instance！
   千萬不可以忽略複製建構子，
   畢竟不是全部的CRectangle都透過```CRectangle::CRectangle(int w_, int h_)```來生成object。
   
   比方說：
   (1) 調用一個以CRectangle類別物件作為「參數」的函數時
   (2) 調用一個以CRectangle類別物件作為「返回值」的函數時
   
   這樣就會發生一種情況：
   這些臨時物件被生成時，nTotalNumber和nTotalArea沒有被增加，但生命週期結束時卻被減掉了，造成總數變少。
   
   解決辦法：編寫一個複製建構子
   ```c++
   CRectangle::CRectangle(CRectangle & r)
   {
       w = r.w;
       h = r.h;
       nTotalNumber ++;
       nTotalArea += w * h;
   }
   ```
   
6. 成員物件和封閉類

   成員物件：一個類別的成員變數是另一個類的物件
   包含成員物件的類叫做封閉類(Enclosing)
   例如：
   ```c++
   class CTyre
   {
       private:
           int radius;   // 半徑
           int width;    // 寬度
       public:
           CTyre(int r, int w): radius(r), width(w)
           // 初始化列表，取代原本用'='賦值的風格
           {
           
           }
   };
   
   class CEgine
   {
   
   };
   
   class CCar    // 汽車類 -> "封閉類"
   {
       private:
           int price;    // 價格
           Ctyre tyre;
           CEngine engine;
       public:
           CCar(int p, int tr, int tw);
   };
   
   CCar::CCar(int p, int tr, int w): price(p), tyre(tr, w)
   {
   
   }
   
   int main()
   {
       CCar car(20000, 17, 225);
       return 0;
   }
   ```
   上述程式碼的CCar類別若不定義建構子，則
   ```c++
   CCar car; // error，會出錯
   ```
   compiler不知道car.tyre該如何初始化，
   而car.engine的初始化沒有問題：用默認建構子。
   
   生成封閉類物件的語句 $\Rightarrow$ 明確"物件中的成員對象" $\Rightarrow$ 如何初始化
   
   封閉類建構子的初始化列表
   * 定義封閉類的建構子時，添加「初始化列表」：
   ```
   類別名::建構子函數(參數表):成員變數1(參數表), 成員變數2(參數表), ...
   {
       ...
   }
   ```
   
   * 成員物件初始化列表中的參數
     - 任意複雜的表示式
     - 函數 / 變數 / 表示式中的函數，變數有定義
   
   * 呼叫順序
     當生成封閉類物件時，
     - Step 1: 執行所有成員物件的建構子
     - Step 2: 執行封閉類別的建構子
     
     成員物件建構子調用順序:star::star::star:：
     - 和成員物件在類別中的陳述順序一致
     - 與在成員初始化列表中出現的順序無關

     成員物件解構子調用順序：
     - Step 1: 先執行封閉類別的解構子
     - Step 1: 再執行成員函數的解構子
       i.e. 先構造的後析構，後構造的先析構

    以下一個例子完整說明：
    ```c++
    class CTyre
    {
        public:
            CTyre()
            {
                cout << "CTyre constructor" << endl;
            }
            ~CTyre()
            {
                cout << "CTyre destructor" << endl;
            }
    };
    
    class CEngine
    {
        public:
            CEngine()
            {
                cout << "CEngine constructor" << endl;
            }
            ~CEngine()
            {
                cout << "CEngine destructor" << endl;
            }
    };
    
    class CCar
    {
        private:
            CEngine engine;    // 第一個被建構
            CTyre tyre;        // 第二個被建構
        public:
            CCar()             // 第三個被建構
            {
                cout << "CCar constructor" << endl;
            }
            ~CCar()            // 解構順序與建構順序相反
            {
                cout << "CCar destructor" << endl;
            }
    };
    
    int main()
    {
        CCar car;
        return 0;
    }
    ```
    上述程式碼執行結果如下：
    ```
    CEngine constructor
    CTyre constructor
    CCar constructor
    CCar destructor
    CTyre destructor
    CEngine destructor
    ```

7. 友元(Friend)

   包含兩種類型：友元函數、友元類別
   
   友元函數：
   一個類別的friend可以訪問該類別的private成員
   例如：
   ```c++
   class CCar;    //提前宣告CCar類別，以便後面CDriver使用
   
   class CDriver
   {
       public:
           void ModifyCar(CCar * pCar);    //改裝汽車
   };
   
   class CCar
   {
       private:
           int price;
       // 以下兩者皆不屬於CCar的成員函數
       friend int MostExpensiveCar(CCar cars[], int total);    // 宣告友元
       friend void CDriver::ModifyCar(CCar * pCar);            // 宣告友元
   };
   
   void CDriver::ModifyCar(CCar * pCar)
   {
       pCar -> price += 1000; // 汽車改裝後價值增加
   }
   
   int MostExpensiveCar(CCar cars[], int total) // 求最貴的汽車價格
   {
       int tmpMax = -1;
       for(int i = 0; i < total; ++i)
       {
           if(cars[i].price > tempMax)
               tmpMax = cars[i].price
       }
       return tmpMax;
   }
   
   int main()
   {
       return 0;
   }   
   ```
   這方式使得一個類別的成員函數(包括建構子、解構子)成為另一個類別的友元。
   ```c++
   class B
   {
       public:
           void function();
   };
   
   class A
   {
       friend void B::function();
   };
   ```
   
   接著介紹友元類別：
   A是B的友元類 $\Rightarrow$ A的成員函數可以訪問B的private成員
   ```c++
    class CCar
    {
        private:
        int price;
        friend class CDriver;    //宣告CDriver為友元類別
     };
     
     class CDriver
     {
         public:
             CCar myCar;
             void ModifyCar()    //改裝汽車
             {
                 myCar.price += 1000; //CDriver是CCar的友元類別->可以訪問其私有成元
             }
     };
     
     int main()
     {
         return 0;
     }
   ```
   
   Note: 友元類別之間的關係不能傳遞、不能繼承

8. this指針

   歷史原因：原本的C++沒有自己的compiler，需要先翻譯成C
   以下為一個簡單的C++程式：
   ```c++
   class CCar
   {
       public:
           int price;
           void SetPrice(int p)
   };
   
   void CCar::SetPrice(int p)
   {
       price = p;
   }
   
   int main()
   {
       CCar car;
       car.SetPrice(20000);
       return 0;
   }
   ```
   上述程式碼可進行翻譯如下C語言程式碼：
   ```c
   struct CCar
   {
       int price;
   };
   
   void SetPrice(struct CCar * this, int p)
   {
       this -> price = p;
   }
   
   int main()
   {
       struct CCar car;
       SetPrice(& car, 20000);
       return 0;
   }
   ```
   其中最難的翻譯就是成員函數要怎麼翻譯？Ans: 翻譯成一個全域函數。
   
   this指針作用：
   其作用就是指向成員函數所作用的instance，
   非靜態的成員函數可以直接使用this來代表指向該函數作用的物件的指針。
   ```c++
   class Complex
   {
       public:
           double real, imag;
           void Print()
           {
               cout << real << "," << imag;
           }
           Complex(double r, double i): real(r), imag(i){}
           Complex AddOne()
           {
               this -> real ++;    // 等價於real++
               this -> Print();    // 等價於Print
               return * this;      // 此處依然會呼叫copy constructor
           }
   };
   
   int main()
   {
       Complex c1(1, 1), c2(0, 0);
       c2 = c1.AddOne();
       return 0;
   }    //輸出2, 1
   ```
   
   另一個例子：
   ```c++
   class A
   {
       int i;
       public:
           void Hello()
           {
               cout << "hello" << endl;
           }
   };
   
   int main()
   {
       A * p = NULL;    // 空指針
       p -> Hello();    // 結果會怎樣？
   }
   ```
   上述程式碼執行結果為：
   ```
   hello
   ```
   為什麼呢？因為```void Hello()```被compiler編譯成：
   ```c
   void Hello(A * this){cout << "hello" << endl;}
   --
   // 而p->Hello();被翻譯成
   Hello(p);
   ```

   注意：
   靜態成員函數中不能使用this指針！
   因為靜態成員函數並不具體作用於某個物件，
   因此，靜態成員函數的真實參數個數，就是程式碼中實際寫出的參數個數。
   
9. 常數物件、常數成員函數和常引用

   常數物件：
   如果不希望某個物件的值被改變，則定義該物件的時候可以在前面加const關鍵字。
   ```c++
   class Demo
   {
       private:
           int value;
       public:
           void SetValue(){}
   };
   const Demo Obj;    // 常數物件
   ```
   
   常數成員函數：
   在類別的成員函數陳述後面可以加const關鍵字，則該成員函數成為常量成員函數。
   
   常數成員函數執行期間不應該修改其所作用的物件。
   因此，在常數成員函數中不能修改成員變數的值(靜態成員變數除外)，
   也不能調用同類別的非常數成員函數(靜態成員函數例外)。
   
   ```c++
   class Sample
   {
       public:
           int value;
           void GetValue() const;
           void func(){};
           Sample(){}
       
   };
   
   void Sample::GetValue() const
   {
       vaule = 0;    // wrong，不能修改所作用的成員變數
       func();    // wrong，不能呼叫非常數成員函數
   }
   
   // 如果是在main函數中呢？
   int main()
   {
       const Sample o;
       o.value = 100; // error，常量對象不可以被修改
       o.func();    //error，常量對象上面不能執行非常亮成員函數
       o.GetValue();    // ok，常量對象上面可以執行常量成員函數
       return 0;
   }    // 在Dev C++中，要為Sample類別編寫無參數建構子才行
   ```
   
   常數成員函數的over loadding：
   兩個成員函數，名字和參數表都一樣，但是一個是const，算是重載而非重複定義。
   
   例子：
   ```c++
   class CTest
   {
       private:
           int n;
       public:
       CTest()
       {
           n = 1;
       }
       int GetValue() const
       {
           return n;
       }
       int GetValue()
       {
           return 2 * n;
       }
   };
   
   int main()
   {
       const CTest objTest1;
       CTest objTest2;
       cout << objTest1.GetValue() << "," << objTest2.GetValue();
       return 0;
   }
   ```
   上述程式碼輸出：
   ```
   1
   2
   ```
   i.e. 當重載發生時，編譯器依照該物件本身是否為const來決定何者出線
   
   常引用：
   物件作為函數的參數時，生成該參數需要調用複製建構子，效率比較低。
   用指針做參數，程式碼又不好看，如何解決
   
   可以利用物件的引用作為參數，如：
   ```c++
   class Sample
   {
       ...
   };
   
   void PrintfObj(Sample & o)
   {
       // 因為o不是object，所以不會引發copy constructor被呼叫，節省了時間
       ...
   }
   ```
   上述程式碼有一個問題，
   object引用最為函數的參數具一定風險性，若函數不小心修改了參數o，則caller的引數也跟著改變。這可能不是我們想要的，如何避免？
   Ans: 加上const當作常引用
   ```c++
   class Sample
   {
       ...
   };
   
   void PrintfObj(const Sample & o)
   {
       // 因為o不是object，所以不會引發copy constructor被呼叫，節省了時間
       ...
   }
   ```
   這樣就能確保函數中不會出現無意修改o的陳述句了。
   
---

## 第四週

1. 運算子重載的基本概念(operator overloading)

   如：+, -, , /, %, ^, ~, !, |, =, <<, >>, !=, ...
   只能用於基本的資料類型
   如：整數、實數、字元、布林值
   
   但因為C++提供user自己定義class
   呼叫class的member function $\Rightarrow$ 操作它的物件
   但不太方便，因此想用數學上的符號一樣。
   
   對抽象資料結構也能夠直接使用C++提供的運算子
   * 程式碼更簡潔
   * 程式碼更容易理解
   例如：
   complex_a和complex_b是兩個複數物件
   求兩複數之和，希望能直接寫成```complex_a + complex_b```
   
   運算子重載：
   * 對已有的運算子賦予多重含意
   * 使同一運算子作用於不同類型的資料時 $\Rightarrow$ 不同類型的行為
   
   目的：
   擴展C++中提供的operatpor的適用範圍，以用於class所表示的抽象資料結構
   
   同一個運算子，對不同類型的運算元所發生的行為不同
   ```
   (5, 10i) + (4, 8i) = (9, 18i)
   5 + 4 = 9
   ```
   
   運算子重載的本質是函數重載：
   ```
   返回值類型 operator 運算子(參數表)
   {
       ...
   }
   ```
   在程式碼編譯時，首先把含運算子的表示式 $\Rightarrow$ 對運算子函數的調用
   再把運算子的運算元 $\Rightarrow$ 運算子函數的引數
   運算子被多次重載時，根據參數的類型決定呼叫哪個運算子函數
   運算子可以被重載成普通函數，也可以被重載成類別的成員函數
   
   重載為普通函數的例子：
   ```c++
   class Complex
   {
       public:
           Complex(double r = 0.0, double i = 0.0)
           {
               real = r;
               imaginary = i;
           }
           
       private:
           double real;    // 實部
           double imaginary;    // 虛部
   };
   
   Complex operator+(const Complex & a, const Complex & b) // 重載"+"號
   {
       return Complex(a.real + b.real, a.imaginary + b.imaginary)
   }    // "類別名(參數表)"就代表一個物件
   
   Complex a(1, 2), b(2, 3), c;
   c = a+b;    // 相當於調用operator+(a, b)
   ```
   重載為普通函數時，參數個數為運算元數
   
   而當運算子重載為成員函數時：
   ```c++
   class Complex
   {
       public:
           Complex(double r = 0.0, double i = 0.0):
               real(r), imaginary(i){}
           Complex operator+(const Complex &);    // addition
           Complex operator-(const Complex &);    // subtraction
       private:
           double real;    // 實部
           double imaginary;    // 虛部
   };
   
   // Overloaded addition operator
   Complex Complex::operator+(const Complex & operand2)
   {
       return Complex(real + operand2.real, imaginary + operand2.imaginary);
   }
   
   Complex Complex::operator-(const Complex & operand2)
   {
       return Complex(real - operand2.real, imaginary - operand2.imaginary);
   }
   
   int main()
   {
       Complex x, y(4.3, 8.2), z(3.3, 1.1);
       x = y + z;    // 相當於x = y.operator+(z);
       x = y - z;    // 相當於x = y.operator-(z);
       return 0;
   }
   ```
   重載為成員函數時，參數個數為運算元減1
   
2. 賦值運算子'='的重載
   
   賦值運算子兩邊的類型可以不匹配
   如：把一個int變數賦值給一個Complex物件、把一個char *類型的字串賦值給一個字串物件
   
   需要重載賦值運算子'='
   但賦值運算子只能重載為「成員函數」
   (不可重載為普通函數)
   
   編寫一個長度可辨的字串類string
   包含一個char *類型的成員變數
   $\Rightarrow$ 指向動態分配的儲存空間
   該儲存空間用於存放'\0'結尾的字串
   ```c++  
   class String
   {
       private:
           char * str;
       public:
           String(): str(NULL){}    // constructor，初始化str為NULL
           const char * c_str(){return str;}
           void operator=(const char * s);    //重載運算子'='
           ~String();
   };
   
   // 重載'='使得obj = "hello"能夠成立
   void String::operator=(const char * s)
   {
       if(str) delete [] str;
       if(s)
       {
           str = new char[strlen(s) + 1]; // +1是為了放'\0'
           strcpy(str, s);
       }
       else
           str = NULL;
   }
   
   String::~String()
   {
       if(str)
           delete [] str;
   }
   
   int main()
   {
       String s;
       s = "Good Luck,";
       cout << s.c_str() << endl;
       // String s2 = "hello!"; // 這條陳述會出錯
       s = "Shenzhou 8!";
       cout << s.c_str() << endl;
       return 0;
   }
   ```
   
   關於淺複製和深複製(Shallow copy and deep copy)：
   ```S1 = S2```
   * 淺複製/淺拷貝：
     執行逐個byte的複製工作
     例如：
     ```c++
     Mystring S1, S2;
     S1 = "this";
     S2 = "that";
     S1 = S2;    // s1.str = s2.str，兩者指向相同內存空間
     ```
     上述S1=S2只是逐byte的將S2的「指針值」複製給了S1
     可能導致S1和S2生命週期結束時，被delete兩次(double free)
   
   * 深複製/深拷貝：
     希望能夠將一個對象中指針變數指向的「內容」$\Rightarrow$複製到另一個對象中指針成員指向的地方
     
     如何實現？
     在class MyString裡添加成員函數：
     ```c++
     String & operator=(const String & s)
     {
         if(str) delete [] str;
         str = new char[strlen(s.str) + 1];
         strcpy(str, s.str);
         return * this;
     }
     ```
     
     這樣夠完善了嗎？考慮下面陳述句：
     ```c++
     Mystring s;
     s = "Hello";
     s = s;
     ```
     這樣會發生內容已經被```if(str) delete [] str;```給刪掉，但是卻要```strcpy```複製不知從哪來的內容的情形。
     正確寫法：
     ```c++
     // 添加此行在開頭處
     String & operator=(const String & s)
     {
         if(str == s.str) return * this;
         if(str) delete [] str;
         
         if(s.str)    // s.ste不為NULL才拷貝
         {
             str = new char[strlen(s.str) + 1];
             strcpy(str, s.str);
             return * this;
         }
         else
             str = NULL
         return * this;
     }
     ```
     
     另外，對於operator=的返回值，
     將其設定為void好不好？
     如之前的程式碼：
     ```c++
     void String::operator=(const char * s)
     ```
     考慮一情形：
     ```
     a = b = c
     // 等價於a.operator=(b.operator=(c));
     ```
     此處b.operator=沒有返回值，陳述句發生error。
     那麼，改成String好不好呢？
     應該改為
     ```c++
     // 重載'='使得obj = "hello"能夠成立
     String & String::operator=(const char * s)
     {
         if(str) delete [] str;
         if(s)
         {
             str = new char[strlen(s) + 1];
             strcpy(str, s);
         }
         else
             str = NULL;
         return * this;
     }
     ```
     為什麼要寫成String &
     Ans: 習慣用法
     
     運算子重載時，好的風格 -- 「盡量保留運算子原本的特性」
     考慮：
     ```c++
     (a = b) = c;    // 會修改a的值
     ```
     其中(a=b)相當於是a的引用(&a)，也就是(a.operator=(b))
     如下，等價於：
     ```c++
     (a.operator=(b)).operator=(c);
     ```
     
     這樣String類別是否就沒問題了呢？
     為String類別編寫copy constructor時，會面臨和'='相同的問題，須用相同的方法處理。
     如：
     ```c++
     String s1;
     s1 = "Hello";
     String s2(s1);
     ```
     相當於s2=s1的淺拷貝，因此同樣要重新設計複製建構子：
     ```c++
     String::String(String & s)
     {
         if(s.str)
         {
             str = new char[strlen(s.str) + 1];
             strcpy(str, s.str);
         }
         else
             str = NULL;
     }
     ```
     
     以下是可以在電腦上正確執行的完整程式碼：
     ```c++
     #include <iostream>
     #include <string.h>
     using namespace std;
     class String
     {
         private:
             char * str;
         public:
             String(): str(NULL){}    // constructor，初始化str為NULL
	         String(String & s);	 // 定義copy constructor避免進行shallow copy
             const char * c_str(){return str;}
             String & operator=(const char * s);    //重載運算子'='
             ~String();
     };

     String::String(String & s)
     {
         if(s.str)
         {
             str = new char[strlen(s.str) + 1];
             strcpy(str, s.str);
         }
         else
             str = NULL;
     }

     // 重載'='使得obj = "hello"能夠成立
     String & String::operator=(const char * s)
     {
         if(str) delete [] str;
         if(s)
         {
             str = new char[strlen(s) + 1];
             strcpy(str, s);
         }
         else
             str = NULL;
         return * this;
     }

     String::~String()
     {
         if(str)
             delete [] str;
     }

     int main()
     {
         String s;
         s = "Good Luck,";
         cout << s.c_str() << endl;
         // String s2 = "hello!"; // 這條陳述會出錯
         s = "Shenzhou 8!";
         cout << s.c_str() << endl;
         String s2(s);
         cout << s.c_str() << endl;
         return 0;
     }
     ```
     
3. 運算子重載為友元函數
   
   通常，將運算子重載為class的成員函數
   重載為友元函數的情況：
   * 成員函數不能滿足使用要求
   * 普通函數，又不能訪問類別的私有成員
   具體看一下：
   ```c++
   class Complex
   {
       double real, imag;
       public:
           Complex(double r, double i):real(r), imag(i){};
           Complex operator+(double r);
   };
   
   Complex Complex::operator+(double r)    //能解釋c+5
   {
       return Complex(real + r, imag);
   }
   ```
   上述程式碼可以讓```c = c + 5;```成立，
   但是：
   ```c++
   c = 5 + c; // 編譯會出錯！
   ```
   原因在於：
   ```c++
   c = c + 5; // 相當於c = c.operator+(5)
   --
   c = 5 + c; // 5.operator+()沒有關於Complex的多載
   ```
   為了使上述表示式能成立，需要將+重載為普通函數：
   ```c++
   Complex operator+(double r, const Complex & c)
   {
       return Complex(c.real +r, c.imag);
   }
   ```
   此時才能解釋5+c，但相對應的real和imag是private的，必須利用friend：
   ```c++
   class Complex
   {
       double real, imag;
       public:
           Complex(double r, double i):real(r), imag(i){};
           Complex operator+(double r);
           friend Complex operator+(double r, const Complex & c);    // 新增此行
   };
   ```
   
4. 實例 -- 可變動的整數陣列
   ```c++
   int main() // 要編寫可變長度的整數陣列，使之能如下使用
   {
       CArray a; // 剛開始的array是空的
       for(int i = 0; i < 5; ++i)
           a.push_back(i);    // (1)
       CArray a2, a3;
       a2 = a;    // (2)
       for(int i = 0; i < a.length(); ++i)
           cout << a2[i] << " ";    // (3)
       a2 = a3; //a2是空的
       for(int i = 0; i < a2.length(); ++i) // a2.length()返回0
           cout << a2[i] << " ";
       cout << endl;
       a[3] = 100;
       CArray a4(a);    // (4)
       for(int i = 0; i < a4.length(); ++i)
           cout << a4[i] << " ";
       return 0;
   }
   ```
   上述程式碼執行之結果需為：
   ```
   0 1 2 3 4
   0 1 2 100 4
   ```
   需要做哪些事情？
   (1) 要用動態分配的內存來存放陣列元素，需要一個指針成員變數。
   (2) 要重載'='
   (3) 要重載"[ ]"
   (4) 要自己重新寫一個copy constructor
   
   以下程式碼示範如何編寫CArray：
   ```c++
   class CArray
   {
       int size; // 陣列元素的個數
       int *ptr; // 指向動態分配的陣列
       public:
           CArray(int s = 0);    // s代表陣列元素個數
           CArray(CArray & a);
           ~CArray();
           void push_back(int v);    // 用於在陣列尾部添加一個元素v
           CArray & operator=(const CArray & a); // 用於陣列物件之間的賦值
           int length()    // 返回陣列元素的個數
           {
               return size;
           }
           int & operator[](int i) //返回值不可為int，因為不支持a[i]=4這個目的
           {
               // 用以支持根據下標訪問陣列元素
               // 如n = a[i]和a[i] = 4這樣的陳述句
               return ptr[i];
           }
   };
   
   CArray::CArray(int s): size(s)
   {
       if(s == 0)
           ptr == nullptr;
       else
           ptr = new int[s];
   }
   
   CArray::CArray(CArray & a)
   {
       if(!a.ptr)
       {
           ptr = nullptr;
           size = 0;
           return;
       }
       ptr = new int[a.size];
       memcpy(ptr, a.ptr, sizeof(int) * a.size);
       size = a.size;
   }
   
   CArray::~CArray()
   {
       if(ptr) delete [] ptr;
   }
   
   CArray & CArray::operator=(const CArray & a)
   {
       // 賦值記號的作用是使'='左邊物件裡存放的陣列，大小和內容都和右邊的物件一樣
       if(ptr == a.ptr)    // 防止a=a這樣的賦值導致出錯
           return * this;
           
       if (!a.ptr)    // 如果a裡面的陣列是空的
       {
           if(ptr) delete [] ptr;
           ptr = nullptr;
           size = 0;
           return * this;
       }
       
       if(size < a.size)    // 如果原有空間夠大，就不用分配新的空間，節省時間
       {
           if(ptr)
               delete [] ptr;
           ptr = new int[a.size];
       }
       memcpy(ptr, a.ptr, sizeof(int) * a.size);
       size = a.size;
       return * this;
   }
   
   void CArray::push_back(int v)    // 在array尾部添加一個element
   {
       if(ptr)
       {
           int * tmpPtr = new int[size + 1];    // 重新分配空間
           memcpy(tmpPtr, ptr, sizeof(int) * size);    // 拷貝原陣列內容
           delete [] ptr;
           ptr = tmpPtr;
       }
       else    // 陣列原本是空的
           ptr = new int[1]
       ptr[size++] = v;    // 加入新的陣列元素
   }
   /*此種push back方法比較沒有效率，如果一次分配足夠多的空間，
   等需要時再取出來用，會比較有效率，跟stl中vector的實作方法一樣*/
   ```
   
5. 串流輸入運算子和串流輸出運算子

   問題：```cout << 5 << "this"```為什麼能夠成立
   cout又是什麼？ "<<"(左移)為什麼能用在cout上
   
   Ans: cout是在iostream標頭檔中定義的，ostream類別的物件。
   
   "<<"能用在cout上是因為在iostream對"<<"進行了over loading。
   
   考慮，怎麼重載才能使得```cout << 5```和```cout << "this"```都能成立？
   
   假設我們把"<<"重載成ostream類別的成員函數：
   ```c++
   void ostream::operator<<(int n)
   {
       ... //輸出n的代碼
       return;
   }
   ```
   ```cout << 5;```即cout.operator<<(5)
   ```cout << "tihs";```即cout.operator<<("this")
   
   怎麼重載才能使得```cout << 5 << "this"```成立？
   需考慮返回值，如下：
   ```c++
   ostream & ostream::operator<<(int n)
   {
       ... //輸出n的代碼
       return * this;
   }
   
   ostream & ostream::operator<<(const char * s)
   {
       ... //輸出n的代碼
       return * this;
   }
   ```
   所以串流輸入運算子的本質就是
   ```c++
   cout.operator<<(5).operator<<("this");
   ```
   
   假定下面程式碼質行會輸出5hello，該補寫些什麼？
   ```c++
   class CStudent
   {
       public:
           int n Age;
   };
   
   int main()
   {
       CStudent s;
       s.nAge = 5;
       cout << s << "hello";
       return 0;
   }
   ```
   顯然需要重載"<<"運算子：
   ```c++
   ostream & operator<<(ostream & o, const CStudent & s)
   {
       o << s.Age;    // o其實就是cout
       return o;
   }
   ```
   
   另一個例題：
   
   假定c是Complex複數類別的物件，現在希望寫```cout << c;```就能以"a + bi"的形式輸出c的值，寫```cin >> c;```就能從鍵盤接受"a + bi"形式的輸入，並且使得```c.real = a, c.imag = b```，怎麼做？
   
   ```c++
   int main()
   {
       Complex c;
       int n;
       cin >> c >> n;
       cout << c << ", " << n;
       return 0;
   }
   ```
   希望執行結果可以如下：
   ```
   13.2+133i, 87 (input)
   13.2+133i, 87 (output)
   ```
   
   作法如下：
   ```c++
   #include <iostream>
   #include <string>
   #include <cstdlib>
   
   using namespace std;
   
   class Complex
   {
       double real, imag;
       public:
           Complex(double r = 0, double i = 0): real(r), imag(i){};
           friend ostream & operator<<(ostream & os, const Complex & c);
           friend istream & operator>>(istream & is, Complex & c);
   };
   
   ostream & operator<<(ostream & os, const Complex & c)
   {
       os << c.real << "+" << c.imag << "i";    // 以"a+bi"的形式輸出
       return os;
   }
   
   istream & operator>>(istream & is, Complex & c)
   {
       string s;
       is >> s;    // 將"a+bi"作為字串讀入，"a+bi"中間不能有空格，is其實就是cin
       int pos = s.find("+", 0);
       string sTmp = s.substr(0, pos);    // 分離出代表實部的字串
       c.real = atof(sTmp.c_str());
       // atof函數能將const char *指針指向的內容轉換成float
       sTmp = s.substr(pos + 1, s.length() - pos - 2);
       // 分離出代表虛部的字串
       c.imag = atof(sTmp.c_str());
       return is;
   }
   ```
   
6. 遞增/遞減運算子的重載
   
   自增++和自減--運算子有前置和後置之分
   前置：
        ++i和--i
   後置：
        i++和--
   
   前置運算子作為一元運算子重載：
   * 重載為成員函數：
     T operator++();
     T operator--();

   * 重載為全域函數：
     T operator++(T);
     T operator--(T);
     
   ++ obj、obj.operator++()、operator++(obj)都呼叫上述函數。
   
   後置運算子作為二元運算子重載：
   多寫一個int參數，具體其實無意義(單純規定！通常編譯器自動初始化為0):star::star::star:
   用以區分前置後置而已。
   * 重載成為成員函數：
     T operator++(int);
     T operator--(int);
     
   * 重載成為成員函數：
     T operator++(T, int);
     T operator--(T, int);
     
   obj++, obj.operator++(0), operator++(obj, 0)都調用上述函數。
   
   ```c++
   int main()
   {
       CDemo d(5);
       
       // 題目要求++寫成member function
       cout << (d++) << ", "; // 等價於d.operator++(0);
       cout << d << ",";
       cout << (++d) << ", "; // 等價於d.operator++();
       cout << d << endl;
       // 題目要求--寫成global function
       cout << (d--) << ", "; // 等價於operator--(d, 0)
       cout << d << ", ";
       cout << (--d) << ","; // 等價於operator--)(d)
       cout << d << endl;
       return 0;
   }
   ```
   上述程式碼輸出結果為：
   ```
   5, 6, 7, 7
   7, 6, 5, 5
   ```
   請問如何編寫CDemo？
   答案如下：
   ```c++
   class CDemo
   {
       private:
           int n;
       public:
           CDemo(int i = 0): n(i){}
           CDemo operator++();    // 用於前置形式
           CDemo operator++(int);    // 用於後置形式
           operator int()    // 類型轉換運算子
           {
               return n;
           }
           friend CDemo operator--(CDemo &);
           friend CDemo operator--(CDemo &, int);
   };
   
   CDemo CDemo::operator++()
   {
       n++;
       return * this;
   }
   
   CDemo CDemo::operator++(int k)
   {
       CDemo tmp(*this);    // 紀錄修改前的物件
       n++;
       return tmp;    // 返回修改前的物件
   }
   
   CDemo operator--(CDemo & d)
   {
       d.n--;
       return d;
   }
   
   CDemo operator--(CDemo & d, int k)
   {
       CDemo tmp(d);
       d.n--;
       return tmp;
   }
   ```
   
   上述程式碼中，int為一個轉型運算子(cast operator)被重載：
   ```c++
   operator int() {return n;}
   
   --
   
   Demo s;
   (int)s;    // 等價於s.int();
   ```
   當編譯器遇到```cout << (CDemo類型物件)```時，
   會自動尋找可以轉型為ostream可接受型別之轉型運算子以利印出，所以不一定要覆寫int()，
   其實對operator char *()進行overload也可以。如下：
   ```c++
   class CDemo
   {
       private:
           int n;
           char s[1]; // 故意新增的，因為轉型運算子不接受回傳local variable之參考
       public:
           CDemo(int i = 0): n(i){}
           CDemo operator++();
           CDemo operator++(int);
           operator char *()    // 類型轉換運算子
           {
               sprintf(s, "%d", n);
               return s;
           }
       friend CDemo operator--(CDemo &);
       friend CDemo operator--(CDemo &, int);
   };
   ```
   
   
   轉型運算子重載時，不能寫返回值類型，
   實際上其返回值類型就是轉型運算子代表的類型。
   
   運算子重載的注意事項:star::star::star:
   * C++不允許定義新的運算子
   * 重載後的運算子含意應該符合日常習慣
     - complex_a + complex_b
     - word_a > word_b
     - date_b = date_a + n
   * 運算子重載不改變運算子的優先程度(priority)
   * 以下運算子不能被重載：
     '.', ".*", "::", ""?:", sizeof
   * 重載運算子( )、[ ]、->或者賦值運算子'='時，重載函數必須宣告為類別的「成員函數」

---

## 第五週

1. 繼承和衍生
   
   繼承：在定義一個新的類別B時，如果該類別與某個已有的類別A相似(指的是B擁有A的全部特性)，那麼就可以把A作為一個基礎類別(base class，又稱作父類別superclass)，而把B作為此基礎之上的一個衍生類別(derived class，也稱為子類別subclass)。
   
   * 衍生類別是通過對基礎類別進行修改和擴充得到的。在衍生類別中，可以擴充新的成員變數和成員函數。
   * 衍生類別一經定義後，可以獨立使用，不依賴於基礎類別。
   * 衍生類別擁有基礎類別的全部成員函數和成員變數，不論是private、protected、public
     - 在衍生類別的各個成員函數中，不能訪問基礎類別的private成員

   需要繼承機制的例子：
   ```
   所有的學生都有的共同屬性：
       姓名
       學號
       性別
       成績
       
   所有學生的有的共同方法(成員函數)：
       是否該留級
       是否該獎勵
   ```

   而不同的學生，又有各自不同的屬性和方法：
   ```
       研究生：
           導師
           系所
       大學生：
           系所
       中學生：
           競賽專長加分
   ```

    實際寫成class：
    ```c++
    class CStudent
    {
        private:
            string sName;
            int nAge;
        public:
            bool IsThreeGood(){};
            void SetName(const string & name)
            {
                sName = name;
            }
            // ......
    };
    
    class CUndergraduateStudent: public CStudent
    {
        private:
            int nDepartment;
        public:
            bool IsThreeGood(){...}    // 覆蓋
            bool CanBaoYan(){...}
    };
    
    class CGraduatedStudent: public CStudent
    {
        private:
            int nDepartment;
            char szMentorName[20];
        public:
            int CountSalary(){...};
    }
    
    // 繼承的寫法是：
    //    類別名：public 基礎類別名
    ```
    
    衍生類別物件的體積，等於基礎類別物件的體積，再加上衍生類別物件自己的成員變數體積之和。在衍生類別物件中，包含著基礎類別物件，而且基礎類別物件的儲存空間位於衍生類別物件新增的成員變數「之前」。
    
    ```c++
    class CBase
    {
        int v1, v2;
    };
    
    class CDerived: public CBase
    {
        int v3;
    }
    ```
    
    則CDerived的存放空間中，v1、v2會在v3之前。
    
    | adress | 0x0 | 0x1 | 0x2 |
    | ------ | --- | --- | --- |
    | var    | v1  | v2  | v3  |
    
    學生管理的具體實現：
    ```c
    #include <iostream>
    #include <string>
    
    using namespace std;
    
    class CStudent
    {
        private:
            string name;
            string id;
            char gender;
            int age;
        public:
        void PrintInfo();
        void SetInfo(const string & name_, const string & id_, int age_, char gender_);
        string GetName(){return name;}
    };
    
    class CUndergaduateStudent: public CStudent    // 本科生類別，繼承了CStudent
    {
        private:
            string department;
        public:
            void QualifiedForBaoyan()    // 給予保研資格
            {
                cout << "qualified for baoyan" << endl;
            }
            void PrintInfo()
            {
                CStudent::PrintInfo();    // 調用基礎類別的PrintInfo
                cout << "Department: " << department << endl;
            }
            void SetInfo(const string & name_, const string & id_, int age_, char gender_, const string & department)
            {
                CStudent::SetInfo(name_, id_, age_, gender_);    // 調用基礎類別的SetInfo
                department = department_;
            }
    };
    
    int main()
    {
        CUndergraduateStudent s2;
        s2.SetInfo("Harry Potter ", "118829212", 19, 'M', "Computer Science");
        cout << s2.GetName() << " ";
        s2.QualifiedForBaoyan();
        s2.PrintInfo();
        return 0;
    }
    ```
    
    上述程式碼執行結果：
    ```
    Harry Potter qualified for baoyan
    Name:Harry Potter
    ID:118829212
    Age:19
    Gender:M
    Department:Computer Science
    ```
    
2. 複合關係和繼承關係
   
   繼承："是"關係
   * B是基礎類別A的衍生類別
   * 邏輯上要求：「一個B物件也是一個A物件」。
   例如： 「中學生」是「學生」的一種
   
   複合："有"關係
   * 類別C中「有」成員變數k，k是類別D的物件，則C和D是複合關係。
   * 一般邏輯上要求：「D物件是C物件的固有屬性或組成部分」

   繼承關係的使用：
   寫了一個CMan類別代表男人
   後來又發現需要一個CWoman來代表女人
   
   CWoman類別和CMan類別有共同之處
   就讓CWoman類別從CMan類別衍生而來，是否合適？
   Ans: 不合理！
   
   好的做法應該概括男人和女人的共同特性，寫一個CHuman代表人，然後CMan和CWowman都從它繼承而來。
   
   複合關係的使用：
   幾何形體的程式中，需要寫「點」類別，也需要「圓」類別
   ```c++
   class CPoint
   {
       double x, y;
   };
   
   class CCircle: public Cpoint
   {
       double r;
   };
   
   // 兩者之間是繼承關係
   ```
   
   以上例子中，圓是點的繼承者，然而圓並不是一個點，此關係並不合理！
   正確做法：
   使用「複合關係」
   
   ```c++
   class CPoint
   {
       double x, y;
       friend class CCircle;
   };
   
   class CCircle
   {
       double r;
       CPoint center;
   };
   
   // 用CPoint當成CCircle的圓心
   ```
   
   如果要寫一個小區養狗管理程序：
   需要寫一個「業主」類，還需要寫一「狗」類。
   而狗是有主人的，主人當然是業主(假定狗只有一個主人，但一個業主可以有最多10條狗)
   
   ```c++
   class CDog;
   
   class CMaster
   {
       CDog dogs[10];
   };
   
   class CDog
   {
       CMaster m;
   };
   ```
   
   以上的做法：完全錯誤！
   
   為何：因為「循環定義」！
   
   例如：
   CMaster裡包含10個CDogs，但CDogs裡有一個CMaster，那無法得知一個CMaster總共要佔幾個Byte
   
   另一種寫法：
   為「狗」類別設置一個「業主」類的成員物件；
   為「業主」類別設置一個「狗」類別的物件指針陣列。
   
   ```c++
   class CDog;
   
   class CMaster
   {
       CDog * dogs[10];
   };
   
   class CDog
   {
       CMaster m;
   };
   ```
   
   雖然此時已經可以計算出每個物件應該佔多少記憶體大小，但此種寫法依然錯誤！
   兩隻狗的主人有可能是同一個主人，如何維護不同的狗若有相同的主人，訊息需保持一致。
   例如：
   修改狗1裡面的M1類別的屬性，那狗2若也具有相同主人M1，則狗2裡的M1也需要同時變動。
   
   湊合的寫法：
   為「狗」類別設置一個「業主」類別的物件指針，
   為「業主」類別設置一個「狗」類別的物件陣列。
   
   ```c++
   class CMaster;
   
   class CDog
   {
       CMaster * pm;
   };
   
   class CMaster
   {
       CDog dogs[10];
   };
   ```
   
   勉強能用，但不好！
   複合關係通常要求成員物件是物件的固有屬性，然而「狗」並不是組成「主人」的一部分。
   另外，狗的操作必須透過主人來進行，狗會失去自由！
   
   正確的寫法如下：
   為「狗」類別設一個「業主」的物件指針；
   為「業主」類別設一個「狗」類別的物件「指針」陣列。
   
   ```c++
   class CMaster;
   
   class CDog
   {
       CMaster * pm;
   };
   
   class CMaster
   {
       CDog * dogs[10];
   };
   
   // 主人和狗之間是用指針互指的關係
   ```
   
3. 基礎類別/衍生類別同名成員和protected範圍解析運算子

   bass和derived擁有相同成員的情況：
   ```c++
   class base
   {
       int j;
       public:
           int i;
           void func();
   };
   
   class derived: public base
   {
       public:
           int i;
           void access();
           void func();
   };
   
   ```
   
   兩者都擁有int i和func()，
   在使用上會遇到一些問題：
   
   ```c++
   void dervided::access()
   {
       j = 5;    // error
       i = 5;    // 引用的是衍生類別的i
       base::i = 5;    // 引用的是基礎類別的i
       func();    // 引用的是衍生類別的
       base::func();    // 基礎類別的
   }
   
   derived obj;
   obj.i = 1;    // 衍生類別的i
   obj.basee::i = 1;    // 基礎類別的i
   ```
   
   obj占用的存儲空間：
   | adress | 0x0     | 0x1     | 0x2 |
   | ------ | ------- | ------- | --  |
   | var    | Base::j | Base::i | i   |
   
   範圍解析運算子(訪問範圍說明符)：

   基礎類別的private成員：可以被下列函數訪問
   * 基礎類別的成員函數
   * 基礎類別的友元函數

   基礎類別的public成員：可以被下列的函數訪問
   * 基礎類別的成員函數
   * 基礎類別的友元函數
   * 衍生類別的成員函數
   * 衍生類別的友元函數
   * 其他的函數

   基礎類別的protected成員：可以被下列的函數訪問
   * 基礎類別的成員函數
   * 基礎類別的友元函數
   * 衍生類別的成員函數(可以訪問當前物件的基礎類別的保護成員)
   具體如下：
   ```c++
   class Father
   {
       private:
           int nPrivate;
       public:
           int nPublic;
       protected:
           int nProtected;
   };
   
   class Son: public Father
   {
       void AccessFather()
       {
           nPublic = 1;    // ok
           nPrivate = 1;    // wrong
           nProtected = 1;    //ok，訪問從基礎類別繼承的protected成員
           Son f;
           f.nProtected = 1;    // wrong，f不是「當前」物件
       }
   };
   ```
   也就是說，AccessFather()裡能被訪問的nProtected，只能是「當前正在執行AccessFather()的物件」所繼承的nProtected，意即無法透過「類別名.屬性名」去存取。
   
4. 衍生類別的建構子
   
   衍生類別的物件包含基礎類別的物件
   執行衍生類的建構子之前，先執行基礎類別的建構子
   衍生類別交代基礎類別初始化，具體形式：
   ```
   建構子名稱(參數表): 基礎類別名稱(基礎類別建構子參數表)
   {
       ......
   }
   
   ```
   
   例子：
   ```c++
   class Bug
   {
       private:
           int nLegs;
           int nColor;
       public:
           int nType;
           Bug (int legs, int color);
           void PrintBug(){};
   };
   
   class FlyBug: public Bug
   {
       int nWings;
       public:
           FlyBug(int legs, int color, int wings);
   };
   
   Bug::Bug(int legs, int color): nLegs(legs), nColor(color){}
   
   FlyBug::FlyBug(int legs, int color, int wings): Bug(legs, color)
   {
       nType = 1; // ok
       nWings = wings;
   }
   
   int main()
   {
       FlyBug fb(2, 3, 4);
       fb.PrintBug();
       fb.nType = 1;
       fb.nLegs = 2;    // error.nLegs is private
       return 0;
   }
   ```
   
   在創建衍生類別的物件時：
   * 需要呼叫基礎類別的建構子：
     初始化衍生類別物件中從基礎類別繼承的成員
   * 在執行一個衍生類別的建構子之前：
     總是先執行基礎類別的建構子
     
   調用基礎類別建構子函數的兩種方式：
   * 顯式方式：
     衍生類別的建構子 $\Rightarrow$ 基礎類別的建構子提供參數
     ```derived::derived(arg_derived-list): base(arg_base-list)```
   * 隱式方式：
     衍生類別的建構子中，省略基礎類別建構子時
     衍生類別的建構子，自動呼叫基礎類別的默認建構子
     
   衍生類別的解構子被執行時，執行完衍生類別的解構子後，自動呼叫基礎類別的解構子。
   
   ```c++
   class Base
   {
       public:
           int n;
           Base(int i): n(i)
           {
               cout << "Base " << n << "constructed" << endl;
           }
           ~Base()
           {
               cout << "Base " << n << "destructed" << endl;
           }
   };
   
   class Derrived: public Base
   {
       public:
           Base(int i): Base(i)
           {
               cout << "Derived constructed" << endl;
           }
           ~Derived()
           {
               cout << "Derived destructed" << endl;
           }
   };
   
   int main()
   {
       Derived Obj(3);
       return 0;
   }
   ```
   
   上述程式碼執行結果如下：
   ```
   Base 3 constructed
   Derived constructed
   Derived destructed
   Base 3 destructed
   ```
   
   另外，包含成員物件的衍生類別的建構子：
   ```c++
   class Skill
   {
       public:
           Skill(int n){}
   };
     
   class FlyBug: public Bug
   {
       int nWings;
       Skill sk1, sk2;
       public:
           FlyBug(int legs, int color, int wings);
   };

   FlyBug::FlyBug(int legs, int color, int wings): Bug(legs, color), sk1(5), sk2(color)
   // 表示式中可以出現：FlyBug建構子的參數，常量
   {
       nWings = wings;
   }
   ```
   
   創建衍生類別的物件時，執行衍生類別的建構子之步驟：
   1. 調用基礎類別的建構子
   $\Rightarrow$ 初始化衍生類別物件中從基礎類別繼承的成員
   2. 調用成員物件的建構子
   $\Rightarrow$ 初始化衍生類別物件中之成員物件
   3. 其他衍生類別自己擁有的屬性

   執行完衍生類別之解構子後：
   1. 呼叫成員物件類別的解構子
   2. 呼叫基礎類別的解構子

   i.e 解構子和建構子的調用順序正好相反
   
5. public繼承的賦值兼容規則
   
   ```c++
   class base{};
   class dervied: public base{};    // 公有派生
   base b;
   dervied d;
   ```
   (1) 衍生類別物件可以賦值給基礎類別物件
       ``` b = d; ```
   (2) 衍生類別物件可以初始化基礎類別引用
       ``` base & br = d; ```
   (3) 衍生類別物件的地址可以賦值給基礎類別指針
       ``` base * pb = & d; ```
   以上三點成立，原因在於「衍生類別」物件就是一個「基礎類別」物件
   衍生類別中的儲存空間本來就是把基礎類別物件中之內容放在記憶體最前面
   但反過來不行，或是用private或protected來繼承，也不行
   
   直接基礎類別與間接基礎類別：
   類A衍生類B，類B衍生類C，類C衍生類D，......
   - 類A是類B的直接基礎類別
   - 類B是類C的直接基礎類別，類A是類C的間接基礎類別
   - 類C是類D的直接基礎類別，類A、B是類D的間接基礎類別

   在宣告一個衍生類別時，只需要列出它的值接基礎類別
   - 衍生類別沿著類的層次自動向上繼承它的間接基礎類別
   - 衍生類別的成員包括：
     * 衍生類別自己定義的成員
     * 直接基礎類別的所有成員
     * 所有間接基礎類別的全部成員

   多層繼承的實際例子：
   ```c++
   class Base
   {
       public:
           int n;
           Base(int i)n(i)
           {
               cout << "Base " << n << "constructed" << endl;
           }
           ~Base()
           {
               cout << "Base" << n << "destructed" << endl;
           }
   };
   
   class Derived: public Base
   {
       public:
           Derived(int i): Base(i)
           {
               cout << "Derived constructed" << endl;
           }
           
           ~Derived():
           {
               cout << "Derived destructed" << endl;
           }
   };
   
   class MoreDerived: public Derived
   {
       public:
           MoreDerived(): Derived(4)
           {
               cout << "More Derived constructed" << endl;
           }
           
           ~MoreDerived():
           {
               cout << "More Derived destructed" << endl;
           }
   };
   
   int main()
   {
       MoreDerived Obj;
       return 0;
   }
   ```
   
   以上程式碼執行結果：
   ```
   Base 4 constructed
   Derived constructed
   More Derived constructed
   More Derived detructed
   Derived detructed
   Base 4 detructed
   ```
   
---

## 第六週

1. 虛擬函數和多型(virtual function & polymorphism，也叫多態)
   
   在類別的定義中，前面有virtual關鍵字的成員就是虛擬函數。
   ```c++
   class base
   {
       virtual int get();
   };
   
   int base::get(){};
   ```
   - virtual關鍵字只用在類別定義裡的函數宣告中，寫函數體時不用。
   - 建構子和靜態成員不能是virtual function。

   ### 多型的表現形式一：
   
   衍生類別的指針可以賦值給基礎類別指針。
   通過基礎類別指針呼叫基礎類別和衍生類別的同名virtual函數時：
   (1) 若該指針指向一個「基礎類別物件」，那麼被呼叫的是「基礎類別」的虛擬函數。
   (2) 若該指針指向一個「衍生類別物件」，那麼被呼叫的是「衍生類別」的虛擬函數。
   
   這種機制就叫做"多型"
   
   ```c++
   class CBase
   {
       public:
           virtual void SomeVirtualFunction(){}
   };
   
   class CDerived: public CBase
   {
       public:
           virtual void SomeVirtualFunction(){}
   };
   
   int main()
   {
       CDerived ODerived;
       CBase * p = & ODerived;
       p -> SomeVirtualFunction();    // 調用哪個虛擬函數取決於p指向哪種類型的物件
       return 0;
   }
   ```
   上述程式碼執行的是CDerived的虛擬函數。
   
   ### 多型的表現形式二：
   
   衍生類別的物件可以賦值給基礎類別引用
   通過基礎類別引用呼叫基礎類別和衍生類別中的同名虛擬函數時：
   (1) 若引用的是一個「基礎類別物件」，那麼被呼叫的是「基礎類別」的虛擬函數。
   (2) 若引用的是一個「衍生類別物件」，那麼被呼叫的是「衍生類別」的虛擬函數。
   
   這種機制也叫做多型，例子如下：
   ```c++
   class CBase
   {
       public:
           virtual void SomeVirtualFunction(){}
   };
   
   class CDerived: public CBase
   {
       public:
           virtual void SomeVirtualFunction(){}
   };
   
   int main()
   {
       CDerived ODerived;
       CBase & r = ODerived;
       r.SomeVirtualFunction();    // 調用哪個虛擬函數取決於r引用哪種類型的物件
       return 0;
   }
   ```
   上述程式碼執行的是CDerived的虛擬函數。
   
   另一個例子：
   ```c++
   class A
   {
   	public:
		virtual void Print()
		{
			cout << "A::Print" << endl;
		}
   };
   
   class B: public A
   {
   	public:
		virtual void Print()
		{
			cout << "B::Print" << endl;
		}
   };
   
   class D: public A
   {
   	public:
			virtual void Print()
			{
				cout << "D::Print" << endl;
			}
   };
   
   class E: public B
   {
		public:
			virtual void Print()
			{
				cout << "E::Print" << endl;
			}
   };
   
   int main()
   {
   	A a; B b;
		E e; D d;
		A * pa = & a; B * pb = & b;
		D * pd = & d; E * pe = & e;
		
		pa->Print();	// a.Print()被呼叫
		pa = pb;
		pa->Print();	// b.Print()被呼叫
		pa = pd;
		pa->Print();	// d.Print()被呼叫
		pa = pe;
		pa->Print();	// e.Print()被呼叫
		return 0;
   }
   ```
   
   上述程式碼執行結果如下：
   ```
   A::Print
   B::Print
   D::Print
   E::Print
   ```
   
   多型的作用：
   
   在物件導向程式設計中使用多型，能夠增強程式的可擴充性，即程式需要修改或增加功能的時候，需要改動和增加的程式碼較少。
   
2. 使用多型的遊戲程式實例

   遊戲【魔法門之英雄無敵】
   
   怪物能夠互相攻擊，攻擊敵人和被攻擊時都有相應的動作，動作是通過物件的成員函數實現的。
   
   遊戲版本升級時，要增加新的怪物--雷鳥。
   如何寫程式才能使升級時的程式碼改動和增加量較少？
   原有類別： CSoldier、CDragon、CPhonex、CAngel
   新增類別： CThunderBird
   
   基本思路：
   為每個怪物類別編寫Attack、FightBack和Hurted成員函數。
   
   Attack：表現攻擊動作，攻擊某個怪物，並呼叫被攻擊怪物的Hurted函數以減少被攻擊怪物的生命值，同時也呼叫被攻擊怪物的FightBack成員函數，遭受被攻擊怪物的反擊。
   
   Hurted：減少自身生命值，並表現受傷動作。
   FightBack：此成員函數表現反擊動作，並呼叫被反擊對象的Hurted成員函數，使被反擊對象受傷。
   
   設置基礎類別CCreature，並且使CDragon, CWolf等其他類別都從設置基礎類別CCreature衍生而來。
   
   若我們不使用多型來實現：
   ```c++
   class CCreature
   {
   	protected:
			int nPower; // 代表攻擊力
			int nLifeValue; // 代表生命值
   };
   
   class CDragon: public CCreature
   {
   	public:
			void Attack(CWolf * pWolf)
			{
				...表現攻擊動作的代碼
				pWolf -> Hurted(nPower);
				pWolf -> FightBack(this);
			}
			
			void Attack(CGhost * pGhost)
			{
				...表現攻擊動作的代碼
				pGhost-> Hurted(nPower);
				pGhost -> FightBack(this);
			}
			
			void Hurted(int nPower)
			{
				...表現受傷動作的代碼
				nLifeValue -= nPower;
			}
			
			void FightBack(CWolf * pWolf)
			{
				...表現反擊動作的代碼
				pWolf -> Hurted(nPower / 2);
			}
			
			void FightBack(CWolf * pGhost)
			{
				...表現反擊動作的代碼
				pGhost -> Hurted(nPower / 2);
			}
   }
   ```
   有n種怪物，CDragon類別中就會有n個Attack成員函數，以及n個FightBack成員函數，而其他類也如此。
   
   非多型實現方法的缺點：
   如果遊戲版本升級，增加新的怪物雷鳥CThunderBird，則程式改動較大。
   所有的類別都需要增加兩個成員函數：
   ```
   void Attack(CThunderBird * pThunderBird);
   void FightBack(CThunderBird * pThunderBird);
   ```
   
   利用多型的實現方法：
   
   ```c++
   class CCreature
   {
   	protected:
			int nPower; // 代表攻擊力
			int nLifeValue; // 代表生命值
		public:
			virtual void Attack(CCreature * pCreature){}
			virtual void Hurted(int nPower){}
			virtual void FightBack(CCreature * pCreature){}
   };
   ```
   基礎類別只有一個Attack成員函數；
   也只有一個FightBack成員函數；
   所有CCreature的衍生類別也是這樣。
   
   以CDragon為例：
   ```c
   class CDragon: public CCreature
   {
   	public:
			virtual void Attack(CCreature * pCreature);
			virtual void Hurted(int nPower);
			virtual void FightBack(CCreature * pCreature);
   };
   
   void CDragon::Attack(CCreature * p)
   {
   	...表現攻擊動作的代碼
		p -> Hurted(m_nPower); // 多型
		p -> FightBack(this); // 多型
   }
   
   void CDragon::Hurted(int nPower)
   {
   	...表現受傷動作的代碼
		m_nLifeValue -= nPower;
   }
   
   void CDragon::FightBack(CCreature * p)
   {
   	...表現反擊動作的代碼
		p -> Hurted(m_nPower / 2); // 多型
   }
   
   ```
   
   利用多型的方式，如果遊戲版本升級，增加新的怪物雷鳥。
   只需要編寫新類別CThunderBird，不需要在已有的類別裡專門為新怪物增加：
   ```c++
   void Attack(CThunderBird * pThunderBird);
   void FightBack(CThunderBird * pThunderBird);
   ```
   成員函數，已有的類別可以原封不動，沒壓力阿！！！
   
   
   ### 原理：
   
   ```c
   CDragon Dragon; CWolf Wolf; CGhost Ghost;
   CThunderBird Bird;
   Dragon.Attack(& Wolf);	// (1)
   Dragon.Attack(& Ghost);	// (2)
   Dragon.Attack(& Bird);	// (3)
   ```
   
   根據多型的規則，上面的(1)、(2)、(3)進入到```CDragon::Attack```函數後，能分別呼叫：
   ```
   CDragon::Hurted
   CWolf::Hurted
   CGhost::Hurted
   ```
   
3. 更多的多型程式實例
   
   幾何形體處理程序：輸入若干個幾何形體的參數，要求按面積排序輸出。輸出時要指名形狀。
   
   Input:
   
   第一行是幾何形數目n(不超過100)，下面有n行，每行以一個字母c開頭。
   
   若c是'R'，則代表一個矩形，本行後面跟著兩個整數，分別是矩形的寬和高；
   
   若c是'C'，則代表一個圓，本行後面跟著一個整數代表其半徑；
   
   若c是'T'，則代表一個三角形，本行後面跟著三個整數，代表三個邊的長度；
   
   Output:
   
   按面積從小到大依次輸出每個幾何形體的種類及面積。每行一個幾何形體，輸出格式為：
   
   形體名稱: 面積
   
   Sample Input:
   3
   R 3 5
   C 9
   T 3 4 5
   
   Sample Out:
   Triangle: 6
   Rectangle: 15
   Circle: 254.34
   
   程式碼實現：
   ```c
   #include <iostream>
   #include <stdlib.h>
   #include <math.h>
   
   using namespace std;
   
   class CShape
   {
   	public:
		   virtual double Area() = 0; // 純虛擬函數
		   virtual void PrintInfo() = 0;
   };
   
   class CRectangle:public CShape
   {
   	public:
		   int w,h;
		   virtual double Area();
		   virtual void PrintInfo();
   };
   
   class CCircle:public CShape
   {
		public:
		   int r;
		   virtual double Area();
		   virtual void PrintInfo();
   };
   
   class CTriangle:public CShape
   {
		public:
		   int a,b,c;
		   virtual double Area();
		   virtual void PrintInfo();
   };
   
   double CRectangle::Area()
   {
   	return w * h;
   }
   
   void CRectangle::PrintInfo()
   {
   	cout << "Rectangle:" << Area() << endl;
   }
   double CCircle::Area()
   {
   	return 3.14 * r * r ;
   }
   void CCircle::PrintInfo()
   {
   	cout << "Circle:" << Area() << endl;
   }
   double CTriangle::Area()
   {
	   double p = ( a + b + c) / 2.0;
	   return sqrt(p * ( p - a)*(p- b)*(p - c));
   }
   void CTriangle::PrintInfo()
   {
   	cout << "Triangle:" << Area() << endl;
   }
   
   CShape * pShapes[100]; // 用來存放指針的陣列
   
   int MyCompare(const void * s1, const void * s2);
   
   int main()
   {
	   int i;
	   int n;
	   
	   CRectangle * pr;
	   CCircle * pc;
	   CTriangle * pt;
	   
	   cin >> n;
	   
	   for( i = 0;i < n;i ++ )
	   {
		   char c;
		   cin >> c;
		   switch(c)
		   {
			   case 'R':
				   pr = new CRectangle();
				   cin >> pr -> w >> pr -> h;
				   pShapes[i] = pr;
				   break;
			   case 'C':
				   pc = new CCircle();
				   cin >> pc -> r;
				   pShapes[i] = pc;
				   break;
			   case 'T':
				   pt = new CTriangle();
				   cin >> pt -> a >> pt -> b >> pt -> c;
				   pShapes[i] = pt;
				   break;
		   }

	   }
	   qsort(pShapes, n, sizeof( CShape*), MyCompare);
	   for( i = 0;i <n;i ++)
	   	pShapes[i] -> PrintInfo();
	   return 0;
   }
   
   int MyCompare(const void * s1, const void * s2)
   {
	   double a1, a2;
	   CShape * * p1 ; // s1,s2是void * ，不可寫「* s1」來取得s1指向的內容
	   CShape * * p2;
	   p1 = ( CShape * * ) s1; // s1, s2指向pShapes陣列中的元素，陣列元素的類型是CShape *
	   p2 = ( CShape * * ) s2; // 故p1, p2都是指向指針的指針，類行為CShape **
	   a1 = (*p1)->Area(); // * p1類型是Cshape *，是基礎類別指針，故此句為多型
	   a2 = (*p2)->Area();
	   
	   if( a1 < a2 )
	   	return -1;
	   else if ( a2 < a1 )
	   	return 1;
	   else
	   	return 0;
   }
   ```
   
   如果添加新的幾何形體，比如五邊形，則只需要從CShape衍生出CPentagon，以及在main中的switch陳述句中增加一個case，其餘部分不變。
   
   用基礎類別的指針陣列存放指向各種衍生類別的物件，然後遍歷該陣列，就能對各個衍生類別物件進行各種操作，是很常用的作法。:star::star::star:
   
   多型的又一個例子：
   ```c++
   class Base
   {
		public:
		   void fun1() { fun2(); }
		   virtual void fun2() { cout << "Base::fun2()" << endl; }
   };
   
   class Derived: public Base
   {
	   public:
		virtual void fun2() { cout << "Derived:fun2()" << endl; }
   };
   
   int main()
   {
	   Derived d;
	   Base * pBase = & d;
	   pBase -> fun1();
	   return 0;
   }
   ```
   上述程式碼執行結果為：
   ```
   Derived:fun2()
   ```
   
   為何不是```Base:fun2()```呢？
   
   Ans: 因為this指針的關係(回顧第三週第8節)
   編譯時被還原成如下程式
   ```c++
   class Base
   {
		public:
		   void fun1()
		   {
			// this是基礎類別指針，fun2是virtual function，所以是多型
		   	this -> fun2();
		   }
		   virtual void fun2() { cout << "Base::fun2()" << endl; }
   };
   ```
   
   在非建構函數、非解構函數的成員函數中調用virtual function，就是多型！:star::star::star:
   
   在建構子和解構子中呼叫virtual function不是多型。
   
   編譯時即可確定，呼叫的函數是自己的類別或基礎類別中定義的函數，不會等到執行時才決定要調用自己的還是衍生類別的函數。
   
   另外，在衍生類別中和虛擬函數同名同參數表的函數，不加virtual也自動變成虛擬函數。
   例如：
   ```c++
   class myclass
   {
	   public:
		   virtual void hello()
		   {
		   	cout<<"hello from myclass"<<endl;
		   }
		   
		   virtual void bye()
		   {
		   	cout<<"bye from myclass"<<endl;
		   }
   };
   
   class son:public myclass
   {
	   public:
		   void hello()	// 雖然沒加virtual關鍵字，但仍然是虛擬函數
		   {
		   	cout<<"hello from son"<<endl;
		   }
		   son()
		   {
		   	hello();	// 調用son自己的hello
			// 而且在建構子中呼叫的函數不是多型
		   }
		   ~son()
		   {
		   	bye();	// 調用son自己的bye，因為在解構子中，仍然不是多型
			// 但是son自己沒有定義bye，此處的bye是從myclass繼承而來的
		   }
   }; 
   
   class grandson:public son
   {
	   public:
		   void hello()	// 雖然沒加virtual關鍵字，但仍然是虛擬函數
		   {
		   	cout<<"hello from grandson"<<endl;
		   }
		   
		   void bye()	// 雖然沒加virtual關鍵字，但仍然是虛擬函數
		   {
		   	cout << "bye from grandson"<<endl;
		   }
		   
		   grandson()
		   {
		   	cout<<"constructing grandson"<<endl;
		   }
		   
		   ~grandson()
		   {
		   	cout<<"destructing grandson"<<endl;
		   }
   };
   
   int main()
   {
	   grandson gson;
	   son * pson;
	   pson = & gson;
	   pson -> hello(); // 多型
	   return 0;
   }
   ```
   
   以上程式碼輸出結果：
   ```
   hello from son
   constructing grandson
   hello from grandson
   destructing grandson
   bye from myclass 
   ```
   
   為什麼在建構子和解構子中呼叫virtual function不會是多型？
   
   Ans:
   當一個衍生類別的物件要進行初始化時，會先執行基礎類別物件的構造函數，而此時衍生類別物件自己的成員實際上是還沒被初始化的。若在基礎類別的建構函數執行期間呼叫虛擬函數，又允許這個虛擬函數是多型的話，那麼在基礎類別建構子執行期間就會呼叫衍生類別虛擬函數，那這個執行結果會可能不正確(例如用到了衍生類別物件中的某個尚未初始化的成員變數)。
   
4. 多型實現原理

   「多型」的關鍵在於通過基礎類別的指針引用呼叫一個虛擬函數時，編譯時不確定到底調用的是擠出類別還是衍生類別的函數，執行時才確定--這叫動態「動態繫結(dynamic binding)」。
   
   請看下面例子：
   ```c++
   class Base
   {
		public:
			int i;
			virtual void Print()
			{
				cout << "Base:Print" << endl;
			}
   };
   
   class Derived: public Base
   {
		public:
			int n;
			virtual void Print()
			{
				cout << "Derived:Print" << endl;
			}
   };
   
   int main()
   {
		Dervied d;
		cout << sizeof(Base) << "," << sizeof(Dervied);
		return 0;
   }
   ```
	執行結果為：
	```
	8, 12
	```
	
	為什麼會多了四個byte?
	多型的實現關鍵--虛擬函數表
	
	每一個虛擬函數的類別(或有虛擬函數的類別的衍生類別)，都有一個虛擬函數表，該類別的任何物件中都放著虛擬函數表的指針。虛擬函數表中列出了該類別的虛擬函數地址，多出來的四個byte就是用來放虛擬函數表的地址的。
	
	所以Base的記憶體空間放了pointer for vTable(4 byte) + int i (4 byte) = 8 byte
	所以Base的記憶體空間放了pointer for vTable(4 byte) + int i (4 byte) + int n (4 byte) = 12 byte
	
5. 虛擬解構函式

   問題：
   ```c++
   class CSon
   {
		public:
			~CSon(){};
   };
   
   class CGrandson: CSon
   {
		public:
			~CGrandson(){};
   };
   
   int main()
   {
		CSon * p = new CGrandson;
		delete p;
		return 0;
   }
   ```
   
   基礎類別的指針刪除衍生類物件時 $\Rightarrow$ 只呼叫基礎類別的解構函數
   
   V.s.
   
   刪除一個衍生類別的物件時 $\Rightarrow$ 先呼叫衍生類別的解構函數 $\Rightarrow$ 再呼叫基礎類別的解構函數

   解決辦法：
   把基礎類別的解構函數宣告為virtual
   - 衍生類別的解構函數virtual可以不宣告
   - 通過基礎類別的指針刪除衍生類別物件時
     $\Rightarrow$ 先呼叫衍生類別的解構函數 $\Rightarrow$ 再呼叫基礎類別的解構函數
   
   類別如果定義了虛擬函數，最好將解構函數也定義成虛擬函數。(但不允許虛擬函數作為建構子)
   
   具體例子：
   ```c++
   class son
   {
		public:
			~son()
			{
				cout << "bye from son" << endl;
			}
   };
   
   class grandson: public son
   {
		public:
			~grandson()
			{
				cout << "bye from grandson" << endl;
			}
   }
   
   int main()
   {
   	son * pson;
		pson = new grandson;
		delete pson;
		return 0;
   }
   ```
   上述程式碼輸出結果：```bye from son```
   沒有執行```grandson::~grandson()```
   
   需改正為：
   
   ```c++
   class son
   {
		public:
			~son()
			{
				cout << "bye from son" << endl;
			}
   };
   ```
   輸出結果：
   ```
   bye from grandson
   bye from son
   ```
   
6. 純虛函數和抽象類別
   
   純虛函數：沒有函數體的虛擬函數
   ```c
   class A
   {
		private:
			int a;
		public:
			virtual void Print() = 0;	// 純虛函數
			void fun()
			{
				cout << "fun";
			}
   };
   ```
   
   抽象類別：包含純虛函數的類別
   - 只能作為基礎類別來衍生新類別使用
   - 不能創建抽象類別的物件
   - 抽象類別的指針和引用 $\Rightarrow$ 由抽象類別衍生出來的類別的物件
   
   ```c++
   A a;	// error，A是抽象類別，不能創建物件
   A * pa;	// ok，可以定義抽象類別的指針和引用
   pa = new A;	// 錯誤，A是抽象類別，不能創造物件
   ```
   
   這種使用方式特別有利於使用多型去創造相應不同的新物件。
   也就是virtual function只在其子類別中被實現。
   
   抽象類別中
   - 在成員函數內可以呼叫純虛函數
   - 在建構函數/解構函數內部不能呼叫純虛函數

   如果一個類別從抽象類別衍生而來，
   則它必須實現基礎類別中所有的純虛函數才能成為非抽象類別。
   
   ```c++
   class A
   {
   	public:
			virtual void f() = 0; // 純虛函數
			void g() { this -> f(); } // ok，this可省略
			A(){ } // f(); 錯誤，構造函數不可呼叫純虛函數
   };
   
   class B: public A
   {
   	public:
			void f(){ cout << "B: f()" << endl; }
			// 實現所有純虛函數，不再是抽象類別
   };
   
   int main()
   {
   	B b;
		b.g();
		return 0;
   }
   ```
   上述程式執行結果為：
   ```
   B: f()
   ```
   
7. override關鍵字
   virtual如果有迫切需要在繼承類別上實現，或避免一些錯誤導致沒有被實現，如：
   ```c
   class CA
   {
	   public:
		   virtual void func1(int)
		   {
			std::cout << “func1 in CA" << std::endl;
		   }
   };
   
   class CB : public CA
   {
	   public:
		   void func1(int) const
		   {
			std::cout << “func1 in CB" << std::endl;
		   }
   };
   
   class CC : public CA
   {
	   public:
		   void func1(float)
		   {
			std::cout << “func1 in CC" << std::endl;
		   }
   };
   ```
   以CB的func1()來說，雖然看來和CA的一樣，但是由於他有加上const，所以還是不同；
   而 CC 的func1()則是由於參數型別不同，所以也不會被視為同一個虛擬函式。
   所以，在執行下面的程式的時候，就都只會呼叫到 CA::func1()，而不會呼叫到各自的實作了。
   ```c
   CA* pObjA = new CA();
   CA* pObjB = new CB();
   CA* pObjC = new CC();
   pObjA->func1(0); // func1 in CA
   pObjB->func1(0); // func1 in CA
   pObjC->func1(0); // func1 in CA
   ```
   而為了避免這類的問題發生，C++11 提供了一個新的語法：「override」，來在編譯階段就可以確定衍生類別的函式的覆寫是否有成功。
   
   它的使用方法也很簡單，只要在衍生類別裡面、要覆寫函式後面加上「override」，告訴編譯器這個函式是要用來覆寫基礎類別的虛擬函式就可以了。
   
   如果加上override關鍵字，卻沒有覆寫任何父類別的virtual function，那編譯器就會報錯：
   ```c
   class CA
   {
	   public:
		   virtual void func1(int)
		   {
			std::cout << “func1 in CA" << std::endl;
		   }
   };
   
   class CB : public CA
   {
	   public:
		   void func1(int) const override
		   {
			std::cout << “func1 in CB" << std::endl;
		   }
   };
   
   class CC : public CA
   {
	   public:
		   void func1(float) override
		   {
			std::cout << “func1 in CC" << std::endl;
		   }
   };
   ```
   這樣的話，上述程式編譯時就會出現錯誤，不必擔心沒有實現到某個純虛擬函數的問題。
   
   
   參考資料：
   1. https://www.itread01.com/content/1549433557.html
   2. https://kheresy.wordpress.com/2014/10/03/override-and-final-in-cpp-11/
   
---
7～11週[筆記在這](/JPMUh5LvSVeEo0hfWmK7AA)