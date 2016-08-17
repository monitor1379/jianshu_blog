# 前言

前段时间使用Python解析IDX文件格式的MNIST数据集，需要对二进制文件进行读取操作，其中我使用的是struct模块。查了网上挺多教程都写的挺好的，不过对新手不是很友好，所以我重新整理了一些笔记以供快速上手。

> **注**：教程中以下四个名词同义：二进制流、二进制数组、字节流、字节数组

# 快速上手

在struct模块中，将一个整型数字、浮点型数字或字符流（字符数组）转换为字节流（字节数组）时，需要使用格式化字符串fmt告诉struct模块被转换的对象是什么类型，比如整型数字是'i'，浮点型数字是'f'，一个ascii码字符是's'。

```python

def demo1():
    # 使用bin_buf = struct.pack(fmt, buf)将buf为二进制数组bin_buf
    # 使用buf = struct.unpack(fmt, bin_buf)将bin_buf二进制数组反转换回buf

    # 整型数 -> 二进制流
    buf1 = 256
    bin_buf1 = struct.pack('i', buf1) # 'i'代表'integer'
    ret1 = struct.unpack('i', bin_buf1)
    print bin_buf1, '  <====>  ', ret1

    # 浮点数 -> 二进制流
    buf2 = 3.1415
    bin_buf2 = struct.pack('d', buf2) # 'd'代表'double'
    ret2 = struct.unpack('d', bin_buf2)
    print bin_buf2, '  <====>  ', ret2

    # 字符串 -> 二进制流
    buf3 = 'Hello World'
    bin_buf3 = struct.pack('11s', buf3) # '11s'代表长度为11的'string'字符数组
    ret3 = struct.unpack('11s', bin_buf3)
    print bin_buf3, '  <====>  ', ret3

    # 结构体 -> 二进制流
    # 假设有一个结构体
    # struct header {
    #   int buf1;
    #   double buf2;
    #   char buf3[11];
    # }
    bin_buf_all = struct.pack('id11s', buf1, buf2, buf3)
    ret_all = struct.unpack('id11s', bin_buf_all)
    print bin_buf_all, '  <====>  ', ret_all

```
输出结果如下：
![demo1输出结果](http://upload-images.jianshu.io/upload_images/1624726-809dd03571fb5198.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




# 详解struct模块

## 主要函数
struct模块中最重要的三个函数是pack(), unpack(), calcsize()

```python
# 按照给定的格式化字符串，把数据封装成字符串(实际上是类似于c结构体的字节流)
string = struct.pack(fmt, v1, v2, ...)

# 按照给定的格式(fmt)解析字节流string，返回解析出来的tuple
tuple = unpack(fmt, string)

# 计算给定的格式(fmt)占用多少字节的内存
offset = calcsize(fmt) 
```

# struct中的格式化字符串

struct中支持的格式如下表：

|Format	|C Type	|Python	|字节数|
|--------|--------|--------|-----|
|x|pad byte|no value	|1|
|c|char|string of length 1|1|
|b|signed char|integer|1|
|B|unsigned char|integer|1|
|?|_Bool|bool|1|
|h|short|integer|2|
|H|unsigned short|integer	|2|
|i|int|integer|4|
|I|unsigned int|integer or lon	|4|
|l|long|integer|4|
|L|unsigned long|long|4|
|q|long long|long|8|
|Q|unsigned long long|long|8|
|f|float|float|4|
|d|double|float|8|
|s|char[]|string|1|
|p|char[]|string|1|
|P|void *|long |　|

> **注1**：q和Q只在机器支持64位操作时有意思
> **注2**：每个格式前可以有一个数字，表示个数
> **注3**：s格式表示一定长度的字符串，4s表示长度为4的字符串，但是p表示的是pascal字符串
> **注4**：P用来转换一个指针，其长度和机器字长相关
> **注5**：最后一个可以用来表示指针类型的，占4个字节
 

为了同c中的结构体交换数据，还要考虑有的c或c++编译器使用了字节对齐，通常是以4个字节为单位的32位系统，故而struct根据本地机器字节顺序转换.可以用格式中的第一个字符来改变对齐方式.定义如下：

|Character	|Byte order|Size and alignment|
|--------|--------|-----------------|
|@|native|native            凑够4个字节|
|=|native|standard        按原字节数|
|<|little-endian|standard        按原字节数|
|>|big-endian|standard       按原字节数|
|!|network (= big-endian)	|standard       按原字节数|

使用方法是放在fmt的第一个位置，就像'@5s6sif'


# 参考

[1] [Python使用struct处理二进制](http://www.cnblogs.com/gala/archive/2011/09/22/2184801.html)
