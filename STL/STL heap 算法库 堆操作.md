# 算法库 -堆操作





基本操作

|`make_heap()`|(1)从一个元素范围创建出一个最大堆 (2)将区间内的元素转化为heap.--传比较器|
|---|---|
|`push_heap()`|对heap增加一个元素.将一个元素加入到一个最大堆|
|`pop_heap()`|对heap取出下一个元素.从最大堆中移除最大元素|
|`sort_heap()`|对heap转化为一个已排序群集.将一个最大堆变成一个按升序排序的元素范围|



C++11新增特性
|is_heap      |检查给定范围是否为一个最大堆|
|---|---|
|is_heap_until  |查找能成为最大堆的最大子范围,返回有效二叉堆的最末范围。如果都有效，则返回last.否则返回第一个非二叉堆结构元素的迭代器。|





### 1.make_heap()

make_heap()用于把一个可迭代容器变成一个堆，默认是大顶堆。

它有三个参数。第一个参数是指向开始元素的迭代器，第二个参数是指向最末尾元素的迭代器，第三个参数是less<>()或是greater<>()，前者用于生成大顶堆，后者用于生成小顶堆，第三个参数默认情况下为less<>()，less<int>()用于生成大顶堆。

> 要使用less<int>()，以及greater<int>()，请添加头文件#include <functional>，且一定要加括号less<>()

### 2.pop_heap()

pop_heap()用于将堆的第零个元素与最后一个元素交换位置，然后针对前n - 1个元素调用make_heap()函数，它也有三个参数，参数意义与make_heap()相同，第三个参数应与make_heap时的第三个参数保持一致。

> 注意:pop_heap()函数，只是交换了两个数据的位置，如果需要弹出这个数据，需要在pop_heap()后加上
>
> pop_back();



### 3.push_heap()

push_heap()用于把数据插入到堆中，它也有三个参数，其意义与make_heap()的相同，第三个参数应与make_heap时的第三个参数保持一致。

> 在使用push_heap()前，请确保数据存在于容器中



### 4.sort_heap()

sort_heap()是将堆进行排序，排序后，序列将失去堆的特性（子节点的键值总是小于或大于它的父节点）。它也具有三个参数，参数意义与make_heap()相同，第三个参数应与make_heap时的第三个参数保持一致。大顶堆sort_heap()后是一个递增序列，小顶堆是一个递减序列。

> 在使用这个函数前，需要确保序列符合堆的特性，否则会报错！

![](STL%20heap%20%E7%AE%97%E6%B3%95%E5%BA%93%20%E5%A0%86%E6%93%8D%E4%BD%9C.assets/image-20240813122505260.png)

[算法库 - cppreference.com](https://zh.cppreference.com/w/cpp/algorithm)

[【C++】STL中heap函数的用法(make_heap，push_heap，pop_heap，sort_heap，is_heap，is_heap_until)_c++heap.pop()-CSDN博客](https://blog.csdn.net/bandaoyu/article/details/109441444)
