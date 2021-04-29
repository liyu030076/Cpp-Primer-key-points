```
C++ 提供 高效率编程 的方式1: `抽象`, 如 标准库

标准库 定义了许多 容器类 和 泛型算法, 
使 coder 可以 抽象 地编程, 
标准库 会 操心 细节, 尤其是 `内存管理`

1. vector 类 将 深入探讨, 
string 类 可视为 仅包含字符 的 特殊容器

2. 关联容器: 按 key 排序

3. 泛型算法 通常作用于 某一范围 的 元素

泛型 指2个方面:
1)算法 可用于 不同类型的容器
2)容器 可容纳 不同类型的元素

设计 库 的目的 是 为 容器类型 提供 common interface(操作/算法):
1)若 两种容器 提供相似的 操作, 则 该操作 应 相同
=> 学习 标准库 更容易, 只要学会一种类型,
就能运用另一种类型

eg. size() 及其 返回类型 size_type

2)一段 code 使用 某种容器, 通常只需要改变类型声明, 
就可 用于 另一种容器
```

#chapter 9 顺序容器

```
3种 容器:
vector
list
deque

3种 适配器:
stack
queue
priority_queue

1. 容器 只定义少量操作, 
大量额外操作由 泛型算法库 提供

2. 标准库 为 容器类型 的 操作 `强加了 公共接口`
若 2个容器 提供的操作相同, 则 接口应相同

3. 容器都是 class template
```
##9.1 顺序容器 定义/初始化, element type 约束

```
1. 定义/初始化
element type: 这里指 element type of 容器

(1)default ctor: 最佳运行性能 / empty 对象
C<T> c;

(2)copy ctor:copy /同 容器类型/ 同 element type
C c(c2);

(3)一段 元素 的 copy : [iter1, iter2) 内 
元素 要能 converted to element type

C c(iter1, iter2);

(4)n个 元素: 提供初始化式 t(按 t 初始化) / 
不 提供初始化式 t(`按值 初始化`) /
`只 适于 顺序容器`

C c(n, t);//t 要能 converted to element type
C c(n); 
```

```
2.  element type 约束

(1)至少满足 2 个约束:
1)可 copy
2)可 赋值

(2)关联容器 还有其他约束

(3)容器的容器(vector of vectors)
element type 为 容器
vector < vector<string> > lines; 

(4)3种 不可作 容器元素 的 type

1)reference : 不可 赋值
2)IO 库 类型: 不可 copy 或 赋值
3)auto_ptr
```

##9.2  迭代器 及其 范围
```
1. 每种容器 有 多种 迭代器 类型,
所有 迭代器 接口相同

2. 所有标准库容器 支持的 迭代器 运算
*iter
iter->item
++iter
iter++
--iter
iter--
==
!=

3. vector 和 deque 提供 额外运算
iter + n
iter - n
iter += n
iter -= n
> >= < <=

4. 迭代器 范围: 前闭后开 [first, last)

first == last 是 空区间 情况, `无须再特别处理`

first != last 时, 至少1个元素

while(first != last)
{
    // *first is safe to use
    ++first;
}

5. 使 迭代器 失效 的 容器操作

1)元素被移动后, 仍指向该元素原位置的 迭代器 等

2)指向 已删除元素 的 迭代器
```

##9.3  顺序容器 操作

```
1. 容器定义的 类型别名: 3组
(1)size_type: unsigned type, 
足以存储此 容器类 最大可能长度

(2)迭代器
iterator
const_iterator   // read-only to element
reverse_iterator // 逆序
const_reverse_iterator

(3)5种必要类型
difference_type: signed type, 
足以存储 2个迭代器差值

value_type: element type
reference : <=> value_type&
const_reference : <=> const value_type&

2. begin 和 end member function: 2对
c.begin()
c.end()    : 指向 最后一个元素 的 下一位置
c.rbegin() : <=> --c.end()
c.rend()   : <=> --c.begin()

这2对 member function 
都有 `const / non-const 2个版本`, 
调哪个 `取决于 容器 是 const / non-const`,
相应返回 的迭代器 为 
    const/non-const + _reverse 类型

3. 添加元素: 2组, 以 push 开头 & insert

t: value == t 的元素

//(1) push 开头: return void
//1)尾部添
c.push_back(t)

//2)头部添 / 只适用于 list deque
c.push_front(t)

//(2)insert: insert 的是 copy 
前插 / 
插 单个元素时, `return 新添元素 的 迭代器` / 
另2种版本, return void

c.insert(iter, t)
c.insert(iter, n, t)

c.insert(iter,begin, end)
// begin, end 可以是 `另一种类型的迭代器`, 
// 甚至可以是 `指针 or 地址`
// 只要 指向的 元素类型 可 转换为 容器元素类型 即可
```

`1)容器元素 都是 副本(copy): 添加 元素时, 是将 元素值 copy 到 容器 里`

`2)cin 接收 字符串 时, 遇 空格 tab 回车` 则 结束

`3)添加元素 可能使` begin() / end() / 其他某些 原位置 等 `迭代器 失效` =>  `使用 begin() / end() 等 迭代器 要取` 其 `更新 的值, 而不是 cache(缓存) 的 值`
```
能不 cache begin() / end() 等迭代器 就别 cache
```

```
// insert 1个:
list<string> lst;
list<string>::iterator iter = lst.begin();
while( cin >> word )
    // equivalent to push_front
    iter = lst.insert(iter, word);
```

