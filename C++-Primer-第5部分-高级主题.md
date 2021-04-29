```
key points:

chapter 17 tools for large scale program
1. 异常处理

2. RAII (资源分配即初始化) & auto_ptr 

3. namespace

4. 多重继承 虚继承

chapter 18 special tools and techniques
1. `class` 怎样自定义 `优化内存管理`

2. RTTI (运行时 类型 识别)
   支持 在 运行时 确定 obj 的 实际类型
   
3. pointer to mem of class
    与 ordinary obj/func 的 pointer 异同:
	同: 根据 obj / func 的 type 而变
	异: pointer 还必须 反映 所属 class

4. 3种聚合 type
	联合 class : 节省空间
	嵌套 class : 
	local class:
	
5. 固有的 不可移植的 特征
	volatile
	链接指示
```

#chapter 17 tools for large scale program

##1 异常处理

`1. 意义`

`分离` code 为 `exception check` 和 `exception process` 2个部分, exception check 部分 `throw/pass object of exception` 给 exception process 以 `catch 该 obj 并处理`

```
A
operator+(const A& lhs, const A& rhs)
{
	if (!lhs.same_isbn(rhs))
		throw runtime_error("Data must refer to the same ISBN");
	// ...
}

A a1, a2, sum
// assign a1 a2 
try
{
	sum = a1 + a2;
	// use sum
}
catch (const runtime_error& e)
{
	std::cerr << e.what() << std::endl;
}
```

`2.  throw / catch`

`2.1 exception 通过 throw obj 而 引发`, 该 `obj 的 type 决定 激活哪个 catch: 匹配 且 最近`

`2.2 执行 throw 时`, throw 后的 code 不 执行, 而是将 `control 转移到 匹配的 catch`

```
(1) 匹配 catch 的过程
1) 先在 `同一 function` 中 找 match
2) 再在 `direct / indirect caller function` 中 找 match
```
`(2) control 转移 的 2 层含义`
```
1) 沿 调用链 的 function 提早退出: 栈展开 ( stack unwinding )
```
`2) 一般, process exception 时, throw block 中 local storage 不再存在`
```
reason: process exception 时, 会 释放 local storage

=> the thrown obj 不能再 local stored, 而是 
```
`用 throw expression 初始化 1个 exception object`
```
(3) exception object: 
```
`1) 由 compiler 创建`

`2) 初始化 为 copy of the thrown expression`
```
=> result of the thrown expression / object 
必须是 `可 copy type`
```

`3) 驻留在` 可能被 激活的 `任意 catch 都能访问的 space`
```
4) catch 处理完成后 撤销
```

`3. exception object 与 继承`
```
`(1) thrown expression's type 往往来自 某个 继承层次`
```
`(2) the thrown expression / object 的 静态类型 决定 exception object 的 type`

`4. exception object 与 pointer`
```
2 个 问题:
```
`(1) 抛出的 pointer 的 type 与 所指 obj 的 type 不同 时, exception object type match static type of pointer => 若 Base pointer 指向 Derived obj, 则 Derived obj 被 分割, 只 throw base part`

```
(2) `抛出 pointer 时, 应该在 对应 catch 处 存在 pointer 指向的 obj` 

若 抛出的 pointer 指向 local obj, catch 中 local obj 很可能 不再存在
=> 抛出 poiner 通常是个 坏主意
```

`5. 栈展开 ( stack unwinding )`

`throw expression 时, 将 suspend 当前 function 的 执行, 沿 nest function 调用链 向上寻找, 直到 找到 matched catch`
```
(1) 栈展开 期间,
1) 调 class type's dtor
2) 释放 local obj memory
```

`(2) dtor should never throw exception`

`(3) ctor usually throw exception`
`即使 obj 已部分构造, 也要保证 撤销 已构造 mem`

`(4) exception 必须被 process, 否则, program 调 库函数 terminate`


`6. catch exception`

`通常, 若 catch 处理的 exception 是 inheritance hierarchy's type,` 
`1) catch 的 para 应为 reference, 以 反映 hierarchy/layer of the calss type`
`2) 若有 多个 catch, 必须从 最低 到 最高 派生类型 排序`

```
catch(exception& e)
{
	// do cleanup
	// print a message
	std::cerr << e.what() << std::endl;
	size_t error_code = 10;
	return error_code
}
```

`7. rethrow`
`将 exception 传给 function 调用链 中 更上层的 function, 形式为`
```
throw;
```

```
catch(exception& e)
{
	 e.status = severe_err;
	 throw;
}
```
`8. call-all`

`(1) 用于捕获所有异常, 可 match 任意 type 的 exception, 经常与 rethrow 结合使用, 形式为`

```
catch(...)
{
	// work to partially handle the exception
	// throw;
}
```

```
(2) 与 other catch 结合使用 时, 
catch(...) 必须是 最后 1 个, 
否则, 其后的 catch 不会被 match
```
`9. ctor 与 function try block`

`ctor 处理 初始化式 的 exception: 将 ctor 写为 function try block`

```
template <class T>
Handle<T>::Handle(T* p)
try: ptr(p), p_ref_count(new size_t(1) ) 
{
	// empty function body
} 
catch( const std::bad_alloc& e) 
{
	handle_out_of_memory(e);
}
```

`10. 标准 exception class 层次`

