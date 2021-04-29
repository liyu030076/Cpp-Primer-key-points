```
泛型编程 GP：独立于 特定类型 的编程
```
`OO:` 多态 发生于 `运行时` 存在继承关系的类

`GP:` 多态 体现为 跨越 `编译时` 不相关的类型
```
标准库 独立于类型 定义 容器、迭代器、算法 
=> 几乎可以在 任意类型上 用 标准库的 类 和 函数
```

#1 模板
```
定义
```
template `模板形参列表(template parameter list)`
```
// 即
template < 以逗号分隔的 >=1个 模板形参 >
```

##1.1 函数模板 function template

```
(1)定义
```
```
template <typename T>
int compare(const T& x, const T& y)
{
	if(x < y) 
		return -1;
	if(y < x)
		return 1;
	return 0;
}
```

```
(2)使用
note: 无论使用 模板还是非模板, 
编译器都会 推导出 字面值 arg 的 类型:
1 / "hello" -> const int / const char*
```

```
int main()
{
	//(1) T is int
	// compiler instantiates int compare(const int&, const int&)
	std::cout << compare(1, 0) << std::endl;

	//(2) T is string
	// compiler instantiates int compare(const string&, const string&)
	std::string s1 = "hello", s2 = "world";
	std::cout << compare(s1, s2) << std::endl;
}
```
##1.2 类模板 class template

```
(1)定义

template<typename T>
class Queue
{
    ...
};
```
```
可 定义 3种 成员: mem `type / data / function`
```
class 和 class mem `定义 时, template para` 可作为 type / value 的 `占位符`,
`使用 时`, 再提供 `特定 type / value`


**(2)`使用 类模板时, 必须为 模板形参 显示指定 模板实参( 即 模板形参 的 特定 类型/值)`, 而使用 模板函数时, 不必**

```
编译器 用 模板实参(`用户指定的 特定 类型/值`) 
实例化 类模板(`替换 模板形参`) , 
得到 特定类型/值 的 版本
```

```
Queue<int> queue;
```


##1.3 模板形参

```
模板形参 可以是
1)类型形参(type parameter)     表示 未知 类型
2)非类型形参(nontype parameter) 表示 未知 值/常量表达式
```
(1) 每个 `模板 类型形参 / 非类型形参` 前 必须带 `class 或 typename / 类型名`
```
template <typename T, size_t N> ...
```

```
(2) 模板形参的 作用域

从 声明 模板形参 之后 到 模板声明或定义 结尾

(3)模板 前向声明 与 定义 中, 模板形参名 可以不同
```

##1.4 模板 类型形参: 表示 `未知 类型`
```
(1)模板形参列表 中 class( 早出现 ) / typename 
关键字 含义相同, 都是 指定 模板类型形参

模板类型形参 可作 `类型说明符` 用在 `模板中任何地方`,
与 内置类型/类类型 说明符 使用方式相同, 可用于指定
1)返回类型
2)函数形参类型
3)变量声明
4)强制类型转换
```
(2)`typeneme 的另一功能`

`告诉编译器 其 修饰的 name 是一个类型`

```
原因:
默认下, 编译器 把 class_name::member_name 
当作 member data, 而不是 member type
```

```
//error
template <typename T>
T func(T* array)
{
	//默认, 编译器 把 T::size_type 当作 T 的 data, 
	// 与 p 相乘 => 编译报错
	T::size_type *p; 
}

//ok
template <typename T>
T func(T* array)
{
	//typename 告诉 编译器, 
	//绑定到 `类型T` 的 `成员 size_type` 是 类型名
	typename T::size_type *p; // 声明 指针 P
}
```
(3) `若 拿不准 是否需要以 typename 指定 一个名字 是 一个类型, 则 用 typename. 在 类型 前 指定 typename 没有害处, 即使 typename 是不必要的`

##1.5 模板 `非类型形参` : 表示 `未知 值/常量表达式`

是 `常量值`
用于 需要 `常量表达式` 的 `场景`
其 `类型 在 模板形参表 中 指定`
`调用函数时 用 值代替`

```
#include <iostream>

template <typename T, size_t N>
void array_init( T (&parm) [N] ) 
{   // 函数模板 的 `形参是 数组的引用 `
	for( size_t i = 0; i < N; ++i)
		parm[i] = i;
}

int main()
{
	int x[5];

	//函数调用时, 编译器 从 数组实参 计算 非类型形参 的值
	// instantiate array_init( int (&) [5] )
	array_init(x); 
	
	std::cout << x[1] << std::endl;
}
```

##1.6 编写 泛型程序 的 2个原则

(1)`模板 的 形参` 是 `const 引用`
以 允许使用 `copy deleted 的 类型`

```
模板的 形参: 指 模板体的 形参, 
      而不是 模板形参列表中的 模板形参

(2)函数体中的 测试 只用 < 比较
```

#2 实例化

**`实例化 == 匹配 推导出 模板形参 + 绑定到 模板实参`
编译器 从 `使用模板 所用的 已知类型/值` `匹配推导` 出 `唯一确定` 的 `模板形参( 类型 / 值)`, 再将 模板形参 `绑定到 模板实参`**
```
//`使用模板 所用的 已知类型/值` 主要有3种:
//(1)模板类
Queue<int> queue    中的 int (模板实参)

//(2)模板函数
compare(1, 0)       中的 1, 0 (模板实参)

//(3)模板函数 初始化 函数指针
int (*pf) (const int&, const int&) = compare 中的 pf 
//note: pf 不是 模板实参, 所以 用 `使用模板 所用的 已知类型/值`
// 若 仅看 模板实参 到 模板形参 而 忽略 函数模板 到 指针 的转换, 
// 也可以称为
//`从 模板实参(const int&, const int&) 推导出 模板形参(int, int)`
```
##2.1 概述

```
模板本身不是 函数 或 类

实例化: 编译器 产生模板的 特定 类型/值 实例(版本) 的过程

类/函数 模板 每次实例化 编译器都会产生 `独立的` 类/函数
```

`使用 类模板 时, 必须显式 指定 模板实参`, 而 使用 函数模板 则 不必
```
Queue<int> queue;

compare(1, 0); // 而不用 compare<int>(1, 0)
```