![](http://upload-images.jianshu.io/upload_images/20172887-9b507b71895df93e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1080/q/50)

```
//insert 一段: arr是 数组 首元素地址, 转换为 pointer
string arr[2] = {"hello", "world"};
slist.insert(s.end(), arr, arr + 2);
```

![](http://upload-images.jianshu.io/upload_images/20172887-33a5f201d75190c0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1080/q/50)

```
4. 关系 操作符
(1)比较的容器, 要 同容器类型& 同 元素类型

(2)容器 的 比较 是基于 容器内元素的比较
=>用 元素类型 定义的 关系操作符 实现 容器的关系操作符

(3)返回 true / false

5. 容器 大小 操作: 3组

//(1) 实际 / 容量 大小
c.size()
c.max_size() // 能容纳的 最多元素个数

//(2)是否 空
c.empty() // <=> c.size() == 0

//(3)调整: 
1)指定/不指定 初始化式=>`按 指定式/值 初始化` 
2)若要调整的 n 小于 原 size() => `删除多余元素`

c.resize(n, t)

c.resize(n)

6. 访问 元素
(1)返回 `reference`
(2)由 `coder 确保 访问 有效/安全`: 
    `front/back 指向有效元素 / n 安全`

//1)front/back
c.back()
c.front()

//2)下标: 只适用于 vector & deque
c[n]

7. 删除 元素: 3组

//1)erase:`return 迭代器, 指向 被删元素(段)下一位置`
erase(iter)
erase(begin, end)//begin, end 不一定非要为 容器 的 迭代器 类型

//2)clear: return void, 删除容器内 所有元素

//3)pop_ : return void
pop_back()
pop_front() // 只适用于 list deque

(1)由 `coder 确保 删除 有效/安全`
(2)用 `pop_back/front 删`, 要想 获取删除的元素值, 
要在删除前 `先调 back()/front() 获取`
(3)equivalent delete:
lst.clear();
lst.erase(lst.begin(), lst.end() );

8. 赋值 与 swap

(1)operator==
c1 = c2

clear + insert (copy) + 
  同 容器类型 & 同元素类型

(2)assign: 赋值 要求太严, 无法使用时, 用 assign

c.assign(n, t)
c.assign(begin, end)

clear + insert (copy) + 
  容器类型 可不同,  元素类型 要能 converted 过去

(3)swap
c1.swap(iter1, iter2)
同 容器类型 & 同元素类型

总结:
(1)operator== 与 assign 同(异 见 上述): 
先 删除 左操作数容器 所有元素, 
再将 右操作数容器 所有元素 插入(copy) 到 左容器 中

=> `左操作数容器所有迭代器失效`

=> operator==内 `不能用 左容器 begin()/ end()等 迭代器`;
assign 内 `不能用 传递的迭代器 操作 容器内元素`

(2)swap
1)常量时间内交换

2)不 删除 或 插入 任何元素
=> 容器内 没移动任何元素
=> `迭代器 不失效: 仍指向原来元素, 
    只是 换到 指向另一容器中`

eg. 若某 iter 指向 svec1[2] 的 字符串, 则
swap 之后 它指向 svec2[2] 的 该同一字字符串
```

```
note: assignment 指 operator==, 不同于 assign

//eg1. assignment equivalent clear/earse + insert

c1 = c2;

//equivalent
c1.erase(c1.begin(), c1.end() );
c1.insert(c1.begin(), c2.begin(), c2.end())

// eg2. assign 最通用的场景 

vector<char*> vec;
vec.push_back("hello");
vec.push_back("world");

list<string> lst;
lst.assign(vec.begin(), vec.end() );

//eg3. swap 迭代器
vector<int> vec1(2, 1);
vector<int> vec2(3, 2);
vector<int>::iterator iter = vec1.begin();
std::cout << *iter; // 1
vec1.swap(vec2);
std::cout << *iter; // 1
```

## 9.4 vector 容器 自增长

```
1. vector 中 已无空间 容纳 新元素 时, vector 必须 
重新分配 存储空间, 以 存放 新添加的元素, 3步曲:
1)`copy` 旧存储空间中 元素 到 新存储空间
2)`接着 插入` 新元素
3)`撤销` 旧存储空间

2. vector vs. list

list 无需重新分配空间, 貌似 要优于 vector;
```

但实际上, 对 `大部分应用, 用 vector 最好`

原因: vector 采用 的 内存分配策略 为 `以最小的代价 连续存储元素`. 由此带来的 `访问元素的便利 弥补了 存储代价`

```
具体而言:
```
`每当 vector 不得不 重新分配 存储空间 时`, 才(大多)采用  `加倍当前容量` 的 分配策略 重新分配. `除此之外, capacity() > size()`, 即 实际分配的容量 大于 当前所需空间, 以预留额外存储区, 用于存放 新添加元素

```
分配策略取决于实现方式
```
```
3. capacity() / reserve(n)

capacity() 容量: 
指 容器 在必须 重新分配存储空间 之前 可以存储的元素数

reserve(n): set capacity to at least n, 
    might be more
```

```
//eg. capacity() & reserve(n) & 重新分配存储空间

#include <iostream>
#include <vector>

int main()
{
	std::vector<int> vec;

	//(1)
	vec.reserve(50);

	// 0 50
	std::cout << vec.size() << ' ' << vec.capacity() << std::endl;

	//(2)
	while ( vec.size() != vec.capacity() )
		vec.push_back(0);
	
	//50 50
	std::cout << vec.size() << ' ' << vec.capacity() << std::endl;
	
	//(3)
	vec.push_back(10);
	
	//Linux 下: 51 100, Windows 下 51 75
	std::cout << vec.size() << ' ' << vec.capacity() << std::endl;
}

// 加倍当前容量
```

##9.5 容器选用
```
list: 不连续内存区域
(1)插入/删除:
在任何位置 插/删, 不需要移动任何其他元素
(2)不支持 随机存储

2. vector: 连续内存区域
(1)插入/删除:
除尾部外,要移动 被插/删元素 `右边所有元素`

(2)支持随机存储

3. deque: 分段连续内存区域, 
用 下标操作符 实现像 真实 随机存储 一样的效果
(1)插入/删除:
除 首 尾 外,要移动被插/删元素`左或右边所有元素`

在首部 插/删 就像 vector 在尾部 插/删 一样

(2)用 [] 支持 随机存储, 但比 vector 随机存储慢, 因为
可能要通过 `中控器 用 指针 去 跨越 连续存储 block`
```
`通常, 除非找到 更好理由, 否则 vector 都是 最佳选择`

`无法确定 某应用 该用哪种容器时, 写 vector 和 list 都提供的操作: 用 迭代器, 而不是 下标, 且 避免 随机访问`

##9.6 再谈 string
```
pos2: string 对象 s2 的 下标 

//1)
//cp:const char* 指针, `要为 C风格字符串`
string s(cp)

//2)copy ctor
string s(s2)

getline(is/cin, s)

1. 构造 string 的 其他方法
编译器 在 copy 完最后一个字符后, 自动在末尾添 '\0'

//(1) 初始化为 cp 指向数组 前 n 个元素 的 copy
//cp:const char* 指针, `不必为 C风格字符串`
//`cp 再 带1个参数 n 时, 不必为 C风格字符串`

string s(cp, n)

//(2)初始化为 子串
//从下标pos2开始, (len2 个字符, 但最多复制到最后1个字符)
string s(s2, pos2) 
string s(s2, pos2, len2) 

2. 修改 string 对象 的其他方法

//--- 与 容器共有的 string 操作
insert / erase / assign 

3. string 类型 独有

(1)substr : 
返回(整个; 从 pos 开始 到结尾 / n个) 子串的 copy

s.substr()
s.substr(pos)
s.substr(pos, n)

(2)append(串接) / replace(删除 + 替换)
//replace(删除 指定区间元素 + 替换 为 args)

s.append(args)
s.replace(pos, len, args)
s.replace(begin, end, args)

args:
s2 / cp // C风格字符串
s2, pos2, len2
cp, len2

n, c
begin2, end2

4. 查找
// s 中 查找 args 第一次出现 位置
s.find(args)

// s 中 查找 args 的 任意字符 的 第一次出现 位置
s.find_first_of(args)

5. 比较
s.compare(s2 / cp) // cp: C风格
s.compare(pos1, n1, s2 / cp)
```
##9.7 容器 adapter

`adapter 本质: 使 一事物的行为 类似于 另一事物的行为 的机制`

`容器 adapter: 让 一种已存在的 容器类型 以 另一种抽象类型的工作方式 实现`

如 `stack: 可使 任一种顺序容器 以栈的方式 工作`

```
1. 初始化
//(1)default ctor
stack<int> stk1;

//(2)参数为 一个容器: copy elements
stack<int> stk2(deq);

2. 覆盖 底层容器: 
adapter 第2 类型实参 设为 顺序容器, 可 覆盖其底层容器

stack<string, vector<string> > str_stk;

3种 adapter: stack / queue / priority_queue
(1)default 底层容器:
deque / deque / vector

(2)要求底层容器支持
push_back()+pop_back() /
push_front() /
能随机访问

=>可以是
vector list deque /
list deque /
vector deque

3. 关系运算

第1对 不相等的元素 决定两者的大小关系

4. stack

empty()
size()
push(item)
pop()
top() // return reference 


所有 adapter 根据其 底层容器 的操作 实现自己的操作

5. 优先队列
(1)允许用户 为队列中元素 设置优先级

(2)元素不是放在队列尾部, 而是放在比它优先级低的元素前

(3)标准库默认 用 `元素类型的 < 操作符 确定优先级`

(4)应用: OS 调度表
```

#chapter 10 关联容器


```
(1)关联容器 与 顺序容器 本质区别: 存取 & 访问 元素的方式, 
通过 key / 元素在容器中的位置顺序

(2)set : 存储 不同值 得集合

map: 存储/修改 key 关联的 value

map 应用: 字典 
单词 是 key, 其 说明 是 value
```

##10.1 pair 类型
```
<utility>
1. 操作
//(1)构造

必须指定 2个 type 名, 对应2个 member data

未指定 /指定 初始化式 => 值初始化 / 指定式初始化

pair<T1, T2> p1; 

pair<T1, T2> p1(v1, v2); 

// pair<string, vector<int> > line;

//(2)创建
make_pair(v1, v2); // 不用指定 pair 元素类型

//(3)取 public member data
p.first
p.second

//(4)<  ==
p1 < p2
p1 == p2

2. pair 类型 使用繁琐, 可用 typedef 简化其声明
typedef pair<string, string> Author;
Author author1("lilei", "hanmei");
Author author2("xiaoyuer", "huawuque");

3. make_pair 

next_author = make_pair(first, last);
// equivalent to
next_author=pair<string,string>(first,last);

string first, last;
pair<string, string> next_author;
//(1)
while(cin >> first >> last)
{
	// generate a pair from first and last
	next_author = make_pair(first, last);
	
	// process next_author
}

//(2)
while(cin >> next_author.first >> next_author.last)
{
	next_author = pair<string,string>(first, last);
	
	// process next_author
}
```

##10.2 关联容器 操作

`容器 根据 key 的 次序 排列 => 迭代遍历 时, 可确保 按 key 的 顺序 访问元素, 与 元素在容器中 存放位置无关`
```
1. 与 顺序容器 共享的操作
(1)3种 ctor
C<T> c;
C<T> c1(c2);
C<T> c(begin, end);

(2)begin/end rbegin/rend 

(3)赋值 / swap

(4)clear / erase : 但 return viod

(5)容器大小: 但 无 resize()

(6)关系

2. 与 顺序容器 不同的操作
(1)不能通过 容器大小 定义

(2)无 push_back/front pop_back/front back/front

(3)无 assign

(4)无 resize() 
```
##10.3 map

```
1. ctor

map<K, V> m;

// m 与 m2 同 key/value 类型
map<K, V> m(m2);

//迭代器范围内 元素类型 要能 转换为 pair<const K, V>
map<K, V> m(begin, end);
```

`key 类型 必须定义 default 比较操作符 <`

```
// empty map from string to int
map<string, int> word_count;
```

```
2. map 相关类型
//1)
map<K,V>::key_type

//2)real type of V
map<K,V>::mapped_type

//3)类型为 (combination type of K and V):
// pair<const map<K,V>::key_type, 
//            map<K,V>::mapped_type >

map<K,V>::value_type
```

```
// eg. word_count 的 value_type 为 
//    pair<const string, string>
```

**(1)`解引用 map 迭代器` 将 产生 `pair 类型 对象` 的 `引用`**


```
std::map<std::string, int> word_count;

// add new element

// get an iterator to an element in word_count
std::map<std::string, int>::iterator iter = word_count.begin();
// *iter is a reference to a pair<const string, int> object

std::cout << iter->first;

//iter->first = "new key"; // error: key is const
++iter->second;
```

###3. 添加 新元素

```
2种方式:
1) insert
2) [] + 赋值
```
###3.1 用 下标 访问 元素

`1. 下标操作 的 过程`

```
std::map<std::string, int> word_count; 
word_count["Anna"] = 1;  // key or 下标值 == "Anna"
```
(1) map 中  `查找`  `以 该 key 为 索引 的 element`

(2) `找到 ( 与 vector 下标运算 行为相同 )` => **return 其 `value 的 reference` -> 赋值为 1;**

`没找到 ( 与 vector 下标运算 行为不同 )` => (3)

(3) `创建 并 插入` 以 该 key 为 索引 的 `新 element,`
其 `key 为 该 下标值`, value 用 `值初始化: 内置类型 初始化为 0; 类类型 用 default ctor 初始化`

(4)`读取` 新 element, 其 `value 赋值` 为1

**`note:` map `下标操作符 的 return type` 为 `type of value: map<K,V>::mapped_type;` 而 `迭代器解引用 的 return type` 为 `value_type: pair<const map<K,V>::key_type, map<K,V>::mapped_type >`**

```
=> 下标操作符  应用:

(1)返回左值
++word_count["Anna"];

(2)`使程序简练`

std::map<std::string, int> word_count;
std::string word;

// if key(word) in map: only ++value; 
// not in map: 
// 1)create & insert into map
// 2) value initialized to 0
// 3) ++value
while (std::cin >> word)
	++word_count[word]; 

```

###3.2 Insert
```
(1) 给定 ( 迭代器 区间 中 ) key `在 m 中`, `do nothing`; 
`不在 m 中, 插入`
(2) 给定 ( 迭代器 区间 中 ) elem 为 m 的 
`value_type 类型, 即 pair<const K, V> 类型`

//(1) return pair< map<const K, V>::iterator, bool >
// iterator: point to the given ( key's ) elem 
// bool: indicate whether insert or not
m.insert(elem)

//(2) return iterator, 
//point to the given ( key's ) elem 
// 从 iter 开始搜索
m.insert(iter, elem)

//(3)return void
m.insert(begin, end)
```

`用 insert 代替 [] 的好处: 避免了 [] 带来的 ( 对 elem 的 value ) 不必要的 值初始化`

```
word_count.insert( std::map<std::string, int>::value_type("Anna", 1) ); 

// arg : std::map<std::string, int>::value_type("Anna", 1)
// 是 新创建 的 pair 对象

// 简化 1: std::make_pair
word_count.insert( std::make_pair("Anna", 1) );

// 简化 2: typedef
typedef std::map<std::string, int>::value_type pari_type;
word_count.insert( pari_type("Anna", 1));
```

```
// eg: check return value of insert
#include <iostream>
#include <map>
#include <string>
#include <utility>

using std::map;
using std::string;
int main()
{
	std::map<std::string, int> word_count;
	std::string word;

	while (std::cin >> word)
	{
		std::pair< std::map<std::string, int>::iterator, bool> ret =
			word_count.insert( std::make_pair(word, 1) );

		// if not insert, that's the given key already in map,
		// increase counter
		if( ! ret.second ) 
			++ret.first->second; 
		//1)ret.first : map<string, int>::iterator
		//2)ret.first->second == (*ret.first).second: 
		// dereference iterator + fetch second member ==
		// value_type == pair<const string, int> + fetch value/int
	}
}
```

###4. 查找 读取 map 中 elem / pair

`1. 只 查找`
```
//(1)返回 key 出现的次数: 对 map -> 0 / 1;
// 对 multimap -> 0 / >= 1
m.count(key)

//(2)若 找到, 则 返回 目标 elem / pair 的 iterator; 
//否则, 返回 end() 迭代器
m.find(key)
```

`2. 查找 + 读取`

(1)`下标 操作符`
`副作用`: 若 key 不存在, 则 插入
```
map<string, int> word_count;
int occurs = word_count["hello"];
```

(2)solution1: `count() != 0 时, + 下标操作符`, 则 safe, 只查找 不插入
Problem: 2次查找
```
int occurs = 0; 
if( word_count.count("hello") )
    occurs = word_count["hello"];
```

(3)solution2: `find() ->  found/ iter != end() 时, + 解引用 *iter / iter-> 得 目标 elem / pair 的 reference`
```
int occurs=0; 
map<string, int>::iterator iter =  
    word_count.find("hello");
if( iter != word_count.end() )
    occurs = iter->second;
```

###5. 删除 map 中 elem

```
3种 变式:
1)删除 key / map iterator (scope) 指定的 elem
2)return 
size_type 型 删除的元素数, 
对 map 只能是 1 或 0, 表示 elem 是/否 在 map 中 
/ void 
/ void

3)由 coder 保证 删除 scope 中 元素有效

m.erase(key)

m.erase(iter)

m.erase(iter1, iter2)
```

###6. 迭代遍历 map object

```
map<string, int>::const_iterator 
    iter = map_obj.begin();

while(iter != map_obj.end() )
{
    cout << iter->first << iter->first;
    ++iter;
}
```

###7. 应用: 单词转换 map object
```
创建 查找 迭代遍历 map object

(1)单词转换文件 的 content 存储到 map,
(2)被替换 的单词作 key, 替换为 的单词 作 value
(3)读取输入, 查找 其 是否在 map 中, 
在 则 转换后输出; 否则, 直接输出
```

![](http://upload-images.jianshu.io/upload_images/20172887-603d90ea8c6c407d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1080/q/50)

```
#include <iostream>
#include <fstream>  // std::ifstream
#include <sstream>

#include <map>
#include <string>
#include <utility>  // std::pair
#include <exception>

using std::map;
using std::string;

/*
* transform words
* 2 args: name of word transformation file,
*		  name of input to transform
*/
int main(int argc, char** argv)
{
	//(1)map to hold word transformation pairs:
	std::map<string, string> trans_map;

	if (argc != 3)
		throw std::runtime_error("wrong number of args");

	std::ifstream map_file;
	map_file.open( argv[1], std::ifstream::in );

	string key, value;
	while ( map_file >> key >> value)
		trans_map.insert( std::make_pair(key, value) );

	std::ifstream input_file;
	input_file.open(argv[2], std::ifstream::in);

	string line;

	// 1)getline: 读 ifstream -> string
	while ( std::getline(input_file, line) ) 
	{
		//2) string -> 初始化 -> istringstream
		std::istringstream stream(line); 

		string word;
		bool on_off_flag = true;

		//3) istringstream >> 类 cin >>:  istringstream -> string
		while (stream >> word) // word using to cache/store key
		{
			map<string, string>::const_iterator iter =
				trans_map.find(word);

			if (iter != trans_map.end())
				word = iter->second; // word changed to be value to be printed

			if (on_off_flag)
				on_off_flag = false;
			else
				std::cout << " ";

			std::cout << word;
		}
		std::cout << std::endl;
	}
}

循环的 每两次迭代之间 输出 space: 
用 开关 on_off_flag, 
每次迭代 转换 on/off == true / false
```

##10.4 set

`1. set 存储的 element 只是 key, 而没有关联的 value`

=>

`1) 不支持 下标操作`
`2) 没定义 mapped_type; value_type == key_type, 而不是 pair`

```
2. 操作: 除了上述与map不同, 其余相同
```

```
//eg.
set<int> iset( ivec.begin(), ivec.end() );

若 ivec 有 20个元素, 且 两两相等, 
则 iset 只有 10个元素: key 各不相同
```

`3. 单词统计程序: 只统计 单词排除集 中的单词`

```
void restricte_wc(ifstream &remove_file, 
        map<string, int>&word_count)
{
	set<string> excluded;
	string remove_word;
	while(remove_file >> remove_word)
		excluded.insert(remove_word);

	string word;
	while(cin >> word)
		if( !excluded.count(word) )// not in 排除集
			++word_count[word];
}

//若 word not in 排除集:
// (1)若 word 还没出现过, 
1)插入 
2)关联的 value 初始化为 0
//(2)无论 word  是否出现过, 
关联的 value 自增
```

##10.5 multimap / multiset

###1. `1 key 多 value` 下的 添加 / 删除

`1 key 多 value`
=>
`(1)key 不唯一 => insert 的 elem/pair 只要 key or value 不存在, 就会 添加 elem`

`(2)不支持 下标操作符`

`(3)erase: return type 与 map/set 相同`
1)参数为 key, `删除 拥有 key 的 所有 elememt`

2)参数为 1个/对 迭代器, `只删除 指定 element`

```
相对于 map / set, multimap / multiset 中 
相同操作 以不同方式做了修改
```
###2 查找

map/set / multimap / multiset 的 element 都是 `按 顺序存储` =>  `同一个 key 关联的 多个 element 相邻存放 => 查找有3种策略, 依次更优雅简洁`

`(1)count + find`
count `求出 key 出现次数`

find `返回 迭代器, 指向 key 关联的 第1个 elememt`

`(2)lower_bound + upper_bound`

```
m.lower_bound/upper_bound(k)
```
```
返回 迭代器, 指向 key >= / > k 的 第1个 elem
=>[m.lower_bound, upper_bound(k) )
为 k 关联的 所有 elem 的 区间

(1)若 k 存在 =>
指向 k 关联的 第1个elem 的位置 / 
    最后1个 elem 的 下一位置

(2)若 k 不存在 => `两个迭代器 相同`
指向 依 元素排列顺序 该 k 应插入的位置
1)指向 同一元素
2)指向 end() 迭代器 下一位置
```

`(3)equal_range`

```
m.equal_range(k)

//<=>
make_pair( m.lower_bound(k), m.upper_bound(k) )
```
```
return pair 对象, 
first  成员 等价于 m.lower_bound(k),
second 成员 等价于 m.upper_bound(k)
```

```
#include <iostream>
#include <string>
#include <map>
#include <utility> // std::pair relevant

int main(int argc, char** argv)
{
	std::multimap<std::string, std::string> authors;
	authors.insert( std::make_pair( std::string("zhangsan"), 
		             std::string("book1")) );
	authors.insert(std::make_pair(std::string("zhangsan"),
		std::string("book2")));

	// ---1.count + find
	std::string search_item("zhangsan");

	typedef std::multimap<std::string, std::string>::size_type sz_type;

	sz_type occurs_num = authors.count(search_item);

	typedef std::multimap<std::string, std::string>::const_iterator Iter;
	
	Iter iter = authors.find(search_item);
	for (sz_type i = 0; i != occurs_num; ++i, ++iter)
		std::cout << iter->second << std::endl;

	//---2.lower_bound + upper_bound
	Iter begin = authors.lower_bound(search_item),
		end = authors.upper_bound(search_item);
	while (begin != end)
	{
		std::cout << begin->second << std::endl;
		++begin;
	}

	//---3.equal_range
	std::pair<Iter, Iter> equ_range 
		= authors.equal_range(search_item);
	while (equ_range.first != equ_range.second)
	{
		std::cout << equ_range.first->second << std::endl;
		++equ_range.first;
	}
}
```

##10.6 容器综合应用: 文本查询程序
```
需求: 
用户 指定 任意文本文件(可用 main argv 传入),
可 与用户 交互: 提示用户 `输入要查询的 word`

`输出 查询结果`: 
该 word 出现 次数 + 所有 line  行号 + content
某单词在1行中出现多次时, 只显示1次
=> 每个 word, 其所有行号 line_no 的 集合 只统计1次 
=> 可用 set
```

###1. design text_quary program

`设计程序的1个好习惯:`

`列出 程序所涉及的 operations`, 有助于建立 data structure 和 implement these operatorations

![image.png](https://upload-images.jianshu.io/upload_images/20172887-812ff05037f00892.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/20172887-08b7358c08bbc4a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`1. receive 传入 用户指定的 file -> ifstream -> lines -> store to vector`
`通过 文件流 将 file content 分解为 多行, 存到 vector`

```
std::vector< std::string > text_lines_vec;
```

```
// file -> ifstream -> lines -> store to vector
//1) receive file
argv[1] == file_path

//2) connect file to ifstream object related: open file
std::ifstream query_file;
query_file.open(argv[1], std::ifstream::in);

//3)store file to vector line by line
void store_file_to_vector(std::ifstream& ifs)
    std::string line;
    while ( std::getline(ifs, line) )
        text_lines_vec.push_back(line);
```

`2. vector 存 各 lines`
`=> naturally, vector 包含了 line_no/行号 到 line_content 的 关系,`
`即 以 line_no 作 下标, 查询 vector, 就能得到 line_content`


而 `目标: 是 通过 word 查询 line_content`
=> 原本需要 `记录 每个 word 到其 所有 line 的 line_content 的 关系`
现在 `转化为` 只需要 `记录 每个 word 到其 所有 line_no 的 关系`

```
// => 
1)`1个 word 的 所有 line_no 用 可 set 存`, 
刚好 `解决了 1个word 在1行中出现多次时, 其 line_no 只 记录 1 次`
set 中 添加 的 key 值 已存在时, 啥也不干

note: 若 1个 word 的 所有 line_no 用 vector 存,
则 `要添加的行号 不是 vector 中 最后1个元素时, 才能添加`,
`否则, 该 word 在 该 行 前面已出现过`

2)所有 word 的 lines_no 用 map 存
std::map< std::string, std::set<Line_no> > word_map;
```

```
(1) line_no 作 下标, 查询 vector, 得到 line_content
`line_no -> line_content:`

std::string 
Text_query::get_line(Line_no line_no) const

(2) line_no + text_lines_vec => build map of word to lines_no
1)line_no -> line_content : text_lines_vec[line_no]

2)line_content -> word : (istringstream) line >> (string) word

3)insert word's lines_no:
word_map[word] is word_lines_no_set
   set.insert(line_no)

void build_map_of_word_to_lines_no()
	Line_no Loop: [0, text_lines_vec.size() )
		std::istringstream line( text_lines_vec[line_no] );
		
		line Loop: while (line >> word)
			word_map[word].insert(line_no);
```

`word_map: type std::map< std::string, std::set<Line_no> >`
`=> word_map[word] : return word_map's value's reference, type is std::set<Line_no>`


`3. query_word_line_no_set`
```
// word -> word_lines_no_set 
std::set<Text_query::Line_no>
query_word_line_no_set(const std::string& word) const
    iter = word_map.find(word);

    if (iter != word_map.end())
        return iter->second; // return set by value, 可以换成 by const reference
```

`4. print result`
```
// 1)word_lines_no_set.begin() / end() / size() 以确定 遍历范围
// => 要传入 word_lines_no_set的 const 引用
// 2) get_line(*iter) 以获得行内容 
// => 要传入 Text_query obj 的 const 引用
// => print_resuts 作 non-menber function
// 3)word 输出提示中 用
void print_resuts(const std::string& word,
		const std::set<Text_query::Line_no>& word_lines_no_set,
		const Text_query& text_query)
		
	std::cout << "\n" << word << " occurss "
        << word_lines_no_set.size() << " times:\n"
		
    iter = word_lines_no_set.begin();
    for (; iter != word_lines_no_set.end(); ++iter)
        std::cout << "line "<< *iter + 1 << ":"
			<< text_query.get_line(*iter) << std::endl;
```


`whole program`
```
// text_query.h
// 为简单起见, text_query.h 和 text_query.cpp 都放 工程的 include 目录
#include <string>
#include <vector>
#include <map>
#include <set>

class Text_query
{
public:
	typedef std::vector<std::string>::size_type Line_no;

	void build_data_stru_from_file(std::ifstream& ifs)
	{
		store_file_to_vector(ifs);
		build_map_of_word_to_lines_no();
	}
	std::set<Line_no> query_word_line_no_set(const std::string&) const;
	std::string get_line(Line_no) const;
	
private:
	// store content of the query file
	std::vector< std::string > text_lines_vec;

	// (one) word to (multi) lines_no set: word unique => use map
	std::map< std::string, std::set<Line_no> > word_map; // line nomber

	// utility functions used by build_data_stru_from_file
	void store_file_to_vector(std::ifstream& ifs);

	void build_map_of_word_to_lines_no();
};
```

```
// test.cpp
#include <iostream>
#include <fstream>  // std::ifstream
#include <sstream>
#include <map>
#include <string>
#include "text_query.h"

/*
1) word    -> word_lines_no_set
2) line_no -> line_content: traverse word_lines_no_set
std::set<Text_query::Line_no>::const_iterator 
	   iter =  word_lines_no_set.begin();
for(; iter != word_lines_no_set.end(); ++iter)
    text_query.get_line(*iter) // *iter == line_no_of_word
*/
void 
print_resuts(const std::string& word,
	const std::set<Text_query::Line_no>& word_lines_no_set,
	const Text_query& text_query)
{
	typedef std::set<Text_query::Line_no> word_lines_no_Set;

	std::cout << "\n" << word << " occurss "
		<< word_lines_no_set.size() << " times:" << std::endl;

	word_lines_no_Set::const_iterator 
		iter = word_lines_no_set.begin();
	for (; iter != word_lines_no_set.end(); ++iter)
		std::cout << "line "
		<< *iter + 1 << ":"
		<< text_query.get_line(*iter) << std::endl;
}

int main(int argc, char** argv)
{
	std::ifstream query_file;

	if (argc != 2)
		return 1;

	query_file.open(argv[1], std::ifstream::in);

	// just for calling member function
	Text_query text_query;
	
	text_query.build_data_stru_from_file(query_file);

	// iteract with user
	while (true)
	{
		std::cout << "---enter a word to query, or q to quit: ";
		std::string s;
		std::cin >> s;

		if ( !std::cin || s == "q")
			break;

		std::set<Text_query::Line_no> word_lines_no_set = 
			text_query.query_word_line_no_set(s);

		print_resuts(s, word_lines_no_set, text_query);

		std::cout << std::endl;
	}
}
```

```
// text_query.cpp
#include <string>
#include <map>
#include <iostream>
#include <fstream>  // std::ifstream
#include <sstream>
#include "text_query.h"

void 
Text_query::store_file_to_vector(std::ifstream& ifs)
{
	std::string line;
	while ( std::getline(ifs, line) )
		text_lines_vec.push_back(line);
}

void
Text_query::build_map_of_word_to_lines_no()
{
	for (Line_no line_no = 0;
		line_no != text_lines_vec.size();
		++line_no)
	{
		std::istringstream line( text_lines_vec[line_no] );

		std::string word;

		while (line >> word)
			word_map[word].insert(line_no);
		// word_map[word]: type std::map< std::string, std::set<Line_no> >
		//=> word_map[word] : type  std::set<Line_no>
	}

}

// word   -> word_lines_no_set  //<=>
// string -> set<Text_query::Line_no>
std::set<Text_query::Line_no>
Text_query::query_word_line_no_set(const std::string& word) const
{
	std::map< std::string, std::set<Line_no> >::const_iterator
		iter = word_map.find(word);

	if (iter == word_map.end())
		return std::set<Line_no>(); // not found, return empty set
	else
		return iter->second; // return set by value, can change to do better
}

// line_no -> line_content
// Line_no -> std::string 
// only get a line, 
// if get all lines needs to traverse word_lines_no_set 
// use a non-member function
std::string 
Text_query::get_line(Line_no line_no) const
{
	if (line_no < text_lines_vec.size())
		return text_lines_vec[line_no];
	std::cout << "line number out of range :" << std::endl;
	return std::string();
}
```

```
//query_text.txt
hello world, hello liyu!
hello all the good friends.
hello all the good men.
```

![image.png](https://upload-images.jianshu.io/upload_images/20172887-0df2e5f53727f9b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#chapter 11 泛型算法

容器的 更多操作由 标准库 中 `独立于特定容器 的 泛型算法 实现`, 可用于 `不同类型的容器、不同类型的 element, 只隐式地 依赖 elememt type: 要能对 element 做 诸如 比较 的 操作`

##11.1 概述

`1. 算法 要求`
```
以 find 为例, 有 4个要求: 要能
(1)遍历 容器
所有 迭代器 都支持 `++ 以 移动` 到 指向下1元素,
`解引用 以 访问`

(2)知道 是否到达 容器尾部
end() 迭代器:
1)当 `尾部 哨兵`
2)== return 的 iterator 时, 表示 `未找到`

(3)比较 容器中元素 与 查找元素
默认下, find 提供 == 操作符
若 `elem type 不支持 == 或 打算用其他方法比较`,
可用 算法 `第2版本, 提供1个额外参数: 比较函数`

(4)指出 找到的元素在容器中的位置, 或 未找到
```

`2. 算法 从不执行 容器提供的操作`

`泛型算法 本身 不执行容器操作, 基于 迭代器 及其操作 实现, 而不是基于 容器操作 实现`
=> 用 `普通(非插入/删除)迭代器` 时, 算法 `从不修改 容器大小`, 最多 在容器内 `修改 / 移动`元素; `赋值时, 赋值给 容器内元素` 

`插入迭代器: 赋值时, 迭代器 可能导致` 给容器 `添加元素, 但 算法本身 从不 添加元素`

`3. 常用 泛型算法`


```  
//(1) copy     
template<class InputIterator, class OutputIterator>
OutputIterator copy(InputIterator first, InputIterator last,
                                    OutputIterator result);

char* copy(const char* first, const char* last, char* result);

const wchar_t* copy(const wchar_t* first, const wchar_t* last, wchar_t* result);

//(2) fill / fill_n			  
template< class ForwardIt, class T >
void 
fill( ForwardIt first, ForwardIt last, const T& value );

template< class OutputIt, class Size, class T >
OutputIt 
fill_n( OutputIt first, Size count, const T& value );

```

```   
//(1) accumulate
template< class InputIt, class T >
T 
accumulate( InputIt first, InputIt last, T init );
               
//(2) count / count_if
template< class InputIt, class T >
typename iterator_traits<InputIt>::difference_type
count( InputIt first, InputIt last, 
       const T &value );
    
template< class InputIt, class UnaryPredicate >
typename iterator_traits<InputIt>::difference_type
count_if( InputIt first, InputIt last, 
          UnaryPredicate p );  
		  
//(3) find / find_if / find_first_of
template< class InputIt, class T >
InputIt 
find( InputIt first, InputIt last, 
	  const T& value );

template< class InputIt, class UnaryPredicate >
InputIt 
find_if( InputIt first, InputIt last,
         UnaryPredicate p );
	
template< class ForwardIt1, class ForwardIt2 >
ForwardIt1 
find_first_of( ForwardIt1 first,   ForwardIt1 last,
               ForwardIt2 s_first, ForwardIt2 s_last );
			  
//(4) replace / replace_copy
template< class ForwardIt, class T >
void 
replace( ForwardIt first, ForwardIt last,
         const T& old_value, const T& new_value );
		 
template< class InputIt, class OutputIt, class T >
OutputIt 
replace_copy( InputIt first, InputIt last, 
			  OutputIt d_first,
              const T& old_value, const T& new_value );
```
	

```		
//(1) sort / stable_sort	  
template< class RandomIt >
void 
sort( RandomIt first, RandomIt last );
	
template< class RandomIt, class Compare >
void 
sort( RandomIt first, RandomIt last, 
	  Compare comp );
	
template< class RandomIt, class Compare >
void 
stable_sort( RandomIt first, 
             RandomIt last, 
             Compare comp );
			 
//(2) reverse / reverse_copy
template< class BidirIt >
void 
reverse( BidirIt first, BidirIt last );
			 
template< class BidirIt, class OutputIt >
OutputIt 
reverse_copy( BidirIt first, BidirIt last, 
		      OutputIt d_first );

//(3) merge
template< class InputIt1, class InputIt2, class OutputIt >
OutputIt 
merge( InputIt1 first1, InputIt1 last1,
	   InputIt2 first2, InputIt2 last2,
	   OutputIt d_first );
	   
template< class InputIt1, class InputIt2, class OutputIt, 
		  class Compare>
OutputIt 
merge( InputIt1 first1, InputIt1 last1,
       InputIt2 first2, InputIt2 last2,
       OutputIt d_first, 
	   Compare comp );	

//(4) remove	   
int 
remove( const char* fname );
	
//(5) unique
template< class ForwardIt >
ForwardIt 
unique( ForwardIt first, ForwardIt last );	
	
template< class ForwardIt, class BinaryPredicate >
ForwardIt 
unique( ForwardIt first, ForwardIt last, 
	    BinaryPredicate p );	
```		  
		 	  
##11.2 初见算法

```
1. 头文件
泛型算法 必须 #include <algorithm>
算术算法 还必须 #include <numeric>

2. input range
除少数例外, 大多算法在 一段范围 的 元素上 操作
```
###1 read-only 容器 算法
```
(1)find

(2)accumulate
int sum=accumulate(vec.begin(),vec.end(),10);
```
第3 arg 作用:
1)做初值
2)`告诉 accumulate` 要累加的 element type, `容器内 元素类型 必须可 匹配 / converted 为 该 type`

```
(3)find_first_of
```
2对 迭代器, 在 `第1段范围内` 查找 `与 第2段范围内 任意元素 匹配 的元素`, 返回 `指向 第1个匹配的元素` 的 迭代器 或 `end() 迭代器`

```
// 查找 有多少个值 同时出现在 2 个 container 中
// 只考虑 vec1 中 无重复 的 场景

#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric> // std::accumulate

int main()
{
    size_t match_count = 0;
    int a[2] = { 1,5 };
    int b[6] = { 1, 1, 2, 3, 4, 5 };
    std::vector<int> vec1(a, a + 2);
    std::vector<int> vec2(a, a + 6);

    std::vector<int>::iterator iter =
        vec1.begin();

    while (iter != vec1.end())
    {
        if ( (iter = std::find_first_of(
                iter, vec1.end(),
                vec2.begin(), vec2.end() ) 
              ) != vec1.end() ) // found
            ++match_count;
        ++iter;
    }
    std::cout << match_count << std::endl; // 2

    int sum = std::accumulate(vec1.begin(), vec1.end(), 10);
    std::cout << sum << std::endl; // 16
}
```

###2 write elem of container 算法

`非 插入迭代器 时, 由 coder 保证 要写的 容器元素 存在`
 
```
1. fill : safe
template< class ForwardIt, class T >
void 
fill( ForwardIt first, ForwardIt last, const T& value );

fill(vec.begin(), vec.end(), 0);

2. fill_n : disaster if there aren't elems in writing scope 
template< class OutputIt, class Size, class T >
OutputIt 
fill_n( OutputIt first, Size count, const T& value );

fill_n(vec.begin(), 2, 0);
```

`3. back_inserter 函数`
```
// in < iterator >
template< class Container >
std::back_insert_iterator<Container> 
back_inserter( Container& c);
```

(1)为`解决` fill_n 可能存在的 `写 范围内 无 element 而 导致`的 
`write to nonexistent element in containter 问题`, 引入 back_inserter

(2)机制

```
back_inserter 函数是 `迭代器适配器`,
arg 是一个对象, 生成一个新对象
```
`arg为容器的引用`,back_inserter 生成一个`绑定 在该容器上的 插入迭代器`
=>
用 `插入迭代器 赋值(容器 元素)` 时, `赋值 操作符 调用 push_back 给容器中 添加元素`
```
容器为 map/set 时, 若元素已存在, 则只是赋值; 若不存在, 则添加
```

```
用 `普通迭代器`  赋值 ( 容器 元素 )` 时, 被赋值的元素 要存在
```
```
//ivec1/ ivec2 : empty

//1)safe: back_insterer creates an insert iterator 
// that adds elements to vecr
std::fill_n(std::back_inserter(ivec1), 10, 6);

//<=> 2) ivec1 ivec2 capacity 可能不同
ivec2.insert(ivec2.begin(), 10, 6);
```

`4. 写入到 目标 迭代器`
`copy: 从 input range 读取, copy 给 目标`

若要 `以  已存在的容器 为 copy 创建 新容器 时, 直接用 input range 作 新容器 初始化式 is a better way`

```
// ilist: 10 20
// ivec : empty

//(1)
std::copy(ilist.begin(), ilist.end(),
		  std::back_inserter(ivec) );

//(2) better way
std::vector<int> ivec2(ilist.begin(), ilist.end());
```

`5. 算法的 _copy 版本`

`_copy 版本: 输入序列 的元素 copy 到 新创建的序列, 处理 新序列` => 相当于对 原序列 只处理 不修改

```
template< class ForwardIt, class T >
void 
replace( ForwardIt first, ForwardIt last,
         const T& old_value, const T& new_value );

template< class InputIt, class OutputIt, class T >
OutputIt 
replace_copy( InputIt first, InputIt last, 
			  OutputIt d_first,
              const T& old_value, const T& new_value );
```

```
//ilist : 1 2

//(1)replace
std::replace(ilist.begin(), ilist.end(), 1, 10);

//<=> for iter = [ ilist.begin(), ilist.end() )
//    if(*iter == 1) 
//        *iter = 10;

std::vector<int> ivec;
//(2)replace_copy
//若不想改变原序列, 用 ivec 存 ilist 的 copy,
//再处理 ivec
std::replace_copy(ilist.begin(), ilist.end(),
	std::back_inserter(ivec), 10, 100);
```

###3. 重新排序 容器元素 的 算法

```
需求: 统计 一组 word 中, 长度 >= 6 的 word 及其 个数
按 `长度大小` 输出, 长度相同时 按 `字典顺序` 输出

solution:
(1)去重
1)std::sort
to make duplicates adjacent

