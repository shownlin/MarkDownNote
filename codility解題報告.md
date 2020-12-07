# codility解題報告

## 1. BinaryGap(訓練loop之使用)
給定一個正整數N，介於[1, 2147483647]之間，因此恰好是int正數能容納之上限。
計算N的二進位表示中，兩個1中間有幾個0，並返回最大的長度，例如：
```
16421 = 100000000100101
```
則兩個1中間有00000000、00和0這三種長度，分別為8、2、1，要返回8。
又例如：
```
32 = 100000
```
則因為只有一個1，沒有gap存在，要返回0。

解題思路：
先判斷popcount是不是1或0，如果是代表沒有gap，返回0。
如果不是，就代表有兩個以上的1。
再來把右移到使N最右邊的bit為1，開始數0的個數，每次右移1個bit。
每次進入迴圈，如果最右邊的bit是0，則計數器遞增，繼續下一圈。
如果最右邊的bit是1，當前計數器與目前最大長度作比較，並把計數器重置，再繼續下一圈。
直到N被右移到為0為止。

100%解答：
```c
int solution(int N) {
    int n1 = __builtin_popcount(N);
    if(n1 == 1 || n1 == 0)
        return  0;
    int max_gap = 0, cur_gap = 0;
    N >>= __builtin_ctz(N);
    while(N > 0)
    {
        if(N & 1)
        {
            max_gap = (max_gap < cur_gap)? cur_gap : max_gap;
            cur_gap = 0;
        }
        else
            ++cur_gap;
        N >>= 1;
    }
    return max_gap;
}
```

## 2. CyclicRotation

