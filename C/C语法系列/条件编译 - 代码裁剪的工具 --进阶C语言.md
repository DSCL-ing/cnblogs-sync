[toc]

### 条件编译 - 代码裁剪的工具

#### 为何要有条件编译

条件编译主要是用于代码裁剪,通过代码裁剪,能够快速实现某种目的,如版本维护(free版本,pro版本等,功能裁剪,跨平台性等.

<br>

#### 条件编译都在那些地方用？

> 举个例子
>
> 我们经常听说过,某某版程序是完全版/精简版,某某版应用是商用版/校园版,某某软件是基础版/扩展版等。
>
> 其实这些软件在公司内部都是同一个项目，是多个源文件构成的。所以，所谓的不同版本，其实就是那些功能的有无；在技术层面上，公司为了好维护，可以维护多种版本；如果是使用条件编译，想使用哪个版本，就使用哪种条件进行裁剪就行。
>
> 如著名的Linux内核，功能上也是使用条件编译进行功能裁剪的，来满足不同平台的软件。

#### 见一见条件编译的代码

```
int main()
{
#ifndef DEBUG
    printf("hello debug\n");
#elif RELEASE
    printf("hello release\n");
#else 
	printf("hello unknow\n");
#endif
    return 0;
}
```



<br>

#### 宏是否被定义 vs 宏是否为真or假

```
#define DEBUG    // 宏被定义

#define DEBUG 1  // 宏被定义,且值为真

#define DEBUG 0  // 宏被定义,且值为假
```

宏为真假是在宏被定义之上的.

<br>

#### 编译器也能够自动帮你加上宏

##### GCC

```
语法:gcc 源文件  -D 宏=值
#   gcc test.c -D MACRO=1 
```

##### VS2023-VS2019

![image-20240508153946278](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508153946278.png)

![image-20240508154007078](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508154007078.png)

在vs平台上用的不多.

<br><br>

#### #ifdef/#ifndef 

\#ifdef/#ifndef用于检测宏是否被定义,有没有值,是真是假不重要

\#ifdef 检测宏是否已经定义,是则保留,否则裁剪;#ifndef则相反

用法举例:

![image-20240507171846307](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240507171846307.png)

\#ifdef/#ifndef一般只在头文件中使用

<br>

#### #if

\#if的默认用法和#ifdef有一定区别,其他用法差不多,#if使用更频繁.

区别是#if如果定义了宏则要求必须要有值,没定义则当作假或者else.

<br>

##### 注意事项

使用#if或#ifdef时,很容易会忘记写#endif.因为我们平常写if-else没有这个end,很容易会类比忘记掉#endif.所以在使用条件编译时,先把#if - #endif写上,后面就不再容易遗漏了.



<br><br>



#### 让#if和#ifdef/#ifndef完全一样

\#if模拟#ifdef:

```
#define MACRO
int main()
{
#if defined(MACRO)
	puts("MACRO defined!");
#else
	puts("MACRO undefined!");
#endif
	return 0;
}
```

程序运行结果:

![image-20240508160951829](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508160951829.png)

如果是未定义呢? 没有别的名词,加个逻辑反就好啦

![image-20240508170933763](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508170933763.png)

<br>

#### 条件编译也支持嵌套

```
#include<stdio.h>
#include<math.h>

#define C    
#define CPP    

int main()     
{             
#if defined(C)    
    #if defined(CPP)    
        puts("hello CPP");    
    #endif                  
  puts("hello C");    
#else                 
  puts("hello other");    
#endif                    
  return 0;    
}  
```

![image-20240508173148156](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508173148156.png)

注释掉`#define C`后

![image-20240508173245666](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508173245666.png)

可以证明,条件编译是支持嵌套的.

不过,使用嵌套的代码阅读体验是比较差的,一般不建议使用嵌套,下面还有其他更好的代码写法推荐.

<br><br>

#### 一个使用#if defined能起到很好优化的用法

> [引用]([C语言#if defined高级用法-CSDN博客](https://blog.csdn.net/lyy901135/article/details/100688824))

在一个需要完成“多个宏定义来共同控制同一代码分支”的情况下,例如

- TEST_1 或 TEST_2被定义，则选择执行1，2

```
#ifndef TEST_1
#define TEST_1
#endif

#ifdef TEST_1
	puts("1");
#else
	#ifdef TEST_2
		puts("1");
	#else
		puts("2");
	#endif
#endif
```

- 或者， TEST_1和TEST_2均未定义，则选择执行1，否则执行2 

```
#ifndef TEST_1
	puts("1");
#else
	#ifndef TEST_2
		puts("1");
	#else
		puts("2");
	#endif
#endif
```

这样的代码看起来是比较冗余的,不好阅读,因为#ifdef是没有对应的"else if",我们只能采用这样的方式写.对比到一般使用的if-else,**if()内可以是一个表达式**,那#ifdef能否也能将宏定义组织成表达式呢?

 看一下代码

```
#ifdef TEST_1 || TEST_2
	puts("1");
#else
	puts("2");
#endif
```

这样的代码看起来是更简洁,更优雅.但它是错误的.

![image-20240508163628082](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508163628082.png)

**因为ifdef和ifndef仅能跟一个宏定义参数，而不能使用表达式**。

<br>

虽然在vs下可以运行

![image-20240508163859587](%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91%20-%20%E4%BB%A3%E7%A0%81%E8%A3%81%E5%89%AA%E7%9A%84%E5%B7%A5%E5%85%B7%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/image-20240508163859587.png)

但是我们不推荐这样不能跨平台的代码.

<br>

因为#if需要判断真假而具有计算表达式的功能,

因此,使用`#if defined` 和 `#if !defined`是**更好的选择**.

- TEST_1 或 TEST_2被定义，则选择执行1，否则执行2

```
#if defined TEST_1 || defined TEST_2
	puts("1");
#else
	puts("2");
#endif
```

- TEST_1 或 TEST_2未被定义，则选择执行1，否则执行2

```
#if !defined TEST_1 || !defined TEST_2
	puts("1");
#else
	puts("2");
#endif
```

<br>

<br>