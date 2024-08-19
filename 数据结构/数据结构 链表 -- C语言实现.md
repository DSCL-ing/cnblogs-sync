[toc]

# 链表 

## 链表的概念

链表是一种物理存储结构上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的指针链接次序实现的 。



## 链表的分类

实际中链表的结构非常多样，以下情况组合起来就有8种链表结构：
1. 单向或者双向

![image-20240805205154867](%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%20%E9%93%BE%E8%A1%A8%20--%20C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240805205154867.png)

2. 带头或者不带头

![image-20240805205209467](%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%20%E9%93%BE%E8%A1%A8%20--%20C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240805205209467.png)

3. 循环或者非循环

![image-20240805205222718](%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%20%E9%93%BE%E8%A1%A8%20--%20C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240805205222718.png)

虽然有这么多的链表的结构，但是我们实际中最常用还是两种结构：

1. 无头单向非循环链表：结构简单，一般不会单独用来存数据。实际中更多是作为其他数据结构的子结构，如哈希桶、图的邻接表等等。
2. 带头双向循环链表：结构最复杂，一般用在单独存储数据。实际中使用的链表数据结构，都
   是带头双向循环链表。另外这个结构虽然结构复杂，但是使用代码实现以后会发现结构会带 
   来很多优势，实现反而简单了。

掌握了这两种链表,其他的就容易推导出来了;

![image-20240805205233380](%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%20%E9%93%BE%E8%A1%A8%20--%20C%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0.assets/image-20240805205233380.png)

## 代码实现

### 无头单向非循环链表

#### SingleLink.h

```
#define _CRT_SECURE_NO_WARNINGS 1

#include<stdio.h>
#include<stdlib.h>
#include<assert.h>

typedef int SLDataType;

typedef struct SListNode//SingleList
{
	SLDataType data;
	struct SListNode*next;//自己定义自己类型的指针

}SListNode;


//SListNode* phead = NULL;//非哨兵卫，只是起始，调用插入函数后变成表头

// 要改变传过来的指向第一个节点的指针就传二级
// 不改变传过来的指向第一个节点的指针就传一级
// 只读的函数接口传一级

//创建节点
//开辟空间、返回地址：销毁变量，但地址有效
SListNode * CreateSListNode(SLDataType x);

//---------------------------一级指针----------//

//打印链表元素
void PrintSList(SListNode *phead);

//计算链表大小
int SizeSList(SListNode *phead);

//查找
SListNode *FindSList(SListNode *phead, SLDataType x);

//----------------------------------------------//


//---------------------------二级指针----------//

//尾插
//判断是否空表:
//是空表->调用CreateNode；
//非空->找尾插入
void PushBackSList(SListNode **phead, SLDataType x);


//头插
//不用判断
void PushFrontSList(SListNode **pphead,SLDataType x);


//尾删
void PopBackSList(SListNode **pphead);


//头删
void PopFrontSList(SListNode **pphead);


//释放空间
void DestroySListNode(SListNode**phead);

//----------------------------------------------//


/*
单链表前插必须要知前节点
*/


//pos位置前插x
//需要配合Find使用
void InsertSList(SListNode **pphead, SListNode *pos, SLDataType x);


//pos位置后插x;
//需要配合Find使用;
void InsertAfterSList(SListNode **pphead, SListNode *pos, SLDataType x);


//擦除
void EraseSList(SListNode **pphead, SListNode *pos);


void SListEraseAfter(SListNode* pos);

SListNode* FindElement(SListNode*L, SLDataType x);
```



#### SingleLink.c

