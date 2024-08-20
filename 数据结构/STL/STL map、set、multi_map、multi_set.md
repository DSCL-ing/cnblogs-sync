[toc]





## 基本概念

### 关联式容器 

> 在初阶阶段，我们已经接触过STL中的部分容器，比如：vector、list、deque、 
> forward_list(C++11)等，这些容器统称为序列式容器，因为其底层为线性序列的数据结构，里面 
> 存储的是元素本身。那什么是关联式容器？它与序列式容器有什么区别？
> 关联式容器也是用来存储数据的，与序列式容器不同的是，其里面存储的是<key, value>结构的 
> 键值对，在数据检索时比序列式容器效率更高。



### 键值对 

> 用来表示具有一一对应 关系的一种结构，该结构中一般只包含两个成员变量key和value，key代 
> 表键值，value表示与key对应的信息。比如：现在要建立一个英汉互译的字典，那该字典中必然 
> 有英文单词与其对应的中文含义，而且，英文单词与其中文含义是一一对应的关系，即通过该应 
> 该单词，在词典中就可以找到与其对应的中文含义。
> SGI-STL中关于键值对的定义：

```
template <class T1, class T2> 
struct pair 
{
typedef T1 first_type; 
typedef T2 second_type;

T1 first;
T2 second;
pair(): first(T1()), second(T2()) 
{}
 
pair(const T1& a, const T2& b): first(a), second(b) 
{}
};
```



### 树形结构的关联式容器 

> 根据应用场景的不桶，STL总共实现了两种不同结构的管理式容器：树型结构与哈希结构。树型结 
> 构的关联式容器主要有四种：map、set、multimap、multiset。这四种容器的共同点是：使 
> 用平衡搜索树(即红黑树)作为其底层结果，容器中的元素是一个有序的序列。下面一依次介绍每一 
> 个容器。



## set

### 描述

