[toc]

## 堆排序

堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。堆排序可以说是一种利用堆的概念来排序的选择排序。分为两种方法：

1. 大顶堆：每个节点的值都大于或等于其子节点的值，在堆排序算法中用于升序排列；
2. 小顶堆：每个节点的值都小于或等于其子节点的值，在堆排序算法中用于降序排列；



堆排序的平均时间复杂度为 Ο(nlogn)。



### 堆排序的好处：

1. **最坏情况性能保证**：
   - 堆排序的时间复杂度在最坏情况下也是 O(n log n)，这使得 Introsort 即使在处理最不利的输入数据时也能保持高效。
2. **简单性**：
   - 堆排序算法相对简单，易于实现，尤其是在 C++ 标准库中已经提供了现成的函数，如 `std::make_heap` 和 `std::sort_heap`。
3. **无需额外存储空间**：
   - 堆排序是原地排序算法，除了几个辅助变量外不需要额外的存储空间。这使得它适用于内存受限的环境。
4. **稳定性**：
   - 虽然堆排序本身不是稳定的排序算法，但在 Introsort 的上下文中，这并不是一个问题，因为快速排序本身也不稳定。

堆排序属于选择排序



### 算法步骤

1. 创建一个堆 H[0……n-1]；
2. 把堆首（最大值）和堆尾互换；
3. 把堆的尺寸缩小 1，并调用 shift_down(0)，目的是把新的数组顶端数据调整到相应位置；
4. 重复步骤 2，直到堆的尺寸为 1。



### 代码实现

```
void AdjustDown()

void HeapSort(int *a, int size)
{
	assert(a);
	
	// ---- 1. 向下调整算法建堆  ----
	//时间复杂度O(n)
	int parent = (size - 1 - 1) / 2;
	while (parent >= 0)
	{
		AdjustDown(a, size, parent);
		parent--;
	}

	
	// ---- 2. 选数 ----
	//时间复杂度O(n*logN),n个数都要高度次调整
	//int end = size - 1; //下标版本
	//元素个数版本,能够复用删除写法
    while (size > 1) //元素个数大于1
	{
		Swap(&a[0], &a[size - 1]);		//交换
		size--;
		AdjustDown(a, size, 0);  //调整堆 -- 注意,此处end为元素个数
	}

}
```



### 时间复杂度分析

```

/*
向下调整算法建堆时间复杂度计算
假设满二叉树树高度h
各层的节点数为
第一层 2 ^ 0               ------向下调整h-1次
第二层 2 ^ 1               ------向下调整h-2次
第三层 2 ^ 2               ------向下调整h-3次
...    ... 
第h - 1层  2 ^ (h - 2)     ------向下调整1次
第h层  2 ^ (h - 1)

向下调整算法建堆是从最小父亲开始，即第h-1层的最后一个节点 parent = (size-1-1)/2
最坏情况下所有节点需要执行的次数为
f(h) = 2^(h-2)*1 + 2^(h-3)*2 + ... + 2^1*(h-2) + 2^0*(h-1)    错位相减
2*f(h) = 2^(h-1)*1 + 2^(h-2)*2 + ... + 2^2*(h-2) + 2^1*(h-1)
作差、合并得f(h) = 2^h -h-1
其中 满二叉树节点数N = 2^h-1，即h = log(N+1) 代入得
f(N) = N - 1 - log(N+1)  ， 舍去logN(数量级)
所以O(n) = n

-------------------------------------------------------------------------------
而向上调整算法建堆时间复杂度比较吃亏，见图
假设满二叉树树高度h
各层的节点数为
第一层 2 ^ 0               
第二层 2 ^ 1               ------向上调整1次
第三层 2 ^ 2               ------向上调整2次
...    ...
第h - 1层  2 ^ (h - 2)     ------向上调整h-2次
第h层  2 ^ (h - 1)         ------向上调整h-1次
计算方法还是错位相减，由图显然可发现向上调整算法执行次数数量级明显提高
不再计算
O(n) = n*logN



总结：向下调整算法跳过最下层最多节点层，且从下层开始节点多执行次数少。快
	向上调整算法从上开始从节点少往节点多执行次数成倍增加，前面的加起来都没最后一层多，慢
*/
```