2)std::unique 
reorder, so that each elem
appears once in the front portion,
returns iterator one past the unique range

std::vector<std::string>::iterator unique_end =
        std::unique(words.begin(), words.end());

3)container_obj.erase:
use container's mem func to remove nonunique elems 

words.erase(unique_end, words.end() );
erase is safe: unique_end 至多 == words.end()

(2)按长度排序 + 长度相同时, 按字典排序
// sort words by size, 
// but maintain alphabetic order 
//   for words of the same size
std::stable_sort(words.begin(), words.end(), is_shorter);

bool is_shorter(const std::string& s1, const std::string& s2)
	return s1.size() < s2.size();

=> stable_sort 结果 按 word.size() 升序排列

(3)统计 长度>= 6 的单词个数
// count_if
std::vector<std::string>::size_type word_count =
	std::count_if(words.begin(), words.end(), longer_than_6);

(4)逐个输出 : 要 忽略前面 所有的 unique elems
```

`(1) unique: 把 无重复的元素 copy/移动 到 序列前端, 从而 覆盖 相邻的重复元素, 后端元素值 被析构, 但 空间还没 destory/删除 => 用 容器操作 erase 去 删除 后端元素空间`

`算法 不直接 修改容器大小, 若想 添加 / 删除 元素, 必须 用 容器操作`

`(2)谓词 / predicate 函数: 返回 bool 值, 指示 检测的 条件 是/否 成立`

**(3) sort_stable: 对 `相等元素 保留其 原始相对位置`, 我们 `将 "相等" 定义为 “相同长度”` => 对 `相同长度` 的元素, `保留其 字典顺序`**

**=> sort_stable 可用于 在 `已进行了 某种维度排序 的 基础上`, 再 `叠加 另一维度 的 排序`. 可保证, `对 新维度下 的 相等元素` 保留  `原维度下 的 相对位置`**

`(4)count_if 的 func 只能带 1个 arg`=> func 无法重构得 更通用, 即 `无法再 传1个arg 表示 要比较的长度`

```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

