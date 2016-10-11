matplotlib库的教程和使用方法此处就不累赘了，网上有十分多优秀的教程资源。此处直接上代码：
```python
def demo():
    # 读取图片
    training_x, training_y, test_x, test_y = dataset.load_mnist_data(1000, 100)
    im = training_x[0]
    # 绘画灰度图的四种方法
    plt.subplot(221); plt.imshow(im, cmap=plt.cm.gray)
    plt.subplot(222); plt.imshow(im, cmap=plt.cm.gray_r)
    plt.subplot(223); plt.imshow(im, cmap='gray')
    plt.subplot(224); plt.imshow(im, cmap='gray_r')
    plt.show()

```

效果图下图所示：

![4种灰度图效果.png](http://upload-images.jianshu.io/upload_images/1624726-5b90864f2b43c5de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由上图可以看出，`gray_r`中的`r`表示`reverse`，代表`逆转`的意思