(1)模板 在 `使用时 实例化`
```
1)类模板 在 使用 指定类型的类模板时 实例化
```
2)函数模板 在其 `1)调用时` 或 `2)初始化/赋值 函数指针 时` 实例化

```
(2)类模板 实例化

code 中 编写 `Queue<int> queue` 时, 编译器 产生 `Queue<int>` 的类

(3)函数模板 实例化

使用 函数模板 时, 编译器 会进行 `模板实参推导`
```

##2.2 模板实参推导

`1. 模板实参 2种 受限转换`

`(1)const 转换`
```
1)模板函数 形参为 const 引用 或 cosnt 指针 时, 
  实参可用 non-const obj, 
 `non-const obj converted to const reference` by compiler

2)模板函数 形参 `不是 引用类型`, 则 
  形参类型 和 实参 都 `ignore const`
```

`(2)数组 或 函数` 类型 转换为 `指针`

```
template<typename T>
T fobj(T, T); // arguments are copied

template<typename T>
T fref(cosnt T&, const T&); //reference arguments

string s1("hello");
cosnt string s2("world");

//(1)cosnt ignored: calls fobj(string, string)
fobj(s1, s2); 

//(2)non-const obj s1 converted to const reference
fref(s1, s2);

int a[10], b[5];
//(3)calls f(int*, int*)
fobj(a, b);

//(4)函数类型 转换为 指针 见 第3小节
```
`(3)推导出 模板形参类型 后 不会再 隐式转换`
```
short a;
// 推导出 第1/2个 模板形参 类型为 short int 
//=> 无法匹配 compare(const T&, cosnt T&) 
//=> 模板实参推导 失败
compare(a, 10);
```
`2. 非模板实参 可 常规转换`
```
template<typename T>
T sum(cosnt T& op1, int op2)
{
	return op1 + op2;
}

double d = 3.14;

//d converted to int: instantiate sum(int, int)
sum(10, d); 
```
`3. 模板实参推导 与 函数指针`

```
用 函数模板 对 函数指针 初始化/赋值 时. 
编译器 `用 指针类型 实例化 函数模板`
```

```
template <typename T>
int compare(const T& x, const T& y)

// pf1 points to instantiation: 
//   int compare(const int&, const int&)
int (*pf) (const int&, const int&) = compare;

编译器 推导出 模板形参T 为 int, 
再将 T 绑定到 int 形成 实例化 版本, 
pf 引用该 实例化
```

若 编译器 `不能 推导出 唯一确定的 模板形参 T`, 则 报错
```
void func( int(*) (const int&, const int&) );
void func( int(*) (const string&, const string&) );

//=> error, which instantiation of compare ?
//int compare(const int&, const int&)
//int compare(const string&, const string&)
func(compare); 
```

```
#include <iostream>

template <typename T>
int compare(const T& x, const T& y)
{
    if (x < y)
        return -1;
    if (y < x)
        return 1;
    return 0;
}

void func( int(*_pf) (const int&, const int&) )
{
    std::cout << _pf(1, 0) << std::endl;
}
int main()
{
    //(1)
    int (*pf) (const int&, const int&) = compare;
    std::cout << pf(1, 0) << std::endl;

    //<=>(2)
    func( compare );
}
```

##2.3 函数模板 的 显式实参

scenerio：`无法推导 出 模板形参` 时, 可以 `显示指定 模板实参( 即 模板形参 类型/值）`

```
secenerio: 定义 sum 函数, 使得 返回类型 足够大, 
可以包含 `任意次序 传递的 2个类型的值 的 和`

template <class T, class U>
?? sum(T, U);

sum(1, 2L);// want U sum(T, U)
sum(1L, 2);// want T sum(T, U)

solution1: caller 将小类型 强转为 大类型
short x;
int   y;
sum(static_cast<int>(x), y);
```

solution2： `引入 另一 模板形参, 由 caller 显式指定(实参)`

```
// T1 can't be deduced: 
// it doesn't appear in the function parameter list
template <class T1, class T2, class T3>
T1 sum(T2, T3);

int  i = 1;
long lng = 2L;

// T1 explicitly specified; 
// T2 and T3 deduced from arguments types.
// calls long sum(int, long)
long z = sum<long>(i, lng); 
```

显式 模板实参 可 `消除  二义性`
```
void func( int(*) (const int&, const int&) );
void func( int(*) (const string&, const string&) );

// explicitly specify which version of compare
func(compare<int>); 
```

#3 模板编译模型

```
编译器 看到 `使用模板` 时 才产生 `特性类型/值 的 实例`
```

```
3.1 为何引入 模板 编译模型？

(1)`调用 函数` 时, 编译器 只需看到 函数`声明`;
`定义 类的 对象` 时, 编译器 必须看到 `类 的 定义`, 
而 只需看到 `成员函数 的 声明`
=>
`函数声明 和 类定义` 应放 `头文件`;
`非成员函数 和 成员函数` 的 `定义` 应放 `源文件`

(2)模板 则不同
Inclusion Compilation Model 中 
`头文件 要 #include 模板定义 (的 源文件)`, 
```

`实例化 (编译阶段) 时, 编译器 必须能访问 定义模板的源代码`
```
调用 函数模板 或 类模板的成员函数 时, 编译器需要 函数定义

2种 模板编译模型: 包含/分别 编译模型
同: `函数声明 和 类定义 放 头文件, 
函数定义 和 成员定义 放 源文件`

异:
(1)所有/部分 编译器支持
(2)编译器如何使用 源文件中的定义
```
##3.1 包含编译模型 inclusion
```
一般, 通过在 函数模板声明 或 类模板定义 的
```
`头文件 中, 用 #include 引入 包含 相关定义 的 源文件`

Problem: 多个 源文件 用 同一模板 时, 
该模板的 `同一实例 可能 实例化多次, 编译性能降低`

```
编译器是否优化, 要查 编译器用户指南

优点: 可 保持 头文件 与 实现源文件 分离

//source1.cpp
// instantiate int compare(const int&, const int&)
compare(1,0); 

//source2.cpp 
// instantiate int compare(const int&, const int&)
compare(1,2); 
```

