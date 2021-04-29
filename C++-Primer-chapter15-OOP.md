#1 OOP

`1. virtual mem func 动态绑定`

`C++ 中, 通过 base class's reference / pointer 调用 virtual function` 时, 发生 `动态绑定:` 1) 在 `运行时 确定` 要 `调用的是 base class 还是 derived class 定义的 virtual function,` 2) 并 `根据 reference / pointer 所指 实际 object 的 class type 调用` 相应 virtual function
```
(1) reference / pointer 可 point to 
base class object 或 derived class object

(2) 为什么 必须通过 base class's reference / pointer 
调用 virtual function 才可以实现 动态绑定?

key point: 只要理解                                                                                                                                                      中 
`reference / pointer to obj of class 
在 使用时 会发生什么`
```
`每个 derived class's obj 都 包含 base class part`
=> 
`reference of / pointer to base class 可 bind / point` to `derived class's obj ( 的 base class part )`
=>
**`对 reference of / pointer to base class,` 编译器 `编译时 不知道 ( 运行时 才知道 ) 其 指向 的是 base class's obj  还是 derived class's obj,` 索性 `将 指向的 实际对象 当作 base class's obj`**

`(3) 覆盖 virtual function`
```
1) `mem func 中 才应该使用 scope operator :: 
覆盖 virtual function`
```
`2) derived class's virtual function 调 base class's version 时, 必须 explicitly 用 scope operator :: ; 否则 运行时 确定 调用 自身 => 无穷递归`

`(4) virtual function 与 default arg`

`virtual function 的 default arg 在 编译时 确定`
```
, 在 运行时 传给 `实际 obj 的 version`

=> 

若 `同一 virtual function` 的 
`基类 version 和 derived 类 version` 中 
用 `不同的 default arg, 几乎 一定会 引起麻烦`
```

`2. non-virtual mem func 静态绑定`
```
`non-virtual mem func` 总是在 `编译时` 
`根据 调用 该函数的 obj / reference / pointer 
的 type 而 确定`
```

#2 base class & derived class: inheritance hierarchy

`1. virtual ( keyword: 虚 )`

`(1) 只 放 class 内 declaration 处`
```
, 不放 class 外 definition 处
```

`(2) 放 return type 前`
```
(3) 只用于 `non-static` mem func
```
`(4) 若 class explicitly / implicitly ( 从 其 base calss 继承 ) declare virtual ( function ), 则 derived class 会 继承 virtual, 无论 derived class 中 是否 explicitly declare virtual`
```
code 中 最好 explicitly declare, 
免得 还得逐级 确定 base class 
相应 mem func 是否 为 virtual
```
`2. 访问 控制`

```
class 的 3 种 client & `最小 访问权限`
```
`1) class's client: 只能访问 public mem, 通过 class's obj`

`2) class mem: 可访问 private mem, 直接访问`

`3) derived class: 可访问 protected mem, 通过 derived class's obj`

`3. C++ obj 内存模型: 不要求` 编译器 将 `obj 的 base class part 与 derived class part 连续排列`

`4. derived class 必须知道 base class's mem => 已 定义的 class 才可以作 base class`

`5. interface 继承 与 implement 继承`
```
interface 继承 /  implement 继承: 
即 public 继承 / private protected 继承:
继承 base class 的 part 成为 derived class 
interface 的 part / implement 的 part
```

```
class / struct: default 继承 为 private / public 继承
```

`6. friend 关系 与 继承: friend 关系不能 继承`

`7. static mem 与 继承`

`(1) 若 base class 中 定义了 static mem`
=> 往下的 `整个 inheritance hierarchy 中 该 static mem 只有 1 个 instance`
```
(2) static mem 遵循 常规访问控制, 可用 3 种 operator 访问:
```
`:: . ->`

#3 conversion 与 继承

```
`1) reference / pointer` to derived class 
    -> reference / pointer to base class:
`automatically conversion`

`2) obj` of derived class -> obj of base class:
`一般可 初始化 / 赋值, 但, 无 directly conversion`
```

**1. derived class ->  base class 的 conversion**

**(1) reference conversion vs. obj conversion**


`1) 将 obj 传给 接受 reference 的 function 时, reference 直接 绑定到 该 obj`
=>
`形式上 看似 在 pass obj, 实际上 arg 是 reference of obj, obj 本身 未被 copy`

```
2) 将 derived class's obj 传给 接受 para 为 
base class's obj 
的 function 时, 
```
 `derived class's obj 的 base part 被 copy 到 para`

**(2) derived class's obj -> base class's obj: 初始化 / 赋值**

`实际是 调用函数: 初始化 / 赋值 时, 调用 ctor / operator=`

