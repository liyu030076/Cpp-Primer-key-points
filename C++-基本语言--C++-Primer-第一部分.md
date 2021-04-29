```
1. 程序设计语言 有 共同特征:
1)内置数据类型, int
2)表达式 和 语句
3)变量 : 用于 对所用的对象 命名
4)控制语句: if while for
5)函数

2. 现代程序设计语言 用 2种方式 扩充上述 基本特征集:
1)允许 coder 通过 自定义数据类型 扩展该语言
2)提供 库: 定义 `实用的 数据类型 和 函数`

C++ 中:
1)最重要的特征 是  `类 (class)`
coder 可使用 `类` 自定义数据类型
包含 data function type

2)C++ 标准库 

3. C++ vs. python 等

C++: `静态类型 语言, 编译时 类型检查`
  => 变量使用前 先定义 类型

python: 动态类型语言, 运行时 类型检查
```

#chapter1 快速入门

`1. std::endl` 是 操纵符, 将其 写入流 时, 有 `换行` 效果, 并`刷新` 与设备关联的 `缓冲区(buffer)`, 从而使 用户可 `立即`看到写入到流中的 输出

```
std 是一个 namespace
```

`流:` 指的是 要 从 `某种 IO 设备`上 读入 或 写出 的 `字符序列`

```
2. for

for(初始化语句; 条件; 表达式)
{
	// for 语句体
}

初始化语句 -> 循环执行以下3者:
-> 条件 -> for 语句体 -> 表达式 -> 
```

#chapter2 变量 和 基本类型

```
2.1 基本内置类型

1. 最小存储空间
bool
char  1 Byte
short 2
int   3
long  4

2. 除 bool 外, 整型 可以是 signed / unsigned

int short long 默认 signed 

char 很特殊, 要视编译器而定
=> 用char 时, 最好明确指定 signed / unsigned char
```

`3. 负值 赋给 unsigned` obj
```
结果是 `负值 对 该类型取值个数 求模` 后的值

-1 赋给 unsigned char, 结果为
-1 % 2^8 = -1 % 256 = (-1 + 256) % 256 = 255
```

##2.2 字面值常量

`字面值 : 只能用 它的值 称呼它`
```
常量：其值 不能修改

7种字面值:

1. 整型 ~

(1)3种进制
0     : 8  进制
0x/0X : 16 进制

10   // 10 进制
010  //  8 进制
0x10 // 16 进制

(2)加后缀 U/u L/l 强转 为 unsigned / long
1024UL

2. 浮点型 ~

F/f  单精度
E/e  指数

1E-3F

3. bool 型 ~

true / false

4. 可打印字符~ : char 型

单引号 ' ' 

'a' 
'2' 
'.' 
' ' // blank ( 空格 )

5. 不可 打印(显示) 字符 `用 转义序列 书写`
(1) 转义字符
\n  换行
\r  回车
\t  水平制表符
\\  反斜线
\'  单引号

(2)任何字符 可 表示为 `反斜线 3个8进制数字` 的 
`通用 转义字符`

\0   空字符( null character )
\12  换行
\40  空格

6. 字符串 ~

note: 编译器 自动在 其 末尾 加1个 空字符 '\0'

""             // empty string literal
"hello world!" // simple string literal
"\nA\t[cc]"    // string literal using newline and tab

7. 长字符串字面值

(1)相邻的 仅由 空格 \t \n 分开的 字符串字面值, 
可连接成1个 新字符串字面值 
=> 使多行书写 长字符串 变得简单

std::cout << "a multi-line "
			 "string literal"
		  << std::endl;
//output: a multi-line string literal

(2)`一行的 末尾 加一反斜线, 则 
此行 与 下一行 当作同一行处理`

反斜线后面 不允许 注释 空格, 
后继行行首 空格 \t 都是 字符串字面值的一部分
=>后继行 一般 `不正常缩进`

#include <iostream>

int main()
{
	std::cout << "a multi-line \
string literal"
		      << std::endl;
}
//output: a multi-line string literal
```

##2.3 变量

`1. 变量/对象 : 有名字的、内存中 有类型 的 存储区`

变量 `类型 决定` 变量 `1)内存 大小 和 布局, 2)其上可执行的操作`

```
2. 初始化

(1)2种初始化方式
1)直接初始化: 更灵活 且 效率更高

int val(10);

2)copy 初始化
//1)内置类型
int val = 10; // note: 不是赋值
//2)类类型: 调 copy ctor

对内置类型而言, 两者几乎没区别

(2)初始化 vs. 赋值
创建变量 并 赋初值 vs. `擦除`当前值 并 用新值代替

(3)初始化多个变量
同一定义中, `前面 已定义变量的值 可用于 初始化 后面的变量`

int prev = 10,
	next(prev + 5);

(4)初始化规则
```
`1)内置类型 变量 的初始化`

函数体 `外/内` 定义： `初始化为 0 / 不自动初始化`

```
2)类类型 变量 的初始化: 调相应的 ctor
```

`3. 定义 与 声明`
```
定义: 为 变量 `分配存储空间, 指定初值`

声明: 声明 `类型 和 名字`

定义 也是 声明

用 `extern` 可 `只声明 不定义`
```

`(1)函数外部  extern 声明` 变量时 `有 初始化式`, 则 为 变量 `定义`

```
extern int g_count = 1; // 定义
void f() 
{ 
	// 
}
```

`(2)变量 可 声明多次( 用 extern ), 但 只能 定义一次`
```
// 定义 与 声明 分离
一个文件中 定义, 其他文件中声明
```

`4. 作用域(scope)` of name
```
5种 scope:
global
local
statement : for(int i = 0; i < N; i++) { }
class
namespace

同名时, local 变量 hide(屏蔽) global 变量
```

`5. 首次使用时 定义` 变量 是个好办法

##2.4 const 限定符

`1. const 变量 定义后就不能被修改, 所以 定义时 必须初始化`

```
const int bufSize = 256;
```

`2. const 变量的 2种初始化`

`1)常量表达式 初始化: 编译时计算出其值, 可以用于 常量表达式 场景`
```
#include <iostream>

int main()
{
	const int n = 1 + 2;

	int a[n];

	for (int i = 0; i < n; i++)
	{
		a[i] = i;
		std::cout << a[i] << ' ';
	}
	std::cout << std::endl;
}
```

`2)非常量表达式 初始化, 比如 函数初始化: 运行时 计算出其值, 不能用于 常量表达式场景`

```
#include <iostream>

int get_value()
{
	int x = 1;
	return x + 2;
}

int main()
{
	// get_value() 运行时 才能计算出来 
	//=> n 虽然用 const 声明, 但 无法再 编译时计算出来
	// => n 不是 常量表达式, 不能用于 常量表达式 场景
	const int n = get_value(); 

	// int a[n]; // 编译报错: n 不是常量值

	// n = 5;    // 编译报错: n 为 const, 定义后 不可修改
}
```

`3. global scope 定义` 的 `const 变量` 默认 为 `局部变量`

**原因： 以允许 `用 常量表达式 初始化 的 const  变量` 定义于 `头文件`, 使得 `无论 该 const 变量 何时 使用、在 哪个 源文件 中 使用, 编译器 都能看见 其 初始化式`**

```
否则, 若默认为 `全局变量`, 则 其 `定义 不能放 头文件`, 
假如 `放 file1.cpp`, 则 `file2.cpp 中 使用` 时, 
`编译器 看不到 其 初始化式`, 只能 看到 其 名称(通过 extern)
```

**但是, 对 `用 常量表达式 初始化的 const 变量`, `编译时 已经计算出 常量表达式 的值, 没必要 再浪费存储空间 用 一个 const 变量 去存`, 实践中 直接用 常量表达式 替换 对 这种 const 变量 的使用, 而 大多编译器 正是这么做的**

```
global scope 中 
1)`非 const 变量` 默认 为 `全局变量 且 为 extern` 
2)`const 变量`    默认 为 `局部变量 且 非 extern`
```

`4. 若const 变量 不是用 常量表达式初始化, 比如用 函数, 则 不应该 定义于 头文件`

```
`应 定义于1个源文件 并 初始化, 并 加 extern 声明, 
使其 能被 其他文件 共享`
```

##2.5 引用

`1. 引用 只是 其绑定的对象 的 别名`, 作用于引用上的操作 都作用于 绑定的对象上

`2. 引用 必须初始化, 且 初始化后, 保持绑定到 初始化时 绑定/指向的对象, 不能 再 绑定到 另一对象`

`3. 非 const 引用` 只能绑定到 同类型 `非 const 对象`

`const 引用` 可以绑定到 `1)不同 但 相关类型 的 对象 2)右值 3)const / non-const 对象`

```
  const 引用: 指向    const 对象 的引用
非 const 引用: 指向 非 const 对象 的引用
```

```
//eg1
int a = 10;
const int b = 10;
int& ref1= a; 

// error : nonconst reference to a const object
int& ref2 = b; 

//eg2.
double d = 3.14;
// const 引用 绑定到 不同但相关的类型的对象, 
//且为 non-const 对象
const int& r = d; 
/* 编译器转换为:
double d = 3.14;
int tmp = d;
const int& r = tmp;
*/

//eg3.
const int& r = 10; // const 引用 绑定到 右值
```

##2.6 typedef

```
typedef type synonym; // 别名
```

##2.7 enum 枚举

```
enum 枚举名 { 枚举值(成员)表 };
```

```
应用场景： 为 `某属性` 定义一组` 可选的 值/状态`, 
比如 文件打开的状态 有 `r w a` 3种, 
`记录` 这些状态值 的方法之一是 
```

`使 属性 的 每种状态 与 1个唯一的 整型常量 相关联`, 则 `属性的状态集` 可表示为 `整型 ( 默认为 int ) 常量集`— `enum 枚举`

