[TOC]

# 哈希



## 与红黑树性能对比

使用unorder系列前,先对比一下红黑树性能,就知道为什么C++11要搞出unorder系列了;

测试代码参考前篇红黑树,AVL树,以及排序算法测试方法.[test_code参考]( https://www.cnblogs.com/DSCL-ing/p/18365072)

- 随机数测试(一千万数据)

```
template<class T>
void t_insert(T& t, int* a, int size) {
    for (int i = 0; i < size; i++) {
        t.insert(a[i]);
    }
}

template<class T>
void t_find(T& t, int* a, int size) {
    for (int i = 0; i < size; i++) {
        t.find(a[i]);
    }
}

template<class T>
void t_erase(T& t, int* a, int size) {
    for (int i = 0; i < size; i++) {
        t.erase(a[i]);
    }
}


void Hash_test() {
    int size = 10000000;
    int* a1 = new int[size];
    RandomArray_Generator(a1, size);
    std::set<int> s;
    std::unordered_set<int> us;

    std::cout<<std::fixed;

    std::cout << "set cost: " << std::endl;
    Cost([&]() {std::cout << "\t insert: "; t_insert<std::set<int>>(s, a1, size);  });
    Cost([&]() {std::cout << "\t find: "; t_find<std::set<int>>(s, a1, size);  });
    Cost([&]() {std::cout << "\t erase: "; t_erase<std::set<int>>(s, a1, size);  });
    std::cout << "unordered_set cost: " << std::endl;
    Cost([&]() {std::cout << "\t insert: "; t_insert<std::unordered_set<int>>(us, a1, size);  });
    Cost([&]() {std::cout << "\t find: "; t_find<std::unordered_set<int>>(us, a1, size);  });
    Cost([&]() {std::cout << "\t erase: "; t_erase<std::unordered_set<int>>(us, a1, size);  });
}
```

![image-20240914164622845](STL%20%E5%93%88%E5%B8%8C%20unordered%E7%B3%BB%E5%88%97%E5%85%B3%E8%81%94%E5%BC%8F%E5%AE%B9%E5%99%A8.assets/image-20240914164622845.png)

可以发现在一千万随机数的情况下,unordered快了一倍多.显然unordered性能是非常好的.

- 重复数测试 (一亿数据)

![image-20240914165201731](STL%20%E5%93%88%E5%B8%8C%20unordered%E7%B3%BB%E5%88%97%E5%85%B3%E8%81%94%E5%BC%8F%E5%AE%B9%E5%99%A8.assets/image-20240914165201731.png)

- 有序(一千万数据)

![image-20240914165317351](STL%20%E5%93%88%E5%B8%8C%20unordered%E7%B3%BB%E5%88%97%E5%85%B3%E8%81%94%E5%BC%8F%E5%AE%B9%E5%99%A8.assets/image-20240914165317351.png)

> 在有序的情况下,性能略差.



因此,综合复杂场景的情况下.哈希更加实用.



## 认识哈希

hash是散列的意思,哈希表也可以叫散列表



### 与红黑树的区别

1.unordered遍历是无序的,RBT是有序的.
2.unordered系列的底层是哈希表
3.unordered是C++11更新的,RBT是C++98
4.unordered是单向迭代器,RBT是双向迭代器





## 常见哈希函数

  1. 直接定址法--(常用) --直接映射,适合数据范围比较集中,直观
     优点：简单、均匀
      缺点：需要事先知道关键字的分布情况
      使用场景：适合查找比较小且连续的情况 ,如数组
      特点:每个元素都有唯一的key

  2. 除留余数法--(常用) --适合数据分布较为分散,空间零碎等,将分散的值映射到有限或固定的区间里
     设散列表中允许的地址数为m，取一个不大于m，但最接近或者等于m的质数p作为除数，
      按照哈希函数：Hash(key) = key% p(p<=m),将关键码转换成哈希地址
      映射关系是模出来的
      缺点:可能会冲突,不同的值映射到了同一个位置(哈希冲突)
      注意:模数最好为素数,需要证明,使用素数冲突少一点



### 哈希冲突/哈希碰撞:

不同的值映射到了同一个位置

### 哈希冲突的解决:

   解决哈希冲突两种常见的方法是：闭散列和开散列

#### 闭散列

也叫开放定址法，当发生哈希冲突时，如果哈希表未被装满，说明在哈希表中必然还有空位置，那么可以把key存放到冲突位置中的“下一个” 空位置中去。

寻找空位置方法:

1. 线性探测 -- 简单暴力的遍历(问题很多,略)

  发生哈希冲突后进行线性探测：从发生冲突的位置开始，依次向后探测，（超出长度就从头开始找），直到寻找到下一个空位置为止。

```c
hashi = key % len;
//发生冲突后
hashi = hashi + i; (i = 0,1,2...);
```

  缺点:线性探测可能会有一些相邻聚集位置连续冲突,形成**拥堵、踩踏问题**出现

2. 二次探测(二次方探测)

 ` key+i^2(i为1,2,3,4...) `

  评价:一定程度上缓解踩踏问题,但不能解决

3. 还有类似的其他方法--属于设计哈希函数 --
   总结:总体而言,闭散列都不是很好,实际中使用比较少 , 本质还是一个零和游戏

#### 开散列

 --别名:哈希桶/拉链法/开链法...

 1. 开散列概念
 开散列法又叫链地址法(开链法)，首先对关键码集合用散列函数计算散列地址，具有相同地址的关键码归于同一子集合，每一个子集合称为一个桶，
  各个桶中的元素通过一个单链表链接起来，各链表的头结点存储在哈希表中。
  (桶排序也是类似原理,不过桶排序很垃圾,智只能用整型,不要学)



### 哈希表什么情况下进行扩容？如何扩容？

散列表的载荷因子/负载因子α -- 存储数据量的百分比 == 填入表中的元素个数/散列表的长度

 α越大,产生冲突可能性就越大

 早期各种库中
 对于开放定址法(闭散列,现在不用了),载荷因子限制在0.7-0.8.java为0.75,超过就resize





