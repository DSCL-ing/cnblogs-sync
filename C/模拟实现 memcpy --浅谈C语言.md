# 内存拷贝 - memcpy

## 描述

C 库函数 **void \*memcpy(void \*str1, const void \*str2, size_t n)** 从存储区 **str2** 复制 **n** 个字节到存储区 **str1**。

`memcpy` 是最快的内存到内存复制子程序。

它通常比必须扫描其所复制数据的strcpy ，或必须预防以处理重叠输入的memmove更高效。

[memcpy, memcpy_s - C++中文 - API参考文档 (apiref.com)](https://www.apiref.com/cpp-zh/c/string/byte/memcpy.html)



## 声明

下面是 memcpy() 函数的声明。

```
void *memcpy(void *str1, const void *str2, size_t n)
```

## 参数

- **str1** -- 指向用于存储复制内容的目标数组，类型强制转换为 void* 指针。
- **str2** -- 指向要复制的数据源，类型强制转换为 void* 指针。
- **n** -- 要被复制的字节数。

## 返回值

该函数返回一个指向目标存储区 str1 的指针。



```
#include<assert.h>
#include<stdio.h>

void* my_memcpy(void*dest, const void* src, size_t num)
{
		//返回void*
    void* ret = dest;
    assert(dest&&src);

		//循环逐字节拷贝
    while (num--)//先使用后减减
    {
         *(char *)dest = *(char *)src 
         dest = (char *)dest + 1;
         src  = (char *)src  + 1;
    }
    return ret;
}

int main()
{
    int arr1[] = { 1, 2, 3, 4, 5 };
    int arr2[5] = { 0 };
    my_memcpy(arr2, arr1, sizeof(arr1));
    return 0;
}
```





