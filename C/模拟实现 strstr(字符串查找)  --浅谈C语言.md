# C 字符串查找 - strstr()

## 描述

C 库函数 **char \*strstr(const char \*haystack, const char \*needle)** 在字符串 **haystack** 中查找第一次出现字符串 **needle** 的位置，不包含终止符 '\0'。

## 声明

下面是 strstr() 函数的声明。

```
char *strstr(const char *haystack, const char *needle)
```

## 参数

- **haystack** -- 要被检索的 C 字符串。
- **needle** -- 在 haystack 字符串内要搜索的小字符串。

> haystack 干草堆
>
> needle 针
>
> 来源推导: 干草堆里捞针/大海捞针. 
>
> 参考VS命名
>
> haystack == string
>
> needle == substring

## 返回值

该函数返回在 haystack 中第一次出现 needle 字符串的位置，如果未找到则返回 null。



### 模拟实现

```
#include<assert.h>


char *  strstr (const char * str1, const char * str2)
{
        char *cp = (char *) str1; //强转成非const ; cp不修改原指针的指针变量副本
        char *s1, *s2;

				//要找的字串为空,返回
        if ( !*str2 )
            return((char *)str1);

				//暴力查找
        while (*cp) 
        {
        				//小循环用的临时副本
                s1 = cp;
                s2 = (char *) str2;
								//循环比较
                while ( *s1 && *s2 && !(*s1-*s2) ) //strcmp
                        s1++, s2++;

								//循环结束后,找到了(s2走到'\0'),返回字串起始地址cp
                if (!*s2) 
                        return(cp);
								
								//找下一个
                cp++; 
        }
				//走到末尾,找不到,返回空
        return(NULL);

int main()
{
    char* p1 = "abcddefdef";
    char* p2 = "def";
    char* ret = my_strstr(p1, p2);
    if (ret == NULL)
        printf("子串不存在\n");
    else
        printf("%s\n", ret);
    return 0;
}  
```

