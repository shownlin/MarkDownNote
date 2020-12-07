# Verilog筆記

## Verilog的模型有五種
* 低階交換層次模型(Switch Level Model)或電晶體交換模型(Transistor Model)
* 邏輯閘層次模型(Gate Level Model)
* 資料處理模型(Data Flow Model)
* 行為模型(Behavioral Model)
* 結構模型(Structure Model)
上述模型由上而下從低階至高階

## 註解(Comments)
單行註解：```// ```
多行註解：```/* 註解內容 */```
與C語言相同

## 空白(Whitespace)：
空格(blank spaces, \b) 這邊跟C語言不同，C的\b是指倒退
欄位(tabs, \t)
換行(newlines, \n)
除字串內空白，註解與空白在編譯合成時會被忽略掉

## 數值
Verilog 有兩種數值表示法
* 固定長度(sized)
  ```<size>'<base formate><number>```
  ```<size>```：十進位來表示此數的位元數(bits)
  ```<base format>```：定義進制，
  十六進位(```'H```或```'h```)(Hexdacimal)
  十進位(```'D```或```'d```)(Decimal)
  八進位(```'O```或```'o```)(Ocatal)
  二進位(```'B```或```'b```)(Binary)
  ```<number>```：用```<base format>```來表示數值
  若為負數，將負號放在```<size>```之前
  
  舉例：
  ```
  18'h47CB	// 18bits的十六進位數47CB，未給定的高位元自動補0
  
  13'h47CB	// 13bits的十六進位數47CB，位元數不足，實際只能表示十六進位數7CB
  
  12'd1023	// 12bits的十進位數1023
  
  5'b11101	// 5bits的二進位數11101
  
  5'b1xx01	// 第2和3個位元為未知，用x表示
  ```
* 不定長度(unsized)
  ```base formate><number>```
  不使用```<size>```規定位元長度，直接採用HDL編譯器預設的長度(通常是32bit的寬度)
  ```<base format>```也可以省略，內定為十進制
  
  舉例：
  ```
  'h47CB	// 32bits的十六進位數47CB
  
  1023		// 32bits的十進位數1023
  ```

有四種數值準位為(value level)：
| 數值位準 | 實際電路狀態                                     |
| -------- | ------------------------------------------------ |
| 0        | 邏輯0，假(false)，接地                           |
| 1        | 邏輯1，真(true)，接電壓源                        |
| x        | 不確定值                                         |
| z        | 高阻抗(high impedance)，浮接狀態(floating state) |

## 資料物件和資料型態
資料物件：
* 描述行為過程中所使用的訊號載具
* 一個物件經過處理再傳到另一個物件
資料型態
* 定義資料物件的類型
* 接線(wire)、暫存器(reg)、參數(parameter)等

接線(nets)是連接實體元建的連接線
最主要的關鍵字是wire
* 一個位元的純量(scalar)
* 多位元長度的向量(vector)
* 內定值為z(高阻抗)

接線之宣告：
```
wire w;	// 宣告一條接線，命名為w，內定預設值為z
wire x = 1'b0'	// 宣告一條接線，命名為x，並指定x為邏輯0
wire a,b,c;	// 一次宣告多條接線
```

暫存器(registers)：
抽象的資料儲存物件(有別於實體暫存器D flip-flop)
保留一個數值直到下一次指定新值為止
類似C語言變數的概念
最主要關鍵字為reg
* 一個位元的純量(scalar)
* 多位元長度的向量(vector)
* 內定值為x(不確定之值)

暫存器之種類：
| 種類    | 功能                                                                                   |
| ------- | -------------------------------------------------------------------------------------- |
| reg     | 可變動位元寬度的無號整數(unsigned integer variable)                                    |
| integer | 32位元寬度的有號整數(signed 32-bit integer variable)，算術運算產生2's complement的結果 |
| real    | 雙倍精確度之有號浮點數(signed floating-point variable with double precision)           |
| time    | 64位元寬度的無號整數                                                                   |
設計電路請以reg位主，其他類型合成器可能不支援。

暫存器之宣告：
```
reg a;	// 宣告1個1位元暫存器為a，內定值為1位元的x
reg x, y;	// 宣告2個暫存器，命名為x和y
integer count;	// 宣告1個整數count，值可以為正或負
```

## 純量與向量
純量：一個位元的物件
向量：多個位元的物件

