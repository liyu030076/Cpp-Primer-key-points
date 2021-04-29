```
key points:
1. copy ctor
2. operator=
3. dtor
4. 消息处理 应用
5. 管理 pointer member: 智能指针 class
```

```
1. 定义 new type 时, 要 explicitly ( coder 负责)
或 implicitly ( 编译器 负责) 地 指定 
`copy / 赋值 / 撤销 该 type 的 obj 时 发生什么`, 
copy ctor / assignment / dtor 的 责任 正在于此,
这 3 者 总称为 copy control

2. copy  难点在于 识别何时需要 覆盖 default version
需要 explicitly 定义 copy control 的 1种 key secenario: 
class has pointer mem
```

#1. copy ctor

`1. 单 para : para 为 该 class type 的 reference, 常用 const reference`
```
class A
{
private:
    int x;
    int y;
};

A::A(const A& rhs)
{
    x = rhs.x;
    y = rhs.y;
}
```

`2. 何时 调用`

`(1) 用 同 type 的 obj 初始化 一个 new obj`
`(2) pass class's obj's copy to function`
`(3) return class's obj's copy from a function`
`(4) 初始化 顺序容器 的 element`
`(5) 根据 元素 初始化列表 初始化 array element`

```
// (1)
A a;
A a2 = a;
A a3(a);

// (2) 
void f(A a);

// (3)
A f()
{
	A a;
	...
	return a;
}

//(4)
// 1) 1 次 ( string ) default ctor 创建 temp value +
// 2) 5 次 ( string ) copy ctor invoked:
//    copy temp value to 5 elems of svec
std::vector<std::string> svec(5);

//(5) 
std::string str_array[2] =
	{
		std::string("hello"),
		std::string("world")
	};
```

`3. 合成 synthesized copy ctor: 逐个 mem initialize`
```
合成 synthesized <=> default
```
 **`initialize non-static mem data,`**
```
初始化 new obj 为 copy of old obj
```

`(1) array 是 例外`
**`array 一般 不能 copy, 但 当 class 有 mem array 时, synthesized copy ctor 会 copy every element of the array`**

**`(2) 逐个 mem initialize 最简单 的 model 是, 将 synthesized copy ctor 视为 每个 mem data 在 initialization list 中 初始化`**
```
class Sales_item
{
public:
	Sales_item(const Sales_item & rhs);
private:
	std::string isbn;
	int units_sold;
	double revenue;
};

// equivalent to synthesized copy ctor
Sales_item::Sales_item(const Sales_item& rhs) :
	isbn(rhs.isbn),
	units_sold(rhs.units_sold),
	revenue(rhs.revenue)
{}
```


`4. 禁止 copy`
```
有些 class 要 完全 禁止 copy, 如 iostream
```
`(1) 为了 avoid copy, class 必须 explicitly declare copy ctor 为 private`

`(2) 如何 禁止 class's client / mem / friend 进行 copy`
`1) explicitly declare` copy ctor `为 private`
`2) define` copy ctor => `只能 禁止 client 进行 copy;`
`不 define` copy ctor => `同时 禁止 mem / friend 进行 copy`

```
`上述禁止 方法 的 原理:`
(1) `declare but not define mem func 是 合法的`
(2) `use undefined mem cause linkage error`
=> declare but not define private copy ctor,
  可以 `禁止 copy` class type's obj 的 `attempt`

`1) client 的 copy attempt` -> `compile error`
`2) mem func / friend code 的 copy attempt` -> `linkage error`
```

#2. `operator= / 赋值操作符 / assigment operator` (note: differenc from assign)

`1. 单 para:` para 为 `同 type ( obj ) 的 ( 通常是 const ) reference, para 与 copy ctor para 相同`

```
assigment operator 是给 class 的 mem 赋值 
=> 
```
`1) assigment operator 必须是 class 的 mem`
`2) this 指向 左操作数 => assigment operator 接受 单 para`
`3) return reference of the same type: return 左操作数 / *this 的 reference`
                                                                            
```
class Sales_item
{
public:
	// equivalent to synthesized assignment operator
	Sales_item& operator=(const Sales_item&);
};
```

```
2. 何时调用

给 `已有 obj` 赋值 时
```

