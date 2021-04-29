```
key points:
1. public / private / protected mem 的 访问
2. this 指针
3. const member function
	mutable
4. class scope
5. ctor
    单 para ctor & explicit
6. friend (友元)
7. static member
```

```
C++ 早期称为 C with class

1. class 

(1)class 就是 定义了 `a new type 和 a new scope`

(2)class 是 抽象数据类型

class type: `对应 Problem` 中各种 `concept`

设计良好的 class type: 像 内置类型 一样 易用

data 和 operation on class 被视为一个单元

data 实际上表示 `状态`

3 种 member: dada / function / typedef 类型别名

2. 数据抽象 和 封装: class 背后蕴含的 基本 思想

(1)数据抽象:
interface 与 implement 分离

(2)封装:
1)hide implement detail

2)2种形式: 
class / function=> C 也有封装的 concept

(3) vector vs. array 
1) vector:
是 抽象的 : 使用 时, 只需 考虑其 interface;
是 封装的 : 是 class template, 

2) array ( 数组 )
非 抽象 / 非 封装

(4)`type / class 未必都是 抽象的, 也可能是 具体的`

标准库中 `pair 类` 
1)是 实用、设计良好的 `具体类`, 暴露 实现细节
2)只是将 2个 mem data 捆绑成 单个 obj
此时, 隐藏 mem data 没有必要, 没有明显好处, 
反而 使其使用 复杂化
3) 有 mem func

(5) 数据 抽象 和 封装 的 2个优点
1) 可 `避免 低级的 用户级 错误`
2) 长期演进中, 非必要时, `无须 改变 用户级 code`

3. client ( 用户 ) in C++

以 class / application program 为 参考, 
client 分别为 
code that use class / people who use app

4. 类的 前向声明

(1)限制: 不能 定义 class's object

(2)用途: 一般用于 互相依赖 的 class 

只能用于
```
`1) 声明 para type / return type` of a function
`2) 定义` 指向该 class ( obj ) 的 `pointer / reference`

```
5. `mem func` 

(1) 只能 `重载` 本 class 的 other mem func

(2)`重载规则 和 匹配过程` 与 non-mem func 相同

(3) class 内 定义时, 默认为 `inline`
inline 位置: `定义 和 声明 处 均 可放` 于 return type 前

6. `inline` mem func

定义 必须在 `调用 它的 每个 源文件 中 可见`
=> class body 外 定义 时, 应 `放 class 定义 文件`
```

#1. public / private / protected 成员 的 访问

###1 不考虑 static 和 friend 时
```
public / private / protected member :

最小访问权限：
类外  通过 类的对象 /
本类内 通过 public 成员函数 /
子类内 通过 public 成员函数

1)对外开放权限: public > protected > private

=> member 若能被 开放性大的 member function 访问, 
必然能被 开放性小的 member function 访问

2)`non-static mem: 必须 直接或间接 用 object 引用`

3)类外 用 `class name + scope operator:: 限定, 
其后 scope 也在 class 内`

4) class/struct mem 默认为 private / public
```

```
#include <iostream>
using namespace std;

class A
{
public:
	A() : x(0), y(0), z(0){ }
	int get_x() const;

    //1. public mem func 调 private mem func
	int get_y_next() const { return get_y() + 1; }
protected:
	int z;
private:
	int x;
	int y;
	int get_y() const { return y; }
};

//2. A:: 限定 的 scope 像在 类内 一样使用
int A::get_x() const 
{
	return x; // 3. 即 this->x
}

class B : public A
{
public:
	int get_z() const { return z; }
};

int main()
{
	//4. non-static mem func: 要通过 obj 引用
	//A::get_x();
	A a;
	cout << a.get_x() << endl;
	cout << a.get_y_next() << endl;

	B b;
	cout << b.get_z() << endl;
}
```

```
static mem 的访问 / 通过 友元 访问, 见后续
```

