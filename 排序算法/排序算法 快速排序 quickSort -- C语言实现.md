## 快速排序



快速排序是由东尼·霍尔所发展的一种排序算法。在平均状况下，排序 n 个项目要 Ο(nlogn) 次比较。在最坏状况下则需要 Ο(n2) 次比较，但这种状况并不常见。事实上，快速排序通常明显比其他 Ο(nlogn) 算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。

快速排序使用分治法（Divide and conquer）策略来把一个串行（list）分为两个子串行（sub-lists）。

快速排序又是一种分而治之思想在排序算法上的典型应用。本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法



> *快速排序的最坏运行情况是 O(n²)，比如说顺序数列的快排。但它的平摊期望时间是 O(nlogn)，且 O(nlogn) 记号中隐含的常数因子很小，比复杂度稳定等于 O(nlogn) 的归并排序要小很多。所以，对绝大多数顺序性较弱的随机数列而言，快速排序总是优于归并排序。*
>
> [引用参考]([1.6 快速排序 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/quick-sort-2.html))



### 算法步骤

1. 从数列中挑出一个元素，称为 "基准"（pivot）;
2. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
3. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序；



### hoare法

#### 基本原理

1. 选一个基准,以升序为例,选择最左边的为基准

2. 遍历,定义左右指针,左指针初值与基准相同,右指针指向最右

   右指针先从最右边开始往左找比基准小的,左指针再从最左边开始往右找比基准大的,都找到时则交换.

3. 当左右指针相遇时,相遇位置与基准交换.



循环/遍历一次的效果(核心思想)

1. 分割出左右区间,左区间比key小,右区间比key大 (大于或等于)

2. key落到了正确的位置(最终排序好的位置)



如何做到:

选最左为基准,则必须从最右开始找;选最右为基准,则必须从最左开始找



原因:

根据hoare法的核心思想,要让key落到正确的位置,就必须要让相遇位置的元素小于或等于key.

hoare法相遇有两种情况,逐个分析证明:

1. L停住,R走着遇到L:

   - 刚开始时是R先走,遇到小停住,L后走,遇到大停住,然后交换.交换后L一定是小于基准.之后下一轮如果R没找到小,一直走到L相遇,因为L在上一轮结束时就是小于基准的,因此此时相遇点一定小于基准.
   - 刚开始时R一直没找到小的,直接走到L(基准位置),说明基准本身就是最小,落到了正确的位置.

2. R停住,L走着遇到R:

   只有一种情况:R先走,R一定是遇到小于基准时停住,然后L再走,无论L怎么走,与R相遇时都一定是小于基准值

   - 刚开始R先走,遇到小停住;L开始走,没找到大,直接走到R,R一定是比基准小.
   - 刚开始R先走,找到小;L先走,找到大,交换,进入下一轮,之后也一定相同.

因此,相遇位置一定是小于等于基准值.



#### 代码实现

```

```









### 三路排序算法

#### 概念

三路快速排序是双路快速排序的进一步改进版本，三路排序算法把排序的数据分为三部分，分别为小于 v，等于 v，大于 v，v 为标定值，这样三部分的数据中，等于 v 的数据在下次递归中不再需要排序，小于 v 和大于 v 的数据也不会出现某一个特别多的情况），通过此方式三路快速排序算法的性能更优。

#### 适用说明

时间和空间复杂度同随机化快速排序。

三路快速排序算法是使用三路划分策略对数组进行划分，对处理大量重复元素的数组非常有效提高快速排序的过程。它添加处理等于划分元素值的逻辑，将所有等于划分元素的值集中在一起。

