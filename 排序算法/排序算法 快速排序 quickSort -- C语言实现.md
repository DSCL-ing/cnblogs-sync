## 快速排序



快速排序是由东尼·霍尔所发展的一种排序算法。在平均状况下，排序 n 个项目要 Ο(nlogn) 次比较。在最坏状况下则需要 Ο(n2) 次比较，但这种状况并不常见。事实上，快速排序通常明显比其他 Ο(nlogn) 算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。

快速排序使用分治法（Divide and conquer）策略来把一个串行（list）分为两个子串行（sub-lists）。

快速排序又是一种分而治之思想在排序算法上的典型应用。本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法

快速排序和冒泡排序同属于**交换排序**

> *快速排序的最坏运行情况是 O(n²)，比如说顺序数列的快排。但它的平摊期望时间是 O(nlogn)，且 O(nlogn) 记号中隐含的常数因子很小，比复杂度稳定等于 O(nlogn) 的归并排序要小很多。所以，对绝大多数顺序性较弱的随机数列而言，快速排序总是优于归并排序。*
>
> [引用参考]([1.6 快速排序 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/quick-sort-2.html))





### 快速排序要解决的几个问题

我们知道快速排序的优点在于,数据是随机时排序速度最快.但最简单的快排在一些极端场景下反而很慢,即时间复杂度退化.后面将逐步解决这些问题,先枚举出来:

1. 正序
2. 逆序
3. 大量重复数据



### 算法步骤

1. 从数列中挑出一个元素，称为 "基准"（pivot）;
2. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
3. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序；



### 递归法空间复杂度: log~2~(n)





### 1. hoare法

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



#### 时间复杂度分析

理想情况下:

![image-20240813162228146](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F%20quickSort%20--%20C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240813162228146.png)

最坏情况:

当数组为顺序或逆序时,即每次选的key都是最小或最大时,快排会退化成O(n^2^).

因为这样没有起到分而治之的作用,从树的角度看就是一棵只有单边生长的二叉树,高度可能达到n.





#### 代码实现1

```
void hoare(int* a, int begin, int end) {

    //一个元素或没有元素时,递归结束
    if (begin >= end) {
        return;
    }
		
    int left = begin;		//递归区间起始
    int right = end;		//递归区间末尾
    int keyi = left;    //keyi == key_index == key的下标

    while (left < right) {
        while (left < right && a[right] >= a[keyi]) { //要找小于key的,大于等于都要走,不然执行不了函数体(下标移动),导致死循环
            right--;  //不能合并,要保证下标指向的是找到的那个值
        }
        while (left < right && a[left] <= a[keyi]) {
            left++;
        }
        Swap(&a[left], &a[right]);
    }

    Swap(&a[left], &a[keyi]);
    keyi = left;

    //区间划分成: [begin,keyi-1]  keyi  [keyi+1,end]
    hoare(a, begin, keyi - 1);
    hoare(a, keyi + 1, end);
}
```



#### 优化一 三数取中

由于一般的快排在**遇到key值为最大或最小时会退化成O(n^2^)**,为此需要让key尽可能不是最大或最小.通常的做法是进行三数取中.

三数取中:即将数组的开始元素,数组的中间元素和数组的末尾元素三个数进行比较,**选出值为中间,不大不小的那个数**交换到最左边(默认)作为key值.

这样做的好处是:

1. 如果是顺序或逆序的情况,则key一定是处于中间大小;
2. 如果是随机序列,则key值是三个数中不大不小的那一个,也能略微增加分支效率.

缺点:

1. 随机情况下,依旧有可能选择到边缘数(key比较大或比较小). 因此还有别的方法如随机数选择等...
2. 逆序情况下,

##### 代码实现

```
void getMidIndex(int *a, int begin,  int end){
	int mid = (begin+end)/2;
	if(a[begin]>a[mid])
	{
		if(a[mid]>a[end])	return mid;
		else if(a[begin]>a[end]) return end;
		else return begin;
	}
	else
	{
		if(a[begin]>a[end]) return begin;
		else if(a[min]>a[end]) return end;
		else return mid;
	}
}
```

##### 逻辑测试

测试原理:三数取中一共有3个变量(begin,mid,end),每个变量有3种属性(>,=,<),总共有9种情况.

经穷举测试无误

```
int main() {
    int a[] = { 5,1,8,4,2,7,5,9,6,4 };
    std::cout<<getMidIndex(a,0,9);
    return 0;
}
```





##### 三数取中优化版本快排

优化后时间复杂度几乎能稳定在O(N*log~2~N).

```

void hoare(int* a, int begin, int end) {

    //一个元素或没有元素时,递归结束
    if (begin >= end) {
        return;
    }
		
		
    int left = begin;
    int right = end;
    int keyi = left; //keyi == key index == key是下标

    while (left < right) {
        while (left < right && a[right] >= a[keyi]) { //要找小于key的,大于等于都要走,不然执行不了函数体(下标移动),导致死循环
            right--;  //不能合并,要保证下标指向的是找到的那个值
        }
        while (left < right && a[left] <= a[keyi]) {
            left++;
        }
        Swap(&a[left], &a[right]);
    }

    Swap(&a[left], &a[keyi]);
    keyi = left;

    //区间划分成: [begin,keyi-1]  keyi  [keyi+1,end]
    hoare(a, begin, keyi - 1);
    hoare(a, keyi + 1, end);
}
```



