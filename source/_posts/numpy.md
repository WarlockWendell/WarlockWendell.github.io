---
title: numpy & pandas
date: 2021-11-20
tags: [python]
---

莫烦大佬的numpy教程笔记

<!--more-->

# 1. numpy

- 用C语言写的，很快

## 1.1  安装

- 用anaconda安装就完事了

## 1.2 Numpy基本属性

```python
import numpy as np
array = np.array([[1,2,3],
                 [4,5,6]]) # 3x2的矩阵
print(array)
print(array.ndim)
print(array.shape)
print(array.size)
```

## 1.3 创建Array

```python
import numpy as np
a = np.array([1,2,3]) # 结果没有逗号
a = np.array([1,2,3]，dtype=np.int) # dtype类型 uint8 float32 ...
print(a.dtype)

#  矩阵
a = np.array([1,2,3],
             [4,5,6])
print(a)

# 全0
a = np.zeros((3,4)) # 3行4列的全0

# 全1
a = np.ones((3,4))

# 没有
a = np.empty((3,4)) # 其实是一个非常接近0的数

# 序列
a = np.arange(10,20,2) # 10 12 14 16 18
a = np.arange(12).reshape((3,4)) 
'''
[[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]
'''
a = np.linspace(1,10,20).reshape((4,5)) # 1-10 20个值
'''
[[ 1.          1.47368421  1.94736842  2.42105263  2.89473684]
 [ 3.36842105  3.84210526  4.31578947  4.78947368  5.26315789]
 [ 5.73684211  6.21052632  6.68421053  7.15789474  7.63157895]
 [ 8.10526316  8.57894737  9.05263158  9.52631579 10.        ]]
'''
```

## 1.4 基本运算

```python
import numpy as np
a = np.array([1,2,3,4])
b = np.arange(4)
c = a + b
c = a - b

c = a ** 2 # 每位的平方
c = 10*np.sin(a)

print(b<3) #显示一个bool阵

# 矩阵
a = np.array([11,12],
             [13,14])
b = np.arange(4).reshape(2,2)
c = a*b #逐个相乘
#矩阵相乘
c = np.dot(a,b) #矩阵相乘
c = a.dot(b) 

# 随机生成
a = np.random.random((2,4))
np.sum(a) #整个array
np.max(a) #整个array

np.sum(a，axis = 1) #行中
np.max(a, axis = 0) #列中
```

```python
import numpy as np
A = np.arange(2,14).reshape((3,4))
'''
[[ 2  3  4  5]
 [ 6  7  8  9]
 [10 11 12 13]]
'''
np.argmin(A) #最小值的索引 0
np.argmax(A) #最大值的索引 11
np.mean(A)  # 和A.mean()一样
np.median(A) #中位数
np.cumsum(A) #逐步加进去
'''
array([ 2,  5,  9, 14, 20, 27, 35, 44, 54, 65, 77, 90], dtype=int32)
'''
np.diff(A) #累差
'''
array([[1, 1, 1],
       [1, 1, 1],
       [1, 1, 1]])
'''
np.nonzero(A) #非零的数  输出两个array，一个是行的坐标，另一个是列的坐标
np.sort(A) # 排序
np.transpose(A) # 或者 A.T

np.clip(A,5,9) # 小于5的等于5，大于9的等于9
```

## 1.5 numpy索引

```python
import numpy as np
A = np.arange(3,15).reashpe((3,4))
'''
[[ 3  4  5  6]
 [ 7  8  9 10]
 [11 12 13 14]]
'''
A[2]  # [11 12 13 14]
A[1][1] # 8
A[2,1] # 12
A[2,:] # [11 12 13 14]
A[:,1] # array([ 4,  8, 12])

for row in A:
    print(row)
for column in A.T:
    print(column)
# A.flatten() # array([ 3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])
for item in A.flat:
    print(item)
```

## 1.6 numpy array合并