```
#define _CRT_SECURE_NO_WARNINGS 1

#include"SingleLinkList.h"

//断言目的：防传参出错

SListNode* CreateSListNode(SLDataType x)
{

	SListNode* node = (SListNode*)malloc(sizeof(SListNode));//注意不要sizeof(...*),犯病，后面会free错误，其他bug不明显
	if (node == NULL)
	{
		perror("malloc fail\n ");
		exit(-1);
	}

	node->data = x;
	node->next=NULL;//初始化为空
	return node;
}


void PrintSList(SListNode *phead)
{
	SListNode *cur = phead;//current:当前  ->记忆当前的节点地址
	while (cur != NULL)
	{
		printf("%d->", cur->data);
		cur = cur->next;
	}
	printf("NULL\n");
}

int SizeSList(SListNode *phead)
{
	SListNode*cur = phead;
	int size = 0;
	while (cur)
	{
		size++;
		cur = cur->next;
	}
	return size;
}

SListNode *FindSList(SListNode *phead, SLDataType x)
{
	SListNode *cur = phead;
	while (cur)
	{
		if (cur->data == x)
		{
			return cur;
		}
		else
		{
			cur = cur->next;
		}
	}
	return NULL;
}

SListNode* FindElement(SListNode*L, SLDataType x)
{
	if (!L)
	{
		return NULL;
	}
	if (L->data == x)
	{
		return L;
	}
	   return FindElement(L->next, x);
}



//pphead=&phead  //目的要改变phead的值
//*pphead==phead
//**pphead==结构体变量
void PushBackSList(SListNode** pphead, SLDataType x)
{
	//为什么要有这一步，不能合并的原因：当head==NULL，tail->next对NULL解引用，错误
	if (*pphead == NULL)
	{
		SListNode* newnode = CreateSListNode(x);
		*pphead = newnode;//将newnode的值（尾节点的地址）赋给指针变量head
	}
	else
	{
		SListNode* tail = *pphead;//用于寻找、记忆尾节点的指针变量
		while (tail->next != NULL)
		{
			tail = tail->next;//得到下个节点的地址
		}
		SListNode *newnode = CreateSListNode(x);
		tail->next = newnode;//连接上尾节点，将newnode的值（尾节点的地址）赋给指针域
	}
}


void PushFrontSList(SListNode **pphead, SLDataType x)
{
	SListNode *newnode = CreateSListNode(x);
	newnode->next = *pphead;//将新节点链接在头节点前。
	*pphead = newnode;//将newnode的值（首节点的地址）赋给head，使head成为首节点。
}

void PopBackSList(SListNode **pphead)
{
	//1.头空
	//2.只有头
	//3.有多个
	if (*pphead == NULL)//判断空表
	{
		return;
	}

	if ((*pphead)->next == NULL)//不能合并，因为找尾节点需要对next解引用
	{
		*pphead = NULL;
		free(*pphead);//释放头节点
		return;//可以不用return，换成if-else
	}

	SListNode *tail, *prev;//previous
	tail = prev = *pphead;
	while (tail->next != NULL)//找尾节点
	{
		prev = tail;  //先记录当前节点
		tail = tail->next;//再到下个节点
	}
	free(tail);//释放尾节点
	prev->next = NULL;
}

void PopFrontSList(SListNode **pphead)
{
	//1.头空
	//2.只有头
	//3.有多个
	if (*pphead == NULL)
	{
		return;
	}

	//-------可以合并，将next放到头指针
	//if ((*pphead)->next == NULL)
	//{
	//	*pphead = NULL;
	//	free(*pphead);//释放头节点
	//	//return;//可以不用return，换成if-else
	//}
	//else
	//{



	//--方法一
		SListNode *cur = *pphead;//记录下头节点地址
		*pphead = (*pphead)->next;//丢掉头节点：将下一个节点放到头指针
		free(cur);//释放空间
	//--方法二
	//SListNode* next = (*pphead)->next;//记录下后一个节点
	//free(*pphead);//释放掉头节点
	//(*pphead) = next;//将下一个节点放到头指针
}


void InsertSList(SListNode **pphead, SListNode *pos, SLDataType x)
{
	SListNode *newnode = CreateSListNode(x);
	SListNode *prev;
	prev = *pphead;
	if (pos == *pphead)
	{
		//you can also call the Head insertion function  :你也可以使用头插函数
		newnode->next = *pphead;
		*pphead = newnode;
	}
	else
	{
		//--------方法一
		while (prev->next)
		{
			if (prev->next == pos)
			{
				newnode->next = prev->next;
				prev->next = newnode;
				return;
			}
			prev = prev->next;
		}
		printf("Insert position not find\n");

		//--------方法二
		/*
		while(prev-> != pos)
		{
			prev=prev->next;
		}
		newnode->next = prev->next;
		prev->next = newnode;
		*/
	}
}


void InsertAfterSList(SListNode **pphead, SListNode *pos, SLDataType x)
{
	SListNode *newnode = CreateSListNode(x);

	//方法三:
	newnode->next = pos->next;
	pos->next = newnode;

	//方法一
	//	SListNode *cur = *pphead;
	//	while (cur)
	//	{
	//		if (cur == pos)
	//		{
	//			newnode->next = cur->next;
	//			cur->next = newnode;
	//			return ;
	//		}
	//		cur = cur->next;
	//	}
	//	printf("InsertAfter fail\n");

	/*方法二
	while(cur!=pos)
	{
	cur=cur->next;
	}
	newnode->next=cur->next;
	cur->next=newnode;
	*/

}

void EraseSList(SListNode **pphead, SListNode *pos)
{
	SListNode *prev = *pphead;
	if (*pphead == pos)
	{
		*pphead = prev->next;
		free(prev);
	}
	while (prev->next != pos)
	{
		prev = prev->next;
	}
	prev->next = prev->next->next;
	free(pos);
	pos = NULL;//没用，但是习惯
}

void SListEraseAfter(SListNode* pos)
{
	assert(pos);
	assert(pos->next);

	SListNode* next = pos->next;
	pos->next = next->next;

	free(next);
	next = NULL;
}

void DesrtoySList(SListNode **pphead)
{
	SListNode *cur = *pphead;
	SListNode *next = cur->next;
	while (cur)
	{
		next = cur->next;
		free(cur);
		cur = next;
	}
	*pphead = NULL;//指针变量要置空
}


```