#2. this 是 `编译器 隐含定义 的 non-static member function 的 parameter: a const pointer`

`1. 指向 调用 non-static mem func 的 object`

`2. 必须用 this 的情形`

需要 `整体 use` 调用 non-static  mem func 的 `obj 时: 如 return reference of obj that calling the function`

```
A& A::f()
{
    ...
    return *this;
}
```

`3. this 是 const pointer: A * const => this 其值不能改变`

`1)const mem fuc: 保证不 modify *this ( 的 immutable mem data )`
=>`只能 return *this 的 const reference: const A&`

```
2)non-const mem func: 
可 modify *this ( 的 immutable mem data )
=>
可 return *this 的 non-const/const reference: 
A& / const A&`
```

```
A& A::f()
{
    ...
    return *this;
}

const A& A::f() const
{
    ...
    return *this;
}
```

#3.const member function

`1. const 位置: 在 声明 和 定义 中 都要有,` 否则, 编译报错

`2. 保证 不 modify obj ('s immutable mem data) `

`3. mem func 的 const / non-const 版本 是 
 重载函数`

`(1)const / non-const 版本:` 

`1)之一存在` 时, 
`const obj 只能调 const 版本;`
```
non-cosnt obj 可调 non-const / const 版本.
```

`2)同时存在` 时,
`const  / non-const obj 调 const / non-const 版本`

`(2)2 个 不同名 的 mem func:` 

`const 者 不能 调用 non-const 者: 因为 non-const 者 会 modify obj => 编译报错;`
```
non-const 者 可 调用 const 者: 因为 const 者 不 modify obj
```

`(3)某 mem func` 被 `另一 mem func` 的 
 `non-const 版本 和 const 版本 都调用` 时 => 该 mem func `必须为 const 版本`

```
#include <iostream>
using namespace std;

class A
{
public:
	A() : x(0) { }
	
	//1) mem func1: non-const 版本
	A& display(ostream& os)  
	{
		do_display(os);
		return *this;
	}
	//2) mem func1: const 版本
	const A& display(ostream& os) const 
	{
		do_display(os);
		return *this;
	}
	void set(int i) { x = i; }
private:
	int x;

	//3) mem func2, 被  mem func1 的 const / non-const 调
	// 必须为 const 版本
	void do_display(ostream& os) const
	{
		os << x << ' ';
	}
};

int main()
{
	A a1;
	const A a2;
	a1.display(cout).set(1); //0 call non-const 版本
	a1.display(cout);        //1

	a2.display(cout);        //0 call const 版本
}
```
**4. 用于 public function 的 实用 `private function`**

`(1) 小 private function ( 如 do_display ) 的 意义: 连续调用`

**`支持  对其 caller function ( display ) 的 调用 return 的 non-const obj 上 接着调用 该 class 的 另一 mem func`**
```
// eg.
a1.display(cout).set(1);
```

```
(2) 设计良好的 C++ 程序 具有很多 像 do_display 一样
的 小函数, 它们
```
**`1) 将 局部 code 分层, 是 1 种 灵活的 解决问题 的 思路`**
`2) 完成 other function 的 "practical" work ( "实际" 工作 )`
`3) 在 class 内 定义, 成为 inline, 无任何开销`

`5. para 为 指向 const / const type ( obj )  的 pointer -> 重载函数`

```
void A::f(A* p) { }
void A::f(const A* p) { }
```

```
3 & 5 =>
```
`6. 基于 const 的 2种 重载`
`1) 基于 mem func 是否 为 const, 可以重载 mem func`
`2) 基于 pointer para 是否 指向 const type, 可以 重载 func`

`7. const & mutable ( keyword : 可变 )`

**`const mem func 可以 modify obj 的 mutable  mem data` => const mem func 只是 `保证 不 modify obj 的 immutable mem data`, 但可以 modify mutable mem data**

`mutable mem data 不能为 const`

```
#include <iostream>

class A
{
private:
	mutable int access_count;
public:
	A() : access_count(0) { }
	void display(std::ostream&) const;
};

void A::display(std::ostream& os) const
{
	++access_count;
	os << access_count << "\n";
}

int main()
{
	A a;
	a.display(std::cout); // 1
}
```

#4. 类 scope
```
1. `class scope 外, class member 的 访问/使用`

1)non-static mem 只能用 
obj / pointer or reference that point to obj 来访问

2)static mem 可用 `class name + scope operator::` 来访问
```

`2. class body 外定义的 成员 必须 explicitly 或 implicitly 用 class name:: 限定`, 以表明 在哪个 class scope 中

`(1)必须 explicitly 用 class name + scope operator::` 限定 的 2种场景

`1) 单独 mem 定义`

`2) return type` of mem func that `defined outside the class body => return type 在 mem func name 之前 =>  不在 class scope 中`

```
// 1)
void A::f() 
{
    ...
}
```

```
// 2)
inline A::integer A::f()
{
    integer tmp;
    ...
    return tmp;
}
```

```
(2)已 `implicitly 用 class name::` 限定
```
`3)para list / mem func body 中 的 mem:` 在 `已用 class name:: 限定 的 mem func name 之后`
```
=> 必然 也在 class scope 中 
=> 可直接 用
```

```
// 3)
class A
{
public:
    typedef integer int;
private:
    int x;
};

void A::f(integer i) //1. 直接引用 mem 型别
{
    x = i;           //2. 直接引用 mem data
}
```
`3. class scope 中的 名字查找`
`名字查找: 找 与 给定 name 匹配 的 declaration 的 过程`

`(1) name 作 type name 时 不能 重复定义`

即 **`hide 的 只能是 variable name, 不能是 type name`**

`(2) class body 外 定义` 的 `mem func 中 mem name` 的 `查找顺序:`

**`1)本 mem func scpoe -> 2)class scope: 包括 class body 外 定义的 other mem func / data name -> 3)本 mem func 定义前 的 global scpoe`**

```
#include <iostream>
int x = 10;
class A
{
public:
	A() : x(0) {}
	void f1(int _x)
	{ 
		// 1) class scope's variable name 
		// `hide` the same global variable name
		x = _x; // x is A's mem x
	}
	void f2(int _x)
	{
		// 2) global scope operator ::
		// `限定 使用` global scope 相应的 variable
		x = _x * ::x; 
	}

	void f3(int);
	int f(int);

	void print() const { std::cout << x << std::endl; }
private:
	int x; 
};

void A::f3(int _x)
{
	//3) inside class scpoe: => 调 A::f(int)
	x = f(_x); 
}

//4)note: `mem func body defined ouside class body`
//  is also `inside class scope`
int A::f(int _x)
{
	return 2 * _x;
}

int main()
{
	A a;

	a.f1(1);
	a.print(); // 1

	a.f2(1);   
	a.print(); // 10

	a.f3(1);
	a.print(); // 2
}
```
#5. ctor

`1. ctor 不能为 const`
`reason: const / non-const obj 都是用同一 ctor 创建的`

`2. 与其他 mem func 的 区别`

ctor 可用 `初始化列表`

`3. 分 2 阶段 : 初始化(分配 存储空间 + 编译器 中 构造 符合表, 以使 标识符 与 内存 关联) + 计算/赋值(擦除 旧值 + 写 新值)`

`无论是否 explicitly use 初始化列表, 初始化阶段 都会执行`

```
1)初始化

`无 explicitly  初始化列表` 时,
1) class type: 
call 相应 default ctor

2) 内置 ( 如 指针) / 复合 ( 如数组 ) type: 
local  scope -> 不初始化; 
global scope, 初始化 为 0
=> `内置/复合 type, 最好 explicitly 初始化`

(2)计算
赋值 etc.
```

`4. 必须 用 初始化列表` 进行初始化 的 3种情形

`mem 为`
`1) 无 default ctor` 的 class type
`2) cosnt`
`3) reference type`

`reason: ctor func body` 中 对这3种 mem 的 `赋值 不起作用`

```
class A
{
public:
    A(int i) : x(i), r(i) { }
private:
    const int x;
    int& r;    
};
```

`5. 单 para ctor: para type 到 class type 的 conversion`

用 `有 / 无 keyword explicit` 告诉编译器, 
`para 到 class type 的 conversion`
`必须 explicitly 指定 / 可按需 implicitly 进行`

```
=> `除非有 明显的理由` 要定义 
单 para type 到 class type 的 implicitly conversion, 
否则, `单 para ctor 应为 explicit, 以 避免出错`. 
而 用户 想实现 这种 conversion 时, 完全可以用 
`explicitly conversion`
```

```
`explicit 位置: 只在 类内 用`, 类外 不用
```

```
// eg1. single para ctor with explicit
#include <iostream>
#include <string>
using namespace std;

class A
{
public:
	explicit A(const string& name1 = "") : name(name1) { }

	//3) cosnt A& / const A
	//   必须 return 给 const A&,
	//   不能 return 给 (non-const) A&
	const A& get_instance(const A& a) 
	{ 
		
		return a; 
	}
private:
	string name;
};

int main()
{
	string str1 = "lilei";
	A a;

	//1) string -> A 必须 explicitly 指定

	//2) rvalue 作 arg 必须传给 const para
	//   不能传给 non-const para
	a.get_instance( A(str1) );
}
```

```
//eg2. single para ctor without explicit
#include <iostream>
#include <string>
using namespace std;

class A
{
public:
    A(const string& name1 = "") : name(name1) { }

    const A& get_instance(const A& a) const 
    { return a; }
private:
    string name;
};

int main()
{
    string str1 = "lilei";
    A a;

    // 2) string -> A 可按需 implicitly 转换 
    //<=> 显式调用 a.get_instance( A(str1) );
    a.get_instance(str1);
}
```

`6. construct obj 时, 不要用 无参圆括号,` 因为 会被 编译器解释为 `函数声明`

```
A a();  // 函数声明    
A a(1); // 调 ctor : arg 为 1  

A a;    // 调 ctor : 无参      

//调 ctor : 无参 / arg 为 1 + copy ctor
A a = A();  
A a = A(1);
```

`7. class 应 explicitly 定义一个 default ctor`, the `initialization value` for obj should `indicate` the obj  is `empty`

```
int : 0
string : "" //空 字符串
```

```
8. arg 决定 使用哪个 ctor

9. ctor 由 编译器 调用 => 自动执行
```
**10. ctor 的 初始化式**
`(1)mem 初始化 顺序: 按 mem 的 定义/声明 顺序` 

=> `最好按 与 mem 定义 一致的 顺序 写 初始化列表 `

```
(2)`初始化式` 可以是 `任意 表达式`

(3)`mem data 为 class type` 时, 要 `指定 arg` 
以使得 编译器 能 `匹配调用 相应的 ctor` 

11. 默认 arg 与 ctor
`单 para with default arg 的 ctor` is a default ctor

12. default ctor
(1)class 没有 1 个 explicitly 定义 的 ctor 时, 
编译器 才 隐含自动生成 1 个 default ctor

(2) 定义 obj 时, 若 不提供 初始化式 
=> 调 default ctor

(3)所有 paras 提供了 default arg 的 ctor 是 default ctor

13. struct 成员的 显式初始化: C风格 初始化
要求:
1)没定义 ctor
2)mem 全为 public

struct A
{
	int x;
	char* ptr;
};
A a = {0, 0}；
```

#6. 友元 friend

`1. class A` 将其 `private 和 protected mem 的 访问权限`  授予 指定 function / class B, 则 B 称为 A 的 `友元  function / class`

```
3种情形: 友元 为

(1)class B => 
B 所有 mem func 都可以访问 A 的 private / protected mem
```

`(2)a mem func` of class B

the `mem func`
`1) 相应的 class B` 必须 `在 class A 前 定义, 而不能只是 声明`
`reason: class A 中 declare friend mem func of B` 时, 至少要 `可见 declaration of the mem func => 要 可见 definition of B`

`2) 本身` 必须`在 class A 定义之后, 才能定义`, 因为要 `访问 class A 的 private 成员`
```
(3)ordinay non-mem func
```

```
//eg1: friend class 
#include <iostream>
using namespace std;

class A
{
	// 1) only declare B as friend 
	// => `needn't forward declare B`
	friend class B;
public:
	typedef int Integer;
	A() : x(0) { }
private:
	Integer x;
};