wire與reg：
* 內定為1個bit
* ```[大數字:小數字]```、```[小數字:大數字]```都是允許的
  但是一定都是```[MSB:LSB]```即左邊為最高位元之index，右邊為最低位元之index

向量之宣告：
```
wire [4:0] x;	// 宣告1個5-bit之接線

reg [0:7] y;	// 宣告1個8-bit之暫存器
reg [31:0] y;	// 宣告1個32-bit之暫存器
```

## 陣列(array)
* 多個暫存器、接線的聚合體
* 索引值(index)定義聚合體中的個別物件
* 支援多維度的陣列
* 記憶體(memory)、暫存器檔案(register file)
* 陣列中暫存器、接線的個數
  ```[大數字:小數字]```、```[小數字:大數字]```都是允許的
  
陣列之宣告：
```
// 定義一個mem_block是一個包含128個暫存器的陣列，
// 每個暫存器皆為32bits寬

reg [31:0] mem_block [127:0];

// 定義一個mem_2D是一個2維4X64的暫存器陣列，
// 每個暫存器皆為8bits寬

reg [7:0] mem_block [3:0][0:63];
```

## 參數(Parameters)
* 定義編譯合成電路時的「常數」
* 每次編譯合成前更改，變異合成器會根據參數值產生相對應的電路
* 可重複使用
* 關鍵字parameter

參數之宣告與使用方式：
```
parameter width = 4;
wire [width-1:0] a, b;
// 接線a和b的位元寬度，會隨著參數值的設定而變動
```

## 模組(Module)
* 一個電路區塊
* 可以由其他模組所組成
連接模組時：
* 考慮模組的輸入與輸出介面
* 不需要考慮模組內部的詳細電路
電路設計時：
* 只修改模組內部電路
* 部會改變模組對外籍周邊的模組

以關鍵字Module為開頭，在module後加一個識別用的模組名稱(module name)；
再來是輸入與輸出土埠列(module terminal list)和埠列宣告，接著是模組內部關於電路的描述；
以關鍵字endmodule作為模組結尾

支援階層式的設計概念

模組內部電路的描述可包含：
* 訊號資料型態宣告
* 引用其他模組(邏輯閘)
* assign資料處理模型之描述
* always行為模型之描述
* 函數(function)與任務(task)
除訊號宣告需要先描述，其他部分的撰寫順序，不會影響電路的行為。

語法：
```
model 模組迷稱(埠列)
埠列宣告
參數宣告

訊號資料型態宣告(如wire, reg)

模組內部電路的描述

endmodule
```

## 埠(port)
* 終端點(terminal)
* 模組與外界溝通的介面接點(門)
* 一個模組通常是經由一串的輸入輸出埠稱為埠列(terminal list)來與外界溝通
* 若模組與外界不需要溝通，則埠列也就不存在(封閉系統)

Port之宣告：

* 埠的宣告可分為input(輸入)、output(輸出)和inout(雙向)
* 埠的宣告型態內定為接線(wire)，若需要將訊號儲存起來則需要顯式的宣告成暫存器reg
:star: input和inout只能宣告成接線，不可宣告成暫存器。
* 輸入訊號只接收外來訊號(被外部訊號所驅動)
* 只有output可以宣告成wire或reg

埠的宣告(範例)：
```
module full_adder(a, b, c_in, sum)
input [3:0] a, b;	// a和b為輸入埠(4位元向量)
input [c_in];		// c_in為輸入埠(1位元純量)
output [4:0] sum;	// sum為輸出埠(5位元向量)
reg [4:0] sum		// sum是以暫存器來使用
endmodule
```

## 邏輯閘層次模型
* 利用關鍵字即可引用基本的邏輯閘元件
* 基本的邏輯閘關鍵字有：
  * and
  * nand
  * or
  * nor
  * not
  * xor
  * xnor

### 多個輸入邏輯閘(Multiple-Input Gates)：
* and、nand、or、nor、xor、xnor
* 具有多個純量(scalar)的輸入，但是只有一個純量的輸出
* 多個輸入邏輯閘的輸出總是放在port list的第一個位置，而輸入則是跟在輸出的後面
* 輸出必須透過接線wire來連接，輸入無規定
* 多個輸入邏輯閘的別名可以加可以不加