```
2 种 secenario:
```
```
1) base class `ctor / operator=` 的 
para 为 ( const ) reference of derived class:
```

```
class Derived;
class Base
{
public:
	Base(const Derived&);
	Base& operator=(const Derived&);
};
```

```
2) base class `copy ctor / operator=` 的 
para 为  ( const ) reference of base class:
```
`1> Derived obj 转换为 reference of Base class: reference of Base class 绑定到 Derived obj`
`2> 该 reference 作 arg 传给 copy ctor / operator=`
`3> 用 Derived 的 Base part 调 copy ctor / operator= 进行 初始化 / 赋值`

```
class Base
{
public:
	Base(const Base&);
	Base& operator=(const Base&);
};
```

**(3) derived class -> base class conversion 的 可访问性**

`derived class -> base class conversion 是否可访问, 只要看 base class 的 public mem 是否可访问`

`1) 无论 public / private / protected 哪种 继承, 派生类 本身 都可以 访问 base class 的 public mem => 派生类 本身的 mem 和 friend 都可以访问 derived class 到 base class 的 conversion`

`2) public 继承: client 和 public 继承类 的 后代类 可访问 derived class 到 base class 的 conversion`

`3) private / protected 继承: client 不能将 derived class 转换为 base class`

`4) private 继承: private 继承类 的 后续派生类 不能转换为 base class`

`5) protected 继承: protected 继承类 的 后续派生类 可以转换为 base class`

**2. base class -> derived class 的 conversion**

`不 自动转换, 因为 base 类 不包含 derived 类 的 mem`

#4. ctor 和 copy control

`ctor 和 copy control mem 不能继承, inheritance hierarchy 中 每层上的 class 定义自己的 ctor 和 copy control mem`

**1. ctor**
`(1) ctor initialization list 为 base class 和 自身 mem 提供 初值, 但不指定 初始化次序.`
`初始化 次序: 先 初始化 base class, 再 根据 declaration 次序 初始化 derived class mem`

`(2) class 只能 初始化 自己的 直接基类`

`reason: 每个 class 都定义了 自己的 interface, 与 该 class obj 的所有 交互 都应该 通过该 nterface, 即使 交互方 是 derived class obj`

`(3) 重构 ( refactoring ) 的 1 种方法: 改变 class 层次, 将 mem data / function 从 1 个 class 移到 另 1 class`

**2. copy control 与 继承**

`(1) Derived class 的 copy ctor / operator= 负责 对 base class part 与 自身 mem 进行 copy / assign: 要 explicitly 调用 base class ctor / operator= 与 自身 mem 初始化式 / 赋值式`

```
若 不 explicitly 调 Base class copy ctor / operator=, 
=> 编译器 调 Base class default version 
=> 通常不是期望的结果
```

`(2) Derived class 的 dtor 不负责 撤销 base class obj 的 mem, 编译器 自动调 base class dtor`

```
class Base
{
public:
	Base(const Base&);
	Base& operator= (const Base&);
};

class Derived: public Base
{
public:
	//(1)
	//1) Base::Base(const Base&) not invoked automatically
	Derived(const Derived& d) :
		Base(d) // copy ctor base part
		/* other mem initialization */
	{
		/*  */
	}

	Derived& operator= (const Derived &);
};

//(2)
Derived& Derived::operator= (const Derived& rhs)
{
	//2) Base::operator= (const Base&) not invoked automatically
	if (&rhs != this)
	{
		Base::operator=(rhs); // assigns base part

		// clean up old value in derived part
		// assign mem from derived
	}
}

//(3)
class Derived : public Base
{
public:
	//3) Base::~Base invoked automatically
	~Derived()
	{
		// clean up derived mem
	}
};
```

**3. virtual dtor**
```
编译器 调用 derived class dtor 前, 
先 自动调用 base class dtor
```
`(1) 若 class 可能被 继承, 则 dtor 要设为 virtual`
```
reason: 若 dtor 设为 non-virtual, 则 
```
`delete 指向 派生类 obj 的 基类 pointer 时, 调 基类 dtor => 非预期`

`(2)即使 dtor 没 工作要做, inheritance hierarchy 中 root class 也应该有 virtual dtor`

```
(3) 三法则 的 1 个 例外
若 基类 为了 将 dtor 设为 virtual 而具有 empty dtor,
则 该类 有 dtor 不表示 也需要 copy ctor / operator=
```

**4. ctor 和 operator= 非 virtual**

`(1) ctor 不能为 virtual, 因 ctor 运行时 obj 的 dynamical type 还不 完整`

`(2) 每个 class 都有自己的 operator=, para 为 该 class type 自身 的 reference => 作 virtual 会 令人混淆, 且 没有用处 `