//2)friend class
class B
{
public:
	void f(A& a, A::Integer x1);
	void display(A& a) const
	{
		cout << a.x << endl;
	}
};

//3) a mem func of friend class B
void B::f(A& a, A::Integer _x)
{
	a.x += _x;
}

int main()
{
	A::Integer x = 1;

	A a;
	B b;
	b.f(a, x);
	b.display(a); // 1
}
```

```
//eg2. a friend mem func
#include <iostream>
using namespace std;

class A;
class B
{
public:
    //2) decalre mem fun: `para type: A&` 
    // => A must be `forward declared`
    void display(A& a) const;
};

class A
{
    //1) declare A's friend: a mem func of B
    // => declaration of the mem func must be seen
    // => definition of B must be seen
    friend void B::display(A& a) const;
public:
    A() : x(0) { }
private:
    int x;
};

// 3) define B's mem func:
// visit `授予 自己 friend 权限的 class A` 的 private mem
// => declaration of private mem of A must be seen
// => definition of A must be seen
void B::display(A& a) const 
{
    cout << a.x << endl;
}

int main()
{
    A a;
    B b;

    b.display(a);
}
```

```
//eg3. friend non-mem func
#include <iostream>
using namespace std;

class A
{
    friend void display(A& a);
public:
    A() : x(0) { }
private:
    int x;
};

