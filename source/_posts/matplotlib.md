---
title: matplotlib 使用笔记
date: 2021-11-20
tags: [python]
---

莫烦大佬的课程笔记，简洁的matplotlib入门教程

<!--more-->

```python
%matplotlib notebook
from matplotlib import pyplot as plt
```

    Warning: Cannot change to a different GUI toolkit: notebook. Using qt instead.

## 基本用法


```python
import numpy as np
x = np.linspace(-1,1,50)
y = 2*x + 1
plt.plot(x,y)
plt.show()
```

## figure
- %matplotlib inline 就是在文档里显示，适合保存md文件
- %matplotlib 弹窗
- %matplotlib notebook 嵌入式的交互式的，但是要重启内核
---
- 调整框的名字，图片大小的方法
- 在一张图里画两条线
- 线的宽度、线的颜色、线的风格


```python
import numpy as np

x = np.linspace(-3,3,50)
y1 = 2*x + 1
y2 = x**2
plt.figure(num=2,figsize=(10,10))
plt.plot(x,y1)
plt.show()

plt.figure(num=3)
plt.plot(x,y2)
plt.plot(x,y1,color = 'red',linewidth=10.0,linestyle='--')
```




    [<matplotlib.lines.Line2D at 0x1afd5a1e848>]



## 坐标轴设置
- 给坐标轴设置范围
- 加坐标轴名
- ticks
- ticks是可以用LaTex符号的
- 坐标轴的位置
    - spines: 对应图片的四个边框
    - `ax.xaxis.set_ticks_position('bottom')`绑定x轴到下面的框
    - `ax.spines['bottom'].set_position(('data',-0.0))`设定位置，根据data设置即可


```python
import numpy as np

x = np.linspace(-3,3,50)
y1 = 2*x + 1
y2 = x**2

plt.figure()
plt.plot(x,y2)
plt.plot(x,y1,color = 'red',linewidth=1.0,linestyle='--')
plt.xlim((-1,2))
plt.ylim((-2,3))
plt.xlabel('I am x')
plt.ylabel('I am y')
new_ticks = np.linspace(-1,2,5)
plt.xticks(new_ticks)
plt.yticks([-2,-1,-1,1.22,3,],
          [r'$really\ bad$',r'$bad$',r'$normal$',r'$good$',r'$really\ good\ \alpha$'])

# 调整位置
# gca = 'get current axis'
ax = plt.gca()
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
# 用另外两个边框绑定x和y轴
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['bottom'].set_position(('data',-0.0)) # outward, axes(百分比表示)
ax.spines['left'].set_position(('data',-0.0))
```

## legend 图例
- 给线条加label(取名字)
- plt.legend() 加图例 可以不使用参数
  - handles=[line1,line2]
  - labels=['aaa','bbb']
  - loc='best' 还有其他的


```python
import numpy as np

x = np.linspace(-3,3,50)
y1 = 2*x + 1
y2 = x**2

plt.figure()

plt.xlim((-1,2))
plt.ylim((-2,3))
plt.xlabel('I am x')
plt.ylabel('I am y')
new_ticks = np.linspace(-1,2,5)
plt.xticks(new_ticks)
plt.yticks([-2,-1,-1,1.22,3,],
          [r'$really\ bad$',r'$bad$',r'$normal$',r'$good$',r'$really\ good\ \alpha$'])

plt.plot(x,y2,label='up')
plt.plot(x,y1,color = 'red',linewidth=1.0,linestyle='--',label='down')
plt.legend()

## 使用参数
l1, = plt.plot(x,y2,label='up') #不止一个返回值，但是只需要handle这一个返回值
l2, = plt.plot(x,y1,color = 'red',linewidth=1.0,linestyle='--',label='down')
plt.legend(handles=[l1,l2],labels=['aaa','bbb'],loc='best')
```




    <matplotlib.legend.Legend at 0x1afdf579888>



