# 位運算筆記(bitwise operation)

## 用位運算取代modulo

1. 取代 $n$ % $1$
   只要檢查最後一位元即可
   ```c
   n % 1 = n & 1
   ```
2. 取代 $n$ % $2^i$
   2的冪次最法都差不多，因為右移一位相當於除以2
   ```c
   n % 2^i = n & (2^i - 1)
   ```
3. 取代 $n$ % $3$
   先從一個較簡單的題目考慮：
   ```
   如何「不用除法與取模」判斷整數是否為 3 的倍數？
   ```
   從2進位的觀點來看，考慮每個位元對於3之餘數為何？
   ```
   00000001： 1 mod 3  = 1 (第0個bit為1)
   00000010： 2 mod 3  = 2 (第1個bit為1)
   00000100： 4 mod 3  = 1 (第2個bit為1)
   00001000： 8 mod 3  = 2 (第3個bit為1)
   00010000： 16 mod 3  = 1 (第4個bit為1)
   00100000： 32 mod 3  = 2 (第5個bit為1)
   ```
   以此類推，發現每經過2個bits，餘數就開始循環。
   
   由於mod具有分配律：
   $(a + b) \mod n = [(a \mod n) + (b \mod n)] \mod n$
   
   因此我們可以推斷出，如果n要是3之倍數，
   必須是以下幾種情況：
   1. 偶數位的bits為3之倍數，例如
      $$
	  \begin{align}
	  00000001 + 00000100 + 00010000 \mod 3 \\
	  = 1+4+8 \mod 3 \\
	  = 1+1+1 \mod 3 \\
	  = 3 \mod 3 \\
	  = 0
	  \end{align}
	  $$
   2. 奇數位的bits為3之倍數，例如
      $$
	  \begin{align}
	  00000010 + 00001000 + 00100000 \mod 3 \\
	  = 2+8+32 \mod 3 \\
	  = 2+2+2 \mod 3 \\
	  = 6 \mod 3 \\
	  = 0
	  \end{align}
	  $$
   3. 一個偶數位的1配上一個奇數位的1，例如：
      $$
	  \begin{align}
	  00000010 + 00000100 \mod 3 \\
	  = 2+4 \mod 3 \\
	  = 2+1 \mod 3 \\
	  = 3 \mod 3 \\
	  = 0
	  \end{align}
	  $$
	  
   因此，要推斷n是不是3之倍數，
   我們需要判斷偶數位bits之1的個數，和奇數位bits之1的個數，兩者之間的差，
   是否恰為0（剛好一個餘數1加一個餘數2可以組成一個3），
   或是3之倍數（有其中一方是1+1+1或2+2+2組成3之倍數），
   如果是結果大於等於3又可繼續遞迴做下去。
   要注意兩邊個數的大小，差要取絕對值。
   ```c
   bool is3mul(int num)
   {
	   while(num >= 3)
	   {
		   int odd = __builtin_popcount(num & 0xAAAAAAAA);
		   int even = __builtin_popcount(num & 0x55555555);
		   num = (odd > even)? (odd - even) : (even - odd);
	   }
	   return (!num);
   }
   ```
   
   如果要一般化成mod運算，也是相同的看法，去數共有幾個奇數位和偶數位的1，可以配成多少餘數。
   權重為每個奇數位bit貢獻一個餘數2，每個偶數位bit貢獻一個餘數1。
   大於3可以繼續遞迴做下去，如果恰好等於3那我們知道餘數就是0。
   
   可寫成如下程式：
   ```c
   int mod3(int num)
   {
	   while(num > 3)
	   {
		   int odd = __builtin_popcount(num & 0xAAAAAAAA);
		   int even = __builtin_popcount(num & 0x55555555);
		   num = odd * 2 + even;
		   if (num == 3)
		   	num = 0;
	   }
	   return (num);
   }
   ```
   
### 若要繼續推廣至「求 mod 任意奇數之值」呢？

4. 推廣到「求 mod 任意"奇數"之值」，若是mod一個奇數，其實都和mod 3的例子差不多，例如以5而言：
   
   step 1: 
   ```
   1 mod 5 = 1,
   2 mod 5 = 2,
   4 mod 5 = 4,
   8 mod 5 = 3,
   16 mod 5 = 1,
   32 mod 5 = 2,
   ....
   ```
   每經過 4 bits 後，餘數開始循環。```weight[4] = {1, 2, 4, 3}```。
   
   step 2:
   
   x 第 0, 4, 8, 12, ... 之位元加總得 s0
   x 第 1, 5, 9, 13, ....之位元加總得 s1 
   x 第 2, 6, 10, 14, ....之位元加總得 s2
   x 第 3, 7, 11, 15, ....之位元加總得 s3
   
   step 3:
   ```x = s0 * weight[0] + s1 * weight[1] + s2 * weight[2] + s3 * weight[3]```
   
   step 4:
   若 x 小於 5 ，結束；否則回到 step 2。
   
