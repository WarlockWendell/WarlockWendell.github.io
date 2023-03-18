---
title: Verilog语法(2)
date: 2020-2-28 9:28:33
tags: [Verilog]
---

本篇主要记录Verilog语言一些我之前没有了解到的知识。

<!--more-->

- 连续赋值语句

  `assign [drive strength] [delay3] list_of_net_assignments` 中括号内为可以省略的，其中驱动强度默认为`strong0`和`strong1`。延迟类似于门级延迟

  赋值延迟有三种方式：

  - 普通赋值延迟：`assign #delay_time out = i1&i2;`，在这个句子中，每当`i1`或者`i2`有一个变化时，在经过延迟之后再对`out`进行赋值，但是注意：进行赋值时取的是实时值，而不是刚发生变化时的值，换句话说，当脉冲时间小于延迟时间，就不产生变化

  - 隐式连续赋值延迟：

    ```verilog
    wire #10 out = i1 & i2;
    //等价于
    wire out;
    assign #10 out = i1 & i2;
    ```

  - 线网声明延迟

    ```verilog
    wire #10 out;
    assign out = i1 & i2;
    //等价于
    wire #10 out = i1 & i2;
    ```

- 门级延迟的使用格式:`#(rise_min:rise_typ:rise_max,fall_min:fall_typ:fall_max,turnoff_min:turnoff_typ:turnoff_max)`

  举例

  `fulladder#(1:2:3,2:3:4,3:4:5) u0(port map);`

- 非阻塞赋值`<=`的赋值逻辑：并行执行，当产生变化时，仿真器将暂存右边的变量，然后当达到仿真时刻时就可以将右侧的值赋值给左侧

  举例

  ```verilog
  always @ (posedge clk)
  	a = b;
  always @ (posedge clk)
  	b = a;
  	
  always @ (posedge clk)
  	a <= b;
  always @ (posedge clk)
  	b <= a;
  ```

  如果理解了逻辑，就可以发现第一种模式将产生竞争，最后两个变量会得到相同的值

  第二种模式方可达到交换两变量值的目的

  > 应注意：连续赋值语句使用延时时，到了赋值时刻赋予左值的是右值得实时值，而不是语句书写时的值
  >
  > 而非阻塞赋值延时时，赋予的是赋值语句书写时的值

- 过程语句赋值延迟和连续赋值延迟的问题

  连续语句赋值的问题前面提到了，当使用`assign #delay_time commmad`的格式时，取的操作值是实时值，而在过程赋值中，延迟方式比较多变

  - 当使用`#delay_time command`，则是在延迟之后再执行操作
  - 当使用`a <= #10 in`的非阻塞式赋值时或者使用`a = #10 in`阻塞式赋值时，都是暂存变量，然后再等待10个单位之后，将暂存的值赋予左值，而不是像连续赋值一样实时赋值
  - 同理可以延伸，在过程赋值语句中`a = @(posedge clk) b`就是在发生时立即计算b的值暂存，然后等满足条件了（也就是等来了时钟的上升沿）就将值赋给a

- 事件控制: **event**

  可以定义一个事件，通过条件控制它的发生，然后根据它的变化执行其他操作，用`->`来触发事件，用`@`来接收事件

  ```verilog
  event receive_data;
  
  always @ (posedge clk)
  begin
  	if(last_data_packet)
  		-> receive_data;
  end
  
  always @ (receive_data)
  	data_r = data;
  ```

  其实这种操作，可以使用类似于`flag`变量的形式来实现，可以不使用`event`的用法，可达到同样的目的

- 电平敏感控制：**wait**

  ```verilog
  always
  	wait(enable) a <= b;
  ```

  这种功能经常使用选择分支来实现，而不是例子中的这种

- `case`和、`casex`、`casez`

  - `case`下的表达是可以使用`x`和`z`这两个值的
  - `casex`和`casez`分别表示下面条件的`x`和`z`表示无关位，用`?`来理解

- 顺序块和并行快

  - 使用`begin` `end`的就是顺序块
  - 使用`fork` `join`的是并行块
  - 顺序块中所有的语句是顺序执行的，除非有时序控制或者非阻塞赋值模块
  - 并行块中所有语句是并发执行的，并且可以使用时序控制语句来控制执行的顺序，因此，并行块中语句无顺序要求