// 将 "相等" 定义为 “相同长度”
bool is_shorter(const std::string& s1, const std::string& s2)
{
	return s1.size() < s2.size();
}

bool longer_than_6(const std::string& s)
{
	return s.size() >= 6;
}

void print_results(const std::vector<std::string>::iterator& begin,
				   const std::vector<std::string>::iterator& end,
				   std::vector<std::string>::size_type word_ignore)
{
	
	std::vector<std::string>::iterator iter = begin;

	std::vector<std::string>::size_type i = 0;

	while (i < word_ignore)
	{
		++i;
		++iter;
	}
	while (iter != end)
	{
		std::cout << *iter << " ";
		++iter;
	}
	std::cout << std::endl;
}

int main()
{
	//(1)insert/push input words 
	// word by word to vector
	std::vector<std::string> words;
	std::string next_word;
	while (std::cin >> next_word)
	{
		words.push_back(next_word);
	}

	//(2)sort words alphabetically 
	//to make duplicates adjacent
	sort(words.begin(), words.end());

	//(3)unique: reorders words so that 
	// each word occurs once 
	//in the front portion in the container,
	//returns an iterator one past the unique range
	std::vector<std::string>::iterator unique_end =
		std::unique(words.begin(), words.end());

	//(4)use vector's mem func to remove the nonunique elems 
	//in the last portion in the container
	words.erase(unique_end, words.end() );

	//(5)sort words by size, but maintain 
	// alphabetic order for words of the same size
	std::stable_sort(words.begin(), words.end(), is_shorter);

	//(6)count_if
	std::vector<std::string>::size_type word_count =
		std::count_if(words.begin(), words.end(), longer_than_6);

	std::cout << word_count << " words longer than 6 characters \n";

	print_results(words.begin(), words.end(), words.size() - word_count);
}