### 带头双向循环链表

#### DoubleLink.h

```
#define _CRT_SECURE_NO_WARNINGS 1

#include<stdio.h>
#include<stdlib.h>
#include<stdlib.h>
#include<stdbool.h>
#include<assert.h>

#pragma once

//有头双向循环链表
//结构复杂，操作反而简单

typedef int ListDataType;

typedef struct ListNode
{
	struct ListNode *prev;
	ListDataType data;
	struct ListNode *next;
}ListNode;


void Print(ListNode *phead);

//void ListNode(ListNode **pphead);  //改变头地址---不太合适了
ListNode *InitList(void);          //返回值，返回哨兵卫

//保持接口一致性，用一级指针



//创建新节点
ListNode *CreateListNode(ListDataType x);


//初始化
//创建哨兵卫
//初始化成员
ListNode *InitList(void);


//尾插
void PushBackList(ListNode *phead, ListDataType x);


//头插
void PushFrontList(ListNode *phead, ListDataType x);


//尾删
void PopBackList(ListNode *phead);


//头插
void PopFrontList(ListNode *phead);


ListNode *FindList(ListNode *phead, ListDataType x);


//--------------------带头双向循环链表好处
//pos就是所在节点的地址，不用头指针phead
void InsertList(ListNode *pos, ListDataType x);//用不到phead
//Insert和Erase 效率很高，直接插直接删，不用查找，且包含头尾插删
void EraseList(ListNode *pos);//用不到phead


//销毁
void DestroyList(ListNode *phead);

size_t ListSize(ListNode *phead);

```

#### DoubleLink.c