使用方法：
```
// 單一個
gate_type instance(out, in_1, in_2,in_3, ..., in_n);

// 多個
gate_type inst_1(out_1, in1_1, in1_2, in1_3, ..., in1_n),
          inst_2(out_2, in2_1, in2_2, in2_3, ..., in2_n),
		  ...
		  inst_m(out_m, inm_1, inm_2, inm_3, ..., inm_n);
```

### 多個輸出邏輯閘
* 多個輸出邏輯閘(Multiple-Output Gates)
  * not、buf
* 具有一個或是多個純量(scalar)的輸出，但是只有一個純量的輸入
* 多個輸出邏輯閘的輸出是放在port list的前面位置，而輸入則是放在最後面位置
  * 輸出必須透過接線wire連接，輸入無規定
* 多個輸出邏輯閘的別名可以加可以不加

使用方法：
```
// 單一個
gate_type instance(out_1, out_2, out_3, ..., out_n, in);

// 多個
gate_type inst_1(out1_1, out1_2, out1_3, ..., out1_n, in1),
          inst_2(out2_1, out2_2, out2_3, ..., out2_n, in2),
		  ...
		  inst_m(outm_1, outm_2, outm_3, ..., outm_n, inm);
```

### 邏輯閘層次模型範例：
```
module and_or_gate(in1, in1, in2, in4, out)
input in1, in2, in3, in4;
output out;

wire w1, w2;

and a1(w1, in1, in2),
    a2(w2, in3, in4);
	
or o1(out, w1, w2;

endmodule
```

## 結構式模型
* 結構式模型(Structural Model)
  * 方塊架構圖
  * 引用子模組
  * 完成上層模組
* 不需要了解子模組的內部設計，重點放在模組的介面與功能
* 類似邏輯閘層次模型，只是基礎邏輯閘元建轉變成其他的子模組來替代

port的連接規定：
* 由於子模組intput一定為net(wire)，所以要上層模組要使用子模組可以用reg或net去對接
* 由於子模組intput可能是net或reg，所以要上層模組要使用子模組必須用net(wire)去對接
* inout端一定要用net

port mapping：
1. 位置對應(Position association)(又叫in order)
   跟C語言呼叫函式時一樣
2. by name
   跟python的方法很像，範例如下：
   ```
   // (by name)
   Full_Adder FA0(.sum([0]),
                  .a(a[0]),
                  .c_out(w0),
                  .b(b[0]),
                  .c_in(c));
   ```
兩種對應方式不能在同一個引用模組中混合使用

## 模組的別名
模組就像一個模具
引用模組就是複製一個相同功能的硬體電路區塊
複製出來的模組都是獨立的，所以可以：
* 為它命名(模組的別名)
* 個別的I/O介面與參數

## 資料處理模型(Data Flow Model)
* 描述電路中資料的處理方式
* 資料如何在電路中運算及傳送
* 輸入與輸入經過運算，運算結果「持續驅動」輸出
* 持續指定方式，assign關鍵字

持續指定(Continuous Assignment)：
* 資料處理模型的基本描述
* 輸入持續驅動輸出，輸入經過邏輯閘連接至輸出
* 關鍵字為assign

assign是利用「運算式」表示輸入與輸出之間的關係
:star:規定：
* 等號左邊必須宣告為wire，不能是reg
* 等號右邊無規定

隱藏式指定：
宣告wire時，就指定與其他接線的關係，省略assign關鍵字

持續指定是永遠驅動的狀態：
* 等號右邊的訊號（輸入）變動，左邊指定的接線內容（輸出）就會跟著改變
* 持續指定主要是用來描述「組合邏輯電路」

範例：
```
// 正規式持續指定

wire wout1;
assign wout 1 = a & b;	// and閘的運算式

// 隱藏式持續指定
wire wout2 = a & b;	// 宣告時同時指定
```

資料處理模型範例：
```
module and_or_dataflow(in1, in2, in3, in4, out);
input in1, in2, in3, in4;
output out;

assign out = (in1 & in2) | (in3 & in4);
endmodule
```

## 運算式(Expression)
* 描述訊號與其他訊號之間的關係
* 由operand與operator組合而成
  例如：```X = A * (B + C)```