```
//`枚举变量` 只能放 枚举结构中的 `1个常数`
// => 枚举类型 和 枚举变量 内存大小 都是
//  `1个 整型常数 的大小`

#include <iostream>
int main()
{
	enum open_modes
	{
		input,  // 1)`枚举值 / 枚举成员`: 第1个 默认为 0, 其后 依次 加 1
		output, // 1
		append  // 2
	};

	enum weekday
	{
		weekmid = 1,// 2)枚举值: 可指定, 如 1
		weekend,    // 2
	};

	enum open_modes mode1;
	
	//3) `枚举 obj 赋值` 为 `枚举值`, 无同名 枚举值 时, 可以不用 枚举类型名 限定
	mode1 = input;    
	enum weekday day1;
	day1 = weekday::weekmid;

	std::cout << mode1 << ' ' << day1 << std::endl; // 0 1

	//4)枚举 obj `赋值` 为 该 `枚举类型 强转` 后的 `整型值`
	day1 = (enum weekday)2;

	std::cout << mode1 << ' ' << day1 << std::endl; // 0 2

	//5) 枚举 obj `初始化` 为 同类型 另一 枚举 obj
	enum open_modes mode2 = mode1; 

	std::cout << mode2 << std::endl; // 0

	//6) 枚举值: 是常量表达式(右值), 编译时计算出来, 后面不能被修改
	//weekend = (enum weekday)3; // 编译错误 : = 左边必须为 左值, 而 枚举值 是 右值

	//7) 枚举类型 size = 4
	std::cout << sizeof(weekday) << std::endl;
}
```

##2.8 类类型

1. 从 对外 `接口(interface)` 开始 设计类

```
interface : 供 使用类的 code 用
implement : 接口 使用, 但一般 不对外开放
```

`2. class` / struct 的 member `默认为 private` / public

##2.9 编写 头文件

`1. 头文件` 作用：多文件 中, `连接 名字 的 使用 和 声明`

2. 头文件 一般包含 

```
1)类 的 定义
2)extern 变量 的 声明
3)函数 的 声明

`使用` 这些 `实体` 的文件 要 `包含适当的 头文件`
```

```
正确使用 头文件 的 2个好处:
1)保证所有文件 使用给的实体的 同一声明
2)声明需要修改时, 只改 头文件 即可
```

`3. 头文件 用于 声明 而不是 定义`

**`(1)定义 只能1次, 声明 可以多次`**

**这里的定义指 `变量(object) / 函数 的 定义`, 因为只有 这2种定义(不包含 模板定义), 编译器 才会 `分配 存储空间(data / code section)`**
```
而 `同一 object / funtion 的 存储空间 分配多次` 时, 
编译报错
```

`(2)头文件中 不应该含` 有 `non-const 变量 / non-inline 函数 的 定义`, 以`避免` 头文件 包含于 多个源文件时 `引起 变量 / 函数 重定义`

`(3)定义 不能放 头文件 的 3个例外`

```
1)inline 函数 定义
编译器优化, 只定义1次

2)const 变量 定义
const 变量 编译时 就计算出其值
=> `const 变量 默认为 局部变量, 对 其他文件不可见`
=> 避免了 多次 定义
```

`3)类 定义`

```
类定义 放 头文件:
```
`1>不会引起 同一 object / function 存储空间 的 多次分配`

```
类定义 中:
1)non-static/non-inline/non-const 成员 只有声明
2)static / inline / const 成员 有定义, 
但 这3种限定符 限定了 这3种 `实体` 只定义1次
```
`2>使得 源文件中 code` 可以 `定义/使用 类的 object `

`(4)模板 则不同`
```
Inclusion Compilation Model 中 
`头文件 要 #include 模板定义 (的 源文件)` =>
```
`实例化 (编译阶段) 时, 编译器 必须能访问 模板定义 的源代码`

```
`模板 使用 时/处`, 才 引起 `模板 实例化`, 
实例化 出的 版本(类/函数) 才 分配存储空间
=> 这 就到了 `#include 该 头文件` 的各 `源文件` 中了
=> 模板定义 `并不引起 object/function 定义多次`
=> 模板定义 `并不引起 (data/code)存储空间 的多次分配`
```

`4. 预处理 之 #include, 在 编译前`

```
头文件 经常要 包含 other 头文件

//a.h 
include "b.h"

//c.cpp 
include "a.h"
include "b.h"

=>Problem: c.cpp 2次 #include "b.h" 

=> 要 保证 多次包含同一头文件时, 
`不会引起 多次处理 头文件内容`

solution:用 `头文件保护符(header guard)` 避免多重包含

// a.h
#ifndef A_H
#define A_H
// class definition etc.
#endif
```

#chapter3 标准库类型: string / vector


`string / vector : 定义了 大小可变的 字符串 / 集合`, 两者往往`配套 迭代器, 以 访问 string 中的 字符 / vector 中的 元素`

##3.1 namespace 的 using 声明

```
// 形式
using namespace::name;

// eg.
using std::string;
```

`头文件` 中 必须用 `完全限定的 标准库 名字`, 如 std::string
```
因为 头文件内容 会放到 #include 它的 其他文件 中
```

##3.2 string

支持 `变长字符串`

```
标准库 进行 内存管理(存储字符) & 操作管理
```

string 对象 的 

`1. 定义 和 初始化`

```
// 3 种 ctor
//1) defalut
string s1;

//2)copy : other string obj / string 字面值
string s2(s1);
string s3("hello");

//3) n 个 字符
string s4(n, 'c');
```

`2. 读 写`

`(1)读 1个 string: 空白符(空格 ' ', 换行 \n, 制表符 \t) 隔开的 被认为是 1个字符串`
```
std::cin >> string_obj
```
`1)忽略 开头的 空白符`
`2)读 >=0 个字符` 
```
至 下一个 空白符 则终止读入, 末尾 空白符 被丢弃, 
前面 >=0 个字符 被存入 string_obj
```

`(2)读 1行 string: 以 newline( \n) 结尾的 被认为是 1个字符串`
`1)不忽略 开头的 换行符`
`2)每遇1个换行符, 丢弃该换行符, 把 前面的 字符存到 string_obj`
```
// std::getline in <string>
istream& getline (istream& is, string& str);
```

```
// (1)一个个 string 读: 空白符 隔开的 被认为是 1个字符串
#include <iostream>
#include <string>

int main()
{
	std::string s;

	//read until end-of-file, writing each word end with (空格 换行 Tab)
	while (std::cin >> s)
		std::cout << s << std::endl;
}
/*
input:
' '(空格)\t(Tab)\n(用回车)hello' 'world\n(用回车)Ctrl+Z+回车(即 EOF, windows下)

output:
第1行:第1个 换行产生的显示, 第1个字符串时 `空字符串 不占1行`
第2行:光标移到 第 2行再输 hello
第3行:hello 
第4行:world
*/

// 2)一行行 字符串读: 1行(以 newline / \n 结尾的)被认为是 1个字符串
#include <iostream>
#include <string>   // getline

int main()
{
	std::string s;

	//read a line a time, until end-of-file
	while ( std::getline(std::cin, s) )
		std::cout << s << std::endl;
}
/*
input:
' '(空格)\t(Tab)\n(用回车)hello' 'world\n(用回车)Ctrl+Z+回车(即 EOF, windows下)

output:
第1行:第1个 换行产生的显示
第2行: 1个空格 + 1个 Tab 组成的 字符串 占第1行, 虽然啥也看不见
第3行: 光标移到 第3行再输 hello
第4行:hello world
*/
```

`3. 操作`

`string::size_type 类型`
```
string的 配套类型, 使 string 的使用 与 机器无关

若 用 int 型, 2Byte 机器上, 最大表示 
`含 2^16 / 2 - 1 个字符 的 string object`,
则 `能容纳 1个文件 的 string object` 会轻易超过该值
```

```
(1)s.empty() : s 为空串时, 返回 true; 否则, false

(2)大小 下标
s.size() : 返回 s 中 有效字符数, 不含编译器自动加的 '\0'
		   返回值为 string::size_type 类型
s[n]     : index 用 string::size_type 类型

(3)赋值: 右操作数 可以 是 other string / string 字面值
一般为3步:
1)释放 作操作数 内存 
2)分配足够 存右操作数的 内存空间
3)copy 右操作数 到 新内存空间
s1 = s2

(4) + / concatenation(连接)  +=
s1 + s2
s1 += s2

(5)比较 string 每个 字符串的 字符, 为真 返回 1

== != < <= > >=

字典排序 策略:
1)若 短字符串 完全匹配 长字符串 前面部分, 则 短串小
2)否则, 比较 第1个 不匹配的字符
```

```
#include <iostream>
#include <string>
using std::string;

int main()
{
	//(1)s.empty()
	string s;
	if (s.empty())
		std::cout << "empty string\n";

	//(2)大小 下标
	s = "hello";
	for (std::string::size_type i = 0; i != s.size(); i++)
	{
		s[i] = '*';
		std::cout << s[i];
	}
	std::cout << std::endl;

	//(3) 赋值
	string s1, s2 = " world"; // 初始化, 不是赋值
	s1 = s2;
	s1 = "hello"; 

	//(4) + / concatenation(连接) +=
	string s3 = s1 + s2;
	string s4 = s1 + " world";
	string s5 = "hello" + s2;
	string s6 = s1 + " " + "world";
	// string s6;
	// string tmp = s1 + " "; // 结果仍为 string object
	// s6 = tmp + "world"'

	// + 的 2个操作数 至少1个是 string obj(左值)
	// "hello" + " " 编译报错: 不能 加 2个指针
	//string s7 = "hello" + " " + s2; 

	s1 += s2;       // s1 = s1 + s2;
	s1 += " hello"; // s1 = s1 + "hello";

	//(5)比较
	string s8 = "hello";
	string s9 = "hello world";
	
	std::cout << (s8 < s9) << std::endl;  // 1
	if(s8 == "hello world")
		std::cout << " == " << std::endl;
	else
		std::cout << "!= " << std::endl;
}
```

`4. 字符处理`

```
<cctype>
isalpha(c) 等
```

```
#include <iostream>
#include <cctype>
int main()
{
	std::string s("hello");

	if ( ! isdigit(s[0]) )
		std::cout << "yes\n";
	if ( isalpha(s[0]) )
		std::cout << "yes\n";
}
```

##3.3 vector

`vector 是 同一种 类型 的 对象 的 集合, 称为 容器`

```
vector 是一个 类模板
#inlcude <vector>
```

**1. 定义 和 初始化: 存 元素的 `copy`**

```
//(1)default ctor: 空 vector object
vector<T> v1;

//(2)copy ctor
vector<T> v2(v1);

//(3)n 个 元素, 给定 初始化式
vector<T> v3(n, i);

//(4)n 个 元素, 未给定 初始化式: `值初始化`
//标准库 根据 vector 元素类型, 自行提供初始化式,进行 值初始化

// 1)内置类型: 初始化式 为 0
std::vector<int> v_int(5); //each initialized to 0

// 2)含 default ctor 的 类类型, 初始化式 为 default ctor
std::vector<string> v_str(5); // each initialized to empty string

// 3)不含 default ctor, 标准库 自行给初始值, 
// undefined 值, 不推荐
```

