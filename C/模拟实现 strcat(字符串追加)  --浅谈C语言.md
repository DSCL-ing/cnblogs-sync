
## strcat
### 描述
char *strcat(char *dest, const char *src) 把 src 所指向的字符串追加到 dest 所指向的字符串的结尾。

### 声明
下面是 strcat() 函数的声明。

char *strcat(char *dest, const char *src)

### 参数
dest -- 指向目标数组，该数组包含了一个 C 字符串，且足够容纳追加后的字符串。
src -- 指向要追加的字符串，该字符串不会覆盖目标字符串。

### 返回值
该函数返回一个指向最终的目标字符串 dest 的指针。

### 注意事项:
对于strcat（追加）函数,不能自己追加自己，因为写入后会覆盖掉arr1本身的\0，后面arr2也改变，从而死循环

### 模拟实现
```
//my_strcat

#include<stdio.h>
#include<assert.h>
char* my_strcat(char*dest, const char*source)
{
    //ret存dest指针
    char*ret = dest;
    assert(dest&&source);
    //走到'\0'这里
    while (*dest)
    {
        dest++;
    }
    //开始追加,直到最后一个位为'\0',结束循环
    while (*dest++ = *source++)  //strcpy
        ;
    return ret;
}

int main()  //  my_strcat
{
    char arr1[30] = { 'a', 'b', 'c', 'd', '\0' };
    char arr2[] = "bit";
    printf("%s", my_strcat(arr1, arr2));
    return 0;
}
```