## annotation 注解
- 文字型: `plt.text()`
 - x坐标，y坐标，内容
- 注解型: `plt.annotate()`
 - text 内容，这里用到了%s 这种用法，格式化输出
 - xy = (x0,y0) 指向的点嘛
 - xycoords = 'data' 坐标类型吗
 - xytext = (+30,-30) 内容的位置
 - textcoords = 'offset points' 类型吗
 - arrowprops = dict(arrowstyle='->',connectionstyle='arc3,rad=.2') 箭头类型，连接类型


```python
import numpy as np
x = np.linspace(-3,3,50)
y = 2*x + 1
plt.figure(num=1,figsize=(8,5),)
plt.plot(x,y)

ax = plt.gca()
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['bottom'].set_position(('data',0))
ax.spines['left'].set_position(('data',0))

# 加注解
x0 = 1
y0 = 2*x0 + 1
plt.scatter(x0,y0,s=50,color='b')
plt.plot([x0,x0],[y0,0],'k--',lw=2.5)
### method 1
plt.annotate(r'$2x+1=%s$' % y0,xy=(x0,y0),xycoords='data',xytext=(+30,-30),textcoords='offset points',
             fontsize=16,arrowprops=dict(arrowstyle='->',connectionstyle='arc3,rad=.2'))
### method 2
plt.text(-3.7,3,r'$This\ is\ the\ some\ text.\ \mu\ \sigma_i\ \alpha_t$')

```




    Text(-3.7, 3, '$This\\ is\\ the\\ some\\ text.\\ \\mu\\ \\sigma_i\\ \\alpha_t$')