![image.png](https://upload-images.jianshu.io/upload_images/20172887-6fd814e0acad11b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`11. exception specification`

`(1) 指定 若 function throw exception, 被抛出的异常 将是 包含在 该 specification 中的 1 种, 或 其 派生 type`

```
(2) 形式
```
```
throw 后跟一个 圆括号 括住的 exception type list 
( maybe empty : 不抛出 exception ) 
```
```
void f(int) throw (runtime_error);

void no_problem() throw();
```

`(3) 运行时 才 检测 exception specification 是否被 违反`

`(4) function 若 保证 不抛出 任何 exception`
```
则,
1) client 可以 call function safely
2) 编译器 感知 到, 就可以 做相应 优化
```

##2 RAII & auto_ptr 

`1. 自动 释放资源`


`(1) non-dynamic local objcet / dynamic object`
```
`dtor 自动 ( 由 compiler 调用 ) 运行`, 
以 自动撤销 non-dynamic local objcet / 
`必须 explicitly delete pointer to dynamic obj`, 
以 explicitly 撤销 dynamic obj
=> 
```
`若 new 之后 delete 之前 抛出 exception, 则 delete 被跳过, dynamic obj 没被撤销 / dynamic memory 未被回收`

```
void f()
{
	std::vector<string> vec; //1) local vector
	std::string s;
	while (cin >> s)
		vec.push_back(s);   // populate vec

	//2) dynamic array
	std::string* p = 
		new std::string( vec.size() );

	// process

	//3) it's possible that an exception occurs

	//4) cleanup is bypassed if an exception occurs
	delete[] p;
}   //5) vec destoryed automatically when function exits
```

```
`如何解决 the Problem that exception 引起的` 
`delete 被跳过, dynamic obj 没被撤销 ?`

机制: `利用 dtor 自动运行 的 RAII 机制`
```

`2. RAII ( Resource Acquisition Is Initialization 资源分配即初始化 )`

**`RAII: 用 1 个 class 封装 Resource 的 分配 和 释放, 使得 ctor 中 分配 Resource, dtor 中 释放 Resource. 即使 构造 Resource obj 之后, 超出其 scope 之前 发生 exception, dtor 也会 ( 因 作为 exception 处理 的 一部分 而 ) 自动运行. 使 program 更 exception safe`**
```
若 无 exception, Resource obj 超出其 scope 时, 
dtor 自动运行
```

```
`optimal practice`:
`可能存在 exception / 分配资源 的 program 应该 用 RAII class`
```

```
class Resource
{
private:
	//1) resource managed by this type
	resource_type* p_res;

	// private practical func
	//2) allocate this resource
	resource_type* allocate(Paras p);

	//3) free this resource
	void release(resource_type*);
public:
	Resource(Paras p) : p_res( allocate(p) ) {}
	
	~Resource() { release(p_res); }

	// also need to define copy and assignment
};

void f()
{
	Resource res(args); //1) allocates resource_type

	// code that might throw an exception
	//2) if exception occurs, dtor for res is run automatically
	// ...
}   //3) res goes out of scope and is destoryed automatically
```

`3. auto_ptr: class template`
```
RAII 技术的 例子
```
```
// in <memory>
```
```
(1) default ctor
internal pointer = 0 
=> unbound
```

```
(2) 单 para explicit ctor
auto_ptr<T> ap(p_t)
```
the stored `internal pointer can point to any type specified by the single para`

**`必须 explicitly call 单 para ctor 以 初始化 auto_ptr obj, 而不能 用 =`**

```
auto_ptr<std::string> ap(new std::string("hello") );
```

```
(3)copy ctor / operator=
auto_ptr<T> ap1(ap2); / ap1 = ap2;
```
`将 ap2 指向的 obj 的 ownership 转移给 ap1 / 非 自身赋值 时, 1)delete (if exist) obj pointed by ap1   2)将 ap2 指向的 obj 的 ownership 转移给 ap1`
```
=> ap2 become unbound
```
**`copy 和 assignment 会使 右 auto_otr obj 置为 unbound`**
```
=> note1:
```
**`不能 将 auto_otr obj 存于 标准容器 中, 因 标准容器 要求 copy 和 assignment 后, 2 个 obj 相等`**

```
(4) dtor
~ap : delete obj pointed by ap
```

```
(5) ap.get()
return the stored internal pointer
```

`get() 只应该 用于 使用 return 的 internal pointer, 不能 作 arg 调 单 para explicit ctor, 否则  get() 相应的 auto_ptr obj 和 构造的 新 auto_ptr obj 都 指向 同一 给定 obj`
```
=> note2:
```
**违反 `auto_ptr 设计原则:`**
**`任意时刻, 给定 obj 只能 由 1 个 auto_ptr obj ( 的 internal pointer ) 指向, 若 由 2 个 auto_ptr obj ( 的 internal pointer ) 指向, 则 the same internal pointer 会被 delete 2 次`**

```
(6) ap.release()
return `wrapped ( stored ) pointer` and `give up ownership`
=> ap become unbound
```

```
(7) reset(p)
1) 若 p == ptr stored in ap, 啥也不干
2) 否则, 
delete (if exist) obj pointed by ap 
+ make ap point to obj pointed by p
```

```
(8) * deference / ->

*ap  : return `reference of the obj` pointed by ap
ap-> : return `internal pointer` stored in ap
```
```
auto 主要 2个作用:
1) RAII
2) 支持 ordinary pointer 的 * -> 操作
```

```
(9) note3:
```
**auto_ptr obj 的 `internal pointer 只能指向 单个 obj, 不能指向 动态分配的 array, 否则, 因 dtor 中 时 delete ptr, 而非 delete [] ptr`
=> `只能 释放 1 个 obj`**

`(10) auto_ptr 的 4个缺陷`
```
前面 3个 note + 不要指向 静态分配 obj

(1) `auto_ptr obj 不要指向 静态分配对象`, 否则, 
`auto_ptr obj 撤销时, 试图 delete 给定 obj`
=> `undefined bebavior`

(2)不能 将 auto_otr obj 存于 标准容器 中, 
因 标准容器 要求 copy 和 assignment 后, 2 个 obj 相等

(3)任意时刻, 给定 obj 
只能 由 1 个 auto_ptr obj ( 的 internal pointer ) 指向, 
若 由 2 个 auto_ptr obj ( 的 internal pointer ) 指向, 
则 `the same internal pointer 会被 delete 2 次`

(4) auto_ptr obj 的 `internal pointer 只能指向 单个 obj, 
`不能指向 动态分配的 array`, 否则, 
`因 dtor 中 时 delete ptr, 而非 delete [] ptr`
=> `只能 释放 1 个 obj`
```

```
// source code of auto_ptr
#include <iostream>

// STL : <memory>
template<class T>
class auto_ptr
{
private:
    T* ptr;
public:
    //(1) default ctor
    auto_ptr() : ptr(0) {}

    //(2) explicit single para ctor
    explicit auto_ptr(T* p = 0) : 
        ptr(p) {}

    //(3) copy ctor
    auto_ptr(auto_ptr<T>& rhs) : 
        ptr(rhs.release()) { }

    // operator=
    auto_ptr<T>& operator=(auto_ptr<T>& rhs)
    {
        // avoid self assignment
        if (&rhs != this)
            reset( rhs.release() );
        return *this;
    }

    //(4) dtor
    ~auto_ptr() { delete ptr; }

    //(5)
    T* 
    get() const { return ptr; }

    //(6) release  
    // return wrapped pointer and give up ownership
    // note : release ownership of the given obj
    //        not release memory of the given obj
    T* 
    release()
    {
        T* tmp(ptr); //1) record old ptr
        ptr = 0;     //2) ptr = 0 
        return tmp;  //3) return old ptr
    }

    //(7)
    void 
    reset(T* p)
    {
        if (p != ptr)
            delete ptr;
        ptr = p;
    }

    //(8)
    T& operator*() const { return *ptr; }
    T* operator->() const { return ptr; }
};

int main()
{
    // ctor
    auto_ptr<int> sp1(new int(0));

    // copy ctor
    auto_ptr<int> sp2(sp1);

    // operator=
    auto_ptr<int> sp3(new int(1));
    sp1 = sp3;
}
```
![image.png](https://upload-images.jianshu.io/upload_images/20172887-65749e5f87191ded.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##3 namespace

```
1. 引出 namespace 的 意义:

库 倾向使用 global name (template / type / function name)
-> 多库 code Problem: 
`name conflict / namespace pollution`

solution1: 加长 name -> hard to write / read

solution2: namespace 的 各种机制 
```
`namespace 能 划分 global namespace: 在 namespace 内部 定义 库中 name, 可 avoid global name 固有限制`

`2. namespace definition`
```
(1)  namespace definition 以 namespace ( keyword ) 开始
```

```
namespace namespace_name
{
	// ...
} // note: there is no ; here
```

`(2) 1 个 namespace 是 1 个 scope`


`(3) namespace 可 叠加 => 可 分离 interface 与 implement 于 不同 file`

```
// file1.h
namespace namespace_name
{
	definitions of class + 
	declarations of members/non-members of class 
} 

// file2.cpp
namespace namespace_name
{
	definitions of members of class 
} 
```

```
//eg.
// Sales_item.h
namespace cplusplus_primer
{
    class Sales_item
    {
        /* ... */
    };
    
    Sales_item operator+(const Sales_item&,
                         const Sales_item&);
}  

// Sales_item.cc
#include "Sales_item.h"
namespace cplusplus_primer
{
	//definitions for Sales_item members 
	//	and related functions
} 
```
`(4) namespace 内部 使用 namespace's mem name: 可 不用 namespace_name:: 限定`

`namespace 外部 定义 namespace mem, 类似 class 外 定义 class mem: 要用 namespace_name:: 限定` 
```
cplusplus_primer::Sales_item 
cplusplus_primer::operator+(const Sales_item& lhs,
				      const Sales_item& rhs)
{
	Sales_item res(lhs);
	// ...
}
```

`(5) global namespace 是 implicitly 声明 的, 无 name => 用 ::member_name 引用其 member`

`3. nest namespace`

```
是 1 个 nest scope
```

`4. unnamed namespace`

`(1) 定义 局限于 特定 file, 从不 跨越 多个 file`

```
=> 可在 给定 file 中 叠加, 但不能 跨越 file
```

**`(2) 若 头文件 定义了 unnamed namespace, 则 每个包含该头文件 的 文件 中, 该  unnamed namespace 中的 name 将 定义 different local 实体`**

`=> 引用 unnamed namespace 意义: 取代 static declaration`
`声明 为 static 的 name 局限于 该文件`

`5. use namespace's member`

`namespace 外 use / 引用 namespace's member 的 4 种 方法:`

```
namespace std
{
    template<class T， class Alloc = alloc>
	class vector
	{
		/*  */
	}
}

//(1) 用 namespace_name:: 限定
std::vector vec;

//(2) namespace alias (别名): 用 短 name 代替
namespace st = std; 
st::vector vec;

//(3) using indication
using namespace std;

//(4) using declaration
using std::vector;
vector vec;
```

```
cplusplus_primer::Sales_item item;

namespace primer = cplusplus_primer; 
primer::Sales_item item;

using namespace cplusplus_primer;
Sales_item item;

using cplusplus_primer::Sales_item;
Sales_item item;
```

`(1) 用 namespace_name:: 限定`

```
cplusplus_primer::Sales_item item;
```

`(2) namespace alias (别名): 用 短 name 代替`

```
namespace primer = cplusplus_primer; 
primer::Sales_item item;
```

`(3) using indication`
`1) 1 次 引入 所有 member of the namespace => 使 namespace 中 所有 member 可见`

```
=> 
使用 多库 时, 会引起 name conflict
```

**`2) 将 namespace's member 提升到 包含 namespace, 且 与 using declaration 的 最近 scope`**

`3) using indication 有用 的 1 种 case 是, 用在 namespace 本身的 implement 文件中`

```
4) eg
```

```
namespace cpp_primer
{
    int a = 1;
}

int a = 2; //1) hide a inside cpp_primer

void f()
{
    using namespace cpp_primer;
    
    ++a;  // 2)error: ambiguous, global a or cpp_primer::a
    
	++::a; //3)
	++cpp_primer::a; //4
	
    int a = 5; //5) hide global a and cpp_primer::a
}
```

`(4) using declaration`

`1) 1次 只 引入 1 个 member of the namespace => 使 namespace 中 指定的 member 可见`

```
2) 可用于 4 种 scope: 
global / local / namespace / class scope
```
**`class scope 中的 using declaration 局限于 该 class 的 base class 中 定义的 name`**

```
(5) note:
```
**`1) scope 小 的 namespace 中 的 name hide scope 大 的 namespace 中 的 同名 name`**

**`2) 某 scope 中 可见 另 2 个 scope 中的 同名 name => name ambiguous`**

`6. class / namespace / scope`

`(1) 若  function / overloaded operator` 
`1) para 为 class type / reference or pointer to class type`
`2) 或者, 与 class 定义于 同一 namespace`
`+ arg 为 object of class type / reference or pointer to class type 时, 该 function / overloaded operator 是 可见 的 => 可以不用 namespace_name:: 限定`

```
//eg.
std::string s;

// ok: calls std::getline(std::istream&, const std::string&)
getline(std::cin, s);
```

`(2) implicitly friend declaration 与 namespace`

`若 不存在 可见的 ( 同名 ) 声明,  则 friend declaration 将 function / class declaration 放入 ouside scope`

```
namespace A
{
    class B
	{
		// make f a member of namespace A
		friend void f(const B&);
	};
}

// <=>
namespace A
{
	class B
	{
		//1) friend declaration 
		// put function / class declaration ouside scope
		friend void f(const B&);
	};

	//2) friend declaration put function declaration ouside scope
	void f(const B&);
}
```

```
#include <iostream>
namespace A
{
	class B
	{
		//1) friend declaration 
		// put function / class declaration ouside scope
		friend void f(const B&);
	private:
		int x;
	public:
		B() : x(10) {}
	};

	//2) friend declaration put function declaration ouside scope
	void f(const B&);
}

namespace A
{
	void f(const B& b)
	{
		//3) note: friend 可以通过 class's obj 
		// 直接 访问 class's private member
		std::cout << b.x << std::endl;
	}
}

void f2()
{
	A::B b_obj;

	//4) f's para is reference to class type,
	// + f's arg  is object of class type
	// => 该 function / overloaded operator 是 可见 的 
	// => 可以不用 namespace_name:: , 即 A:: 限定
	f(b_obj); // calls A::f(b_obj);
}

int main()
{
	f2();
}
```

`7. 重载 与 namespace`

```
每个 namespace 维持自己的 scope
different namespace 的 mem func 不能重载
给定 namespace 内 mem func 可重载
```

`(1) 重载 operator 的 候选函数 与 namespace`

**`若 某 function 调用点处 可见 某 namespace, 则 即使不可见 该 namespace 中 与 自己 同名的 mem functions, 也 将他们加入 候选函数集`**
```
namespace NS
{
	class Base
	{
		/*  */
	};

	void display(const Base& b)
	{
		/*  */
	}
}

class Derived : public NS::Base
{
	/*  */
};

int main()
{
	Derived d;
	
	display(d);

	// <=>
	NS::display(d);
}
```
`(2) 重载 与 using indication`

```
using indication 将 namespace's member 提升到 
包含 namespace, 且 与 using declaration 的 最近 scope

=>
```
**`若 function of namespace 与 function with the same scope as namespace 同名, 则 也 重载`**

`(3) 重载 与 using declaration`

`using declaration ( 引入 function 时 ) 引入所有 具有 该 name 的 function`

`8. template 与 namespace`

`template 的 explicitly 特化 必须在定义 generic template 的 namespace 中 declare, 否则, 与 相应 generic template 不同名`

##4 多重继承 虚继承

https://www.jianshu.com/p/eb69e02766e7

#chapter 18 special tools and techniques
##1 class 怎样自定义 优化内存管理

`1. C++ 中的 memory management`


```
涉及 4 方面内容:
内存分配 + 对象构造
对象析构 + 内存回收

new / delete expression:
分配 内存 + 分配的内存中 构造 对象 /
调 dtor 撤销 对象 + 对象所用内存 回返给 系统

=> 接管 内存管理时, 必须处理 2个 任务:
1) `分配 (原始 未构造) 内存 后, 必须 在该内存中 构造对象`
2) `释放 内存 前, 必须 适当 撤销对象`
```

`(1) 分配 / 释放 未构造 (未 type 化) 内存`

```
1) allocator class: 提供 可感知 type 的 内存 分配/释放
allocate / deallocate ( mem func )
2) 标准库 中 operator new / operator delete:
```

`(2) 构造 / 撤销为 未构造内存`
```
1) allocator 的 construct / destory ( mem func )

2) placement new expression:
接受 pointer to 未构造内存,
调 相应 type's ctor 在该空间中 初始化 1 个 obj / array

3) 直接调 obj's dtor 撤销 obj

4) 算法 uninitialized_fill / uninitialized_copy 像 fill / copy 一样,
除了 在 dst 构造 obj, 而不是 assign obj
```

`2. allocator ( class template ) 及其 用于 管理 class's mem data`

```
// (1)
allocator<T> a;

a.allocate(n); 
分配 未构造内存, 以 store n 个 T 型 obj

a.deallocate(ptr, n); 
释放 T* 型指针 ptr 处 store 的 n 个 T 型 obj 的 未构造内存

a.construct(ptr, t)
在 T* 型指针 ptr 处, 构造 1 个 新 obj,
并 调 T 型 copy ctor 用 t 初始化 该 obj

a.destory(ptr)
调 T* 型指针 ptr 所指 obj 的 dtor 以 撤销 obj

//(2) 以 t fill 输入范围内 objs / fill 最多 n 个 obj /
// copy ctor
uninitialized_fill(beg, end, t) 
uninitialized_fill_n(beg, end, t, n) 

uninitialized_copy(beg, end, beg2) 
```

```
#include <iostream>
#include <memory>    // allocator / uninitialized_copy

// pseudo-implement of memory allocation strategy 
//for a vector-like class
// does not include destory & deallocate

template <class T>
class vector
{
private:
	// note: std::allocator<T> obj can't be static
	std::allocator<T> alloc;

	void reallocate();

	// pointer to first memory in the array / 
	// first free element in the array /
	// the one past the end of the array
	T* p_elements;
	T* p_first_free;
	T* p_end;

	//...
public:
	vector() :
		p_elements(0), p_first_free(0), p_end(0) {}

	void push_back(const T&);

	size_t size() const
	{
		return p_first_free - p_elements;
	}

	size_t capacity() const
	{
		return p_end - p_elements;
	}
};

template <class T>
void vector<T>::push_back(const T& t)
{
	// out of space?
	if (p_first_free == p_end)
		reallocate(); 

	alloc.construct(p_first_free, t);
	++p_first_free;
}

template <class T>
void vector<T>::reallocate()
{
	//1) compute current size => new space
	size_t sz = p_first_free - p_elements;

	// every time to reallocate, increase capacity by 2 times
	// 每次重新分配时, 2 倍 扩容
	size_t new_capacity = 2 * ( (sz > 1) ? sz : 1);

	//2) allocate space to hold new_capacity number of type T
	T* p_new_mem = alloc.allocate(new_capacity);


	//3) copy and construct elems in new space
	std::uninitialized_copy(p_elements, p_first_free, p_new_mem);

	//4) destory old elems in old space: in reverse order(逆序)
	for (T* p = p_first_free; p != p_elements; )
		alloc.destroy(--p);

	//5) deallocate old space: arg can't be 0
	if (p_elements)
		alloc.deallocate(p_elements, p_end - p_elements);

	//6) update pointers of new vector to new elems
	p_elements = p_new_mem;
	p_first_free = p_elements + sz;
	p_end = p_elements + new_capacity;
}

int main()
{
	vector<int> vec; // size = capacity = 0

	// p_first_free == end == 0 
	//=> 2 倍扩容 => size = 1， capacity = 2
	vec.push_back(1); 

	vec.push_back(2);

	// => 2 倍扩容 => size = new_size = old_capacity + 1 = 3， 
	// capacity = 2 * old_capacity = 4
	vec.push_back(3);
	std::cout << vec.size() << ' ' 
		<< vec.capacity() << std::endl;
}
```
```
vector: 
size: 实际使用 元素数 = p_first_free - p_elements
capacity: 必须重新分配 space 前, = p_end - p_elements
```
`free space: 必须重新分配 space 前, = end - p_first_free`

`3. operator new / operator delete function`

`(1) interface`
```
// in <new>
// allocate an object / array
void* operator new(size_t);
void* operator new [](size_t);

// free an object / array
void* operator delete(void*);
void* operator delete [](void*);
```

`(2) 作用`
`1) 为 标准库 function => 可 直接调用`

`2) 供 new / delete / new [] / delete [] expression 调用`

```
//1) 
// 2 组 等价
T* p_new_mem = alloc.allocate(new_capacity); 
T* p_new_mem = static_cast<T*>
	operator new []( new_capacity * sizeof(T) );
	
alloc.deallocate(p_elements, p_end - p_elements);
operator delete [] (p_elements);

//2)
// new / delete expression : 分别引发 3 / 2 个 步骤
string *ps = new string("initialized");
delete ps

1) 调 operator new 分配 `未 type 化` 内存
2) 在 该内存处, 调 该 type 的 ctor, 用 指定 初始化式 构造 obj
3) return pointer to 新构造 obj

1) 对 ptr 指向的 obj 调 适当 dtor 撤销 obj
2) 调 operator delete 释放 obj 所占 内存
```



`(3) vs. allocator 的 mem: allocate deallocate`
```
1) 一般 直接用 allocator  更 safe

2) 分配 + 释放 `非 type 化  / type 化 内存`

operator new / operator delete: 
返回值 要 type 强转 / para = void*

allocate / deallocate : 不用 type 强转 / T*
```

`4. placement new`

```
(1) 形式

new (place_address) type
new (place_address) type (initializer-list)
```

`(2) 作用: 在 已分配的 未构造 内存 处, 构造 / 初始化 1 个 对象`
```
=>
1) 可 `作 allocator 的 construct 函数 的 低级选择`
2) `更灵活,`
`初始化式 可以用 任何 ctor;`
```
`直接 调 ctor 构造 obj;`
`而 construct 是 调 copy ctor 构造 obj`

`5. explicitly call dtor`
```
(1) 形式
p->~T();

(2) `作 allocator 的 destory 函数 的 低级选择`

for (T* p = p_first_free; p != p_elements; )
	alloc.destroy(--p);

for (T* p = p_first_free; p != p_elements; )
	(--p)->~T();
```

`6. class 特定的 new 和 delete`


`(1) 优化 内存管理 的 2种办法:`
```
1) class 接管 自己 internal data structure 的 内存管理
```
**`2) class 定义 自己 的 operator new / delete 以 优化 new / delete`**
=> 
`new / delete expression 就会 call class 内部 的 operator new / delete; 而不是 defaultly 下 调 标准库 的 operator new / delete version`

```
(2) 按 方法 2 可优化的 1个 例子
chapter 16 的 Queue class, 不直接保存其 elements, 
而是 `用 new expression 分配 QueueItem type 的 obj`
```

**`预先分配 1 块 未构造 内存 以 保存 QueueItem obj, 也许可能 改善 Queue 性能: 构造于 / 释放到 预先分配的内存`**

`(3) operator new / delete 作 mem of class`

```
void* operator delete(void*);
void* operator delete(void*, size_t);
```
**`1) operator delete 还可以 接受 size_t 型 第 2 para, 但 只有 class 是 继承层次 中 某层上的 class 时, 第 2 para 才是 必需的: 第 1 arg 的 Base class 型 pointer 指向 Derived class obj 时, 若 Base class 有 virtual dtor => 传给 operator delete 的 第 2 arg 的 size 将 根据 第 1 arg 的 pointer 指向 的 obj 的 动态类型 而变; 否则 undefined behavior`**

**`2) operator new / delete 作 mem 时, implicitly 是 static:`**
**`1> 因为 它们在 构造 obj 前 / 撤销 obj 后 调用 => 无 mem data 可 操纵`**
`2> 只能 直接访问 所属类 static mem`

`7. 1 个 内存分配器 基类`
```
为 QueueItem 实现 operator new / delete mem
```

`(1) 优化 库 中 new / delete`

**`通用策略: 预先分配 1 块 未构造内存, 创建 obj 时, 不用再 分配内存, 直接在 该内存处 构造; 释放 obj 时, 在 该内存处 析构, 之后 先不将内存 返还给系统. 具体 implement: freelist (自由列表), 将 freelist 实现为 (根据指定 type)已分配内存 但 未构造对象 的 链表 `**

```
为了支持 不同类型 的 内存优化, 
定义1个 CachedObj class 处理 freelist
想 优化自己 对象内存分配 的 class 继承 CachedObj 即可 
```

**(2) CachedObj class 的 interface: 只负责 `根据知道的 type 大小`  通过 `freelist` 来 `分配 / 释放 未构造的内存`**
```
operator new: 
1) freelist empty 时, `分配` 新 `unconstructed memory block`
2) `record the first` unconstructed `memory block` from freelist,
   以 最后 return
3) `移出` unconstructed `memory block` from freelist: 
   `freelist (free_memory) head pointer` free_list 
   `update` 为指向 `the next memory block`
	
operator delete:
撤销 obj 后, `将 unconstructed memory block 放回 freelist`
```

**`(3) 限制: CachedObj 只能用于 非 inheiritance hierarchy 中 的 class type. 因 CachedObj class 无法根据 obj 的 动态类型 分配 不同大小的 obj: CachedObj 的 freelist 保存 大小相同的 obj`**

**`(4) why the four privete mem is defined static? resson: maintains a freelist only for all the objects of the given type`**

`(5) QueueItem class 具有 自动内存分配 功能, 使用 freelist 减少 创建 新 QueueItem (Queue 元素) 时 需要的分配数`

![image.png](https://upload-images.jianshu.io/upload_images/20172887-2567a42a7536dd2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/20172887-8e6744f9cd6c47f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
#include <memory>    // std::allocator
#include <stdexcept> // std::runtime_error
/* memory allocation class:
*  Pre-allocates objects and 
*  maintains a freelist of typed unconstructed memory block.
*  When an object is freed, it's put back to the freelist.
*  The memory blocks of freelist are only returned
*  when the program exits
*/

// for example T is QueueItem
template <class T>
class CachedObj
{
public:
	void* operator new(std::size_t);
	void  operator delete(void*, std::size_t);

	// it's Derived obj's responsible to 
	// return memory blocks of freelist to system
	virtual ~CachedObj() {}
protected:
	T* next; // pointer to next Derived obj 
private:

	static std::allocator<T> alloc;

	// free_list's head pointer:
	// point to the first unconstructed memory blocks of the free_list
	static T* free_list;

	// the number of unconstructed memory blocks 
	// that needed to be allocated when freelist is empty
	static const std::size_t chunk;

	static void add_to_freelist(T*);
};

template <class T>
void* CachedObj<T>::operator new(std::size_t sz)
{
	//1) new should only build a T not an object
	// CachedObj's derived class T check that it is not inherited 
	if (sz != sizeof(T))
		throw std::runtime_error(
			"CachedObj's derived class can't be inherited!" );

	if (!free_list) // free_list empty
	{
		// 2) allocates chunk number of memory block of type T
		T* p_array = alloc.allocate(chunk);

		// 3) add each memory block to freelist
		for (size_t i = 0; i != chunk; ++i)
			add_to_freelist( p_array + i );
	}

	// move out free_list's first memory block 
	// & return it's pointer
	//1) record old head pointer of free_list
	T* old_head_pointer = free_list;

	//2) update free_list's head pointer to the next memory block:
	// equivalent to move out the first memory block
	// note: the type of pointer free_list is T*, eg. QueueItem*,
	// the forward(base) part of type T/QueueItem is CachedObj<T>,
	// next is mem of CachedObj<T>
	// => visit next through free_list must use CachedObj<T>:: to constrain 
	free_list = free_list->CachedObj<T>::next;

	//3) return old head pointer of free_list
	return old_head_pointer;
}

template <class T>
void CachedObj<T>::add_to_freelist(T* p)
{
		p->CachedObj<T>::next = free_list;
		free_list = p;	
}

template <class T>
void CachedObj<T>::operator delete(void* p, std::size_t)
{
	// the second para is no use
	if (p) // p != 0
		add_to_freelist( static_cast<T*>(p) );
}

// define static mem data,
// static / const : not use / use outside

// alloc need not to initialized
template <class T>
std::allocator<T> CachedObj<T>::alloc;

template <class T>
T* CachedObj<T>::free_list = 0;

template <class T>
const std::size_t CachedObj<T>::chunk = 16;

template <class T>
class QueueItem : public CachedObj< QueueItem<T> >
{
//...
};

int main()
{
	QueueItem<int>* p = new QueueItem<int>;

	// not consider free memory blocks in freelist
}
```

##2 RTTI (运行时 类型 识别)
```
   支持 在 运行时 确定 obj 的 实际类型
```

```
1. 概述
```
`通过 RTTI, 可以用 基类 pointer / reference 确定 其 所指 obj 的 实际 派生类型`

```
提供 RTTI 的 2个 operator:
```
`1) typeid`
```
return  actual type of obj pointed to by pointer / reference
```

`2) dynamic_cast`
```
convert pointer / reference to base class to 
  pointer / reference to derived class
