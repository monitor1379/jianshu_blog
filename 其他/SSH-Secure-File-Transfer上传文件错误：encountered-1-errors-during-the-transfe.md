SSH Secure File Transfer上传文件错误：encountered 1 errors during the transfer解决办法


最近在学习Hadoop时，需要使用虚拟机来搭建Cluster，结果在使用SSH Secure File Transfer从本地向虚拟机传输文件时，提示错误：
> encountered 1 errors during the transfer!

查了一下，原因是本地文件的路径中存在括号，将括号去掉或者把文件复制换个路径再upload就可以了。