#### 优化二 小区间优化

当快排在递归到一定层次后,拆分得到很多子数组,且子数组又很小.此时如果继续对小数组进行拆分,则需要很多次(二叉树越深子树越多,最后一层占一半,),很多次就意味着要申请多次栈帧,以及其他因递归带来的开销(虽说现代编译器对递归优化很好);另一方面,当递归到深层时,往往子数组也比较小了,对小型数据堆,显然能够有更好的方案去处理.因此可以针对性进行优化:

根据插入排序的特性,在小型数据集(n<10或n<16)时插入排序的适应性非常强,特别高效.因此小区间优化可以选择插入排序取代.(大约减少80％的递归调用)

如在内省排序(std::sort采用的算法)中,当数组大小小于16时,就替换成插入排序进行排序.



##### 代码实现

```
void hoare_small(int* a, int begin, int end) {

    //一个元素或没有元素时,递归结束
    if (begin >= end) {
        return;
    }
    if (end - begin + 1 < 16) {
        InsertSort(a + begin, end - begin + 1);
    }
    else {
        int mid = getMidIndex(a, begin, end);
        Swap(&a[begin], &a[mid]);

        int left = begin;
        int right = end;
        int keyi = left; //keyi == key index == key是下标

        while (left < right) {
            while (left < right && a[right] >= a[keyi]) {  //要找小于key的,大于等于都要走,不然执行不了函数体(下标移动),导致死循环
                right--;  //不能合并,要保证下标指向的是找到的那个值
            }
            while (left < right && a[left] <= a[keyi]) {
                left++;
            }
            Swap(&a[left], &a[right]);
        }

        Swap(&a[left], &a[keyi]);
        keyi = left;

        // [begin,keyi-1] keyi [keyi+1,end]
        hoare_small(a, begin, keyi - 1);
        hoare_small(a, keyi + 1, end);
    }
}
```



##### 测试

一亿数据下(9成随机数)测试结果,测试用例下文提供

![image-20240813221657101](%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%20%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F%20quickSort%20--%20C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240813221657101.png)

可见小区间优化能够带来一定的性能提升.

>  std::sort是内观排序,是混合排序(快排,堆排,插入),综合性能比较稳定





### 2. 挖坑法

挖坑法是在hoare法的基础上,主要是改进优化实现思路,使其更容易实现,思想并无太大区别

与hoare的区别:

- hoare法key值参与排序,而挖坑法key值被保存起来,不参与排序.
- hoare法是交换方式,而挖坑法因为多了key值的空间,采用的是覆盖的方式

- 挖坑法相对于hoare法代码逻辑更容易实现.
- 挖坑法性能比hoare略胜一筹:hoare是交换,挖坑法是直接覆盖,节约了一点性能

具体方式:将基准值key保存.依旧从右边开始找小;区别开始,当第一次右边找到小后,就直接向key位置写入/覆盖,写完后,原来的小的位置就形成一个"坑",然后再左边找大,找到大后向上一步右边第一次找到小的位置写入/覆盖,即写到坑中,然后自己原来的位置就形成新的"坑",循环过程数组始终只有一个坑位存在.如此循环下去.



### 3. 前后指针法

性能较差,用于给3路划分铺垫

```
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





### 4. 三路排序算法



#### 背景

虽然三数取中能有效解决正序退化情况,但是重复数据较多情况下并没有太多优化,依旧会退化成O(n^2^).

因此提出三路并排算法

三鹿并排算法综合性能较好,能够应对大部分极端情况

#### 概念

三路快速排序是双路快速排序的进一步改进版本，三路排序算法把排序的数据分为三部分，分别为小于 v，等于 v，大于 v，v 为标定值，这样三部分的数据中，等于 v 的数据在下次递归中不再需要排序，小于 v 和大于 v 的数据也不会出现某一个特别多的情况），通过此方式三路快速排序算法的性能更优。

#### 适用说明

时间和空间复杂度同随机化快速排序。

三路快速排序算法是使用三路划分策略对数组进行划分，对处理大量重复元素的数组非常有效提高快速排序的过程。它添加处理等于划分元素值的逻辑，将所有等于划分元素的值集中在一起。



#### 实现思路

- 定义三个变量 
  	left = begin,
  	cur = begin+1,
  	right

- 根据三种情况判断执行

  1. cur<key:
     	swap([left++],[cur++]); 

  2. cur==key:
     	cur++

  3. cur>key:

     ​	swap([light],[cur]); //cur只交换不加价,
     ​	light--;

- 最终,left和right是key相同的一段区间,因此三路划分完成
  [begin , left-1] [left , right] [right , end]

##### 代码实现

```
static void Swap(int *p1, int *p2)
{
	int tmp = 0;
	tmp = *p1;
	*p1 = *p2;
	*p2 = tmp;
}



//[begin , left-1] [left , right] [right , end]
static void partitionQSort(int *a, int begin, int end)  
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



### 缺陷 -- 逆序

虽然三数取中+三路并排能够解决正序与重复数的问题,但逆序问题依然存在.

测试中发现,优化过的挖坑法适应性最强,随机情况下性能甚至比VS下std::sort更强.且在有少量随机数的较为逆序中性能也较为卓越.







### 测试代码

见测试工具篇