**5. ctor / dtor 中的 virtual function**

**`construct / destruct 时, obj 不完整 => base class ctor / dtor 中, 编译器 只能看到 derived obj 的 base class part, derived class part 未初始化 / 已 destruct => 编译器 将 derived obj 视为 base obj`**

**`=> 若 ctor / dtor 中 直接 或 间接 调用 某 virtual function, 则 运行的是 ctor / dtor 自身 class 定义的 version`**

```
考虑 从 基类 ctor / dtor 调 virtual function 
的 derived class version 的情形 就能容易理解
```

```
(1) construct derived obj 时, 
先 调 base class ctor 构造/初始化 obj 的 base class part

(2) destruct derived obj 时, 
先 destruct derived class part, 
再 按 构造顺序的逆序 destruct 其 base class part
```
#6 继承下的 class scope
```
1. 继承下 派生类 scope 嵌套在 基类 scope 中, 
若 派生类 scope 中 找不到 name,
就到 外围 基类 scope 中 找
=> 派生类 中可 直接访问 基类 mem

// eg
Base b;
Derived d;
d.f(); // f() 只在 Base 中 定义

(1) 在 Derived 中 找 name f, 没找到 
(2) 在 相应的 Base 中 找 name f, 找到, 确定
```

`2. 继承下 的 名字冲突`

(1) 与 基类 mem `同名` 的 派生类 mem 将 `屏蔽 ( hide )` 对 基类 mem 的 `直接访问`; 若 想 间接访问, 可用 `scope operator ::`

```
(2) 设计 derived class 时, if possible, 
最好避免与 base class mem 同名
```
 
**3. scope 与 mem func**

(1) local scope 声明 的 function 不重载 global scope 定义的 function; `派生类 定义的 mem func 也不重载 基类 定义的 mem func`

(2) `派生类 没定义` function 时, 才考虑 基类 function;
`派生类 重定义 重载 mem func` 时, 若不用 using, 则 通过 派生类 `只能访问 派生类 中 重定义 的 mem func`

(3) 派生类 重定义 重载 mem func, `但 还想 访问 基类 相应 mem func, 可用 using`

**4. virtual function 与 scope**
```
class Base
{
public:
	virtual int func();
};

class D1 : public Base
{
public:
	//1) hides func in the Base;
	int func(int);

	//2) inherits definition of Base::func()
};

class D2 : public D1
{
public:
	//3) hides D1::func(int)
	int func(int);

	//4) redefines virtual func from Base
	int func();
};

int main()
{
	Base b;
	D1 d1;
	D2 d2;
	Base* pb1 = &b, * pb2 = &d1, * pb3 = &d2;

	// virtual func
	pb1->func(); // call Base::func() at run time
	pb2->func(); // call Base::func()
	pb3->func(); // call D2::func()

}
```

`5. 名字查找 与 继承: 4步`
`(1) 确定 进行 函数调用 的 obj / reference / pointer` 的 `静态类型`
`(2) 在 该 类型 中 查找 name`, 找不到 就沿着 继承体系 一直往上找, 若 找到
`(3) 常规类型检查`, 看 调用是否合法, 若合法
`(4) 编译器 生成 code`
```
1) 若 为 virtual + reference / pointer 调用 
=> 
```
`编译器 生成 code 以 确定 根据 obj 的 动态类型 调用哪个 function version`

`2) 否则, 编译器 生成 code 直接调用函数`

#6 纯虚函数 & 抽象基类

`1. 纯虚函数 ( pure virtual function )`
`(1) 不 define`
`(2) 本 class 该 function 没有实际意义`
`(3) 为 后代类 提供 可以 覆盖 的 interface`
`(4) 可防止 client 创建 本 class 的 obj`

`2. 抽象基类 ( abstract base class ): 含有 或 继承 >= 1 个 纯虚函数 的 class, 不能创建 obj`

#7 容器 与 继承

```
目标: 想用 容器 保存 继承 相关的 object
限制: obj 非 多态
```
`solution: 用 容器 保存 pointer to obj`

```
代价: 
```
`1) 用户 要确保 只要 容器存在, 被指向的 object 就存在`
`2) 若 obj 动态分配, 用户 要确保 容器 消亡时 适当地 释放 obj`

#8 句柄 ( handle ) 类 与 继承
```
`C++ OOP 有讽刺意味的地方是 不能用 obj 支持 OOP`, 
必须用 reference / pointer
```

`1. handle class: store 和 manage 基类 pointer => virtual mem func 行为 在 运行时 根据 handle class 实际指向 的 object 的 type 而变`

```
handle 的 client 可以获得 动态行为,
但 无须操心 pointer management
```