```
3. `合成` 赋值操作符: `逐个 mem 赋值`
```
```
class Sales_item
{
public:
	Sales_item& operator=(const Sales_item&);
private:
	std::string isbn;
	int units_sold;
	double revenue;
};

// equivalent to synthesized assignment operator
Sales_item&
Sales_item::operator=(const Sales_item& rhs)
{
	isbn = rhs.isbn;
	units_sold = rhs.units_sold;
	revenue = rhs.revenue;

	return *this;
}
```

`4. assigment 和 copy 通常一起使用`
```
实际上, 应将 assigment 和 copy 这 2个 操作符 看作 1个 单元.
若需要其中 1 个, 几乎可以肯定 也需要 另一个
```

#3. dtor / 析构函数

**`构造函数 : 获取资源, 比如 分配一个缓冲区`**
**`析构函数 : 释放资源`**

`1. 不能指定 para => 不能重载 dtor`

**`ctor 可以有 多个, 但 explicitly dtor 只能有 1 个, 用于 class 的所有 obj`**

`2. 何时调用`
`撤销 对象 时, 编译器 自动调用, 3种情形:`
`(1) 实际 obj ( 而不是 reference of obj )  超出 scope 时`

`(2) delete pointer that point to 动态分配的对象`

**`(3)容器 ( 标准库 容器 / array ) 超出 scope 时, 自动调用 容器中 每个类类型元素 的 dtor`**

```
// (1) & (2)
//1) dynamic allocated
Sales_item* p = new Sales_item;
{	//2) new scope
	Sales_item item(*p);

	//3) dtor run on obj pointed to by p
	delete p;
}   //4) local scope exit; dtor called on item

//(3)
{
	//1) dynamic allocated
	Sales_item* p_arr = new Sales_item[3];

	//2) local object
	std::vector<Sales_item> vec(p, p + 3);
	//...

	//3) array is freed; dtor on each element
	delete [] p_arr;
}   //4) vec out of scope; dtor run on each element

```

```
3. synthesized dtor

无论是否 explicitly 定义dtor, 编译器 都会 合成 1 个 dtor: 

(1)按 obj 创建 时的 `逆序 撤销` 每个 non-static mem data

(2)`不 delete pointer mem 指向 的 obj`

(3)撤销 build-in type 或 复合 type 的 mem 没啥影响
```

`4. dtor 与 copy ctor / assignment operator 的 important difference:`

**`即使 explicitly define dtor, synthesized dtor 仍然 ( 在 explicitly 定义的 dtor 运行完成后 ) 运行`**

```
class Sales_item
{
public:

	//1) empty dtor: do nothing
	~Sales_item() { }
};  //2) synthesized dtor invoked
```
```
synthesized dtor 仍然运行:
(1)调 string 的 dtor 撤销 string mem 
   -> string dtor: 释放 保存 isbn 的 内存

(2)units_sold / revenue 是 `build-in type`
=> `撤销 它们 不需要 做什么`
```

`5. 何时 要有 explicitly dtor`

```
仅在 有些工作 default dtor 不能完成时, 
才需要 explicitly dtor

(1) 三法则
若 class 需要 dtor, 
则 也需要 copy ctor 和 assignment operator

(2) dtor `通常用于` 
`释放 在 ctor 或 obj lifetime 内 获取的 resource`

(3) dtor 实际可执行 
`class 设计者 希望 class's obj 使用完毕后` 执行的 
`任意 operation`
```

#4. 消息处理 应用

![image.png](https://upload-images.jianshu.io/upload_images/20172887-2ed793c3e604d021.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
邮件处理 application:

1 个 Message 可以出现在 多个 Folder ( 目录 )

1. Message 类: emial message

`(1) 每个 Message store
1) 1个 pointer set`:
    `各 pointer` 指向 该 Message 所在 的 `相应 Folder`
2) Message content

(2) `construct:` 指定 Message content

(3) `copy` 
1) copy `content`
2) copy `pointer set`
3) `指向 source Message` 的 `每个 Folder`:
`add a pointer ( point ) to new Message`

(4) `assignment`
1) `指向 left Message` 的 `每个 Folder`:
`delete pointer to left Message`
2) `content + pointer set` assignment
3) `指向 right Message` 的 `每个 Folder`:
add a pointer to left Message

(5) dtor: 撤销 Message
1) Message 放入的 `每个 Folder`: 
delete pointer to the Message
即 remove Message from corresponding Folders

`2) note: there is no need to delete pointer to 
each Folder that include the Message.
Because when Message destory, its Folder pointers 
in Folder pointer set will automatically destory first`

(6) save: 将 Message 放入 指定 Folder
1) Message:
add a pointer to the Folder
2) 指定 Folder: 
add a pointer to the Message

(7) remove: 将 Message 从 指定 Folder 中 删除
1) 指定 Folder: 
delete pointer to the Message
2) Message:
delete pointer to the Folder

2. Folder 类: message 出现 的 目录

`(1) 每个 Folder 保存 1个 pointer set:`
    `各 pointer` 指向 该 Folder 所包含的 `相应 Message`
```