```
// 1. include/utilities.h
#ifndef UTILITIES_H // header guard
#define UTILITIES_H
template <class T>
int compare(const T&, const T&);

// gets definition for compare etc.
#include "../source/utilities.cpp" 

#endif

// 2. source/utilities.cpp
// implementation file utilities.cpp
template <typename T>
int compare(const T& x, const T& y)
{
    if (x < y)
        return -1;
    if (y < x)
        return 1;
    return 0;
}

// 3. test/test.cpp
#include "utilities.h" //工程属性/C++ 常规/附加包含目录 里加 .../include 
#include <iostream>

int main()
{
	std::cout << compare(1, 0) << std::endl;
}
```

##3.2 分别编译模型 separate

coder `用 export 关键字 让 编译器 记住 其修饰的 模板定义`, 编译器 会 `追踪` 模板定义, 使得 `export 修饰的 模板 的 同一实例 只 实例化1次`

```
export 放在 (成员)函数模板 或 类模板 定义的 源文件中, 
可 只 export 类的 某些 成员函数, 声明处 不用 export

//(1)函数模板
// template definition goes in a separately-compiled source file
export template <typename T>
T sum(T t1, T t2)
{
	//...
}

//(2)类模板
//1)Queue.h header file: class template goes in shared file
template <class T>
class Queue
{
	//...
};

//2)source file
//Queue.cpp implementation file: declares Queue as exported
export template <class T>
class Queue;

#include "Queue.h"

// Queue member definitions
```

#4 类模板 成员

