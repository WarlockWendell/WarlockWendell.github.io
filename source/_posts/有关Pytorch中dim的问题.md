---
title: 有关Pytorch中dim的问题
date: 2021-11-21 13:36:49
tags: [PyTorch]
---

PyTorch中dim和numpy中axis问题,初学numpy时,这玩意没少费脑细胞,终于想明白了

<!--more-->

以一个例子来看一下

```python
X = torch.tensor([[[1,2,],[3,4]],[[5,6],[7,8]]])
Y = torch.tensor([[[1,2,],[3,4]],[[5,6],[7,8]]])
torch.cat((X,Y),dim=0)
```

输出是

```bash
tensor([[[1, 2],
         [3, 4]],

        [[5, 6],
         [7, 8]],

        [[1, 2],
         [3, 4]],

        [[5, 6],
         [7, 8]]])
```

这里的X和Y都是三维的张量,事情在张量里好像变得简单了起来

一个张量可以这样表示

$$\left(\begin{array}{1} \left(\begin{array}{1} a_{000}&a_{001}\\a_{010}&a_{011}\end{array}\right) \left(\begin{array}{1} a_{100}&a_{101}\\a_{110}&a_{111} \end{array}\right) \end{array}\right)$$

当`dim=0`时, 自然就是对第一个下标进行操作,这里是拼接，所以就是延伸第0维，也就是上面这个结果

当`dim=1`时,对第二个下标进行操作，所以可以想像结果应该是这里面每个小矩阵的行数变多了，结果也是不出所料的

```bash
tensor([[[1, 2],
         [3, 4],
         [1, 2],
         [3, 4]],

        [[5, 6],
         [7, 8],
         [5, 6],
         [7, 8]]])
```



下面再以求和来验证一下

```python
X.sum(dim=0)
```

按照上述想法，这个求和是对第0维操作，所以应该是里面两个小矩阵对应元素相加，结果呢？

```bash
tensor([[ 6,  8],
        [10, 12]])
```

是对的！

```python
X.sum(dim=2)
```

这个是对dim2操作，所以应该是小矩阵的列相加拼成二维矩阵，二维矩阵的顺寻按照前两维坐标来排序就好

```bash
tensor([[ 3,  7],
        [11, 15]])
```

也是对的 ，好耶



最后再来降维打击一下二维的情况

```python
X = torch.arange(12,dtype=torch.float32).reshape((3,4))
Y = torch.tensor([[2.0,1,4,3],[1,2,3,4],[4,3,2,1]])
torch.cat((X,Y),dim=0,), torch.cat((X,Y),dim=1)
```

第一个dim=0，将dim=0的操作，拼起来，显然应该是按行摞起来，dim=1,应该就是按列连接起来

```bahs
(tensor([[ 0.,  1.,  2.,  3.],
         [ 4.,  5.,  6.,  7.],
         [ 8.,  9., 10., 11.],
         [ 2.,  1.,  4.,  3.],
         [ 1.,  2.,  3.,  4.],
         [ 4.,  3.,  2.,  1.]]),
 tensor([[ 0.,  1.,  2.,  3.,  2.,  1.,  4.,  3.],
         [ 4.,  5.,  6.,  7.,  1.,  2.,  3.,  4.],
         [ 8.,  9., 10., 11.,  4.,  3.,  2.,  1.]]))
```

是对的





