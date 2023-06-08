- [一、vector容器概述](#一vector容器概述)
- [二、方法](#二方法)
  - [2.1 常见用法](#21-常见用法)
  - [2.2 构造方法](#22-构造方法)
  - [2.3 其他方法及实例](#23-其他方法及实例)
- [三、源码参考](#三源码参考)
- [四、迭代器失效](#四迭代器失效)
- [五、经验总结](#五经验总结)

##### 一、vector容器概述
vector的数据安排以及操作方式，与array非常相似。两者的唯一区别在于空间的运用的灵活性。array是静态空间，一旦配置了就不能改变；要换个大（或小）一点的房子，可以，一切琐细都得由客户端自己来：首先配置一块新空间，然后将元素从旧址一一搬往新址，再把原来的空间释还给系统。vector是动态空间，随着元素的加入，它的内部机制会自行扩充空间以容纳新元素。因此，vector的运用对于内存的合理利用与运用的灵活性有很大的帮助，我们再也不必因为害怕空间不足而一开始要求一个大块头的array了，我们可以安心使用array，吃多少用多少。

vector的实现技术，关键在于其对大小的控制以及重新配置时的数据移动效率。一旦vector的旧有空间满载，如果客户端每新增一个元素，vector的内部只是扩充一个元素的空间，实为不智。因为所谓扩充空间（不论多大），一如稍早所说，是”配置新空间/数据移动/释还旧空间“的大工程，时间成本很高，应该加入某种未雨绸缪的考虑。稍后我们便可看到SGI vector的空间配置策略了。

另外，由于vector维护的是一个连续线性空间，所以vector支持随机存取。

注意：vector动态增加大小时，并不是在原空间之后持续新空间（因为无法保证原空间之后尚有可供配置的空间），而是以原大小的两倍另外配置一块较大的空间，然后将原内容拷贝过来，然后才开始在原内容之后构造新元素，并释放原空间。**因此，对vector的任何操作，一旦引起空间重新配置，指向原vector的所有迭代器就都失效了**。这是程序员易犯的一个错误，务需小心。

![Image text](https://github.com/7Meet112/Others/blob/main/Image/vector2.png)
![Image text](https://github.com/7Meet112/Others/blob/main/Image/vector1.png)
##### 二、方法
###### 2.1 常见用法
```
1.push_back 	在数组的最后添加一个数据
2.pop_back 		去掉数组的最后一个数据
3.at 			得到编号位置的数据
4.begin 		得到数组头的指针
5.end 			得到数组的最后一个单元+1的指针
6.front 		得到数组头的引用
7.back 			得到数组的最后一个单元的引用
8.max_size 		得到vector最大可以是多大
9.capacity 		当前vector分配的大小
10.size 		当前使用数据的大小
11.resize 		改变当前使用数据的大小，如果它比当前使用的大，则填充默认值
12.reserve 		改变当前vecotr所分配空间的大小
13.erase 		删除指针指向的数据项
14.clear 		清空当前的vector
15.rbegin 		将vector反转后的开始指针返回(其实就是原来的end-1)
16.rend 		将vector反转后的结束指针返回(其实就是原来的begin-1)
17.empty 		判断vector是否为空
18.swap 		与另一个vector交换数据
```
###### 2.2 构造方法
```
vector<T> v; 				采用模板实现类实现，默认构造函数
vector(v.begin(), v.end());	将v[begin(), end())区间中的元素拷贝给本身
vector(n, elem);			构造函数将n个elem拷贝给本身
vector(const vector &vec);	拷贝构造函数
```
###### 2.3 其他方法及实例
```
// 数组列表初始化
int arr[] = {2, 3, 4, 1, 9};
vector<int> v1(arr, arr + sizeof(arr) / sizeof(int));

// 访问数据
vector<int> v1;
int nSize = v1.size();
// 方法一：直接利用数组
for (int i = 0; i < nSize; i++)
{
	cout << v1[i] << " ";
}
// 方法二：使用迭代器将容器中数据输出
// vector<int>::iterator it;
for (vector<int>::iterator it = v1.begin(); it != v1.end(); it++)
{
	cout << *it << " ";
}
// 方法三：C++11 auto 
// 需要改变值的话，for (auto &c : input)，或者for (auto &&c : input)
for (auto cc : v1)
{
	std::cout << cc << " ";
}

// 交换两个同类型向量的数据
void swap(vector &);

// 设置向量中前n个元素的值为x
void assign(int n, const T &x)
v1.assign(v1.size(), 111);
v1.assign(2, 112); // 改变size和capacity:2

// 向量中[first,last)中元素设置成当前向量元素
void assign(const_iterator first, const_iterator last)
v1.assign(v1.begin(), v1.end());

// 清除容器中所有数据
v1.clear();

// 排序
// #include <algorithm>
cout << "从小到大:" << endl;
sort(v1.begin(), v1.end());

// 反转
cout << "从大到小:" << endl;
reverse(v1.begin(), v1.end());
// 如果想 sort 来降序，可重写 sort
bool compare(int a, int b)
{
	// 升序排列，如果改为return a>b，则为降序
	return a < b;
}
int a[20] = {2, 4, 1, 23, 5, 76, 0, 43, 24, 65}, i;
for (i = 0; i < 20; i++)
	cout << a[i] << endl;
sort(a, a + 20, compare);

// 二维数组相关
// 方法一：
int N = 5, M = 6;
// 定义二维动态数组大小5行
vector<vector<int>> obj(N);
// 动态二维数组为5行6列，值全为0
for (int i = 0; i < obj.size(); i++)
{
	obj[i].resize(M);
}

for (int i = 0; i < obj.size(); i++) // 输出二维动态数组
{
	for (int j = 0; j < obj[i].size(); j++)
	{
		cout << obj[i][j] << " ";
	}
	cout << "\n";
}

// 方法二：
int N = 5, M = 6;
// 定义二维动态数组5行6列
vector<vector<int>> obj(N, vector<int>(M)); 
for (int i = 0; i < obj.size(); i++)
{
	for (int j = 0; j < obj[i].size(); j++)
	{
		cout << obj[i][j] << " ";
	}
	cout << "\n";
}
// 经验
vector<vector<int>> res;
for (int i = 0; i < 4; ++i)
{
	res.push_back(vector<int>());
	res.back().push_back(i);
	// res.push_back(i);   // 报错，i不是vector<int> 类型
}

// vector<vector<int> > array
// int m=array.size();
// int n=array[0].size();
```
##### 三、源码参考

```
#include <iostream>
using namespace std;
#include <memory.h>

// alloc是SGI STL的空间配置器
template <class T, class Alloc = alloc>
class vector
{
public:
    // vector的嵌套类型定义,typedefs用于提供iterator_traits<I>支持
    typedef T value_type;
    typedef value_type *pointer;
    typedef value_type *iterator;
    typedef value_type &reference;
    typedef size_t size_type;
    typedef ptrdiff_t difference_type;

protected:
    // 这个提供STL标准的allocator接口
    typedef simple_alloc<value_type, Alloc> data_allocator;

    iterator start;          // 表示目前使用空间的头
    iterator finish;         // 表示目前使用空间的尾
    iterator end_of_storage; // 表示实际分配内存空间的尾

    void insert_aux(iterator position, const T &x);

    // 释放分配的内存空间
    void deallocate()
    {
        // 由于使用的是data_allocator进行内存空间的分配,
        // 所以需要同样使用data_allocator::deallocate()进行释放
        // 如果直接释放, 对于data_allocator内部使用内存池的版本
        // 就会发生错误
        if (start)
            data_allocator::deallocate(start, end_of_storage - start);
    }

    // 赋值 n 个 value
    void fill_initialize(size_type n, const T &value)
    {
        start = allocate_and_fill(n, value);
        finish = start + n; // 设置当前使用内存空间的结束点
        // 构造阶段, 此实作不多分配内存,
        // 所以要设置内存空间结束点和, 已经使用的内存空间结束点相同
        end_of_storage = finish;
    }

public:
    // 获取几种迭代器
    iterator begin() { return start; }
    iterator end() { return finish; }

    // 返回当前对象个数
    size_type size() const { return size_type(end() - begin()); }
    size_type max_size() const { return size_type(-1) / sizeof(T); }

    // 返回重新分配内存前最多能存储的对象个数
    size_type capacity() const { return size_type(end_of_storage - begin()); }
    bool empty() const { return begin() == end(); }

    reference operator[](size_type n) { return *(begin() + n); }

    // 本实作中默认构造出的vector不分配内存空间
    vector() : start(0), finish(0), end_of_storage(0) {}

    vector(size_type n, const T &value) { fill_initialize(n, value); }
    vector(int n, const T &value) { fill_initialize(n, value); }
    vector(long n, const T &value) { fill_initialize(n, value); }

    // 需要对象提供默认构造函数
    explicit vector(size_type n) { fill_initialize(n, T()); }

    vector(const vector<T, Alloc> &x)
    {
        start = allocate_and_copy(x.end() - x.begin(), x.begin(), x.end());
        finish = start + (x.end() - x.begin());
        end_of_storage = finish;
    }

    ~vector()
    {
        // 析构对象
        destroy(start, finish);
        // 释放内存
        deallocate();
    }

    vector<T, Alloc> &operator=(const vector<T, Alloc> &x);

    // 提供访问函数
    reference front() { return *begin(); }
    reference back() { return *(end() - 1); }

    ////////////////////////////////////////////////////////////////////////////////
    // 向容器尾追加一个元素, 可能导致内存重新分配
    ////////////////////////////////////////////////////////////////////////////////
    //                          push_back(const T& x)
    //                                   |
    //                                   |---------------- 容量已满?
    //                                   |
    //               ----------------------------
    //           No  |                          |  Yes
    //               |                          |
    //               ↓                          ↓
    //      construct(finish, x);       insert_aux(end(), x);
    //      ++finish;                           |
    //                                          |------ 内存不足, 重新分配
    //                                          |       大小为原来的2倍
    //      new_finish = data_allocator::allocate(len);       <stl_alloc.h>
    //      uninitialized_copy(start, position, new_start);   <stl_uninitialized.h>
    //      construct(new_finish, x);                         <stl_construct.h>
    //      ++new_finish;
    //      uninitialized_copy(position, finish, new_finish); <stl_uninitialized.h>
    ////////////////////////////////////////////////////////////////////////////////

    void push_back(const T &x)
    {
        // 内存满足条件则直接追加元素, 否则需要重新分配内存空间
        if (finish != end_of_storage)
        {
            construct(finish, x);
            ++finish;
        }
        else
            insert_aux(end(), x);
    }

    ////////////////////////////////////////////////////////////////////////////////
    // 在指定位置插入元素
    ////////////////////////////////////////////////////////////////////////////////
    //                   insert(iterator position, const T& x)
    //                                   |
    //                                   |------------ 容量是否足够 && 是否是end()?
    //                                   |
    //               -------------------------------------------
    //            No |                                         | Yes
    //               |                                         |
    //               ↓                                         ↓
    //    insert_aux(position, x);                  construct(finish, x);
    //               |                              ++finish;
    //               |-------- 容量是否够用?
    //               |
    //        --------------------------------------------------
    //    Yes |                                                | No
    //        |                                                |
    //        ↓                                                |
    // construct(finish, *(finish - 1));                       |
    // ++finish;                                               |
    // T x_copy = x;                                           |
    // copy_backward(position, finish - 2, finish - 1);        |
    // *position = x_copy;                                     |
    //                                                         ↓
    // data_allocator::allocate(len);                       <stl_alloc.h>
    // uninitialized_copy(start, position, new_start);      <stl_uninitialized.h>
    // construct(new_finish, x);                            <stl_construct.h>
    // ++new_finish;
    // uninitialized_copy(position, finish, new_finish);    <stl_uninitialized.h>
    // destroy(begin(), end());                             <stl_construct.h>
    // deallocate();
    ////////////////////////////////////////////////////////////////////////////////

    iterator insert(iterator position, const T &x)
    {
        size_type n = position - begin();
        if (finish != end_of_storage && position == end())
        {
            construct(finish, x);
            ++finish;
        }
        else
            insert_aux(position, x);
        return begin() + n;
    }

    iterator insert(iterator position) { return insert(position, T()); }

    void pop_back()
    {
        --finish;
        destroy(finish);
    }

    iterator erase(iterator position)
    {
        if (position + 1 != end())
            copy(position + 1, finish, position);
        --finish;
        destroy(finish);
        return position;
    }

    iterator erase(iterator first, iterator last)
    {
        iterator i = copy(last, finish, first);
        // 析构掉需要析构的元素
        destroy(i, finish);
        finish = finish - (last - first);
        return first;
    }

    // 调整size, 但是并不会重新分配内存空间
    void resize(size_type new_size, const T &x)
    {
        if (new_size < size())
            erase(begin() + new_size, end());
        else
            insert(end(), new_size - size(), x);
    }
    void resize(size_type new_size) { resize(new_size, T()); }

    void clear() { erase(begin(), end()); }

protected:
    // 分配空间, 并且复制对象到分配的空间处
    iterator allocate_and_fill(size_type n, const T &x)
    {
        iterator result = data_allocator::allocate(n);
        uninitialized_fill_n(result, n, x);
        return result;
    }

    // 提供插入操作
    ////////////////////////////////////////////////////////////////////////////////
    //                 insert_aux(iterator position, const T& x)
    //                                   |
    //                                   |---------------- 容量是否足够?
    //                                   ↓
    //              -----------------------------------------
    //        Yes   |                                       | No
    //              |                                       |
    //              ↓                                       |
    // 从opsition开始, 整体向后移动一个位置                     |
    // construct(finish, *(finish - 1));                    |
    // ++finish;                                            |
    // T x_copy = x;                                        |
    // copy_backward(position, finish - 2, finish - 1);     |
    // *position = x_copy;                                  |
    //                                                      ↓
    //                            data_allocator::allocate(len);
    //                            uninitialized_copy(start, position, new_start);
    //                            construct(new_finish, x);
    //                            ++new_finish;
    //                            uninitialized_copy(position, finish, new_finish);
    //                            destroy(begin(), end());
    //                            deallocate();
    ////////////////////////////////////////////////////////////////////////////////

    template <class T, class Alloc>
    void insert_aux(iterator position, const T &x)
    {
        if (finish != end_of_storage) // 还有备用空间
        {
            // 在备用空间起始处构造一个元素，并以vector最后一个元素值为其初值
            construct(finish, *(finish - 1));
            ++finish;
            T x_copy = x;
            copy_backward(position, finish - 2, finish - 1);
            *position = x_copy;
        }
        else // 已无备用空间
        {
            const size_type old_size = size();
            const size_type len = old_size != 0 ? 2 * old_size : 1;
            // 以上配置元素：如果大小为0，则配置1（个元素大小）
            // 如果大小不为0，则配置原来大小的两倍
            // 前半段用来放置原数据，后半段准备用来放置新数据

            iterator new_start = data_allocator::allocate(len); // 实际配置
            iterator new_finish = new_start;
            // 将内存重新配置
            try
            {
                // 将原vector的安插点以前的内容拷贝到新vector
                new_finish = uninitialized_copy(start, position, new_start);
                // 为新元素设定初值 x
                construct(new_finish, x);
                // 调整水位
                ++new_finish;
                // 将安插点以后的原内容也拷贝过来
                new_finish = uninitialized_copy(position, finish, new_finish);
            }
            catch (...)
            {
                // 回滚操作
                destroy(new_start, new_finish);
                data_allocator::deallocate(new_start, len);
                throw;
            }
            // 析构并释放原vector
            destroy(begin(), end());
            deallocate();

            // 调整迭代器，指向新vector
            start = new_start;
            finish = new_finish;
            end_of_storage = new_start + len;
        }
    }

    ////////////////////////////////////////////////////////////////////////////////
    // 在指定位置插入n个元素
    ////////////////////////////////////////////////////////////////////////////////
    //             insert(iterator position, size_type n, const T& x)
    //                                   |
    //                                   |---------------- 插入元素个数是否为0?
    //                                   ↓
    //              -----------------------------------------
    //        No    |                                       | Yes
    //              |                                       |
    //              |                                       ↓
    //              |                                    return;
    //              |----------- 内存是否足够?
    //              |
    //      -------------------------------------------------
    //  Yes |                                               | No
    //      |                                               |
    //      |------ (finish - position) > n?                |
    //      |       分别调整指针                              |
    //      ↓                                               |
    //    ----------------------------                      |
    // No |                          | Yes                  |
    //    |                          |                      |
    //    ↓                          ↓                      |
    // 插入操作, 调整指针           插入操作, 调整指针           |
    //                                                      ↓
    //            data_allocator::allocate(len);
    //            new_finish = uninitialized_copy(start, position, new_start);
    //            new_finish = uninitialized_fill_n(new_finish, n, x);
    //            new_finish = uninitialized_copy(position, finish, new_finish);
    //            destroy(start, finish);
    //            deallocate();
    ////////////////////////////////////////////////////////////////////////////////

    template <class T, class Alloc>
    void insert(iterator position, size_type n, const T &x)
    {
        // 如果n为0则不进行任何操作
        if (n != 0)
        {
            if (size_type(end_of_storage - finish) >= n)
            { // 剩下的备用空间大于等于“新增元素的个数”
                T x_copy = x;
                // 以下计算插入点之后的现有元素个数
                const size_type elems_after = finish - position;
                iterator old_finish = finish;
                if (elems_after > n)
                {
                    // 插入点之后的现有元素个数 大于 新增元素个数
                    uninitialized_copy(finish - n, finish, finish);
                    finish += n; // 将vector 尾端标记后移
                    copy_backward(position, old_finish - n, old_finish);
                    fill(position, position + n, x_copy); // 从插入点开始填入新值
                }
                else
                {
                    // 插入点之后的现有元素个数 小于等于 新增元素个数
                    uninitialized_fill_n(finish, n - elems_after, x_copy);
                    finish += n - elems_after;
                    uninitialized_copy(position, old_finish, finish);
                    finish += elems_after;
                    fill(position, old_finish, x_copy);
                }
            }
            else
            { // 剩下的备用空间小于“新增元素个数”（那就必须配置额外的内存）
                // 首先决定新长度：就长度的两倍 ， 或旧长度+新增元素个数
                const size_type old_size = size();
                const size_type len = old_size + max(old_size, n);
                // 以下配置新的vector空间
                iterator new_start = data_allocator::allocate(len);
                iterator new_finish = new_start;
                __STL_TRY
                {
                    // 以下首先将旧的vector的插入点之前的元素复制到新空间
                    new_finish = uninitialized_copy(start, position, new_start);
                    // 以下再将新增元素（初值皆为n）填入新空间
                    new_finish = uninitialized_fill_n(new_finish, n, x);
                    // 以下再将旧vector的插入点之后的元素复制到新空间
                    new_finish = uninitialized_copy(position, finish, new_finish);
                }
#ifdef __STL_USE_EXCEPTIONS
                catch (...)
                {
                    destroy(new_start, new_finish);
                    data_allocator::deallocate(new_start, len);
                    throw;
                }
#endif /* __STL_USE_EXCEPTIONS */
                destroy(start, finish);
                deallocate();
                start = new_start;
                finish = new_finish;
                end_of_storage = new_start + len;
            }
        }
    }
};
```
##### 四、迭代器失效
- insert和erase引起的迭代器失效

当进行insert和erase操作，都会使得插入和删除点之后的元素位置改变，插入点和
删除点之后的迭代器全部失效。
解决方法就是更新之后的迭代器：

对于删除，erase()返回的是下一个有效迭代器的值，
可以通过iter = v.erase(iter)来避免迭代器失效。

对于插入，insert返回的是插入元素的迭代器的值，可以通过+2来避免迭代器失效。

解决方法：
```
void vectorTest()
{
    vector<int> container;
    for (int i = 0; i < 10; i++)
    {
        container.push_back(i);
    }

    vector<int>::iterator iter;
    for (iter = container.begin(); iter != container.end(); ) // 这里没有++
    {
        if (*iter == 3)
        {
            iter = container.erase(iter); // erase的返回值是删除元素下一个元素的迭代器
	    std::cout << *iter << std::endl; // 4
        }
        else
        {
            iter++; // 注意此处才后移
        }
    }

    vector<int>::iterator iter;
    for (iter = container.begin(); iter != container.end(); ) // 这里没有++
    {
        if (*iter == 3)
        {
            iter = container.insert(iter, 12); // 返回值是指向插入元素的迭代器
	    std::cout << *iter << std::endl; // 12
            iter += 2;  // 本例中，插入之前iter指向3，在3之前插入12，iter指向12，insert 之后需要iter指向新增加的元素，+2 之后指向下一个要处理的元素
	    std::cout << *iter << std::endl; // 4
        }
        else
        {
            iter++; // 注意此处才后移
        }
    }
}
```

- 建议resize()和operator[] 一起使用

```
    vector<int> v;
    v.reserve(10); // 空间重新配置，迭代器失效
    v[2] = 1;
    std::cout << v.capacity() << std::endl; // 10
    std::cout << v.size() << std::endl;     // 0

    vector<int> v2;
    v2.resize(10);
    v2[2] = 1;
    std::cout << v2.capacity() << std::endl; // 10
    std::cout << v2.size() << std::endl;     // 10
```

##### 五、经验总结
- 提前分配好部分容量，减少因扩充发生的拷贝   
v.reserve(num);
- 空间申请
reserve(nNum): 申请空间，扩大[finish, end_of_storage),改变capacity,仅当nNum > capaciry时有效。
resize(nNum): 申请空间并赋值，改变[start, finish),改变size。
- 空间释放
erase()、resize()、clear()改变finish(size),不改变end_of_storage。
swap()释放空间，改变end_of_storage。

```
v.reserve(10);
vector<int>().swap(v); // capacity = size = 0
```
##### 六、补充
1. 在c++11中，vector 增加了data()的用法，它返回内置vecotr所指的数组内存的第一个元素的指针.

