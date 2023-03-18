---
title: Verilog语法(3)
date: 2020-3-1 9:11:00
tags: [Verilog]
---

前面记录了Verilog的设计常用语法、一些不常用的语法，这篇博客记录一些Verilog较为高级的用法

<!--more-->

### 时序和延迟

- 延迟模型：分布延迟，集总延迟、路径延迟（引脚到引脚的延迟）

  - 分布延迟：这个就是前面提到的分布元件上的延迟，比如一个逻辑门上的延迟，延迟可以使用逻辑门调用时的延迟表示，或者用连续赋值时的延迟表示

    ```verilog
    and #5 a0(out, a, b);
    
    assign #5 out = a & b;
    ```

  - 集总延迟：这个就是将一个模块中的延迟全部集中到输出门上，前面的逻辑门不设延迟，表示方法和分布延迟表示类似，只是集总延迟全部表示到输出端上

  - 路径延迟：这种延迟是最为常见的，就是表示从每个输入端到输出端的延迟，表示可以使用`=>` 和`*>`表示，前者表示单个引脚到引脚的延迟，后者表示源域中任意一个引脚到目标域所有引脚的延迟，延迟要使用在`specify  endspecify`块中，可以使用`specparam`参数定义延迟，还可以使用条件延迟语句表示延迟，如下面的例子（这个例子是不对的，只是为了展示多一些功能，实际代码是不能前后矛盾的)

    ```verilog
    module top(out , a, b, c, d);
    input a, b, c, d;
    output out;
    
    specify 
    	(a=>out) = 9;/*并行，若是a和out都是[1:0],则这个句子等价于(a[0]=>out[0]) = 9;(a[1]=>out[1]) = 9;*/
    	
    	//全连接 
    	(（a,b,c,d) *> out) = 9; //表示a,b,c,d的每一位到out的每一位的延迟都是9
    	
    	//边沿敏感延迟
    	(posedge clk => (out +: in)) = (1,2);
    /*在clk的上升沿，从clk到out的路径，其上升延时是1，下降延时是2，从in到out的数据路径是同向传输，即out = in，若是-号，则是out = ~in;*/
    
    	//specparam
    	specparam  a_to_out = 9;
    	(a=>out) = a_to_out;
    	
    	//条件路径延迟
    	if(a) (a=>out) = 9;
    	if(~a) (a=>out) = 11;
    endspecify
    ```

  - 路径延迟里的上升、下降、关断延迟

    这一点在前面已经提到过了，这里具体说明一下，延迟值的个数只能是1、2、3、6、12这个5中形式，可以结合最小值:典型值:最大值的方式来使用

    规则是

    1个：所有延迟共用一个延迟值

    2个：上升延迟(0->z，0->1，z->1)，下降延迟(1->z，z->0，1->0)

    3个：上升延迟(0->1, z->1)，下降延迟(1->0, z->0)，关断延迟(1->z, 0->z)

    6个：0->1, 1->0, 0->z, z->1, 1->z, z->0

    12个： 0->1, 1->0, 0->z, z->1, 1->z, z->0, 0->x, x->1, 1->x, x->0, x->z, z->x

  - 当没有指定x的路径延迟时，采用最为保守的方式来计算

    - 当x到已知态的转换消耗的最大时间
    - 当已知态到x的转换消耗的最小时间

- 时序检查

  - `$setup`：有效时钟变沿到达之前的最下时间，就是在时钟到来之间要先建立好信号，这个信号需要在时钟到来之前建立好：`$setup(data_event, reference_event, limit);`

    ```verilog
    specify
        $setup(data,posedge clk,3); //data:被检查，posedge clk：参考信号，3:最小时间
    endspecify
    ```

  - `$hold`：保持时间：`$hold(reference_event, data_event,limit);`

    ```verilog
    specify
        $hold(posedge  rst_n, data, 5);
    endspecify
    ```

  - `$width`：宽度检查：`$width(reference_event, limit);`



### 用户自定义原语UDP

- 用户自己定义一个原语，就好像Verilog自带的原语and、or一样，使用方式也是一样的

- 定义方式：

  - 输出必须在端口第一个，且只能有一个输出，输入可以有多个；输入输出都只能有一位
  - 时序逻辑UDP需要将输出定义成reg型，且可以使用initial语句赋予初始值
  - primitive和module时同级的，定义时需要在module外，但是module可以例化primitive，但是primitive不可以调用任何模块以及其他原语
  - 状态表的输入只允许是0、1、x，输入z将被认作x
  - 无关项可以使用？表示
  - 使用`table` `endtable`来写状态，就像数字电路里的真值表，书写的顺序要和前面端口列表里的顺序一致

  实例：逻辑电路

  ```verilog
  primitive mux_4_to_1 (out,i0,i1,i2,i3,s0,s1);
  output out;
  input i0, i1, i2, i3, s0, s1;
  table
  	//i0 i1 i2 i3 s0 s1 : out  按定义的顺序
  	   1  ?  ?  ?  0  0 : 1;
  	   0  ?  ?  ?  0  0 : 0;
  	   ?  1  ?  ?  1  0 : 1;
         ?  0  ?  ?  1  0 : 0;
         ?  ?  1  ?  0  1 : 1;
         ?  ?  0  ?  0  1 : 0;
         ?  ?  ?  1  1  1 : 1;
         ?  ?  ?  0  1  1 : 0;
         ?  ?  ?  ?  x  ? : x;
         ?  ?  ?  ?  ?  x : x;
  endtable
  endprimitive
  ```

  实例：时序电路 电平敏感

  ```verilog
  primitive latch(q, d, clk, clear)
  output q;
  input d, clk, clear;
  reg q;
  initial 
      q = 0;
  table
      //  d clk clear :q : q+
      	? ?     1   :? : 0;
      	1 1     0   :? : 1;
      	0 1     0   :? : 0;
      	? 0     0   :? : -; //保持不变
  endtable
  endprimitive
  ```

  实例：时序电路 边沿敏感

  ```verilog
  primitive edge_dff(q, d, clk, clear)
      output q;
  	reg q;
      input d, clk, clear;
      initial 
          q = 0;
      table
          ? ? 1 : ? : 0;	//复位
          ? ? (10):?: -;	//清零跳转不影响
          1 (10) 0:?: 1;	//下降沿锁存1
          0 (10) 0:?: 0;	//下降沿锁存0
          ? (1x) 0:?: -;	//由1变为不确定，不变
          ? (0?) 0:?: -;	//由0变为不确定，不变
          ? (x1) 0:?: -;	//由不确定变1，不变
          (??) ? 0:?: -;	//时钟不变的时候，不变
      endtable
  endprimitive
  ```

  一行里不能有多个跳变的变量

  - 关于跳变，还可以有如下的缩写

    | 缩写 | 含义             | 解释         |
    | ---- | ---------------- | ------------ |
    | ?    | 0,1,x            | 不可用于输出 |
    | b    | 0,1              | 不可用于输出 |
    | -    | 不变             | 只用于输出   |
    | f    | (10)             | 下降沿       |
    | r    | (01)             | 上升沿       |
    | p    | (0x)、(01)、(x1) | 可能是上升沿 |
    | n    | (1x)、(10)、(x0) | 可能是下降沿 |
    | *    | (??)             | 任意变化     |

    

  