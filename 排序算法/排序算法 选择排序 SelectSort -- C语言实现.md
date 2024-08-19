[toc]



## 直接选择排序

### 描述

选择排序是一种简单直观的排序算法，无论什么数据进去都是 O(n²) 的时间复杂度。所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了。



### 选择排序的缺点：

1. **效率低下**：选择排序的时间复杂度始终是 𝑂(𝑛2)，无论最好、平均还是最坏情况。这使得它在处理大量数据时效率极低。
2. **非稳定排序**：选择排序是不稳定的排序算法，即相等的元素在排序后可能会改变原有的相对顺序。
3. **大量的比较操作**：在每一轮迭代中，选择排序都需要遍历未排序的部分以寻找最小（或最大）元素，这导致了大量的比较操作。



### 算法步骤

首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置。

再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。

重复第二步，直到所有元素均排序完毕。

### 代码实现

```
/*交换函数*/
static void Swap(int *p1, int *p2)
{
	int tmp = 0;
	tmp = *p1;
	*p1 = *p2;
	*p2 = tmp;
}


void SelectSort(int *a, int n)
{
	int begin = 0; int end = n - 1;
	int maxi = begin; int mini = begin;
	while (begin < end)
	{
		for (int i = begin + 1; i <=end; i++) //找最大最小数 的下标
		{
			if (a[i] < a[mini])
			{
				mini = i;
			}
			if (a[i] > a[maxi])
			{
				maxi = i;
			}
		}
		
	Swap(&a[begin], &a[mini]);
	if (maxi == begin)  //maxi刚好处于begin时,需修正
	{
		maxi = mini;
	}
	Swap(&a[end], &a[maxi]);
	begin++;
	end--;
	}
}

```

