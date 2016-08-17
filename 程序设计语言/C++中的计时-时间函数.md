
C++中可以用来计算时间耗时的方法有很多种，下面只介绍一种比较简单又可以精确到毫秒的方法：使用time.h中的clock()构造clock_t对象（其实就是一个long类型的变量）, 将一段程序执行过程的clock差值除以`CLOCK_PER_SEC`(clock数/每秒, 值为((clock_t)1000))，即可求出以秒为单位的耗时。

注意，在time.h中的相关类型定义如下：

``` C++
// clock_t类型实际上是long类型
typedef	long	clock_t;

// 1000l
#define	CLOCKS_PER_SEC	((clock_t)1000)
```

主函数
```c++
#include <iostream>
#include <cmath>
#include <time.h>

using namespace std;

int main()
{
	// 使用clock()构造clock_t对象(实际上是long类型的变量)
    clock_t t1 = clock();

	// 一段计算
    for(int i = 0; i < 1000000; i++) {
        pow(2, i);
    }
    
    // 计算clock差，除以clock数/每秒，即可求出秒数
    // 将秒数乘以1000得到毫秒数
    cout << (clock() - t1) * 1.0 / CLOCKS_PER_SEC * 1000 << endl;
    return 0;
}
```