```python
import numpy as np
A = np.array([1,1,1])
B = np.array([2,2,2])

C = np.vstack((A,B)) # vertical stack
D = np.hstack((A,B)) # horizontal stack

# transpose不能把一个序列变成一个矩阵(行向量变列向量)
A[np.newaxis,:] # 行上面加了一个维度
A[:,np.newaxis] # 列上面加了一个维度，向量的转置
A = np.array([1,1,1])[:,np.newaxis] #纵向的
B = np.array([2,2,2])[:,np.newaxis] #列向量

C = np.concatenate((A,B,B,A),axis=0) # 上下合并
C = np.concatenate((A,B,B,A),axis=1) # 横向合并
```

## 1.7 array分割

```python
import numpy as np
A = np.arange(12).reshape((3,4))
np.split(A,2,axis=1) # 竖着切，切成两块

# 不等分割
np.array_split(A,3,axis=1)

np.vsplit(A,3)
np.hsplit(A,2)
```

## 1.8 deep copy

```python
import numpy as np
a = np.arange(4)
b = a 
b is a # True  改变a同时也改变b，改变b同时也改变a

# 不关联起来
b = a.copy() # deep copy
b is a # False
```



# 2. Pandas

## 2.1 简介

有点像字典

```python
import numpy as np
import pandas as pd
s = pd.Series([1,2,6,np.nan,44,1])
print(s)
'''
0     1.0
1     2.0
2     6.0
3     NaN
4    44.0
5     1.0
dtype: float64
'''
dates = pd.date_range('20200802',periods=6)
'''
DatetimeIndex(['2020-08-02', '2020-08-03', '2020-08-04', '2020-08-05',
               '2020-08-06', '2020-08-07'],
              dtype='datetime64[ns]', freq='D')
'''
df = pd.DataFrame(np.random.randn(6,4),index=dates,columns=['a','b','c','d'])
'''
                   a         b         c         d
2020-08-02 -0.267901  0.809192  1.585409 -0.800896
2020-08-03 -0.974380  0.236844  0.889068  0.456217
2020-08-04 -1.296105  0.384586 -0.140811  2.476563
2020-08-05 -1.316836 -1.397234 -0.852017 -1.697567
2020-08-06 -0.341858  0.101268  0.008662  0.399342
2020-08-07  0.854321 -0.969657  0.318324 -1.289780
'''

df = pd.DataFrame(np.arange(12).reshape((3,4)))
'''
   0  1   2   3
0  0  1   2   3
1  4  5   6   7
2  8  9  10  11
'''
df = pd.DataFrame({'A':1.,
                  'B':pd.Timestamp('20120102'),
                  'C':pd.Series(1,index=list(range(4)),dtype='float32'),
                  'D':np.array([3]*4,dtype='int32'),
                  'E':pd.Categorical(['test','train','test','trasin']),
                  'F':'foo'})
'''
     A          B    C  D       E    F
0  1.0 2012-01-02  1.0  3    test  foo
1  1.0 2012-01-02  1.0  3   train  foo
2  1.0 2012-01-02  1.0  3    test  foo
3  1.0 2012-01-02  1.0  3  trasin  foo
'''
df.dtypes
df.index
df.columns
df.values
df.describe() #数据形式的
'''
         A    C    D
count  4.0  4.0  4.0
mean   1.0  4.0  3.0
std    0.0  0.0  0.0
min    1.0  4.0  3.0
25%    1.0  4.0  3.0
50%    1.0  4.0  3.0
75%    1.0  4.0  3.0
max    1.0  4.0  3.0
'''
df.T
df.sort_index(axis=1,ascending=False)
'''
     F       E  D    C          B    A
0  foo    test  3  4.0 2012-01-02  1.0
1  foo   train  3  4.0 2012-01-02  1.0
2  foo    test  3  4.0 2012-01-02  1.0
3  foo  trasin  3  4.0 2012-01-02  1.0
'''
df.sort_values(by='E')
'''
     A          B    C  D       E    F
0  1.0 2012-01-02  4.0  3    test  foo
2  1.0 2012-01-02  4.0  3    test  foo
1  1.0 2012-01-02  4.0  3   train  foo
3  1.0 2012-01-02  4.0  3  trasin  foo
'''
```

## 2.2 选择数据