5. 推廣到「求 mod 任意"偶數"之值」，由於2的冪次前面討論過了，我們這邊嘗試非2的冪次之偶數。
   
   若是mod一個偶數，一樣先觀察循環，如mod 12：
   ```
   1 mod 12 = 1,
   2 mod 12 = 2,
   4 mod 12 = 4,
   8 mod 12 = 8,
   ----------
   16 mod 12 = 4,
   32 mod 12 = 8,
   64 mod 12 = 4,
   128 mod 12 = 8,
   256 mod 12 = 4,
   512 mod 12 = 8,
   ....
   ```   
   可以觀察到在n大於12之後，就開始4、8、4、8之循環
   因此我們只要對最右邊4個bits進行特別處理即可，
   即檢查剩餘的數有沒有大於12之部分，有的話就用減的求餘數：
   ```c
   int mod12(int num)
   {
	   while(num > 12)
	   {
		   int odd = __builtin_popcount(num & 0xAAAAAAA0);
		   int even = __builtin_popcount(num & 0x55555550);
		   int residual = (num & 0xF);
		   if (residual > 12)
		   	residual -= 12;
		   num = odd * 8 + even * 4 + residual;
		   if(num == 12)
		   	num = 0;
	   }
	   return num;
   }
   ```
   
## popcount之實作
利用SWAR-algorithm來進行計算，
詳細參考[這裡](http://ponder.work/2020/08/01/variable-precision-SWAR-algorithm/)

主要想法是利用兩兩合併的做法(分治法的一種)：
首先是奇數位的bit和偶數位的bit兩兩計算，
並利用2個bits來表達，每兩個bits中有幾個為1的bits，
接著考慮每4個bits中有幾個為1的bits，
再考慮每8個bits中有幾個為1的bits，......以此類推。

直接上圖：
![](https://i.imgur.com/5Bx7xtu.png)

考慮一個實際例子0x12345678：

![](https://i.imgur.com/D16IEBs.png)
放大圖片：https://upload.cc/i1/2020/11/17/ilsJCg.png

寫成code：
```c
#include "stdio.h"

int popcount(int num)
{
	num = (num & 0x55555555) + ((num >> 1) & 0x55555555);
	num = (num & 0x33333333) + ((num >> 2) & 0x33333333);
	num = (num & 0x0F0F0F0F) + ((num >> 4) & 0x0F0F0F0F);
	num = (num & 0x00FF00FF) + ((num >> 8) & 0x00FF00FF);
	return (num >> 16) + (num & 0x0000FFFF);
}

int main()
{
	int my_pop = popcount(0x12345678);
	int builtin_pop = __builtin_popcount(0x12345678);
	printf("my pop =\t%d\nbuiltin pop = \t%d", my_pop, builtin_pop);
	return 0;
}

```

## reverse bits string之實作
參考[影片](https://www.youtube.com/watch?v=ZW7st_pN05w)
主要想法也是divide-and-conquer，
先前後兩半16個bits交換，再兩兩8bits交換，再兩兩4bits交換，......以此類推。

![](https://i.imgur.com/p8vPY6r.png)
放大圖片：https://upload.cc/i1/2020/11/17/NEY9kt.png

可參考leetcode第190題，寫成code：
```c
uint32_t reverse_bits(uint32_t num)
{
        num = ((num >> 16) & 0x0000FFFF) | (num << 16);
        num = ((num >> 8) & 0x00FF00FF) | ((num << 8) & 0xFF00FF00);
        num = ((num >> 4) & 0x0F0F0F0F) | ((num << 4) & 0xF0F0F0F0);
        num = ((num >> 2) & 0x33333333) | ((num << 2) & 0xCCCCCCCC);
        num = ((num >> 1) & 0x55555555) | ((num << 1) & 0xAAAAAAAA);
        return num;
}
```

## __builtin_ctz之實作

一樣可以使用二分法：
```c
int ctz(int num)
{
        int count = 31;

        num &= -num;    // unset all bits except least significant bit

        if(num & 0x0000FFFF)
                count -= 16;
        if(num & 0x00FF00FF)
                count -= 8;
        if(num & 0x0F0F0F0F)
                count -= 4;
        if(num & 0x33333333)
                count -= 2;
        if(num & 0x55555555)
                count -= 1;
        return count;
}
```
主要思路，僅留下最右邊為1之bit(Least Significant Bit)，
然後從31開始減(最多31個0，如果num為0此函數不work)，
每次把bit string切成兩半，每次檢查右半邊是否全為0，
若不全為0則代表最右邊的1在右邊，則我們需要減去左半邊的bits數，接著遞迴做下去。

## __builtin_clz之實作

也可以套用二分法，但需要注意的是，除了每次的mask要反過來以外，
一開始的```num &= -num```也不能直接移植，需要另外想辦法來留下最左邊為1的bit(Most Significant Bit)：
```c
int clz(int num)
{
        int count = 31;

        num = keepMSB(num);

        if (num & 0xFFFF0000)
                count -= 16;
        if (num & 0xFF00FF00)
                count -= 8;
        if (num & 0xF0F0F0F0)
                count -= 4;
        if (num & 0xCCCCCCCC)
                count -= 2;
        if (num & 0xAAAAAAAA)
                count -= 1;
        return count;
}
```
關於keepMSB之實作請看下面一節。

## 保留MSB的作法(Most Significant Bit)
主要想法：
先把最左邊的bit，其右邊的所有bits都設為1，
即以most significant bit為分界點，左邊全0，右邊全1，
接著此數再用xor或是減法運算去unset右邊所有bits，即```num ^ (num >> 1)```或```num - (num >> 1)```

程式碼如下：
```c
int keepMSB(int num)
{
        num |= (num >> 1);
        num |= (num >> 2);
        num |= (num >> 4);
        num |= (num >> 8);
        num |= (num >> 16);

        return num ^ (num >> 1);
}
```

## bit pattern

若對於一個數，需要經常查第 n bit 為 0/1 時，可以這麼做：
```c
int GetBit(unsigned char x, size_t idx)
{
    return ( x & (1<<idx) ) != 0;
}
```
另一種方式是 union 裡再包 struct：
```c
#include "stdio.h"
union U8
{
	unsigned char val;
	struct
	{
		unsigned char b0:1, b1:1, b2:1, b3:1, b4:1, b5:1, b6:1, b7:1;
	};
};

void print_binary(union U8 var)
{
	printf("%d%d%d%d%d%d%d%d\n",
	var.b7, var.b6, var.b5, var.b4,
	var.b3, var.b2, var.b1, var.b0);
}

int main()
{    
	union U8 var;
	var.val = 10;
	print_binary(var); // display 00001010
	return 0;
}
```
但是這種寫法8-bit就很繁瑣了，而且bit-fileds不允許使用陣列，如：
```c
union U8
{
    unsigned char val;
    struct {unsigned char b[8]:1;};
};
```
:x:這樣是錯誤的。

參考資料：[這裡](https://edisonx.pixnet.net/blog/post/91085554)

## 用位運算取代加法 +

先考慮一位元的bit相加，我們要使：


| bit 1 | bit 2 | 運算結果 |
|:-----:|:-----:|:--------:|
|   0   |   0   |    0     |
|   0   |   1   |    1     |
|   1   |   0   |    1     |
|   1   |   1   |    10    |

前三項我們可以使用xor來達成，因為：
```
0 ^ 0 = 0
0 ^ 1 = 1
1 ^ 0 = 1
```
第四項則無法只用1個bit來達成，需要把carry帶到第二位，無法使用單一次的位元運算做到這件事，
我們需要組合&和<<的方式才能做到：
```
(1 & 1) << 1 = 10
```
為什麼需要```&```呢？因為只有當```1 & 1 = 1```時我們才需要向左移。

所以可以把整個加法運算結果寫為如下程式：
```c
int add(int a, int b)
{
	int sum = a, carry = b;
	while(carry)
	{
		int temp = sum;
		sum = temp ^ carry;
		carry = (temp & carry) << 1;
	}
	return sum;
}
```
上述程式先把sum令為a，carry令為b，
接著不斷相加sum和carry直到carry為0為止，就是我們要的答案。

## 用位運算取代減法 -

減法其實就是加上一個負數，負數在2's complement就是用取反再加1即可。

因此很簡單的可以寫為如下程式：
```c
int sub(int a, int b)
{
	b = add(~b, 1);
	return add(a, b);
}
```

## 用位運算取代乘法 *

對於乘法來說，就是用很多次的加法來實現：
```c
int abs(int num)
{
  return num < 0 ? add(~num, 1) : num;
}

int multiply(int a, int b)
{
  int multiplicand = abs(a), multiplier = abs(b);

  int product = 0;
  while (multiplier)
  {
    product = add(product, multiplicand);
    multiplier = sub(multiplier , 1);
  }

  if ((a ^ b) < 0)
  {
    product = add(~product, 1);
  }

  return product;
}
```

一開始，令multiplier為a之絕對值，令multiplicand為b之絕對值，
用一個product來儲存每次加上去的數，
每次把multiplicand加進product中，就把multiplier減掉1，直到multiplier = 0 為止。
最後要把正負號還原，也就是看a跟b的xor，為何呢？

我們只看most significant的bit：
| bit 1 | bit 2 | 正負號 |
|:-----:|:-----:|:------:|
|   0   |   0   |   0    |
|   0   |   1   |   1    |
|   1   |   0   |   1    |
|   1   |   1   |   0    |

恰與xor之運算相符。

除了上述做法，我們可以利用左移和右移來達到更快的效率，
因為我們知道：
$$
\begin{align}
乘數\times  被乘數 &= 積\\
(乘數\times\frac{1}{2}) \times (被乘數\times 2) &= 積
\end{align}
$$
所以左移和右移在這邊是可以派上用場的。

因為當我們左移一位時，相當於將一個數字乘以2，右移一位時，相當於將一個數字除以2，且取floor。

所以乘法可以寫為如下程式：
```c
int abs(int num)
{
  return num < 0 ? add(~num, 1) : num;
}

int multiply(int a, int b)
{
	int multiplicand = abs(a), multiplier = abs(b);
	int product = 0;
	while(multiplier)
	{
		if(multiplier & 1)
			add(product, multiplicand);
		multiplicand <<= 1;
		multiplier >>= 1;
	}
	
	if ((a ^ b) < 0)
    	product = add(~product, 1);
		
	return product;
```
每次左移被乘數，並右移乘數，若遇到乘數的least significant bit為1，就把被乘數加到product中。
為什麼可以這樣做呢？

因為當每次multiplier為奇數時，相當於下一次右移會把最右邊的1捨去作floor，因此我們需要將這個結果加進product中。

## 用位運算取代除法 /

和乘法一樣，我們可以透過一個迴圈不斷的減去除數來實現，但一樣有可以加速的技巧存在。
首先，先用```10101011```除以```1010```當範例。

第一步：
```
1
-----------------------------
1 0 1 0 1 0 1 1
1 0 1 0
-----------------------------
0 0 0 0 1 0 1 1
```
此時，我們進行了一次減法運算，我們是用```10100011```減去```1010```的左移4個位元。

第二步：
```
1 0 0 0 1
-----------------------------
0 0 0 0 1 0 1 1
        1 0 1 0
-----------------------------
0 0 0 0 0 0 0 1
```
再度進行了一次減法運算，是用上一次的餘數減去```1010```的左移0個位元。

最終得到：
$$
\begin{align}
1 0 1 0 1 0 1 1 \div 1 0 1 0 = 1 0 0 0 1 餘 1\\
171 \div 10 = 17 餘 1
\end{align}
$$

再看另外一個例子，用```10101011```除以```1010```的第一步：
```
  1
-----------------------------
1 0 1 0 1 0 1 1
  1 0 1 1 0 0 0
-----------------------------
  1 0 1 0 0 1 1
```
很明顯是將```1011```向左移了 3 位，為什麼不移 4 位呢？
因為如果移 4 位，得到的除數就比被除數大了。

知道規則之後，我們就可以把除法寫為如下程式：
```c
int len_diff(int a, int b)
{
	int a_len = sub((sizeof(int) << 3),  __builtin_clz(a));
	int b_len =sub((sizeof(int) << 3),  __builtin_clz(b));
	return sub(a_len, b_len);
}

int division(int a, int b)
{
	int dividend = abs(a), divisor = abs(b);
	int quotient = 0;
	
	for (int i = len_diff(dividend, divisor); i >= 0; i = sub(i, 1))
	{
		int r = (divisor << i);
		
		// Left shift divisor until it's smaller than dividend
		if (r <= dividend)
		{
			quotient |= (1 << i);
			dividend = sub(dividend, r);
		}
	}

	if ((a ^ b) < 0)
		quotient = add(~quotient, 1);

	return quotient;
}
```
上述程式的流程如下：

1. 首先將除數和被除數進行對齊，即除數和被除數的第一個 1 在同一位置上

2. 判斷除數是否大於等於被除數，如果為否，就不斷右移除數（不斷進行下一個迴圈），直到為真

3. 用除數減去當前的被除數，把商的第 i 位設置為1，減完的結果作為新的被除數

4. 重複前面的步驟，直到被除數為 0

其中，len_diff 是用來判斷被除數和除數到底相差幾個有效位元，也就是最左邊1的位置，
假設被除數的有效位元為16位，除數為4位，那代表總共有12個位置要進行比較。