```
#define _CRT_SECURE_NO_WARNINGS 1


#include"DoubleList.h"

void Print(ListNode *phead)
{
	assert(phead);
	ListNode *cur = phead->next;
	while (cur != phead)
	{
		printf("%d ", cur->data);
		cur = cur->next;
	}
	printf("\n");
}

//void ListNode(ListNode *phead)


ListNode *InitList(void)
{
	//ListNode *prev, *tail;
	//哨兵卫头节点
	//哨兵卫的特点是不存储数据，由此，可以不初始化，随机值都行
	ListNode *phead = (ListNode*)malloc(sizeof(ListNode));
	if (phead == NULL)
	{
		printf("malloc fail\n ");
		exit(-1);
	}
	phead->prev = phead;
	phead->next = phead;
	return phead;
}

ListNode *CreateListNode(ListDataType x)
{
	ListNode *newnode = (ListNode*)malloc(sizeof(ListNode));
	if (newnode == NULL)
	{
		perror("malloc fail\n");
		exit(-1);
	}
	newnode->data = x;
	newnode->next = NULL;
	newnode->prev = NULL;
	return newnode;
}


void PushBackList(ListNode *phead, ListDataType x)
{
	ListNode *newnode = CreateListNode(x);
	ListNode *tail = phead->prev;//这样定义更加灵活，可以互换位置
	tail->next = newnode;
	newnode->prev = tail;
	phead->prev = newnode;
	newnode->next = phead;

	//phead->prev->next = newnode;//尾节点指向新节点
	//newnode->prev = phead->prev;//新节点指向尾节点
	//newnode->next = phead;//新节点指向头节点
	//phead->prev = newnode;//头节点指向新节点


	//Insert(phead, x);
}

void PushFrontList(ListNode *phead,ListDataType x)
{
	ListNode *newnode = CreateListNode(x);
	ListNode *next = phead->next;//这样定义更加灵活，可以互换位置
	newnode->next = next;//下个节点的位置给新节点
	next->prev = newnode;//新节点地址给下节点
	phead->next = newnode;
	newnode->prev = phead;


	//Insert(phead->next, x);
}

void PopBackList(ListNode *phead)
{
	ListNode *tail = phead->prev;
	ListNode *tailPrev = tail->prev;
	tailPrev->next = phead;
	phead->prev = tailPrev;
	free(tail);
	tail = NULL;
	//EraseList(phead->prev);
}

void PopFrontList(ListNode *phead)
{
	ListNode *next = phead->next;
	ListNode *nextNext = next->next;
	free(next);
	phead->next = nextNext;
	nextNext->prev = phead;
	next = NULL;
	//EraseList(phead->next);
}


ListNode *FindList(ListNode *phead, ListDataType x)
{
	assert(phead);
	ListNode *pos = phead->next;
	while (pos!=phead)
	{
		if (pos->next = x)
		{
			return pos;
		}
		pos = pos->next;
	}
	printf("not find");
	return NULL;
	
}


void InsertList(ListNode *pos,ListDataType x)//用不到phead
{
	ListNode *newnode = CreateListNode(x);
	ListNode *prev = pos->prev;
	prev->next = newnode;
	newnode->prev = prev;
	newnode->next = pos;
	pos->prev = newnode;
}

void EraseList(ListNode *pos)
{
	ListNode *posPrev = pos->prev;
	ListNode *posNext = pos->next;
	free(pos);
	pos = NULL;
	posPrev->next = posNext;
	posNext->prev = posNext;
}

void DestroyList(ListNode *phead)
{
	assert(phead);
	ListNode *cur = phead->next;
	ListNode *next = NULL;
	while (cur)
	{
		if (cur != phead)
		{
			next = cur->next;
			free(cur);
			cur = next;
		}
	}
	free(phead);
}
//head = NULL;//在外界置空


bool ListEmpty(ListNode *phead)
{
	assert(phead);
	return phead->next == phead;
}

size_t ListSize(ListNode *phead)
{

	assert(phead);
	size_t n = 0;
	ListNode *cur = phead->next;
	while (cur!=phead )
	{
		n++;
		cur = cur->next;
	}
	return n;
}

```



#### 