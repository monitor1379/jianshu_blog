# 前言
Keras中提供了一个神经网络可视化的函数plot，并可以将可视化结果保存在本地。plot使用方法如下：

```python
from keras.utils.visualize_util import plot
plot(model, to_file='model.png')
```
> **注**：笔者使用的Keras版本是1.0.6

不过这项功能依赖于graphviz模块与pydot模块，因此需要先安装这两个模块，并安装graphviz软件本身（笔者安装的版本为2.38）。

# 安装步骤

1. 命令行输入 `pip install graphviz`
2. 安装graphviz软件。官网地址为[http://www.graphviz.org/](http://www.graphviz.org/)
  - 解压版：配置环境变量。将安装目录中的`graphviz-2.38\release\bin`添加进Path环境变量
  - 安装版：安装msi
- 命令行输入`pip install pydot==1.1.0`
  - **注**：此处需要指定安装1.1.0版本的pydot，是因为最新版（截止2016.8最新版本号是1.2.x）中find_graphviz函数是deprecated的，使用时会报错

# 测试方法

使用以下脚本

```python



# encoding: utf-8
"""
@author: monitor1379 
@contact: yy4f5da2@hotmail.com
@site: www.monitor1379.com

@version: 1.0
@license: Apache Licence
@file: test_keras2.py
@time: 2016/8/17 16:51
"""

import numpy as np
from keras.models import Sequential
from keras.layers.core import Dense, Activation
from keras.optimizers import SGD
from keras.utils import np_utils    
from keras.utils.visualize_util import plot


def run():
    # 构建神经网络
    model = Sequential()
    model.add(Dense(4, input_dim=2, init='uniform'))
    model.add(Activation('relu'))
    model.add(Dense(2, init='uniform'))
    model.add(Activation('sigmoid'))
    sgd = SGD(lr=0.05, decay=1e-6, momentum=0.9, nesterov=True)
    model.compile(loss='binary_crossentropy', optimizer=sgd, metrics=['accuracy'])

    # 神经网络可视化
    plot(model, to_file='model.png')

if __name__ == '__main__':
    run()

```


输出结果：

![神经网络可视化结果](http://upload-images.jianshu.io/upload_images/1624726-dcbd8bb60454a938.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 参考
[1] [keras可视化pydot graphviz问题](http://www.jianshu.com/p/f3a0b2ca0ca8)
[2] StackOverflow解答：[Importing theano: AttributeError: 'module' object has no attribute 'find_graphviz'](http://stackoverflow.com/questions/38446771/importing-theano-attributeerror-module-object-has-no-attribute-find-graphvi)
