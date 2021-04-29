#1 概述

**1. 定义**

**`重载操作符 ( overloaded operator )：即 重载操作符函数, 又称 操作符函数`**

```
is a function with special name: 
```
`operator 操作符名称` 

```
//eg:
operator +
```

![重载操作符1.jpg](https://upload-images.jianshu.io/upload_images/20172887-d44d7e0a5c00ec8d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
2.   重载操作符 注意事项

(1) `操作数 至少有1个为 class / enum type`

(2) 相对于 build-in type operator 优先级、结合性 不变

(3) `重载 && || 不再具有短路特性` 

=> 重载 &&  ||  逗号 操作符 不是好的做法

(4) `不能重载` 的 4种 操作符
:: 
.
.*
?:

(5) para number ( 算上 隐含的 this ) 与 operands number 相同

重载函数 为 class's mem func 时, 隐含 第 1 para this
```

`3. 何时 作为 member function / non-member function`

**`(1) 必须作为 mem func 的 operator, 否则 编译报错`**
```
[]   下标 
->   成员访问  
()   函数调用 
=    赋值 
```

`(2) modify state of object 时，通常作为 mem func`

```
+= 等
++
-- 
*   // dereference 
```

`4. 对称 的 operator, 最好作为 non-mem func`
```
算术     + - * /

关系     == != < <= > >=

位运算   & |
```

**`5. overloaded operator 不能 作 static mem func,` 只能 作 non-static mem func**

`reason：作 mem func 时, 其中 要引用 para this 以 read / write mem data`, 但 `static mem func 无 para this`

`6. 作 mem func 时, 其 para number 看起来 比 operands 少1个:
后置 ++/-- 除外`

**`n 个 operands => para = 隐含 第 1 para this+ （n -1）个 operands`**

```
7. 作 non-mem func 时, 通常作为 class's friend, 
   以便访问 class's private mem
```

```
// non-mem func: >> << 
// mem func    : +=
class A
{
private:
    friend istream& operator >>(istream&, A&);
    friend ostream& operator <<(ostream&, const A&);
public:
    A& operator +=(const A&);
};
```

**`8. 必须 作 friend func 的 secenario ?`**

`(1) 由 basic type variable 发起调用 时`

```
Complex a；
Complex b= 2+ a;
```

`(2) 不用  object.mem_func 形式 调用 时`

`9. 算术 vs. 复合赋值: 以 + vs. += 为例`

```
(1) 算术
 ```
`1) return rvalue`
**`2) 不用设为 friend, 因 operaotr+ 可用  operator+= (mem func) 实现`**

```
(2) 复合赋值
```
`return reference of left operand`

`(3) 重载了 算术 operator 一般也要 重载 复合赋值 operator`


`10. 设计` overloaded operator

```
(1) 有 内置含义 的 overloaded operator
```
**`不 explicitly 重载 时, 编译器 会为其 生成 default operator function`**

```
取地址   & 
逗号     ,           从左到右 算, return 最右边 operand's value
赋值     =           逐个对应赋值
逻辑与   &&  ||
```

```
(2) 设计方法

1) 先设计 public interface of class
```
**2) `当 operation on class 与 build-in operator 逻辑上有对应关系 时, 考虑作为 overloaded operator`**

```
相等测试：operator==
输入输出： operator>>    operetor<<
测试对象是否为空：operator!
```
```
(3) 关系 操作符: 容器中常用

== != < <= > >=
```
**`(4) 不应该 重载 逻辑与 &&`**

`call function 时, 先 calculate arg, 后 execute function body: a && b
 == a.operator&&(b) => 先计算 b => && 不再有 短路性`

```
// 假设，A 重载了 && 
bool A::operator&&(const A &rhs )
{ 
	// ...
}

// 使用时:
A a, b;

a && b
// equivalent to
a.operator&&(b)
```

`11. 如何调用` overloaded operator
```
(1) implicitly 调用
A a1, a2;
A* p = &a1;

//1) non-mem
A a3 = a1 + a2;

//2) mem
a1 += a2;
*p += a2;

//(2) explicitly 调用
//1) non-mem
A a3 = operator+(a1, a2);

