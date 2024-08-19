[toc]

# 基数排序



基数排序（radix sort）属于“分配式排序”（distribution sort），又称“桶子法”（bucket sort）或bin sort，顾名思义，它是透过键值的部份资讯，将要排序的元素分配至某些“桶”中，藉以达到排序的作用，基数排序法是属于稳定性的排序，其时间复杂度为O (nlog(r)m)，其中r为所采取的基数，而m为堆数，在某些时候，基数排序法的效率高于其它的稳定性排序法。



## 实现原理

基数排序的发明可以追溯到1887年赫尔曼·何乐礼在打孔卡片制表机(Tabulation Machine)上的贡献。它是这样实现的：将所有待比较数值（正整数）统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。

基数排序的方式可以采用LSD（Least significant digital）或MSD（Most significant digital），LSD的排序方式由键值的最右边开始，而MSD则相反，由键值的最左边开始。



>‌**LSD和MSD是基数排序中的两种不同排序顺序，分别代表Least Significant Digital（最低有效位优先）和Most Significant Digital（最高有效位优先）。**‌
>
>- **LSD（Least Significant Digital）**‌：LSD从数值的最右边（低位）开始排序。这种排序方式从数据的最低位开始处理，逐步向高位进行，适用于位数较多的数据排序，因为它从较低位开始，可以更快地处理大量数据。
>
>- **MSD（Most Significant Digital）**‌：MSD则从数值的最左边（高位）开始排序。与LSD相反，MSD从数据的最高位开始处理，逐步向低位进行。这种方式适用于位数较少但高位有较大差异的数据排序。



## 基本步骤

以LSD为例，对一串数据`278 109 063 930 589 184 505 269 008 083`进行基数确定,最高位是百位,故分发趟数为3.是十进制数据,即基数为0~9,

开始分发:

1. 第一趟分发:

   ![image-20240817194614925](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%20RadixSort%20--C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240817194614925.png)回收:

   ![image-20240817194714553](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%20RadixSort%20--C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240817194714553.png)

2. 第二趟分发:

   ![image-20240817194751433](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%20RadixSort%20--C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240817194751433.png)

   回收:

   ![image-20240817194822076](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%20RadixSort%20--C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240817194822076.png)

3. 第三趟分发:

   ![image-20240817194853172](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%20RadixSort%20--C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240817194853172.png)

   回收:

   ![image-20240817194910589](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%20RadixSort%20--C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240817194910589.png)

4. 排序完成

过程分析:

第一趟分发回收使个位有序;

第二趟分发回收使十位有序;(在个位有序的前提下,使十位有序)

第三趟分发回收使百位有序;(在个位和十位有序的前提下,使百位有序)



## 效率分析

时间效率：设待排序列为n个记录，d个关键码，关键码的取值范围为radix，则进行链式基数排序的时间复杂度为O(d(n+radix))，其中，一趟分配时间复杂度为O(n)，一趟收集时间复杂度为O(radix)，共进行d趟分配和收集。 空间效率：需要2*radix个指向队列的辅助空间，以及用于静态链表的n个指针。



## 代码实现

分发回收过程符合先进先出方式,因此可以使用队列.



### sort

```
std::queue<int> q[10]; //0~9一共10个基数,需要10个队列(先进先出)

//获取对应位的值
int GetDigit(int value, int k) {
    int tmp;
    while (k-- >= 0) {
        tmp = value % 10;
        value /= 10;
    }
    return tmp;
}

//获取最大位数
int getNumberOfDigits(int number) {
    if (number == 0) return 1;  // 特殊情况：0 的位数为 1
    if (number < 0) number = -number;  // 转换为正数
    return (int)log10(number) + 1; //log后取整+1就能得到位数
}

//分发
void Distribute(int *a,int size, int k) {
    for (int i = 0 ; i < size; ++i) {
        int digit = GetDigit(a[i],k);
        q[digit].push(a[i]);
    }

}

//收集
void Collect(int *a, int size) {
    //回收:考回原数组
    int j = 0;
    for (int i = 0; i < size;i++) {
        while (!q[i].empty()) {
            a[j++] = q[i].front();
            q[i].pop();
        }
    }
}

void RadixSort(int* a, int size) {
    int k = 3; //扩展:getMaxNum:获取最大的数,计算有多少位
    //int k = getNumberOfDigits(999); //k=3
    for (int i = 0; i < k; i++) {
        Distribute(a, size, i);
        Collect(a, size);
    }
}
```



### main

```
    int a[] = {278,109,63,930,589,184,505,269,8,83}; //自定义数组
    RadixSort(a,10);
    PrintArray(a,10);
```

![image-20240817220610622](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%20RadixSort%20--C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240817220610622.png)