```
static void Swap(int *p1, int *p2)
{
	int tmp = 0;
	tmp = *p1;
	*p1 = *p2;
	*p2 = tmp;
}


//直接插入排序
static void InsertSort(int* a, int n)
{
	for (int i = 0; i < n - 1; i++)  //n个数，只需排n-1个，（因为第一个必定有序--默认）
	{
		int end = i;  //下标 ， 已排序数组【0 - end】
		int tmp = a[end + 1];  //元素交换用 ， 数组往后覆盖挪动
		//一个元素排序，排一个(先假想出一堆乱序数组)
		while (end >= 0)
		{
			if (a[end] > tmp)
			{
				a[end + 1] = a[end]; //往后挪动
				end--;   //比下一个元素
			}
			else
			{
				break; //插入
			}
		}
		a[end + 1] = tmp; //插在end后面
	}
}

/*三数取中，消除有序影响*/
static int getMidIndex(int *a, int begin, int end)
{
	int mid = (begin + end) / 2;
	if (a[begin] > a[mid])
	{
		if (a[begin] < a[end])
		{
			return begin;
		}
		else if (a[mid] > a[end])
		{
			return mid;
		}
		else
		{
			return end;
		}
	}
	else
	{
		if (a[mid] < a[end])
		{
			return mid;
		}
		else if (a[begin] > a[end])
		{
			return begin;
		}
		else
		{
			return end;
		}
	}
}


static void partitionQSort(int *a, int begin, int end)  //[begin , left-1] [left , right] [right , end]
{
	if (begin >= end)
	{
		return;
	}
	int mid = getMidIndex(a, begin, end);
	Swap(&a[begin], &a[mid]);

	int left = begin; int right = end;
	int cur = begin + 1;
	int key = a[left];

	while (cur <= right)
	{
		if (a[cur] < key)
		{
			Swap(&a[cur++], &a[left++]);//a[left]永远小于key
		}
		else if (a[cur] == key)
		{
			cur++;
		}
		else
		{
			Swap(&a[cur], &a[right--]);
		}
		/*
		* 如果是大于，则c不动，只控制right往前走，原因是不知道啊a[right]大小，再走就会出问题。
		解决方法：交换a[right--]后保持cur不动，让循环进入下一轮，比较新的a[cur]和key，
		在新一轮的循环中如果上一轮从right换过来的a[cur]比key大就丢回去，如果小于等于就按命令走
		这样就能保证	左边<key ,中间 == key ,右边>key
		*/
	}

	partitionQSort(a, begin, left - 1);
	partitionQSort(a, right + 1, end);

}
```



### 挖坑法与前后指针法

```
/*交换函数*/
static void Swap(int *p1, int *p2)
{
	int tmp = 0;
	tmp = *p1;
	*p1 = *p2;
	*p2 = tmp;
}

/*三数取中，消除有序影响*/
static int getMidIndex(int *a, int begin, int end)
{
	int mid = (begin + end) / 2;
	if (a[begin] > a[mid])
	{
		if (a[begin] < a[end])
		{
			return begin;
		}
		else if (a[mid] > a[end])
		{
			return mid;
		}
		else
		{
			return end;
		}
	}
	else
	{
		if (a[mid] < a[end])
		{
			return mid;
		}
		else if (a[begin]>a[end])
		{
			return begin;
		}
		else
		{
			return end;
		}
	}
}


int _HoareQuickSort(int *a, int begin , int end)
{

	int mid = getMidIndex(a , begin , end);//mid是中间索引（中间值的下标）
	Swap(&a[begin], &a[mid]); //获取到中间值的下标后对换begin和mid对应的值，使key对应的是中间值，消除有序

	int left = begin; int right = end;
	int keyi = left;

	while (left < right)
	{
		while (left < right && a[right] >= a[keyi])
		{
			right--;
		}

		while (left < right && a[left] <= a[keyi])
		{
			left++;
		}

		Swap(&a[left], &a[right]);
	}
	Swap(&a[keyi], &a[right]);
	keyi = right;

	return keyi;

}

int _HoleQuickSort(int *a, int begin, int end)
{

	int mid = getMidIndex(a, begin, end);//mid是中间索引（中间值的下标）
	Swap(&a[begin], &a[mid]); //获取到中间值的下标后对换begin和mid对应的值，使key对应的是中间值，消除有序

	int left = begin; int right = end;
	int key = a[left];

	int hole = left;//第一个坑给begin'
	while (left < right)
	{
		while (left < right && key <= a[right])
		{
			right--;
		}
		a[hole] = a[right];
		hole = right;

		while (left < right && key >= a[left])
		{
			left++;
		}
		a[hole] = a[left];
		hole = left;
	}
	a[hole] = key;
	return hole;

	//_HoleQuickSort(a, begin, hole - 1);
	//_HoleQuickSort(a, hole + 1, end);
}

int _PointQuickSort(int *a, int begin, int end)
{

	int mid = getMidIndex(a, begin, end);//mid是中间索引（中间值的下标）
	Swap(&a[begin], &a[mid]); //获取到中间值的下标后对换begin和mid对应的值，使key对应的是中间值，消除有序

	int prev = begin; int cur = begin+1;
	int keyi = begin;
	while (cur <= end)
	{
		if (a[cur] < a[keyi] && ++prev != cur) //后一个条件是用于限制指针相同情况下进行交换，因为交换有代价，减少时间
		{										//加不加等号都行,最好不加,少交换减少消耗
			//++prev;
			Swap(&a[prev], &a[cur]);
		} 
		++cur;
	}
	Swap(&a[keyi], &a[prev]);
	keyi = prev;
	return keyi;
}

/*三路“划分partition分割”*/  // ----  独立使用，不能返回key了，因为分成了两个区间
void partitionQSort(int *a, int begin, int end)  //[begin , left-1] [left , right] [right , end]
{
	if (begin >= end)
	{
		return;
	}
	int mid = getMidIndex(a, begin, end);
	Swap(&a[begin], &a[mid]);

	int left = begin; int right = end;
	int cur = begin+1;
	int key = a[left];

	while (cur <= right)
	{
		if (a[cur] < key)
		{
			Swap(&a[cur++], &a[left++]);//a[left]永远小于key
		}
		else if (a[cur] == key)
		{
			cur++;
		}
		else			
		{				
			Swap(&a[cur], &a[right--]); 
		}
/*
* 如果是大于，则c不动，只控制right往前走，原因是不知道啊a[right]大小，再走就会出问题。
解决方法：交换a[right--]后保持cur不动，让循环进入下一轮，比较新的a[cur]和key，
在新一轮的循环中如果上一轮从right换过来的a[cur]比key大就丢回去，如果小于等于就按命令走
这样就能保证	左边<key ,中间 == key ,右边>key
*/
	}



	
	QuickSort(a, begin, left - 1);
	QuickSort(a, right+1, end);

}


void QuickSort(int *a, int begin, int end)
{
	if (begin >= end)
	{
		return;
	}


	if ((end - begin + 1) < 15) //为什么15？，最后一层为1个，其次2，倒数第三层4.... , 1+2+4<15 即最后三层
	{							//最后三层最好，多少都会慢---前提是递归没有递归优化，不然相差不大
		InsertSort(a + begin, end - begin + 1);
	}
	else
	{
		int keyi = _PointQuickSort(a, begin, end);
		QuickSort(a, begin, keyi - 1);
		QuickSort(a, keyi + 1, end);
	}
}
```



