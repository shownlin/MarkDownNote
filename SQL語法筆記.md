# SQL語法筆記

## SELECT
它是用來從資料庫取得資料，這個動作我們通常稱之為查詢 (query)，資料庫依 SELECT 查詢的要求會返回一個結果資料表 (result table)，我們通常稱之為資料集 (result-set)。

```sql=
SELECT table_column1, table_column2, table_column3...
FROM table_name;
```
想一次取得整張資料表裡所有的資料可以在SELECT語句裡用 * 這個特殊符號。
SELECT * FROM table_name;


## WHERE
可以進一步在 SELECT 查詢語句使用 WHERE 關鍵字搭配運算子來取出 "符合條件" 的紀錄值。
```sql=
SELECT table_column1, table_column2...
FROM table_name
WHERE column_name operator value;
```
其中：
operator可以是>、<、=，遇到字串比對可以使用LIKE
條件之間也可以用AND、OR、XOR來連接
另外，字串也可以比大小，以字典順序來比(ASCII編碼)

## LIKE
接在WHERE後面比對字串，如：
```sql=
SELECT name FROM world
WHERE name LIKE 'F%'
```
代表從world這張表格中，查詢name為F開頭的資料，並且輸出他們的name欄位。
其中 % 是萬用字元，可以用代表任何字元。
可以用底線符 _ 當作單一個字母的萬用字元，如：
```sql=
SELECT name FROM world
WHERE name LIKE '_n%'
ORDER BY name
```
從world表格中找出所有name第二個字元為n的資料，並以name排序列出所有name欄位。

## CONCAT
CONCAT()函數用來合併多個欄位的值(也可以是字串)。
用法：
```sql=
SELECT name
FROM world
WHERE capital LIKE '%City'
```
代表顯示所有國家名字，其首都名是國家名字加上「City」。
CANCAT可以一次合併多個字串和欄位，格式如下：
```sql=
CONCAT(str1, str2,...)
```
其中str也可以是欄位名

## REPLACE
格式為：
```REPLACE(str, s1, s2)```
用來將某個字串str中所有出現的s1，通通替換成s2。
如：
```sql=
REPLACE('vessel','e','a')
```
結果為'vassal'

## MID
MID()函數用來截取字串欄位值。
格式如下：
```sql=
SELECT MID(column_name, start [,length]) FROM table_name;
```
從字串索引值 start 開始截取共 length 長度的字串。沒指定 length 則取到尾。而 start 是從 1 開始。

範例如下：
"Monaco-Ville"是合併國家名字 "Monaco" 和延伸詞"-Ville".
欲顯示國家名字，及其延伸詞，如首都是國家名字的延伸。
```sql=
SELECT name, MID(capital, LENGTH(name)+1)
FROM world
WHERE capital LIKE CONCAT(name, '_%')
```

## LENGTH (LEN)
LENGTH()、LEN()函數是用來取得字串長度。
如：
```sql=
LENGTH(Smith)
```
得到結果為5。

## AS (Alias、別名)
修改查詢結果中的欄位名稱(或表格名)
例如：
```sql=
SELECT name, gdp/population as 'Per capita GDP' FROM world WHERE population > 200000000
```
原本的gdp/population欄位名稱會以Per capita GDP的別名來顯示。

## IN
IN 搭配 WHERE 子句可以用來限定必需符合某些欄位值為條件來搜尋資料表中的特定資料。
格式如下：
```sql=
SELECT table_column1, table_column2, table_column3...
FROM table_name
WHERE column_name
IN (value1, value2, value3...);
```
例如，顯示法國，德國，意大利(France, Germany, Italy)的國家名稱和人口：
```sql=
SELECT name, population
FROM world
WHERE name IN ('France', 'Germany', 'Italy')
```

## ROUND
ROUND()函數用來對數值欄位值進行四捨五入計算。
如：
```sql=
ROUND(some_number, decimals)
```
其中decimals用來設定要四捨五入到小數點第幾位，0表示個位數。
如：
```sql=
ROUND(7253.86, 0)    ->  7254
ROUND(7253.86, 1)    ->  7253.9
ROUND(7253.86,-3)    ->  7000
```