[set文档](https://cplusplus.com/reference/set/set/?kw=set)

> 翻译
>1. set是按照一定次序存储元素的容器
>2. 在set中，元素的value也标识它(value就是key，类型为T)，并且每个value必须是唯一的。
>set中的元素不能在容器中修改(元素总是const)，但是可以从容器中插入或删除它们。
>3. 在内部，set中的元素总是按照其内部比较对象(类型比较)所指示的特定严格弱排序准则进行
>排序。
>4. set容器通过key访问单个元素的速度通常比unordered_set容器慢，但它们允许根据顺序对
>子集进行直接迭代。
>5. set在底层是用二叉搜索树(红黑树)实现的。
>
>
>
>注意：
>
>1. 与map/multimap不同，map/multimap中存储的是真正的键值对<key, value>，set中只放
>value，但在底层实际存放的是由<value, value>构成的键值对。 
>2. set中插入元素时，只需要插入value即可，不需要构造键值对。 
>3. set中的元素不可以重复(因此可以使用set进行去重)。
>4. 使用set的迭代器遍历set中的元素，可以得到有序序列
>5. set中的元素默认按照小于来比较
>6. set中查找某个元素，时间复杂度为：$log_2 n$
>7. set中的元素不允许修改(为什么?)
>8. set中的底层使用二叉搜索树(红黑树)来实现。
>



### set的使用

模板参数列表

![image-20240820182841841](STL%20map%E3%80%81set%E3%80%81multi_map%E3%80%81multi_set.assets/image-20240820182841841.png)

参数:

| `key_type`      | `Key`     |
| --------------- | --------- |
| `value_type`    | `Key`     |
| `key_compare`   | `Compare` |
| `value_compare` | `Compare` |

> set中只有key,所以value_type也是key;同理value_compare等于key_compare
>
> Alloc：set中元素空间的管理方式，使用STL提供的空间配置器管理



基本构造函数

|函数声明 | 功能介绍|
|---|---|
|set (const Compare& comp = Compare(), const Allocator& = Allocator() ); | 构造空的set|
|set (InputIterator ﬁrst, InputIterator last, const Compare& comp = Compare(), const Allocator&= Allocator() ); |用[ﬁrst, last)区间中的元素构造 set|
|set ( const set<Key,Compare,Allocator>& x); |set的拷贝构造|
|... ||



set的迭代器
|函数声明 | 功能介绍|
|---|---|
iterator begin() |返回set中起始位置元素的迭代器
iterator end() |返回set中最后一个元素后面的迭代器
const_iterator cbegin() const |返回set中起始位置元素的const迭代器
const_iterator cend() const |返回set中最后一个元素后面的const迭代器 
reverse_iterator rbegin() |返回set第一个元素的反向迭代器，即end 
reverse_iterator rend() |返回set最后一个元素下一个位置的反向迭代器，即rbegin
const_reverse_iterator crbegin() const | 返回set第一个元素的反向const迭代器，即cend
const_reverse_iterator crend() const | 返回set最后一个元素下一个位置的反向const迭代器，即crbegin

> set支持正向迭代器与反向迭代器



set的容量

|函数声明 | 功能介绍|
|---|---|
bool empty ( ) const |检测set是否为空，空返回true，否则返回true 
size_type size() const |返回set中有效元素的个数



set的常规操作

|函数声明 | 功能介绍|
|---|---|
|pair<iterator,bool> insert ( const value_type& x )|在set中插入元素x，实际插入的是<x, x>构成的 键值对，如果插入成功，返回<该元素在set中的 位置，true>,如果插入失败，说明x在set中已经 存在，返回<x在set中的位置，false>|
|void erase ( iterator position ) | 删除set中position位置上的元素|
|size_type erase ( const key_type& x ) |删除set中值为x的元素，返回删除的元素的个数|
|void erase ( iterator ﬁrst, iterator last ) |删除set中[ﬁrst, last)区间中的元素|
|void swap ( set<Key,Compare,Allocator>& s );|交换两个set中的元素|
|void clear ( ) |将set中的元素清空|
|iterator ﬁnd ( const key_type& x ) const |返回set中值为x的元素的位置|
|size_type count ( const key_type& x ) const |返回set中值为x的元素的个数(set.count()只等于0或1)|





## map

[map文档](https://legacy.cplusplus.com/reference/map/map/)

### 描述

>翻译：
>1. map是关联容器，它按照特定的次序(按照key来比较)存储由键值key和值value组合而成的元素。
>2. 在map中，键值key通常用于排序和惟一地标识元素，而值value中存储与此键值key关联的内容。键值key和值value的类型可能不同，并且在map的内部，key与value通过成员类型 value_type绑定在一起，为其取别名称为pair:
>typedef pair<const key, T> value_type;
>3. 在内部，map中的元素总是按照键值key进行比较排序的。
>4. map中通过键值访问单个元素的速度通常比unordered_map容器慢，但map允许根据顺序对元素进行直接迭代(即对map中的元素进行迭代时，可以得到一个有序的序列)。 
>5. map支持下标访问符，即在[]中放入key，就可以找到与key对应的value。
>6. map通常被实现为二叉搜索树(更准确的说：平衡二叉搜索树(红黑树))。
>
>





### map的使用

模板参数列表

![image-20240820212232400](STL%20map%E3%80%81set%E3%80%81multi_map%E3%80%81multi_set.assets/image-20240820212232400.png)

|key|键值对中key的类型|
|---|---|
T|键值对中value的类型
Compare:|较器的类型，map中的元素是按照key来比较的，缺省情况下按照小于来比较，一般情况下(内置类型元素)该参数不需要传递，如果无法比较时(自定义类型)，需要用户自己显式传递比较规则(一般情况下按照函数指针或者仿函数来传递) 
Alloc|通过空间配置器来申请底层空间，不需要用户传递，除非用户不想使用标准库提供的空间配置器





构造函数

![image-20240820212643432](STL%20map%E3%80%81set%E3%80%81multi_map%E3%80%81multi_set.assets/image-20240820212643432.png)



迭代器
|函数声明 | 功能介绍|
|---|---|
begin()和end() |begin:首元素的位置，end最后一个元素的下一个位置 
cbegin()和cend() |与begin和end意义相同，但cbegin和cend所指向的元素不能修改
rbegin()和rend() |反向迭代器，rbegin在end位置，rend在begin位置，其++和--操作与begin和end操作移动相反
crbegin()和crend() |与rbegin和rend位置相同，操作相同，但crbegin和crend所指向的元素不能修改



map的容量与元素访问
|函数声明 | 功能介绍|
|---|---|
|bool empty ( ) const | 检测map中的元素是否为空，是返回true，否则返回false|
|size_type size() const| 返回map中有效元素的个数|
|mapped_type& operator[] (const key_type& k) |返回去key对应的value,不存在则默认构造后插入|
|mapped_type& at (const key_type& k); |返回去key对应的value,不存在则抛异常|

> 在元素访问时，有一个与operator[]类似的操作at()(该函数不常用)函数，都是通过key找到与key对应的value然后返回其引用，不同的是：当key不存在时，operator[]用默认value与key构造键值对然后插入，返回该默认value,at()函数则是直接抛异常。



map中元素的操作
|函数声明 | 功能介绍|
|---|---|
pair<iterator,bool> insert ( const value_type& x )|在map中插入键值对x注意x是一个键值对，返回值也是键值对：iterator代表新插入元素的位置，bool代表释放插入成功
void erase ( iterator position ) |删除position位置上的元素 
size_type erase ( const key_type& x )| 删除键值为x的元素
void erase ( iterator ﬁrst, iterator last )| 删除[ﬁrst, last)区间中的元素
void swap ( map<Key,T,Compare,Allocator>& mp )|交换两个map中的元素
void clear ( ) |将map中的元素清空
iterator ﬁnd ( const key_type& x )|在map中插入key为x的元素，找到返回该元素的位置的迭代器，否则返回end
const_iterator ﬁnd ( const key_type& x ) const|在map中插入key为x的元素，找到返回该元素的位置的const迭代器，否则返回cend
size_type count ( const key_type& x ) const|返回key为x的键值在map中的个数，注意map中key是唯一的，因此该函数的返回值要么为0，要么为1，因此也可以用该函数来检测一个key是否在map中

> 当key已存在时，insert插入失败
>
> [] 支持 查找，插入，修改



【总结】

>1. map中的的元素是键值对
>2. map中的key是唯一的，并且不能修改
>3. 默认按照小于的方式对key进行比较
>4. map中的元素如果用迭代器去遍历，可以得到一个有序的序列
>5. map的底层为平衡搜索树(红黑树)，查找效率比较高$O(log_2 N)$ 
>6. 支持[]操作符，operator[]中实际进行插入查找。





## multiset

### 描述

[multiset文档](https://legacy.cplusplus.com/reference/set/multiset/)

> [翻译]：
>
> 1. multiset是按照特定顺序存储元素的容器，其中元素是可以重复的。
> 2. 在multiset中，元素的value也会识别它(因为multiset中本身存储的就是<value, value>组成
>    的键值对，因此value本身就是key，key就是value，类型为T). multiset元素的值不能在容器 
>    中进行修改(因为元素总是const的)，但可以从容器中插入或删除。
> 3. 在内部，multiset中的元素总是按照其内部比较规则(类型比较)所指示的特定严格弱排序准则
>    进行排序。
> 4. multiset容器通过key访问单个元素的速度通常比unordered_multiset容器慢，但当使用迭
>    代器遍历时会得到一个有序序列。
> 5. multiset底层结构为二叉搜索树(红黑树)。
>
> 
>
> 补充：
>
> 1. multiset中再底层中存储的是<value, value>的键值对
> 2. mtltiset的插入接口中只需要插入即可
> 3. 与set的区别是，multiset中的元素可以重复，set是中value是唯一的 
> 4. 使用迭代器对multiset中的元素进行遍历，可以得到有序的序列
> 5. multiset中的元素不能修改
> 6. 在multiset中找某个元素，时间复杂度为$O(log_2 N)$ 
> 7. multiset的作用：可以对元素进行排序



### multiset简单使用

```
#include <set> 
void TestSet()
{
  int array[] = { 2, 1, 3, 9, 6, 0, 5, 8, 4, 7 }; 
    
     // 注意：multiset在底层实际存储的是<int, int>的键值对
     multiset<int> s(array, array + sizeof(array)/sizeof(array[0])); 
     for (auto& e : s)
         cout << e << " ";
     cout << endl;
     return 0;
}
```



【注意】

multiset中的find，规定找**中序的第一个**





## multimap

### 描述

[multimap文档 ](https://legacy.cplusplus.com/reference/map/multimap/)

>翻译：
>
>1. Multimaps是关联式容器，它按照特定的顺序，存储由key和value映射成的键值对<key, 
>     value>，其中多个键值对之间的key是可以重复的。
>
>2. 在multimap中，通常按照key排序和惟一地标识元素，而映射的value存储与key关联的内
>     容。key和value的类型可能不同，通过multimap内部的成员类型value_type组合在一起， 
>     value_type是组合key和value的键值对:
>     typedef pair<const Key, T> value_type;
>
>3. 在内部，multimap中的元素总是通过其内部比较对象，按照指定的特定严格弱排序标准对
>     key进行排序的。
>
>4. multimap通过key访问单个元素的速度通常比unordered_multimap容器慢，但是使用迭代
>     器直接遍历multimap中的元素可以得到关于key有序的序列。 
>
>5. multimap在底层用二叉搜索树(红黑树)来实现。
>
>   multimap和map的唯一不同就是：map中的key是唯一的，而multimap中key是可以重复的。
>multimap中的接口可以参考map，功能都是类似的。 
> 
> 
> 
>注意：
>
>1. multimap中的key是可以重复的。
>2. multimap中的元素默认将key按照小于来比较
>3. multimap中没有重载operator[]操作，（因为key-value不再是唯一）
>4. 使用时与map包含的头文件相同：



## 底层结构 

map/multimap/set/multiset这几个容器有个共同点是：其底层都是按照二叉搜索树来实现的，但是二叉搜索树有其自身的缺陷，假如往树中插入的元素有序或者接近有序，二叉搜索树就会退化成单支树，时间复杂度会退化成O(N)，因此map、set等关联式容器的底层结构是对二叉树进行了平衡处理，即采用平衡树来实现。