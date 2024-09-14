## 浮点数与"零值"

### 精度损失:

浮点值与实际值不等,可能偏大可能偏小,都属于精度损失  

1. **验证浮点数是否存在精度损失**  
   ![精度损失1](%E6%B5%AE%E7%82%B9%E6%95%B0%E7%9A%84%E6%AF%94%E8%BE%83%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E7%B2%BE%E5%BA%A6%E6%8D%9F%E5%A4%B11.png)  

### 验证浮点数的差值是否存在精度损失

   ![精度损失2](%E6%B5%AE%E7%82%B9%E6%95%B0%E7%9A%84%E6%AF%94%E8%BE%83%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E7%B2%BE%E5%BA%A6%E6%8D%9F%E5%A4%B12.png)

### 浮点数直接比较验证

   ![精度损失3](%E6%B5%AE%E7%82%B9%E6%95%B0%E7%9A%84%E6%AF%94%E8%BE%83%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E7%B2%BE%E5%BA%A6%E6%8D%9F%E5%A4%B13.png)  
   **结论: 浮点数在进行比较时,绝对不能使用双等号`==`来进行比较.  浮点数本身有精度损失,进而导致结果可能有细微的差别.**  

### 如何进行浮点数比较

```C
1. x - y == 0的条件是 |x - y| < 精度.
即 x - y > -精度 && x - y < 精度

2.还可以使用fabs函数,C90,<math.h>, double fabs(double x); 返回x的绝对值.
即 fabs(x-y) < 精度
```

```
//--------------------------------------------------------------
//方法1,自定义精度
#include<stdio.h>
#include<math.h>

#define EPSILON 0.0000000000000001 //自定义精度
int main()
{
    double x = 1.0;
    double y = 0.1;

    //验证x - 0.9 是否等于 0.1
    if(fabs((x-0.9)- y) < EPSILON ) printf("aaaa\n");
    else printf("bbbb\n");

    puts("hello world!");
    return 0;
}
```

```
//方法2:使用C语言提供的精度
#include<stdio.h>
#include<math.h>
#include<float.h>

int main()
{
    double x = 1.0;
    double y = 0.1;

    //验证x - 0.9 是否等于 0.1
    //<float.h> 内置最小精度值 DBL_EPSILON 和 FLT_EPSILON ,1.0+DBL_EPSILON != 1.0 ,EPSILON是改变1.0的最小的值,数学概念,略
    if(fabs((x-0.9)- y) < DBL_EPSILON ) printf("aaaa\n");
    else printf("bbbb\n");
    
    return 0;
}
```

![浮点比较方法1](%E6%B5%AE%E7%82%B9%E6%95%B0%E7%9A%84%E6%AF%94%E8%BE%83%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E6%B5%AE%E7%82%B9%E6%AF%94%E8%BE%83%E6%96%B9%E6%B3%951.png)



### 浮点数与"零值"比较

只需要判定它是否小于EPSILON即可

```
int main()
{
    double x = 0.0;
    // double x  = 0.00000000000000000000000000001; //很小也可以认为等于0
    if(fabs(x) < DBL_EPSILON ) printf("等于0\n");
    else printf("不等于0\n");
    
    return 0;
}
```

#### 