給定一個array和整數k，要求將此array每個元素向右邊移動k次，並且超出array範圍的從最前面塞回去。
可以參考std::rotate的[實作](http://www.cplusplus.com/reference/algorithm/rotate/)

基本想法：給定三個指標，first、middle和last，

first代表陣列的頭
middle代表第k個元素
last則是陣列的最末端元素後一個位置
i.e. 陣列範圍是由```[begin, last)```組成

![](https://i.imgur.com/tqh41sW.png)


inplace的解法是採用類似遞迴的概念(但其實不是，以下說明)，
我們用first指標走訪整個陣列，並要求當「first走到middle時」經過的路線都是已經被放置到「正確位置」上的元素，不會再改動。

給定一個指標next，指向要與first交換的元素位置，而next的初始值設定為middle，因為我們知道middle的元素必須移動到陣列的頭個元素。

![](https://i.imgur.com/5xSI12r.png)

每次針對first的位置和next的位置作swap之後，將first和next移動到各自的下一格。

![](https://i.imgur.com/q11RkVH.png)

持續交換，當first走訪到middle位置時，middle前面的元素都已經是就定位的情況(紅色代表位置已固定，不再移動)。

![](https://i.imgur.com/48zDYqx.png)

會發現我們的問題剩下綠色框住的部分，需要把A和G交換、B和H交換才能把next所指向的元素擺放到正確的位置：

![](https://i.imgur.com/zRcMO7C.png)

因此我們重新設定問題，藉由把middle移動到next部分，開始新一輪的交換過程，

![](https://i.imgur.com/VGZKypa.png)

之前說這個方法類似遞迴，但其實不是，就在於我們其實並沒有完整解決子問題，而是只有把first所經過的元素就定位而已。

持續交換下去，還會遇到一樣的情況，

![](https://i.imgur.com/N6KjD5X.png)

一樣再把middle移動到next的部分，middle永遠代表子問題中要擺到這個子陣列的頭個元素

![](https://i.imgur.com/U8U5KY0.png)


當next走到last時，由於已經沒有元素可以交換了：

![](https://i.imgur.com/ko0OQfM.png)

我們知道這個array是circulate的概念，所以next必須從middle重新開始進行下一輪交換：

![](https://i.imgur.com/kUB12wQ.png)

![](https://i.imgur.com/dx8i1xc.png)

![](https://i.imgur.com/J3NUIxM.png)

![](https://i.imgur.com/PYFPsK7.png)

![](https://i.imgur.com/p0G70Go.png)

當first、next和middle指向同一個元素時，代表交換完成，程式結束。

![](https://i.imgur.com/xGts3q7.png)

因為只有一種情況會使first和next指向同一個元素，那就是next++時遇到了last，
必須把next往前移動到middle的位置，而first又恰好指向middle時，兩者才會相遇。

又middle代表剩餘的子陣列必須放到頭一個元素的位置，而first、last又在同一個位置上，
所以已經不會再進行任何交換，整個陣列的元素都被擺放到它應該擺放的位置上。

撰寫程式時，因為一定是next向前移動到middle才有機會跟first指向同一個元素，所以只要判斷是不是```first == middle```就好。

:star:另外要注意的是：
在codility中的題目，是問陣列向右移動的K次後的情況，
而我們的寫法則是把middle所指的元素的移動到最頭，
因此我們必須要先算出究竟誰是middle。
如：
```
A = [3, 8, 9, 7, 6]
K = 3
```
則
```
[3, 8, 9, 7, 6] -> [6, 3, 8, 9, 7]
[6, 3, 8, 9, 7] -> [7, 6, 3, 8, 9]
[7, 6, 3, 8, 9] -> [9, 7, 6, 3, 8]
```
答案是```[9, 7, 6, 3, 8]```，因此9的位置就是我們要的那個middle，怎麼計算呢？
```
middle = last - (K % (last - first))

e.g.
middle = 5 - (3 % (5 - 0)) = 5 - 3 = 2

middle為2，代表指向[3, 8, 9, 7, 6]中的9，正確
```

100%解答：
```c
vector<int> solution(vector<int> &A, int K) {
    if (A.size() == 0)
        return A;
    K = K % A.size();
    auto first = A.begin();
    auto last = A.end();

    // 注意K=0時第一圈的swap會出錯，因為last是指項末端元素的後面一個空位置
	// 需要特別處理
    if(K == 0)
        return A;
    auto middle = last - K;

    auto next = middle;
    while (first != next)
    {
        swap(*(first++), *(next++));
        if(next == last)
            next = middle;
        if(first == middle)
            middle = next;
    }
    return A;
}
```

## 3. OddOccurrencesInArray

給定一個有奇數個整數的array，並且其中的元素都可以找到另一個跟自己一模一樣的元素兩兩成對，僅有一個元素無法配成paired，找出那個單獨的元素是哪個數字。

如：
```
A[0] = 9  A[1] = 3  A[2] = 9
A[3] = 3  A[4] = 9  A[5] = 7
A[6] = 9
```
則答案要回傳7，因為第```A[5]```的7沒辦法與其他元素配對。

基本思路：
沒什麼好講的，就是xor之應用。

C++中的```^```運算子就是bitwise的xor，任兩個相同的數字經過xor會變成0，
而0跟任何數字N作xor就是N本身。

Correctness、Performance 100%
答案：
```c
int solution(vector<int> &A) {
    int ans = 0;
    for(auto item : A)
    {
        ans ^= item;
    }
    return ans;
}
```

## 4. FrogJmp
一隻青蛙要過馬路，給定三個數字：(X, Y, D)
X：代表青蛙的起始位置
Y：代表青蛙想要到達的位置
D：代表青蛙每次跳一步可以跳多遠

寫一個程式回傳青蛙至少要跳幾次才能達到或超過目標位置

基本思路：
簡單的除法而已，只是要記得型態要轉換避免被自動無條件捨去小數點，
再用cmath中的ceil無條件進位。

Correctness Performance 100%
答案：
```c
#include <cmath>
int solution(int X, int Y, int D) {
    return ceil((double)(Y - X) / D);
}
```

## 5. PermMissingElem

給定一組長度為N之陣列，裡面的數字範圍為```[1, (N + 1)]```之正整數串。
要求從1 ~ N+1的整數中，哪一個數字沒有在陣列之中。
記得若為空陣列```[]```，要求回傳1。
:star:注意會有很大的陣列，只用int型態無法解決，請使用unsigned long long。

Correctness Performance 100%
解答：
```c
#include <numeric>
int solution(vector<int> &A) {
    int len = A.size() + 1;
    unsigned long long actual = accumulate(A.begin(), A.end(), 0llu);
    unsigned long long sum = (1llu + len) * len / 2;
    return sum - actual;
}
```

## 6. TapeEquilibrium

給定一個長度為N之整數陣列，
找出一個index P，可以將整個陣列一分為2：
$$
\text{diff} = \left|(A[0] + A[1] + ... + A[P − 1]) − (A[P] + A[P + 1] + ... + A[N − 1])\right|
$$
並且這個P要使diff為最小，回傳這個最小的diff。

解題思路：
利用prefix_sum，先從頭到尾遍歷過一次，使得```prefix_sum[n] = 前n個元素之和```，
並且成遍歷完成時可以得到一個記錄全部陣列原素相加的sum。
記得要把prefix_sum最後一個元素pop掉，因為那個就是sum本身。
如此一來，我們要計算第p個diff時：
$$
\begin{align}
\text{diff} &= \left| (\text{sum} - \text{prefix_sum}[p]) - \text{prefix_sum}[p]) \right|\\
&= \left| sum - 2 \times \text{prefix_sum}[p] \right|
\end{align}
$$

:star:sum有可能會很大，要用long long(不要unsigned，因為陣列中可能有負數)。

Correctness Performance 100%
解答：
```c
#include <climits>
#include <cmath>

int solution(vector<int> &A) {
    vector<int> prefix_sum{};
    long long sum = 0;
    for(auto item : A)
    {
        sum += item;
        prefix_sum.emplace_back(sum);
    }

    prefix_sum.pop_back();

    int min_diff = INT_MAX;
    for(auto item : prefix_sum)
    {
        auto tmp = abs(sum - (item * 2));  
        min_diff = (min_diff > tmp)? tmp : min_diff;
    }
    return min_diff;
}
```

## 7. FrogRiverOne

一隻青蛙要過河，青蛙必須踩著河面上的落葉前進，
然而每秒只會掉一片樹葉，樹葉掉的位置不固定，也有可能掉在重複的地方；
求青蛙最快可以過河的時間點為何？

假設青蛙位於座標0，X + 1代表對岸的座標，給定一個整數陣列A，
A中數字介於```[1, X]```之間，當河面上1, 2, 3, ..., X都存在有樹葉時，青蛙始可過河。

解題思路：
跑迴圈，由第0秒跑到A.size() - 1秒，紀錄當下每一個位置的樹葉有沒有掉落過，並維護一個計數器。
若這個位置是第一次有樹葉落在上面，計數器加1；
如果曾經有樹葉落在同位置，不動作繼續下個迴圈。
當計數器的數字恰好等於X時，代表1~X的位置都有樹葉了，此時回傳for loop的index即為最快可過河之秒數。

若迴圈跑完計數器仍不到X，回傳-1代表青蛙無法過河。

由於只要一次迴圈就可以確定，時間複雜度為O(n)。

Correctness Performance 100%
解答：
```c
#include <unordered_map>
int solution(int X, vector<int> &A) {
    unordered_map<int, bool> hash;
    int len = A.size();
    
    int cnt = 0;

    for(int i = 0; i < len; ++i)
    {
        if(!hash[A[i]])
        {
            hash[A[i]] = true;
            ++cnt;
        }
        if(cnt == X)
            return i;
    }

    return -1;
}
```

## 8. MaxCounters

給定N個計數器，有兩種操作方法：
* 第k個counter遞增1
* 全部的counter都變成最大的counter的值

用一個整數陣列A表示所有的operation，設A之長度為M，A中各個元素大小介於```[1, N+1]```之間。
其中：
1. ```A[K] = X```，且$1\le X\le N$表示第X個計數器遞增1。
2. ```A[K] = N + 1```，則代表全部的計數器都變成最大那個

解題思路：
維護兩個max值，分別max_cur和max_prev，
max_cur用來記錄從```A[0]～A[N]```所有counter中最大的值。
max_prev代表遇到上一個N + 1(設最大值的那個operation)時，那個時候的最大值。

另外建立一個array叫做counter，每次遇到要遞增時，對應index的counter值加1。

當我們遇到```A[K] = N + 1```時，要令max_prev = max_cur，代表這之後遇到的計數器如果小於max_prev都要進行更新，最少要等於max_prev。

而當我們遇到```A[K] = X```時，先比較第X個計數器有沒有小於max_prev，若小於，不合理，先更新到max_prev，再把第X個計數器遞增1；
接著，比較第X個計數器的值和max_cur誰比較大，更新max_cur的值。

最後，再另外用一個迴圈，從第1個遍歷到第N個counter，看有沒有不合理的小於max_prev，若有就至少把那個counter的值assign成max_prev。
完成此迴圈後，counter這個array即為解答。

Correctness Performance 100%
解答：
```c
vector<int> solution(int N, vector<int> &A) {
    vector<int> counter(N, 0);
    int max_cur, max_prev;
    max_cur = max_prev = 0;

    for(auto &X: A)
    {
        if(X <= N)
        {
            if(counter[X - 1] < max_prev)
                counter[X - 1] = max_prev;
            ++counter[X - 1];
            max_cur = (max_cur > counter[X - 1])? max_cur : counter[X - 1];
        }
        else
        {
            max_prev = max_cur;
        }
    }
    for(auto &c: counter)
    {
        if(c < max_prev)
            c = max_prev;
    }
    return counter;
}
```

## 9. MissingInteger

給定一整數陣列A，陣列中數字範圍落在```[−1,000,000..1,000,000]```，
計算從未出現過在A中的最小正整數，例如：
```
A = [1, 3, 6, 4, 1, 2], return 5
A = [1, 2, 3], return 4
A = [−1, −3], return 1
```

基本解題思路：
用hash table記錄每一個數字是否出現過，key為該數字，value為true/false，
接著從1開始，每次遞增1，一直檢查到A之長度中是否有沒有出現過的數字，
若沒有該數字，則該圈的index即為答案。
若從```hash[1] ~ hash[len]```都有數字存在，返回len + 1為答案。

Correctness Performance 100%
解答：
```c
#include <unordered_map>
int solution(vector<int> &A) {

    unordered_map<int, bool> hash;

    int len = A.size();

    for(int &i : A)
    {
        hash[i] = true;
    }

    for(int i = 1; i <= len; ++i)
    {
        if(!hash[i])
            return i;
    }

    return len + 1;
}
```

## 10. PermCheck

給定一個非空整數陣列A，長度為N。
若A中的存的數字為```1 ~ N```的一種排列，如：
```
A[0] = 4
A[1] = 1
A[2] = 3
A[3] = 2
```
則返回true，若不是一種排列，如：
```
A[0] = 4
A[1] = 1
A[2] = 3
```
因為缺少2這個數字，返回false。

:star:特別需要注意的是，測資當中可能會有A中所有元素和加總等於```1 ~ N```之總和的情形。
例如：
```
A[0] = 4
A[1] = 2
A[2] = 2
A[3] = 2
```
雖然$4 + 2 + 2 + 2 = 8 = \frac{(1+4)\times 4}{2}$，但其實並不是一個permutaion。

解題思路：
先用梯形公式計算若為permutaion，那sum應該是多少；
接著遍歷A一次，把實際的加總算出來，並且在過程中用一個hash table來紀錄是否有重複出現過的數字，若有重複出現過，返回false。
最後，若sum == actual_sum，則為true。
這邊不用numeric中的accumulate而是手動遍歷原因，就是怕會有總和相等但答案為非的情況。

Correctness Performance 100%
解答：
```c
#include <unordered_map>
int solution(vector<int> &A) {
    unsigned int len = A.size();
    unsigned long long sum = (1llu + len) * len / 2;
    unsigned long long actual_sum = 0;
    unordered_map<int, bool> once;
    for(int &i: A)
    {
        if(!once[i])
            once[i] = true;
        else
        {
            return false;
        }
        actual_sum += i;
    }
    return (sum == actual_sum)? true : false;
}
```

## 11. CountDiv

給定int A, int B, int K三個整數。
其中$A\le B$，求```[A, A+1, A+2, ..., B]```這個區間中有幾個數能被K整除？

解題思路：
![](https://i.imgur.com/ulZtYD2.png)
就是種樹問題，我們先把上下界調整到都可以被K整除的狀態，
然後去算要恰好分隔這些區間，總共需要幾棵樹，由於整個區間要被樹包住，所以最終答案要+1。

Correctness Performance 100%
解答：
```c
int solution(int A, int B, int K) {
    return ((B - (B % K)) - (A + (K - (A % K)) % K)) / K + 1;
}
```

### 另一種想法，更加簡潔：
![](https://i.imgur.com/w5uwuat.png)
但要記得這樣算出來的是A~B之間可以分成幾等分的K，
因此還需要判斷A本身有沒有被K整除：
![](https://i.imgur.com/77bzfow.png)
若較小的數可以被K整除，代表答案還要再加1，不然會少算A本身也是可以被K整除。
畢竟問題問的是區間中可以被K整除的數有幾個，而不是可以劃分成幾等分（i.e. 種樹問題）。

Correctness Performance 100%
解答：
```c
int solution(int A, int B, int K) {
    return B / K - A / K + ((A % K) == 0);
}
```

## 加映：採蘑菇問題，在codility prefix sum的[PDF檔](https://codility.com/media/train/3-PrefixSums.pdf)

問題敘述：
給定一個非空整數陣列A，A中有n個正整數。
一個採蘑菇人從位置$K$出發，他的體力足夠他走$m$步的距離，
而A中的每個元素對應的數字代表在該index下的能採集到的蘑菇數量，
若採蘑菇人每走一步就會把該處對應的蘑菇都採完，那請問他能採到的蘑菇最大數量為何？

首先進行問題之思考：他應該怎麼移動才能採到最多的蘑菇？

因為我們允許採蘑菇人來回移動，他可以先走向另一邊，在往另一邊走。
但採過的蘑菇並不會馬上長回來，因此想要達到最大數量，一定是只走一個方向，或是僅有一次的改變方向。

他可以探索的方向如下圖所示：
![](https://i.imgur.com/YkNj5Ow.png)

然而當採蘑菇人改變方向時，必定要往回走超過原本的位置$K$才能採到更多蘑菇；
假設我們一開始先往左邊走$p$步，接著往右邊走，那麼先往左在回到位置k總共會消耗$2\times p$步

![](https://i.imgur.com/fcZEPmA.png)

因此只剩下$m - 2\times p$步可以往右邊繼續採蘑菇。

同理：
若先往右邊走p步，當回到位置K往左邊走時也同樣只剩下$m - 2\times p$步可以往左走：

![](https://i.imgur.com/CBxmR2X.png)

因此我們只要計算：
1. $[k - p, k + m - 2\times p]$的區間有多少蘑菇，$k = 0, 1, 2, ..., m$
2. $[k + p, k - (m - 2\times p)]$的區間有多少蘑菇，$k = 0, 1, 2, ..., m$

並找出最大的值即可。

程式碼：
```python=
def mushrooms(A, k, m):
	n = len(A)
	result = 0
	pref = prefix_sums(A)
	
	# 先往左邊走
	for p in xrange(min(m, k) + 1):
		left_pos = k - p
		right_pos = min(n - 1, max(k, k + m - 2 * p))
		result = max(result, count_total(pref, left_pos, right_pos))
		
	# 先往右邊走
	for p in xrange(min(m + 1, n - k)):
		right_pos = k + p
		left_pos = max(0, min(k, k - (m - 2 * p)))
		result = max(result, count_total(pref, left_pos, right_pos))
	return result
```
其中要注意邊界的問題：
如：
```python
right_pos = min(n - 1, max(k, k + m - 2 * p))
```
是希望往右走到的最終位置不要超過右邊邊界，而之中的
```python
max(k, k + m - 2 * p)
```
則是因為上述程式中，單純往左走(one-direction)沒有回頭往右的計算也被我們寫在了一起，若沒有用max的話會少算蘑菇的數量（i.e. $2\times p$超過$m$，走不回來）。

至於為什麼for loop是
```python
for p in xrange(min(m, k) + 1)
```
是因為若k比m還小，當你```p > k```就會走到-1那邊而不合理了(segmentation fault)。

往右邊走的話，也是同理。
```python
for p in xrange(min(m + 1, n - k))
```
若```p >= (n - k)```，則往右走p步會到達```k + n - k```而使位置超過n-1，同樣要注意邊界問題。

## 12. GenomicRangeQuery

題目：在基因序列中尋找對應的最小核苷酸。
給定一組基因字串序列S，內容由'A'、'C'、'G'和'T'所組成，每個字元的影響因子不同，分別是
```
'A': 1
'C': 2
'G': 3
'T': 4
```
同時，給予兩個長度為M之整數陣列P和Q，分別代表M個子問題中的起始位置和結束，如：
```
P[0] = 2    Q[0] = 4
P[1] = 5    Q[1] = 5
P[2] = 0    Q[2] = 6
```
上述例子即：
```
第0個子問題，要查從S[2]~S[4]之中最小的impact factor
第1個子問題，要查從S[5]~S[5]之中最小的impact factor
第2個子問題，要查從S[0]~S[6]之中最小的impact factor
```
題目要求你回傳任兩個字元之間區段，最小的impact factor為何。

若用暴力解：
```c
vector<int> solution(string &S, vector<int> &P, vector<int> &Q) {
    vector<int> impact;
    for(auto &c: S)
    {
        switch(c)
        {
            case 'A':
                impact.emplace_back(1);
                break;
            case 'C':
                impact.emplace_back(2);
                break;
            case 'G':
                impact.emplace_back(3);
                break;
            case 'T':
                impact.emplace_back(4);
                break;
        }
    }

    vector<int> ans;

    for(int i = 0; i < P.size(); ++i)
    {
        int start = P[i];
        int end = Q[i];
        int minimal = impact[start];
        while(start != end)
        {
            minimal = min(impact[++start], minimal);
        }
        ans.emplace_back(minimal);
    }

    return ans;
}
```
所需時間為O(N * M)過不了大測資。

解題思路：
用空間換取時間，我們想要做的事情就是知道「```P[0] ~ Q[i]```中間到底有沒有出現過'A'、'C'、'G'或'T'」。
因此我們可以為每個字元分別建造專屬的陣列：

![](https://i.imgur.com/h2HcNWJ.png)

建造方式為：
```
Pre_A[i] = 第i個位置時，前面s[0] ~ s[i]總共出現過幾次'A'
```
其他的C、G、T也以此類推。
可以利用一個conter就把Pre_A建造完成(每次遇到'A'則counter++)：

![](https://i.imgur.com/w6DBnG6.png)

這樣一來，只要透過前後相減，就可以迅速知道A有沒有出現在```s[i] ~ s[j]```之中，
不過為了計算方便，建議將所有index全部向後移一格，讓第一欄全部為0：
![](https://i.imgur.com/aGfvdY8.png)

例如：
```
P[0] = 2
Q[4] = 4
Pre_A[4 + 1] - Pre_A[2] = 1 - 1 = 0
```
0代表在這段區間沒有出現過'A'，再看另外一個例子：
```
P[0] = 5
Q[4] = 5
Pre_T[5 + 1] - Pre_T[5] = 1 - 0 = 1
```
因為結果大於0，代表在這段區間```s[5] ~ s[5]```內T至少出現過1次以上。
這樣就可以快速地求出這段區間內所有的字元究竟出現過幾次，也可以立刻求出這段區間內最小的impact factor了。

總共時間複雜度為：
建造表格O(n) + 遍歷P和Q陣列O(m) = O(n + m)

Correctness Performance 100%
解答：
```c
vector<int> solution(string &S, vector<int> &P, vector<int> &Q) {
    int len = S.size();
    vector<int> pre_A(len, 0), pre_C(len, 0), pre_G(len, 0), pre_T(len, 0);
    for(int i = 1; i <= len; ++i)
    {
        pre_A[i] = pre_A[i - 1];
        pre_C[i] = pre_C[i - 1];
        pre_G[i] = pre_G[i - 1];
        pre_T[i] = pre_T[i - 1];

        switch(S[i - 1])
        {
            case 'A':
                ++pre_A[i];
                break;
            case 'C':
                ++pre_C[i];
                break;
            case 'G':
                ++pre_G[i];
                break;
            // The first 3 vectors are enough, We built this vector to explain your strategy though.
            // T was never used.
            case 'T':
                ++pre_T[i];
                break;
        }
    }

    vector<int> result;

    for(size_t i = 0; i < P.size(); ++i)
    {
        int start = P[i];
        int end = Q[i] + 1;
        if((pre_A[end] - pre_A[start]) > 0)
            result.emplace_back(1);
        else if((pre_C[end] - pre_C[start]) > 0)
            result.emplace_back(2);
        else if((pre_G[end] - pre_G[start])> 0)
            result.emplace_back(3);
        else
            result.emplace_back(4);
    }
    return result;
}
```
註：其實pre_T是用不到的，因為當前三個(A、C、G)都沒出現的話，必然是T出現，不過為了解釋還有其他用途(如計算出現次數)，這邊依然把它建立出來給你參考。

## 13. MinAvgTwoSlice

參考資料：[這裡](https://blog.csdn.net/dear0607/article/details/42581149)

給定一個整數陣列A，A中有N個整數，並且題目定義了何謂一個「slice」。
$$
slice(P, Q) = \frac{A[P] + A[P+1] + ... + A[Q]}{Q - P + 1}
0 \le P < Q < N
$$
即slice(P, Q)為```A[P]~A[Q]```之平均。

題目要求：
在A中找出所有slice中最小的那個，並且回傳它的起始位置，即P這個數。
如：
```
A = [4, 2, 2, 5, 1, 5, 8]

則 
slice (1, 2) = (2 + 2) / 2 = 2
slice (3, 4) = (5 + 1) / 2 = 3
slice (1, 4) = (2 + 2 + 5 + 1) / 4 = 2.5
...

其中
slice (1, 2)為最小，故回傳它的起始位置1。
```

解題思路：

最小的平均值，必定是由相鄰的2個元素，或3個元素所組成。

原因：
當你找到一個由4個元素所組成的slice時，這個slice必定可以拆成更小的子slice。
(也就是說長度為4以上的slice根本不必考慮，因為它的子slice之平均會更小或等於)

證明：
長度4以上的slice的平均值若為最小，則它一定有一個長度為2的子slice跟它平均值相同。

假設```(a + b + c + d) / 4```為最小，則它可以拆成2個子slice。
```(a + b) / 2```和```(c + d) / 2```。

那根據三一律，```(a + b) / 2```必定是大於、小於或等於```(c + d) / 2```這三種情況之一。
若```(a + b) / 2``` > ```(c + d) / 2```，則
$$
\frac{a + b + c + d}{4} = \frac{\frac{a + b}{2} + \frac{c + d}{2}}{2} > \frac{a + b}{2}
$$
與假設矛盾。
反過來說大於```(a + b) / 2``` < ```(c + d) / 2```也是相同的情況。

若是等於的情況，那麼我們返回的數字都會是a的index，對問題不造成影響。

知道了上面這個證明之後，解題變得很簡單，
我們只要去計算每個 $slice(P, Q),\quad Q-P\in \{1, 2\}$ 就可以了。
並且用兩個值min_P, min_val，從A的頭遍歷到尾，就可以結束了。
由於每次只會算```A[P]、A[P+1]之平均```和```A[P]、A[P+1]、A[P+2]之平均```，
從頭到尾只需要O(n)的時間。

Correctness Performance 100%
解答：
```c
#include <limits>
int solution(vector<int> &A) {
    double min_val;
    int min_p;
    min_val = numeric_limits<double>::max();
    min_p = 0;
    double slice;
    for(size_t i = 0; i < A.size() - 1; ++i)
    {
        slice = static_cast<double>((A[i] + A[i + 1])) / 2.;
        if(slice < min_val)
        {
            min_val = slice;
            min_p = i;
        }
        if(i < A.size() - 2)
        {
            slice = static_cast<double>((A[i] + A[i + 1] + A[i + 2])) / 3.;
            if(slice < min_val)
            {
                min_val = slice;
                min_p = i;
            }
        }
    }
    return min_p;
}
```

## 14. PassingCars

給定一個長度為N之非空整數陣列A，由0跟1組成。
當```A[i] = 0```的時候代表車子向東行駛；
當```A[i] = 1```的時候代表車子向西行駛。

定義了一個叫做cars pair的東西，
$\text{cars pair} = (P, Q),\quad 0\le P < Q < N$

而每一個往東的車輛可以跟「i時間點之後」往西的車輛組成pairs，
但是往西的車輛只能跟「i時間點之前」往東的車輛。

也就是說，例如：
```
A[0] = 0
A[1] = 1
A[2] = 0
A[3] = 1
A[4] = 1
```
其中第0個時間點往東之車輛，他可以跟後面時間點1、3、4往西的車輛組成cars pair。
而第2個時間點往東之車輛，只可以跟後面時間點3、4往西的車輛組成cars pair，而時間點1之往西的車輛因為P > Q所以不能配對。

綜上所述，該A陣列共有5個cars pair，如下：
```
(0, 1), (0, 3), (0, 4), (2, 3), (2, 4)
```
試寫一演算法能有效計算出所有cars pair的數量。

解題思路：
可以跟[GenomicRangeQuery](#12-GenomicRangeQuery)用一樣的演算法，計算出每個時間點共有多少個1和0，
但由於我們這邊可以用更簡單的方式，遍歷整個A一次就可以解決。

基本想法是，當我們遇到1的時候，只要知道他前面有幾個0，就知道當下這個1可以提供多少數量的cars pair，
例如遇到```A[3] = 1```，它前面共有```A[0] = 0```和```A[2] = 0```，所以可以提供2組的cars pari。
有了這個想法之後，題目就變得很簡單，只要維護一個counter來計算當前0的個數，每次遇到1的時候就把counter的加進result之中，最終回傳result即可。
另外要小心，題目要求當cars pair數量大於1000000000時要回傳-1，記得用unsigned long long或size_t類型的變數。

Correctness Performance 100%
解答：
```c
int solution(vector<int> &A) {
    size_t counter_P = 0;
    size_t result = 0;
    for(auto &a: A)
    {
        if(a == 0)
            ++counter_P;
        else
            result += counter_P;
    }

    return (result > 1000000000) ? -1 : result;
}
```

## 15. Distinct

問一個整數陣列A中有幾個不重複的數字？

若想使用C++的std來做，可以用sort + unique。
sort可以排序一個vector，
unique可以刪除跟前一個元素重複的元素，並返回一個iterator，暫時稱它為result，代表從```[first, result)```之間都沒有重複的元素。
:star: unique並不會把元素真正的刪掉，詳情可以參考[筆記](https://hackmd.io/e00_PVmZQYK9N1CkbIZxtw?view)，
所以vector經過unique之後只是把重複的元素移動到後端，size()不會變。

解法：
```c
#include <algorithm>
int solution(vector<int> &A) {
    sort(A.begin(), A.end());
    vector<int>::iterator res = unique(A.begin(), A.end());
    return res - A.begin();
}
```
這樣其實就可以雙100%了。

但是其實這題可以用O(n)來解決，
Correctness Performance 100%
解答：
```c
#include <unordered_set>
int solution(vector<int> &A) {
    unordered_set<int> cnt;
    for(auto &a: A)
        cnt.insert(a);
    return cnt.size();
}
```

## 16. MaxProductOfThree

給定一非空整數陣列A，其長度N至少為3。
試求A中任三數相乘之最大乘積，如：
```
A[0] = -3
A[1] = 1
A[2] = 2
A[3] = -2
A[4] = 5
A[5] = 6
```
可知：
```
A[0] * A[1] * A[2]，乘積為 −3 * 1 * 2 = −6
A[1] * A[2] * A[4]，乘積為 is 1 * 2 * 5 = 10
A[2] * A[4] * A[5]，乘積為 is 2 * 5 * 6 = 60
```
故：
最大乘積為60。

解題思路：

其實只要對整個A進行sort，然後三個、三個地計算乘積，取最大的即可。
:star:但是要注意到還有一種最大值的可能性：兩個很大的負數相乘再乘上一個正數。

因此可以寫成如下程式：
```c
#include <algorithm>
int solution(vector<int> &A) {
	sort(A.begin(), A.end());
    int maximum = A[0] * A[1] * A[A.size()-1];
    for(size_t i = 0; i < A.size() - 2; ++i)
    {
        int product = A[i] * A[i + 1] * A[i + 2];
        maximum = (maximum >= product)? maximum: product;
    }
    return maximum;
}
```

其中，A經過sort過後由小排到大。

但其實還可以再度簡化，
因為仔細想想，最大值只會有四種可能：
1. 三個很大的正數。
2. 兩個很大的負數相乘再乘上一個正數。
3. A中全部都是負數，取最小的三個負數。
4. A中只有兩個正數，取最小的負數跟這兩個正數相乘
所以我們只需要比較：
```
n = A.size()
A[0] * A[1] * A[n - 1]	// 兩負數一正數的情況
A[n - 1] * A[n - 2] * A[n - 3]	// 三正數、三負數，或是A中只有兩個正數的情況
```
僅需要將上述兩者進行比較即可。
另外，由於題目已知A中數字會介於[-1000, 1000]之間。
可以改用counting sort達成O(n)之排序。

Correctness Performance 100%
解答：
```c
#include <unordered_map>
int solution(vector<int> &A) {
    unordered_map<int, int> cnt;
    for(auto &a : A)
    {
        ++cnt[a];
    }
    vector<int> A_sorted;
    for(int i = -1000; i <= 1000; ++i)
    {
        for(int j = 0; j < cnt[i]; ++j)
            A_sorted.emplace_back(i);
    }
    int maximum = max(A_sorted[0] * A_sorted[1] * A_sorted[A.size()-1], A_sorted[A.size()-1] * A_sorted[A.size()-2] * A_sorted[A.size()-3]);
    return maximum;
}
```

## 17. NumberOfDiscIntersections

在一平面上畫出N個圓盤：
![](https://i.imgur.com/s1EHkCd.png)

給定一個整數陣列A，```A[i] = radius```表示在座標(i, 0)的地方畫一個半徑為```A[i]```的圓。
求這些圓盤種共有幾個交集？
若答案超過10000000就回傳-1。

:star:Assuming that the discs contain their borders. 注意圓盤的邊緣彼此碰到也要算進去。

要注意是兩兩之間算一個交集。所以要做的事情就是計算任兩圓盤之間有沒有接觸到，有接觸到答案就加1。

:star:由於A中之整數介於```[0, 2147483647]```，需要注意溢位的問題，有相加計算的部分請用long long。

先嘗試暴力法，先將所有圓的左邊界都求出來，
接著再掃過一遍A中所有的圓點，並計算圓點加上它的半徑有沒有超過或接觸到某個在其右手邊的圓之左邊界。
這個方法要建立左邊界之陣列$O(n)$加上每個圓點都跟自己右手邊的圓比較一次$O(n^2)$

時間複雜度為$O(n^2)$：
```c
int solution(vector<int> &A) {
    vector<int> left_bound;
    int len = A.size();
    for(int i = 0; i < len; ++i)
    {
        left_bound.emplace_back(i - A[i]);
    }
    
    int result = 0;

    for(long long i = 0; i < len; ++i)
    {
        for(int j = i + 1; j < len; ++j)
        {
            if(i + A[i] >= left_bound[j])
                ++result;
        }
    }
    if(result > 10000000) return -1;
    return result;
}
```

解題思路：

因為肯定是要遍例陣列一次的，時間複雜度必然是$O(N\times X)$，
現在問題就在於如何降低這個$X$。

依然是上述的邏輯，但是我們每次去搜尋低於當前右邊界的左邊界時，也就是這行：
```for(int j = i + 1; j < len; ++j)```
其實是可以加快的。

因為我們只要先將所有圓的左邊界排好序，接著就可以用「二分搜尋法(binary search)」，來找小於當前右邊界的左邊界總共有幾個。

這樣的話搜尋小於自己邊界有幾個這件事情就可以被我們壓縮到$log(n)$，
因此時間是$O(N\log{N})$，而前面作的排序amortized下來剛好也是$O(N\log{N})$，
所以總時間複雜度就是$O(N\log{N})$。

然而由於這樣找出來的數量肯定會包含當前這個圓自己的左邊界，所以要 -1 來扣掉；
而且在左手邊的圓的左邊界，也肯定會比自己的右邊界來的小，所以要用 -i 來扣掉，
因為我們只是要去計算 $disc A \cap disc B$ 成不成立，且 $A < B$ 的部分而已。

以下為圖例：

![](https://i.imgur.com/PYZnfsq.png)


若要使用STL來解的話，
```upper_bound(first, last, val)```會利用二分搜尋找出第一個不小於val的位置；
```distance(first, last)```會計算first到last中間有幾個元素。

```c
#include <algorithm>
int solution(vector<int> &A) {
    vector<int> left_bound;
    
    int len = A.size();
    for(int i = 0; i < len; ++i)
    {
        left_bound.emplace_back(i - A[i]);
    }
    
    sort(left_bound.begin(), left_bound.end());


    int result = 0;
    for(long long i = 0; i < len; ++i)
    {
        long long right_bound = i + A[i];
        vector<int>::iterator it = upper_bound(left_bound.begin(), left_bound.end(), right_bound);
        result += distance(left_bound.begin(), it) - 1 - i;
    }
    return (result > 10000000)? -1: result;
}
```

如果要自己刻binary search，以下是遞迴版本：
```c
#include <algorithm>
int binary_search(vector<int> &A, long long num, int l, int r)
{
    if(A[r - 1] <= num)
    {
        while((r < A.size()) && (A[r - 1] == A[r]))
            ++r;
        return r;
    }

    int mid = l + ((r - l) >> 1);

    int result = 0;
    // 左半邊全部小於num，往右邊搜
    if ((l < mid) && (A[mid - 1] <= num))
    {
        result = binary_search(A, num, mid, r);
    }
    // 左半邊有大於num的數，往左邊搜
    else
        result = binary_search(A, num, l, mid);
    return result;
}

int solution(vector<int> &A) {
    vector<int> left_bound;
    int len = A.size();
    for(int i = 0; i < len; ++i) 
    {
        left_bound.emplace_back(i - A[i]);
    }
    sort(left_bound.begin(), left_bound.end());

    int result = 0;
    for(long long i = 0; i < len; ++i)
    {
        result += binary_seach(left_bound, i + A[i], 0, len);
        result -= i + 1;
        if(result > 10000000)
            return -1;
    }
    return result;
}
```
:star:要特別注意，```binary_seach(vector<int> &A, long long num, int l, int r)```之中的A一定要用pass by reference，否則會執行copy constructor，時間需要O(n)。

如果是迴圈版本：
```c
int binary_search(vector<int> &A, long long num)
{
    int len = A.size();
    if(num < A[0])
        return 0;
    else if(num >= A[len - 1])
        return len;
    
    int mid;
    int l = 0;
    int r = len;
    while(l <= r)
    {
        mid  = l + ((r - l) >> 1);
        if(A[mid] == num)
        {
            while(A[mid] == A[mid + 1])
                ++mid;
            return mid + 1;
        }
        if(num < A[mid])
            r = mid - 1;
        else if(num > A[mid])
            l = mid + 1;
    }
    return l;
}
```

### 更快的解法，掃描線：

參考[這裡](https://stackoverflow.com/questions/4801242/algorithm-to-calculate-number-of-intersecting-discs)
首先，利用每個disc的左邊界當作事件的start，右邊界當作事件的end。
先一一計算每個點總共有多少事件發生，如圖：

![](https://i.imgur.com/wuJDA9L.png)

首先我們要維護一個counter，假設它叫作t，用來記錄當前掃描線上總共有多少進入左邊界的disc。
接著我們用掃描線遍歷這條數線上的所有點，
當走到第i個點時，如果```start[i] > 0```代表存在disc的左邊界在此點上，
這時，它們會跟先前已經進入左邊界但還沒離開右邊界的disc相交。
因此我們需要將```result += t * start[i]```，計算出現在進入的disc跟之前進入的disk兩兩相交有幾個。

除此之外，在第i個點進入的disc們本身也會互相相交，
例如：有5個disc同時在第i個點進入左邊界，那這5個disc應該也會有$\begin{pmatrix} 5 \\ 2 \end{pmatrix} = 1+2+3+4+5 = \frac{5\times 4}{2}$個相交，
即我們應該還要加上```result += start[i] * (start[i] - 1) / 2```才對。

然後，這個點上共有幾個進入左邊界要加入t之中，並且計算共有幾個disc離開右邊界，
故```t += start[i] - end[i]```，維持t在正確的disc數。

由於只需要從頭到尾掃過兩次，時間是$O(n)$

Correctness Performance 100%
解答：
```c
int solution(vector<int> &A) {
    int len = A.size();

    vector<int> start(len, 0), end(len, 0);
    for(int i = 0; i < len; ++i)
    {
		// 左邊界低於0的都當成0
        int left_bound = (i > A[i])? i - A[i] : 0;
		// 右邊界高於len - 1的都成len - 1
        int right_bound = (len - i > A[i])? i + A[i] : len - 1;
        ++start[left_bound];
        ++end[right_bound];
    }

    int result = 0;
    int activate = 0;
    for(int i = 0; i < len; ++i)
    {
        if(start[i] > 0)
        {
            result += activate * start[i];
            result += start[i] * (start[i] - 1) / 2;
        }
        activate += start[i] - end[i];
    }
    if(result > 10000000)
        return -1;
    return result;
}
```
:star:要特別注意溢位的問題，如```int right_bound = (len - i > A[i])? i + A[i] : len - 1;```這一句，盡量用相減來取代相加。
如果已經知道```len - i > A[i]```成立，那 $i$ 加上它的半徑肯定不會超過 $len$，因為題目已知 $len$ 必定小於100000，也就沒有溢位的問題。

## 18. Triangle

給定一個整數陣列A，長度為N。
若將A之中的整數視為三角形之三邊長，檢查是否存在任意三個邊長能組合成一個合法的三角形，即存在：

![](https://i.imgur.com/cthROlF.png)

$$
\begin{cases} b+c\gt a \gt |b-c| \\ c+a\gt b \gt |c-a| \\ a+b\gt c \gt |a-b| \end{cases}
$$

i.e. 三角不等式，任兩點之間最短距離為直線的距離。

例如：
```
A[0] = 10    A[1] = 2    A[2] = 5
A[3] = 1     A[4] = 8    A[5] = 20
```
因為存在：
```
A[0] + A[2] > A[4]
A[2] + A[4] > A[0]
A[4] + A[0] > A[2]

所以(0, 2, 4)是一組合法三角形之邊長
```

陣列A中之整數範圍由```[-2147483648, 214748364]```組成，A的最大長度不超過100000。

解題思路：

對A作逆排序，由大排到小。
檢查是否存在```A[i] < A[i + 1] + A[i + 2]```即可。

為什麼不用檢查另外兩種情況```A[i + 1] < A[i + 2] + A[i]```、```A[i + 2] < A[i] + A[i + 1]```？

因為已知```A[i]```為最大，當

$A[i] < A[i + 1] + A[i + 2]$ 成立，

則 $A[i + 1] < A[i] < A[i + 1] + A[i + 2] < A[i + 2] + A[i]$ 和

$A[i + 2] < A[i] < A[i + 1] + A[i + 2] < A[i] + A[i + 1]$ 也同時成立。

故只要檢查第一種狀況即可。

Correctness Performance 100%
解答：
```c
#include <algorithm>
int solution(vector<int> &A) {
    sort(A.begin(), A.end(), greater<int>());
    int len = A.size();
    for(int i = 0; i < len - 2; ++i)
    {
        // 等價A[i] < A[i + 1] + A[i + 2]
        if(A[i] - A[i + 1] < A[i + 2])
            return 1;
    }
    return 0;
}
```
:star:```A[i] - A[i + 1] < A[i + 2]```是為了避免溢位。


## 19. Brackets

判斷字串中括弧的合法性。
有三種括弧：```"("、"{"、"["```，分別對應的是```")"、"}"和"]"```

合法情況為：
1. S為空；
2. S的形式為```"(U)"```或```"[U]"```或```"{U}"```，U是已知的合法括弧字串；
3. S的形式為```"VW"``` ，而V和W是已知的合法括弧字串。

字串S由N個字元組成，每個字元都是一個括弧，判斷S是否符合上述三種情況。
若是，返回1；若不是，返回0。

基本解題思路：
利用Stack，遇到左半邊的括弧就push，遇到右半邊的就觀察頂部是不是剛好對應的另一半，並且pop出來；
最後觀察Stack是否為空。

Correctness Performance 100%
解答：
```c
#include <stack>
#include <unordered_map>
int solution(string &S) {
    stack<char> st;
    unordered_map<char, char> brackers_dict{{'(', ')'}, {'[', ']'}, {'{', '}'}};
    for(auto &s: S)
    {
        if(s == '(' || s == '[' || s == '{')
            st.push(s);
        else
        {
            if(s != brackers_dict[st.top()])
                return 0;
            st.pop();
        }
    }
    if(st.empty())
        return 1;
    return 0;
}
```

## 20. Fish

一條河有N隻魚，每隻魚的編號為$0\le P < N$，且當P < Q時，編號P之魚在編號Q之魚的上游。
給定兩整數數列A和B，其中：

```
A[P]：由各種數字組成，表示第P隻魚的體型大小，A中元素不重複
B[P]：僅由0和1組成，表示第P隻魚往哪個方向游，0表示往上游，1表示往下游
```

若兩隻魚互相緊鄰，且他們互相往對方的位置游過去，那只有一隻魚會存活下來，因為體型較小的魚會被體型較大的魚給吃掉，例如：
```
P = Q - 1
B[P] = 1, B[Q] = 0

--

If A[P] > A[Q]，則P會把Q吃掉，且P繼續往下游。
If A[P] < A[Q]，則Q會把P吃掉，且Q繼續往上游。
不存在 == 的情形，因為A中美個元素獨一無二。
```

假設所有的魚移動速度相同，也就是說，如果兩隻魚游的方向相同，則牠們不可能會相遇。
試計算最後還剩多少魚存活，舉例來說：
```
A[0] = 4    B[0] = 0
A[1] = 3    B[1] = 1
A[2] = 2    B[2] = 0
A[3] = 1    B[3] = 0
A[4] = 5    B[4] = 0
```
代表除了編號1的魚以外都往上游，且編號1的魚會把編號2的魚吃掉，編號1的魚繼續往下游，接著又遇到編號3的魚，再度把3號吃掉。
最後，當牠遇到編號4的魚時，換成1號魚被4號魚吃掉。
所以，最終會剩下兩隻魚，編號0和編號4。

寫一個函式，在給定A、B兩長度為N之非空整數陣列時，計算最終會剩下幾隻魚存活。
對上面的例子而言，就是return 2。

解題思路：

利用Stack，從最上方的魚開始遍歷一次，遇到往下游的魚(```B[P] == 1```)就把牠的size給push進去，
而接下來遇到往上游的魚就把pop出來看誰會吃掉誰，有兩種情況：
1. 往上游的魚吃掉往下游的魚，再pop下一個stack中的魚
2. 往下游的魚吃掉往上游的魚，那就再把牠push回去

若遇到```B[P] == 0```且Stack為空的情況，答案+1，這是計算往上游的魚有多少隻會存活下來。
最後，把答案加上Stack的size，這是計算往下游的魚會有多少隻存活下來。

Correctness Performance 100%
解答：
```c
#include <stack>
int solution(vector<int> &A, vector<int> &B) {
    stack<int> S;
    int len = A.size();
    int ans = 0;
    for(int i = 0; i < len; ++i)
    {
        if(B[i] == 1)
            S.push(A[i]);
        else
        {
            while(!S.empty() && S.top() < A[i])
                S.pop();
            if(S.empty())
                ++ans;
        }
    }
    ans += S.size();
    return ans;
}
```

## 21. Nesting

上述[第19題Brackets](#19-Brackets)的簡化版本；

對括弧進行配對，僅有'('和')'兩種，合法的括弧方式回傳1，不合法的回傳0。

解題思路：

利用Stack的想法，但由於只有小括弧一種型態，所以不需要真的造一個Stack出來。
我們只需要紀錄Stack的size即可，如果遇到'('就push進去，所以size++，遇到')'就size--；
若過程中遇到負數代表不合法，回傳0；
最後size恰為0代表每一個'('都有配到')'，且剛好配完，合法所以回傳1；
若size不為0，代表Stack非空，有剩下的'('沒有配到，不合法所以回傳-1。

這樣的話我們僅需O(1)的space complexity而不需要O(n)的空間。

Correctness Performance 100%
解答：
```c
int solution(string &S) {
    int size = 0;
    for(auto &s : S)
    {
        if(s == '(')
            size += 1;
        else
            size -=1;
        if(size < 0)
            return 0;
    }
    return (size == 0)? 1: 0;
}
```

:star:codility的[教學文件](https://codility.com/media/train/5-Stacks.pdf)還是很有用的，每次練習解題前最好可以看一看該單元的pdf檔。

## 22. StoneWall

建造一面長度為N的牆，其中第i到i+1公尺的牆壁高度必須為```H[i]```，特別是```H[0]```代表這座牆最左端的高度，H[N-1]則為牆壁最右端的高度。
牆面必須由矩形的石頭來建成，厚度不重要，請寫一個函式計算出最少必須用幾塊石頭才能建成這面牆壁。

例如：
```
H[0] = 8    H[1] = 8    H[2] = 5
H[3] = 7    H[4] = 9    H[5] = 8
H[6] = 7    H[7] = 4    H[8] = 8
```
要建立的石牆為示意圖：
![](https://i.imgur.com/BwdbqBL.png)

可以拆分成幾塊矩形疊起來，以下是三種不同拆解法：
![](https://i.imgur.com/rnPgU2y.png)

上圖中三種方法可以明顯看出總共用了七塊石頭建成，因此這個函式必須回傳7。

其中，官方提供的拆解法為第一種。

解題思路：

我們採用第一種組成方法；
利用Stack，當Stack的top比當前要蓋的牆高度還高，那代表我們不需要Stack中的那一塊牆，把它pop掉；
一直pop到Stack為空、或是top的牆跟當前要蓋的高度一樣或更低為止，而pop完之後有三種情況：

1. 若top的高度跟當前要蓋的高度一樣，那不需要再加蓋，這一輪pass；
2. 若是top更低，那我們需要在上面加蓋一塊小石頭；
3. 如果Stack為空，那我們要放一塊石頭進去。

Correctness Performance 100%
解答：
```c
#include <stack>
int solution(vector<int> &H) {
    stack<int> s;
    int ans = 0;
    for(auto &h : H)
    {
        while(!s.empty() && h < s.top())
            s.pop();
        if(s.empty() || h > s.top())
        {
            s.push(h);
            ans += 1;
        }
    }
    return ans;
}
```