## 運算元(operand)
* 有常數、整數、實數、接線(wire)、暫存器(reg)、陣列等資料型態
* 函數的回傳值也可以當作運算元
* 向量接線、向量暫存器的一個、部分個位元或全部位元
  ```
  wire [31:0] a;
  
  a[0]		// 一個位元
  a[7:0]	// 部分個位元
  a			// 全部位元
  ```

## 運算子(operator)
* 特定符號表式特定功能的運算
* 依所需的運算元個數來分類；
  * 單元(unary)運算子
  * 二元(binary)運算子
  * 三元(ternary)運算子

### 算數運算子(Arithmetic)
跟C語言相同：
| 運算子 | 運算功能 | 所需運算元 |
| ------ | -------- | ---------- |
| +      | 正號     | 1          |
| -      | 負號     | 1          |
| +      | 加法     | 2          |
| -      | 減法     | 2          |
| *      | 乘法     | 2          |
| /      | 除法     | 2          |
| %      | 取餘數   | 2          |
:star: ```/```和```%```盡量少用，硬體成本極高！

範例：
```
module arith(X, Y, Z, O1, O2, O3, O4);

input [3:0] X;
input [2:0] Y, Z;

output [4:0] O1;	// 無號數 O1 = X + Y
output [4:0] O2;	// 無號數 O2 = X - Z
output [6:0] O3;	// 無號數 O3 = X * Y，相乘則位元數是兩者位元數之總和
output [2:0] O4;	// 無號數 O4 = X % Y

// X = 10ㄝY = 3'b111, Z = 3'b1xx;

assign O1 = X + Y;	// O1 = 17
assign O2 = X - Z;	// O2 = x
assign O3 = X * Y;	// O3 = 70
assign O4 = X % Y;	// O4 = 3

endmodule
```

### 位元運算子(Bit-wise)
* 兩個運算元的相對應位元作邏輯運算
* 兩個運算元不等長，則較短者縮缺之相對位元自動補0

| 運算子   | 運算功能       | 所需運算元 |
| -------- | -------------- | ---------- |
| ~        | 取1的補數(NOT) | 1          |
| &        | 相對位元的AND  | 2          |
| \|       | 相對位元的OR   | 2          |
| ^        | 相對位元的XOR  | 2          |
| ^~ 或 ~^ | 相對位元的XNOR | 2          |

```
// X = 4'b1110, Y = 4'b1011;

assign O1 = ~X;	// O1 = 4'b0001
assign O2 = X & Y;	// O1 = 4'b0001
assign O3 = X | Y;	// O1 = 4'b1010
assign O4 = X ^ Y;	// O1 = 4'b1111
assign O5 = X ~^ Y;	// O1 = 4'b1010
```

### 連結運算子
用來進行連結運算(Concatenation)
| 運算子      | 運算功能 | 所需運算元 |                                |
| ----------- | -------- | ---------- | ------------------------------ |
| ```{}```    | 序連     | 任意       | 將不同的運算元作位元上的concat |
| ```{n{}}``` | 重複     | 任意       | 將一個運算元重複n次            |

範例：
```
wire A = 1;b1;
wire [1:0] B = 2'b10;
wire [1:0] C = 3'b111;

assign Y1 = {B, C};	// Y1 = 5'b10_111 (底線是區隔符號，編譯器會忽略)

assign Y2 = {C, A, B[0]};	// Y2 = 5'b111_1_0

assign Y3 = { { 3{ C[2:1] } }, { 2{ B} }, A };
// C[2:1]重複三次，B重複2次，最後接上A
// Y3 = 11'b11_11_11_10_10_1
```

### 相等運算子(Equality)
判斷兩運算元是否相等
判斷運算式(回傳真(0)/偽(1)，1個bit回傳值)



| 運算子 | 運算功能     | 所需運算元 |
| ------ | ------------ | ---------- |
| ==     | 邏輯的相等   | 2          |
| !=     | 邏輯的不相等 | 2          |
| ===    | 事件的相等   | 2          |
| !==    | 事件的不相等 | 2          |

* 邏輯上的相等：
  當運算元出現x或z值皆傳回x
  用於電路設計(符合實際電路行為)
  
* 事件上的相等：
  較為嚴謹
  運算元包含x與z值皆作相等判斷
  用於模擬驗證比對(波行模擬，觀察輸入輸出有沒有按照想法運行)

