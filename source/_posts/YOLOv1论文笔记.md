---
title: YOLOv1论文笔记
date: 2021-11-17 23:24:32
tags: [目标检测,论文笔记]
---

You Only Look Once: Unified, Real-Time Object Detection

<!--more-->

# 1 关键词

- 实时目标检测

# 2 想解决的问题

- 提升目标检测的速度同时不要丢失太多的精度

# 3 解决的思路

- 一个端到端的神经网络，把图片喂进去通过网络黑盒子得到预测结果，这样能让速度很快
- 将问题变成一个回归问题
- 训练过程中: 将图片分割成七七四十九个grid cells, 每个grid cell预测出来两个 bounding boxes，数据集中的框的中心点会落在一个grid cell中，由这个grid cell中与ground truth IOU大的bbox负责拟合这个ground truth
- 测试过程中: 训练好的模型，会预测出98个bounding boxes，每个box都有包含物体的置信度，以及每个grid cell都会有一个P(class_i | object)的条件概率，即当有物体时这个物体是某个类别的概率，两个概率相乘就是最终的置信度，用这个做阈值筛选以及非极大值抑制就可以得到结果。

# 4 思考

- 怎么理解confidence? 要从损失函数中看：对于不负责预测物体的bbox，希望C是靠近0的，此时数据集的label自然也标注成0，对于负责预测物体的bbox，C应该靠近预测出来的框和ground truth的IOU

  > 这里靠近的是IOU 理性上是可以理解的，因为当我的框和真实差距太多时，它的C确实不应该太大，所以用IOU来做label貌似还不错，但是这样损失函数就一直在变化了，这样好吗？
  >
  > 想想好像也没有更好的办法了，如果直接用1来代表有物体，那么就无法来做推理过程中的根据置信度来评分的操作了
  >
  > 但是在一些博客里看到的解释是直接置成1了，不是很理解，论文里好像也没有对这个做出解释

- 两个bbox公用条件概率这好嘛

- 训练基于224x224图像的，然后直接double了？这样是咋做的？好吗？

  > 是在ImageNet上先训练的前20层，训练了一周达到88%的top 5准确度，此时的输入是224×224，然后加上了四个卷积层和两个全连接层，此时需要细粒度，将输入变成了448x448，此时

- 注意：训练过程和预测过程要分开理解，训练时是有ground truth标签的，但是预测时没有答案，只是根据预测出来的框框以及置信度这些数字以及一些后处理方法(NMS等)来实现，从这个角度来理解损失函数的定义会好一点。

- 训练时的ground truth中心点在的grid cell要负责拟合，所以当然也希望那个bbox的中心落在这个grid cell中，使用的（x,y）坐标是根据相对grid cell左上角坐标来算的，并且对grid cell的宽高做了归一化，所以训练时希望这些bbox中心坐标在0到1中间，但是其实框架是没有对坐标进行0到1的限制的，所以预测的时候，其坐标可能不在grid cell中，这也是可能的。（但是训练之后磨练了那么久（努力让他落在中心点），应该概率不大？）

  > **从来没说过grid cell生成的bbox中心在grid cell中哦，只是在训练时 ground truth 所在grid cell的bbox负责拟合罢了，也没说一定要在中心，当然不在中心其损失惩罚很大就是了**

# 5 贡献（优势）

- **将目标检测问题归结为一个回归问题****
- 快、比较准、将背景识别为物体（FP）的比例较小（相比与Fast RCNN）
- 可以迁移到其他领域
- 易于训练
- 分析了误差类型，说明了模型ensemble 要优势互补，有不同的特征

# 6 不足

- 小目标 多目标
- 定位误差比较大
- 输入图像尺度有限制（448x448)

# 7 For me

- 第一篇文献，一些基本术语
- 这个想法其实是最直观的，但是之前没人去做，工程实现和idea之间还是有很大gap的

# 8 References

- [13] 解释 R-CNN rescore the boxes based on other objects in the scene 
- [18] co-adaptation
- [20] 为啥加卷积层和全连接层在预训练模型上会变好？

# 9 Other works