```
vector 对象 动态增长: 可以在运行时 高效地添加对象

虽然可以对给定元素个数的  vector 对象 预先分配内存,
但更有效的方法是(已知元素值时):
先初始化一个 空 vector 对象, 再 动态地增加元素
```

2. 操作
```
vector<T>::size_type 类型 // vector 的配套类型

使用时 指定 T, eg.
vector<int>::size_type
```


```
// 与 string 有部分操作 类似
(1)v.empty()

(2)大小 下标
v.size() : 返回 v 中 元素个数 >= 0
		   返回值为 vector::size_type 类型
v[n]     : index 用 string::size_type 类型

(3)赋值: 右操作数 可为 other vector 对象 / 元素值
v1 = v2

(4)比较 string 每个 字符串的 字符, 为真 返回 1

== != < <= > >=

保持这些操作符的 `惯有含义`

(5)v.push_back(t)
```

```
vector下标操作 的 4点注意:
(1)下标操作 不添加元素
`只能对 确知存在的元素 进行 下标操作`

(2)循环变量 用 vector<T>::size_type类型

(3)循环变量比较 用 !=

(4)v.size() 直接调用, 不用先保存其返回值
```
##3.4 迭代器 简介

```
1. 下标 操作符 / 迭代器:
少数 / 所有 容器类 支持
=> 现代 C++ 更倾向于 用 迭代器
```

`2. 术语 iterator 有 2层含义:
1)类型名 为 iterator
2)概念上 的 iterator(迭代器)`

```
标准库中 `各 容器类`
1)都定义了一个 `名为 iterator 的 类型`
2)该类型 支持 `(概念上的)迭代器 的 各种操作`
```

```
各 容器类都定义了 2个 迭代器类型,
iterator / const_oterator : 
1)`访问 / 只读` 容器内元素
2)解引用, 得 元素的 `非 const 引用 / const 引用`
```

`3. begin end(off-the-end iterator)`


```
4. 常用操作

解引用 *

比较 == !=

前置自增++ : 所有容器类都有
```

#chapter4 数组 和 指针

##4.1 数组

```
虽然 C++ 中 倾向于使用 vector, 但 当 性能测试表明 
vector 无法达到性能要求 而 数组可以时, 用数组
```

1. 数组维度 必须用 `编译时已经能计算出来 的 整型量`
```
可以是 
(1)define 预编译/预处理 进行 `简单文本替换`的 宏名/标识符
1)出现 标识符/宏名 的地方 全替换为 其后的 常量(text)
=>
2)编译器 看不到 标识符, 也就 不存在 为标识符分配内存的问题

(2)常量表达式: 编译时 计算其值
1)字面值常量
2)枚举常量
3)用 常量表达式 初始化的 const 变量

note: const 变量 若用 函数初始化, 运行时才能计算出其值
```

```
2. 数组 隐式/默认 初始化
1)内置类型 元素 & 函数体 内/外 定义 
=> 不初始化(脏值) / 初始化为 0

2)类类型 元素 => 调 default ctor 初始化
```

```
3. 数组操作

数组下标的 正确类型为 size_t

(1)size_t defined in 7种头文件中
<stddef.h>
<stdio.h>
<stdlib.h>
<string.h>a
<time.h>
<uchar.h>
<wchar.h>

(2)size_t 
是 sizeof 操作符 的结果
是 unsigned integer
可存储 任何类型的 object 的 理论 最大 size
常用于 array indexing 和 loop counting

(3) vs. ptrdiff_t 
signed integer type of the result of subtracting two pointers

defined in <cstddef>

```
##4.2 指针

```
1. 通过指针赋值 vs. 赋值给指针
```   

