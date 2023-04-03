
- [一、概述](#一概述)
- [二、使用](#二使用)
  - [2.1 基本使用方法](#21-基本使用方法)
  - [2.2 自定义排序](#22-自定义排序)
  - [2.3 自定义类型](#23-自定义类型)
  - [2.4 set容器insert返回值](#24-set容器insert返回值)
  - [2.5 multiset容器insert返回值](#25-multiset容器insert返回值)
- [四、实例](#四实例)
- [五、经验](#五经验)

##### 一、概述
set是关联容器，含有key类型对象的已排序集。用比较函数compare进行排序。搜索、移除和插入拥有对数复杂度，set通常以红黑树实现。
##### 二、使用
###### 2.1 基本使用方法
```
begin()         -- 返回指向第一个元素的迭代器
clear()         -- 清除所有元素
count()         -- 返回某个值元素的个数
empty()         -- 如果集合为空，返回true
end()           -- 返回指向最后一个元素的迭代器
equal_range()   -- 返回集合中与给定值相等的上下限的两个迭代器
erase()         -- 删除集合中的元素
find()          -- 返回一个指向被查找到元素的迭代器
get_allocator() -- 返回集合的分配器
insert()        -- 在集合中插入元素
lower_bound()   -- 返回指向大于（或等于）某值的第一个元素的迭代器
key_comp()      -- 返回一个用于元素间值比较的函数
max_size()      -- 返回集合能容纳的元素的最大限值
rbegin()        -- 返回指向集合中最后一个元素的反向迭代器
rend()          -- 返回指向集合中第一个元素的反向迭代器
size()          -- 集合中元素的数目
swap()          -- 交换两个集合变量
upper_bound()   -- 返回大于某个值元素的迭代器
value_comp()    -- 返回一个用于比较元素间的值的函数
```
###### 2.2 自定义排序
- 默认排序
```
void test01()
{
    // set容器默认从小到大排序
    set<int> s;
    s.insert(20);
    s.insert(10);
    s.insert(30);
 
    //输出set
    for (set<int>::iterator iter = s.begin(); iter != s.end(); ++iter)
        cout << *iter << " ";
    cout << endl;
    //结果为:10 20 30
}
```
- 添加仿函数
```
/* 仿函数CompareSet，在test02使用 */
class CompareSet
{
public:
    //从大到小排序
    bool operator()(int v1, int v2)
    {
        return v1 > v2;
    }
    //从小到大排序
    //bool operator()(int v1, int v2)
    //{
    //    return v1 < v2;
    //}
};

void test02()
{
    /* 如果想让set容器从大到小排序，需要给set容
       器提供一个仿函数,本例的仿函数为CompareSet
    */
    set<int, CompareSet> s;
    s.insert(10);
    s.insert(20);
    s.insert(30);
    
    //打印set
    // PrintSet(s);
    for (set<int, CompareSet>::iterator iter = s.begin(); iter != s.end(); ++iter)
        cout << *iter << " ";
    cout << endl;
    //结果为:30,20,10
}
```
###### 2.3 自定义类型
```
#include <iostream>
#include <string>
#include <set>
using namespace std;

/* Person类，用于test03 */
class Person
{
    friend ostream &operator<<(ostream &out, const Person &person);
public:
    Person(string name, int age)
    {
        mName = name;
        mAge = age;
    }
public:
    string mName;
    int mAge;
};
 
ostream &operator<<(ostream &out, const Person &person)
{
    out << "name:" << person.mName << " age:" << person.mAge << endl;
    return out;
}
 
/* 仿函数ComparePerson,用于test03 */
class ComparePerson
{
public:
    // 名字大的在前面，如果名字相同，年龄大的排前面
    bool operator()(const Person &p1, const Person &p2)
    {
        if (p1.mName == p2.mName)
        {
            return p1.mAge > p2.mAge;
        }
        return p1.mName > p2.mName;
    }
};

void test03()
{
    /* set元素类型为Person，当set元素类型为自定义类型的时候
       必须给set提供一个仿函数，用于比较自定义类型的大小，
       否则无法通过编译 
    */
    set<Person,ComparePerson> s;
    s.insert(Person("John", 22));
    s.insert(Person("Peter", 25));
    s.insert(Person("Marry", 18));
    s.insert(Person("Peter", 36));
 
    // 打印set
    // PrintSet(s);
    for (set<Person, ComparePerson>::iterator iter = s.begin(); iter != s.end(); ++iter)
        cout << *iter << " ";
    cout << endl;
}
```
###### 2.4 set容器insert返回值
```
/* set的insert函数返回值为一个对组(pair)。
   对组的第一个值first为set类型的迭代器：
   1、若插入成功，迭代器指向该元素。
   2、若插入失败，迭代器指向之前已经存在的元素
   对组的第二个值seconde为bool类型：
   1、若插入成功，bool值为true
   2、若插入失败，bool值为false
*/
pair<set<int>::iterator, bool> ret = s.insert(40);
if (true == ret.second)
   cout << *ret.first << " 插入成功" << endl;
else
   cout << *ret.first << " 插入失败" << endl;
```
###### 2.5 multiset容器insert返回值
```
#include <iostream>
#include <set>
using namespace std;
 
#define T multiset<int>
void PrintSet(T s)
{
    for (T::iterator iter = s.begin(); iter != s.end(); ++iter)
        cout << *iter << " ";
    cout << endl;
}
 
void test(void)
{
    multiset<int> s;
    s.insert(10);
    s.insert(20);
    s.insert(30);
    
    //打印multiset
    PrintSet(s);
 
    /* multiset的insert函数返回值为multiset类型的迭代器，
       指向新插入的元素。multiset允许插入相同的值，因此
       插入一定成功，因此不需要返回bool类型。
    */
    multiset<int>::iterator iter = s.insert(10);
    
    cout << *iter << endl;    
}

int main()
{
    test();
    return 0;
}
```
##### 四、实例
```
std::set<int> s;
std::multiset<int> m;
for (int i = 0; i < 5; i++)
{
   s.insert(i); 
}

auto temp = s.find(2);
std::cout << *temp << std::endl;
std::cout << s.count(2) << std::endl;
```
##### 五、经验
- set容器内的元素会被自动排序，set与map不同，set中的元素即是键值又是实值，set不允许两个元素有相同的键值，不能通过set的迭代器去修改set元素，原因是修改元素会破坏set组织，当对容器中的元素进行插入或者删除时，操作之前的所有迭代器在操作之后仍然有效。
- 由于set元素是排好序的，且默认是升序，因此当set集合中的元素为结构体或者自定义类时，该结构体或者自定义类必须实现运算符<的重载。
- multiset特性以及用法和set完全相同，唯一的差别在于它允许键值重复。
set与multiset的底层实现都是一种高效的平衡二叉树（红黑树）
```