相等運算子範例：
```
// W = 4'b1z0x, X = 4'b0010, Y = 4'b1101, Z = 4'b1z0x

assign out = (X == Y); // X與Y不相等，out為邏輯0(偽)

assign out = (X != Y); // X與Y不相等，out為邏輯1（真）

assign out = (W === Z); // W與Z相等，out為邏輯1（真）

assign out = (Y !== Z); // Y與Z不相等，out為邏輯1（真）

assign out = (X == Z); // Z含有為x, z的值，out為x
```

### 關係運算子(Relational)
* 與C語言的關係運算子相同，比較大小
* 判斷運算式回傳真(1)/偽(0)
* 運算元為不確定值x、z時，結果也為不確定值x
* 與C一樣有```>```、```<```、```>=```、```<=```

### 邏輯運算子
* 與C語言相同，將運算元先以真/偽判斷後，在進行邏輯運算
* 當運算元含有x時，回傳一樣是x

| 運算子 | 運算功能  | 所需運算元 |
| ------ | --------- | ---------- |
| !      | 邏輯的NOT | 1          |
| &&     | 邏輯的AND | 2          |
| \|\|   | 邏輯的OR  | 2          |

邏輯運算子與位元運算子之比較：
```
// X = 3'b011, Y = 3'b100

assign out1 = (X && Y);
// 非全0為真(1'b1); 全0(3'b000)為偽(1'b0)
// X非0為1'b1，Y非0為1'b1
// out為1'b1

assign out2 = (X & Y);
// 對應位元作and閘
// out2為3'b000
```

### 判斷運算子(Conditional)
三元運算子，與C語言相同
語法：
```
（判斷運算式）?（判斷運算為真時之運算）:（判斷運算為假時之運算）
```
可以使用巢狀結構，如：
```
wire [3:0] X,Y, Z;
wire [3:0] out1, out2;

assign out2 = Z ? Z : (X ? X : Y);
// 巢狀結構，若X = 4'b1100, Y = 4'b1100, Z = 4'b0000
// out2 = 4'b1100
```

### 簡化運算子(Reduction)
* 類似位元運算子，只需要一個運算元
* 將單一運算元的所有位元作邏輯上的運算，最後輸出僅有「1個bit」之結果

| 運算子   | 運算功能   | 所需運算元 |
| -------- | ---------- | ---------- |
| &        | 簡化的AND  | 1          |
| \|       | 簡化的OR   | 1          |
| ~&       | 簡化的NAND | 1          |
| ~\|      | 簡化的NOR  | 1          |
| ^        | 簡化的XOR  | 1          |
| ~^ 或 ^~ | 簡化的XNOR | 1          |

範例：
```
// X = 4'b1110

assign O1 = & X;	// O1 = 1'b0

assign O1 = | X;	// O2 = 1'b1

assign O1 = ^ X;	// O3 = 1'b1 (類似於__builtin_parity，奇數個1為1，偶數個1為0)
```

### 移位運算子(Shift)
* 左移或右移
* 向量運算元向左或向右移特定的位元數
* 移位所產生的空位元則自動補0
* 與C語言相同，有```<<```和```>>```

### 其他運算子

| 運算子    | 運算功能         | 所需運算元 |
| --------- | ---------------- | ---------- |
| ```[ ]``` | 位元或部分選擇   | 任意       |
| ```( )``` | 括號代表優先運算 | 任意       |

## 行為描述模型(Behavior Model)
* Verilog最高階的層次模型
* 不需要考慮硬體元建如何實現，只須放在模組的功能描述
* 很像C語言
  * 程序指定(procedural assignment)
* 高階語法透過合成軟體工具轉換成邏輯閘才能實現出電路結果

### 程序指定(procedural assignment)
* 是行為模型(Behavior Model)的基本描述
  *  組合電路(combinational circuit)
  *  循序電路(sequential circuit)
* 關鍵字引導出的「區塊」描述行為
  * initial、always
  * 區塊內的輸出資料(等號左邊)，必須用暫存器(reg)宣告
* 測試波型與電路驗證的描述

## initial行為描述
* 區塊內的描述只「執行一次」
* 多行敘述（以句末";"區隔），須以```begin ... end```包覆
  * 循序區塊(sequential blocks)
  * 按順序執行
* 區塊內的輸出資料（等號左邊），必須用暫存器(reg)宣告
* 測試波型與電路驗證的描述

