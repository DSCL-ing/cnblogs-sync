[toc]

# 顺序表 

## 概念

顺序表是用一段物理地址连续的存储单元依次存储数据元素的线性结构，一般情况下采用数组存 
储。在数组上完成数据的增删查改。
顺序表一般可以分为：

1. 静态顺序表：使用定长数组存储元素。
1. 动态顺序表：使用动态开辟的数组存储。



## 代码实现 

### 动态顺序表

静态顺序表只适用于确定知道需要存多少数据的场景。静态顺序表的定长数组导致N定大了，空 
间开多了浪费，开少了不够用。所以现实中基本都是使用动态顺序表，根据需要动态的分配空间 
大小，下面实现动态顺序表。

#### SeqLish.h

```
#define _CRT_SECURE_NO_WARNINGS 1

#pragma once
#include<stdio.h>


typedef int SLDataType;

#define FORMAT "%d"  //格式

typedef struct SeqList
{
	SLDataType *a;
	int size;
	int capacity;
}SL;


//初始化
void InitSeqList(SL*ps);//

//打印
void Print(SL*ps);

//尾插
void PushBack(SL* ps,SLDataType x);

//头插
void PushFront(SL*ps, SLDataType x);

//尾删
void PopBack(SL*ps);

//头删
void PopFront(SL*ps);

//内存释放
void SLDestory(SL*ps);

//查找
int SLFind(SL*ps);

//插入
void SLInsert(SL*ps, int pos, SLDataType x);

```

#### SeqList.c

```
#define _CRT_SECURE_NO_WARNINGS 1

#include"SeqList.h"


void InitSeqList(SL*ps)
{
	ps->size = ps->capacity = 0;
	//ps->a = (SL*)realloc(ps->a, (ps->capacity)*sizeof(SL));
	ps->a = NULL;
}

void Print(SL*ps)
{
	int i = 0;
	while (i<=ps->size-1)
	{
		printf(FORMAT, ps->a[i++]);
	}
	printf("\n");
}


void checkcapacity(SL*ps)
{
	if (ps->size == ps->capacity)
	{
		//扩容二倍是个适中的方式
		int newcapacity = ps->capacity == 0 ? 4 : 2 * (ps->capacity);
		SLDataType *ptr = (SLDataType *)realloc(ps->a, newcapacity* sizeof(SLDataType));
		if (ptr == NULL)
		{
			perror("realloc fail");
			//exit(-1);
			return;
		}
		ps->a = ptr;
		ps->capacity = newcapacity;
	}
}

void PushBack(SL*ps, SLDataType x)
{
	checkcapacity(ps);
	ps->a[ps->size] = x;
	ps->size++;	
}


void PushFront(SL*ps, SLDataType x)
{
	checkcapacity(ps);
		int end = ps->size-1;
		for (; end >= 0;end--)
		{
			ps->a[end+1] = ps->a[end];
		}
		ps->a[0] = x;
		ps->size++;
}


void PopBack(SL*ps)
{
	if (ps->size > 0)
	{
		ps->size--;
	}
}


void PopFront(SL*ps)
{
	if (ps -> size >= 0)
	{
		int begin = 0;
		for (; begin < ps->size - 1; begin++)
		{
			ps->a[begin] = ps->a[begin + 1];
		}
	}
	ps->size--;
}

//内存释放
void SLDestory(SL*ps)
{
	free(ps->a);
	ps->a == NULL;
}

int SLFind(SL*ps)
{
	{
		SLDataType x = 0;
		scanf(FORMAT, &x);
		if (ps->size > 0)
		{
			int begin = 0;
			for (; begin <= ps->size - 1; begin++)
			{
				if (strcmp(ps->a[begin], x) == 0)
					return begin;
			}
			return -1;
		}
		
	}
}

//position:等级位置，定位（立场，高度）  location:位置
void SLInsert(SL*ps, int pos, SLDataType x)
{
	;
}

void SLErase(SL*ps, int pos)
{
	;
}
```



## 线性表区分

线性表（linear list）是n个具有相同特性的数据元素的有限序列。 线性表是一种在实际中广泛使 
用的数据结构，常见的线性表：顺序表、链表、栈、队列、字符串...
线性表在逻辑上是线性结构，也就说是连续的一条直线。但是在物理结构上并不一定是连续的， 
线性表在物理上存储时，通常以数组或链式结构的形式存储。