## CASE
類似於程式語言裡的 if/then/else 語句，允許在不同的情況(conditions)下得到不同的返回值。
若沒有任何conditions符合，則返回NULL。
格式：
```sql=
CASE WHEN condition1 THEN value1
   WHEN condition2 THEN value2
   ELSE def_value
END
```
case可以巢狀(nest)，如[第13題](https://sqlzoo.net/wiki/SQLZOO:SELECT_from_WORLD_Tutorial/zh)：
```sql=
SELECT name, continent,
CASE WHEN continent = 'Oceania' THEN 'Australasia'
WHEN continent in ('Eurasia', 'Turkey') THEN 'Europe/Asia'
WHEN continent = 'Caribbean' THEN CASE
WHEN name LIKE 'B%' THEN 'North America'
ELSE 'South America' END
ELSE continent END
FROM world
```

## &
位元運算的AND
(A & B)
0000 0000 1010 1010
0000 0000 0100 1011
-------------------
0000 0000 0000 1010

## |
位元運算的OR
(A | B)
0000 0000 1010 1010
0000 0000 0100 1011
-------------------
0000 0000 1110 1011

## BETWEEN
```BETWEEN value1 AND value2```可用於WHERE中作連續值的判斷，不同於IN。
前面加上NOT，```NOT BETWEEN value1 AND value2```代表不在value1和value2之間則true。

## 逸出字元


| 符號      | 功能                                                        |
| --------- | ----------------------------------------------------------- |
| ```%```   | 任何含有零或多個字元的字串。                                |
| ```-```   | 任何單一字元。                                              |
| ```[]```  | 在指定範圍 ([a-f]) 或集合 ([abcdef]) 中的任何單一字元。     |
| ```[^]``` | 不在指定範圍 ([^a-f]) 或集合 ([^abcdef]) 中的任何單一字元。 |
| ```''```  | 在字串中代表單一個```'```單引號                             |

## ORDER BY
將查詢結果排序，語法：
```sql=
SELECT table_column1, table_column2...
FROM table_name
ORDER BY column_name1 ASC|DESC, column_name2 ASC|DESC...
```
ORDER BY之後接欄位名稱，依序由左至右排，每個欄位選擇要ASC(升序)或DESC(降序)排。
也可以結合IN來排序，例如：
```sql=
SELECT winner, subject
FROM nobel
WHERE yr=1984
ORDER BY subject IN ('Physics','Chemistry'), subject, winner
```
IN會根據在不在後面括號中的值決定是0或1，這題是[第14題](https://sqlzoo.net/wiki/SELECT_from_Nobel_Tutorial/zh)

## COUNT
COUNT()函數用來計算符合查詢條件的欄位紀錄總共有幾筆。
格式：
```sql=
SELECT COUNT(column_name) FROM table_name
```
COUNT配合DISTINCT可以用來找出資料表中有多少筆不相同的資料 。

如果我們想查詢orders資料表中有多少位不同的顧客，SQL查詢如下：
```sql=
SELECT COUNT(DISTINCT Customer) FROM orders
```

## DISTINCT
一個資料表的某欄位中可能會有多個紀錄都是相同值的情況，在 SELECT 查詢語句中我們可使用 DISTINCT 關鍵字過濾重複出現的紀錄值。
格式：
```sql=
SELECT DISTINCT table_column1, table_column2...
FROM table_name;
```
可以從table_name表格中來限制僅取出table_column1欄位和table_column2欄位中"不相同"的值。

## GROUP BY

GROUP BY用來搭配聚合函數(aggregation function)使用，
是用來將查詢結果中特定欄位值相同的資料分為若干個群組，而每一個群組都會傳回一個資料列。

若沒有使用 GROUP BY，聚合函數針對一個SELECT查詢，只會返回一個彙總值。

語法：
```sql=
SELECT column_name(s), aggregate_function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name1, column_name2...
```
聚合函數指的也就是 AVG()、COUNT()、MAX()、MIN()、SUM() 等這些內建函數。
詳細請參考[這裡](https://www.fooish.com/sql/group-by.html)。

其中：
```sql=
SELECT ... aggregate_function(another_column)
GROUP BY column_name
```
代表another_column若具有相同的column_name之值，會被aggregate_function(another_column)計算出相對應的值。

另外，可以針對不同的欄位綁在一起進行GROUP BY，如[第12題](https://sqlzoo.net/wiki/The_nobel_table_can_be_used_to_practice_more_SUM_and_COUNT_functions./zh)：
```sql=
SELECT yr, subject FROM nobel WHERE yr >= 2000 GROUP BY yr, subject HAVING COUNT(winner) = 3
```
註：若是GROUP BY語句，則放在SELECT後的欄位，一定是GROUP BY中有的欄位，否則要使用聚合函數。

## 子查詢(SUBSELECT)
SELECT查詢的結果可以當作一個值，用在另一個查詢當中，例如 SELECT continent FROM world WHERE name = 'Brazil' 得到結果 'South America' ，所以我們可以用此值來找出與巴西'Brazil'同一洲份的全部國家。
如：
```sql=
SELECT name FROM world WHERE continent = 
(SELECT continent FROM world WHERE name = 'Brazil')
```
有些系統需要幫子查詢寫上別名，如：
```sql=
SELECT name FROM world WHERE continent = 
(SELECT continent FROM world WHERE name = 'Brazil') AS brazil_continent
```
取名為brazil_continent的子查詢。

另外，此種寫法如果子查詢的結果多於一個，會出錯，建議加上IN來使用：
```sql=
SELECT name, continent FROM world
WHERE continent IN
(SELECT continent FROM world WHERE name='Brazil' OR name='Mexico')
```

如保證子查詢只會有一個結果，甚至可以在SELECT後面使用子查詢。
```sql=
SELECT population/(SELECT population FROM world WHERE name='United Kingdom')
FROM world
WHERE name = 'China'
```
這段程式碼計算了中國人口是英國人口的多少倍。

若子查詢多於一個結果，也可以使用ALL或ANY來配合。

## ALL
滿足所有的元素就回傳true：
```sql=
select * from student where class='01' and age > all (select age from student where class='02')
```
上述語句可以查出1班中比2班的「所有」學生年齡都還大的學生。

## ANY
滿足任一個元素就回傳true：
```sql=
select * from student where class='01' and age > any (select age from student where class='02');
```
上述語句可以查出1班中比2班的「任一個」學生年齡還要大的學生。

## 不等於
可以使用
```!=```、```<>```
如果要跟NULL做比較，則：
```IS NULL```或是```IS NOT NULL```

## 表格的再命名
當查詢的表個需要臨時取一個名字，以便在條件或子查詢中使用，只需要在原本的表個名稱後面直接加上別名即可，如[第7題](https://sqlzoo.net/wiki/SELECT_within_SELECT_Tutorial/zh)：
```sql=
SELECT continent, name, area FROM world x
WHERE area >= ALL
(SELECT area FROM world y
WHERE y.continent = x.continent
AND area > 0)
```
外部的表個被取名為x，內部的表格被取名y，這段程式碼用意在找出每一個州中找出最大面積的國家。

找出洲份，當中全部國家都有少於或等於 25000000 人口。在這些洲份中，列出國家名字name，continent 洲份和population人口：
```sql=
SELECT name, continent, population FROM world x
WHERE 25000000 >= ALL(SELECT population 
FROM world y WHERE x.continent = y.continent)
```
有些國家的人口是同洲份的所有其他國的3倍或以上。列出 國家名字name和洲份continent：
```sql=
SELECT name, continent FROM world x
WHERE x.population/3 >= ALL(SELECT population
FROM world y WHERE x.continent = y.continent AND x.name <> y.name) 
```

## HAVING
HAVING是用來取代WHERE搭配聚合函數(參考GROUP BY)進行條件查詢，因為WHERE不能搭配聚合函數一起使用，白話文就是GROUP BY版的WHERE。
如：
```sql=
SELECT Customer, SUM(Price) FROM orders
GROUP BY Customer
HAVING SUM(Price)<1000;
```
作用是orders資料表中查詢訂單金額總合小於1000的顧客。

## JOIN
格式：
```sql=
SELECT *
FROM game JOIN goal ON (id=matchid)
```
語句FROM表示合拼兩個表格game和goal的數據。
語句ON表示如何找出game中每一列應該配對goal中的哪一列 -- goal的id必須配對game的matchid。
如果要寫得更詳信嚴謹，應該指名id是從goal而來(尤其當兩者具有相同欄位名稱時)，matchid是從game而來，如下：
```sql=
SELECT *
FROM game JOIN goal ON (goal.id = game.matchid)
```
另外，JOIN還有分成多種不同的形式：
1. INNER JOIN 內部連接
2. LEFT (OUTER) JOIN 左外部連接
3. RIGHT (OUTER) JOIN 右外部連接
4. FULL (OUTER) JOIN 全部外部連接
5. CROSS JOIN 交叉連接
6. NATURAL JOIN 自然連接

## INNER JOIN
格式：
```sql=
SELECT table_column1, table_column2...
FROM table_name1
INNER JOIN table_name2 
ON table_name1.column_name=table_name2.column_name;
```
INNER JOIN(內部連接)為等值連接，必需指定等值連接的條件，而查詢結果只會返回符合連接條件的資料。
也就是說，如果table_name1.column_name在table_name2.column_name之中沒有找到相等的值，則該行不列出。

另外，ON關鍵字在部分資料庫是改用USING關鍵字，後面是接連接條件，條件一樣可以使用AND、OR之類的邏輯運算。

## LEFT JOIN
可以用來建立左外部連接，查詢的SQL敘述句LEFT JOIN左側資料表(table_name1)的所有記錄都會加入到查詢結果中，即使右側資料表(table_name2)中的連接欄位沒有符合的值也一樣。
格式：
```sql=
SELECT customers.Name, orders.Order_No
FROM customers
LEFT JOIN orders
ON customers.C_Id=orders.C_Id;
```
結果顯示了每個顧客和其對應的訂單編號，若該顧客有一筆以上訂單，分成不同列顯示。
若該顧客沒有對應的訂單編號，也會顯示出來，並且在orders.Order_No欄位以空值(NULL)顯示。

如[第13題](https://sqlzoo.net/wiki/The_JOIN_operation/zh)
```sql=
SELECT mdate,
    team1,     
    SUM(CASE  WHEN teamid=team1 THEN 1  ELSE 0 END) score1,
    team2,     
    SUM(CASE          
    WHEN teamid=team2 THEN 1 ELSE 0 END) score2     
    FROM game LEFT JOIN goal ON matchid = id 
GROUP by mdate, team1, team2
ORDER BY mdate, matchid, team1 and team2
```
若沒有LEFT，則game中沒有對應到goal的都不會被列出，會造成雙方球隊得分0:0的比賽被忽略。

