# 前言

> 前言属闲聊，正文请转后。

标题比较长，其实“如何用Python调用C的函数”以及“如何编写Python的C扩展”在广义上是同一件事，因为都是用C写底层实现，用Python作接口。

具体方法有很多，比如用`ctypes`、用`SWIG`等等，各种方法有利有弊。前段时间笔者用`SWIG`编译某小段函数一直出BUG，检查了两天才发现原来是因为`MinGW 32-bit`和`Python 64-bit`不兼容，加之后续笔者还需要用到`NumPy C-API`，所以现在笔者已弃坑SWIG，转用以下方法：

- 按照`Python C-API`的编程规范，用C编写底层实现函数。
- 用Python自带的`disutils`模块来管理编译、打包、生成Python模块。

所以后续的内容将主要围绕以上的方法来展开。

# 正文

### 编写C代码

假设要实现一个数学计算模块`mymath`，包含一个整数加法的函数`add`，那么首先要编写以下代码：

```c
#include "D:\Anaconda2\include\Python.h"

/* file: mymath.c */

// 真正实现
int add(int a, int b)
{
    return a + b;
}

// 包装函数。Python调用add方法时传进来的参数在args里
PyObject* wrap_add(PyObject* self, PyObject* args)
{
    int a, b, result;
    // 解析参数
    if (!PyArg_ParseTuple(args, "ii", &a, &b))
        return NULL;
    result = add(a, b);
    // 返回PyObject* 类型的参数
    return Py_BuildValue("i", result);
}

// mymath模块所包含的函数列表
static PyMethodDef mymathMethods[] =
{
    // 每行一个方法，含义依次为
    // Python方法名，C方法名，参数值，方法文档
    {"add", wrap_add, METH_VARARGS, "doc: add(a, b) \nreturn a + b"},
    {NULL, NULL, 0, NULL}
    // 上面的最后一行相当于结束符
};

// 初始化模块的方法，自动调用
// 命名要求为init后加上模块名
void initmymath()
{
    PyObject* m;
    // 调用Py_InitModule方法初始化模块mymath，其中模块所具有
    // 的函数列表由第二个参数提供
    m = Py_InitModule("mymath", mymathMethods);
}

```

### 编译、打包与生成

在与`mymath.c`文件同级目录下编写`setup.py`文件，代码如下：
```python
# file: setup.py
from distutils.core import setup, Extension

module1 = Extension('mymath',
                    sources = ['mymath.c'])

setup(name = 'mymath',
    version = '1.0',
    description = 'This is a Math package',
    ext_modules= [module1])


```


然后打开命令行窗口，进入到mymath.c所在目录，输入以下命令：
> `python setup.py build`

可以看到以下输出信息：

![编译、打包、生成时的输出信息.png](http://upload-images.jianshu.io/upload_images/1624726-015463c9f18063b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这个时候可以看到当前目录多了个build文件夹，一路进去可以看到`mymath.pyd`文件，这就是直接可调用的Python module了。继续在命令行里进入Python，可以直接调用mymath模块了


![调用效果](http://upload-images.jianshu.io/upload_images/1624726-741539c9368725ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



# 参考资料

[1] Python/C API Reference Manual, [https://docs.python.org/2/c-api/](https://docs.python.org/2/c-api/)
[2] 《Python基础教程》（第2版）