/*
hello the beautiful day hello the wonderful world
容器的 size() 变化:
sort   : 8
unique : 8
erase  : 6
*/
```

![image.png](https://upload-images.jianshu.io/upload_images/20172887-af8ed07f59273e96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##11.3 迭代器 3种维度 的划分
```
(1)insert / iostream / reverse
in <iterator>

1)插入迭代器: 
`与 容器 绑定`, 实现 `在容器中 插入元素`

2)iostream 迭代器: 
`与 IO stream 绑定`, 
用于 `迭代遍历 所关联的 IO stream`

3)反向 迭代器: 
实现 `向后遍历`
所有容器 都定义了自己的 revere_iterator 类型, 
由 rbegin() / rend() 返回

(2)const / non-const

(3)input / output / forward / bidirectional / random_access
```
迭代器 不依赖于 特定容器


###1 插入迭代器 / insert iterator

(1)是 1种 iterator `adapter`

(2)`至少带 1 个 容器参数, inserter 还带 第2 arg: 指向 插入起始位置的 迭代器`

(3)`机制:` 通过 插入迭代器 `赋值` 时, 相应的 
`back_inserter / front_inserter / inserter 函数`

`1)创建 1个 iterator`
`2)调用 该 iterator 关联的 容器 的 push_back / push_front / insert` 成员函数 `代替 赋值`

(4)`front_inserter vs. inserter + container's begin() 迭代器`