```
`note: 带 >=1 个 virtual function 的 class, 这 2个 operator 才 return dynamic type; 否则, return compile-time type`

`用 动态类型强转 要 知道 目标类型, 且 应该 check 转换是否成功. 只要可能, 用 virtual function 更好`


`2. dynamic_cast`

```
执行 2 个 操作:
1) 运行时 check 转换 是否有效
pointer / reference to obj 的 type 不是 目标 type 时, 无效 / 失败
2) 有效时 才 转换
```

`(1) dynamic_cast + pointer`
```
失败时, return 0

dynamic_cast<T> (0) == 0
```

```
if( Derived* pd = dynamic_cast<Derived*>(pb) )
{   // if 1: 转换 成功 <=> pb points to a Derived obj
	// use Derived obj to which pd points
}
else
{	// pb points to a Base obj
	// use Base obj to which pb points
}
```

`(2) dynamic_cast + reference`
```
失败时, 抛出 bad_cast exception
```

```
void f(const Base& b)
{
	try
	{
		const Derived& d = dynamic_cast<const Derived&>(b);
		// b refer to a Derived obj
		// use Derived obj to which b referred
	}
	catch(bad_cast)
	{   // b refer to a Base obj
	
	}
}
```

`3. typeid`

```
(1) 形式

typeid(e)

