# C 库函数 - strcpy()

[toc]

## 描述

**char \*strcpy(char \*dest, const char \*src)** 把 **src** 所指向的字符串复制到 **dest**。

需要注意的是如果目标数组 dest 不够大，而源字符串的长度又太长，可能会造成缓冲溢出的情况。

## 声明

下面是 strcpy() 函数的声明。

```
char *strcpy(char *dest, const char *src)
```

## 参数

- **dest** -- 指向用于存储复制内容的目标数组。
- **src** -- 要复制的字符串。

## 返回值

该函数返回一个指向最终的目标字符串 dest 的指针。



## 模拟实现

### 1.0

```
void my_strcpy(char* dest, char* src)//版本一
{
	while (*src != '\0')
	{
		*dest = *src;
		src++;
		dest++;
	}
	*dest = *src;
}
```

### 2.0

```
// 满足递进关系与后置++，简化代码
//（缺点，指针解引用前无保护）
void my_strcpy(char* dest, char* src)
{
	while (*dest++ = *src++)  //'\0' 为假，控制循环
		;
}
```

### 3.0

```
//优化三:在解引用指针前，判断指针的有效性（必须）
//（虽防止出现野指针，但没提示出哪里出错）
void my_strcpy(char* dest, char* src)
{
	if (src != NULL&&dest != NULL)
	{
		while (*dest++ = *src++)
			;
	}
}
```

### 4.0

```
//编写代码时，我们总是会做出一些假设，断言就是用于在代码中捕捉这些假设，可以将断言看作是异常处理的一种高级形式。
#include<assert.h>
void my_strcpy(char* dest, char* src)//法四：断言法(在保护的基础上，告诉错误在哪)
{
	assert(dest != NULL);           //fail(失败，不成功)
	assert(src != NULL);
	if (src != NULL){
		while (*dest++ = *src++);
	}
}
```

### 5.0

```
//优化五：常变量修饰参数与返回值类型
//1、为防止下面表达式写错，用const限制*src不能被修改（防止*dest赋给*src）
//2、确保结果与目标相同，将返回目的地（结果）的地址，便于后续操作验证与使用
#include<assert.h>
char* my_strcpy(char* dest, const char* src)
{
	char* ret = dest;   //在开始前就保存好目的地地址，便于后续修改后可以找到起点
	assert(dest != NULL);           
	assert(src);//(NULL==0)      
	if (src != NULL){
		while (*dest++ = *src++);
	}
	return ret;
}


int	main()
{
	char arr1[] = "##############";
	char arr2[] = "hello";
	my_strcpy(arr1, NULL);            //此处错误还没改
	printf("%s\n", arr1);
	printf("%s\n", my_strcpy(arr1, NULL)); //最终版本 //函数的返回值作为另一个函数的参数（链式访问）
	return 0;
}
```