```
标准库 queue 实现为 容器 deque 之上的 adapter

本节 为强调使用 低级数据结构 涉及的 编程要点,
将 Queue 实现为 链表 : 用 2个类
```
![image.png](https://upload-images.jianshu.io/upload_images/20172887-54fd85c9c7103aa8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`QueueItem 类 只是为了 实现 Queue 类`, 所以 设为 private 类, 没有 public 成员, 需要将 `Queue 类 设为 QueueItem 类 的 友元`, 以便 Queue 类 访问 QueueItem 类 的成员 

##4.1 语法点

`1. 类 模板 作用域内部, 使用 本类名 时可以 不用指定 模板形参`

`编译器 推断`, 引用 `本类名` 时, `引用的是 同一版本`

```
template <class T>
class Queue
{
public:
	Queue(const Queue& q): head(0), tail(0) { }
	
	/* <=>
	Queue<T>(const Queue<T>& q):head(0), tail(0)
	{
		copy_elems(q);
	}
	*/
};
```

`2. 类模板 外 定义 成员函数 时`
```
template <class T>
return_type Queue<T>::member_name
```

```
(1)必须以 `template 开头, 后接 模板形参列表`
(2)必须 用 `类名::` 作用域操作符 指出它是哪个类的成员
(3)类名必须包含 `模板形参`
```

**3. 类模板 `不带 额外模板形参的 成员函数` 的 实例化**

`(1)类模板 的  成员函数 必为 函数模板`, 无论是否带 额外模板形参
```
成员函数 只能在其 所属类 的 scope 中使用
=> 必然要 先 实例化 类模板
=> 类模板的 成员函数 必为 函数模板, 
   无论是否带 额外模板形参
```

`(2) vs. non-member 函数模板`

`同: 使用 (member / non-member) 函数模板 时, 才实例化`

**异: 对 不带额外模板形参 的 mem func, `实例化 时, 编译器 不进行 模板实参 推导, template para 由 调用它的 obj 的 template arg 确定`**

```
调 Queue<int>  push 函数时, 实例化 push 为:
void Queue<int>::push(const int& val)
```

**=> 用 `模板形参 定义的` 不带 额外模板形参的 `成员函数 形参`, `允许 实参 的 类型转换`**
```
调用 `类模板的 不带...mem function` 
比 调用 `non-member function template` 更灵活
```

```
//不带 额外模板形参的 成员函数, eg.
// void push(const T& val)

Queue<int> q1; // instantiates Queue<int>
short s = 1;

// arg s converted to int(模板形参) and passed to push
q1.push(s); // instantiates Queue<int>::push(const int&)
```

**`4. 成员 模板 member template`**

**(1)成员 模板: `任意类 (template / non-template)` 的 `member` 可以为 `函数模板 或 类模板, 即 member 带额外 模板形参
`**

**`(2)Problem:` copy ctor 和 operator= `固定了 容器 Queue 的 类型` 和 `可 copy/assign` 到 容器 Queue 的 `元素类型`
=> `无法实现 从 Queue<short> 或 vector<int>  copy / assign 元素 到 Queue<int>`**

**`soluition： 用 成员模板, 使 member 的 形参类型 可变`
定义另一 ctor / assign 版本, 其 `形参类型` 为 某种 `迭代器 类型`, 迭代器 `所指 元素 的 类型 可转换为 容器 Queue 的 元素  类型`**

```
template <class Iter>
Queue(Iter begin, Iter end): head(0), tail(0)
{
	copy_items(begin, end); // 重载 copy_items 
}
```
**`(3)成员模板 在 类外定义 时, 必须包含 2个 模板形参列表`**
```
template <class T> template <class Iter>
void Queue<T>::assign(Iter beg, Iter end)
{
	destory();
	copy_elems(beg, end);
}
```

`(4)成员模板 不能为 虚`

`原因: 基类 看不到 子类 覆写 ( override / redefine ) 的 实例化版本, 生成 vtbl 时, 编译器 不知所措了`

`(5)成员模板 实例化: 使用时`

```
2种 模板形参:
```
`1)类定义 的: 类 模板形参`

由 调用它的 `对象的模板实参 确定`


**`2)成员模板 本身定义 的 : 成员模板 本身的模板形参 / 额外 模板形参`**

**由 `成员模板 本身的模板实参 推导 而 确定`**
```
与 non-member 函数模板 相同

与 member non-template 函数 不同:
  不进行 模板实参 推导, 由 调用它的 `对象的模板实参 确定`
```

```
short a[5] = {0, 3, 6, 9, 12};
std::vector<int> vec(a, a + 5);

//(1)构造 Queue<int> 对象 q 时, 
//1) 实例化 类模板 为 Queue<int>
//2) 实例化 Queue<int> 的 基于 迭代器的 ctor

//1)`类 模板形参`: T 
// 由 对象 q 的 模板实参 int 确定 => T == int
//2)`成员模板 本身的模板形参`: Iter
//  类型 由 成员模板本身的 模板实参 (a 和 a+5) 
//  的 类型(均为 short*) 推导
//  成员模板 为 template ... Queue(Iter begin, Iter end)
// => instantiates 
//  void Queue<int>::Queue(short*, short*)
Queue<int> q(a, a + 5);

//(2)使用 q.assign() 时
// instantiates
// void Queue<int>::asign(std::vector<int>::iterator,
//						  std::vector<int>::iterator);
q.assign(vec.begin(), vec.end());
```

`5. 何时实例化 类和成员 : 使用时`

(1)定义 类模板 对象 时, 实例化 
`1)类模板` 
`2)一个 ctor:` 用于 初始化该对象 的 相应的
`3)该 ctor 调用的 任意 成员`

```
//(1) instantiates Queue<int> and Queue<int>::Queue()
Queue<int> q1; 

//(2) instantiates Queue<int>::push(const int&), then
// instantiates QueueItem<int> 及其 copy ctor: QueueItem<int>(const int&) 
q1.push(s); 

/*
void push(const T& val) 
{
	//(1)使用 类模板 QueueItem<T> 创建 obj
	// => `实例化 QueueItem<T> 及其 copy ctor`
	QueueItem<T>* p = new QueueItem<T>(val);

	//(2)使用 member data: QueueItem<T>* head 
	//  => `只 实例化 QueueItem<T>`
	// 若编译器 做了优化, 或者 用 separate compilation model, 
	// 因前面 已 实例化 QueueItem<T>, 则 这里不再实例化 QueueItem<T>
	if (empty()) 
		head = tail = p; 
	//...
}
*/
```

**(2)若 类模板 的 `member data 为 指针, 其 定义/初始化 时, 不 实例化 类模板, 使用时才 实例化 类模板`**
```
//(1)定义/初始化
// instantiates Queue<int> and Queue<int>::Queue(), 
// 只 涉及 `指针 head 的 定义/初始化` : 
//	 Queue() : head(0), tail(0) {} 
// => `不 实例化 head 的 类模板 QueueItem<T>`
Queue<int> q1; 

//(2)使用: 上一个例子中 push 中已涉及
```

`6. 类模板 中 友元声明`

```
将 友元关系 授予 
1)non-template 函数/类
2)函数/类 模板 `所有 instance`
3)函数/类 模板 `特定 instance`

声明依赖 => 前向声明
```

**`只将` 与 QueueItem `用 同样类型 实例化` 的 Queue `设为友元`**

让 Queue 与 QueueItem `一 一 映射`
```
eg. Queue<int>  与 QueueItem<int>
```

```
要决定 类 QueueItem 将 友元关系 授予 Queue 
`所有 instance` 还是 特定 instance

允许 Queue<string> 去 
访问 QueueItem<int> 的 member 没有意义
```

```
//eg1.
template <class T> 
class Queue;

template <class T>
class QueueItem
{
	// Queue 后 用 <T> 是为了 使友元 Queue
	// 与 QueueItem<T> `一 一 映射`
	friend class Queue<T>; 	
	//...
}
```

```
//eg2.
template <class T>
std::ostream& operator<<(std::ostream&, const Queue<T>&);

template <class T>
class QueueItem
{
	// operator<< 后 用 <T> 是为了 使 友元 operator<<
	// 与 Queue<T> / QueueItem<T> `一 一映射`
	friend std::ostream&
	operator<< <T> (std::ostream&, const Queue<T>&);
	//...
}

template <class T>
class Queue
{
	friend std::ostream&
	operator<< <T> (std::ostream&, const Queue<T>&);
	//...
}
```

##4.2 设计过程

```
4.1 语法点 与 4.3 完整类模板 中的 重点就是设计过程
设计过程 就是 不断 解决需求中 遇到的问题 的过程, 循序渐进
```

##4.3 Queue 完整类模板

```
// 写到1个文件中
// test.cpp 
#include <iostream>
#include <vector>

// 前向声明
template <class T>
class Queue;

template <class T>
std::ostream& operator<<(std::ostream&, const Queue<T>&);

template <class T>
class QueueItem
{
    // ---- 对外 友元
    // 类模板 Queue<T> 为了便于访问 QueueItem<T> 的成员, 设为友元
    // Queue 后 用 <T> 是为了 使友元 Queue
    // 与 QueueItem<T> `一 一 映射`
    friend class Queue<T>; // => 要 前向声明

    // operator<<: needs access item / next, 设为 friend
    // operator<< 后 用 <T> 是为了 使 友元 operator<<
    // 与 Queue<T> / QueueItem<T> `一 一映射`
    friend std::ostream&
        operator<< <T> (std::ostream&, const Queue<T>&); // => 前向声明

    // --- 自身
    T item; // Queue 元素 类型 T
    QueueItem* next;

    //(1)item 初始化:
    // 1) `const 引用 接收` 外部 T 型 值,
    // 2) copy 到 data 域
    // => QueueItem 存储 所给元素的 copy, 与 标准库 容器 做法相同
    //(2)next 初始化 为 0, 表示 后面再无元素(其后 没有 QueueItem 节点)
    QueueItem(const T& val) : item(val), next(0) {}
};

template <class T>
class Queue
{
    friend std::ostream&
        operator<< <T> (std::ostream&, const Queue<T>&); // => 前向声明

public:
    Queue() : head(0), tail(0) {}

    Queue(const Queue& queue) : head(0), tail(0)
    {
        copy_items(queue);
    }

    // 支持 从 Queue<short> 或 vector<int>  copy / assign 元素 到 Queue<int>
    //用 成员模板, 使 member 的 形参类型 可变`
    // 定义另一 ctor / assign 版本, 其 `形参类型` 为 某种 `迭代器 类型`, 
    // 迭代器 `所指 元素 的 类型 可转换为 容器 Queue 的 元素  类型
    template <class Iter>
    Queue(Iter begin, Iter end) : head(0), tail(0)
    {
        copy_items(begin, end); // 重载 copy_items 
    }

    // 通常, operator= 与 copy ctor, 形式上的区别:
    // 不能用 初始化列表 方式 进行初始化.
    // 这里, 赋值 的含义为:
    // 将 对方 queue 所有元素 的 copy 存到 本 queue 中, 
    // 若 本 queue 元素多, 只有前面被 覆盖的 item 值变了
    Queue& operator=(const Queue& rhs)
    {
        if (&rhs != this)
        {
            destory(); 
            copy_items(rhs);
        }
        return *this;
    }

    template <class Iter>
    void assign(Iter begin, Iter end)
    {
        destory();
        copy_items(begin, end);
    }

    ~Queue() { destory(); }

    bool empty() const
    {
        return head == NULL;

        /*
        if (head) // head != NULL
            return false;
        else
            return true;
        */
    }

    void push(const T& val) // 尾进/后进, tail 变(原 Queue 为空时, head 也变)
    {
        //(1)push() `const 引用接收` 外部 value, 作 实参 
        //调 QueueItem 的 copy ctor 构造出 QueueItem obj
        //(2) QueueItem 的 copy ctor `const 引用 接收` value,
        // copy 到 data 域
        // => Queue(Item) 存储 所给元素的 copy, 与 标准库 容器 做法相同

        // allocate a new QueueItem obj
        QueueItem<T>* p = new QueueItem<T>(val);

        if (empty()) // 空队列: 头 尾 都指向 p
            head = tail = p;
        else         // 非空  : 尾插
        {
            tail->next = p;
            tail = p;
        }
    }

    // pop 假定 用户 不会在 空 Queue 上调用 pop
    void pop() // 头出/先出
    {
        QueueItem<T>* p = head;
        head = head->next;
        delete p;
    }

    // 2个 front 版本: 
    T& front() { return head->item; }
    const T& front() const { return head->item; }

private:
    // Queue<.> QueueItem<.> 两个类模板的 模板实参 一一对应 才有意义, 
    // 如 Queue<int> 与 QueueItem<int>
    QueueItem<T>* head;
    QueueItem<T>* tail;

    // 供 copy ctor 用 
    //push copy of elements from other queue to this Queue
    void copy_items(const Queue& queue)
    {

        //(1) p == 0 时, jump out of loop 
        // p <=> p != 0(NULL)
        //(2) 链表 指针后移 p = p->next, 不要误写为 p++
        for (QueueItem<T>* p = queue.head; p; p = p->next)
            push(p->item);
    }

    template <class Iter>
    void copy_items(Iter begin, Iter end)
    {
        while (begin != end)
        {
            push(*begin);
            ++begin;
        }
    }

    //释放 Queue 中元素
    void destory()
    {
        while (!empty())
            pop();
    }
};

