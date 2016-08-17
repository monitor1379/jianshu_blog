本文主要介绍如何在Visual Studio 2015中为工程项目配置OpenCV 3.1库

# Visual Studio 2015下OpenCV 3.1 配置

---
配置说明：

 OS : Win10
Visual Studio : 2015
OpenCV : 3.1.0


---

### 配置环境变量

- 在官网下载最新版本的[OpenCV for Windows](http://opencv.org/downloads.html)
- 解压好opencv 3.1后，在环境变量的Path一栏加入 `D:\***\opencv\build\x64\vc14\bin;`。
![opencv_path](http://upload-images.jianshu.io/upload_images/1624726-d3c10cb1040314e2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 要注意的是不同版本的Visual Studio自带不同版本的VC：
VS版本：VC版本
Visual Studio 2012 : vc12
Visual Studio 2015 : vc14


---

### 配置Visual Studio

- 第二步主要配置项目的**包含目录、库目录以及链接器**三项。这三项都可以在项目用 VS新建一个Win32空项目后，右键`项目` ->  `属性`:

![项目-属性](http://upload-images.jianshu.io/upload_images/1624726-ba1744e74d480c5f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 配置包含目录。VC++目录 -> 包含目录 -> 编辑，添加`D:\OpenCV\opencv\build\include`

![包含目录配置1](http://upload-images.jianshu.io/upload_images/1624726-7ef293186a3568e4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![包含目录配置2](http://upload-images.jianshu.io/upload_images/1624726-d008181d7075ef89?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 配置库目录。同上点击 库目录 -> 编辑，添加库目录文件夹`D:\OpenCV\opencv\build\x64\vc14\lib`

- 配置链接器。点击左边的 链接器 -> 输入 -> 附加依赖项，如果是Release则添加 `opencv_world310.lib`，如果是Debug则添加`opencv_world310d.lib`

![链接器配置1](http://upload-images.jianshu.io/upload_images/1624726-6993c08d8d99834f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![链接器配置2](http://upload-images.jianshu.io/upload_images/1624726-379e92997ffe130f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


---

###测试

项目配置成功，可用如下代码测试
``` c++
#include <iostream>
#include <opencv2\core\core.hpp>
#include <opencv2\highgui.hpp>

using namespace cv;

int main()
{
	Mat img = imread("monitor1379.jpg");//读取图片
	namedWindow("namedWindow");//定义一个窗口
	imshow("namedWindow", img);//显示图片
	waitKey(0);//按键则关闭，如果没有这个图片窗口会一闪而过
	return 0;
}

```

测试效果：
![测试效果](http://upload-images.jianshu.io/upload_images/1624726-0de652ac06013ddc?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