void display(A& a)
{  cout << a.x << endl; }

int main()
{
    A a;
    display(a);
}
```
`2. friend declaration / scope`

`class / non-mem func 设为 friend 时, 不必 forward declaration, 该 friend declaration 就相当于 class / non-mem func 的 global declaration => 其后 其他想 引用 该 class / non-mem func name 的 地方 可直接引用`
```
class X
{
	//1) global declaration of class Y
	friend class Y;
	
	friend void f() 
	{ 
		//2) ok to `define friend func` inside class body
	}
};

class Z
{
	//3) declaration of class Y 
	// introduced by friend in X
	Y* py; 
	
	void g() 
	{
		//4) declaration of f introduced by friend in X
		return ::f(); 
	}
};
```

```
3. keyword friend 位置
1) 只能放 本 class A 内`
2) friend `不是 本 class 成员` 
=> friend 放在 本 class public / protected / private 区都一样
```

#7. static member

```
1. `vs. global obj`

1)可实现 封装, `可为 private`, 而 全局对象 不行
2)与 特定类 相关, 可以 清晰地表达程序的意图
```

`2. vs. non-static mem`

属于 class / obj

`static mem 虽然不属于 任何 obj, 但可以 通过 obj / pointer or reference that point to obj 访问`

`3. 访问 / 使用`
```
(1)类内: 直接用

(2)类外:
```
`1)用 class name:: 直接 访问/调用`

