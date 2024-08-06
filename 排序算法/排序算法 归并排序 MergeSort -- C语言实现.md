# 归并排序

归并排序（Merge sort）是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。

作为一种典型的分而治之思想的算法应用，归并排序的实现由两种方法：

- 自上而下的递归（所有递归的方法都可以用迭代重写，所以就有了第 2 种方法）；
- 自下而上的迭代；



## 用途

速度仅次于快速排序，为稳定排序算法，一般用于对总体无序，但是各子项相对有序的数列.

也常用于外排序实现.



### 算法步骤

1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列；
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置；
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置；
4. 重复步骤 3 直到某一指针达到序列尾；
5. 将另一序列剩下的所有元素直接复制到合并序列尾。



### 递归实现

```
//递归归并排序分三步
/*
* 1、递归
* 2、合并
* 3、拷贝
*/

/*  时间复杂度分析
归并算法中比较耗时的是归并操作，也就是把两个子数组合并为大数组
* 每一层都要归并n个数，一共logN层，所以O(N*logN)

*/

void _MergeSort(int *a , int begin , int end ,int *tmp)
{
	//返回条件:只有一个的时候返回
	if (begin >= end)
	{	
		return;
	}

	//区间
	int mid = (begin + end) / 2;

	//类后序遍历,递归让子区间有序
	_MergeSort(a, begin, mid , tmp);//另一种时mid+1
	_MergeSort(a, mid+1, end , tmp);

	//合并
	int begin1 = begin; int begin2 = mid + 1; //左右数组最小下标（升序要从小开始比较）
	int end1 = mid;     int end2 = end;
	int i = begin; //tmp起始位置
	while (begin1 <= end1 && begin2 <= end2)
	{
		if (a[begin1] < a[begin2])
		{
			tmp[i] = a[begin1++];
		}
		else
		{
			tmp[i] = a[begin2++];
		}
		i++;
	}

	while (begin1 <= end1)
	{
		tmp[i++] = a[begin1++];
	}

	while (begin2 <= end2)
	{
		tmp[i++] = a[begin2++];
	}

	//拷贝回去原数组
	/*
	1、一个一个交换
	2、字符函数mem系列
	*/

	memcpy(a + begin, tmp + begin, sizeof(int)*(end - begin + 1));

}

void MergeSort(int*a, int begin1)
{
	int *tmp = (int *)malloc( begin1 * sizeof(int));
	if (!tmp)
	{
		perror("malloc fail");
		exit(1);
	}
	_MergeSort(a, 0, begin1 - 1, tmp);
	free(tmp);
}

//当递归到只有一个数时，就相当于有序了，一个即有序，所以直接返回
```



### 非递归实现

```
void MergeSortNonR(int *a, int n)
{
	//range = 范围、区间。layer = 层数

	//  第一个结束下标是第二个起始下标-1    
	//  每个起始位置可以由循环次数控制 ----		设一个循环次数i  ----  要循环多少次？
	//	结束条件是：range == n
	//	下一轮区间变大可以由层数控制   ----  设一个层数控制N
	//	合并时需要两组 ,两组区间一轮循环---- 设两组起始和结束下标
	int *tmp = (int*)malloc(n*sizeof(int));
	if (!tmp)
	{
		perror("malloc fail");
		exit(-1);
	}

	int begin1 = 0;		int begin2 = 0;
	int end1 = 0;		int end2  = 0;
	//int i = 0;
	int rangeN = 1;//一个begin到end 的范围

	while (rangeN < n)
	{
		int j = 0;
		//控制变量让其进入下一轮
		
		for (int i = 0; 2 * rangeN * i < n; i++)
		{   //范围：2、4、8、16 --- 2^rangeN  == range=range*2
			//[0,1][2,3][4,5]...到[0,1,2,3][4,5,6,7][8,9,10,11]
			begin1 =  2 * rangeN * i;			// 0 ,2 ,4   /   0 ,4 ,8   /  0 ， 8 ，16 ， 24  /
			begin2 = begin1 + rangeN ;		// 1 ,3 ,5   /   2 ,6 ,10  /  4 ,  12, 20  , 28
			end1 = begin1 + rangeN - 1;
			end2 = begin2 + rangeN - 1;
			

			//修正
			if (end1 >= n-1)
			{
				end1 = n - 1;
				// 不存在区间
				begin2 = n;		//随便写
				end2 = n - 1;//小于begin2就行
			}
			//else if (end1 == n-1)
			//{
			//	// 不存在区间
			//	begin2 = n;
			//	end2 = n - 1;
			//}
			else if (end2 > n-1) //必须要if，否则就是end1 < n-1 => 可能出现end2 也小于n-1
			{
				end2 = n - 1;
			}

			//归并
			/*
			1、一组一组拷贝：比较一组完拷贝到tmp后，就拷贝回原数组，
			2、一轮一轮拷贝：比较完一轮所有组后，再拷贝回原数组
			*/
			j = 2 * rangeN * i;//起始位置
			while (begin1 <= end1 && begin2 <= end2)
			{
				if (a[begin1] < a[begin2])
				{
					tmp[j] = a[begin1++];
				}
				else
				{
					tmp[j] = a[begin2++];
				}
				j++;
			}

			while (begin1 <= end1)
			{
				tmp[j++] = a[begin1++];  //只能一个一个拷，或memcpy
			}

			while (begin2 <= end2)
			{
				tmp[j++] = a[begin2++];
			}
			//归并完一组就拷贝回一组
			//memcpy(a + 2 * rangeN * i, tmp + 2 * rangeN * i, (end2 - 2 * rangeN * i + 1) * sizeof(int));

		}
		memcpy(a, tmp, (n)*sizeof(int));
		rangeN *= 2;
	}
}
```

