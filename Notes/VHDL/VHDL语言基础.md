---
date created: 2022-10-07 17:42:52
date updated: 2022-10-07 21:07
tags: [code, language]
title: VHDL语言基础
date modified: 2022-10-08 20:05:52
---

## 标识符

- 由字母、数字、下划线组成
- 用于定义常数、变量、信号、文件、端口、子程序或参数的名字
- 要求：
	- 首字符为字母
	- 最后一个字符不是下划线
	- 不能出现两个连续的下划线
	- 最长 32 个字符
- 不区分大小写
- VHDL 关键字不能用于标识符
- `--` 用于注释

## 数据对象

- 常量（Constant）
	- 常量是对某一常量名赋予一个固定的值，而且只能赋值一次。通常赋 值在程序开始前进行，该值的数据类型则在说明语句中指明。
	- Constant 常数名：数据类型：＝表达式
	- e.g.：
	- Constant Vcc： real:=5.0; -- 定义 Vcc 的数据类型是实数，赋值为 5.0V
	- 常量所赋的值应和定义的数据类型一致
	- 常量在程序包、实体、构造体或进程的说明性区域内必须加以说明。
		- 定义在程序包 内的常量可供所含的任何实体、构造体所引用
		- 定义在实体说明内的常量只能在该 实体内可见
		- 定义在进程说明性区域中的常量只能在该进程内可见。
- 变量（Variable）
	- 性质
		- 只能在进程语句、函数语句和过程语句结构中使用。
		- 变量的赋值是直接的，非预设的，分配给变量的值立即成为当前值
		- 变量不能表达“连线”或存储元件，不能设置传输延迟量。
	- 定义语句
		- Variable 变量名：数据类型 :＝初始值
	- 赋值语句
		- 变量名:=表达式
- 信号（Signal）
	- 性质
		- 信号表示逻辑门的输入或输出，类似于连接线
		- 也可以表达存储元件的状态
		- 通常在构造体、程序包和实体中说明
	- 定义语句：
		- Signal 信号名: 数据类型 :＝初始值
	- 赋值语句：
		- 信号名<=表达式

## 数据类型

### VDHL 预设数据类型

- 布尔（Boolean）
	- `TYPE BOOLEAN IS (FALSE, TRUE)
	- 取值为 FALSE 和 TRUE，不是数值，不能运算， 一般用于关系运算符
- 位（Bit）
	- `TYPE BIT IS (’0‘, ’1‘)
	- 取值为 0 和 1，用于逻辑运算
- 位矢量（Bit_Vector）
	- `TYPE BIT_VECTOR IS ARRAY (Natural range<>) OF BIT
	- 基于 Bit 类型的数组， 用于逻辑运算
- 字符（Character）

	` TYPE CHARACTER IS (NUL, SOH,STX, …, ‘ ’, ‘!’,…)

	- 通常用‘’引起来， 区分大小写
- 字符串（String）
	- `VARIABLE string_var: STRING (1 TO 7)
	- 通常用“”引起来，区分大小写
- 整数（Integer）
	- 取值范围 $-(2^{31}-1) ～ (2^{31}-1)$， 可用 32 位有符号的二进制数表示
	- 要求用 RANGE 子句为所定义的数限定范围，以便根据范围来决定表示此 信号或变量的二进制数的位数。
	- VHDL 仿真器将 Integer 做为有符号数处理，而 VHDL 综合器将 Integer 做为无符号数处理
- 实数（Real）
	- 取值范围 -1.0E38 ～ +1.0E38， 仅用于仿真不可综合
- 时间（Time）
	- 物理量数据，完整的包括整数和单位两个部分，用至少一个空格隔 开， 仅用于仿真不可综合
- 错误等级（Severity Level）
	- TYPE severity_level IS (NOTE、 WARNING、 ERROR、 FAILURE)
	- 表示系统状态，仅用于仿真不可综合

### IEEE 预定义标准逻辑位与矢量

- 标准逻辑位（Std_Logic）：
- 标准逻辑位矢量（ Std_Logic_vector）
	- 基于 Std_Logic 类型的数组
	- 使用 Std_Logic 和 Std_Logic_Vector 要调用 IEEE 库中的 Std_Logic_1164 程序包; 就综合而言，能够在数字器件中实现的是“－、0、1、 Z”四种状态。

### 自定义数据类型

- TYPE 数据类型名 IS 数据类型定义 OF 基本数据类型 或 TYPE 数据类型名 IS 数据类型定义
	- 数组：
		- `type value_type is array (127 downto 0) of integer;
		- `type matrix_type is array (0 to 15,0 to 31) of std_logic;
	- 枚举：
		- `type states is (idle， decision，read，write);
		- `type boolean is (false， true);
		- `type bit is (‘0’， ‘ 1’);
- SUBTYPE 子类型名 IS 基本数据类型定义 RANGE 约束范围
	- 例子：`subtype digit is integer range 0 to 9;

### 数据类型转换

- 类型标记法
	- `Variable A: integer; A= integer (B);`
	- `Variable B: real;B=real(A);`
- 函数法
	- `Conv_interger (A);`
	- 由 std_logic 转换为 integer 型， 在 std_logic_unsigned 包。
- 常数转换法 / 常量转换法
	- `Type conv_table is array(std_logic) of bit;`
	- `Constant table:conv_table:=(‘0’|’L’=>’0’, ‘1’|’H’=>’1’, others=>’0’);`
	- `Signal a: bit; Signal b: std_logic; A<=table(b);`

## 属性

- 属性提供的是关于信号、类型等的指定特性。
- event：
	- 若属性对象有事件发生，则生成布尔值“true”，常用来检查时钟边沿是否有效。
	- 上升沿：`Clock’ EVENT AND Clock=‘1’
- range：
	- 生成一个限制性数组对象的范围
		- ``range: “0 to n” ；
		- `reverse_range：“n downto 0”
- left：
	- 生成数据类型或数据子类型的左边界值；
	- 类似：right, high, low, length

## 运算符

- 算术运算符
	- ``＋， －， *， / ， MOD， REM ， SLL ， SRL ， SLA， SRA ， ROL ， ROR ， **， ABS
	- 移位运算
		- 移位运算符的左边为一维数组，其类型必须是 BIT 或 BOOLEAN， 右边必须是整数移位次数为整数的绝对值
			- SLL：将位向量左移， 右边移空位补零；
			- SRL：将位向量右移，左边移空位补零；
			- SLA：将位向量左移，右边第一位的数值保持原值不变；
			- SRA：将位向量右移，左边第一位的数值保持原值不变；
			- ROL 和 ROR：自循环左右移位。
		- 例子
			- `“1100”SLL1 =“1000” “1100”SRA1 =“1110”`
			- `“1100”SRL1 =“0110” “1100”ROL1 =“1001”`
			- `“1100”SLA1 =“1000” “1100”ROR1 =“0110”`
	- 取余运算
	- 取模运算
- 关系运算符
	- `＝， /＝， <， > ， <=， >=`
- 逻辑运算符
	- `AND， OR， NAND， NOR， XNOR， NOT，XOR`
- 赋值运算符
	- `<=，:=`
- 关联运算符
	- `=>`
- 其他运算符
	- `＋， －， &`
	- 此处 `+, -` 是指正负
- 并置操作符`&
	- `a <= '1'&'0'&d(1)&'1';`
	- 元素与元素并置，并置后的数组长度为 4
- 运算符优先级
	- 逻辑、 算术运算符
	- 乘法运算符
	- 正负运算符
	- 加减、并置运算符
	- 关系运算符
	- 逻辑运算符
