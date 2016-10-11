# 前言
先让我们来看看一个用到相对文件路径的函数调用的问题。假设现在有两个脚本文件`main.py`和`func.py`，他们的路径关系是：
```
.
|--dir1
    |--main.py
    |--dir2
          |--func.py
          |--test.txt
```
`func.py`的作用是提供`load_txt()`函数，读取**同级目录**下`test.txt`文件中的内容并返回。
```python
# func.py

def load_txt()
	filename = './test.txt'
	return open(filenamem, 'r').read()
```
假设现在在`main.py`中调用`load_txt()`函数：
```python
# main.py
from dir2 import func
 
if __name__ == '__main__':
    print func.load_txt()
```

这个时候会报类似`找不到文件test.txt`的错误。

为什么会这样呢？这是因为在函数调用的过程中，当前路径`.`代表的是**被执行的脚本文件**的所在路径。在这个情况中，`.`表示的就是`main.py`的所在路径，所以`load_txt()`函数会在`dir1`文件夹中寻找`test.txt`文件。

那么怎么样才能在函数调用的过程中保持相对路径的不变呢？

# 方法
在网上有相当多的教程都有提到这个Python中相对文件路径的问题，但是大部分都没有提及到在这种情况下的解决办法。

在以下的三个函数中，第一个和第二个是大部分教程中的解决办法，但是这样是错误的，因为第一个和第二个函数所获取的"当前文件路径"都是**被执行的脚本文件**的所在路径，**只有第三个函数返回的当前文件路径才是真正的、该函数所在的脚本文件的所在路径**
```python
def get_cur_path1():
    import os
    return os.path.abspath(os.curdir)

def get_cur_path2():
    import sys
    return sys.argv[0]

def get_cur_path3():
    import os
    return os.path.dirname(__file__)
```

因此，解决办法如下。修改`func.py`中的读取函数如下即可：
```
# func.py

import os

def load_txt()
    module_path = os.path.dirname(__file__)    
    filename = modelu_path + '/test.txt'
    return open(filenamem, 'r').read()
```