`2)用 
obj / pointer or reference that point to obj` 间接 访问 (调用) , 此时 **`obj 的 地址 不会传入 static mem func`**

`4. static 位置`

`只能 放 class 内 声明 开头`, 不能放 类外 定义 前

**5. static mem func**

```
`static mem func 属于 class, 而 不属于 任何 obj`
=> 
```
**`所有 依赖 this 指针 的 operation 都不支持:`**

`1) 没 this 指针:` 因 `this 指向 mem func 所属 obj`

`2) 不能是 const:` 因 const mem func 会 `read` mem func 所属 `obj ( 通过 this )`

`3) 不能是 virtual`

因 virtua 将开启 `动态绑定 ( 通过 this )`, 根据 `obj` 的 `dynamic type 选择 调用的 func`

```
//eg1. static mem func
#include <iostream>
#include <string>
class Account
{
public:
    //2) declaration
    static void setRate(double);

    //3) static mem func: `const isn't allowed`,
    // otherwise, compile error
    static double getRate() //const
    {
        return interestRate;
    }

    //---
    Account() : amount(0), owner("") { }
    Account(int _amount, std::string _name) : amount(_amount), owner(_name) { }
    void update()
    {
        amount += amount * interestRate * 0.01;
    }
    double getAmount() const { return amount; }
