[toc]

### if-else组合

- 表达式: 变量与操作符的组合称为表达式
- 语句: 以分号结尾的表达式称为语句
- if(0){ //... }注释法,在看到if(0)时,有可能这是一个注释,不推荐这种做法,但是需要认识.

#### if的执行顺序

1. 计算功能:先执行完毕if括号()中的表达式or某种函数,得到表达式的真假结果

2. 判定功能:根据表达式结果进行条件判定

3. 分支功能:根据判定结果进行分支  

   > (if有判定和分支两个功能,而switch只有判定而没有分支功能,因此必须使用break)

#### 操作符的执行顺序测试方法

`printf("1   ") && printf("2   ");`  
`printf("1   ") || printf("2   ");`

#### C语言的布尔类型

- C89/C90没有bool类型
- C99 引入了关键字为_Bool的类型,在新增的头文件stdbool.h中.为了保证C/C++的兼容性,被重新用宏写成了bool.
- 微软对C语言bool类型也有一套标准,BOOL,FALSE,TRUE. 不推荐使用微软这套标准,不具备可移植性

#### 浮点数与"零值"比较

- ##### 精度损失:浮点值与实际值不等,可能偏大可能偏小,都属于精度损失  

  1. **验证浮点数是否存在精度损失**  
     ![精度损失1](%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E4%B8%8E%E6%9D%A1%E4%BB%B6%E8%AF%AD%E5%8F%A5%E7%9A%84%E7%BB%86%E8%8A%82%E4%B8%8E%E6%80%9D%E6%83%B3%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E7%B2%BE%E5%BA%A6%E6%8D%9F%E5%A4%B11.png)  
  2. **验证浮点数的差值是否存在精度损失**  
     ![精度损失2](%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E4%B8%8E%E6%9D%A1%E4%BB%B6%E8%AF%AD%E5%8F%A5%E7%9A%84%E7%BB%86%E8%8A%82%E4%B8%8E%E6%80%9D%E6%83%B3%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E7%B2%BE%E5%BA%A6%E6%8D%9F%E5%A4%B12.png)
  3. **浮点数直接比较验证**  
     ![精度损失3](%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E4%B8%8E%E6%9D%A1%E4%BB%B6%E8%AF%AD%E5%8F%A5%E7%9A%84%E7%BB%86%E8%8A%82%E4%B8%8E%E6%80%9D%E6%83%B3%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E7%B2%BE%E5%BA%A6%E6%8D%9F%E5%A4%B13.png)  
     **结论: 浮点数在进行比较时,绝对不能使用双等号`==`来进行比较.  浮点数本身有精度损失,进而导致结果可能有细微的差别.**  

- ##### 如何进行浮点数比较

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

![浮点比较方法1](%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E4%B8%8E%E6%9D%A1%E4%BB%B6%E8%AF%AD%E5%8F%A5%E7%9A%84%E7%BB%86%E8%8A%82%E4%B8%8E%E6%80%9D%E6%83%B3%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E6%B5%AE%E7%82%B9%E6%AF%94%E8%BE%83%E6%96%B9%E6%B3%951.png)

- 浮点数与"零值"比较,只需要判定它是否小于EPSILON即可

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

#### 补充:如何理解强制类型转化

**强制类型转化:不改变数据本身,只改变数据的类型**

- "123456" -> int:123456

```
字符串"123456"如何转化成整型值123456,能强转吗? 答案是不能,只能通过算法进行转化

因为"123456"的空间至少占了7个,而整型int只占4个字节.
```

- ##### 不同类型的0

```
printf("%d\n",0);
printf("%d\n",'\0');
printf("%d\n",NULL); //(void*)0
```

![不同类型的0](%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E4%B8%8E%E6%9D%A1%E4%BB%B6%E8%AF%AD%E5%8F%A5%E7%9A%84%E7%BB%86%E8%8A%82%E4%B8%8E%E6%80%9D%E6%83%B3%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/%E4%B8%8D%E5%90%8C%E7%B1%BB%E5%9E%8B%E7%9A%840.png)

### switch case组合

- **基本语法结构**  

```
//switch只能对整数进行判定,做不了复杂的逻辑计算
switch(整型变量/常量/整型表达式){
    case 常量1:
        break;
    case 常量2:
        break;
    case 常量3:
        break;
    default:
        break;
}
推荐使用switch的场景:只能用于整数判定且分支很多的情况下
```

- switch case 的功能
  switch本身没有判断和分支能力,switch是拿着结果去找case进行匹配,  
  case具有判定能力,但没有分支能力,case是通过break完成分支功能  
  break具有分支功能,相当于if的分支能力.  
  default相当else,处理异常情况

#### (补充) 屏蔽警告的方法

```
error C4996: 'scanf': This function or variable may be unsafe. Consider using scanf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.
方法1:
#pragma warning(disable:4996)
方法2:
#define _CRT_SECURE_NO_WARNINGS //该宏定义必须写在文件的首行(头文件的前面)才有效
(如果宏没有宏值,则只能用在#ifdef等条件编译语句中,即只用于标识)
```

- ##### 在case中执行多条语句,建议case后都带上花括号.

  在case中定义变量,直接写会警告,需要带上花括号,但不建议在case中定义变量,如果非要这么做,可以封装成函数来替代.并且
  ![case](%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E4%B8%8E%E6%9D%A1%E4%BB%B6%E8%AF%AD%E5%8F%A5%E7%9A%84%E7%BB%86%E8%8A%82%E4%B8%8E%E6%80%9D%E6%83%B3%20--%E8%BF%9B%E9%98%B6C%E8%AF%AD%E8%A8%80.assets/case%E8%AD%A6%E5%91%8A.png)  

- ##### 多个case执行同样语句

```C
int main()
{
    int n = 0 ;
    scanf("%d",&n);
    switch (n)
    {
        case 1: case 2: case 3: case 4: case 5:
            puts("周内");
            break;
        case 6:
            puts("周六");
            break;
        case 7:
            puts("周日");
            break;
        default:
            break;
    }
    return 0;
}
```

- default可以在switch中的任意位置,一般习惯放在最后的case后
- switch中尽量不要单独出现return.一般习惯用break,突然return容易搞混
- switch中不要使用bool值,不好维护
- case的值必须是数字常量,不能是`const int a = 1;`这种
- 按执行频率排列case语句,频率越高越靠前,能减少匹配次数

### do、while、for

#### 循环的基本结构  

- 一般的循环都必须要有3种功能：  
  1. 循环条件初始化
  2. 循环条件判定
  3. 循环条件更新

(死循环除外)

```C
int main()
{
    int count = 10; //1.循环条件初始化
    while (count > 10) //2.循环条件判定
    {
        printf("%d\n", count); //3.业务逻辑
        count--; //4.循环条件更新
    }
    return 0;
}
```

- for循环  

```
使用样例:
for(int i = 0; i<10; i++)
{
    //业务逻辑
}
```

for的结构更加紧凑,更清晰

```
for(1.循环条件初始化； 2.循环条件判定； 4.循环条件更新){
    //3.业务逻辑
}
```


- do-while

```
//1.循环条件初始化
do{
  //2.业务逻辑
  //3.循环条件更新
}while(4.循环条件判定);
```

do while结构需要在while()后加上分号，容易忘记

#### continue跳转的位置

- while循环continue后会跳转到循环条件判定的位置,之后执行循环判定
- for循环会跳转到循环条件更新的位置,之后进行循环条件更新!!!

#### 循环设计的思想推荐

1.尽可能减少循环来回多次的跳转的次数 --- 涉及缓存,局部性原理,CPU命中概率.尽可能让代码执行的更加平滑
2.在多重循环中,如果有可能,应当将最长的循环放在最内层,最短的循环放在最外层,以减少CPU跨且循环层的次数.

#### 推荐使用for的前闭后开写法

```
推荐1:for语句循环的次数的计算方式
1.for(int i = 0; i<=9; i++){} //cnt = 9-0+1 = 10次
2.for(int i = 0; i<10; i++){} //cnt = 10-0 = 10次
3.for(int i = 6; i<=9; i++){} //cnt = 9-6+1 = 4次
4.for(int i = 6; i<10; i++){} //cnt = 10-6 = 4次
从计算角度,前闭后开写法能更加直观,快速

推荐2:下标映射时,思维清晰,不容易混乱
```

### 