![](http://upload-images.jianshu.io/upload_images/20172887-944f6858a07b18a2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1080/q/50)


```
2. 指针 vs. 引用

2个重要区别
(1)引用一旦初始化, 就始终指向 其绑定的 对象
=> 引用 定义时 必须初始化 为 某一特定对象

而 指针 初始化之后, 可以指向 other object

(2)赋值 差异
赋值 给 引用 修改的是 引用所绑定的对象的值,
    而不是 使引用绑定到 other 对象

赋值 给 指针 修改的是 指针的值, 即 使指针重新 指向 somewhere
```

```
3. 指针 是 数组的迭代器

#include <iostream> // 包含了 size_t
int main()
{
	const size_t sz = 3;
	int a[sz] = {0, 1, 2};
	
	for (int* pbegin = a, *pend = a + sz;
			  pbegin != pend; ++pbegin)
		std::cout << *pbegin << ' ';
}
```

`4. 指针 const typedef`

```
(1)const 对象 的指针: const 在 * 左
const int *p;
int const *p; // <=>

(2)const 指针: const 在 * 右
int *const  p;

(3)const 对象 的 const 指针
const int *const p;
```

```
(4)指针 const typedef

// typedef isn't text-replacement
typedef std::string *pstring; // <=> typedef std::string* pstring;
std::string s;
const pstring cp_str = &s; 
```

**`const 放在 类型 pstring 后`, pstring const cp_str = &s; 然后 `从右往左` 看, 就能清楚地知道 `变量 cp_str 的类型 是 std::string* const, 即 指向 string 对象的 const 指针`**

```
不幸的是, 大多数 coder 阅读 C++ 程序时, 
习惯看到 const 放在 类型 前,
所以 只好建议编程时 把 const 放在 类型 前
```

```
#include <iostream> 
#include <string>

int main()
{
	// <=> typedef std::string* pstring;
	typedef std::string *pstring; 
    
	std::string s = "hello";
	// 1)const 指针 必须初始化
	const pstring cp_str1 = &s; 

	pstring const cp_str2 = &s; 

	std::string* const cp_str3 = &s;

	// 2)const char * 不能用于初始化 std::string * const
	// std::string* const cp_str4 = "hello"; 
}
```

##4.3 C 风格 字符串: 以 空字符 null ('\0')结尾的 字符数组

`1. 使用`

`通过 const char* 类型的 指针 操纵`
遍历时 指针 每次测试并自增1, 直到 null

```
const char *p = "hello";
while(*p)
{
    do sth to *p
    ++p;
}
```

`2. C风格字符串的 库函数: 不检查其 字符串参数`

`传入的 指针 非0, 相应的 字符串 以 null 结尾`

```
#include <cstring> // string.h 的 C++ 版本

有些函数会 修改 传递给它的 字符串, 假定 
所修改的字符串 有 足够空间 接收 新生成的字符
=> coder/caller 必须确保 字符串 足够大


strlen(s) : 有效长度, 不含 '\0'
strcmp(s1, s2): 前 > 后 时, 返回 正数

strcat(s1, s2): 去 s1 '\0' -> s2 接在其后-> 返回 s1
strcpy(s1, s2): copy s2 to s1 -> 返回s1

//strn: 取 右 字符串 前 n 个字符, 
// 这n个字符 要包含 '\0', 最后生成的 s1 才能是 C风格字符串
strncat(s1, s2): 
strncat(s1, s2): 
```

```
// str / strn / string 
//能计算出 字符串 size, strn 更安全
// 对 大多应用, string 更安全, 效率更高

const char *p1 = "hello";
const char *p2 = "world!";

char s[5 + 6 + 2]; // 中间加1个空格, s1原 末尾为 '\0', 
//连接时 先去掉 最后又添上 s2 的末尾, 实际上 
//最终只要比2 个字符串 有效字符数 之和 多 1个 位置就行
//(1)
strcpy(s, p1);
strcat(s, " ");
strcat(s, p2);

//(2)
strcpy(s, p1, 6);
strcat(s, " ", 2);
strcat(s, p2, 7);

//(3)
string str = p1;
str += " ";
str += p2;
```

`3. 动态数组`

```
数组变量3限制:
1)长度固定
2)编译时 必须知道其 长度
3)只能在 定义它的语句内 存在

编译时 无法知道 数组维度, 为了让 
运行时 再 动态地 根据所需长度 创建数组, 
引出 动态数组

C/C++ 中 用 malloc 和 free / new 和 delete 来 在 堆 heap 上 
为动态分配的对象 申请 和 释放 存储空间

(1)定义和初始化

定义时 元素类型为 内置类型 / 类类型 时, 
无初始化 / 调 类的 default ctor 初始化

int *p = new int[3]; 未初始化//
std::string *ps = new std::string[3];


内置类型定义 末尾加 空圆括号, 则 按 `值初始化`
int *p = new int[3]();  // 值初始化 : 全0

(2)动态数组 维度可为0
size_t n = get_size();

int *p = new int[n];
for(int *q = p; q != p + n; ++q)
    // process the array

(3)释放
delete [] p;
```

```
相比于 C 风格字符串, 用 string 的 code 更短、更易读、更安全

const char *p = "a long literal string";
const size_t len = strlen(p);
for(size_t i = 0; i != 1000; ++i)
{
char * p2 = new char[len + 1]; 
strcpy(p2, p);
if( strcmp(p2, p) )
    ; // do sth
delete [] p2;
}


string str("a long literal string");
for(size_t i = 0; i != 1000; ++i)
{
string str2 = str;
if(str != str2) 
    ;// do sth
}// str2 is automatically freed

4. 混合使用 std::string 与 C风格字符串
```
`C风格字符串 与 字符串字面值 类型相同: 都是 const char *, 且 都以 '\0' 结尾`

```
=>用 字符串字面值 的地方都可以用 C风格字符串

// (1)风格字符串 -> std::string 对象
1)初始化 / 赋值 std::string 对象

2)作 std::string + 的一个 操作数

// (2)std::string 对象 -> C风格字符串
// string类 的 成员函数 c_str() 返回 
// 指向 与 string 管理的字符串 内容相同的 
// const char 型 数组 的首地址

1)const char *p = str.c_str();
```

##4.4 多维数组

```
1. 初始化

int a[2][3] = 
	{ 
		{1, 2, 3},
		{4, 5, 5}
	};
```

2. 多维数组 的 指针 是 `指向 第1个内层数组的指针`

**(1)`int (*p)[3]: 由内而外 看 指针声明, *p 是 int[3]类型 => p 是 int[3] * 型`, 即 指向 含3个元素的数组 的 指针**


```
(2)用 typedef 简化 多维数组 指针
`N 维数组 定义前 加 typedef`
=>
`原 数组名` 就变成 `N 维数组 类型`, 
`该名称 定义的 指针` 为 `指向 N 维数组的 指针`
```

```
int a[2][3];
int (*p_array)[3] = a; 

// 而不是 typedef int[3] int_array;
typedef int int_array[3]; 
int_array* p_a = a;  
```
**int_array 是 int[3] 型, `p_array / p_a 是 int[3] * 型 指针.
对 int[3] * 型指针 解引用, 得 int[3]`, 即 有 3个 int 型元素 的数组, `使用 该数组`, 系统会 `自动将其转换为 指向 其第1个元素的 指针`**

```
//e.g 
#include <iostream> 
int main()
{
	int a[2][3] =
	{
		{1, 2, 3},
		{4, 5, 6}
	};

	// p_array points to an array of 3 ints
	int (*p_array)[3] = a;     
	
	// a[1] is an array of 3 ints
	p_array = &a[1]; // a + 1; 

	std::cout << *(*p_array + 2) << std::endl; //6

	// 1)int_array 是 int[3] 型
	typedef int int_array[3];
	// 2)p_a 是 int[3] * 型 指针
	// int_array* p_a = a;   

	// 3)对 p, 即 `int[3] * 型指针 解引用, 得 int[3]`, 使用 该数组,
	//系统会 `自动将其转换为 指向 第1个元素的 指针`
	for (int_array *p_a = a; p_a != a + 2; ++p_a)
		for (int *q = *p_a; q != *p_a + 3; ++q)
			std::cout << *q << std::endl;
}
```

#chapter5 表达式

```
(1)操作数为 `内置类型` 时, C++ 定义了 `操作符` 的含义

(2)操作数为 `类类型` 时, C++ 支持 `操作符 重载`, 
让 用户自定义 操作符的含义
```

#5.1 基础

`1. expression`(表达式) 由 >=1 个 `operand`(操作数) 通过 >=0 个 `operator`(操作符) 组合而成
```
=> 最简单的 表达式: 1个 字面值常量 或 变量
```
每个表达式 都会产生1个 `result`

`2. 理解 表达式` 就是理解 `操作符` 的 `优先级、结合性` 和 `操作数` 的 `求值顺序`

```
结论: 
1)同优先级 时, 看结合性; 
2)`C/C++ 没有规定 求值顺序
( 左/右 操作数 到底哪个先执行), 
这依赖于 编译器实现` 
=> 不要写 `取决于 计算顺序 的 模棱两可` 的代码

本章 具体可参考 另一篇文章, 链接如下:
```

https://www.jianshu.com/p/921b4e0fb712

`3. 溢出`

```
// 假定 short 在 当前机器上 为 2 Byte = 16 bit
=> 范围 -2^(16 - 1) ~ 2^(16 - 1) - 1 = -32768 ~ 32767

//(1)
short val = 32767;
short i = 1;
val += i; // -32768

std::cout << val << std::endl;

(2)负数 2 进制 转 10 进制: 补码
-1 取反 (<= 是 10机制 转 2进制: j绝对值的2进制 取反 +1)

(3)上述 溢出 计算过程
32767 + 1 = ？

1)笨办法算
011...1
+     1
100...0 => 负数 2机制

-     1
011...1 = 32768 -> 添 负号 -> -32768

2)用 结论:
32767 + 1 = 32768 不属于
[-2^(w - 1), 2^(w - 1)) = [-32768, 32767)
=> - 2^w = - 2*32768 = -32768 才能落到 该区间
=> -32768 为 + 的 补码结果
结论见 如下链接
```
https://www.jianshu.com/p/27f082c1ddb7

`4. bool 类型 可转换为算术类型 — bool 值 true / false 用 1 / 0 表示`

`5. 复合赋值: 左操作数 只计算了 1 次; 相应 长表达式: 左操作数 计算了2次`

```
note: i += 2 比 i = i + 2 效率高

i += 2    // `把 2 加到 i`
i = i + 2 //  取 i, 加上 2, 结果放回 i
```

`6. 后置 自增 操作符 只有在 必须时才使用`

```
前置 自增: 
1) 加 1 
2) 返回 加 1 后的 result => 做的工作更少 

后置 自增:
1)保存原值
2)加 1
3)返回 原值 的 copy
```

`7. *iter++ : 简洁即是美`

```
`解引用 与 自增` 都是 单目运算符 => 同级
=> 看 结合性: 单目运算符 自右向左 结合
=> 等价于 *(iter++)

// 优于如下
*iter; // do sth to *iter
++iter;
```

`8. 箭头 -> 可以用于避免忘记 圆括号 ()`
```
// p = &some_obj;

// note: *p.mem_func() <=> *( p.mem_func() )
(*p).mem_func(); // (*p) 的 圆括号 不能少

p->mem_func();
```

`9. sizeof ( array ) == sizeof( array_element_type ) * array_size`


##5.2 new / delete 表达式

`1. 创建 动态对象 时的 3种初始化`
```
(1)默认初始化
1)内置类型 : 函数 内/外 => 未初始化 / 初始化为 0
2)类类型   : 用 default ctor 初始化

int *p = new int;        // 若在 函数内, 则 uninitialized
string *ps = new string; // initialized to empty string

(2)值初始化
`类型名后 带 圆括号 ()`
1)内置类型 : 初始化为 0
2)类类型   : 还是用 default ctor 初始化

int *p = new int();      // initialized to 0
string *ps = new string; // initialized to empty string

(3)显式指定初始化式
int *p = new int(10);            // 用 指定初始化式 初始化: initialized to10
string *ps = new string("hello");// 用 指定 ctor 初始化

note: int x(); // 为 函数声明
```

`2. delete 释放指针 指向的 空间`

`(1)可以安全地 delete 2种 指针: 1)new 动态分配的内存 2)0 指针`

`(2)delete 指针 p 之后, 指针p 变成 悬垂指针(dangling pointer)` — 指向 `曾经存放对象的 内存, 但 该对象 已经不存在了`
=> `最佳实践: delete 指针 之后, 立即将 指针 置 0`, 以 清楚地表明 `指针不再指向任何对象, 使得后续用该指针前 一检测 就知道 该指针为 0`

`3. 动态内存分配时, 3种常见错误`

(1)delete 动态分配的内存时 失败, 该 `内存无法返还给 自由存储区 => 内存泄漏(memory leak)`
```
内存泄漏 很难跟踪和修正, 一般要等程序运行一段时间后, 
耗尽了所有内存空间, 内存泄漏才会显露常量
```

`(2)读写 已 deleted 指针`
```
solution: delete 指针 之后, 立即将 指针 置 0, 
以 清楚地表明 `指针不再指向任何对象, 
使得后续用该指针前 一检测 就知道 该指针为 0`
```
(3)对同一块内存 `delete 2 次: 第2次 delete 时, 自由存储区 可能被破坏` 

`4. 动态 const 对象`

```
//allocate
const int *p = new const int(10); // new 时 必须初始化

//deallocate
delete p;
```

`5. 各种指针`
```
(1)0 / NULL: 空指针, `未指向 任何对象`
    NULL 是 宏定义: #define NULL （（void *） 0）
    不用严格 区分 0 与 NULL, 认为 2者 相同即可

(2)void* : 可指向任意类型, 没有相关联的类型信息
    malloc 返回的即 void*

(3)野指针: 指向 `垃圾内存` 的 指针, 有 3 种
	1)未 初始化
	2)delete / free 后 未 置为 0
	3)操作超越 变量范围 
	
(4)悬垂指针：指向 `曾经存放对象的 内存`, 
但 该对象 已经不存在了.
	delete / free 后 的指针 即 悬垂指针

=> 悬垂指针 只是 野指针 的1种

note: `指针 2种隐式 转换`:
0(整型常量)    -> 任意类型的指针
任意类型的指针 -> void*
```

```
//eg. 0 / void* / signed integer 转换 unsigned integer 
#include <iostream> 
int main()
{
	//(1) 0(整型常量)->任意类型的指针
	short* p = 0; 

	short a = -32768; 
	p = &a;

	//(2)任意类型的指针 -> void*
	void* void_p = p;

	//(3)void* -> unsigned short* -> 解引用
	//<=> 对 a 求 T2U_w : 
	// 1)(unsigned )short 2 Byte: 字长 w = 16
	// => 补码 T_w 范围 [-2^(w-1), 2^(w-1) ) = [-32768, 32767)
	// 2)T_w = -32768 = -2^15 不属于 `转换后 result 正常(不溢出)的范围`:
	// [0, 2^(w - 1) ) = [0, 32768)
	// => T2U_w = T_w + 2^w = -2^15 + 2^16 = 2^15 = 32768
	unsigned short b = *( (unsigned short*) void_p );

	std::cout << b << std::endl;
}
```

##5.3 类型转换

```
1. 隐式类型转换 何时发生

`(1)混合类型 表达式` 中, 操作数 转换为 相同类型
`(2)条件表达式` 转换为 bool 类型: if / while ( condition_expression )
`(3)用 表达式 初始化/赋值 某变量` 时, 
表达式 转换为 该变量的类型

2. 算数转换, 见
```
https://www.jianshu.com/p/27f082c1ddb7

```
3. 其他 隐式转换
 
(1)in most cases, `数组 使用时 自动转换为 指向首元素的 指针.`
exceptions:
1)数组 作 & 的 操作数
2)数组 作 sizeof 的 操作数
3)用数组 对 数组的引用 初始化 时 chapter 7.2

note:
1)0 可以 转换为 任意类型的指针
2)任意类型的指针 可以 转换为 void*

int a[3];
int *p = a; // a converted to pointer to first elem

(2)算数值 和 指针值 都可以 转换为 bool 型
0 / 非0 -> false / true

if/while(p) // if/while( p != 0 )
if/while(*p)

(3)算术类 <-> bool

算术 -> bool: 非 0 / 0 -> true / false
bool -> 算术: true / false -> 1 / 0

(4)枚举对象 / 枚举值 -> int / unsigned int / long / unsigned long 
由机器决定提升为哪个

(5)const 转换: const 引用 / 指针 可以 指向 non-const 对象

1)non-const obj 初始化 reference of const obj: 
non-const obj -> const obj
2)non-const obj 的 地址/指针 -> const 类型的指针

int i = 0;
const int &j = i;
const int *p = &i;

(6) istream 类型 转换为 bool 类型
string s;
while( cin >> s)

检验流的状态
读 cin 成功 / 失败(比如 已 读到 文件尾), 
cin 转换为 true / false
```

`4. 强转换 / 显式类型转换`

```
(1)旧式 强转 : 以 兼容 C

(type) expr; // C-style 
type (expr); // Function-style

(2)static_cast
编译器 隐式转换 都可由 static_cast 显式完成

(3)dynamic_cast : 
支持 运行时 识别 指针/引用 指向的对象
```

##5.4 void 与 void* 的 应用

`1. Problem: delete void* 引起 内存泄漏`
```
class FrameInfo
{
private:
    char* frame;
public:
    FrameInfo()
	{
        frame = new char[FRAME_WIDTH * FRAME_HEIGHT];
    }
    ~FrameInfo()
	{
        if(frame)
			delete[] frame;
    }
};

FrameInfo* pFrameInfo = new FrameInfo();

// void* 传入的 实参为 pFrameInfo
void func(int par1, char par2, void* voidPointer);

void callBack(void* voidPointer){
    //...
	
    delete voidPointer;
}
```
=> `delete void* 只是 释放了 该 void* 指针, 
   并没有 delete 动态内存 pFrameInfo`
=> 动态对象 pFrameInfo 并没有 释放
`=> 其内部的 动态内存 frame 也没有 释放`
=> 内存泄漏

`solution:  不能 delete void*, 要强转为 相应的 指针类型`

```
void callBack(void* voidPointer){
    //...
	
    delete (FrameInfo *)voidPointer;
}
```
`2. void 的作用`
```
void: 不确定类型 
=> 所占内存不确定
=> 不能声明 object
=> void 2 大作用:
1)限制 函数不能有返回值 
2)限制 函数不接受参数 
```

`3. void* : 通用 指针 / 可指向 任意类型 的指针`

`void* 指针指向 某具体类型的 obj:`
`(1)实际是 屏蔽了类型信息, 而 直接暴露 内存`
`(2)但 使用时 coder 心里要清楚 被屏蔽的类型信息`

基于`这一特性`, 

`(3)许多 操作内存的函数 中 void*, 使 C 可以实现 类似 C++ 的 泛型编程`

```
void* memcpy(void* dest, const void* src, size_t len);
void* memset(void* buffer, int c, size_t num);

任意类型的指针 都可以传入 memcpy / memset,
这也体现了 内存操作函数 的意义

(4)该特性 带来的 限制:

1)void* 指针 `赋给` 其他类型指针, `要先强转`

int a = 10;
void* pvoid = &a;
int* pInt = (int*) pvoid;

2)`delete void*` 指向的动态内存 时, `要先强转`
delete pvoid; 			  //只清空了 指针 pvoid
delete (FrameInfo*) pvoid;//正确析构 pvoid 指向的 obj

note: void* 可以 free

3)void* 不可解引用

(5)void* vs. template
void* 可作为一种轻量化的 模板编程, 区别:

1)问题暴露的时间上
template / void* : 编译时/(延迟到)运行时 暴露问题

2)效率上: 
1] 一般, 可能时 使用 void* 会提高 编译速度 
2] C++ STL函数 sort 以及 C 标准函数 qsort
sort 效率 高于 qsort, 虽然两者都基于相似的快速排序算法

void qsort(void *base, int nelem, int width, 
		   int (*fcmp)(const void *,const void *) );
```	   
		   
##5.5 C++ 5种 内存布局: `堆、栈、自由存储区、全局/静态存储区、常量存储区`

```
1. 自由存储区 Free Store:

(1)由 new/delete allocated/freed

(2)allocate -> construct -> destory -> deallocate
construct -> destory:  Object lifetime
Object lifetime 之外, 可以用 void* access 和 操纵 the storage

2. 堆 Heap

(1)由 malloc/free and their variants  allocated/freed 

(2)global new / delete  可能 用 malloc / free 实现
但 堆 和 自由存储区 不同: 
memory allocated in one area 
cannot be safely deallocated in the other
含义 待后续弄清楚!!!

(3)堆上分配的内存 通过 
`placement-new construction / explicit destruction`
用于 类类型 时, 与 自由存储区 类似

3. 栈

4. global / static 存储区：存 global / static 变量, 
分 初始化区 / 未初始化区, 程序结束后 释放 该空间

5. 常量存储区: 只能读不能写, 
const 全局/局部 变量 存储在 常量区/栈区

//------
1. Free Store:
The free store is one of the two dynamic memory areas, 
`allocated/freed by new/delete`. 
Object lifetime can be less than the time the storage is allocated; 
that is, free store objects can have memory allocated 
without being immediately initialized, 
and can be destroyed without the memory being immediately deallocated. 

During the period when the storage is allocated 
but outside the object's lifetime, 
the `storage may be accessed and manipulated through a void*` 
but none of the proto-object's 
nonstatic members or member functions 
may be accessed, have their addresses taken, or be otherwise manipulated.

2. Heap
The heap is the other dynamic memory area, 
allocated/freed by `malloc/free and their variants`. 

Note that while the default global new and delete 
might be implemented in terms of malloc and free by a particular compiler, 
the heap is not the same as free store and 
`memory allocated in one area cannot be safely deallocated in the other`. 

Memory allocated from the heap 
can be used for objects of class type by 
`placement-new construction and explicit destruction`. 
If so used, the notes about 
free store object lifetime apply `similarly` here.
```

#chapter6 语句

`1. 复合语句, 即 block(块) : 标识了 1个 作用域, { }, 不用以 ; 结束`

`2. 语句作用域 : for / while 语句中`

`(1)语句体 本身就是一个 块语句`
`(2)变量 只在 语句中 visible`

`3. switch语句`
```
(1)每个 case 后 
1] 不用 { }
2] 至少1个语句, 哪怕是 空语句

(2)哪怕没有要执行的 default 语句, 定义 default 也有意义:
以 告诉 reader, 这种情况 已 考虑到了, 只是没什么要执行的
```
`(3)switch 中 变量定义 只能在 最后1个 case 标号后 或 default 标号后`
```
2个 case 之间 定义变量 的结果: 
若 switch 从后续 case 开始执行, 
则该 `变量 未定义 就使用了`
```

`4. break语句: 结束 最近的 循环 / switch`

`5. continue: 提前结束 最近的循环 的 本次迭代`

`6. try-catch`

```
try
{
	program_statement
} 
cstch(exception-specifier)
{
	handler-statement
}


try
{
	do_sth();
} 
cstch(runtime_err err)
{
	std::cout << err.what() << std::endl;
}
```
#chapter7 函数
```
1. 函数 定义
2. 函数声明
3. local object

4. 给 函数传参 : 参数传递
5. 从 函数 返回值 : return

//--- 3 类特殊函数
6. inline(内联) 函数
7. 类的 成员函数
8. 重载函数

9. 函数指针
```

```
函数 与 操作符 区别:
同:
(1)都是 计算过程
(2)都可 重载

异:
函数 操作数 数量无限制
```

##7.1 function definition

```
1. parameter ( 形参 ): function 的 operand ( 操作数 )

2. function body ( 函数体 ): 

(1)是1个 `block(语句块)` : 通常 包含在一对 { } 中
(2)是1个 `scope(作用域)`
(3)其中 定义的 变量 称为 `local variable(局部变量):`
1)相对于 定义它们的 函数 而言 是 局部的
2)名字只在 函数的 scope 可见
```
`3. 函数调用`

```
`(1)实现` : 用 `call operator(调用操作符)` 

call operator 的 操作数：
1)`function name`
2)`argument ( 实参 )`

call operator 的 result type： `return type` of function


`(2)过程 : 2件事`
```
1)用 `argument 初始化 parameter`
2)将 `控制权 转移` 给 called function( 被调函数)：
`calling function ( 主调函数 ) 被 挂起, called function 开始执行`