1)`总是插入到 更新的 begin() 迭代器 前`
2)总是插入到 `最初 固定位置 / 旧 的 begin() 迭代器 前` 


```
//eg. find + inserter + replace_copy

// ivec : 3 4 5
// ilist: 1 2

//1) position an iterator into ilist
std::list<int>::iterator iter =
	std::find(ilist.begin(), ilist.end(), 2);

//2)从 源 scpoe 元素 一个个 `每次都 copy/insert 到` 
// 定位的 `固定(迭代器)位置 前`, 
// 并把 值为 3 的元素 替换为 33
// ilist: 1 33 2 -> 1 33 4 2 -> 1 33 4 5 2
std::replace_copy(ivec.begin(), ivec.end(),
	std::inserter(ilist, iter), 3, 33);
```

```
//eg. front_inserter vs. inserter + container's begin() 
// results are different

std::list<int> ilist, ilist2, ilist3, ilist4;

// after loop, ilist1 : 3 2 1 0
for (std::list<int>::size_type i = 0; i != 4; ++i)
	ilist.push_front(i);

// after copy, list2: 0 1 2 3
std::copy(ilist.begin(), ilist.end(), 
	std::front_inserter(ilist2) );

// after copy, list3: 3 2 1 0
std::copy(ilist.begin(), ilist.end(),
	std::inserter(ilist3, ilist3.begin() ) );

// after copy, list3: 3 2 1 0
std::copy(ilist.begin(), ilist.end(),
	std::back_inserter(ilist4) );
```

