##### 一、介绍
所谓的deque是”double ended queue”的缩写，双端队列不论在尾部或头部插入元素，都十分迅速。而在中间插入元素则会比较费时，因为必须移动中间其他的元素。双端队列是一种随机访问的数据类型，提供了在序列两端快速插入和删除操作的功能，它可以在需要的时候改变自身大小，完成了标准的C++数据结构中队列的所有功能。 

Vector是单向开口的连续线性空间，deque则是一种双向开口的连续线性空间。deque对象在队列的两端放置元素和删除元素是高效的，而向量vector只是在插入序列的末尾时操作才是高效的。deque和vector的最大差异，一在于deque允许于常数时间内对头端进行元素的插入或移除操作，二在于deque没有所谓的capacity观念，因为它是动态地以分段连续空间组合而成，随时可以增加一段新的空间并链接起来。换句话说，像vector那样“因旧空间不足而重新配置一块更大空间，然后复制元素，再释放旧空间”这样的事情在deque中是不会发生的。也因此，deque没有必要提供所谓的空间预留（reserved）功能。 

虽然deque也提供Random Access Iterator，但它的迭代器并不是普通指针，其复杂度和vector不可同日而语，这当然涉及到各个运算层面。因此，除非必要，我们应尽可能选择使用vector而非deque。对deque进行的排序操作，为了最高效率，可将deque先完整复制到一个vector身上，将vector排序后（利用STL的sort算法），再复制回deque。 

deque是一种优化了的对序列两端元素进行添加和删除操作的基本序列容器。通常由一些独立的区块组成，第一区块朝某方向扩展，最后一个区块朝另一方向扩展。它允许较为快速地随机访问但它不像vector一样把所有对象保存在一个连续的内存块，而是多个连续的内存块。并且在一个映射结构中保存对这些块以及顺序的跟踪。

##### 二、使用
###### 2.1 定义声明
```
// #include<deque>  // 头文件
// 声明一个元素类型为type的双端队列que
deque<type> deq;
// 声明一个类型为type、含有size个默认值初始化元素的的双端队列que
deque<type> deq(size);
// 声明一个元素类型为type、含有size个value元素的双端队列que
deque<type> deq(size, value);
// deq是mydeque的一个副本
deque<type> deq(mydeque);
// 使用迭代器first、last范围内的元素初始化deq
deque<type> deq(first, last);
```
###### 2.2 常用方法
```
// deq[ ]：用来访问双向队列中单个的元素。
// deq.front()：返回第一个元素的引用。
// deq.back()：返回最后一个元素的引用。
// deq.push_front(x)：把元素x插入到双向队列的头部。
// deq.pop_front()：弹出双向队列的第一个元素。
// deq.push_back(x)：把元素x插入到双向队列的尾部。
// deq.pop_back()：弹出双向队列的最后一个元素。
```
###### 2.3 特点
```
// 支持随机访问，即支持[ ]以及at()，但是性能没有vector好。
// 可以在内部进行插入和删除操作，但性能不及list。
// deque两端都能够快速插入和删除元素，而vector只能在尾端进行。
// deque的元素存取和迭代器操作会稍微慢一些，因为deque的内部结构会多一个间接过程。
// deque迭代器是特殊的智能指针，而不是一般指针，它需要在不同的区块之间跳转。
// deque可以包含更多的元素，其max_size可能更大，因为不止使用一块内存。
// deque不支持对容量和内存分配时机的控制。
// 在除了首尾两端的其他地方插入和删除元素，都将会导致指向deque元素的任何pointers、references、iterators失效。不过，deque的内存重分配优于vector，因为其内部结构显示不需要复制所有元素。
// deque的内存区块不再被使用时，会被释放，deque的内存大小是可缩减的。不过，是不是这么做以及怎么做由实际操作版本定义。
// deque不提供容量操作：capacity()和reverse()，但是vector可以。
```
###### 2.4 实例
```
int i;
int a[10] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
deque<int> q;
for (i = 0; i <= 9; i++)
{
    if (i % 2 == 0)
        q.push_front(a[i]);
    else
        q.push_back(a[i]);
} /*此时队列里的内容是: {8,6,4,2,0,1,3,5,7,9}*/
q.pop_front();
printf("%d\n", q.front()); /*清除第一个元素后输出第一个(6)*/
q.pop_back();
printf("%d\n", q.back()); /*清除最后一个元素后输出最后一个(7)*/
deque<int>::iterator it;
for (it = q.begin(); it != q.end(); it++)
{
    cout << *it << '\t';
}
cout << endl;
```
##### 三、经验