//2) mem
a1.operator+=(a2); // object.operator...
p->operator+=(a2); // p_object->operator...
```

#2 `I/O` operator overload

`1. 重载 I/O operator 的 限制`

**`(1) 必须为 non mem`**

否则，`左 operand 只能是 输出 type 的 object, 这与 build-in type 的 输出 operator 使用方式相反`

```
A a;
a << cout;
```

`(2) 通常 要 read/write private/protected mem => 通常设为 friend`

![1.jpg](https://upload-images.jianshu.io/upload_images/20172887-8db7f313fcb482b4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`2. 重载 <<`

`(1) 第1 para: ostream&`
```
用 reference + non-const reference
```
**` 因 不能 copy ostream obj + write to stream 会 modify state of stream`**

`(2) 第2 para: const A&`, 要 write 的 class 的 const reference
```
用 reference + const reference
```
**`以 避免 copy arg + 支持 输出 const / non-const object`**

`(3) return: ostream&`

```
ostream&
operator <<(ostream&, const A&a)
{
    //1) any special logic to prepare object

    //2) actual output of members
    os <<  //...

    //3) return ostream object
    return os;
}
```

`3. 重载 >>`

`(1) 第 1 para: istream&`, 要 read 的 stream 的 reference

`(2) 第 2 para: A&`, 要 read 到 的 class 的 non-const reference
```
用 reference + non-const reference
```
**`以 避免 copy arg + read data 到 object 中`**

`(3)return: istream&`, 要 read 的 stream 的 reference


`4. I operator vs. O operator: I operator 要能处理 error 和 end-of-file`

```
// read 3 mems to initialize object 
// 	with calculating the 4th mem
istream&
operator >>(istream& in, rhs& rhs)
{
    double price;
    
    in >> rhs.isbn >> rhs.units_sold >> price;

    if(in)
        rhs.revenue = rhs.units_sold * price;
    else // input failed: reset rhs to default state
        rhs = rhs(); 

    return in;
}
```

#3 `算术 / 复合赋值` operator overload

```
1. 复合赋值 operator 
```
`1) 作 mem`
`2) return 左值`

```
2. 算术 operator
1) 通常 作 `non-mem`
2) `return 右值`
3) 用 `复合赋值` 实现, 更简单、高效
reason: 不用 多1次 temp objrct 的 创建 和 撤销
```

```
class A
{
public:
	A& operator+=(const A&);
private:
    int x;
    double y;
};

A&
A::operator+=(const A& rhs)
{
    x += rhs.x;
    y += rhs.y;

	//1) return *this / lvalue as reference
    return *this;
}

A
operator+(const A& lhs, const A& rhs)
{
	//1) construct A's local object which will be returned,
	// `initialize it to lhs`
    A ret(lhs); 

	//2) implement + with += 
    ret += rhs; 

	//3) return ret (A's local object) by value
    return ret; 
}
```

```
// + not implement with +=
A
operator +(const A& lhs, const A& rhs)
{
	//1) construct local object of A
    A ret;

	//2) add each mem of loperand and roperand
	//  add result is a `temp rvalue obj`
    ret.x = lhs.x + rhs.x; 
    ret.x = lhs.y + rhs.y; 

	//3) return ret ( local object ) by value
    return ret;  
}
```

#4 `赋值` operator overload

`必须为 mem of class, 以使 编译器 知道是否需要  synthesize 1个`

`1. para 通常是 const A&`
```
用 reference + const reference
```
**`以 避免 copy arg + 支持 接收 arg 为  const / non-const object + don't modify original obj`**

**`2. 必须 return reference of *this`**
```
return reference
```
`避免 temp copy` 的 创建和撤销

```
#include<iostream>
#include<string>
using namespace std;

class A
{
private:
    char* name;
public:
    A() : name(NULL) { }
    A(const char* _name)
    {
        name = new char[ strlen(_name) + 1 ];
		
		// dst size src
        strcpy_s(name, strlen(_name) + 1, _name); 
    }

    A(const A& rhs)
    {
        name = new char[ strlen(rhs.name) + 1];
        strcpy_s(name, strlen(rhs.name) + 1, rhs.name);
    }

    A& operator=(const A& rhs)
    {
        if ( &rhs != this )
        {
			//1) erase old value
            if (name != NULL)
                delete[] name;
			
			//2) reallocate new memory for array
            name = new char[strlen(rhs.name) + 1];
			
			//3) assign new value
            strcpy_s(name, strlen(rhs.name) + 1, rhs.name);
        }
        return *this;
    }

    ~A()
    {
        delete[] name;
    }
};

int main()
{
    A a1;
    A a2("wyx");
    A a3 = a2;
    a1 = a2;
}
```

#5 `==` operator overload

`1. 作 non-mem, 必须设为 friend, 以便 直接用 obj 访问 private / protected mem of class`

```
2. 通常, 2个 obj 所有 对应 mem 都 相同 时, 
   才 认为 2个 obj 相等

3. 定义 operator== 时, 更易与 标准库 一起使用
// eg: find 算法 默认使用 operator==
```
```
inline bool
operator==(const A& lhs, const A& rhs)
{
	// must be made friend of A, in order to
	// use `lhs.private_mem`
    return lhs.x == rhs.x && 
		   lhs.y == rhs.y;
}

inline bool
operator!=(const A& lhs, const A& rhs)
{
    return !(lhs == rhs);
}
```