`(3)函数 的 运行`
`1)以 形参的初始化 开始`
`2)执行到 } / return 结束`
```
return 执行后, 被挂起的 calling function 
`在 调用处 恢复执行`
```

```
4. parameter / argument ( para / arg )
parameter : 为函数提供 已命名的 局部存储空间
argument : 是1个 expression (表达式)

区别: 
parameter 在 parameter list 中 定义, 
由 函数调用 时 传递给函数的 argument 初始化
```

`5. return type of function`
`(1)函数 必须指定 return type`
`(2)return type 不能是 另一函数 或 内置数组`
```
, 但可以是 函数指针 或 数组指针

`6. parameter list`

函数调用 时, `编译器 检查 argument 到 parameter 的 type (convertion)`
```


##7.2 function declaration

```
`函数 定义/声明` 与 `变量 定义/声明` 有很多相似之处
```

```
1. 函数 调用前 必须 先声明

2. 函数 `定义 和 声明 可 分离`, 
只能定义 1次, 可声明多次
```

`3. function prototype ( 函数 原型 )`

`(1)指 3 元素: return type / function name / parameter list` 
```
parameter list 必须 包含 parameter type, 
不必包含 parameter name
```
`(2)是 函数接口: 函数定义 与 函数使用 (的 两方 programmers ) 之间 的接口`
```
使用函数 时, 只需要根据 函数原型 编程
```

`4. 头文件 中 放 函数声明`

```
`可 确保 函数的所有声明 一致`
若 `函数接口` 发生 `变化, 只要 改 唯一的声明` 即可

否则, 若 函数声明 放 每个使用函数 的 源文件中, 
很难 确保 函数的所有声明 一致 => 易出错
```

`定义函数 的 源文件 应包含 声明函数 的 头文件, 可使 编译器 能检查 函数的 定义和声明 是否一致`

`5. 默认实参: 必须 从右到左 逐个默认`
```
(1)默认实参 `可以是 表达式`
```

**`(2)默认实参` 必须在 函数 `定义 与 声明 之间 2选1 指定`, 区别在于: `定义 / 声明 中 指定`, 则 某 源文件中  `必须包含 函数定义 / 只要包含 函数声明`, `调用函数 时, 默认实参 才 / 就 有效`=> 通常, `默认实参 应在 函数声明 中指定`, 函数声明 放 合适的 头文件**

##7.3 local object
```
每个 name 都有 scope (作用域), 每个 object 都有 lifetime (生存期)
```

`1. scope` of name：指 知道该 name 的 `程序文本区`

`2. lifetime` of object：程序执行过程中 对象存在的时间

```
3. 函数中 定义的 parameters 和 variable 的 name:
只 在 function body 中 visible

通常, variable name `从 声明 或 定义 处 开始, 
到 包围它的 scope 结束 处 结束`

4. `automatic object ( 又称 local variable )`
(1)是 `定义它的 函数 被调用` 时, `才存在` 的 object

(2)`默认`下, `lifetime 局限于 所在函数 每次执行期间`

(3)`函数调用 时, 遇 variable 定义语句` 时 `创建`,

创建 时 `有/无 初始化式` 
=> 初始化 / 未初始化( `初值 undefined` );

定义它的 `block 结束` 时 `撤销 (释放 局部空间)`

(4)parameter 也是 automatic object 

5. `static local object`
(1)scope 局限于 函数
(2)lifetime 却 横跨 函数的 多次调用
(3)lifetime 内 持续存在 并 保持其(每次更新后 的)值
```
`(4)函数 第1次调用 前, 就已 创建 并 初始化 (不指定 / 指定 初始化式 -> 初值为 0 / 指定值); 函数 第 >= 2 次 调用 时, 遇 "定义(即 初始化)" 语句, 啥也不干`