範例：
```
reg a, b, c;

intital
begin
	a = 1'b0;
	b = 1'b1;
	c = a & b;
end
```

### 限制指定(blocking assignment)
* 會在循序區塊(sequential blocks)(begin .. end)中的位置，依序執行
* 像C語言指令敘述是由上而下依序執行
* 限制指定的符號是 "="

範例：
```
initial
begin
	A = 3;
	B = 2;
	C = 1;
	...
	B = C;	// B、C皆為1
	A = B;	// A、B、C皆為1
end
```
若更改順序
```
initial
begin
	A = 3;
	B = 2;
	C = 1;
	...
	A = B;	// A、B皆為2
	B = C;	// B、C皆為1
end
```
:star:在限制指定中，位置調換會改變結果

### 無限制指定(nonblocking assignment)
* 在循序區塊內(sequential blocks)(begin ... end)中，平行處理
* 執行順序不受位置影響
* 每行敘述位置可以任意調換
* 無限制指定的符號是 "<="

範例：
```
inital
begin
	A = 3;
	B = 2;
	C = 1;
	...
	B <= C;	// B = 1
	A <= B;	// A = 2
	C <= A;	// C = 3
end
```
說明：
前面```A = 3; B = 2; C = 1;```仍然會先順序執行，由上往下。
當執行到B <= C時進入無限制指定，所以後面三行```B <= C; A <= B; C <= A;```會同時進行。
後面這三行，無論順序如何，結果都會一樣，即位置調換不改變結果。

## always關鍵字
* 區塊內的描述永無止盡
* 與實際電路的行為一致
* 事件觸發以```@(事件表示式)```來表示
* 多行敘述（以句末";"區隔），須以```begin ... end```包覆
* 區塊內的輸出資料（等號左邊），必須用暫存器(reg)來宣告

### 事件觸發
* 輸出改變是因為「事件發生」
* 組合電路之（輸入）
* 循序邏輯電路（時脈）

always的基本型
```
always @(事件表示式)
begin
	電路行為描述區塊
end
```

有兩種東西可以當成事件：
1. 直接放入訊號（變數）
   * 訊號（變數）發生變化時（值由0變1或由1變0時），又稱為準位觸發
   * 可以為scalar或vector
   * 可以多個訊號一起放進來，中間以"or"關鍵字區隔
   * 對應到組合邏輯電路
   範例：
   ```
   // a, b為純量(單一位元)
   
   always @(a or b)
   begin
   	out = a & b;
   end
   ```
   上述程式碼對應到一個AND邏輯閘，a和b一定要放到@事件之中，否則會有警告訊息（沒有對應的實際電路）
2. 放入邊緣觸發的scalar訊號（變數）
   * 由0變化到1的瞬間（正緣觸發）
     * 在訊號前加上posedge (positive edge)
   * 由0變化到1的瞬間（負緣觸發）
     * 在訊號前加上negedge (negative edge)
   * 訊號只能是純量
   * 可以多個邊緣處發訊號（變數），中間以"or"關鍵字區隔(也可以用逗號','區隔)
   * 對應到循序電路(sequential circuit)
   範例：
   ```
   // a, b, clk為純量(單一位元)
   
   reg out;
   
   always @(posedge clk)
   begin
   	out = a & b;
   end
   ```
   上述程式碼對應到一個D flip-flop正反器，
   當a或b變化時不會馬上影響out，
   而是在時脈訊號clk「由0變1的瞬間」才改變out的結果。
   
### 行為模型範例：
```
module and_or_behavior(in1, in2, in3, in4, out);
input in1, in2, in3, in4;
output out;
reg out;

always @(in1 or in2 or in3 or in4)
begin
	out = (in1 & in2) | (in3 & in4);
end
endmodule
```

## 條件敘述(conditional statements)
* 依照條件的成立與否，決定執行成立敘述或不成立敘述
* 關鍵字為if ... else ...
* 多行執行敘述，須以begin ... end包覆
* 二分法的條件敘述
* 只能放在initial、always引導的行為區塊內
* 允許巢狀結構

