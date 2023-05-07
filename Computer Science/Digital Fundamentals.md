## 1 Introductory Concepts 概念引入

### 1. Digital and Analog Quantities 数字信号和模拟信号

**模拟信号**(analog quantity)连续，**数字信号**(digital quantity)离散。

==数字信号的优点==：
- 方便处理和传输。
- 易于存储和恢复。
- 抗干扰能力强。

采样频率 >= 2 \* 最大频率。
CD采样频率44.1kHz >= 2 \* 人听觉最大频率。

量化精度8 bit，取值0~255。

### 2. Pulse 脉冲信号

rise time：上升坡10%~90%的时间。
fall time：下降坡90%~10%的时间。
pulse width：强度>=50%的持续时间。

## 2 Number Systems, Operations, and Codes 进制、变换与编码

### 1. Number Systems 进制

二进制 binary，八进制 octal，十进制 decimal，十六进制 hexadecimal。

十 -> 二：小数点左侧连续除2取余，余数从下往上读；右侧连续乘2取整，整数从上往下读。

二 -> 八：每3位算出1位，小数点左侧在前面补0、右侧在后面补0。
eg. 0.1011（二转八）：补0 -> 0.101,100 -> 0.54（不是0.51）。

二 -> 十六：每4位算出1位，小数点左侧在前面补0、右侧在后面补0。

### 2. Signed Numbers 有符号数

根(root)：进制的标志，如一个二进制数的根为2。

**符号位**(sign bit)：在数值之前用一个额外的数字标记其正负，正数为0，负数为(根 - 1)，相当于`ifNegative`。

**原码**：带有符号位的二进制数值。

**补数**(complement)针对无符号数，**补码**(complement code)针对有符号数。

**（根）补码（2'）**：$[N]_r = r^n - N_r$。

**（根）-1补码（反码）（1'）**：$[N]_{r-1} = r^n - N_r - 1$。

==求补码==：
- 正数：转换为二进制 -> 在前面补0作为符号位，原码、反码、补码均为此码。
- 负数：求相反数补码，记为$C$ -> 再求：
	- 补码：将$C$各位反转 -> +1。
	- 反码：将$C$各位反转。

求$c = a + b$：求$c_补 = a_补 + b_补$，再将$c_补$还原为$c$。

==补码竖式计算==：
- 对齐：正数在前面补0，负数在前面补1。
- 判断溢出：数值计算的最后两次进位(carry)不一致则出现溢出。
- 符号位扩展：对两个加数，正数在前面补0，负数在前面补1，再次竖式计算。

==由补码求原式==：
- 法1 权重和：最高位若为1则减去该位权重，其它位正常加法。eg.$11000110 = -2^7 + 2^6 + 2^2 + 2^1 = -58$。
- 法2 （若2'则-1，否则不减）取反权重和加负号：eg. $2'补码 11000110 - 1 = 11000101 >> 00111010 = 58 >> -58$。

## 3 Logic Gates 逻辑门

|中文|英文|规则|图示|备注|
|:-:|:-:|:-:|:-:|:-:|
|非|Inverter / NOT|取反|![[NOT.png\|150]]||
|与|AND|有 0 则 0|![[AND.png\|150]]||
|或|OR|有 1 则 1|![[OR.png\|150]]||
|与非|NAND|有 0 则 1|![[NAND.png\|150]]|等价于 非或|
|或非|NOR|有 1 则 0|![[NOR.png\|150]]|等价于 非与|
|非与|negative-AND|有 1 则 0|与 前面加圆圈|等价于 或非|
|非或|negative-OR|有 0 则 1|或 前面加圆圈|等价于 与非|
|异或|XOR|相同则 0|![[XOR.png\|150]]||
|同或|XNOR|相同则 1|![[XNOR.png\|150]]||

与非、或非都能通过组合构成与、或、非门。

|门|NAND构成|NOR构成|
|:-:|:-:|:-:|
|NOT|![[NOT from NAND.png\|100]]|![[NOT from NOR.png\|100]]|
|AND|![[AND from NAND.png\|150]]|![[AND from NOR.png\|150]]|
|OR|![[OR from NAND.png\|150]]|![[OR from NOR.png\|150]]|

## 4 Boolean Algebra and Logic Simplification 布尔代数与逻辑简化

布尔变量只能为0或1。

布尔乘(\*)为逻辑与，布尔加(+)为逻辑或。

运算规律：
- 吸收率：$A + AB = A$，亦作$A (A + B) = A$。
- $A + \overline{A} B = A + B$。
- $(A + B) (A + C) = A + B C$。

**DeMorgan's Theorems 德摩根定律**：
- $\overline{X Y} = \overline{X} + \overline{Y}$。
- $\overline{X + Y} = \overline{X} \space \overline{Y}$。

==快速应用==：
规定：一条上划线横跨多个字母称为一条**算式**。
对于==一条没有子算式的算式==，作如下变化：
- 字母上方的上划线条数（计到本算式的上划线为止）为奇数则保留、为偶数则消去。
- 算式内的运算符号乘变加、加变乘。
注意：当算式下含有子算式时，必须先计算出子算式，才能计算父算式，例如：$\overline{\overline{A B} + C D}$，需要先计算子算式$\overline{A B}$将原式化为$\overline{\overline{A} + \overline{B} + C D}$，再计算父算式得$A B (\overline{C} + \overline{D})$，若当成一个算式、只计算一步会得到错误答案$(A + B) (\overline{C} + \overline{D})$。

**SOP**(sum of products)：乘积之和，形如$\overline{A} B + B \overline{C}$。