```python
import numpy as np
import pandas as pd
dates=pd.date_range('20130101',periods=6)
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])
'''
             A   B   C   D
2013-01-01   0   1   2   3
2013-01-02   4   5   6   7
2013-01-03   8   9  10  11
2013-01-04  12  13  14  15
2013-01-05  16  17  18  19
2013-01-06  20  21  22  23
'''
# simple select
df.A
df['A']
df[0:3] # 0-3行
df['20130101':'20130104']

# select by label: loc
df.loc['20130102']
df.loc[:,['A','B']]
df.loc['20130102',['A','B']]

# select by postion: iloc
df.iloc[3] #第4行
df.iloc[3:5,1:3]
df.iloc[[1,3,5],1:3]

# mixed selection: ix 这个已经被弃用了，不用记了
df.ix[:3,['A','C']]
df.ix['20130101',[2:4]]
# boolean indexing
df.A > 8
'''
2013-01-01    False
2013-01-02    False
2013-01-03    False
2013-01-04     True
2013-01-05     True
2013-01-06     True
Freq: D, Name: A, dtype: bool
'''
df[df.A>8]
```

## 2.3 赋值

```python
import numpy as np
import pandas as pd
dates=pd.date_range('20130101',periods=6)
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])

df.iloc[2,2] = 1111
df.loc['20130101','B'] = 2222
df[df.A>4] = 0
df.A[df.A>4] = 0
df.B[df.A>4] = 0
df['F'] = np.nan # 加一行
df['E'] = pd.Series([1,2,3,4,5,6],index=dates)
```

## 2.4 处理NaN

```python
import numpy as np
import pandas as pd
dates=pd.date_range('20130101',periods=6)
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])
df.iloc[0,1] = np.nan
df.iloc[1,2] = np.nan
'''
             A     B     C   D
2013-01-01   0   NaN   2.0   3
2013-01-02   4   5.0   NaN   7
2013-01-03   8   9.0  10.0  11
2013-01-04  12  13.0  14.0  15
2013-01-05  16  17.0  18.0  19
2013-01-06  20  21.0  22.0  23
'''
df.dropna(axis=0,how='any') # 按行丢掉 how={'any','all'}
'''
             A     B     C   D
2013-01-03   8   9.0  10.0  11
2013-01-04  12  13.0  14.0  15
2013-01-05  16  17.0  18.0  19
2013-01-06  20  21.0  22.0  23
'''
df.dropna(axis=1,how='any')
'''
             A   D
2013-01-01   0   3
2013-01-02   4   7
2013-01-03   8  11
2013-01-04  12  15
2013-01-05  16  19
2013-01-06  20  23
'''
df.fillna(value=0)
'''
             A     B     C   D
2013-01-01   0   0.0   2.0   3
2013-01-02   4   5.0   0.0   7
2013-01-03   8   9.0  10.0  11
2013-01-04  12  13.0  14.0  15
2013-01-05  16  17.0  18.0  19
2013-01-06  20  21.0  22.0  23
'''

# check
df.isnull() #返回nan
np.any(df.isnull())
```

## 2.5 导入导出

<img src="C:\Users\wendell\AppData\Roaming\Typora\typora-user-images\image-20210802120524357.png" alt="image-20210802120524357" style="zoom: 33%;" />![image-20210802120647273](C:\Users\wendell\AppData\Roaming\Typora\typora-user-images\image-20210802120647273.png)

<img src="C:\Users\wendell\AppData\Roaming\Typora\typora-user-images\image-20210802120647273.png" alt="image-20210802120647273" style="zoom:33%;" />

```python
import pandas as pd
a = pd.read_csv('student,csv')
a.to_pickle('student.pickle')
```

## 2.6 contact合并