private:
    static double interestRate;

    //---
    std::string owner;
    double amount;
};
double Account::interestRate = 4.9;

//1) define
// static 位置: 只放 class 内 mem 声明处
void Account::setRate(double newRate)
{
    //4) used to process static mem data
    interestRate = newRate;
}

int main()
{
    Account::setRate(5.3);
    std::cout << Account::getRate() << std::endl;

    //---
    Account a(100, "lilei");
    Account* p = &a;
    p->update();
    std::cout << p->getAmount() << std::endl;
}
```

**6. static mem data**
```
(1) 声明: class 内
```
`(2) 定义: 必须在 class body 外 正好 定义 1 次 => 最后的办法是 定义放在 包含 class 的 non-inline mem func 定义 的 文件中`
 
`(3) 其 初始化 2点 特别`

`1) 其 初始化 有 1 种情形 可放类内 ( 其余 都放 类外 ):` mem data 是 `static const 整型` 时,  是 `常量表达式` 
=> 可放 `class 内 初始化`
=> **`初始化 不是 定义时 完成, 这和通常的理解矛盾 ( 记 住 )`**

```
该情形下, 
`类外定义 时 不能 再 指定初值`
```

`2) 其 初始化 有 1 种 情形 是通过 ctor 完成 ( 其余 都不是 ): 其 type 可以是其所属 class type 时`

```
//eg2.1 static mem data: 初始化 放 类内
#include <iostream>
class Account
{
public:
    static double getPeriod() { return period; }
private:
    //2)declare + initialization :inside class
    static const int period = 30;

    double daily_interest[period];
};

//1) define outside class
const int Account::period; 

int main()
{
    std::cout << Account::getPeriod() << std::endl;
}
```

```
//eg2.2 static mem data: 初始化 通过 ctor 完成
// prototype 设计模式 & 单例 设计模式
#include <iostream> 
class Image
{};

class ImageOne : public Image
{
private:
    //2) only declaration
    static ImageOne imageOne; 

    ImageOne()
    {
        std::cout << "hello\n";
        //addPrototype(this);
    }
};

//1) define and initiation:
//3) `call private ctor`
ImageOne ImageOne::imageOne; 

int main()
{ }
```

`(4) 其 type 可以是 其所属 class type,  non-static mem data 则 不行`
```
1) class 不能有 `自身类型` 的 `non-static mem data`

2) class name 一出现 就认为 class 已 声明 =>
class 可以有 自身类型 的 `pointer / reference`
```

```
// eg. prototype 设计模式 & 单例 设计模式
// 见 前一个例子 
class A
{
private:
    static A a1;
};
```

`5) 可 作 default arg, non-static mem data 则不行`
`reason: static mem data 可 独立于 object 而 使用`
```
class A
{
public:
    void setCh(char = ch);
private:
    static const char ch = ‘#’;
};
```

**7. static mem 继承**
```
遵循一般的 继承 访问控制
```

**`父/子 类` 的 `static mem data 共用存储空间`**
```
eg. prototype 设计模式
```