**POS**(product of sums)：和之乘积，形如$(\overline{A} + B) (B + \overline{C})$。

**标准SOP**(Standard SOP Expression)：每项都包含全部变量的SOP。

==eg.== 将$\overline{A} \space \overline{B} + A B C$化为标准SOP。

-> I = $\overline{A} \space \overline{B} * 1 + A B C$ = $\overline{A}\space\overline{B} * (C + \overline{C}) + ABC$ = $\overline{A} \space \overline{B} \space C + \overline{A} \space \overline{B} \space \overline{C} + A B C$

**标准POS**(Standard POS Expression)：每项都包含全部变量的POS。

==eg.== 将$(\overline{A} + \overline{B}) (A + B + C)$化为标准POS。

-> I = $(\overline{A} + \overline{B} + C * \overline{C}) (A + B + C)$ = $(\overline{A} + \overline{B} + C) (\overline{A} + \overline{B} + \overline{C}) (A + B + C)$

注意化为标准SOP/标准POS后需==检查是否有相同的项可以相消==。

==与真值表的对应==：

- SOP：各和项均为乘积，只有一种取值组合可使其值为1，其他组合时全为0。
- POS：各积项均为加和，只有一种取值组合可使其值为0，其他组合时全为1。

最小项：每个变量出现1次（不多不少）的积项（对应SOP），用$m_i$表示，$i$为对应的十进制值，如$A\overline{B}C = 1$时对应$A = 1, B = 0, C = 1$，$101_{(2)} = 5_{(10)}$，于是写为$m_5$；多个相加写为$m_1 + m_2 + m_4 + m_6 = \sum m(1, 2, 4, 6)$。

最大项：每个变量出现1次（不多不少）的和项（对应POS），用$M_i$表示，$i$为对应的十进制值，如$A + \overline{B} + C = 0$时对应$A = 0, B = 1, C = 0$，$010_{(2)} = 2_{(10)}$，于是写为$M_2$；多个相加写为$M_0 + M_3 +M_5 +M_7 = \Pi M(0, 3, 5, 7)$（$M$前面的符号是大$\pi$）。

eg. 将$\sum m(1, 2, 4, 6)$转换为标准SOP。

-> $\sum m(1, 2, 4, 6) = \overline{A} \space \overline{B} \space C + \overline{A} \space B \space \overline{C} + A \space \overline{B} \space \overline{C} + A \space B \space \overline{C}$（在0的位置划线）

eg. 将$\Pi M(0, 3, 5, 7)$转换为标准POS。

-> $\Pi M(0, 3, 5, 7) = (A + B + C) (A + \overline{B} + \overline{C}) (\overline{A} + B + \overline{C}) (\overline{A} + \overline{B} + \overline{C})$（在1的位置划线）

**卡诺图**==组合规则==：

- 对象是1，不管0。
- 相邻，可以是1 \* n长条，也可以是m \* n矩形。
- 总数必须是$2^k$。
- 每个1都必须被覆盖，在此前提下，每个1的被覆盖次数应尽可能少（每个组合应尽可能大）。
- 立体相邻：对3变量，可以卷成一个圆柱；对4变量，可以卷成一个甜甜圈（具体看书）。
- 组合后，只要值恒定的量，不要值变化的量。

> 卡诺图示例见P122

Implicant：每个1（SOP）或0（POS）。

Prime Implicant(PI)：一组（符合组合规则的组）1/0。

Essential Prime Implicant(EPI)：至少一个1/0为其独有的PI。

用卡诺图化简布尔表达式：

1. 找出所有的PI。
2. 标出其中的EPI。
3. 对没有被EPI包含的Implicant，用最大的PI包含，并标记该PI。
4. 对在2、3步中标记的EPI、PI用布尔变量表示，再相加/相乘得出简化的SOP/POS。

Don't Care：有时某个输出为1/0均无所谓，在卡诺图中用d/-/x标记该位置，在化简时可以任意当作1/0处理（只要有助于化简，比如能构成更大的PI）。

用7根发光二极管表示阿拉伯数字：

![[数字显示器.png|100]]

|N||W|X|Y|Z|->|A|B|C|D|E|F|G|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|0||0|0|0|0||0|0|0|0|0|0|1|
|1||0|0|0|1||1|0|0|1|1|1|1|
|2||0|0|1|0||0|0|1|0|0|1|0|
|3||0|0|1|1||0|0|0|0|1|1|0|
|4||0|1|0|0||1|0|0|1|1|0|0|
|5||0|1|0|1||0|1|0|0|1|0|0|
|6||0|1|1|0||0|1|0|0|0|0|0|
|7||0|1|1|1||0|0|0|1|1|1|1|
|8||1|0|0|0||0|0|0|0|0|0|0|
|9||1|0|0|1||0|0|0|0|1|0|0|

- $A = X\space \overline Y\space \overline Z + \overline W\space \overline X\space \overline Y\space Z$
- $B = X\space \overline Y\space Z + X\space Y\space \overline Z$
- $C = \overline X\space Y\space \overline Z$
- $D = X\space \overline Y\space \overline Z + \overline W\space \overline X\space \overline Y\space Z + X\space Y\space Z$
- $E = X\space \overline Y + Z$
- $F = \overline X\space Y + Y\space Z + \overline W\space \overline X\space Z$
- $G = \overline W\space \overline X\space \overline Y + X\space Y\space Z$

## 5 Combinational Logic Analysis 组合逻辑分析

**完备集**：可以通过组合表示出所有逻辑门的一组逻辑门，常见有{AND, OR, NOT}，{NAND}，{NOR}，{XOR, AND}。