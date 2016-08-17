### 对象复制
在C++中，假设现在有一个Person类

``` c
class Person {

public:

	int age;
	char *name;

	// 构造方法
	Person(int a = 0, char *n = NULL)
	{
		age = 0;
		name = strdup(n);
	}
	
};
```



当执行以下代码时，会发现`p2.name` 居然也随着`p1.name` 的改变而改变。
```
int main()
{
	Person p1(20, "Mike"), p2(p1);
	
	// 修改p1指针类型成员变量name指向的值
	strcpy(p1.name, "Jhon");
	
	// 输出p2.name
	cout << p2.name << endl;

	return 0;
}
```

这是因为`p2.name` 与 `p1.name` 指向同一块内存区域。为了修改这样的错误，我们需要使用复制构造函数来重写Person类对象的复制规则。



### 复制构造函数

复制构造函数是C++类中的一种特殊的构造函数，它用一个已知的对象初始化一个正在创建的同类对象。

复制构造函数的一般格式如下：
```
<类名>::<类名>(const <类名>& <引用对象名>)
{
	// 复制构造函数体
}
```

其中，复制构造函数具有一下特点：

- 复制构造函数也是一种构造函数，因此函数名与类名相同，并且不能指定函数返回类型。
- 只有一个参数，是对同类的某个对象的引用。
- 每一个类中都必须有一个复制构造函数。如果类中没有声明复制构造函数，编译器会自动生成一个具有上述形式的共有复制构造函数。


比如Person类
```  c
class Person {

private:
	int age;
	char *name;
	
public:

	// 构造方法
	Person(int a = 0, char *n = NULL)
	{
		age = 0;
		name = strdup(n);
	}
	
	// 复制构造方法
	Person(const Person &other)
	{
		
		age = other.age;
		// 成员变量是指针类型时，需要申请属于自己的内存空间
		name = strdup(other.name);
	}
};
```

再次执行main方法，发现`p2.name` 的值还是`Mike`， 修改成功。


### 复制构造函数的调用时机

通常情况下，复制构造函数在以下3中情况会被调用：
1. 用类的一个已知的对象去初始化该类的另一个正在创建的对象。 
2. 采用传值调用方式时，对象作为函数实参传递给函数形参。
3. 对象作为函数返回值。


最后需要注意的是，对象的深层复制也可以使用重载运算符=来实现。
