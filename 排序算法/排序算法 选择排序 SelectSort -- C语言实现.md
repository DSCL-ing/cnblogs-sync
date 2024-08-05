## 选择排序

### 描述

选择排序是一种简单直观的排序算法，无论什么数据进去都是 O(n²) 的时间复杂度。所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了。

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
		//循环一轮 
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
		//找到后交换，[mini]要到begin位置
	Swap(&a[begin], &a[mini]);
	if (maxi == begin)  //如果maxi刚好处于begin，mini回到begin后，maxi被交换到原来mini的位置，需修正
	{
		maxi = mini;
	}
	Swap(&a[end], &a[maxi]);
	//最后再缩小范围
	begin++;
	end--;
	}
}

/*每一轮比较从begin+1开始 ， end+1结束（要和end比）*/
```