##7.4 给 函数传参 : 参数传递

```
每次 函数调用 时, 都会 重新创建 其 parameter, 
此时 所传递的 argument 会 初始化 对于 parameter

parameter 初始化 与 variable 初始化 一样:
```
`若 parameter 为 non-reference type, 则 copy argument's value;`
`若 parameter 为 reference type, 则 parameter 为 argument 的 别名`

###1. non-reference parameter

###(1)copy argument 实现 初始化

`non-reference parameter` 表示 相应 `arg 的 local copy( 局部 副本), modify para 只 modify local copy of arg`

```
初始化 规则 决定 以下结果
```

`1)para 为 non-reference + non-const`

`1]non-reference => copy arg => 若 modify para, 则 modify 的是 arg's copy`

`2]non-const => may ( attempt to ) modify para, which is arg's copy
=> not modify arg`

`=> const / non-const arg( obj ) 可初始化 reference + non-const para( obj )`
```
`1]non-reference => copy arg 
=> function 内 modify para, 
则 modify 的是 arg's copy
=> const arg( obj ) 可初始化 non-const para( obj )`

2]non-const => may ( attempt to ) modify para, which is arg's copy
=> not modify arg

显而易见, non-const arg( obj ) 可初始化 non-const para( obj )
```

`2)para 为 non-reference + const`
```
1]non-reference => copy arg 
=> 若 modify para, 则 modify 的是 arg's copy
```
`2]const => not modify para: arg's copy`

`=>const / non-const arg(obj) 可初始化 reference + const para(obj)`


###(2)指针 para
```
copy arg pointer
=> modify pointer para is modifying pointer's copy

但 通过 `non-const` type 的 pointer para, 
即 pointer arg's copy,  
we can `modify pointer arg('s copy) 指向` 的 
`non-const obj`
```

`1)指向 const type` 的 `pointer ( arg ) 不可初始化 指向 non-const type` 的 `pointer ( para )`
```
by `non-const type` 的 pointer para, 
which is pointer arg‘s copy,  
it may attempt to 
`modify pointer arg('s copy) 指向` 的 `const obj`
=> 编译报错 
```

`2)指向 non-const type` 的 `pointer ( arg ) 可初始化 指向 const / non-const type` 的 `pointer ( para )`

```
conclusion:
```
1)若要 `保护 pointer 指向的值`, 用 `指向 const type` 的 `pointer para`

```
#include <iostream>

void protected_obj_of_pointer(const int* p);
int main()
{
	const int a = 10;
	int b = 5;
	const int* p1 = &a;
	int* p2 = &b;

	protected_obj_of_pointer(p1);
	protected_obj_of_pointer(p2);
}

void protected_obj_of_pointer(const int* p) 
{ 
	std::cout << *p << std::endl; 
}
```

2)若要 `修改 pointer 指向的值`, 用 `指向 non-const type` 的 `pointer para`
```
#include <iostream>
void modify_obj_of_pointer(int* p);
int main()
{
	const int a = 10;
	int b = 5;

	const int* p1 = &a;
	int* p2 = &b;

	// 无法将 const int* 转换为 int* : compile error
	// modify_obj_of_pointer(p1); 

	modify_obj_of_pointer(p2);
}

void modify_obj_of_pointer(int* p)
{
	*p = 1;
	std::cout << *p << std::endl;
}
```

###(3)不能 copy argument 的 场景
```
1)arg 需要 modify
2)arg 很大, copy 的 时间/空间 代价太大
3)copy deleted

solution: para 定义为 reference 或 pointer 类型
```
###2. reference parameter
```
// 不应该 copy arg 的例子: 交换 2 个 arg

void swap(int v1, int v2);
Problem: 交换的是 local copy of args, 而不是 args
Solution: reference parameter
void swap(int &v1, int &v2);

调用函数 时, 
创建 引用形参 并 绑定 reference para to arg

Solution2: 用 non-const type 的 pointer para
void swap(int *p1, int *p2);
```

**想在 `called function 中 访问 calling function 的 arg`, 用 `reference para 比 pointer para 更安全、更自然`, 因为 `arg (发端) 不用管 类型, 直接 用 arg 调 函数`, para (收端) 会根据指定类型(=> 要管类型) 按 reference 接收 arg**

###(1)用 reference para 返回 extra result :  这是 通过 function 修改 arg 的 第 3种 方法

`function 只能 ( 形式上 ) return 1 个 value; 若 想要 function ( 效果上 ) "return" 多个 value`
```
有 2 种方法:
return 语句 返回 1个 value +

(1)其余 value 构成 struct, 
pointer arg 传给 non-reference 
    + non-const pointer para
```
`(2)其余 value 作 arg 传给 reference para, 或者说 para 以 reference 方式 接收 arg`


###(2)用 const reference 避免 copy

`1)const 引用 ( const reference ) : 指 const 某种类型/对象 的 引用`
```
形式为
const some_type &

而不是
const &some_type
```
```
note: 要 区别 const 引用 与 const 引用
```
`const 引用` 与 `const 对象 的 引用` 是一回事
`const 指针` 与 `const 对象 的 指针` 却不同
```
// const 指针
some_type *const 

//const 对象 的 指针
const some_type*
```
```
2) 用 const 引用 避免 copy`
```
若 用 `reference para` 的 `唯一目的` 是 `避免 copy arg`, 则 应该用 `const reference para:` 
`用 reference 以 避免 copy arg;`
`用 const 以 1]避免 modify arg,  2]接收 rvalue arg, 3]接收 需要进行 type conversion 的 arg`

###(3)const reference 的 灵活性

**`non-const reference para` 只能关联到 `完全同类型` 的 `non-const 对象`**

`=> non-const reference para 很不灵活`, 相应的 `arg 不能是`
`1)字面值 或 rvalue expression`

`2)需要进行 type conversion 才能与 para 同类型`

**`3)const 对象 / const reference` 
=> 相应的 `function 不能被` 其他 `const reference para 的 function` 接收的 `const (对象的) 引用` 作 arg `调用`**
```
non-const reference para 很受限制, 
在于其他 const reference para 的 function 
communicate 时, 很容易误用 引起 (编译) 报错
```

`solution: 应该将 不需要修改(相应 arg) 的 reference para 定义为 const reference para`

```
#include <iostream>
#include <string>
using std::string;

bool is_sentence(const string& s);

int main()
{
	const string s("hello.");
	std::cout << is_sentence(s) << std::endl;
}

string::size_type find_char(string& s, char c)
{
	string::size_type i = 0;
	while (i != s.size() && s[i] != c)
		++i;
	return i;
}

bool is_sentence(const string& s)
{   //(1) const reference para: s
	//(2)s is `viewd` as `const object` in `function body`
	//(3)`const object can't converted` to `non-const reference`
	// => compile error:
	// `const std::string` can't converted to `std::string &`
	return (find_char(s, '.') == s.size() - 1);
}
```
```
//solution: find_char 中 
//non-const reference para 改为为 const reference para

string::size_type find_char(const string& s, char c);
```

```
#include <iostream>

int incr(int& val)
{
	return ++val;
}

int main()
{
	short v1 = 0;

	const int v2 = 1;
	int v3 = 2;

	//v3 = incr(0);  //(1) 字面值 不能初始化 non-const reference
	//v3 = incr(v1 + v2); // rvalue expr 不能 不能初始化 non-const reference
	//v3 = incr(v1); //(2) short 需要 type conversion 为 int => 不能初始化 int&
	//v3 = incr(v2); //(3) const arg 不能初始化 non-const reference
}

// 无 solution: 
// 需要修改(相应 arg) 的 reference para 必须定义为 
// non-const reference para 
//=> 只能接收 `完全同类型` 的 `non-const 对象`
```

###(4)指向 `指针的引用`
```
secenario: 交换 2 个 指针

void swap_ptr(int *&p1, int *&p2)
{
	int *tmp = p2;
	p2 = p1;
	p1 = tmp;
}
```
###3. vector 等 容器类型 的 parameter

```
(1)通常, para 不应该 为 vector 等 标准库 容器 类型.

(2)non-reference para 为 vector 型时, 
function 会 copy vector 中 每个元素
```
(3)C++ 中, 更倾向于 通过 `传递`  `指向 容器中 需要处理的元素` 的 `迭代器` 来 `传递容器`


###4. 数组 parameter

###(1)`数组 的 2个性质` 影响着  `操作数组` 的 `function 的 定义和使用`

`1)数组(名) 不能 复制/赋值 给 另一数组(名)`
**=> `function para` 无法编写为 `(实际上 的) 数组类型`, 只能 编写为 `(形式上 的) 数组类型` (自动转化为 实际上 的 指针类型)**

```
int a[] = {1, 2, 3};

int b[](a); // 数组 不能 复制

int c[3];
c = a;      // 数组 不能 赋值
```
`2)数组 名` 一般会 `自动转化为` 指向其 `首元素的 指针`
=> `function` 通常 `通过  数组元素的 指针` 来 `处理数组`


###(2) `(形式上 的) 数组形参 为 non-reference`

`1)数组语法` 定义的 `数组 para 等价于` 指向数组元素类型的 `指针 定义的形参`

`2)数组语法` 定义的 `数组 para` 直接定义为 `指针 para` 更好, 以 `明确指出 function 操作` 的是 `数组元素的 指针`, 而不是 数组本身

**`3)编译器 check  数组 para` 关联的 `arg` 时, `只 检查 1] arg 是不是 pointer,  2] pointer 类型 与 数组 element 类型 是否匹配, 不检查 数组长度`**
`=> 编译器 会 忽略 数组 para 中的 数组长度`, 把 int [10] 转换为 int*
**=> `数组(形式的) para` 带 `数组长度` 时 `容易引起误用: 数组内存 的 越界访问`**

```
//eg1.three equivalent declarations of array para
void f(int [10]):
void f(int []);
void f(int *);

//or
void f(int a[10]):
void f(int a[]);
void f(int pa*);
```

```
//eg2. 数组 para 带 数组长度 时的 误用: 数组内存 的 越界访问