### 非递归版本

```
/*交换函数*/
static void Swap(int *p1, int *p2)
{
	int tmp = 0;
	tmp = *p1;
	*p1 = *p2;
	*p2 = tmp;
}

/*三数取中，消除有序影响*/
static int getMidIndex(int *a, int begin, int end)
{
	int mid = (begin + end) / 2;
	if (a[begin] > a[mid])
	{
		if (a[begin] < a[end])
		{
			return begin;
		}
		else if (a[mid] > a[end])
		{
			return mid;
		}
		else
		{
			return end;
		}
	}
	else
	{
		if (a[mid] < a[end])
		{
			return mid;
		}
		else if (a[begin] > a[end])
		{
			return begin;
		}
		else
		{
			return end;
		}
	}
}


//总思路：1、先走完左。2、走到底后，通过出栈更新数据回到上级位置
void _NonRecursionQSort(int *a, int begin, int end)
{

	struct QSData QSData;
	Stack stack;
	StackInit(&stack);

	int left = begin; int right = end;
	int keyi = left;

	int flag = 0;
	//QSData.begin = begin;
	//QSData.end = end;
	//QSData.keyi = keyi;
	//StackPush(&stack, QSData);

	while (flag==0 || !StackEmpty(&stack))
	{
		flag = 1;
		if (begin >= end)        //返回后用出栈数据
		{
			QSData = StackTop(&stack);
			StackPop(&stack);
			begin = QSData.begin; end = QSData.end;
			left = QSData.begin; right = QSData.end;

		}
		if (begin>=end) //出栈更新数据后是否符合，不符合再次重开判断或出栈
			{
				continue;
			}

		int mid = getMidIndex(a, begin, end);//mid是中间索引（中间值的下标）
		Swap(&a[begin], &a[mid]); //获取到中间值的下标后对换begin和mid对应的值，使key对应的是中间值，消除有序
		keyi = left;

		while (left < right)
		{
			while (left < right && a[right] >= a[keyi])
			{
				right--;
			}

			while (left < right && a[left] <= a[keyi])
			{
				left++;
			}

			Swap(&a[left], &a[right]);
		}
		//更新下一轮左的数据前，先把右的数据存起来（入栈）
		Swap(&a[keyi], &a[right]);
		keyi = right;
		QSData.begin = keyi + 1;
		QSData.end = end;
		StackPush(&stack, QSData);
		//更新数据，进入下一轮循环
		begin = begin;
		end = keyi - 1;
		left = begin; right = end;
	}
	StackDestroy(&stack);
}


/*完全模拟递归*/ //--- 把栈DataType改成int,一次连续出入begin和end代码就简化了，再加把核心代码用函数
void _NonRecursionQSort1(int *a, int begin, int end)
{
	//初始化栈
	Stack stack;
	StackInit(&stack);

	//插入第一个数据
	struct QSData QSData;
	QSData.begin = begin; QSData.end = end;
	StackPush(&stack, QSData);


	while (!StackEmpty(&stack))
	{
		
		QSData = StackTop(&stack);
		StackPop(&stack);
		int begin = QSData.begin; int end = QSData.end;

		int mid = getMidIndex(a, begin , end);//mid是中间索引（中间值的下标）
		Swap(&a[begin], &a[mid]); //获取到中间值的下标后对换begin和mid对应的值，使key对应的是中间值，消除有序
		int keyi = begin;
		int left = begin; int right = end;

		/*递归方法中选一*/
		while (left < right)
		{	
			begin = QSData.begin; end = QSData.end;
			while (left < right && a[right] >= a[keyi])
			{
				right--;
			}

			while (left < right && a[left] <= a[keyi])
			{
				left++;
			}

			Swap(&a[left], &a[right]);
		}
		Swap(&a[keyi], &a[right]);
		keyi = right;

		//相当于递归不满足条件就return
		if (keyi + 1 < end)
		{
			QSData.begin = keyi + 1; QSData.end = end;
			StackPush(&stack, QSData);
		}

		if (begin < keyi - 1)
		{
			QSData.begin = begin; QSData.end = keyi - 1;
			StackPush(&stack, QSData);
		}


	}

	StackDestroy(&stack);


}

```





