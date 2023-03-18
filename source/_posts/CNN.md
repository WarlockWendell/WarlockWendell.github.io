---
title: CNN基础
date: 2021-12-09
tags: [深度学习]
---

记录一下学习到的卷积神经网络，做个系统的输出，内容来自沐神的教材和课程。

<!--more-->

# 1.LeNet

- 1998年的神经网络，是第一个取得了成功的卷积神经网络。

- 神经网络的结构

  ![image-20211209213826083](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211209213826083.png)

  - 两个卷积层，两个池化层，3个全连接层

  - 使用的是Sigmoid函数

  - ![image-20211209214048552](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211209214048552.png)

    > 这种风格的图片都是来自沐神的动手学习深度学习

- 效果：还可以，过拟合不是特别严重，但是精度只能说是一般般吧，好像比MLP稍微好一点

  > 效果是基于FashionMNIST这个数据集来看的，这个数据集比较小

# 2. AlexNet

- 这一次深度学习浪潮的开启者

- 卷积层可以认为是可以学习的特征提取器，它比手动设计卷积核要高效一些，但是它学到的东西也未必是人类可以理解的。不同通道提取到的特征是不一样的，这样就可以认为不同通道提取出了不同的特征，然后这些卷积核学出来最适用于这个题目的特征。

  然后再使用全连接层将这些特征进行分类，效果很好呀。

- 相比于LeNet的改进

  - 使用了更加深的网络结构
  - 使用了ReLU激活函数代替了Sigmoid函数
  - 使用了Dropout的训练技巧来缓解过拟合

- 能做这些改进的原因：

  - 有了更大的数据集ImageNet
  - 有了更强的硬件GPU（GPU在机器学习还是很常见的，但是在LeNet的那个年代还没这么火）

- 网络的结构

  ![image-20211209222827523](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211209222827523.png)

  因为图片大了，使用更大的卷积核也是可以理解的。加了三个卷积层，这个属于没道理了。

  后面连了两个很强大的4096全连接层，这个具有相当强的分类能力，去掉一层的话效果就没那么好了，说明前面的卷积层提取到的语义信息还不是很强，还需要更强的全连接层来做。

- 效果：还可以，也没有很明显的过拟合现象，准确率相较于LeNet还是有很大的提升的

  > 基于FashionMNIST数据集

# 3. VGG

- 引入了块的结构，方便更快构建出不同深度的网络。

- 网络的结构

  ![image-20211209224659152](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211209224659152.png)

  相较于AlexNet，就是将前面那些不是很好设计的卷积层做成了块状

  一个VGG块由n个卷积层（卷积，ReLU）加上一个最大池化层组成

  然后一个网络由若干个VGG块 加上AlexNet后面相同的卷积层组成

- 结果：效果比AlexNet要好，也没有出现明显的过拟合现象

  > 基于FashionMNIST数据集

# 4. NiN

- Network in Network

- 整个网络没有使用全连接层，其网络参数的大小会比使用大全连接层的网络要小一些

- 网络结构

  ![image-20211209230310614](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211209230310614.png)

  可以看到，NIN块就是一个卷积层，加两个1x1的卷积层（1x1的卷积层可以相当于一个对通道做的全连接层）

  最后的一个NIN块的输出通道数和类别数是相同的，再通过一个全局平均池化来做分类就完事了，这个方法再YOLOv2里也用了这个思想的

  这种网络可能训练起来比较难

- 效果：没有明显的过拟合，但是其效果也很AlexNer的水平好像

  > 基于FashionMNIST数据集

# 5. GoogLeNet

- 采用了并行块的结构Inception

  ![image-20211209232840797](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211209232840797.png)

  有人说小卷积核好，有人用大卷积核，有人用小卷积核，那到底哪个卷积核好用呢，Google表示我全都要，使用并行块将这些都用起来就好了。

- 网络的结构

  ![image-20211209234309888](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211209234309888.png)

  这个结构就像是在炼丹了，确实网络结构很神奇，结合了各种卷积核的特征，其特征提取的能力更强了，所以后面的卷积层也变少了一点

- 效果：还可以，没有明显的过拟合，但是其效果貌似不是VGG好啊？

  > 基于FashionMNIST数据集

# 6. Batch Normalization

- 这不是一个新的网络结构，主要是解决训练过程中的一些问题

- 能够加速收敛，但是对精确度没有很明显的影响

- 原理：

  - 全连接层：对特征维度做BN
  - 卷积层：对通道维做BN

  > 个人理解就是将特征做了归一化呀，这样好像还可以的
  >
  > 你肯定不能对不同特征对归一化啊，这样可以通过调权重来实现的
  >
  > 通道可以理解为特征模式嘛，所以就是对通道来归一化呀，合理的

# 7. ResNet

- 算是很颠覆的一项了

- 加入了残差连接，具有拟合恒等映射的能力，这种网络结构可以有效地避免网络退化的问题

- Res Block

  ![image-20211210001700255](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211210001700255.png)

  当网络通道变化时就需要使用一个1x1卷积来讲x reshape一下了，不然加不起来呀

  ![image-20211210002332908](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211210002332908.png)

- ResNet结构

  ![image-20211210002407692](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211210002407692.png)

  ResNet-18 的结构，可见网络还是很清晰的，而且可以做到很深很深的。

- 结果：网络效果也还行，但是好像比不上VGG呀，而且过拟合现象比较严重呀

  > 基于FashionMNIST数据集

# 8. DenseNet

- 使用了ResNet的思路呀

  将ResNet的加法变成了拼接

  ![image-20211210002915432](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211210002915432.png)

- Dense Block 用来做Dense拼接，这样做出来的输出的通道很长呀

  ![image-20211210002924566](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211210002924566.png)

- transition block 通道太多了不太好训练，网络也太多了，所以使用这个来降低维度

- 效果：感觉和ResNet差不多的

  > 基于FashionMNIST数据集