#include <iostream>
void f(const int a[10])
{
	for (size_t i = 0; i != 10; ++i)
		std::cout << a[i] << std::endl;
}

int main()
{
	int a = 0;
	int b[2] = { 1, 2 };

	f(&a); // ok: &a type is int*, probable run-time error
	f(b);  // ok: b is converted to pointer to 0th element, int*
		   // probable run-time error
}
```

`4)数组 para` 是 `non-reference + 数组 语法 or 指针形式`

`数组 para` 初始化为 `arg ( 数组首元素的 指针) 的 copy`,
function `通过 该 pointer's copy 可 modify 数组元素的值`

###(3)数组 para 为 reference: 通过 引用 传递数组
```
编译器 `不会将 arg 转化为 pointer`, 
而是 `传递 数组 的 reference 本身`
=> 编译器 `检查` arg 与 para 的 `数组大小 是否匹配`
```

```
#include <iostream>

//(1)(&arr) => arr 是 reference type: int (&)[3] 型
//(2) [] 优先级 高于 & => 要用 ()
void f(int (&arr)[3]) 
{
	for (size_t i = 0; i != 3; ++i)
		std::cout << arr[i] << std::endl;
}

// void f(const int (&arr)[3]); 也可以

int main()
{
	int a = 0;
	int b[2] = {1, 2};
	int c[3] = {1, 2, 3};

	// (3)error: 无法从 int * 转换为 int (&)[3]
	//f(&a); 
	
	// (4)error: 无法从 int [2] 转换为 int(&)[3]
	//f(b);  
	
	f(c);    // arg is array of 10 ints
}
```

```
// error: 无法 从 int [3] 转换为 int (&) []
void f(int (&arr)[], size_t size) 

int c[3] = {1, 2, 3};
f(c, 3);
```
数组 para 为 `reference` 时, `必须指定` 数组 para 的 `数组大小`

###(4)数组 实参

`传递数组` 时, `数组 para` 为 
```
1)non-reference 
=>编译器 将 arg 转化为 pointer

arg 一般为 array ( name )
=> 转化为 数组首元素的 pointer
```

`2)reference`
=>编译器 `不会将 arg 转化为 pointer, 而是 传递 数组 的 reference 本身` 
=> 编译器 `检查` arg 与 para 的 `数组大小 是否匹配`

###(5)多维数组 传递: 以 指向 `0 号元素` 的 `指针方式 传递`

多维数组 实际是 `数组的数组`, 即 `其  element 也是 数组`

```
// 2 者等价 : matrix 名字相同, `type 几乎 相同 => 使用 几乎相同`, 
// 只是 作 array name 时, 是 `值不可改变` 的 地址(指针)

// 编译器 忽略 第1维 大小, 数组 para 中 最好不要带 第1维大小
void f(int matrix[][3], int row_size);

void f(int (*matrix)[3], int row_size); 
// [] 优先级 高于 * => (*matrix) 要用 ()
```

```
//(1) int matrix[][3] -> int (*matrix)[3]
int matrix[][3] : 
matrix 与 [] 结合: matrix[]
=> matrix 是 `数组`, element 是 `int [3]`, 即 含 3个元素的数组
=> array 转换为 pointer : *pmatrix 
// 写为 matrix 也可以, 变量 name 叫什么不重要, 重要的 它的 类型
=> 带上 所 指向的 element, int [3]: int (*pmatrix)[3] 

//(2) int (*matrix)[3] -> int matrix[][3]
*matrix => matrix 是 pointer, 指向 int [3]
=> pointer 转换为 array: matrix[]
=> 带上 int [3]: int matrx[][3]
```
###(6)函数 处理 传递的数组 的 3种方式

```
1)用1个element `标记 数组的结束`
如 字符串: 以 null 字符 标记 char 数组的结束

2)借用 STL 做法: 
传递 数组 `第1个元素` 和 `最后1个元素 下一位置` 的 `pointer`

int f(const int* begin, const int* end)
{
	while(begin != end)
		std::cout << *begin++ << std::endl;
}

3)显式 `传递` 表示 `数组大小` 的 para: 标准化之前的 C++ 做法

void f(const int a[], size_t size)
{
	for(size_t i = 0; i != size; ++i)
		std::cout << a[i] << std::endl;
}
```

###(7)conclusion
`1)数组 para` 是 `non-reference + 数组 语法 / 指针 语法`

`数组 para` 初始化为 `arg ( eg. 数组首元素的 指针) 的 copy`,
function `通过 该 pointer's copy 操作 / modify 的是 数组元素的值`

编译器 `只 check 1] arg 是不是 pointer,  2] pointer 类型 与 数组 element 类型 是否匹配, 不检查 数组长度`

`2)数组 para` 是 `reference + 引用 语法`

编译器 `不会将 arg 转化为 pointer, 而是 传递 数组 的 reference 本身` 
=> 编译器 `检查` arg 与 para 的 `数组大小 是否匹配`

`3)不 修改 数组 para 的 element` 时, 数组 para 定义为 `const 对象 的 指针`
```
void f(const int*);
```

```
4)数组 para 
无论是  数组语法 / 指针语法 / 引用 语法,
无论是 non-reference / reference,
一般, `arg 形式上 都是 数组名`, 要根据 para 语法形式 具体分析
```

###5. main: 命令行选项
```
即 main parameter

1. 给 main 传递 实参: 在 main 中 用 2个形参
```

`int main(int argc, char* argv[]) { }`

```
=> argv : array, element type is char* 
```

```
=> 实参 只用传 argv 的 每个 `元素 (用 字符串 / C风格字符串数组)`
   实参 不用传 argc

argv 用于指示 argv 数组 的 元素个数
编译器 根据传入的 `实参( argv 数组 的 元素), 计算出 argc`
```

`int main(int argc, char **argv) { } // 另一种 等价形式`
```
=> argv : pointer, point to char*
```

`理解 array 与 pointer 的 相互转换, 就容易理解 char* argv[] 与 char **argv 的 相互转换`

```
(1)char *argv[] 变形
[]  优先级 高于 *
=> argv 先与 [] 结合: argv[] 
=> argv 是 数组
=> 转换 为 指针: *argv 
=> 再 带上 char* 
=> char **argv

(2)char **argv 变形
argv 先与 左边相邻 的 * 结合: *argv 
=> argv 是 指针
=> 转换为 数组: argv[]
=> 再 带上 char*
=> 再 带上 char* argv[]
```

```
//eg. 用 main 命令行选项, 输出 传给 main 的 2个(int 型)实参的 和
// note: argv 是 array, element 为 char* 
// => 要先把 传给 main 的 argv 中 每个 char* 实参 
//    类型转换为指定 类型
#include <iostream>

int main(int argc, char* argv[])
{
	// const char* -> double
	//double  atof(char const* _String); 
	
	if (argc == 3)
	{
		std::cout << argv[0] << std::endl;
		std::cout << int(atof(argv[1])) + int(atof(argv[2])) << std::endl;
	}
	return 0;
}
```
![image.png](https://upload-images.jianshu.io/upload_images/20172887-31a7d8111477c925.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###6. 可变 parameter: 省略符形参
```
void f(para_list, ...);

编译器 对 `显式 声明的 para_list` 相应的 args 做 类型检查;
对 `省略号 相应的 args 暂停 类型检查`
```

##7.5 return from function: return 语句
```
return 语句 2种形式:
return;
return expression;
```
###(1)无 返回值 的 function

```
1)只能 用于 return type 为 void 的 function

2)return; 语句 非必需

3)return type 为 void 的 function 可以 
  return call to another void function

void do_f();

void f()
{
	do_sth();
	
	// return call to another void function
	return do_f(); 
}
```

###(2)有 返回值 的 function
```
1)main 的 return value

return type 不是 void 的 function 必须 返回一个值, 这1规则的 例外是,
```
`main ( 比如 return type 为 int ) 可以 没有 返回值, 因为 编译器 会隐式地 加 1句 return 0;`

```
main 的 第2个 特别之处在于: 
可将 main 的 返回值 视为 `状态指示器`, 
0 表示 程序运行成功

2)function 返回 non-reference
`函数调用处` 将 `函数返回值 copy` 给 创建的 `临时对象`

3)function 返回 reference
不 copy 返回值, 返回对象本身

// find longer of two strings
const string &longer_string(const string &s1, const string &s1)
{
	return s1.size() > s2.size() ? s1 : s2;
}

4)Don't return reference/pointer to local object
因为 函数 结束后, 会 `释放 local object 的 存储空间`, 
此时, 返回的 reference / pointer 指向 
`不再存在的 object ( => undefined memory )`
=> `悬垂 pointer`

// disaster: return a reference to a local object
const string &f(const string &s)
{
	string ret = s;
	// transform ret in some way
	return ret;
}

5)`返回 reference` 的 function `返回 lvalue 
=> 可以被 赋值`

#include <iostream>
#include <string>

using std::string;

char& get_val(string& str, string::size_type i)
{
	return str[i];
}

int main()
{
	string s("hello");

	get_val(s, 0) = 'A'; // assign s[0] to 'A'

	std::cout << s << std::endl;
}
```

###(3)递归 (recursion)
```
递归函数 : 直接 或 间接 调用自己 的 function

// greatest common divisor 最大公约数
int gcd(int v1, int v2)
{
	if(v2 != 0)
		return (v2, v1 % v2);
	return v1;
}
```

##7.6 inline function / 内联 函数

`1. 调用点` 上 "内联地" 展开 (可视为 `直接 展开`) , `避免了 函数调用的 开销`

```
2. `inline` 对 编译器 来说 只是 advice, 
编译器 可能 `ignore this advice`

3. inline 适于 只有几行 的 function
```

`4. inline function definition 必须放 头文件`

`1)为了 能在 调用点 展开, 编译器 必须 可见 inline function 的 definition`, 而不能 仅仅是 prototype 

`2)inline function` 在程序中 `定义(展开) 不止1次`
```
可理解为:inline function定义 `并非 真正的 函数定义`,
实际 是一种 展开
```

`=> inline function definition 放 头文件, 可 确保 `
`1] 函数调用 的 各处`, inline function `definition 相同`
2] inline function `definition 对 编译器 可见 => 能在 调用点 展开`

##7.7 member function of class
```
#include <string>

class Sales_item
{
private:
	std::string isbn;
	unsigned units_sold;
	double revenue;
public:
	Sales_item() : units_sold(0), revenue(0.0) {}

	double avg_price() const;

	bool same_isbn(const Sales_item& rhs) const
	{
		return isbn == rhs.isbn;
	}
};
```