#### 为什么不使用非递归实现

>
>
>尽管非递归版本的归并排序和快速排序在某些情况下具有优势，但在 C++ Standard Template Library (STL) 和 Introsort 中通常不使用非递归版本的原因有几个方面：
>
>### 1. **递归实现的简洁性和可读性**：
>   - 递归实现通常更简洁，更容易理解和编写。
>   - 递归代码往往更接近算法的核心思想，使得代码更易于维护和调试。
>
>### 2. **现代编译器优化**：
>   - 现代编译器（如 GCC 和 Clang）通常能够对递归调用进行优化，比如尾递归优化，这可以减少递归调用栈的开销。
>   - 编译器的优化能力意味着递归版本的性能损失通常是可以接受的，甚至有时可以达到与非递归版本相当的性能。
>
>### 3. **通用性和适应性**：
>   - 递归实现更容易适应不同的输入和数据结构。
>   - 非递归实现可能需要更多的手动管理，如栈操作，这增加了实现的复杂性。
>
>### 4. **内存使用**：
>   - 对于现代计算机系统，递归调用栈的开销通常不是瓶颈。
>   - 递归实现的额外内存使用通常不会成为问题，除非处理非常大规模的数据集。
>
>### 5. **性能评估**：
>   - 在实践中，递归版本的性能通常与非递归版本相近，特别是在使用现代编译器时。
>   - 在大多数情况下，递归版本提供的性能已经足够优秀，不需要额外的优化。
>
>### 6. **实现难度**：
>   - 非递归版本可能需要更复杂的逻辑来模拟递归过程，这可能会引入错误并增加实现的复杂性。
>   - 递归版本的代码通常更简洁，更容易实现无误。
>
>### 7. **标准库设计原则**：
>   - C++ STL 的设计注重简洁性、可靠性和性能。
>   - 在大多数情况下，递归版本已经足够满足这些设计目标。
>
>### 8. **历史因素**：
>   - STL 和其他标准库的开发始于递归版本更为普遍的时代。
>   - 随着时间的推移，虽然非递归版本可能在某些方面有所改进，但由于上述原因，递归版本仍然是首选。
>
>### 9. **兼容性和稳定性**：
>   - 由于递归版本已经被广泛使用并且经过长时间的测试，它们在兼容性和稳定性方面通常表现更好。
>
>### 10. **性能特征**：
>   - 对于快速排序而言，递归版本可以更容易地实现优化，例如尾递归优化和内联阈值等。
>   - 非递归版本可能需要额外的逻辑来处理这些优化。
>
>### 11. **递归深度**：
>   - 对于 Introsort，递归深度的检测和控制更为直接，有助于避免快速排序的最坏情况。
>   - 非递归版本可能需要更复杂的逻辑来实现类似的控制。
>
>### 总结
>
>尽管非递归版本在某些情况下可能提供更好的性能或资源管理，但递归版本通常更简洁、易于理解和维护，并且在大多数情况下已经足够高效。此外，现代编译器的优化能力通常可以弥补递归版本的任何潜在缺点。因此，STL 和 Introsort 通常使用递归版本的归并排序和快速排序。