- 块语句的特点

  - 嵌套块：并行块和顺序块可以混合使用
  
  - 命名块和禁止命名块
  
    可以给一个块取名，可以在块内定义局部变量，然后可以通过层次名来取其他块中的局部变量
  
    ```verilog
    module top;
    
    initial begin
    	integer i;
    	begin: block1
    		integer j; //可以通过top.block1.j的方式来调用这个变量
    		...
    		...
    	end
    end
    //下面举个嵌套块的例子
    initial 
    begin
    	x = 1;
    	fork 
    		#5 y = 1;
    		#10 z = 0;
    	join
    	#20 x = {y,z};
    end
    	
    ```
  
  - 可以使用`disable block1;`的方式禁用`block1`
  
- 生成块：在需要重复操作或者不确定模块中是否需要某个块时，可以使用生成块`generate`  `endgenerate`

  - 可以使用`net reg integer real time realtime event`
  - 使用生成块可以极大地加快工作效率

  - 控制生成块生成的循环计算，可以使用`genvar`声明一个临时变量
  - 使用生成块多是结合循环语句、条件语句、case语句来使用的

- 理解`@`符号，`@`符号可以理解为等待`@`后面的事件，再执行接下来的操作，比如`@(posedge clk) a = b;`就是指等到时钟上升沿来时，将b的值赋给a。`repeat(2) (@posedge clk);` `repeat`就是指重复两次后面的操作`@(posedge clk)`，而这个操作又是指等待上升沿到来时执行其后的语句，而这里的语句是空语句，所以这句话最终起到的作用就是等待两个时钟周期

- task和function

  - 区别：task可以理解为普通的Verilog代码，可以包含时序逻辑，function只可以是纯组合逻辑；task可以调用其他task和function，function只能调用function；task可以有input、inout、output，function必须有一个及以上的input，不能有输出，但是可以返回一个值

  - task：在同一个模块中调用同一个task是对同一块地址进行操作，所以容易出错，使用`automatic`命令可以自动分配内存。`task automatic task_name;`，调用的使用格式和门语句还是很像的，port map的顺序和task中定义的顺序是一致的

    ```verilog
    task automatic task1;
    input [3:0] a;
    input [3:0] b;
    output [4:0] out;
    begin
    	#5 out = a + b;
    end
    endtask
    
    //调用
    initial 
    	taks1;
    ```

  - function： function的使用方式和task基本是一致的，需要注意的是，function在定义时，隐式定义了一个寄存器变量，用来存放函数返回的值，这个值在函数被调用的地方被使用。同样使用`automatic`参数，可以为多次被调用的同一个函数开辟多个空间，避免错误，注意到使用递归函数的时候，就需要用到这个

  - 函数还有其他的一些定义方法，比如`function integer cal(input [3:0] a);`可以返回一个常数；`function signed [63:0] cal(input [7:0] a);`可以返回一个位宽为64有符号数

- `assign`和`deassign`：过程连续赋值，在过程语句里使用`assign`，在这个模块再次被触发之前，变量暂时被`assign`覆盖，然后等到`deassign`取消覆盖，得到它原来的值，这种方式十分糟糕，不建议使用

  ```verilog
  always @ (negedge clk)
  begin
  	q <= d;
  	q_n <= ~d;
  end
  
  always @ (reset)
  begin
  	if(reset) begin //暂时覆盖
  		assign q <= 1'b0;
  		assign q_n <= 1'b1;
  	end
  	else begin //取消覆盖
  		deassign q;
  		deassign q_n;
  	end
  end
  ```

- `force`和`release`：也是强制赋值，使用在仿真模块中，强制改变一个线网或者寄存器的值

  ```verilog
  initial begin
  	#50 force q = a & b;
  	#100 relaese q;
  end
  ```

- 例化模块改变参数的另一种方法：例化的时候改变参数十分常见，前面也介绍了一种方法，通过`#(.id(id))`的方式。`defparam`也可以实现相同的功能，但是这种代码是十分糟糕的形式，不建议使用

  ```verilog
  defparam u0.id = 1, u1.id = 2;
  cal u0(port_map);
  cal u1(port_map);
  ```

- \`ifdef \`ifndef \`elsif \`else \`endif 这些语句和C语言的条件编译十分相似，都是找是否定义了相应的文本宏，文本宏的定义方式是\`define ，上面的条件编译语句不允许使用逻辑运算

- 条件执行语句：只有在系统添加了相应的标志时，才会运行，否则都不运行

  - `$test$plusargs` ，可以指定`+DISPLAY_VAR`选项在程序运行时设置标志

  	```verilog