`1. 类内 定义`的 member function 被 编译器 `隐式` 地 当作 `inline function`

`2. 类 的 member function 可以 ( 通过 类的 other object ) 访问 类的 private 成员`
```
bool same_isbn(const Sales_item& rhs) const
	{ return isbn == rhs.isbn; }
```

`3. this 指针`

(1)`是` 编译器 为 每个 `non-static member` function 加的 `隐含 para, 是 const 指针`

(2)non-static member `function 调用` 时, this `初始化` 为 调用函数的 `object 的 地址`

```
obj1.same_isbn(obj2);

// 被 编译器 重写 为 
Sales_item::same_isbn(&obj1, obj2);
```

**`4. const member function: const 改变了 this ( 隐含 para, const 指针 ) 的类型, 使 其 指向的 类型 ( 对象 ) 也是 const`, 或者说 `使其 成为 a pointer to const`
=> `const member function 不能 modify 调用该函数的 对象 ( 的 member data )`**

```
// const member function: prototype 后 加 const
bool same_isbn(const Sales_item& rhs) const
	{ return isbn == rhs.isbn; }

// <=> 编译器 重写为
// pseudo-code illustration, illegal
bool Sales_item::same_isbn( const Sales_item *const this,
							const Sales_item& rhs) 
	{ return this->isbn == rhs.isbn; }
```

```
// 若 该函数 声明为 non-const member function
bool same_isbn(const Sales_item& rhs)
	{ return isbn == rhs.isbn; }

// <=> 编译器 重写为
bool Sales_item::same_isbn( Sales_item *const this,
					        const Sales_item& rhs) 
	{ return this->isbn == rhs.isbn; }
```

`5. const 对象`、指向 const 对象 的 `pointer 或 reference 只能 调用 其 const member function`
```
调用 non-const member function 会 编译 报错: 
non-const member function 会 modify const object 
```

`6. synthesized default ctor` 一般用于 `只包含 类类型 member` 的 类
```
含 内置类型 时, 通常应 显式定义 default ctor 
以 初始化 内置类型 member

synthesized default ctor(合成 默认 构造函数): 
类 没有显式定义 `任何 ctor` 时, 
编译器 为该类 `自动生成` 的  default ctor
```
##7.8 重载函数 (overloaded function)
###(1)含义、形似 重载 但 本质相同的 4对 functions

```
1)何谓 重载函数?
```
`scope 相同, name 相同, para list 不同`

`重载 不能仅仅` 基于 `不同的 return type` => 重载 不 care return type

```
main 不能 重载

2)本质相同的 4对 functions
```
`non-reference para: non-const 或 const 版本 的 2个函数 引起 重定义`

```
//1. para name is ignored
Record lookup(const Account& acct);
Record lookup(const Account&);

typedef Phone Telno;

//2. Phone and Telno are the same type
Record lookup(const Phone&);
Record lookup(const Phone&);

//3. default arg doesn't change the number of parameters
Record lookup(const Phone&, const Name&);
Record lookup(const Phone&, const Name& = "");

//4. for non-reference para, const is irrelevant
//non - reference para : copy arg
//= > 函数 操作的是 arg's copy, 无法 modify arg
//= > para 为 non - const 或 const 无本质区别
//    const arg(obj) 可以 pass 给 non-const / const para
//= > 2者会 引起重定义
Record lookup(Phone);
Record lookup(const Phone); // redeclaration
```

3)para 为 `const reference` 或 `指向 const 类型 的 pointer, 有 / 无 const 是 不同 的 重载 functions`

###(2)重载 与 scope

`1) local ( scope‘s ) name 会 hide / 屏蔽 global ( scope's ) name`
```
即使 1个为 function, 另一个为 variable

=> 重载函数 的 每个版本 应在 同一 scope 中 声明
```

`2)name 检查 在 类型检查 之前`

```
//eg1.
string init();

void f()
{
	// init is local, and hides global name init
	int init = 0;  
	
	// error: global init is hidden
	string s = init(); 
}
```

```
//eg2.
void print(const string &);
void print(double);

void f(int val)
{
	// new scope: hides previous instances of print
	void print(int);
	
	print("hello"); // error: void print(const string &) is hidden
}

//solution: void print(int); 放到 f 外
```

###(3)function matching( 即 overload resolution)
```
function call 与 重载函数 集合中 1个函数 的 匹配过程

3种结果:
1)有 `最佳匹配(best match)`
2)无 匹配 => 编译 error
3)有匹配, 但无 最佳匹配 => 调用 具有 `二义性(ambiguous)` 
=> 编译错误
```

###(4)overload resolution (重载 确定) 3步曲
```
1)找 candidate(候选) function : 与 被调函数 同名 的函数

2)找 viable(可行) function: 
1]para number == arg number
2]每个 arg type 匹配 或 可 implicitly converted to para type
```
3)找 `最佳匹配 (best match)`
`匹配 或 可 implicitly convertion` 的 `arg 个数`  `最多 且 唯一`

```
若 匹配 或 可 implicitly convertion 的 
`arg 个数 最多` 的 functions 超过 1个 
=> 调用 二义性(ambiguous)
```

```
//eg3.
void f();
void f(int);
void f(int, int);
void f(double, double = 3.14);

f(6.6);    // call void f(double, double = 3.14);

f(10, 6.6);// ambiguous
```

###(5)arg type conversion 实参 类型转换

```
conversion level  降序:
1)exact match
2)type promotion
3)standard conversion
4)class-type conversion
```

1)arg 为 任意整型 时, 一般, 重载函数 的 `int 型 版本 优于 short 型, 原因: 到 short 型 往往是 standard conversion, 而 到 int 型 可能是 type promotion`

```
// eg.
char -> int 优先级 高于 char -> short

char -> double 优先级 高于 char -> unsigned char
```

```
2)枚举值 -> integer para
```
`枚举值 type promotion` 为 `>= int 型` 
`枚举值 决定 其 提升的类型, 提升的类型 依赖于 机器`
```
enum Tokens { INLINE = 128, VIRTUAL = 129 };

void f(unsigned char);
void f(int);

unsigned char uc = 129; // unsigned char 能存下 129 

f(VIRTUAL); // call void f(int)
f(uc);      // call void f(unsigned char)
```

```
3) 重载 与 const para
```
**`仅当 para 为 reference 或 pointer 时, para 是否为 const 才有影响 => para 为 const 与 non-const 才是 重载`**

**`args 是 const ( T型 ) 对象 => 调 const T& / const T* 型 para;`**
**`args 是 non-const ( T型 ) 对象 => 调 T& / T* 型 para`**

```
用 non-const 对象 初始化 
1)const reference para: conversion
2)non-const reference para: exact match

用 non-const 对象 的 pointer 初始化 
1)const 类型的 pointer(para): conversion
2)non-const 类型的 pointer(para): exact match
```


**不能 基于 `pointer 本身 是否为 const` 来实现 函数`重载`**
```
f(int *);
f(int *const); // redeclaration
```

##7.9 function pointer (fp)/ 函数指针 

###1  定义 用 typedef 简化

```
//(1)
bool (*pf) (const string &, const string &);

//(2)
typedef bool (*Pf) (const string &, const string &);
Pf_Cmp pf;

pf 的 type 为 Pf, 即function pointer type:
bool (*) (const string &, const string &);

pf 后的 ( ) 优先级 高于 前的 *

=> *pf 要用 圆括号
```

###2  初始化 与 赋值

`引用 函数名` 但 `没 调用函数` 时, `函数名(function name) 被自动 解释/转换 为 函数指针(function pointer) => function pointer 只能初始化 或 赋值 为 同类型的 1)function  2)function pointer  3)0`

```
return_type f(para1, ...);
f == &f;
```

```
bool lenCmp(const string &, const string &);

Pf pf1 = 0;
Pf pf2 = lenCmp; 
pf1 = lenCmp;
pf2 = pf1;
```

###3  调用函数

`可以不用 dereference 操作符, 直接用 function pointer 调用函数`

```
Pf pf = lenCmp;

// 3 种 等价 调用
lenCmp("he", "she"); // direct call
(*pf)("he", "she");// explicitly dereferenced
pf("he", "she");//implicitly dereferenced
```

###4  作 parameter: 可以是 function type 或 function pointer type

`para 为 function type` 时, 该 `para 及其 相应的 arg` 都被自动 `converted 为 function pointer type`

```
void f( const string&,const string&,  
		bool (const string&, const string&) );

void f( const string&,const string&,  
		bool (*)(const string&, const string&) );
```

###5  作 return type: `只能是 fp type, 不能是 function type`

`return type为function type`时, return value `无法转换为 fp type`

```
return type 为 fp type 时, 很难写

简单方法为:
1)先写 function 及其 para list
2)再 把 fp 型 return type 嵌进去

//(1)
1)f(int)
2)嵌入 fp 型 return type:  int (*) (int*, int)
=>int (* f(int) ) (int, int);

f 为 function type( para 为 int 型), 
其 return type 为 fp type

//(2)用 typedef 简化
typedef int (* Pf )(int*,int);
Pf f(int);
```  

```
int(* f(int) )(int,int);

由 return type 为 fp type 的 函数声明 推导其
fp type 与 function type

`name + 最先结合的操作符 为 ()`
=> `f(int)`
=> f 为 `function type`
=> `其余为 return type` : int(*)(int*,int)
    ,即 fp type
```

```
typedef int f(int*,int);
void f1(f);
f *f2(int);

f f3(int); // error: f3 has return type of function type
```
###6  指向 重载函数

`function pointer 的 类型 必须 与 重载函数 的 1 个版本 精确匹配`, function pointer `才可以被 重载函数 初始化 或 赋值`

```
extern void f(unsigned int);

void (*pf1)(int) = &f; // no match => error

void (*pf1)(unsigned int) = &f; // exact match => ok
```

#chapter8 标准 IO

![](http://upload-images.jianshu.io/upload_images/20172887-b5ee50b55e852c89.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1080/q/50)

`1. IO 对象 不可 复制 或 赋值`

=>
`(1)流对象 不能 存储到 vector 等 容器`
`(2)para / return type 不能为 流类型, 只能为 流类型(对象)的 pointer 或 reference`

```
支持 copy 的 element 才可以 存储 到 vector 等 容器
```

`2. stringstream 对象 的 使用`

```
// 都是 string 对象, line 以 '\n' 结尾, 
// word 以 '\0' 结尾
string line, word;
while( getline(cin, line) ) 
{
	// do per-line process
	istringstream stream(line);
	while( stream >> word )
	{
		// do per-word process
	}
}
```