template <class T>
std::ostream& operator<<(std::ostream& os, const Queue<T>& queue)
{
    QueueItem<T>* p;

    for (p = queue.head; p; p = p->next)
        os << p->item << " ";

    return os;
}

int main()
{
    //(1)default ctor
    Queue<int> q1; // instantiates Queue<int>
    short s = 1;
    int i = 2;

    // args s converted to int(模板形参 特定类型) and passed to push(.)
    q1.push(s); // instantiates Queue<int>::push(const int&)
    q1.push(i);

    std::cout << q1 << std::endl;

    //(2)copy ctor
    Queue<int> q2(q1);
    std::cout << q2 << std::endl;

    //(3)copy ctor 的重载: 
    // Queue(Iter begin, Iter end)
    short a[5] = { 0, 3, 6, 9, 12 };
    Queue<int> q3(a, a + 5);
    std::cout << q3 << std::endl;

    std::vector<int> vec(a, a + 5);
    Queue<int> q4(vec.begin(), vec.end());
    std::cout << q4 << std::endl;

    //(4)operator=(const Queue& queue)
    q4 = q1; // 元素数 少的 容器 赋值为 元素数 多的 容器
    std::cout << q4 << std::endl;

    q1 = q1;
    q1 = q3; // 元素数 多的 容器 赋值为 元素数 少的 容器
    std::cout << q1 << std::endl;

    //(5)operator= 的重载
    // assign(Iter begin, Iter end)
    q1.assign(vec.begin(), vec.end());
    std::cout << q1 << std::endl;
}
```

`重构: 头文件 源文件 分离`

```
//(1)Queue.h
#ifndef QUEUE_H // header guard
#define QUEUE_H

template <class T>
class Queue;

template <class T>
std::ostream& operator<<(std::ostream&, const Queue<T>&);

template <class T>
class QueueItem
{
	friend class Queue<T>;

	friend std::ostream&
		operator<< <T> (std::ostream&, const Queue<T>&); 

	T item; 
	QueueItem* next;
	QueueItem(const T& val) : item(val), next(0) {}
};

template <class T>
class Queue
{
	friend std::ostream&
	operator<< <T> (std::ostream&, const Queue<T>&);

public:
	Queue() : head(0), tail(0) {}

	Queue(const Queue& queue) : head(0), tail(0)
	{
		copy_items(queue);
	}

	template <class Iter>
	Queue(Iter begin, Iter end) : head(0), tail(0)
	{
		copy_items(begin, end);
	}

	Queue& operator=(const Queue&);

	template <class Iter>
	void assign(Iter, Iter);

	~Queue() { destory(); }

	bool empty() const
	{
		return head == NULL;
	}

	void push(const T&);

	void pop();

	T& front() { return head->item; }
	const T& front() const { return head->item; }

private:
	QueueItem<T>* head;
	QueueItem<T>* tail;

	void copy_items(const Queue&);

	template <class Iter>
	void copy_items(Iter, Iter);

	void assign_items(const Queue&);

	void destory();
};

// class template need this to instantiate
// Inclusion Compilation Model: include member function definitions as well
#include "../source/Queue.cpp" 
#endif

//(2)Queue.cpp
template <class T>
void Queue<T>::push(const T& val)
{
	QueueItem<T>* p = new QueueItem<T>(val);

	if (empty())
		head = tail = p;
	else
	{
		tail->next = p;
		tail = p;
	}
}

template <class T>
void Queue<T>::pop()
{
	QueueItem<T>* p = head;
	head = head->next;
	delete p;
}

template <class T>
void Queue<T>::copy_items(const Queue& queue)
{
	for (QueueItem<T>* p = queue.head; p; p = p->next)
		push(p->item);
}

template <class T> template <class Iter>
void Queue<T>::copy_items(Iter begin, Iter end)
{
	while (begin != end)
	{
		push(*begin);
		++begin;
	}
}

template <class T>
Queue<T>& 
Queue<T>::operator=(const Queue& rhs)
{
	if (&rhs != this)
	{
		
		destory(); 
		copy_items(rhs);
	}
	return *this;
}