條件敘述基本型
```
if(條件運算式)
	begin
		成立時的行為描述區塊
	end
else
	begin
		不成立時的行為描述區塊
	end
```
範例：
```
// a, b, out為純量(1 bit)

reg out;

always @(a or b)	// a或b有變化時，執行區塊內的敘述
begin
	if (a == b)
		out = 1'b1;	// 當a等於b，out為1
	else
	out = 1'b0;		// 當a不等於b，out為0
end
```
也支援巢狀結構：
```
// a, b為純量(1 bit)，out為2 bits向量

reg [1:0] out;

always @(a or b)	// a或b有變化時，執行區塊內的敘述
begin
	if (a)
		if (b)
			out = 2'b01;	// 當a=1且b=1時，out為2'b01
		else
			out = 2'b10;	// 當a=1且b=0時，out為2'b10
	else
		out = 2'b00;		// 當a=0時，out為2'b00
end
```
另外一種是else if的巢狀結構：
```
// sel、a、b、c和out皆為2 bits

always @(sel or a or b or c)

begin
	if(sel == 2'b00) our = c;
	else if(sel == 2'b01) our = b;
	else if(sel == 2'b10) our = a;
	else out = 2'b00;
end
```

## 多路分支敘述(multiway branching)
* if-else-if結構之簡化
* 類似C語言switch ... case
* 關鍵字case開頭，endcase結尾
* 剩餘未指定的條件，以關鍵字default一網打盡
* 僅能放在initial或always引導的行為區塊內
* 允許巢狀結構

多路分支基本型：
```
case (訊號)

條件值1: 行為描述區塊
條件值2: 行為描述區塊
條件值3: 行為描述區塊
...
default: 剩餘條件的行為描述區塊

endcase
```
範例：
```
// sel、a、b、c和out皆為2 bits

always @(sel or a or b or c)
begin
	case (sel)
	2'b00: out = a;			// 當sel為2'b00時，out為a
	2'b01: out = b;			// 當sel為2'b01時，out為b
	2'b10: out = c;			// 當sel為2'b10時，out為c
	default: out = 2'b00;	// 其他情況時，out為a
	endcase
end
```

### casex與casez
* case敘述，不允許分支條件有```don't care```
  * 會針對"0"、"1"、"x"、"z"比對
* casex和casez允許含有```don't care```
  * casex使用"x"來代表```don't care```
  * casez使用"?"或"z"來代表```don't care```

casex範例：
```
// sel、a、b、c和out皆為2 bits

always @(sel or a or b or c)
begin
	casex (sel)
	2'b0x: out = a;			// 當sel[1](較高的那個bit) = 1'b0時，out為a
	2'b1x: out = b;			// 當sel[1] = 1'b1時，out為b
	default: out = c;		// 其他情況時，out為c
	endcase
end
```

casez範例：
```
// sel、a、b、c和out皆為2 bits

always @(sel or a or b or c)
begin
	casex (sel)
	2'b?1: out = a;			// 當sel[0](較低的那個bit) = 1'b1時，out為a
	2'b?0: out = b;			// 當sel[0]= 1'b0時，out為b
	default: out = c;		// 其他情況時，out為c
	endcase
end
```

## 迴圈敘述(loops)
關鍵字
1. forever
2. repeat
3. while
4. for
放在程序指定敘述內，inital、always
電路設計常用for迴圈，其他用在模擬與驗證

### forever
* 持續執行某個敘述，永無止盡
* 通常搭配時間延遲產生週期性的訊號
```
reg clock;
intital
begin
	clock = 1'b0;
	forever #5 clock = ~clock;
end
```
其中井字號```#5```代表延遲5個單位時間
上述程式碼可以產生一個波形模擬檔

### repeat
持續執行某個敘述，重複n次
```
reg clear;
intital
begin
	clear = 1'b0;
	repeat(3) #5 clear = ~clear;
end
```
上述程式碼會將clear進行3次的反向，最後定在1

### while
迴圈一直執行，直到條件運算式不成立，同C語言。
```
while(條件運算式)
begin
	行為描述區塊
end
```
範例：
```
reg [2:0] count;
intital
begin
	count = 3'd0;
	while (count < 3'd4)
		count = count + 1'd1
end
```
執行時：
```
// count = 3'd0
// count = 3'd1
// count = 3'd2
// count = 3'd3
// count = 3'd4
```

### for
for迴圈包含三部分
1. 初始條件
2. 判斷終止條件
3. 改變控制變數的程序指定

語法：
```
for(初始條件;判斷終止條件;改變控制變數的程序指定)
begin
	行為描述區塊
end
```

