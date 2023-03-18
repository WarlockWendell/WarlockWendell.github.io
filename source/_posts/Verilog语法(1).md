---
title: Verilog语法(1)
date: 2020-1-15 13:51:22
tags: [Verilog]
---

之前记录的Verilog语法规则在电脑系统崩掉之后重装时全部丢失了，所以这里重新记录一下Verilog的基础语法以及在实战中的一些运用方法。

<!--more-->

# 1. 电路设计的语法

## 1.1 设计不使用的语法

- `initial`：设计时不使用，仿真时使用
- `task/function`：设计时不使用，仿真时很少用
- `for/while/repeat/forever`：设计不用，仿真很少用
- `integer`：设计不用
- 模块内部不能有X态、Z态，内部不能有三态接口
- `casex/casez`：设计和仿真都不用
- `force/wait/fork`：设计不用，仿真很少用
- `#5`：延时语句，设计不用，仿真常用

## 1.2 设计时使用的语法

- `reg/wire、parameter`：定义参数的语法
- `assign、always`：很常用的语法
- 一般只用`if else`和`case` 两种条件语句
- `+、-、*、/、%`：算术运算符可以使用
- `=，<=`：赋值运算符，时序逻辑用`<=`，组合逻辑用`=`
- 关系运算符：`>，<，>=，<=`
- 逻辑运算符：`&&，||，!`
- 位运算符：`~，|，^，&`
- 移位运算符：`<<，>>`
- 拼接运算符：`{}`

# 2. 电路设计的结构

- 组合逻辑：

  ```verilog
  always @ (*) begin
  	expression
  end
  ```

- 同步复位时序逻辑

  ```verilog
  always @ (posedge clk) begin
  	if(rst_n == 1'b0) begin
  		expression
      else begin
  		expression
  	end
  end
  ```

- 异步复位时序逻辑

  ```verilog
  always @ (posedge clk or negedge rst_n) begin
  	if(rst_n == 1'b0) begin
  		expression
  	else begin
  		expression
  	end
  end
  ```

# 3. 电路设计要点

- 一个`always` 只产生一个信号：设计清晰简洁方便调试
- 一个信号只在一个`always`中产生
- `always` 模块要考虑到全部的情况
- 条件判断只用`if else`或者`case`
- 含有`posedge`或者`negedge`的一定是时序电路
- 即改即用使用组合逻辑，延时改变使用时序逻辑

# 4.模块例化

例化可以简单理解为编程语言中的函数功能（但是实际上并不一样），当有一个模块被例化两次之后，这两个模块都占有实际的电路结构，虽然这两个功能都有相同的功能，并不会共用一套电路，编程语言中的函数是随用随分配，这是例化和函数的一个区别

- 例化的方法：

  ```verilog
  //设计模块
  module uart(
  	clk,
  	rst_n,
  	vld_in,
  	data_in,
  	uart_out,
  	uart_in,
  	vld_out,
  	data_out,
  	rdy_in
  );
  
  //例化方法：  
  uart u1(                //例化的模块名，实例的模块名
  	.clk(clk_100M),
  	.rst_n(sys_rst_n),
  	.vld_in(bt_data_out_vld),
  	.data_in(bt_data_out),
  	.uart_out(uart_tx),
  	.uart_in(uart_rx),
  	.vld_out(uart_data_out_vld),
  	.data_out(uart_data_out),
  	.rdy_in(uart_in_rdy)
  );
  ```

- 参数的例化

  ```verilog
  //设计模块
  module uart(
  	clk,
  	rst_n,
  	vld_in,
  	data_in,
  	uart_out,
  	uart_in,
  	vld_out,
  	data_out,
  	rdy_in
  );
  parameter DATA_W = 8;
      
  
  //例化方法：  
      uart#(.DATA_W(16)) u1(                //例化的模块名，实例的模块名,这里将模块的8位位宽改成了16位的位宽
  	.clk(clk_100M),
  	.rst_n(sys_rst_n),
  	.vld_in(bt_data_out_vld),
  	.data_in(bt_data_out),
  	.uart_out(uart_tx),
  	.uart_in(uart_rx),
  	.vld_out(uart_data_out_vld),
  	.data_out(uart_data_out),
  	.rdy_in(uart_in_rdy)
  );
  ```

# 5. 运算符

## 5.1 信号类型reg/wire

- 设计代码仅用`reg`和`wire`
- 由本模块且是由`always`产生的信号，都用`reg`
- 测试代码中，用`initial`产生的信号（一般是对测模块的输入），用`reg`类型
- 其他的都用`wire`
- `reg`类型的信号，也不一定是产生寄存器的（比如组合逻辑中的一些信号，也是由`always`产生的，也是用`reg`，但是这里没有寄存器）

## 5.2 参数parameter

```verilog
parameter CNT = 12;
```

可以简单理解为C语言中的宏定义，例如上述代码的作用就是将整个模块中的`CNT`全部替换成12

## 5.3 算术运算符

- `+、-、*、/、%`：一般除法和取余比较少于，因为它们涉及到的逻辑比较复杂

## 5.4 关系运算符

`>=，<=，==，>，<`：一般用于条件判断

## 5.5 赋值运算符

- `>=`：时序逻辑里使用
- `=`：组合逻辑里使用

## 5.6 逻辑运算符

- `&&`：与
- `||`：或
- `!`：非
- 一般来说两边都是1bit，多用于条件判断

## 5.7 位运算符

- `&`：按位与
- `|`：按位或
- `~`：按位取反
- `^`：按位异或
- 一般来说用于赋值运算

## 5.8 移位运算符

- `>>`：右移
- `<<`：左移
- 一般用于乘除运算

## 5.9 拼接运算符

- `{}`：将不同bit的数拼接在一起
- `{3{a[0]}}`：表示`a[0]a[0]a[0]`