template <class T> template <class Iter>
void Queue<T>::assign(Iter begin, Iter end)
{
	destory();
	copy_items(begin, end);
}

template <class T>
void Queue<T>::destory()
{
	while (!empty())
		pop();
}

template <class T>
std::ostream& 
operator<<(std::ostream& os, const Queue<T>& queue)
{
	QueueItem<T>* p;

	for (p = queue.head; p; p = p->next)
		os << p->item << " ";

	return os;
}

//(3)test.cpp
#include <iostream>
#include <vector>
#include "Queue.h"

int main()
{
	Queue<int> q1;
	short s = 1;
	int i = 2;

	q1.push(s);
	q1.push(i);

	std::cout << q1 << std::endl;

	Queue<int> q2(q1);
	std::cout << q2 << std::endl;

	short a[5] = { 0, 3, 6, 9, 12 };
	Queue<int> q3(a, a + 5);
	std::cout << q3 << std::endl;

	std::vector<int> vec(a, a + 5);
	Queue<int> q4(vec.begin(), vec.end());
	std::cout << q4 << std::endl;

	q4 = q1;
	std::cout << q4 << std::endl;

	q1 = q3;
	std::cout << q1 << std::endl;
	q1.assign(vec.begin(), vec.end());
	std::cout << q1 << std::endl;
}
```

#5 一个 泛型 句柄类
```
(1) handle class:
1)管理 inheritance hierarchy 中 obj 的 pointer
2)handle 的 client 不用操心 pointer management
3)handle 能 动态分配/释放 相关 继承类的 obj, 
  并将所有 `实际工作 转发 给 继承层次中的类`

(2) generic handle class
```

将 `reference cout` 和 继承层次中 `基础obj 的 pointer 管理` 泛型化

##5.1 define hanle class

`Handle class 行为类似 pointer`

`(1) copy Handle obj 不会 copy 基础 obj`
```
, copy 后, 2 个 Handle obj 引用 同一 基础 obj
```

`(2) 创建 Handle obj` 时, client 要 `传` Handle 管理的 class ( 或 其 后代类 ) 的 `动态分配对象 的 地址`
```
, 此刻起, Handle 将 拥有 该 obj
```

`(3) 不再有任何 Handle obj 关联 该 obj` 时, `Handle class 负责 delete 该 obj`

##5.2 use handle class
```
1. simple use: 
single Handle obj bound to dynamically allocated obj
```

**2. normal Handle class `用于 other class 的 内部实现` 中**
`(1) 用 ++ right ref_count 防止 自身赋值: ++*rhs.p_ref_count;`
```
if self-assignment, after ++, left/right's ref_count >= 2
=> the next -- in decre_ref_count() 
   won't made ref_count 0
=> won't free old resource
```

`(2) 复制 未知 type`
```
secenario: 
传给 Handle 第 2 ctor, Handle(const Base& rhs)
的 `arg obj 的 runtime / actual type`, 
只 知道 是 Base class 或 其 后代 class,
但 `不知道 其 exact type`
```
```
`solution: 通用方法是`
```
`1) 定义 virtual function 进行 copy, 命名为 clone`
`2) 从 base class 开始, inheritance hierarchy 中 每层上的 calss 设 clone, 且 redefine/override 该 virtual function, 以 create 该 class obj 的 new copy`
```
virtual Base::Base* clone() const
{
	return new Base(*this);
}
	
virtual Derived::Derived* clone() const
{
	return new Derived(*this);
}
```

![image.png](https://upload-images.jianshu.io/upload_images/20172887-749f6943698aade2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
#include <iostream>
#include <exception>

/* ********* 1.inheritance hierarchy ********* */
class Base
{
private:
	std::string isbn;
protected:
	double price; // undiscounted price

public:
	Base(const std::string& book = "",
		 double _price = 0.0) :
		 isbn(book), price(_price) {}

	virtual ~Base() {}

	std::string get_isbn() const { return isbn; }

	//(1) total sales price for a specified number of item
	// derived classes will `override` and apply different discount algorithm
	virtual double total_price(std::size_t n) const
	{
		return n * price;
	}

	//(2) copy unknow type
	virtual Base* clone() const
	{
		return new Base(*this);
	}
};

class Derived: public Base
{
private:
	// 2 mem for discount
	std::size_t min_quantity;
	double discount; 
public:
	Derived(const std::string& book = "",
		double _price = 0.0,
		std::size_t quantity = 10,
		double _discount = 0.9) :
		Base(book, _price), min_quantity(quantity), discount(_discount) {}

	//(1) `redefine/override` base version to implement discount policy
	virtual double total_price(std::size_t) const;

	//(2) copy unknow type
	virtual Derived* clone() const
	{
		return new Derived(*this);
	}
};

double Derived::total_price(std::size_t num) const
{
	if (num >= min_quantity)
		return num * ( 1 - discount ) * price;
	else
		return num * price;
}

/* ********* 2.normal handle class ********* */
class Handle
{
private:
	Base* ptr;          //1) pointer to shared object
	size_t* p_ref_count;//2) pointer to shared ref_count

	//3) practical private func
	// decrement ref_count and delete pointer if needed
	void decre_ref_count()
	{
		if (--*p_ref_count == 0)
		{
			delete ptr;
			delete p_ref_count;
		}
	}
public:
	//(1) 2 ctor
	//1) unbound handle: handle not bind to any obj
	Handle() :
		ptr(0), p_ref_count(new size_t(1)) {}

	//2) attach a handle to a copy of the 
	// runtime/actual type of the const reference para
	Handle(const Base&);

	//(2) copy control: normal pointer behavior,
	// but last Handle deletes the object
	Handle(const Handle& rhs) :
		ptr(rhs.ptr), p_ref_count(rhs.p_ref_count)
	{
		++*p_ref_count;
	}
	Handle& operator=(const Handle&);

	~Handle() { decre_ref_count(); }

	//(3) overloaded operators to support pointer behavior
	// only define const version, because only use const obj currently
	const Base& operator*() const
	{
		if (ptr)
			return *ptr;
		throw std::runtime_error("dereference of unbound Handle");
	}
	const Base* operator->() const
	{
		if (ptr)
			return ptr;
		throw std::runtime_error("access through unbound Handle");
	}	
};

// the 2th ctor: 
//1) client of Handle create obj of type of inheritance hierarchy
//2) ctor clone ( allocate ) a new obj according to 
//   runtime/actual type of para ( the created obj)
//   by call rhs's virtual mem func clone()
//=> 1) Handle binds to the new obj; 
//   and ensure last Handle deletes the new object
//   2) clone(): copy unknow type
Handle::Handle(const Base& rhs) :
	ptr(rhs.clone()),
	p_ref_count(new std::size_t(1)) {}

inline Handle&
Handle::operator=(const Handle& rhs)
{
	//1) protect against self-assignment by ++ right ref_count 
	// => if self-assignment,
	//    after ++, left/right's ref_count >= 2
	// => -- won't made ref_count 0
	// => won't free old resource
	++*rhs.p_ref_count; 
	decre_ref_count();  //2) -- left ref_count

	ptr = rhs.ptr;
	p_ref_count = rhs.p_ref_count;

	return *this;
}

void test1()
{
	//1)ctor1
	Handle hp1;
	{   // new scope
		Handle hp2 = hp1;
	}
}

void test2()
{
	Base b = Base("book1", 20);
	Derived d = Derived("book2", 30);

	//1) ctor2
	Handle hp1(b);
	Handle hp2(d);
	{   // new scope
		//2) copy pointer; ref_count incremented
		Handle hp3 = hp1; 
		Handle hp4 = hp2;
	} // hp3/4 goes out of scope; ref_count decremented

	//3) operator= : after assignment, 
	// the cloned obj bound to old hp1 is deleted, 
	// the corresponding ref_count is deleted 
	hp1 = hp2;
} // hp1/2 goes out of scope; hp1/2 dtor delete the cloned obj bound to old hp2

int main()
{
	test1();
	test2();
}
```

