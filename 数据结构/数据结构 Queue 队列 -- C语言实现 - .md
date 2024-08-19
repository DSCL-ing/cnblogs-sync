[toc]

# 队列



## 队列的概念

队列：只允许在一端进行插入数据操作，在另一端进行删除数据操作的特殊线性表，队列具有先进先出的特点FIFO(First In First Out) 

- 入队：进行插入操作的一端称为队尾 
- 出队：进行删除操作的一端称为队头



## 链实栈代码实现

### Ququq.h

```
#pragma once
#define _CRT_SECURE_NO_WARNINGS 1
#include<stdio.h>
#include<stdlib.h>
#include<stdbool.h>
#include<assert.h>

typedef int QDataType;

typedef struct QueueNode
{
	struct QueueNode *next;
	QDataType data;
}QNode;

//控制变量结构体
//只有一个值，就不用定义结构体，有多个就定义结构题。

typedef struct Queue
{
	struct QueueNode *head; //队头，出队，头删
	struct QueueNode *tail; //队尾，入队，尾插
}Queue;
//是指针变量就传二级指针，是普通变量就传一级

void QueueInit(Queue *pq);
void QueueDestroy(Queue *pq);

void QueuePush(Queue *pq, QDataType x);
void QueuePop(Queue *pq);

QDataType QueueFront(Queue *pq);
QDataType QueueBack(Queue *pq);

int QueueSize(Queue *pq);
bool QueueEmpty(Queue *pq);

```

### Queue.c

```
#include "Queue.h"


void QueueInit(Queue *pq)
{
	assert(pq);
	pq->head = NULL;
	pq->tail = NULL;
	

}

void QueueDestroy(Queue* pq)
{
	assert(pq);
	QNode * next = NULL;
	QNode *cur = pq->head;
	while (cur)
	{
		next = pq->head->next;//放这里防止没节点时解引用。
		free(cur);
		cur = next;
	}
	pq->head=pq->tail = NULL;
}

void QueuePush(Queue *pq,QDataType x)
{
	assert(pq);
	QNode *newnode = (QNode*)malloc(sizeof(QNode));
	if (newnode == NULL)
	{
		perror("malloc fail ");
		exit(-1);
	}
	//先初始化再使用
	newnode->data = x;
	newnode->next = NULL;
	//单链表队列-尾插头删。
	if (pq->head == NULL)//头尾都行，判断一个就可以了 ---用头更好，不要用尾--用尾可能插不进，如果删完后尾没有置空的话
	{
		pq->head = pq->tail = newnode;
	}
	else                             //尾插
	{
		pq->tail->next = newnode; //队尾指向的节点链接上新节点
		pq->tail = newnode;      //队尾指向新节点
	}
}

void QueuePop(Queue *pq)
{
	assert(pq);
	assert(!QueueEmpty(pq));
	if (pq->head->next == NULL)//只有一个节点
	{
		free(pq->head);//先释放
		pq->tail = pq->head = NULL;//后置空 :tail 和 head都得置空，不然下次就插不进了 ---为什么要置空，因为push需要NULL识别空队列插入
	}
	else
	{
		QNode *next = pq->head->next;//记住下一个
		free(pq->head);//释放头节点
		 pq->head = next;//下个节点成为新节点 
	}
}

QDataType QueueFront(Queue *pq)
{
	assert(pq);
	assert(!QueueEmpty(pq));
	return pq->head->data;
}


QDataType QueueBack(Queue *pq)
{
	assert(pq);
	assert(!QueueEmpty(pq));
	return pq->tail->data;
}

int QueueSize(Queue *pq)
{
	assert(pq);
	QNode *cur = pq->head;
	int size = 0;
	while (cur)
	{
		size++;
		cur = cur->next;
	}
	return size;
}

bool QueueEmpty(Queue *pq)
{
	assert(pq);
	//return QueueSize(pq) == 0;
	return pq->head == NULL ;//只要有一个就可以了
	//head为空tail也为空
}

```



