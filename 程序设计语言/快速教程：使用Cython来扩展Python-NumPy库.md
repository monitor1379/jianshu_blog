# 前言

整个快速教程直接上例子，具体对Cython的使用可以看参考文章。以下工作均在`Windows 10` + `Python 2.7` + `NumPy 1.11.0` + `Cython 0.24` 版本上进行。

# 正文

### 准备工作

假设现在我们用C实现了一个可以用在数组上的`cos`函数，函数原型如下：
```
// 对in_array中的前size个数求cos值，并存放在out_array对应位置上
void cos_doubles(double * in_array, double * out_array, int size);
```

那么总共需要4个文件：
  - `cos_doubles.c`，C源文件。
  - `cos_doubles.h`，C头文件。
  - `_cos_doubles.pyx`，Python的C扩展文件。（注意：之所以前面加个"_"下划线，是因为使用Cython编译打包后会对pyx文件生成同名的c文件，为了避免覆盖掉原来的`cos_doubles.c`文件，此处加个下划线）
  - `setup.py`，负责管理编译、打包工作的“配置”脚本。

下面给出4个文件的源代码


### cos_doubles.c

```c

#include "cos_doubles.h"
#include <math.h>

/*  Compute the cosine of each element in in_array, storing the result in
 *  out_array. */
void cos_doubles(double * in_array, double * out_array, int size){
    int i;
    for(i=0;i<size;i++){
        out_array[i] = cos(in_array[i]);
    }
}
```


### cos_doubles.h
```c
#ifndef _COS_DOUBLES_H
#define _COS_DOUBLES_H
void cos_doubles(double * in_array, double * out_array, int size);
#endif
```


### _cos_doubles.pyx

```pyx
""" Example of wrapping a C function that takes C double arrays as input using
    the Numpy declarations from Cython """

# import both numpy and the Cython declarations for numpy
import numpy as np
cimport numpy as np

# if you want to use the Numpy-C-API from Cython
# (not strictly necessary for this example)
np.import_array()

# cdefine the signature of our c function
cdef extern from "cos_doubles.h":
    void cos_doubles (double * in_array, double * out_array, int size)

# create the wrapper code, with numpy type annotations
def cos_doubles_func(np.ndarray[double, ndim=1, mode="c"] in_array not None,
                     np.ndarray[double, ndim=1, mode="c"] out_array not None):
    cos_doubles(<double*> np.PyArray_DATA(in_array),
                <double*> np.PyArray_DATA(out_array),
                in_array.shape[0])
```


### setup.py

```python
from distutils.core import setup, Extension
import numpy
from Cython.Distutils import build_ext

setup(
    cmdclass={'build_ext': build_ext},
    ext_modules=[Extension("cos_doubles",
                 sources=["_cos_doubles.pyx", "cos_doubles.c"],
                 include_dirs=[numpy.get_include()])],
)
```

### 编译打包

在命令行窗口中进入到上述文件所在同级目录，输入:
```
>> python setup.py build_ext -i
```
参数`-i`表示`inplace`，即在同级目录下生成Python可调用模块pyd文件。

build过程如下：

![build过程](http://upload-images.jianshu.io/upload_images/1624726-2a61a2d1b100727b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


然后可以看见在同级目录下多了两个文件：
  - `_cos_doubles.c`，使用Python C-API自动包装生成的C文件。
  - `cos_doubles.pyx`，Python可直接调用的module文件，也就是最终我们所需要的东西。

接下来测试一下：

```
# file: test.py
import cos_doubles
import numpy as np
import matplotlib.pyplot as plt

a = np.linspace(-5, 5, 100)
b = np.empty_like(a)
cos_doubles.cos_doubles_func(a, b)

plt.plot(b)
plt.show()
```

运行效果如下图所示：

![运行效果](http://upload-images.jianshu.io/upload_images/1624726-34c4dd2a3f6b1177.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 参考资料


[1] [SciPy lecture notes: 2.8. Interfacing with C](http://www.scipy-lectures.org/advanced/interfacing_with_c/interfacing_with_c.html)
[2] [Working with NumPy](http://docs.cython.org/en/latest/src/tutorial/numpy.html)
[3] [Python中使用C代码：以NumPy为例 ](https://segmentfault.com/a/1190000000479951?utm_source=tuicool&utm_medium=referral#articleHeader0)
[4] [Cython学习](http://www.cnblogs.com/kaituorensheng/p/4452881.html)
