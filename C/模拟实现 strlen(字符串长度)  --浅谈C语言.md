# 字符串长度 - strlen()

## 描述

C 库函数 **size_t strlen(const char \*str)** 计算字符串 **str** 的长度，直到空结束字符，但不包括空结束字符。

## 声明

下面是 strlen() 函数的声明。

```
size_t strlen(const char *str)
```

## 参数

- **str** -- 要计算长度的字符串。

## 返回值

该函数返回字符串的长度。



## 模拟实现

### 1. 计数器方式

```
int my_strlen(const char * str)
{
    int count = 0; 
    while(*str) //走到'\0'为止
    {
        count++;
        str++;
    }
    return count;
}
```

### 2.递归方式 -- 无临时变量

```
int my_strlen(const char * str)
{
    if(*str == '\0')//走到'\0'为止
        return 0;
    else
        return 1+my_strlen(str+1);
}
```

### 3.指针计算方式

```
int my_strlen(char *s)
{
       char *p = s; 
       while(*p != ‘\0’ )//走到'\0'为止
              p++; 
       return p-s;
}
```

