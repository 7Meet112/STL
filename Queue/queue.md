- [一、概述](#一概述)
- [二、使用](#二使用)
  - [2.1 构造函数](#21-构造函数)
  - [2.2 存取、插入、删除](#22-存取插入删除)
  - [2.3 其他](#23-其他)
- [三、源码](#三源码)
- [四、实例](#四实例)
- [五、经验](#五经验)

##### 一、概述
queue时一种先进先出的数据结构，它有两个出口，queue容器允许从一端新增元素，从另一端移除元素。
![Image text](https://github.com/7Meet112/Others/blob/main/D16B5546-06FF-4826-A177-21818013A7FB.png)

由于queue系以底部容器完成其所有工作，所以归类为container adapter。
##### 二、使用
###### 2.1 构造函数
```
queue<T> queT;              // queue采用模板类实现
queue(const queue &que);    // 拷贝构造函数
```
###### 2.2 存取、插入、删除
```
push(elem);        // 往队尾添加元素
pop();             // 从队头移除第一个元素
back();            // 返回最后一个元素
front();           // 返回第一个元素
```
###### 2.3 其他
```
赋值
queue& operator=(const queue &que); // 重载等号操作符

empty(); 
size();  
```
##### 三、源码
```
// 如果编译器不能根据前面模板参数推导出后面使用的默认参数类型,
// 那么就需要手工指定, 本实作queue内部容器默认使用deque
// 由于queue要求在队尾追加元素, 在队头获取和移除元素
// 所以非常适合使用deque
#ifndef __STL_LIMITED_DEFAULT_TEMPLATES
	template <class T, class Sequence = deque<T> >
#else
	template <class T, class Sequence>
#endif
class queue
{
	// 讲解见<stl_pair.h>中的运算符剖析
	friend bool operator== __STL_NULL_TMPL_ARGS (const queue& x, const queue& y);
	friend bool operator< __STL_NULL_TMPL_ARGS (const queue& x, const queue& y);
 
public:
	// 由于queue仅支持对队头和队尾的操作, 所以不定义STL要求的
	// pointer, iterator, difference_type
	typedef typename Sequence::value_type value_type;
	typedef typename Sequence::size_type size_type;
	typedef typename Sequence::reference reference;
	typedef typename Sequence::const_reference const_reference;
 
protected:
	Sequence c;   // 这个是我们实际维护的容器,底层容器
 
public:
 
	// 这些是STL queue的标准接口, 都调用容器的成员函数进行实现
	// 其接口和stack实现很接近, 参考<stl_stack.h>
    //以下完全利用Sequence c的操作完成queue的操作
	bool empty() const { return c.empty(); }
	size_type size() const { return c.size(); }
	reference front() { return c.front(); }
	const_reference front() const { return c.front(); }
	reference back() { return c.back(); }
	const_reference back() const { return c.back(); }
	void push(const value_type& x) { c.push_back(x); }
	void pop() { c.pop_front(); }
};
 
// 详细讲解见<stl_pair.h>
template <class T, class Sequence>
bool operator==(const queue<T, Sequence>& x, const queue<T, Sequence>& y)
{
	return x.c == y.c;
}
 
template <class T, class Sequence>
bool operator<(const queue<T, Sequence>& x, const queue<T, Sequence>& y)
{
	return x.c < y.c;
}
```
##### 四、实例
list 实现的 queue：list和deque一样，是双向开口的数据结构，list也可以作为底部结构并封装其开口，形成一个queue。
```
#include <queue>
#include <list>
#include <iostream>
#include <algorithm>
using namespace std;
 
int main() {
	queue<int, list<int> > iqueue;
	iqueue.push(1);
	iqueue.push(3);
	iqueue.push(6);
 
	cout << iqueue.size() << endl;
	cout << iqueue.front() << endl;
 
	iqueue.pop(); cout << iqueue.front() << endl;
	cout << iqueue.size() << endl;
}
```
##### 五、经验
- queue没有迭代器
   
queue所有元素的进出都必须符合“先进先出”的条件，只有queue的顶端元素，才有机会被外界取用，queue不提供遍历操作，也不提供迭代器。