in <typeinfo>
```

`e 是 表示 obj 的 任意 expression 或 type name`

```
(2) 用途
比较 2 个 expression 的 type / 
将 expression 的 type 与 指定 type 比较
```

```
Base* pb;
Derived* pd;

if( typeid(*pb) == typeid(*pd) )
{
	// pb pd point to objects of the same type
}

if( typeid(*pb) == typeid(Derived) )
{
	// pb actual points to Derived
}
```
`4. RTTI 的应用`

```
比较 继承层次中 Base class 与 Derived class 的 mem data 值是否相等

solution: 要 将 左 右 operand 的 排列组合 进行比较 才完整

bool operator==(const Base&, const Base&);
bool operator==(const Base&, const Derived&);
bool operator==(const Derived&, const Derived&);
bool operator==(const Derived&, const Base&);
```

```
solution2: 更精妙的 做法
```

`每个 class 定义` 
`(1) friend operator==, 2 个 para 均为 const reference of Base type`

**`先用 typeid 判 左右 type 是否 相同`**
`1) 若 不同, return false;`
`2) 若 相同, 则 实际 compare 工作 委托给 equal`

`(2) 1 个 virtual mem function equal, para 均为 const reference of Base type` 
**`先 强转 operand 为 自身类型`**
`1) 若 转换成功 => 左 右 type 相同 -> 再 compare;`
`2) 若 失败 => 左 右 type 不同, return false`

```
class Base
{
	friend bool operator==(const Base&, const Base&);
public:
	//
protected:
	virtual bool equal(const Base&) const;
}