`note: There is no need to delete pointer to each Folder that include the Message in dtor. Because when Message destory, its Folder pointers 
in Folder pointer set mem will automatically destory first`

`1. 即使 obj 赋值 给自己, assignment operator 也应 正确工作. 保证这一行为的 通用方法是 check 右操作数 是否 == 自身; 若是, 啥也不做, 直接 return *this`

`2. assignment operator / copy ctor / dtor 通常有 通用公共 operation => 把 通用公共 operation 放 private 实用 function`

```
// simple simulate
#include <iostream>
#include <string>
#include <set>

class Folder;

class Message
{
public:
	//1) implicitly initialize folders(set) empty 
	// in initialization list
	Message(const std::string& str = "") :
		contents(str) {}

	// copy control: we must manage pointers to this Message
	// from Folders in folders set
	Message(const Message&);
	Message& operator=(const Message&);
	~Message();

	// add / remove this Message from specified Folder
	void save(Folder&);
	void remove(Folder&);

private:
	// actual message text: save copy of message
	std::string contents;

	// Folders that have this message
	std::set<Folder*> folders;

	//Utility functions used by copy ctor / assignment / dtor

	//add this Message to Folders that point to the para
	void put_Msg_in_Folders(const std::set<Folder*>&);

	//remove this Message from every Folder in folders
	void remove_Msg_from_Folders();
};

Message::Message(const Message& rhs):
	contents(rhs.contents), folders(rhs.folders)
{
	put_Msg_in_Folders(folders);
}

Message& Message::operator=(const Message& rhs)
{
	//2) 避免自我 copy
	if (&rhs != this) // note: &rhs
	{
		// erase old folders
		remove_Msg_from_Folders();

		contents = rhs.contents;
		folders = rhs.folders;

		// add this Message to each of Folder in rhs
		put_Msg_in_Folders(rhs.folders);
	}
	return *this;
}

Message::~Message()
{
	remove_Msg_from_Folders();
}

class Folder
{
private:
	std::string path;
	std::set<Message*> msg_set;
public:
	Folder(const std::string& str = ""): path(str) {}

	void addMsg(Message*);

	void removeMsg(Message*);
};

void Folder::addMsg(Message* p_msg)
{
	msg_set.insert(p_msg);
}

void Folder::removeMsg(Message* p_msg)
{
	if (msg_set.count(p_msg))
		msg_set.erase(p_msg);
}

void Message::put_Msg_in_Folders(const std::set<Folder*>& rhs)
{
	//3) this = &message_obj; *iter type Folder*
	for (std::set<Folder*>::const_iterator iter = rhs.begin();
			iter != rhs.end();
			++iter )
		(*iter)->addMsg(this); 
}

void Message::remove_Msg_from_Folders()
{
	for (std::set<Folder*>::const_iterator iter = folders.begin();
		iter != folders.end();
		++iter )
		(*iter)->removeMsg(this);
}

void Message::save(Folder& folder)
{
	folder.addMsg(this);
	folders.insert(&folder);
}

void Message::remove(Folder& folder)
{
	folder.removeMsg(this);
	if( folders.count(&folder) )
		folders.erase(&folder);
}

int main()
{
	Folder folder1("D:\\dir1");
	Folder folder2("D:\\dir2");

	Message msg1("I am liyu.");
	Message msg2("who are you?");

	msg1.save(folder1);
	msg1.save(folder2);
	msg2.save(folder1);

	Message msg3 = msg1;
	msg3 = msg2;
}
```

#5. 管理 指针成员: 智能指针 class

https://www.jianshu.com/p/5c907c0d05b1
