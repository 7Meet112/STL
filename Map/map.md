- [一、概述](#一概述)
  - [map的底层结构](#map的底层结构)
- [二、map](#二map)
  - [2.1 源码](#21-源码)
    - [pair的定义](#pair的定义)
    - [map源码](#map源码)
  - [2.2 方法](#22-方法)
    - [常见使用](#常见使用)
    - [定义声明](#定义声明)
    - [迭代器](#迭代器)
    - [插入](#插入)
    - [erase](#erase)
    - [排序](#排序)
  - [2.3 实例](#23-实例)
- [三、multimap](#三multimap)
  - [3.1 源码](#31-源码)
  - [3.2 实例](#32-实例)
- [四、unordered\_map](#四unordered_map)
  - [4.1 方法](#41-方法)
  - [4.2 实例](#42-实例)
  - [4.3 区别](#43-区别)

#### 一、概述
map和multimap都需要#include<map>,唯一的不同是map的键值key不可重复，multimap可以重复，
也正是由于这种区别，map支持[]运算符，而multimap不支持，在用法上区别不大。

C++中map提供的是一种键值对容器，每一对中的第一个值称为关键字（key），每个关键字只能在map中出现一次，第二个称之为关键字的对应值。

##### map的底层结构
由于RB_tree是一种平衡二叉搜索树，自动排序的效果不错，所以标准的STL map是以RB_tree为底层机制。又由于map所开放的各种操作接口，RB_tree也都提供了，所以几乎所有的map操作行为，都只是转调用RB_tree的操作行为而已。

![Image text](https://github.com/7Meet112/Others/blob/main/8A9FC4C2B419404D9A69D6D7B6F0028D.jpg)
#### 二、map
##### 2.1 源码
###### pair的定义
```
//代码摘录与stl_pair.h
template <class _T1, class _T2>
struct pair {
  typedef _T1 first_type;
  typedef _T2 second_type;
 
  _T1 first;
  _T2 second;
  pair() : first(_T1()), second(_T2()) {}
  pair(const _T1& __a, const _T2& __b) : first(__a), second(__b) {}
 
#ifdef __STL_MEMBER_TEMPLATES
  template <class _U1, class _U2>
  pair(const pair<_U1, _U2>& __p) : first(__p.first), second(__p.second) {}
#endif
};
```
###### map源码
```
//代码摘录与stl_map.h
template <class _Key, class _Tp, class _Compare, class _Alloc>
class map {
public:
 
// requirements:
 
  __STL_CLASS_REQUIRES(_Tp, _Assignable);
  __STL_CLASS_BINARY_FUNCTION_CHECK(_Compare, bool, _Key, _Key);
 
// typedefs:
 
  typedef _Key                  key_type;  //键值类型
  typedef _Tp                   data_type; //数据（实值）类型
  typedef _Tp                   mapped_type;
  typedef pair<const _Key, _Tp> value_type; //元素类型（键值/实值）
  typedef _Compare              key_compare; //键值比较函数
    
  //以下定义一个functor，作用就是调用“元素比较函数”
  class value_compare
    : public binary_function<value_type, value_type, bool> {
  friend class map<_Key,_Tp,_Compare,_Alloc>;
  protected :
    _Compare comp;
    value_compare(_Compare __c) : comp(__c) {}
  public:
    bool operator()(const value_type& __x, const value_type& __y) const {
      return comp(__x.first, __y.first);
    }
  };
 
private:
  //以下定义表述类型。以map元素类型（一个pair）的第一类型，作为RB-tree节点的键值类型
  typedef _Rb_tree<key_type, value_type, 
                   _Select1st<value_type>, key_compare, _Alloc> _Rep_type;
  _Rep_type _M_t;  // 以红黑树实现map
public:
  typedef typename _Rep_type::pointer pointer;
  typedef typename _Rep_type::const_pointer const_pointer;
  typedef typename _Rep_type::reference reference;
  typedef typename _Rep_type::const_reference const_reference;
  typedef typename _Rep_type::iterator iterator;
  //注意上面一行，map不像set，map使用的是RB-tree的iterator
  typedef typename _Rep_type::const_iterator const_iterator;
  typedef typename _Rep_type::reverse_iterator reverse_iterator;
  typedef typename _Rep_type::const_reverse_iterator const_reverse_iterator;
  typedef typename _Rep_type::size_type size_type;
  typedef typename _Rep_type::difference_type difference_type;
  typedef typename _Rep_type::allocator_type allocator_type;
 
  //注意，map一定使用底层RB-tree的insert_unique()而非insert_equal()
  //multimap才使用insert_equal()
  // allocation/deallocation
 
  map() : _M_t(_Compare(), allocator_type()) {}
  explicit map(const _Compare& __comp,
               const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) {}
 
#ifdef __STL_MEMBER_TEMPLATES
  template <class _InputIterator>
  map(_InputIterator __first, _InputIterator __last)
    : _M_t(_Compare(), allocator_type())
    { _M_t.insert_unique(__first, __last); }
 
  template <class _InputIterator>
  map(_InputIterator __first, _InputIterator __last, const _Compare& __comp,
      const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) { _M_t.insert_unique(__first, __last); }
#else
  map(const value_type* __first, const value_type* __last)
    : _M_t(_Compare(), allocator_type())
    { _M_t.insert_unique(__first, __last); }
 
  map(const value_type* __first,
      const value_type* __last, const _Compare& __comp,
      const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) { _M_t.insert_unique(__first, __last); }
 
  map(const_iterator __first, const_iterator __last)
    : _M_t(_Compare(), allocator_type()) 
    { _M_t.insert_unique(__first, __last); }
 
  map(const_iterator __first, const_iterator __last, const _Compare& __comp,
      const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) { _M_t.insert_unique(__first, __last); }
 
#endif /* __STL_MEMBER_TEMPLATES */
 
  map(const map<_Key,_Tp,_Compare,_Alloc>& __x) : _M_t(__x._M_t) {}
  map<_Key,_Tp,_Compare,_Alloc>&
  operator=(const map<_Key, _Tp, _Compare, _Alloc>& __x)
  {
    _M_t = __x._M_t;
    return *this; 
  }
 
  //以下所有mao操作，RB-tree都已提供，mao只要调用即可
  // accessors:
 
  key_compare key_comp() const { return _M_t.key_comp(); }
  value_compare value_comp() const { return value_compare(_M_t.key_comp()); }
  allocator_type get_allocator() const { return _M_t.get_allocator(); }
 
  iterator begin() { return _M_t.begin(); }
  const_iterator begin() const { return _M_t.begin(); }
  iterator end() { return _M_t.end(); }
  const_iterator end() const { return _M_t.end(); }
  reverse_iterator rbegin() { return _M_t.rbegin(); }
  const_reverse_iterator rbegin() const { return _M_t.rbegin(); }
  reverse_iterator rend() { return _M_t.rend(); }
  const_reverse_iterator rend() const { return _M_t.rend(); }
  bool empty() const { return _M_t.empty(); }
  size_type size() const { return _M_t.size(); }
  size_type max_size() const { return _M_t.max_size(); }
  _Tp& operator[](const key_type& __k) {
    iterator __i = lower_bound(__k);
    // __i->first is greater than or equivalent to __k.
    if (__i == end() || key_comp()(__k, (*__i).first))
      __i = insert(__i, value_type(__k, _Tp()));
    return (*__i).second;
  }
  void swap(map<_Key,_Tp,_Compare,_Alloc>& __x) { _M_t.swap(__x._M_t); }
 
  // insert/erase
 
  pair<iterator,bool> insert(const value_type& __x) 
    { return _M_t.insert_unique(__x); }
  iterator insert(iterator position, const value_type& __x)
    { return _M_t.insert_unique(position, __x); }
#ifdef __STL_MEMBER_TEMPLATES
  template <class _InputIterator>
  void insert(_InputIterator __first, _InputIterator __last) {
    _M_t.insert_unique(__first, __last);
  }
#else
  void insert(const value_type* __first, const value_type* __last) {
    _M_t.insert_unique(__first, __last);
  }
  void insert(const_iterator __first, const_iterator __last) {
    _M_t.insert_unique(__first, __last);
  }
#endif /* __STL_MEMBER_TEMPLATES */
 
  void erase(iterator __position) { _M_t.erase(__position); }
  size_type erase(const key_type& __x) { return _M_t.erase(__x); }
  void erase(iterator __first, iterator __last)
    { _M_t.erase(__first, __last); }
  void clear() { _M_t.clear(); }
 
  // map operations:
 
  iterator find(const key_type& __x) { return _M_t.find(__x); }
  const_iterator find(const key_type& __x) const { return _M_t.find(__x); }
  size_type count(const key_type& __x) const {
    return _M_t.find(__x) == _M_t.end() ? 0 : 1; 
  }
  iterator lower_bound(const key_type& __x) {return _M_t.lower_bound(__x); }
  const_iterator lower_bound(const key_type& __x) const {
    return _M_t.lower_bound(__x); 
  }
  iterator upper_bound(const key_type& __x) {return _M_t.upper_bound(__x); }
  const_iterator upper_bound(const key_type& __x) const {
    return _M_t.upper_bound(__x); 
  }
  
  pair<iterator,iterator> equal_range(const key_type& __x) {
    return _M_t.equal_range(__x);
  }
  pair<const_iterator,const_iterator> equal_range(const key_type& __x) const {
    return _M_t.equal_range(__x);
  }
 
#ifdef __STL_TEMPLATE_FRIENDS 
  template <class _K1, class _T1, class _C1, class _A1>
  friend bool operator== (const map<_K1, _T1, _C1, _A1>&,
                          const map<_K1, _T1, _C1, _A1>&);
  template <class _K1, class _T1, class _C1, class _A1>
  friend bool operator< (const map<_K1, _T1, _C1, _A1>&,
                         const map<_K1, _T1, _C1, _A1>&);
#else /* __STL_TEMPLATE_FRIENDS */
  friend bool __STD_QUALIFIER
  operator== __STL_NULL_TMPL_ARGS (const map&, const map&);
  friend bool __STD_QUALIFIER
  operator< __STL_NULL_TMPL_ARGS (const map&, const map&);
#endif /* __STL_TEMPLATE_FRIENDS */
};
```
##### 2.2 方法
###### 常见使用
```
begin()          返回指向map头部的迭代器
clear(）         删除所有元素
count()          返回指定元素出现的次数
empty()          如果map为空则返回true
end()            返回指向map末尾的迭代器
equal_range()    返回特殊条目的迭代器对
erase()          删除一个元素
find()           查找一个元素
get_allocator()  返回map的配置器
insert()         插入元素
key_comp()       返回比较元素key的函数
lower_bound()    返回键值>=给定元素的第一个位置
max_size()       返回可以容纳的最大元素个数
rbegin()         返回一个指向map尾部的逆向迭代器
rend()           返回一个指向map头部的逆向迭代器
size()           返回map中元素的个数
swap()           交换两个map
upper_bound()    返回键值>给定元素的第一个位置
value_comp()     返回比较元素value的函数
```
###### 定义声明
```
map<int, string> ID_Name;
// 使用{}赋值是从c++11开始的，因此编译器版本过低时会报错，如visual studio 2012
map<int, string> ID_Name = 
{
    {2015, "Jim"},
    {2016, "Tom"},
    {2017, "Bob"}
};

 it->second
(*it).second
```
###### 迭代器
```
// 迭代器
// 共有八个获取迭代器的函数：* begin, end, rbegin,rend* 
// 以及对应的 * cbegin, cend, crbegin,crend*。
// 二者的区别在于，后者一定返回 const_iterator，
// 而前者则根据map的类型返回iterator 或者 const_iterator。
// const情况下，不允许对值进行修改。如下面代码所示：
// 返回的迭代器可以进行加减操作，此外，如果map为空，则 begin = end。
map<int,int>::iterator it;
map<int,int> mmap;
const map<int,int> const_mmap;

it = mmap.begin();      //iterator
mmap.cbegin();          //const_iterator

const_mmap.begin();     //const_iterator
const_mmap.cbegin();    //const_iterator
```
###### 插入
insert函数返回pair类型，第一个参数是返回的元素迭代器（iter）第二个为bool，插入成功的话pair参数一才保存返回的节点元素。
```
    std::pair<std::map<int, string>::iterator, bool> ret;
    ret = mp.insert(std::pair<int, string>(1004, "wen"));
    if (ret.second)
    {
        std::cout << ret.first->first << std::endl;
        std::cout << ret.first->second << std::endl;
    }
```
```
// 插入操作 
// map.insert(...); // 往容器插入元素，返回pair<iterator,bool>
map<int, string> mapStu;

// 第一种 通过pair的方式插入对象
mapStu.insert(pair<int, string>(3, "小张"));
// 第二种 通过pair的方式插入对象
mapStu.insert(make_pair(-1, "校长"));
mapStu.insert(make_pair<int, string>(-1, "校长"));
// 第三种 通过value_type的方式插入对象
mapStu.insert(map<int, string>::value_type(1, "小李"));
// 第四种 通过数组的方式插入值
mapStu[3] = "小刘";
mapStu[5] = "小王";
cout << mapStu.size() << endl;

std::map<char, int> mymap;
// 插入单个值
mymap.insert(std::pair<char, int>('a', 100));
mymap.insert(std::pair<char, int>('z', 200));

// 返回插入位置以及是否插入成功
std::pair<std::map<char, int>::iterator, bool> ret;
ret = mymap.insert(std::pair<char, int>('z', 500));
if (ret.second == false) 
{
    std::cout << "element 'z' already existed";
    std::cout << " with a value of " << ret.first->second << '\n';
}
 
// 指定位置插入
std::map<char, int>::iterator it = mymap.begin();
mymap.insert(it, std::pair<char, int>('b', 300));  //效率更高
mymap.insert(it, std::pair<char, int>('c', 400));  //效率非最高

// 范围多值插入
std::map<char, int> anothermap;
anothermap.insert(mymap.begin(), mymap.find('c'));

// 列表形式插入
anothermap.insert({ { 'd', 100 }, {'e', 200} });
```
###### erase
```
#include <map>
#include <iostream>

using namespace std;

int main()
{   
    map<int, string> mapStu;
    // 第一种 通过pair的方式插入对象
    mapStu.insert(pair<int, string>(3, "小张"));
    // 第二种 通过pair的方式插入对象
    mapStu.insert(make_pair(-1, "校长"));
    // 第三种 通过value_type的方式插入对象
    mapStu.insert(map<int, string>::value_type(1, "小李"));
    // 第四种 通过数组的方式插入值
    mapStu[3] = "小刘";
    mapStu[5] = "小王";
    // cout << mapStu.size() << endl;

    for (auto cc : mapStu)
    {
        std::cout << cc.first << " " << cc.second << std::endl;
    }

    map<int, string>::iterator iter;
    for (iter = mapStu.begin(); iter != mapStu.end(); iter++)
    {   
        cout << iter->first << ' '<< iter->second << endl;
    }

    for (iter = mapStu.begin(); iter != mapStu.end();)
    {   
        if (iter->first == 3)
        {
            iter = mapStu.erase(iter);
        }
        else
        {
            iter++;
        }
    }

    // mapStu.erase(3);
    return 0;
}
```
###### 排序
map中的元素是自动按Key升序排序，所以不能对map用sort函数,STL中默认是采用小于号来排序的，
关键字是一个结构体或者自定义类，涉及到排序就会出现问题，因为它没有小于号操作，insert等函数在编译的时候过不去,下面给出两个方法解决这个问题。
- 小于号 < 重载
```
#include <iostream>  
#include <string>  
#include <map>  
using namespace std;

typedef struct tagStudentinfo
{
    int      niD;
    string   strName;
    bool operator < (tagStudentinfo const& _A) const
    {     //这个函数指定排序策略，按niD排序，如果niD相等的话，按strName排序  
        if (niD < _A.niD) return true;
        if (niD == _A.niD)
            return strName.compare(_A.strName) < 0;
        return false;
    }
}Studentinfo, *PStudentinfo; //学生信息  

int main()
{
    int nSize;   //用学生信息映射分数  
    map<Studentinfo, int>mapStudent;
    map<Studentinfo, int>::iterator iter;

    Studentinfo studentinfo;
    studentinfo.niD = 1;
    studentinfo.strName = "student_one";
    mapStudent.insert(pair<Studentinfo, int>(studentinfo, 90));

    studentinfo.niD = 2;
    studentinfo.strName = "student_two";
    mapStudent.insert(pair<Studentinfo, int>(studentinfo, 80));

    for (iter = mapStudent.begin(); iter != mapStudent.end(); iter++)
        cout << iter->first.niD << ' ' << iter->first.strName << ' ' << iter->second << endl;

    return 0;
}
```
- 仿函数的应用
仿函数的应用，这个时候结构体中没有直接的小于号重载，程序说明  
```
#include <iostream>  
#include <map>  
#include <string>  
using namespace std;

typedef struct tagStudentinfo
{
    int      niD;
    string   strName;
}Studentinfo, *PStudentinfo; //学生信息  

class sort
{
public:
    bool operator() (Studentinfo const &_A, Studentinfo const &_B) const
    {
        if (_A.niD < _B.niD)
            return true;
        if (_A.niD == _B.niD)
            return _A.strName.compare(_B.strName) < 0;
        return false;
    }
};

int main()
{   
    //用学生信息映射分数  
    map<Studentinfo, int, sort>mapStudent;
    map<Studentinfo, int>::iterator iter;
    Studentinfo studentinfo;
    studentinfo.niD = 1;
    studentinfo.strName = "student_one";
    mapStudent.insert(pair<Studentinfo, int>(studentinfo, 90));
    studentinfo.niD = 2;
    studentinfo.strName = "student_two";
    mapStudent.insert(pair<Studentinfo, int>(studentinfo, 80));
    for (iter = mapStudent.begin(); iter != mapStudent.end(); iter++)
        cout << iter->first.niD << ' ' << iter->first.strName << ' ' << iter->second << endl;
    system("pause");
}
```
##### 2.3 实例
```
#include <iostream>
#include <string>
#include <map>
#include <utility>
using namespace std;
 
int main()
{
	map<std::string, int> simap;
	simap[std::string("jjhou")] = 1;
	simap[std::string("jerry")] = 2;
	simap[std::string("jason")] = 3;
	simap[std::string("jimmy")] = 4;
 
	std::pair<std::string, int> value(std::string("david"), 5);
	simap.insert(value);
 
	map<std::string, int>::iterator simap_iter = simap.begin();
	for (; simap_iter != simap.end(); simap_iter++)
		std::cout << simap_iter->first << ": " << simap_iter->second << std::endl;
	std::cout << std::endl;
 
	int number = simap[std::string("jjhou")];
	std::cout << number << std::endl << std::endl;
 
 
	map<std::string, int>::iterator iter;
	iter = simap.find(std::string("mchen"));
	if (iter != simap.end())
		std::cout << "mchen found" << std::endl;
	else
		std::cout << "mchen not found" << std::endl;
	iter = simap.find(std::string("jerry"));
	if (iter != simap.end())
		std::cout << "jerry found" << std::endl;
	else
		std::cout << "jerry not found" << std::endl;
	std::cout << std::endl;
 
	iter->second = 9;
	int number2 = simap[std::string("jerry")];
	std::cout << number2 << std::endl;
	return 0;
}
```
#### 三、multimap
##### 3.1 源码
```
//下面代码摘录于stl_multimap.h
template <class _Key, class _Tp, class _Compare, class _Alloc>
class multimap {
  // requirements:
 
  __STL_CLASS_REQUIRES(_Tp, _Assignable);
  __STL_CLASS_BINARY_FUNCTION_CHECK(_Compare, bool, _Key, _Key);
 
public:
 
// typedefs:
 
  typedef _Key                  key_type;
  typedef _Tp                   data_type;
  typedef _Tp                   mapped_type;
  typedef pair<const _Key, _Tp> value_type;
  typedef _Compare              key_compare;
 
  class value_compare : public binary_function<value_type, value_type, bool> {
  friend class multimap<_Key,_Tp,_Compare,_Alloc>;
  protected:
    _Compare comp;
    value_compare(_Compare __c) : comp(__c) {}
  public:
    bool operator()(const value_type& __x, const value_type& __y) const {
      return comp(__x.first, __y.first);
    }
  };
 
private:
  typedef _Rb_tree<key_type, value_type, 
                  _Select1st<value_type>, key_compare, _Alloc> _Rep_type;
  _Rep_type _M_t;  // red-black tree representing multimap
public:
  typedef typename _Rep_type::pointer pointer;
  typedef typename _Rep_type::const_pointer const_pointer;
  typedef typename _Rep_type::reference reference;
  typedef typename _Rep_type::const_reference const_reference;
  typedef typename _Rep_type::iterator iterator;
  typedef typename _Rep_type::const_iterator const_iterator; 
  typedef typename _Rep_type::reverse_iterator reverse_iterator;
  typedef typename _Rep_type::const_reverse_iterator const_reverse_iterator;
  typedef typename _Rep_type::size_type size_type;
  typedef typename _Rep_type::difference_type difference_type;
  typedef typename _Rep_type::allocator_type allocator_type;
 
// allocation/deallocation
 
  multimap() : _M_t(_Compare(), allocator_type()) { }
  explicit multimap(const _Compare& __comp,
                    const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) { }
 
#ifdef __STL_MEMBER_TEMPLATES  
  template <class _InputIterator>
  multimap(_InputIterator __first, _InputIterator __last)
    : _M_t(_Compare(), allocator_type())
    { _M_t.insert_equal(__first, __last); }
 
  template <class _InputIterator>
  multimap(_InputIterator __first, _InputIterator __last,
           const _Compare& __comp,
           const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) { _M_t.insert_equal(__first, __last); }
#else
  multimap(const value_type* __first, const value_type* __last)
    : _M_t(_Compare(), allocator_type())
    { _M_t.insert_equal(__first, __last); }
  multimap(const value_type* __first, const value_type* __last,
           const _Compare& __comp,
           const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) { _M_t.insert_equal(__first, __last); }
 
  multimap(const_iterator __first, const_iterator __last)
    : _M_t(_Compare(), allocator_type())
    { _M_t.insert_equal(__first, __last); }
  multimap(const_iterator __first, const_iterator __last,
           const _Compare& __comp,
           const allocator_type& __a = allocator_type())
    : _M_t(__comp, __a) { _M_t.insert_equal(__first, __last); }
#endif /* __STL_MEMBER_TEMPLATES */
 
  multimap(const multimap<_Key,_Tp,_Compare,_Alloc>& __x) : _M_t(__x._M_t) { }
  multimap<_Key,_Tp,_Compare,_Alloc>&
  operator=(const multimap<_Key,_Tp,_Compare,_Alloc>& __x) {
    _M_t = __x._M_t;
    return *this; 
  }
 
  // accessors:
 
  key_compare key_comp() const { return _M_t.key_comp(); }
  value_compare value_comp() const { return value_compare(_M_t.key_comp()); }
  allocator_type get_allocator() const { return _M_t.get_allocator(); }
 
  iterator begin() { return _M_t.begin(); }
  const_iterator begin() const { return _M_t.begin(); }
  iterator end() { return _M_t.end(); }
  const_iterator end() const { return _M_t.end(); }
  reverse_iterator rbegin() { return _M_t.rbegin(); }
  const_reverse_iterator rbegin() const { return _M_t.rbegin(); }
  reverse_iterator rend() { return _M_t.rend(); }
  const_reverse_iterator rend() const { return _M_t.rend(); }
  bool empty() const { return _M_t.empty(); }
  size_type size() const { return _M_t.size(); }
  size_type max_size() const { return _M_t.max_size(); }
  void swap(multimap<_Key,_Tp,_Compare,_Alloc>& __x) { _M_t.swap(__x._M_t); }
 
  // insert/erase
 
  iterator insert(const value_type& __x) { return _M_t.insert_equal(__x); }
  iterator insert(iterator __position, const value_type& __x) {
    return _M_t.insert_equal(__position, __x);
  }
#ifdef __STL_MEMBER_TEMPLATES  
  template <class _InputIterator>
  void insert(_InputIterator __first, _InputIterator __last) {
    _M_t.insert_equal(__first, __last);
  }
#else
  void insert(const value_type* __first, const value_type* __last) {
    _M_t.insert_equal(__first, __last);
  }
  void insert(const_iterator __first, const_iterator __last) {
    _M_t.insert_equal(__first, __last);
  }
#endif /* __STL_MEMBER_TEMPLATES */
  void erase(iterator __position) { _M_t.erase(__position); }
  size_type erase(const key_type& __x) { return _M_t.erase(__x); }
  void erase(iterator __first, iterator __last)
    { _M_t.erase(__first, __last); }
  void clear() { _M_t.clear(); }
 
  // multimap operations:
 
  iterator find(const key_type& __x) { return _M_t.find(__x); }
  const_iterator find(const key_type& __x) const { return _M_t.find(__x); }
  size_type count(const key_type& __x) const { return _M_t.count(__x); }
  iterator lower_bound(const key_type& __x) {return _M_t.lower_bound(__x); }
  const_iterator lower_bound(const key_type& __x) const {
    return _M_t.lower_bound(__x); 
  }
  iterator upper_bound(const key_type& __x) {return _M_t.upper_bound(__x); }
  const_iterator upper_bound(const key_type& __x) const {
    return _M_t.upper_bound(__x); 
  }
   pair<iterator,iterator> equal_range(const key_type& __x) {
    return _M_t.equal_range(__x);
  }
  pair<const_iterator,const_iterator> equal_range(const key_type& __x) const {
    return _M_t.equal_range(__x);
  }
 
#ifdef __STL_TEMPLATE_FRIENDS 
  template <class _K1, class _T1, class _C1, class _A1>
  friend bool operator== (const multimap<_K1, _T1, _C1, _A1>&,
                          const multimap<_K1, _T1, _C1, _A1>&);
  template <class _K1, class _T1, class _C1, class _A1>
  friend bool operator< (const multimap<_K1, _T1, _C1, _A1>&,
                         const multimap<_K1, _T1, _C1, _A1>&);
#else /* __STL_TEMPLATE_FRIENDS */
  friend bool __STD_QUALIFIER
  operator== __STL_NULL_TMPL_ARGS (const multimap&, const multimap&);
  friend bool __STD_QUALIFIER
  operator< __STL_NULL_TMPL_ARGS (const multimap&, const multimap&);
#endif /* __STL_TEMPLATE_FRIENDS */
};
```
##### 3.2 实例
```
#include <map>
#include <iostream>
using namespace std;

void Test_multimap()
{
    multimap<int, string> plist;
    plist.insert(make_pair(1, "1"));
    plist.insert(make_pair(1, "11"));
    plist.insert(make_pair(2, "111"));
    plist.insert(make_pair(1, "1111"));

    multimap<int, string>::iterator it = plist.find(1);
    // int depatCount = plist.count(1);
    // int num = 0;
    if (it != plist.end())
    {
        while (it != plist.end() /*&& num < depatCount*/)
        {
            cout
                << " 姓名:" << it->second
                << endl;
            it++;
            // num++;
        }
    }
}
```
#### 四、unordered_map
- 内部实现
>unordered_map内部实现了一个哈希表（也叫散列表，通过把关键码值映射到Hash表中一个位置来访问记录，查找的时间复杂度可达到O(1)，其在海量数据处理中有着广泛应用）。因此，其元素的排列顺序是无序的。
- unordered_map是一个将key和value关联起来的容器，它可以高效的根据单个key值查找对应的value。
- key值应该是唯一的，key和value的数据类型可以不相同。
- unordered_map存储元素时是没有顺序的，只是根据key的哈希值，将元素存在指定位置，所以根据key查找单个value时非常高效，平均可以在常数时间内完成。
- unordered_map查询单个key的时候效率比map高，但是要查询某一范围内的key值时比map效率低。
可以使用[]操作符来访问key值对应的value值。
##### 4.1 方法
- unordered_map的模板定义如下：
```
template < class Key,                                    // unordered_map::key_type
           class T,                                      // unordered_map::mapped_type
           class Hash = hash<Key>,                       // unordered_map::hasher
           class Pred = equal_to<Key>,                   // unordered_map::key_equal
           class Alloc = allocator< pair<const Key,T> >  // unordered_map::allocator_type
           > class unordered_map;
```
- 遍历map
```
unordered_map<key,T>::iterator it;
    (*it).first;   //the key value
    (*it).second   //the mapped value
    for(unordered_map<key,T>::iterator iter=mp.begin();iter!=mp.end();iter++)
          cout<<"key value is"<<iter->first<<" the mapped value is "<< iter->second;

    //也可以这样
    for(auto& v : mp)
        print v.first and v.second
```
##### 4.2 实例
```
#include < unordered_map >

std::unordered_map<std::string, std::int> umap; //定义

umap.insert(Map::value_type("test", 1));//增加

//根据key删除,如果没找到n=0
auto n = umap.erase("test")   //删除

auto it = umap.find(key) //改
if(it != umap.end()) 
    it->second = new_value; 


//map中查找x是否存在
umap.find(x) != map.end()//查
//或者
umap.count(x) != 0
```
##### 4.3 区别
- 运行效率方面：unordered_map最高，而map效率较低但 提供了稳定效率和有序的序列。
- 占用内存方面：map内存占用略低，unordered_map内存占用略高,而且是线性成比例的。
- 内部实现原理
1. map： map内部实现了一个红黑树，该结构具有自动排序的功能，因此map内部的所有元素都是有序的，红黑树的每一个节点都代表着map的一个元素，因此，对于map进行的查找，删除，添加等一系列的操作都相当于是对红黑树进行这样的操作，故红黑树的效率决定了map的效率。
2. unordered_map: unordered_map内部实现了一个哈希表，因此其元素的排列顺序是杂乱的，无序的
- 优点、缺点、使用场景
- map
1. 优点：有序性，这是map结构最大的优点，其元素的有序性在很多应用中都会简化很多的操作。红黑树，内部实现一个红黑书使得map的很多操作在lgn的时间复杂度下就可以实现，因此效率非常的高。
2. 缺点：空间占用率高，因为map内部实现了红黑树，虽然提高了运行效率，但是因为每一个节点都需要额外保存父节点，孩子节点以及红/黑性质，使得每一个节点都占用大量的空间
3. 适用处：对于那些有顺序要求的问题，用map会更高效一些。

- unordered_map
1. 优点：内部实现了哈希表，因此其查找速度是常量级别的。
2. 缺点：哈希表的建立比较耗费时间
3. 适用处：对于查找问题，unordered_map会更加高效一些，因此遇到查找问题，常会考虑一下用unordered_map
![Image text](https://github.com/7Meet112/Others/blob/main/unordered_map.jpg)
