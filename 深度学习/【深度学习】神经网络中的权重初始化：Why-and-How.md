# 前言
神经网络中的权重（weight）初始化是个常常被忽略的问题。

最近在手写一个Python的神经网络库([GitHub：hamaa](https://github.com/monitor1379/hamaa))，刚开始为了测试代码是否写对，搭建了一个`2->4->2`的单隐层神经网络来拟合异或运算，拟合结果十分完美。但是在做MNIST手写数字识别，将网络扩展到了`784->100->10`时，发现损失函数一直不下降，训练准确率一直停留在10%左右（和随机猜的命中概率一样嘛）。一直以为是back propagation的代码写错了，debug了整整两天都没发现错误，结果输出中间weights的梯度dw看看，发现两个权重矩阵的梯度都是在`1e-10`左右的数量级。后来查询了一些资料，原来是代码缺少了**权重初始化（weight initialization）**这**及其重要**的一步。增加了权重初始化后拟合结果终于正常。

在以前看一些关于神经网络的资料时，我也经常看到“权重初始化”这一步，但一直**错误地以为“权重初始化”等价于“权重随机初始化”**，以为仅仅将权重初始化为很小的随机数即可，但其实它的原因除了打破梯度更新对称性之外，还有更深层次的原因。所以接下来文章分为两部分，分别介绍为什么需要进行权重初始化，以及如何进行权重初始化。


# 权重初始化：Why

在创建了神经网络后，通常需要对权重和偏置进行初始化，大部分的实现都是采取Gaussian distribution来生成随机初始值。假设现在输入层有1000个神经元，隐藏层有1个神经元，输入数据x为一个全为1的1000维向量，采取高斯分布来初始化权重矩阵w，偏置b取0。下面的代码计算隐藏层的输入z：
```pytohn
# -*- coding:utf-8 -*-
import numpy as np
import matplotlib.pyplot as plt

def run():
    x = np.ones(1000)
    w = np.random.randn(1000)
    b = 0
    # z为加权和
    z = np.sum(x * w) + b
    print z
```
然而通过上述初始化后，**因为w服从均值为0、方差为1的正太分布，x全为1，b全为0，输入层一共1000个神经元，所以z服从的是一个均值为0、方差为1000的正太分布**。修改代码如下，生成20000万个z并查看其均值、方差以及分布图像：
```python

def  run():
    # z的个数
    t = 20000
    z_lst = np.empty(t)

    x = np.ones(1000)
    b = 0

    for i in xrange(t):
        w = np.random.randn(1000)
        z = np.sum(x * w) + b
        z_lst[i] = z

    print 'z 均值：', np.mean(z_lst)
    print 'z 方差：', np.var(z_lst)
    plt.hist(z_lst, bins=100)
    plt.show()
```
输出结果如下：
```
z 均值： -0.0402106463845
z 方差： 997.082082524
```
输出图像如下：
![z分布（1）](http://upload-images.jianshu.io/upload_images/1624726-08f3059e6108011a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在此情况下，**z有可能是一个远小于-1或者远大于1的数，通过激活函数（比如sigmoid）后所得到的输出会非常接近0或者1，也就是隐藏层神经元处于饱和的状态**。所以当出现这样的情况时，在权重中进行微小的调整仅仅会给隐藏层神经元的激活值带来极其微弱的改变。而这种微弱的改变也会影响网络中剩下的神经元，然后会带来相应的代价函数的改变。结果就是，这些权重在我们进行梯度下降算法时会学习得非常缓慢[[1][1]]。

因此，我们可以通过改变权重w的分布，使|z|尽量接近于0。这就是我们为什么需要进行权重初始化的原因了。


# 权重初始化：How

一种简单的做法是修改w的分布，使得z服从均值为0、方差为1的标准正态分布。根据正太分布期望与方差的特性，将w除以sqrt(1000)即可。修改后代码如下：
```python
def run():
    # z的个数
    t = 20000
    z_lst = np.empty(t)

    # 输入神经元个数
    m = 1000
    x = np.ones(m)
    b = 0

    for i in xrange(t):
        w = np.random.randn(m) / np.sqrt(m)
        z = np.sum(x * w) + b
        z_lst[i] = z

    print 'z 均值：', np.mean(z_lst)
    print 'z 方差：', np.var(z_lst)
    # 保持与z分布（1）图像横坐标刻度不变，使得结果更加直观
    plt.xlim([-150, 150])
    plt.hist(z_lst, bins=10)
    plt.show()
```

输出结果如下：
```
z 均值： 0.013468729222
z 方差： 1.00195898464
```
输出图像如下：

![z分布（2）](http://upload-images.jianshu.io/upload_images/1624726-94931f68fd3da2c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样的话z的分布就是一个比较接近于0的数，使得神经元处于不太饱和的状态，让BP过程能够正常进行下去。

除了这种方式之外（除以前一层神经元的个数n_in的开方），还有许多针对不同激活函数的不同权重初始化方法，比如兼顾了BP过程的除以( (n_in + n_out)/2 )。具体可以参考[Stanford CS231n][2]课程中提到的各种方法。


# 后记

最后强烈安利Stanford CS231n的官方授权中文翻译专栏[知乎专栏：智能单元][3]，感谢各位Zhihuer的辛勤翻译，为后辈的学习与快速上手提供了极大的便利。

# 参考
[1] [www.neuralnetworksanddeeplearning.com，第三章][1]
[2] [Stanford CS231n][2]
[3] [Stanford CS231n官方授权中文翻译，知乎专栏：智能单元 ][3]


[1]:(http://neuralnetworksanddeeplearning.com/chap3.html#weight_initialization)
[2]:(http://cs231n.github.io)
[3]:(https://zhuanlan.zhihu.com/p/21560667?refer=intelligentunit)