## 坐标轴被挡住
- windows并不能用下面的代码解决这个问题，需要使用zorder来解决
- 有关[zorder](https://www.osgeo.cn/matplotlib/gallery/misc/zorder_demo.html)


```python
import numpy as np
x = np.linspace(-3,3,50)
y = 2*x + 1
plt.figure(num=1,figsize=(8,5),)
plt.plot(x,y,lw=10,zorder=1.49) # 坐标轴好像是1.5

ax = plt.gca()
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['bottom'].set_position(('data',0))
ax.spines['left'].set_position(('data',0))

for label in ax.get_xticklabels() + ax.get_yticklabels():
    label.set_fontsize(12)
    label.set_bbox(dict(facecolor='white',edgecolor='None',alpha=0.7))
```

## 散点图
- scatter
- x,y数据
- c colormap


```python
import numpy as np

n = 1024
X = np.random.normal(0,1,n)
Y = np.random.normal(0,1,n)

T = np.arctan2(Y,X) # for color value,only make it beautiful colorbar
plt.scatter(X,Y,s=75,c=T,alpha=0.5)
plt.xlim((-1.5,1.5))
plt.ylim((-1.5,1.5))
plt.xticks(())
plt.yticks([])

plt.figure(num=2)

plt.scatter(np.arange(5),np.arange(5))
```




    <matplotlib.collections.PathCollection at 0x1afeaebf248>



## 柱状图
- 数据
- facecolor = 里面的color
- edgecoloe = edge的color
- `for x,y in zip(X,Y)` 就是将X,Y分别赋值给x,y 需要使用zip函数
- text 里 ha va都是对齐方式 horizontal vertical


```python
import numpy as np

n = 12
X = np.arange(n)
Y1 = (1-X/float(n))*np.random.uniform(0.5,1.0,n)
Y2 = (1-X/float(n))*np.random.uniform(0.5,1.0,n)

plt.bar(X,Y1,facecolor='#9999ff',edgecolor='white')
plt.bar(X,-Y2,facecolor='#ff9999',edgecolor='white')

for x,y in zip(X,Y1):
    plt.text(x,y+0.05,'%.2f'% y, ha = 'center',va = 'bottom')
for x,y in zip(X,-Y2):
    plt.text(x,y-0.05,'-%.2f'% y, ha = 'center',va = 'top')
    
plt.xlim((-.5,n))
plt.ylim((-1.25,1.25))
```




    (-1.25, 1.25)



## 等高线图 contour
- meshgrid : X和Y都是代表网格，X代表网格cell的横坐标，Y代表网格cell的纵坐标，X Y的size是相同的，都是size(x) * size(y) 这个函数就是生成栅格数据，但是这个数据的存储方式很诡异，和我们想象的是个转置的关系，就很神奇，下面这个解释还算比较合理的
   [解释](https://zhuanlan.zhihu.com/p/126266520)
   因为存储的**是笛卡尔坐标，不是矩阵坐标**
- 里面的8表示分为多少层
- contourf是画图
- contour是加线
- 输入都是X网格 Y网格，代表横纵坐标，以及对应大小的高度函数


```python
import numpy as np

def f(x,y):
    return (1-x/2 + x**5 + y**3) * np.exp(-x**2 - y**2)

n = 256
x = np.linspace(-3,3,n)
y = np.linspace(-3,3,n)

X,Y = np.meshgrid(x,y) 

# 画图
plt.contourf(X,Y,f(X,Y),8,alpha = 0.75,cmap=plt.cm.hot)
plt.colorbar()
# 加边框线
C = plt.contour(X,Y,f(X,Y),8,colors='black',linewidths=0.5)

# 加label
plt.clabel(C,inline=True,fontsize=10)
```




    <a list of 11 text.Text objects>



## image
- interpolation : [文档](https://matplotlib.org/stable/gallery/images_contours_and_fields/interpolation_methods.html)
- cmap: colorbar
- origin: upper就和输入的矩阵一样，lower就和笛卡尔坐标一样（即Y是个反的）**矩阵表示和笛卡尔表示区别也就在Y是否是反的，以及x,y的顺序不同了，因为矩阵的下标，前面代表的是行，对应笛卡尔的y**）


```python
import numpy as np

a = np.array([[0.30,0.36,0.42],
              [0.36,0.44,0.53],
              [0.42,0.53,0.65]])
plt.imshow(a,interpolation='nearest',cmap='bone',origin='upper')
plt.colorbar()

```




    <matplotlib.colorbar.Colorbar at 0x1afef41cec8>



## 3d图像
-  Axes3D 是坐标轴
- plot_surface是画图
- contourf是画等高线,zdir = 'z'是压缩方向呀


```python
import numpy as np
from mpl_toolkits.mplot3d import Axes3D

fig = plt.figure()
ax = Axes3D(fig,auto_add_to_figure=False)
fig.add_axes(ax)
X = np.arange(-4,4,0.25)
Y = np.arange(-4,4,0.25)
X,Y = np.meshgrid(X,Y)
R = np.sqrt(X**2 + Y**2) #中间值
Z = np.sin(R)

ax.plot_surface(X,Y,Z,rstride=1,cstride=1,cmap=plt.get_cmap('rainbow'),edgecolor='black')
ax.contourf(X,Y,Z,zdir='z',offset=-2,cmap = 'rainbow')
ax.contour(X,Y,Z,zdir='z',offset=-2,colors='black')
ax.set_zlim(-2,2)

```




    (-2.0, 2.0)



## subplot 画小图
- 和matlab一脉相传的
- 上大下小，可以先将格式设置为2，1 序号为1，然后设置为2，3，**序号从4开始哈**


```python
plt.figure()

plt.subplot(2,2,1)
plt.plot([0,1],[0,1])

plt.subplot(2,2,2)
plt.plot([0,2],[0,2])

plt.subplot(2,2,3)
plt.plot([0,3],[0,3])

plt.subplot(2,2,4)
plt.plot([0,4],[0,4])

# 自由调整一下
plt.figure(num=2)

plt.subplot(2,1,1) # 第一行只有一列
plt.plot([0,1],[0,1])

plt.subplot(2,3,4) # 重新划分子图 标签顺序需要变化呀
plt.plot([0,2],[0,2])

plt.subplot(2,3,5)
plt.plot([0,3],[0,3])

plt.subplot(2,3,6)
plt.plot([0,4],[0,4])

```




    [<matplotlib.lines.Line2D at 0x1aff68d2c08>]



## subplot in grid
- 看例子即可明白


```python
import matplotlib.gridspec as gridspec

#method 1 subplot2grid
plt.figure()
ax1 = plt.subplot2grid((3,3),(0,0),colspan=3,rowspan=1)
ax1.plot([1,2],[1,2])
ax1.set_title('ax1_title') #其他的类似于label也是要用set，其他啥都是set
ax2 = plt.subplot2grid((3,3),(1,0),colspan=2,rowspan=1)
ax3 = plt.subplot2grid((3,3),(1,2),colspan=3,rowspan=2)
ax4 = plt.subplot2grid((3,3),(2,0),colspan=1,rowspan=1)
ax5 = plt.subplot2grid((3,3),(2,1),colspan=1,rowspan=1)
plt.tight_layout()
# method 2 
plt.figure()
gs = gridspec.GridSpec(3,3)
ax1 = plt.subplot(gs[0,:])
ax2 = plt.subplot(gs[1,0:2])
ax3 = plt.subplot(gs[1:3,2])
ax4 = plt.subplot(gs[2,0])
ax5 = plt.subplot(gs[2,1])
plt.tight_layout()

# method 3
f,((ax11,ax12),(ax21,ax22)) = plt.subplots(2,2,sharex=True,sharey=True)
```

## plot in plot
- 图中的图
- 本质上还是在figure上画图，不过三幅图的坐标轴是不一样的|


```python
fig = plt.figure()
x = [1,2,3,4,5,6,7]
y = [1,3,4,2,5,8,6]

left,bottom,width,height = 0.1,0.1,0.8,0.8 # 百分比
ax1 = fig.add_axes([left,bottom,width,height])
ax1.plot(x,y,'r')
ax1.set_xlabel('x')
ax1.set_ylabel('y')
ax1.set_title('title')

left,bottom,width,height = 0.2,0.6,0.25,0.25 
ax2 = fig.add_axes([left,bottom,width,height])
ax2.plot(y,x,'b')
ax2.set_xlabel('x')
ax2.set_ylabel('y')
ax2.set_title('title inside 1')

plt.axes([.6,.2,.25,.25])
plt.plot(y[::-1],x,'g')
plt.xlabel('x')
plt.ylabel('y')
plt.title('title inside 2')
```




    Text(0.5, 1.0, 'title inside 2')



## 主次坐标轴



```python
import numpy as np

x = np.arange(0,10,0.1)
y1 = 0.05*x**2
y2 = -1 * y1

fig,ax1 = plt.subplots()
ax2 = ax1.twinx() #共享x轴 镜面
ax1.plot(x,y1,'g-')
ax2.plot(x,y2,'b--')
ax1.set_ylabel('Y1',color = 'g')
ax2.set_ylabel('Y2',color = 'b')

# 和前面的结合一下
ax = ax1
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
# 用另外两个边框绑定x和y轴
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['left'].set_position(('data',-0.0))

# 
ax = ax2
ax.spines['left'].set_color('none')
ax.spines['top'].set_color('none')
# # 用另外两个边框绑定x和y轴
# ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('right')
# ax.spines['bottom'].set_position(('data',-0.0)) # outward, axes(百分比表示)
ax.spines['right'].set_position(('data',10.0))
```

## animation


```python
import numpy as np
from matplotlib import animation

fig,ax = plt.subplots()
x = np.arange(0,2*np.pi,0.01)
line, = ax.plot(x,np.sin(x))

def animate(i):
    line.set_ydata(np.sin(x+i/10))
    return line,
def init():
    line.set_ydata(np.sin(x))
    return line,

ani = animation.FuncAnimation(fig=fig,func=animate,frames=100,init_func=init,interval=20,blit=False)

```


```python

```