**3. `generic Handle class` 用于 `other class 的 内部实现` 中**
```
`与 2 中 concrete Handle class 区别:`
```
**将 `concrete Handle 拆成 2 层: extract 出 generic Handle class`, 余下 `中介层 class ( 假定命名为 ) Handle_client`**

`3.1 generic Handle class`
`(1) 只 1 个 ctor, para 为 泛型 指针, default arg = 0`
```
=> 
1> defaultly, unbound handle
2> can receive pointer to any type: 比如 
`pointer 指向 要管理的 继承层次 中 某个 class‘s obj`
```

`(2) 只应该有 2种 用法:` 
```
1) simple use: 
`直接 用 Handle obj 绑定到 dynamically allocated obj`
```
`2) 只用于 other class 内部实现` 中, generic Handle class `只通过 其 Handle_client 引用`
```
//(1)
Handle_client(const Base& actual_obj) :
	handle( actual_obj.clone() ) {}
//(2)
const Base& Handle_client::operator*() const
{
	return *handle;
}
//(3)
const Base* Handle_client::operator->() const
{
	return handle.operator->();
}
```

`3.2 Handle_client 将 所有 "实际工作" 转发 ( forward )` 给 `继承层次 中的 class`

```
//(1)
Handle_client(const Base& actual_obj) :
	handle( actual_obj.clone() ) {}
```
```
(2) dtor 调用链:
```
`~Handle_client() -> 调 ~Handle（）-> 调 decre_ref_count(): *ref_count 减完后 == 0 时, delete ptr -> 调 ptr 指向的 实际类型 的 dtor: ~autual_Type()`

