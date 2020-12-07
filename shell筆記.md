# shell筆記

## 特殊符號

```#``` 井號(comments)：
用來註解

```~```家目錄(Home)：

```;```分號(Command separator)：
用來把指令分開(跟C語言相同用途)
但是shell可以用換行來分

```;;```連續分號(Terminator)：
用來分割case語句，詳細參考case的用法

```.```點(dot)：
在shell中，一個dot代表目前的目錄，兩個dot代表上層目錄。
如果檔案名稱以 dot 開頭，該檔案就屬特殊檔案，用ls指令必須加上-a選項才會顯示。

```'string'``` 單引號 (single quote)：
單一字串。在引號內的代表變數的$符號，沒有作用，也就是說，他被視為一般符號處理，防止任何變數替換。

```"string"``` 雙引號 (double quote)
被雙引號用括住的內容，將被視為單一字串。它防止萬用字元擴展，但允許變數擴展。在雙引號中的三種特殊字元不被忽略：``` $ \ ` ```，i.e.雙引號會解釋字串的特別意思,

``` `command` ``` 倒引號 (backticks)
在前面的單雙引號，括住的是字串，但如果該字串是一列命令列，會怎樣？答案是不會執行。要處理這種情況，我們得用倒單引號來做。如：
```fdv=`date +%F`echo "Today $fdv"```
在倒引號內的 date +%F 會被視為指令，執行的結果會帶入 fdv 變數中。


```\```倒斜線：
在交互模式下的escape 字元，放在指令前有取消 aliases的作用；放在特殊符號前則該特殊符號的作用消失；放在指令的最末端，表示指令連接下一行。

```|```管道 (pipeline):star:
pipeline是UNIX系統，基礎且重要的觀念。連結上個指令的標準輸出，做為下個指令的標準輸入。
如：
```who | wc -l```
則who的輸出會變成wc的輸入。(who顯示目前電腦上有哪些使用者，wc代表word count，計算有幾個字元)
註：記住是從左流到右

```!```驚嘆號(negate or reverse)：
反邏輯，與C語言中相同

```:```冒號：
什麼都不做，跟python的pass功能一樣。
也可拿來當成註釋用，```:'   '```可以做多行註解
另外```:```還有以下用法：

```:-```預設值：
```${var:-string}```在「命令行」中若var為空沒有定義，則用string變數取代給var在此陳述句的作用。


```:=```指定預設值：
```${var:=string}```若var為空沒有定義，則用string賦值給var。

```:?```變數是否存在檢查：
如：
```printf "Company is %s\n" "${COMPANY:?Hi!: Company has notbeen defined—aborting}"```
若COMPANY有定義，則輸出Hi!，沒有則輸出Company has notbeen defined—aborting

```:+```覆蓋預設值：
跟```:-```和```:=```完全相反，```${var:+string}```是當var有值時才替換成string。

```:數字```切割字串：
可同時使用兩組，第一組代表起始位置，第二組代表結束位置，如：
```
$ printf "%s\n" "${COMPANY:5}"
light Inc.
$ printf "%s\n" "${COMPANY:5:5}"
light
```

```?```問號(wild card)：
在檔案名稱上扮演的角色是匹配一個任意的字元，但不包含null字元，與sql的底線'_'功能相同。
例如可以利用```ls ?a?```來列出當前目錄下a被兩個字元夾住的檔案名，如：bac.exe、1a2之類的。

```*```星號(wild card)：
在檔案名稱上扮演的角色是匹配任意的一個或多個字元，和sql的'%'相同功能。

```**```雙星號：
次方運算，與python同。

```$```錢號(dollar sign)：
變數替換(Variable Substitution)的代表符號。
如：
```
vrs = 123
echo "vrs = $vrs"
```
印出：
```vrs = 123```

而```$```還有許多用法：

```${}```變數的正規運算式：
如：
```
${parameter:-word}
${parameter:=word}
${parameter:?word}
```

另外：
```$0```、```$1```、```$2```、......、```${10}```、```${11}```
使用script的執行引用變數，如C語言main函數的argv[0]、argv[1]、argv[2]、...
其中指令本身為```$0```

```$#```：
相當於C語言中的argc，代表命令行參數個數。

```$*```：
代表所有的參數，把```$0```、```$1```視為一體，可以用：
```
for var in $*
do
    echo "$var"
done
```
來遍歷。

```$@```：
與```$*```相同，但是當命令行參數有用()括號包起來時，```$@```會把它解開來。

```$$```雙錢號：
代表當前process的PID，如：
```echo $$```可以印出PID

```$?```：
用來查詢上一個命令的退出狀態值(status variable)。
一般來說，一個命令成功執行，輸出0。
失敗，輸出1。

```(   )```指令群組 (command group)：
用括弧將一串連續指令括起來，這種用法對shell來說，稱為指令群組。
如下面的例子：
```(cd ~ ; vcgh=`pwd` ;echo $vcgh)```
指令群組有一個特性，shell會以產生subshell來執行這組指令。
因此，在其中所定義的變數，僅作用於此指令群組本身。