###2 iostream 迭代器

`1. 概述`

`(1) 流迭代器 istream_iterator / ostream_iterator `
1)是 `class template`
2)用于 `读输入流 / 写输出流`
3)将其对应的 `流 视为 特定类型的 元素序列`
4)可用 `泛型`算法  `从 流对象中 读数据 / 写数据 到 流对象中`

```
(2) iostream 迭代器 ctor

//1. 读: 创建 istream_iterator obj
1) empty obj : 作 end iterator 
`绑在流上 的 迭代器 遇 文件结束 或 出错 时`, 等于 该值

2) 从 `输入流 istream 中 读 T 类型对象` 的 obj

istream_iterator<T> in_iter;
istream_iterator<T> in_iter(istream);

//2. 创建 ostream_iterator obj
1)将 T 类型 对象 写到 `输出流 ostream`
2)且 写过程中, 用 `C风格字符串 delim 作 元素 分隔符`

ostream_iterator<T> out_iter(ostream);
ostream_iterator<T> out_iter(ostream, delim);
```

```
(3) 流 迭代器 操作

只定义最基本的

1)自增: 
++stream_iter : return `加1后的 迭代器 的 reference`
stream_iter++ : return stream_iter 原 value

2)解引用 : 
*stream_iter  
istream_iter->mem : istream_iterator 有 / ostream_iterator 没有

3)赋值: 

4)== ！= ： istream_iterator 提供 / ostream_iterator 不提供

note: ostream_iterator `没有  3种操作符: stream_iter-> == !=`
```

`2. 流迭代器 定义/构造`

(1)流迭代器 是 `class template:` 若 `某 type (eg. class type) 定义了` 
`>> / <<` 操作符, 则 `可以定义 istream_iterator / ostream_iterator`

(2)构造/定义 流迭代器 时, 必须 指定 其所 `读写 的 obj type`

```
std::istream_iterator<int> eof_stream; // end iterator value

// reads ints from std::cin
std::istream_iterator<int> in_iter(std::cin);

//writes Sales_items from ofstream named out_file_stream
// each elem is followed by a space
std::ofstream out_file_stream;
std::ostream_iterator<Sales_items> out_iter(out_file_stream, " ");
```

`3. istream_iterator obj 上的操作`
```
std::istream_iterator<int> in_iter(std::cin), eof_iter; 

while( in_iter != eof_iter )
	vec.push_back(*in_iter++);
```

```
// <=> 重写为
std::istream_iterator<int> in_iter(std::cin), eof_iter; 

// 流 迭代器 范围 初始化 容器
=> 该 范围的 `elem 通过 读 关联的流 获得`
std::vector<int> vec(in_iter, eof_iter);
```

`4. ostream_iterator obj 上的操作`

ostream_iterator obj 可用于 `将1个 value sequence 写入 流中`, 操作 过程 与 `用 迭代器 将1组值 逐个 赋给 容器中的元素` 相同

```
// used to write one string per line to std::cout
std::ostream_iterator<std::string> 
	out_iter(std::cout, "\n");

// used to read strings from std::cin until end iterator
std::istream_iterator<std::string> 
	in_iter(std::cin), eof_iter;

// and write what was read to std::cout, 
// each string with a "\n"
// 读 std::cin, 
// 每个读入的 value 写到 std::cout 中 不同行中
while (in_iter != eof_iter)
	*out_iter++ = *in_iter++;
```

![image.png](https://upload-images.jianshu.io/upload_images/20172887-9c505efd00739af8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`5. 类 类型 上 用 istream_iterator`

```
std::istream_iterator<Sales_item> 
	item_iter(std::cin), item_iter;
	
Sales_item sum;
sum = *item_iter++;

while (item_iter != item_iter)
{
	if (item_iter->same_isbn(sum))
		sum = sum + *item_iter;
	++item_iter;
}
```

`6. 流迭代器 用于 算法: 算法 基于 迭代器 实现`
```
#include <iostream>
#include <string>
#include <iterator>
#include <vector>
#include <algorithm>

int main()
{
	std::istream_iterator<int> 
		in_iter(std::cin), eof_iter;

	//(1)读的值 保存在 vec 中
	std::vector<int> vec(in_iter, eof_iter);

	std::sort(vec.begin(), vec.end() );
	std::ostream_iterator<int>
		out_iter(std::cout, " ");

	//(2)将 vec 中 不重复的值 copy 到 std::cout
	// `每个赋值的值后 输出1个空格`
	// note: `重复的值 只 copy 1次`
	// write only unique emems in vec to std::cout
	std::unique_copy(vec.begin(), vec.end(), out_iter);
}
/*
in:  5 4 1 2 3 2 1

out: 1 2 3 4 5
*/
```

###3 反向迭代器 : ++ 向前移1个位置

```
1. 正向 vs. 反向 迭代器
vec.begin() == --vec.rend()
--vec.end() == vec.rbegin()
```

![image.png](https://upload-images.jianshu.io/upload_images/20172887-b197714eb8328be4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`2. sort 默认升序排列, 若想 降序排列, 只需向 sort 传 1对 迭代器 vec2.rbegin(), vec2.rend(): 排序后 从 vec2.rbegin() -> vec2.rend() 写`

```
// 降序排列
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	int a[5] = { 5, 4, 1, 3, 2};
	std::vector<int> vec1(a, a+5);
	std::vector<int> vec2(a, a + 5);

	// 排序后 从 vec1.begin() -> vec1.end() 写
	// => vec1.begin() -> vec1.end(): 1 2 3 4 5
	std::sort(vec1.begin(), vec1.end() );

	// 排序后 从 vec2.rbegin() -> vec2.rend() 写
	// => vec2.rbegin() -> vec2.rend(): 1 2 3 4 5 
	// => vec2.begin() -> vec2.end(): 5 4 3 2 1
	std::sort(vec2.rbegin(), vec2.rend());

}
```

```
// 普通用法
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> vec;
	for (std::vector<int>::size_type i = 0; i != 3; ++i)
		vec.push_back(i); // vec: 0 1 2

	std::vector<int>::reverse_iterator r_iter;

	// 2 1 0
	for (r_iter = vec.rbegin();
		 r_iter != vec.rend();
		 ++r_iter)
		std::cout << *r_iter << std::endl;
}
```
`3. 迭代器 支持 ++ -- 时 才可以 定义 反向迭代器 / 标准容器迭代器 支持 ++ -- / 流迭代 不支持 --`

`4. 转换 反向迭代器 为 普通正向迭代器: 1)调 base() 成员函数; 2)不对称 => 要 正向向后移1个位置`

`[line.rbegin(), rcomma ) 与 [rcomma.base(), line.end() ) 迭代器方向相反, 但标记的元素相同`

note：反向迭代器 表示的 `范围 不对称` => `用 普通正向迭代器 初始化或赋值 反向迭代器 时, 2种 迭代器 指向的元素 不同`

```
//line = "first, middle, last"

std::string::iterator comma = 
	std::find(line.begin(), line.end(), ',' );
	
//1) first
std::cout << std::string(line.begin(), comma) << std::endl;


std::string::reverse_iterator rcomma = 
	std::find(line.rbegin(), line.rend(), ',' );

//2) tsal	
std::cout << std::string(line.rbegin(), rcomma) << std::endl;

//3) 转换 反向迭代器 为 普通正向迭代器
// last
std::cout << std::string(rcomma.base(), line.end(), ) << std::endl;
```

###4 const 迭代器

 iterator 定义为
(1)`non-const, 是希望 不修改 容器中元素`

(2)`const`, 有2种场景

1)用于 修改 容器中元素
2)为 满足 `算法要求: 算法要求 指定 范围的 2个迭代器 类型完全相同`
```
// 如 11.1 节 find_first_of 中的 iter
 std::find_first_of(
                iter, vec1.end(),
                vec2.begin(), vec2.end() ) 
```

