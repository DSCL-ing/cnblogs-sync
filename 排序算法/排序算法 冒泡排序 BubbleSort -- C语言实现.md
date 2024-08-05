# 冒泡排序

## 描述

冒泡排序（Bubble Sort）也是一种简单直观的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢"浮"到数列的顶端。

### 什么时候最快

当输入的数据已经是正序时

### 什么时候最慢

当输入的数据是反序时



## 代码实现

```
特点:有序时效率高，实用性不强

//原理
/*
与下标后一个比较，符合交换，不符下一轮
*/

/*交换函数*/
static void Swap(int *p1, int *p2)
{
	int tmp = 0;
	tmp = *p1;
	*p1 = *p2;
	*p2 = tmp;
}

void BubbleSort(int *a, int n)
{

	int end = n - 1;
	while (end>0)
	{
	int exchange = 0;
		for (int i = 0; i < end; i++)
		{
			if (a[i] > a[i + 1])
			{
				Swap(&a[i], &a[i + 1]);
				exchange = 1;  //交换
			}
		}
		if (exchange == 0)
		{
			break;
		}
		end--;
	}

}

```

