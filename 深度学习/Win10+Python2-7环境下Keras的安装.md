网上有很多关于Keras的介绍，该库主要是对Theano与TensorFlow进行的模块化封装，让代码变得更加简洁易用，同时也保持原来的特性（比如支持GPU计算）。下面主要讲一下如何在Window 10与Python2.7下安装Keras。

## Anaconda安装

由于安装Keras需要使用到许多科学计算库，因此为了避免挖坑，尽量安装Python的发行版：[Anaconda](https://www.continuum.io/downloads#_windows)。

在官网选择Anaconda for Windows 64-bit + Python2.7后能够得到Python2.7、Spyder IDE、IPython notebook以及NumPy、SciPy、Matplotlib等库，使用起来非常方便。

![Anaconda for Window 64-Bit](http://upload-images.jianshu.io/upload_images/1624726-cafc6fd32dc83354.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 安装Keras

安装Keras非常简单，在cmd中输入`pip install keras`，可以让pip自动解决库之间的依赖关系。

## 安装MinGW

###### 下载
在cmd中输入`conda install mingw libpython`，遇到需要输入`(y/n)`时输入`y`并点击回车即可。

###### 配置环境变量
安装完后在环境变量Path中新增两行值：
- `E:/Anaconda/MinGW/bin; `
- `E:/Anaconda/MinGW/x86_64-w64-mingw32/lib;`


## 安装Theano
由于Keras依赖于Theano库，因此需要手动下载Theano库的压缩包。

###### 下载
在GitHub上可下载到Theano的[源码](https://github.com/Theano/Theano)。点击`Clone or download`按钮--> 点击`Download ZIP`即可开始下载。

![下载Theano源码](http://upload-images.jianshu.io/upload_images/1624726-438caf3dd0cf3929.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###### 删除Theano
由于安装Keras后默认会得到一个Theano库，而我们需要使用下载的Theano，所以先把原版的Theano库给删掉。在`D:\Anaconda2\Lib\site-packages`里删`theano`文件夹即可。

###### 复制
下载后得到的是一个工程项目Theano-master，在`Theano-master`文件下可以看到一个`theano`文件夹，复制到Anaconda安装目录下的`D:\Anaconda2\Lib\site-packages`中即可。

###### 配置环境变量
在环境变量中添加一个系统变量，变量名为`PYTHONPATH`， 值为`D:\Anaconda2\Lib\site-packages\theano`。

## 检验
理论上来说此时Keras与Theano已经配置完毕，为了准确起见，重启一下电脑，在命令行进入Python，输入`import theano`，如果没有出现一堆东西就表示安装正确，此时再`import keras`一般是没有问题了。