class Derived
{
	friend bool operator==(const Base&, const Base&);
public:
	//
protected:
	virtual bool equal(const Base&) const;
}

bool operator==(const Base& lhs, const Base& rhs)
{
	return typeid(lhs) == typeid(rhs) &&
		lhs.equal(rhs);
}

bool Base::equal(const Base& rhs) const
{
	// objects the same type: Base
	// compare mem data
}

bool Derived::equal(const Base& rhs) const
{
	// Derived 中 再 强转 
	if(const Derived* pd = 
		  dynamic_cast<const Derived*>(&rhs) )
	{	// objects the same type: Derived
		// compare mem data
	}
	else
	{
		return false
	}
}
```

##3 pointer to mem of class
```
pointer to mem of class
    与 ordinary obj/func 的 pointer 异同:
	同: 根据 obj / func 的 type 而变
	异: pointer 还必须 反映 所属 class
```
```
class Screen
{
public:
	char get() const;
private:
	std::string contents;
};

Screen screen;
Screen* pScreen;

1. pointer to mem data / mem function

(1) type:
std::string Screen::*
char (Screen::*)() const

(2) define
std::string Screen::*ps_Screen;
char (Screen::*pf_get)() const;

(3) initialize
std::string Screen::*ps_Screen = &Screen::contents;
char (Screen::*pf_get)() const = &Screen::get;

