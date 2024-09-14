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



