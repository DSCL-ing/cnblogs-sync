# C 库函数 - strcmp()

## 描述

C 库函数 **int strcmp(const char \*str1, const char \*str2)** 把 **str1** 所指向的字符串和 **str2** 所指向的字符串进行比较。

## 声明

下面是 strcmp() 函数的声明。

```
int strcmp(const char *str1, const char *str2)
```

## 参数

- **str1** -- 要进行比较的第一个字符串。
- **str2** -- 要进行比较的第二个字符串。

## 返回值

该函数返回值如下：

- 如果返回值小于 0，则表示 str1 小于 str2。
- 如果返回值大于 0，则表示 str1 大于 str2。
- 如果返回值等于 0，则表示 str1 等于 str2。



## 模拟实现

比较方式，依次比较ascii大小，大返回正数，小于返回负数，等于则比较下一位。若全都相等则返回0；

### 1.0

my_strcmp 1.0 版本 

```
#include<stdio.h>
#include<assert.h>

int my_strcmp(const char*str1, const char*str2)
{
    assert(str1&&str2);
    //依次比较ascii大小
    while (*str1 == *str2)
    {
    		//相等且都为'\0'
        if (str1 == '\0')
        {
            return 0;
        }
        str1++;
        str2++;
    }
    return(*str1 - *str2);
}

int main()  
{
    char* str1 = "aaaa";
    char* str2 = "bbbb";
    int ret = my_strcmp(str1, str2);
    printf("%d\n", ret);
    return 0;
}
```



### 2.0

```
#include<stdio.h>
#include<assert.h>

int my_strcmp (const char * src, const char * dst)
{
        int ret = 0 ; 
        assert(src != NULL);
        assert(dest != NULL);
        //以字符类型(以字节为单位)进行整型计算
        while( *dst && *src && !(ret = *(unsigned char *)src - *(unsigned char *)dst) )
                ++src, ++dst;
                
        if ( ret < 0 )
                ret = -1 ;
        else if ( ret > 0 )
                ret = 1 ;

        return( ret );
}
```