###5 5种 iterator

`算法 要求的 迭代器操作 分 5类`, 分别 `对应 5种 迭代器`

`所有 泛型/算术 算法` 的 `iterator parameter` 必须保证 `最低功能 / 最低级的迭代器类型`

```
1. input iterator

read-only

(1)支持
1) == !=
2)++
3)2种 解引用: * ->

(2)标准库 例子: std::istream_iterator

(3)最低要求 的 algortthm
```
`find / accumulate`

```
2. output iterator

write-only

(1)支持的 operator 比 input iterator 少2种:
1)== !=
2) -> 形式的 解引用
```
`用 output iterator 时, 应该用 1次 *, 而且 只能用1次`

```
(2)标准库 例子: std::ostream_iterator

(3)最低要求 的 algortthm
```
`output iterator 一般作 算法 第 3 arg, 标记 写入 起始位置. 如 copy`

```
3. forward iterator

read-write
```

(1)还支持 对 `同一元素 多次读写`

(2)`可 copy 前向迭代器 来记录 序列中 1个位置, 以便 将来返回 此处`
```
(3)最低要求 的 algortthm
```
`replace`

```
4. bidirectional iterator

read-write

(1)还 支持 --

(2)标准库例子:
`标准库 所有容器 至少是 双向迭代器`

(3)最低要求 的 algortthm
```
`reverse`

(4)`关联容器 的 key 是 const 对象`

=> `关联容器 不能用 任何 写序列元素 的 算法`. 只能 用其 `绑定的 迭代器` 来 提供 用于 `读操作 的 arg`

=> 算法中 `最好将 关联容器的迭代器 视为 支持 -- 的 input iterator, 而不是 完整的 双向迭代器`

```
5. random-access iterator

read-write
(1)还支持
1) < <= > >=
2)与 整数 n 的 + += - -=
3)2个迭代器 - : 得 2个迭代器间的 距离/元素个数
4)下标 iter[n] <=> *(iter +n)

(2)标准库例子:
1)标准库 vector deque string 迭代器
2)`内置数组 的  指针`

(3)最低要求 的 algortthm
```
`sort`

##11.4 泛型算法 structure

```
所有 容器 有共同的 设计模式
算法 也有共同的 设计 structure
```

```
1. 算法分类

(1)按 所用 迭代器类型 分 : 最基本

(2)按 对元素的 操作 分
1)read-only
2)赋新值
3)移动

2. 2种 算法模式
(1)算法 的 `para 规范` 模式

(2)算法 的 `命名 和 重载 规范` 模式
```
###1. 算法 的 `para 规范` 模式

```
只要知道 para 含义, 就可以 专注于 算法实现
```
```
alg (beg, end, other_paras / non_iter_paras);
alg (beg, end, dst_iter, other_paras );

alg (beg, end, beg2, other_paras);
alg (beg, end, beg2, end2, other_paras);
```
```
beg + end 指定 `input scope`

`几乎所有算法都 有 input scope`

beg2 / end2 / dst_iter 使用时, 充当类似角色

non_iter_paras: 算法 特有
```

`1. 带 单个 目标 iterator( dst_iter )`

`(1)指定 目标对象 以 存储输出数据`

`(2)带 dst_iter/ 只带beg2,不带end2的算法`调用时,`由 coder 确保 输出容器 有 足够空间 存储 输出数据`
=> 通常, `dst_iter 与 插入迭代器 或 std::ostream_iterator 绑定, 以 确保` 有足够空间存储输出
 
**2. 带 指定 `第2 input scope` 的 迭代器 beg2 / beg2 + end2**

(1)这类算法 通常 `联合 2 个 input scope 的 element 来 完成计算`

###2. 算法 的 `命名 和 重载 规范` 模式

```
标准库 算法的 `命名和重载规范` 一致
```

`1. 测试 input scope 元素`

第2版本 允许 coder 提供 `predicate function 取代 默认操作符 ==` 去 `查找`(测试) `等于` / `使 predicate function 返回 非 0 值 (true) ` 的 元素

=>

(1)第 1 / 2 版本 `para` 为 `指定 value` / `predicate function`( 算法名 带 后缀 _if)

(2)para number 相同 => 算法名 用 不带 / 带 _if

```
// find first instance of val / 
//	for which pred is true
find (beg, end, val);
find_if (beg, end, pred);
```

`2. 重排 input scope 元素`

第2版本 允许 coder  提供 `compare function ( 额外 para ) 取代 默认操作符 <` 去 `重排`

para number 不同 => 算法名 相同, 按 `重载` 区分

```
// use < operator / function named cmp
//	to sort the elements
sort (beg, end);
sort (beg, end, cmp);
```
3. 将 重排的元素 `写回 input scope` / `写到 指定的 输出目标`

`non_copy 版 / _copy 版: copy 版 算法名 带 _copy`

```
// 将 输入序列 反向 重排
reverse(beg, end);
reverse_copy(beg, end, dst_iter);
```

##11.5 容器特有算法: 针对 list

```
1. list 

(1)迭代器 是 双向的, 不支持 随机访问 
=> 不能 用 泛型 sort 算法

(2)merge remove unique reverse 泛型算法
可用于 list 但付出 性能代价

标准库 结合 list 内部结构, 为 list 提供 高效的
merge remove unique reverse (与 泛型算法 共有) + splice sort(list 特有)
```

`2. list 特有操作`

```
// (1)< 
lst.sort()

// (2)反向重排
lst.reverse()

// (3)
lst.merge(lst2)
lst.merge(lst2, cmp)
1)2个 lst 对象 都必须已排序
2)lst2 中 元素 合并到 lst 中
3)lst2 中 元素 删除
4)版本1/2 用 < / cmp 指定 比较操作
5)return void

// (4)
lst.splice(iter, lst2)
lst.splice(iter, lst2, iter2)
lst.splice(iter, beg, end)
1)lst2 中 元素 `移到` 
lst 中 迭代器 iter 指向的元素前
2)lst2 中 移出的元素 `删除`
3)版本1 移动 lst2 所有元素,
移动后, lst 为空
=> lst 与 lst2 不能是 同一list 对象
4)版本2 只移动 `lst2 中 迭代器 iter2` 指向的元素
=> lst 与 lst2 可以是 同一 list 对象
即 可在 1个 list 对象 中 用 splice 移动1个元素
5)版本3 [beg, end) 可标记 
`任意 list 对象(包括 lst )` 内 的范围.
指定 lst 内 范围时, iter 要在  [beg, end) 之外,
否则, undefined behavior

//(5)
lst.remove(val)
lst.remove_if(unary_pred)
//1)调 lst.erase 删除 所有 等于指定值 /
使指定 predicate 函数 返回非0值(为 true) 的元素
//2)return void

//(6)
lst.unique()
lst.unique(binary_pred)
//1)调 lst.erase 删除 
`同一个值 第1个之后的 连续重复元素`
//2)版本 1 / 2 用 `== / predicate 函数`
判断元素是否相等
```

`3. list 特有算法 与 泛型算法 2个 key differences`

(1)list 特有操作(remove unique) 能 在容器中 `添加/删除` 元素

(2)list 的 merge splice 会 `破坏 arg: 
源 arg 容器 obj` 的 元素 `合并/移动` 到 `目的 list obj` 后, `源 arg 容器 obj 的 合并/移动 元素 被删除`