```(( ))```雙重括號：
這組符號的作用與 let 指令相似，用在算數運算上，是bash的內建功能。
let指令用在算術運算上，如```let no+=10```，後面的no不須加上```$```
而雙括號在執行效率上會比使用let指令要好許多。如：
```bash=
#!/bin/bash
((a = 10))
echo -e "inital value, a = $a\n"
((a++))
echo "after a++, a = $a"
```
印出:
```
inital value, a = 10
after a++, a = 11
```

```[   ]```中括弧：
常出現在流程控制中，扮演括住判斷式的作用。
如：
```bash=
if [ "$?" != 0 ] then
	echo "Executes error"
	exit 1
fi
```
這段程式碼代表：上一條命令若不正確執行則print出Executes error，並結束。

另外中括號在規則運算式中擔任類似「範圍」或「集合」的角色，與正規表示式中功能相同。
如：
```rm -r 200[1234]```
上例，代表刪除2001、2002、2003、2004等目錄的意思。

```–```減號 (dash)
在運算式中，她用來表示減法，如：
```expr 10 – 2```

此外也是系統指令的選項符號。
```ls -expr 10 – 2```

在GNU指令中，如果單獨使用 – 符號，不加任何該加的檔案名稱時，代表「標準輸入」的意思。
這是GNU指令的共通選項，譬如下例：
```tar xpvf –```
這裡的 – 符號，代表從標準輸入讀取資料。
不過，在cd指令中則比較特別：
```cd –```
這代表變更工作目錄到「上一次」工作目錄。

```>>、<<、>、<```輸入重導向：

```N > FILE```，其中N是要設定重新導向的檔案代碼，若省略的話預設值為標準輸出的檔案代碼（即 1），而FILE就是要儲存輸出資料的檔案名稱。

N可以為0~2的數字，分別為：
| N   | 名稱         | 常用縮寫 | 預設值 |
| --- | ------------ | -------- | ------ |
| 0   | 標準輸入     | stdin    | 鍵盤   |
| 1   | 標準輸出     | stdout   | 螢幕   |
| 2   | 標準錯誤輸出 | stderr   | 螢幕   |

如：
```ls > output.txt```可以把ls輸出的結果放到output.txt裡面。
若output.txt裡面原本有東西，則全部清空。

```ls >> output.txt```則是把結果附加到原本output.txt的內容後面。

```cat < input.txt```可以把input.txt的內容列印在螢幕上

```: > filename```把filename檔的內容清空，若filename不存在，建立一個，功能與touch指令相同。

其餘詳細可參考[[1](https://blog.gtwang.org/linux/linux-io-input-output-redirection-operators/), [2](https://jerryw.blog/2018/10/18/shell中常用的特殊符號/)]

## 字串操作（長度，讀取，替換）


|                  格式                  | 功能                                                                       |
|:--------------------------------------:| -------------------------------------------------------------------------- |
|            ```${#string}```            | string的長度                                                               |
|        ```${string:position}```        | 在string中, 從位置position開始提取子串                                     |
|    ```${string:position:length}```     | 在string中, 從位置position開始提取長度為$length的子串                      |
|       ```${string#substring}```        | 從變數string的開頭,刪除最短匹配substring的子串                             |
|       ```${string##substring}```       | 從變數string的開頭,刪除最長匹配substring的子串                             |
|       ```${string%substring}```        | 從變數string的結尾,刪除最短匹配substring的子串                             |
|       ```${string%%substring}```       | 從變數string的結尾,刪除最長匹配substring的子串                             |
| ```${string/substring/replacement}```  | 使用replacement, 來代替第一個匹配的substring                               |
| ```${string//substring/replacement}``` | 使用replacement, 代替所有匹配的substring                                   |
| ```${string/#substring/replacement}``` | 如果string的字首匹配substring,那麼就用replacement來代替匹配到的$substring |
| ```${string/%substring/replacement}``` | 如果string的字尾匹配substring,那麼就用replacement來代替匹配到的$substring |


## 指令

### grep
Linux的grep是一個很好用的指令，可以從串流資料或檔案中，使用關鍵字或正規表示法（regular expression）篩選出想要尋找的資料，並且顯示出來
格式如下：
```
grep 關鍵字 檔案1 檔案2 ...
```
例如在/etc/os-release檔案中搜尋Ubuntu關鍵字：
```
# 在 /etc/os-release 檔案中搜尋 Ubuntu 關鍵字
grep Ubuntu /etc/os-release
```
執行結果如下：
```
NAME="Ubuntu"
PRETTY_NAME="Ubuntu 18.04.3 LTS"
```
grep 亦可搭配萬用字元（```*```）同時搜尋多個檔案，
除了搜尋檔案內容之外，亦可搭配管線（pipe）篩選串流資料，例如篩選出含有 network 關鍵字的檔案名稱：
```
# 篩選含有 network 關鍵字的檔案名稱
ls /etc/ | grep network
```
執行結果如下：
```
network
networkd-dispatcher
networks
```