```python
import numpy as np
import pandas as pd
# concatenating
df1=pd.DataFrame(np.ones((3,4))*0,columns=['a','b','c','d'])
df2=pd.DataFrame(np.ones((3,4))*1,columns=['a','b','c','d'])
df3=pd.DataFrame(np.ones((3,4))*2,columns=['a','b','c','d'])

# 纵向的合并axis=0
res = pd.concat([df1,df2,df3],axis=0) #索引还是有重复的
res = pd.concat([df1,df2,df3],axis=0,ignore_index=True) #索引忽略

# join, ['inner','outer']
df1=pd.DataFrame(np.ones((3,4))*0,columns=['a','b','c','d'],index=[1,2,3])
df2=pd.DataFrame(np.ones((3,4))*1,columns=['b','c','d','e'],index=[2,3,4])
pd.concat([df1,df2],axis=0) #直接合并 默认是outer
'''
     a    b    c    d    e
1  0.0  0.0  0.0  0.0  NaN
2  0.0  0.0  0.0  0.0  NaN
3  0.0  0.0  0.0  0.0  NaN
2  NaN  1.0  1.0  1.0  1.0
3  NaN  1.0  1.0  1.0  1.0
4  NaN  1.0  1.0  1.0  1.0
'''
res = pd.concat([df1,df2],axis=0,join='inner',ignore_index=True)
'''
     b    c    d
0  0.0  0.0  0.0
1  0.0  0.0  0.0
2  0.0  0.0  0.0
3  1.0  1.0  1.0
4  1.0  1.0  1.0
5  1.0  1.0  1.0
'''
res = pd.concat([df1,df2],axis=1,join_axes=[df1.index]) #弃用 了已经 按照df1的合并

s1 = pd.Series([1,2,3,4],index=['a','b','c','d'])
res = df1.append(df2,ignore_index=True)
res = df1.append([df2,df3],ignore_index=True)
res = df1.append(s1,ignore_index=True) #加一行
```

## 2.7 Merge合并

```python
import numpy as np
import pandas as pd
left = pd.DataFrame({'key':['K0','K1','K2','K3'],
                    'A':['A0','A1','A2','A3'],
                    'B':['B0','B1','B2','B3']})
right = pd.DataFrame({'key':['K0','K1','K2','K3'],
                    'C':['C0','C1','C2','C3'],
                    'D':['D0','D1','D2','D3']})
'''
  key   A   B
0  K0  A0  B0
1  K1  A1  B1
2  K2  A2  B2
3  K3  A3  B3
'''
res = pd.merge(left,right,on='key') # 按label合并
'''
  key   A   B   C   D
0  K0  A0  B0  C0  D0
1  K1  A1  B1  C1  D1
2  K2  A2  B2  C2  D2
3  K3  A3  B3  C3  D3
'''

# 两个key都要相同才会合并
left = pd.DataFrame({'key1':['K0','K0','K1','K2'],
                     'key2':['K0','K1','K0','K1'],
                    'A':['A0','A1','A2','A3'],
                    'B':['B0','B1','B2','B3']})
right = pd.DataFrame({'key1':['K0','K1','K1','K2'],
                      'key2':['K0','K0','K0','K0'],
                    'C':['C0','C1','C2','C3'],
                    'D':['D0','D1','D2','D3']})
res = pd.merge(left,right,on=['key1','key2'])
'''
  key1 key2   A   B   C   D
0   K0   K0  A0  B0  C0  D0
1   K1   K0  A2  B2  C1  D1
2   K1   K0  A2  B2  C2  D2
'''
# how = ['inner','outer','left','right'] #inner默认的，交集，outer 并集，补nan，left基于left的key
res = pd.merge(left,right,on=['key1','key2'])

res = pd.merge(left,right,on=['key1','key2'],how='outer',indicator=True) #给一个直观的显示，显示这一行的key在哪有
res = pd.merge(left,right,on=['key1','key2'],how='outer',indicator='indicator_columns') #改个indicator的名字

# 按index合并
res  = pd.merge(left,right,left_index=True,right=True,how='outer')

# 两个表有相同的项， 比如男生女生表里的age，都叫age，区分一下
res = pd.merge(boys,girls,on='k',suffixes=['_boys','_girls'],how='inner')
```

> join和merge很像

## 2.8 画图

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# plot data
data = pd.Series(np.random.randn(1000),index=np.arange(1000))
data = data.cumsum()
data.plot()
plt.show()

# DataFrame
data = pd.DataFrame(np.random.randn(1000,4),index = np.arange(1000),columns=list("ABCD"))
data = data.cumsum()
data.plot()
plt.show()
#plot methods
# bar hist box kde area scatter hexbin pie ...

# scatter
ax = data.plot.scatter(x= 'A', y = 'B',color='DarkBlue',label='Class 1')
data.plot.scatter(x= 'A', y = 'C',color='DarkGreen',label='Class 2',ax = ax)
plt.show()
```

