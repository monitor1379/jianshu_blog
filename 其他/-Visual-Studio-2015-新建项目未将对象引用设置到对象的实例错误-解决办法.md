Visual Studio 2015 新建项目"未将对象引用设置到对象的实例"错误 解决办法

安装的时候没有出错，一点击新建项目就报错，卸载与重装都没用的情况下，可以试一下这个解决办法。经测试，win10 64位下64位的VS2015可以解决


**解决办法**：

- 在window中打开功能里输入regedit,回车打开注册器-
- 然后进入如下路径中：
`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\FipsAlgorithmPolicy`
- 将enable设置为0



>作者：妖燎雨
链接：https://www.zhihu.com/question/37119592/answer/82274469
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