module test;
initial begin
    	if($test$plusargs("DISPLAY_VAR")) //只有添加了这个标志才显示
 	 		$display("...");
	  	else
 	 		$dispaly("///");
	  end
	  
	  endmodule
	  ```

  - `$value$plusargs`

    ```verilog
    module test;
    
    reg[11:0] string_a;
    integer period = 10;
  
    initial begin
    	if($value$plusargs("testname=s%",string_a))
    		$readmemh(string_a,vectors);
    	else
    		$display("error");
    	
    	if($vale$plusargs("clk_t=d%",period))
    		forever #(period/2) clk = ~clk;
    	else
    		$dispaly("error");
    end
    
    endmodule
    ```
    
    要启用上述选项，用带`+testname = test1.vec  +clk_t = 10 testname = "test1.vec" period = 10`的命令行来启动仿真器
  
- 时间尺度：`timescale 1ns/1ps`，这个语句在仿真时使用，但是并没有理解它的意思，其意义是时间测量单位\延迟的时间单位，只能写1、10、100，通俗的说就是参考时间单位\精度；

  ```verilog
  `timescale 100ns/1ns
  #5 //延迟500ns
  ```

- 文件操作

  - 打开文件：`fopen`

    ```verilog
    integer handle1 , handle2, handle3;
    //标准输出是打开的：decription = 32'h0000_0001;
    initial 
    begin
    	handle1 = $fopen("file1.out");//handle1 = 32'h0000_0002;
    	handle2 = $fopen("file2.out");//handle1 = 32'h0000_0004;
    	handle3 = $fopen("file3.out");//handle1 = 32'h0000_0008;
    end
    ```

  - 写文件：`fdispaly` `fmonitor` `fwrite` `fstrobe`

    ```verilog
    ...
    integer desc1, desc2;//定义两个文件描述符
    initial
    begin
    	des1 = handle1 | 1;
    	$fdispaly(dsc1,"dispaly1");//同时在file1和标准输出里写文件
    	
    	desc2 = handle3;
    	$fdispaly(desc2,"dispaly2");//在file3里写文件
    end
    ```

  - 关闭文件：`fclose`

    关闭之后的文件就不可以写了，重新打开可能会获得另外一个通道

- 显示层次的`m%`，使用输出时后面不用附加参数

- 在与`display`同时执行的语句中，两者执行的顺序并不明确，使用`strobe`可以确保是先进行运算，再显示

- 随机数生成函数

  ```verilog
  $random; 
  $random(seed);
  $random % 60; //-59到59
  {$random} % 60; //0到59
  ```

- 使用文件初始化存储器 `readmemb`和`readmemh`

  ```verilog
  $readmemb("<filename>",<memory_name>,[<start_addr>],[<finish_addr>]);
  //$readmemh使用方法一样
  ```

  ```verilog
  module top;
  
  reg[7:0] memory_a [0:7];//8 x 8存储器
  
  initial begin
  	$readmemb("init.dat",memory_a);
  end
  endmodule
  ```

  将`"init.dat"`文件中的内容写入到存储器中

  文件里的格式如下

  ```verilog
  @002 //使用@表示文件的写入位置，使用十六进制表明，数据使用空格分开，未初始化默认为x
  00000000 00000001
  00010101 10010110 
  
  @006
  00001011 01111010
  ```

- VCD文件转储：使用例子来说明转储文件的使用方法，主要是讲仿真信息，如输出，波形等信息转储到文件中

  ```verilog
  initial 
  	$dumpfile("file1.dmp");//将仿真信息转储到file1.dmp中
  
  initial 
  	$dumpvars; //不指定范围，则为转储所有变量信息
  initial
  	$dumpvars(2,top.m2);//表示转储top.m2下两层的变量信息，使用0就是转储top.m2下所有变量的信息
  	
  initial begin
  	$dumpon; //开始转储
  	#1000000 $dumpoff;//仿真一段时间后停止
  end
  
  initial 
      $dumpfall;//生成一个检测点，转储所有VCD变量的当前值(执行的时刻，在这里就是仿真开始处)
  ```

  `.vcd`格式是任何仿真器默认可以打开的，`.fsdb`也是较为常用的一种存储波形的文件格式

<font size=2>主要参考《Verilog+HDL数字设计与综合（第二版）》</font>