範例：
```
input [4:0] a, b'
output [4:0] out;
reg [4:0] out;

always @(a or b)
begin: bits_and_blk	// begin ... end區塊「標籤」
	integer i;	// 區域變數，區域變數會是虛擬的，不會參與到實際電路中
	for (i = 0; i < 5; i = i + 1)	// 不可以寫成i++，verilog沒有指標概念
		out[i] = a[i] & b[i];
end
```
上述為什麼需要區塊標籤？
因為這樣才可以定義區域變數（規定）

而for迴圈的實際電路就是for迴圈直接展開，
因此for迴圈只能精簡程式碼，並不節省硬體。

## 函數(Function)
* 類似其他語言的函式或副程式
* 用意在於重複使用程式碼
* 關鍵字function開頭，endfunction結尾
* 可使用程序指定（除了initial跟always關鍵字以外的選擇）
* 無時間延遲控制的指令（```#n```不可用）
* 至少有一個輸入且傳回單一個值
* 常用在電路設計（可合成）

funciton基本型
```
function [MSB:LSB]函數名稱;	// 函數名稱就是回傳值，編譯器自動變異成reg型態`
// 回傳值可以是1bit或多個bit
intput [?:?] 輸入訊號;	// 至少要有一個輸入
begin
	行為描述區塊
end
endfunction
```
函數的宣告必須放在module .. endmodule之中

funciton宣告範例：
```
function [3:0] min_fun;
input [3:0] in1, in2;
begin
	if(in1 < in2)
		min_fun = in1;
	else
		min_fun = in2;
end
endfunction
```
function呼叫範例：
```
module min(a, b, c, x, y);
input [3:0] a, b, c;
output [3:0] x, y;
reg [3:0] x, y;

always @(a or b or c)
begin
	x = min_fun(a, b);
	y = min_fun(a, c);
end

function [3:0] min_fun;
input [3:0] in1, in2;
begin
	if(in1 < in2)
		min_fun = in1;
	else
		min_fun = in2;
end
endfunction
endmodule
```
也可以用assign來呼叫：
```
module min(a, b, c, x, y);
input [3:0] a, b, c;
output [3:0] x, y;
reg [3:0] x, y;

assign x = min_fun(a, b);
assign y = min_fun(a, c);

function [3:0] min_fun;
input [3:0] in1, in2;
begin
	if(in1 < in2)
		min_fun = in1;
	else
		min_fun = in2;
end
endfunction
endmodule
```

同樣地，呼叫越多次funciton，只能精簡程式碼，但是每次呼叫就會多複製一份硬體。

## 任務(task)
* 類似函數
* 重複使用程式碼
* 關鍵字task開頭，endtask結尾
* 可使用程序指定
* 可有時間延遲的指定(```#n```)
* 輸入、輸出個數不定
* 常用在驗證或模擬（不一定能合成）

task基本型
```
task 任務名稱;
input [?:?] 輸入訊號;
output [?:?] 輸出訊號;
inout [?:?] 輸出入訊號;
begin
	行為描述區塊	//可以有時間延遲
end
endtask
```
任務的宣告必須放在module ... endmodule之中

task範例（宣告）：
```
task min_task;
input [3:0] in1, in2;
output out;
begin
	if (in1 < in2)
		out = in1;
	else
		out = in2;
end
endtask
```
task範例（呼叫）：
```
module min(a, b, c, x, y);
input [3:0] a ,b ,c;
output [3:0] x, y;
reg [3:0] x, y;
always @(a or b or c)
begin
	min_task(a, b, x);
	min_task(a, c, y);
end

task min_task;
input [3:0] in1, in2;
output out;
begin
	if (in1 < in2)
		out = in1;
	else
		out = in2;
end
endtask
endmodule
```

函數與任務的差別：

| 函數                                     | 任務                                      |
| ---------------------------------------- | ----------------------------------------- |
| 可以引用其他函數，但不可以引用任務。     | 可以引用其他任務與韓數。                  |
| 時間等於0的時後開始執行。                | 可以從任一時間開始執行。                  |
| 不能包含有時間延遲、或是事件控制的指令。 | 可以包含有時間延遲、或是事件控制的指令。  |
| 至少要有一個以上input宣告。              | 可以有隨意個（含0）input、output、inout。 |
| 只有一個回傳值。                         | 可以有0到多個輸出值。                     |
