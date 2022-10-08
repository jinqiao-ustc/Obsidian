---
date created: 2022-10-07 19:42
date updated: 2022-10-07 20:03
---

## 基本结构

- 实体（ Entity）：描述所设计的系统的外部接口信号，定义电路设计中 所有的输入和输出端口；
- 结构体 (Architecture）：描述系统内部的结构和行为；
- 包集合 (Package）：存放各设计模块能共享的数据类型、常数和子程序等；
- 配置 (Configuration）：指定实体所对应的结构体；
- 库 (Library）：存放已经编译的实体、结构体、包集合和配置；
- 例子：

```VHDL
LIBRARY IEEE; USE IEEE.Std_Logic_1164.ALL;
-- 库、 程序包的说明调用
ENTITY FreDevider IS 
PORT (Clock: IN Std_logic; 
	  Clkout: OUT Std_logic);
END;
-- 实体声明
ARCHITECTURE Behavior OF FreDevider IS SIGNAL Clk:Std_Logic; 
-- 结构体定义
BEGIN
	PROCESS(Clock） 
BEGIN
	IF rising_edge(Clock） THEN
		Clk<=NOT Clk;
	END IF; 
END PROCESS; 
Clkout<=Clk; 
END;
```

## 实体（ Entity）

- 实体描述了设计单元的输入输出接口信号或引脚，是设计实体 经封装后对外的一个通信界面。

```VHDL
ENTITY 实体名 IS 
[ GENERIC（常数名：数据类型：设定值）] 
PORT (端口名 1：端口方向 端口类型； 
	  端口名 2：端口方向 端口类型； 
	  ...
	  端口名 n：端口方向 端口类型 ）； 
END [实体名 ]；
```

- 实体名由设计者自由命名，用来表示被设计电路芯片的名称， 但是必须与VHDL程序的文件名称相同。
- 类属说明
	- 类属为设计实体与外界通信的静态信息提供通道，用来规 定端口的大小、实体中子元件的数目和实体的定时特性等。
	- 格式：
		- GENERIC（常数名：数据类型：设定值；
			...
			常数名：数据类型：设定值）；
	- 例如：
		- `GENERIC (wide： integer:=32);--说明宽度为32
		- `GENERIC (tmp： integer:=1ns);--说明延时1 ns
- 端口方向：IN， OUT ， INOUT ，BUFFER
	- “OUT”和“ BUFFER”都可定义输出端口;
	- 若实体内部需要反馈输出 信号， 则输出端口必须被 设置为“ BUFFER”，而不 能为“ OUT”。
	- 同方向、同类型的端口可放在同一个说明语句中。

| 说明符        | 含义                                  |
| ---------- | ----------------------------------- |
| IN(输入)     | 信号进入实体内部，内部的信号不能从该端口输出。             |
| OUT(输出)    | 信号从实体内部输出，不能通过该端口在实体内部反馈使用。         |
| INOUT(双向)  | 信号不但可以输入到实体内部，还可以从实体内部输出，也允许用于内部反馈。 |
| BUFFER(缓冲) | 信号输出到实体外部，同时也可通过该端口在实体内部反馈使用。       |

## 结构体 (Architecture）

- 结构体定义了设计单元具体的功能，描述了该基本设计单元的 行为、 元件和内部的连接关系。
- 一个实体可对应多个结构体，每个结构体代表该实体功能的不同实现方案或不同 实现方式。
- 同一时刻只有一个结构体起作用，通过CONFIGURATION决定用哪个结构 体进行仿真或综合。
- 在结构体描述中，具体给出了输入、输出信号之间的逻辑关系。

```VDHL
ARCHITECTURE 结构体名 OF 实体名 IS
[声明语句]
BEGIN 
	功能描述语句 
END [结构体名];
```

```VHDL
ARCHITECTURE Behavior OF FreDevider IS SIGNAL Clk:Std_Logic;
-- 结构体定义
BEGIN
	PROCESS(Clock)        --信号声明
BEGIN
	IF rising_edge(Clock) THEN
		Clk<=NOT Clk;
	END IF; 
END PROCESS; 
Clkout<=Clk; 
END;
```

## 库、 程序包的调用

```VDHL
LIBRARY 库名 ; 
USE 库名 .程序包名.项目名；
```

```VDHL
LIBRARY IEEE; 
USE IEEE.Std_Logic_1164.ALL;
USE IEEE.Std_Logic_Arith.ALL;
USE IEEE.Std_Logic_Unsigned.ALL;
```