#6 `下标` operator overload: operator[]

`1. 必须 作 mem of class`

```
2. 容器 class 一般需要

3. 一般 需要 定义 2个 版本
```
`(1) non-const mem func -> return non-const reference：可以作 赋值 的 left / right operand -> read + write`


`(2) const mem func ->  return const reference：只能作 赋值 的 right operand -> read`

```
class A
{
private:
    vector<int> vec;
public:
    int& operator[] (const size_t);
    const int& operator[] (const size_t) const;
};

// 2 version para are both `const size_t`
int& A::operator[] (const size_t index)
{
    return vec[index];
}

const int& A::operator[] (const size_t index) const
{
    return vec[index];
}
```

#7 `解引用 * / 成员访问 ->` : 以 支持 pointer type class — smart pointer / iterator

`1. ->`
`(1) 必须是 class's mem`

`(2) return type 必须是 reference / pointer to the pointed obj`

```
class Ptr{
   T* operator->();
};
```

`(3) 重载了 -> 的 class's obj`  可以 `像 pointer 一样  访问  另一 class 的 mem`


```
void f(Ptr ptr)
{
	// ( ptr.operator->() )->t_mem = 10;
	ptr->t_mem = 10; 
}
```


```
template <class T>
class U_Ptr
{
    friend class HasPtr<T>;
private:
    T* pt;
    size_t ref_count;
};

template <class T>
class HasPtr
{
private:
    U_Ptr<T>* pU; 
    int val;
public:
	//1) return reference / pointer to 
	//	`non-const pointed obj`
	T& operator*() 
		{ return *pU->pt; }
    T* operator->() 
		{ return pU->pt; }
	
	//2) return reference / pointer to 
	//	`const pointed obj`
    const T& operator*() const 
		{ return *pU->pt; }
    const T* operator->() const 
		{ return pU->pt; }
};
```

#8 `自增 ++` operator overload
```
(1) `modify operand's state => 通常作 class's mem`
(2) `迭代器` 这样的 class 通常支持
```
`1. 前置`
`1) no para`
`2) return ++ 后  obj's reference —— 左值`

```
++a   //调  a.operator ++()
```

`2. 后置`
`1) para 为 int + arg 恒为 0 — 只为 表明 是 后置, 而非 前置`
`2) return ++ 前 obj's copy  — 右值`

```
//使用
a++ //调 a.operator ++(0)
```

```
// prefix
Clock :: Clock& operator++()
{
    second++;
    if(second >=60)
    {
        second -= 60;
        minute++;
        if(minute >= 60)
        {
            minute -= 60;
            hour = (hour + 1) % 24;
        }
    }

    return *this;
}

// postfix
Clock :: Clock operator++(int )
{
	//1) store old *this
    Clock oldTime = *this;
    
	//2) 调 前置 ++: (*this).operator++()
    ++(*this); 

	//3) return the stored value
    return oldTime;
}

int main()
{
    Clock time(23, 59, 59);
    time.showTime();     // 23:59:59

    // return old value's copy: 23:59:59
	//	but tiem increase -> 0:0:0
    (time++).showTime(); 

    time.showTime();     // 0:0:0  

    (++time).showTime(); // 0:0:1 
}
```

```
#include <iostream>
#include <vector>
using namespace std;

class Iter
{
private:
	int* begin;
	int* end;
	int* cur;
public:
	Iter(int* _begin, int* _end) : 
		begin(_begin), end(_end), cur(_begin) { }

	Iter& operator++();
	Iter& operator++(int);
};

// prefix
Iter& Iter::operator++()
{
	if (cur == end)
		throw out_of_range("out_of_range");
	++cur;
	return *this;
}

// postfix
Iter& Iter::operator++(int)
{
	Iter ret(*this);
	++*this;
	return ret;
}

int main()
{
	int a[3];
	a[0] = 1;
	a[1] = 2;
	a[2] = 3;

	Iter iter(a, a + 3);
	++iter;
}
```
#9. conversion operator

`1. 将 class type convert 为 other type`

`2. 形式 operator type();`

`1) 必须是 mem func`
`2) para list 为 empty`
`3) 无 return type, but  必须 explicitly return 1 个 指定 type 的 value`
`4) 一般 不应该 modify 被转换的 obj => 一般定义为 const mem func`

```
class SmallInt
{
private:
	std::size_t val;
public:
	operator int() const
	{
		return val;
	}
};
```
