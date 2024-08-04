# 内存移动 - memmove

>  也是拷贝函数,源字符串可能会被覆盖,但保证目标是想要的

## 描述

C 库函数 **void \*memmove(void \*str1, const void \*str2, size_t n)** 从 **str2** 复制 **n** 个字符到 **str1**，但是在重叠内存块这方面，memmove() 是比 memcpy() 更安全的方法。如果目标区域和源区域有重叠的话，memmove() 能够保证源串在被覆盖之前将重叠区域的字节拷贝到目标区域中，复制后源区域的内容会被更改。如果目标区域与源区域没有重叠，则和 memcpy() 函数功能相同。

## 声明

下面是 memmove() 函数的声明。

```
void *memmove(void *str1, const void *str2, size_t n)
```

## 参数

- **str1** -- 指向用于存储复制内容的目标数组，类型强制转换为 void* 指针。
- **str2** -- 指向要复制的数据源，类型强制转换为 void* 指针。
- **n** -- 要被复制的字节数。

## 返回值

该函数返回一个指向目标存储区 str1 的指针。



## 模拟实现

### 1.0

```
#include<string.h>

void * memmove ( void * dst, const void * src, size_t count)
{
        void * ret = dst;
				//目标地址小于等于源地址和目标地址在源字符串拷贝范围外,能够直接拷贝
        if (dst <= src || (char *)dst >= ((char *)src + count)) {
        				//直接拷贝:等价memcpy
                while (count--) {
                        *(char *)dst = *(char *)src;
                        dst = (char *)dst + 1; 
                        src = (char *)src + 1;
                } 
         }
        //发生重叠
        else {
        				//掉头,反向拷贝(从尾部开始往前走)
                dst = (char *)dst + count - 1; 
                src = (char *)src + count - 1;
								//反向拷贝
                while (count--) {
                        *(char *)dst = *(char *)src;
                        dst = (char *)dst - 1; 
                        src = (char *)src - 1;
                } 
        }
        return(ret);
}


int main()
{
    int arr1[] = { 1, 2, 3, 4, 5 };
    int arr2[5] = { 0 };
    my_memmove(arr1 , arr1+2, 2 * sizeof(arr1[0]));
    return 0;
}

```









