- [一、概述](#一概述)
- [二、定义](#二定义)
- [三、源码](#三源码)
- [四、方法](#四方法)
- [五、实例](#五实例)
- [六、经验](#六经验)
##### 一、概述
```
------------------------------push
栈底                          栈顶 top
------------------------------pop
```
stack是一种先进后出的数据结构，它只有一个出口，允许新增元素、移除元素、取得最顶端元素。但除了最顶端外，没有任何其他方法可以存取stack的其他元素，换言之，stack不允许有遍历行为。
##### 二、定义
以某种既有容器作为底部结构，将其接口改变，使之符合“先进后出”的特性，形成一个stack是很容易做到的。deque是双方开口的数据结构，若以deque为底部结构并封闭其头段开口，便轻易形成一个stack，SGI STL 便以deque作为缺省情况下地stack底部结构。

由于stack系以底部容器完成其所以工作，而具有这种“修改某物接口，形成另一种风貌”之性质者，称为adapter配接器，因此，STL stack往往不被归类为container(容器)，而被归类为container adapter。
##### 三、源码
```
#ifndef __SGI_STL_INTERNAL_STACK_H
#define __SGI_STL_INTERNAL_STACK_H
 
__STL_BEGIN_NAMESPACE
 
#ifndef __STL_LIMITED_DEFAULT_TEMPLATES
template <class T, class Sequence = deque<T> > //默认以 deque 为底层容器
#else
template <class T, class Sequence>
#endif
class stack {
  friend bool operator== __STL_NULL_TMPL_ARGS (const stack&, const stack&);
  friend bool operator< __STL_NULL_TMPL_ARGS (const stack&, const stack&);
public:
  typedef typename Sequence::value_type value_type;
  typedef typename Sequence::size_type size_type;
  typedef typename Sequence::reference reference;
  typedef typename Sequence::const_reference const_reference;
protected:
  Sequence c; //底层容器
public:
  //下面全然利用 Sequence c 的操作完毕 stack 的操作
  bool empty() const { return c.empty(); }
  size_type size() const { return c.size(); }
  reference top() { return c.back(); }
  const_reference top() const { return c.back(); }
  //改动接口使符合 stack "前进后出"的特性
  void push(const value_type& x) { c.push_back(x); }
  void pop() { c.pop_back(); }
};
 
 
template <class T, class Sequence>
bool operator==(const stack<T, Sequence>& x, const stack<T, Sequence>& y) {
  return x.c == y.c;
}
 
 
template <class T, class Sequence>
bool operator<(const stack<T, Sequence>& x, const stack<T, Sequence>& y) {
  return x.c < y.c;
}
 
 
__STL_END_NAMESPACE
 
 
#endif /* __SGI_STL_INTERNAL_STACK_H */
 
 
// Local Variables:
// mode:C++
// End:
```
##### 四、方法
```
stack(const stack& stk);            // 拷贝构造函数
bool empty() const{};               // 判断是否为空
size_type size() const{};           // 返回大小
reference top() const{};            // 返回栈顶元素
void push(const value_type& x){};   // 向栈顶添加元素
void pop(){};                       // 从栈顶移除第一个元素

template <class T, class Sequence>
bool operator==(const stack<T, Sequence>& x, const stack<T, Sequence>& y);

template<class T, class Sequence>
bool operator<(const stack<T, Sequence>& x, const stack<T, Sequence> & y);
```
##### 五、实例
- 以List作为底层容器

除了deque之外，list也是双向开口的数据结构，以list为底部结构并封闭其开端开口，一样能轻易形成一个stack。
```
#include <iostream>
#include <stack>
#include <list>
#include <algorithm>
using namespace std;

int main()
{
    stack<int, list<int>> stack_list;
    for (int i = 1; i <= 5; ++i)
    {
        stack_list.push(i);
    }

    std::cout << stack_list.size() << std::endl;    // 5
    std::cout << stack_list.top() << std::endl;     // 5

    for (int i = 1; i <= 3; ++i)
    {
        stack_list.pop();
        std::cout << stack_list.top();     // 4 3 2
        std::cout << std::endl;
    }

    std::cout << stack_list.size() << std::endl; // 2

    return 0;
}
```
##### 六、经验
- stack没有迭代器
  
stack所以元素的进出都必须符合“先进先出”的条件，只有stack顶端的元素，才有机会被外界取用，stack不提供访问功能，也不提供迭代器。
