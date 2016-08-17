###前言
-  最近在学习深度学习，在编写Auto Encoder提取隐含层的输出值时发现，使用MATLAB中自带的sim函数计算得到的神经网络输出值与自己手工计算的前向传播输出值不符合。
- 经过查阅后发现在旧版本中的newff函数，神经网络输入值的归一化以及输出值的反归一化是编程者自己写的，而新版本的newff函数 MATLAB则会自动帮我们完成归一化与反归一化。这意味着，如果手工计算用新版本newff函数训练出来的BP神经网络，则需要进行手工的归一化与反归一化。

``` MATLAB
clear
clc

% 输入数据
X = [1 2 3;2 3 4;3 4 5;4 5 6];
% X = [1 2 3;
%	   2 3 4;
%	   3 4 5;
%	   4 5 6];
% 期望归一化到[-1, 1]区间中，即
% normX = [-1 0 1;
%	   -1 0 1;
%	   -1 0 1;
%	   -1 0 1];
Y = X;

% 创建一个4个输入神经元，2个隐含层神经元，4个输出神经元的BP神经网络
net = newff(X, Y, 2); % 新版本newff函数
% net = newff(minmax(X), [2 4], {'tansig', 'purelin'}); % 旧版本newff函数

% 设置训练数据集占原有数据集的百分比
net.divideParam.trainRatio = 1;
 
% 训练
net = train(net, X, X);

% 仿真，看结果
net(X)

% 归一化到[-1 1]区间，
[normX, PS] = mapminmax(X);

% 为了测试我们拿第一个数据进行手工前向计算
x = normX(:, 1);

% 计算隐含层输出值
z1 = netsum(net.iw{1} * x, net.b{1})
a1 = tansig(z1)

% 计算输出层输出值
z2 = netsum(net.lw{2} * a1, net.b{2})
a2 = purelin(z2)

% 反归一化
y = mapminmax('reverse', a2, PS)；


disp(y);

disp(Y(:, 1));
```