![image.png](https://upload-images.jianshu.io/upload_images/20172887-f4514db9128d8606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
#include <iostream>
#include <exception>

/* ********* 1.inheritance hierarchy ********* */
class Base
{
private:
    std::string isbn;
protected:
    double price; // undiscounted price

public:
    Base(const std::string& book = "",
        double _price = 0.0) :
        isbn(book), price(_price) {}

    virtual ~Base() {}

    std::string get_isbn() const { return isbn; }

    //(1) total sales price for a specified number of item
    // derived classes will `override` and apply different discount algorithm
    virtual double total_price(std::size_t n) const
    {
        return n * price;
    }

    //(2) copy unknow type
    virtual Base* clone() const
    {
        return new Base(*this);
    }
};

class Derived : public Base
{
private:
    // 2 mem for discount
    std::size_t min_quantity;
    double discount;
public:
    Derived(const std::string& book = "",
        double _price = 0.0,
        std::size_t quantity = 10,
        double _discount = 0.9) :
        Base(book, _price), min_quantity(quantity), discount(_discount) {}

    //(1) `redefine/override` base version to implement discount policy
    virtual double total_price(std::size_t) const;

    //(2) copy unknow type
    virtual Derived* clone() const
    {
        return new Derived(*this);
    }
};

double Derived::total_price(std::size_t num) const
{
    if (num >= min_quantity)
        return num * (1 - discount) * price;
    else
        return num * price;
}

/* ********* 2.generic handle class ********* */
/*
    generic handle class:
    Provides poniterlike behavior.
    1)Although `access through an unbound Handle is checked`
    and throws a runtime_error exception.
    2)The object to which the Handle points
    is deleted when the last Handle goes away.
    3)User should allocate new objects of type T and
    `bind them to a Handle`.
    4)Once an object is `bound` to a Handle,
    the user `must not delete` that object.

*/

template <class T>
class Handle
{
private:
    T* ptr;             //1) pointer to shared object
    size_t* p_ref_count;//2) pointer to shared ref_count

    //3) practical private func
    // decrement ref_count and delete pointer if needed
    void decre_ref_count()
    {
        if (-- * p_ref_count == 0)
        {
            delete ptr;
            delete p_ref_count;
        }
    }
public:
    //(1) ctor para: generic pointer with default arg = 0
    //1) defaultly, unbound handle
    //2) can receive pointer to any type 
    Handle(T* p = 0) :
        ptr(p), p_ref_count(new size_t(1)) {}

    //(2) copy control: normal pointer behavior,
    // but last Handle deletes the object
    Handle(const Handle& rhs) :
        ptr(rhs.ptr), p_ref_count(rhs.p_ref_count)
    {
        ++* p_ref_count;
    }
    Handle& operator=(const Handle&);
    ~Handle() { decre_ref_count(); }

    //(3) overloaded operators to support pointer behavior
    T& operator*();
    T* operator->();
    const T& operator*() const;
    const T* operator->() const;
};

template <class T>
inline Handle<T>&
Handle<T>::operator=(const Handle& rhs)
{
    ++* rhs.p_ref_count;
    decre_ref_count();

    ptr = rhs.ptr;
    p_ref_count = rhs.p_ref_count;

    return *this;
}

template <class T>
inline T&
Handle<T>::operator*()
{
    if (ptr)
        return *ptr;
    throw std::runtime_error("dereference of unbound Handle");
}
template <class T>
inline const T&
Handle<T>::operator*() const
{
    if (ptr)
        return *ptr;
    throw std::runtime_error("dereference of unbound Handle");
}

template <class T>
inline T*
Handle<T>::operator->()
{
    if (ptr)
        return ptr;
    throw std::runtime_error("access through unbound Handle");
}

template <class T>
inline const T*
Handle<T>::operator->() const
{
    if (ptr)
        return ptr;
    throw std::runtime_error("access through unbound Handle");
}

/* ********* 3.Mediator class: client of Handle ********* */
class Handle_client
{
private:
    Handle<Base> handle;
public:
    //(1) ctor
    //1) the first ctor: call Handle ctor with default arg 
    Handle_client() : handle() {}

    //2) the 2th ctor
    // copy/clone the runtime managed obj,
    // and attach handle to the copy
    // forward (转发) actual work to class in heritance hierarchy
    Handle_client(const Base& actual_obj) :
        handle(actual_obj.clone()) {}

    //(2)no copy control: synthesized versions works well
    // synthesized versions call will forward (转发) call
    // handle's copy ctor / operator= / dtor

    //(3)mem access operators:
    // forward (转发) their work to Handle class
    const Base& operator*() const
    {
        return *handle;
    }

    const Base* operator->() const
    {
        return handle.operator->();
    }
};

void test1()
{
    Handle<int> hp(new int(5));
    {
        Handle<int> hp1 = hp;
        *hp1 = 10;
    }
}

void test2()
{
    Base b = Base("book1", 20);
    Derived d = Derived("book2", 30);

    Handle_client hc1(b);
    Handle_client hc2(d);
    {
        Handle_client hc3 = hc1;
        Handle_client hc4 = hc2;
    }

    hc1 = hc2;
}

int main()
{
    test1();
    test2();
}
```

```
after `Handle_client hc4 = hc2` result:
```

![image.png](https://upload-images.jianshu.io/upload_images/20172887-72b54ccbae6a9de4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/20172887-6f3d9758465b9571.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##5.3 C++ 赋值操作符 为什么要 防止 `自身赋值`?
```
保证正确性:
```
`自身赋值 时, 释放 old resource is disaster: 因为 assign allocated new resource 时, 需要 用 old resource`
```
operator= 
1) erase  旧值: 释放   左 obj 的 ( old ) resource
2) allocate new resource
3) assign 新值: 赋值为 右 obj 的 ( old ) resource
```

```
//eg.
class String {
public:             
  String& operator=(const String& rhs);
private:
  char *p; // new str[];
};

String& String::operator=(const String& rhs)
{
	// not check self assignmemt
	
	//1) delete old memory
	delete [] p;    
	
	//2) allocate new memory
	p = new char[strlen(rhs.p) + 1];
	
	//3) copy resource pointed to by rhs's pointer mem
	strcpy(p, rhs.p);
	
	return *this;  
}

String s1 = "Hello";
s1 = s1;   
```

#6 模板 特化 ( specialization )

```
1. 意义
(1) 模板 `泛化版 对某些 type 可能是 错的`
(2) 特化版 `比 泛化版 的 实例化` 可能 `效率更高`
```

```
// eg1. 泛化版  对 T = const char* 是 催的
template <typename T>
int compare(const T& v1, const T& v2)
{
	if (v1 < v2)
		return -1;
	if (v2 < v1)
		return 1;
	return 0
}	
```

`2. function template 的 特化`
```
(1) 形式
1) template 后接 空尖括号 <>
2) template name 后的 <> 中 
指定 特化版 template paras
```
```
T = const char* 时,
```
`const T& (const reference) +   const char* (指向 const 的 pointer) = const char* const&`

```
// eg2. solution of eg1
template <>
int 
compare<const char*>(const char* const& v1, 
					 const char* const& v2)
{
	return strcmp(v1, v2);
}
```
```
(2) function template 特化版 声明, 
只能 省略 function body

若 省略 template<> 及 template name 后的 <...>, 
则 变成 non-template 的 函数声明
```
```
(3) 与 其他 function declaration 一样, 
```
应在 `1个 头文件` 中 包含 `模板特化 的 declaration`, 在 `使用该特化 的 所有 源文件 include 该 头文件`

`(4) explicit 特化版 与 泛化版 的 实例化 不能共存`

`3. class template 的 特化`

```
default, copy C 风格 字符串 只会 copy pointer.

若 `pointer 指向 动态内存, 往往需要 特化版`
```
`(1) 整个 class 的 特化版: 用 const char*`
`(2) class 特化版 外部 定义 mem 时, 不用 template<>`
```
template <>
class Queue<const char*>
{
private:
	Queue<std::string> real_queue;
public:
	void push(const char*);
};

void Queue<const char*>::push(const char* p_val)
{
	return real_queue.push(p_val);
}
```

`4. 只特化 class 的 部分 mem`

```
template <>
void Queue<const char*>::push(const char* const& p_val)
{
	char* p_new = new char[ strlen(p_val) + 1 ];
	strncpy(p_new, p_val, strlen(p_val) + 1 );

	QueueItem<const char*>* pt =
		new QueueItem<const char*>(p_new);

	if (empty())
		head = tail = pt;
	else
	{
		tail->tail = pt;
		tail = pt;
	}
}

template <>
void Queue<const char*>::pop()
{
	QueueItem<const char*>* pt = head;
	delete head->item;

	head = head->next;
	delete pt;
}
```

`5. 只 特化 class 的 部分 template paras: 偏特化`

`泛化版 中 去掉想特化 的 template para, class name 后 < template para list, but 要特化者 写为 具体类型 >`

```
template <class T1, class T2>
class A
{
	// ...
};

// partial specialization: fix T2 as int
template<class T1>
class A<class T1, int>
{
	// ...
};
```