(4) use
screen.*ps_Screen
pScreen->*ps_Screen

char c = (screen.*pf_get)();
	 c = (pScreen->*pf_get)();
```

##4 3种聚合 type
```
	联合 class : 节省空间
	嵌套 class : 
	local class:
```

`1. 嵌套 class`
```
template <class T>
class Queue
{
private:
	struct QueueItem // nested class
	{
		QueueItem(const T&);
		T item;
		QueueItem* next;
	};
	
	QueueItem* head;
	QueueItem* tail;
};
```
`(1) 嵌套 在 class template 内 的 mem implicitly 为 template, template para 即 外围 class 的 template para`

```
// (2) class 外 定义 mem of nested class
template <class T>
Queue<T>::QueueItem::QueueItem(const T& t):
					 item(t), next(0) {}
```
`(3) 外围 scope 的 obj 与 嵌套类 的 obj 没联系`

`(4) 实例化 外围 class template 时, 不自动 实例化 相应的 嵌套 class template. 只有当 使用 嵌套 class template 时, 才 实例化 它`
```
如 Queue<int>
解引用 head / tail 时, 才实例化 QueueItem<int>
```

`2. 局部类`

`(1) 可以在 function body 内 定义的 class, 它 定义了 1 个 type`

`(2) 局部 class 所有 mem 必须完全定义于 该 class body 内 => 远不如 嵌套类 有用`

`(3) 局部类 只能 访问 外围 scope 中 定义的 type name / static obj / enum mem. 不能 访问 外围 scope 中 普通 obj`

##5 固有的 不可移植的 特征
```
	volatile
	链接指示
```
`1. volatile`

```
(1) 确切含义 与 机器相关, 只能通过阅读 编译器文档 来理解. 
通常 相关 code 不可移植
```

`(2) 其值 可以用 编译器 之外的 方式 改变 => volatile 告诉 编译器, 不应该 对 volatile obj 进行优化处理`

`(3) 使用方式 与 const 相同. 但 与 const 不同 的是, 不能用 合成 copy ctor / operator= 从 volatile obj 进行 初始化 或 赋值, 因为 volatile obj 不能传给 const / non-const reference, 只能传给 volatile const / non-const reference`

`2. extern "C" : linkage directive`

`(1) 告诉编译器 按 C 编译 function`
```
extern "C" double f(double para) { /* */ }
```

`(2) C++ 中 重载 C function 时, 重载集合中 other function 都必须为 C++ function`

`(3) extern "C" 函数的 pointer 与 C++ function pointer 的 type 不同, 不可 相互 初始化 / 赋值`
