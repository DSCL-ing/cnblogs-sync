[toc]

### C语言的四种取整方式:

<br>

#### 零向取整

如图:

![image-20240502180459967](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502180459967.png)

可以发现C语言a和b的取整方式都不是四舍五入,而是直接舍弃小数部分.(a四舍五入是-3,b四舍五入是3.)这种方式叫做**零向取整**.也是**c语言中的默认取整方式**

![image-20240502203220627](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502203220627.png)

从图中可以看出无论是-2.9还是2.9，它们取整方向都是向着0的方向取整.

<br>

##### trunc函数(C99)

C语言`<math.h>`库中也有零向取整函数,它的返回值是浮点型，如果需要也是可以强转成int类型使用.

![image-20240502182325013](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502182325013.png)

<br>

###### trunc的使用

![image-20240502222958451](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502222958451.png)

注意,%d不能直接接收浮点型,浮点型在内存空间中的布局和整型是不一样的,这点要注意.

如果需要转成整型使用,需要圆括号`(int)`强制类型转换.

<br>

#### 地板取整

这个名字有点奇怪，它是函数floor的翻译而来.

也叫向下取整,向左取整,向负无穷取整

![image-20240502211936753](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502211936753.png)

![image-20240502203203237](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502203203237.png)

<br>

##### floor函数的使用

![image-20240502223257986](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502223257986.png)

<br>

#### 向上取整

又称向右取整,向正无穷取整, 来源于ceil函数



![image-20240502221803375](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502221803375.png)

![image-20240502221916354](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502221916354.png)

<br>

##### ceil函数的使用

![image-20240502223221177](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502223221177.png)

<br>

#### 四舍五入

<br>

##### round函数(C99)

![image-20240502223527209](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502223527209.png)

<br>

###### round函数的使用

![image-20240502223401492](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502223401492.png)

<br>

#### 四种取整方式演示

```C
#include<stdio.h>
#include<math.h>

int main()
{
    const char * format = "%.1f \t%.1f \t%.1f \t%.1f \t%.1f\n"; 
    printf("value\tround\tfloor\tceil\ttrunc\n");
    printf("-----\t-----\t-----\t----\t-----\n");
    printf(format, 2.3, round(2.3), floor(2.3), ceil(2.3), trunc(2.3));
    printf(format, 3.8, round(3.8), floor(3.8), ceil(3.8), trunc(3.8));
    printf(format, 5.5, round(5.5), floor(5.5), ceil(5.5), trunc(5.5));
    printf(format, -2.3, round(-2.3), floor(-2.3), ceil(-2.3), trunc(-2.3));
    printf(format, -3.8, round(-3.8), floor(-3.8), ceil(-3.8), trunc(-3.8));
    printf(format, -5.5, round(-5.5), floor(-5.5), ceil(-5.5), trunc(-5.5));
    return 0;
}
```

![image-20240502224455627](C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E5%8F%96%E6%95%B4%E6%96%B9%E5%BC%8F.assets/image-20240502224455627.png)



<br>