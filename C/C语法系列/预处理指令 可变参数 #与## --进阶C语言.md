[toc]

### 举例一些的宏和预处理指令

ANSI标准的5个预定义宏

`__FILE__`:当前文件名

`__LINE__`:所在行号

`__STDC__`:当编译器遵循ANSI C标准时该宏被定义为1。

`__TIME__`:表示当前源代码被编译的时间字符串。

`__DATE__`:表示当前源代码被编译的日期字符串。

#### C99引入的特性

- 宏支持取可变参数 #define Macro(...) `__VA_ARGS__`
  `__VA_ARGS__` 表示所有传递给宏的参数，可以是零个或多个参数
- 使用宏的时候，允许省略参数，被省略的参数会被扩展成空串。
- 除了已有的 `__LINE__`和`__FILE__` 以外，增加了`__func__`得到当前的函数名。
- 标准宏`__STDC_VERSION__`被定义为值199901L来指示能够获得C99支持。

```
#if __STDC_VERSION__ >= 199901L
  /* "inline" is a keyword */
#else
# define inline static
#endif
```

##### ` VA_ARGS__`

- `__VA_ARGS__` 必须位于宏定义的参数列表中的最后。

- 在 `__VA_ARGS__` 前后可以有其他参数,但至少要有一个参数,以便在没有传递额外参数时编译不会出错。

- 传给`__VA_ARGS__`的多个参数,输出时会自动使用逗号分隔。




##### `##__VA_ARGS__`

- 如果可变参数被忽略或为空，## 操作将使预处理器（preprocessor）去除掉它前面的那个逗号.
- 如果你在宏调用时，确实提供了一些可变参数，GNU CPP 也会工作正常，它会把这些可变参数放到逗号的后面。
- 这里的##功能不同于##运算符,注意区别.

```
#define LOG(...) printf("[%s:%d]" ,__FILE__,__LINE__,__VA_ARGS__)
int main()
{
	LOG(); //报错
}
```

```
#define LOG(...) printf("[%s:%d]" ,__FILE__,__LINE__,__VA_ARGS__)
int main()
{
	LOG(); //编译通过
}
```



##### 说明:

`__VA_ARGS__`是一个傻傻的宏,在不定参前固定有一个逗号','因此会产生很多"反常"现象

- 不定参为空时,"正常写法"编译器会报错

  预处理视角(右):`__VA_ARGS__`必带一个逗号 

  ![image-20240729205043971](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240729205043971.png)

- 不"正常"的正确写法

  ![image-20240729205459793](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240729205459793.png)

- 日常写法

  ![image-20240729205611187](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240729205611187.png)



<br>

#### #line

可以定制化你的文件名称和代码行号，很少使用

![image-20240508220935484](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240508220935484.png)



<br>

#### #error

\#error命令是C/C++语言的预处理命令之一，当预处理器预处理到#error命令时将停止编译并输出用户自定义的错误消息。

![image-20240508221706882](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240508221706882.png)

用于人为阻止编译,在某些情况下得知编译条件不满足时,可以使用#error让编译停止.



<br>

#### #pragma

\#pragma 指令很复杂，需要使用的时候再查一下[#pragma_百度百科 (baidu.com)](https://baike.baidu.com/item/%23pragma/706691?fr=ge_ala).

##### Message参数

Message 参数能够在编译信息输出窗口中输出相应的信息，这对于源代码信息的控制是非常重要的。其使用方法为：

`#pragma message("消息文本")`

当编译器遇到这条指令时就在编译输出窗口中将消息文本打印出来。

![image-20240508222436848](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240508222436848.png)

也可以用于在编译器检测某个宏是否被定义:

![image-20240508223051592](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240508223051592.png)



##### #warning

```
#pragma warning(disable:4996;once:4385;error:164)
```

等价于：

```
1#pragma warning(disable:4996)//不显示4996警告信息 
2#pragma warning(once:4385)//4385号警告信息仅报告一次 
3#pragma warning(error:164)//把164号警告信息作为一个错误。
```





还有很多用法..,可以查下文档

[#pragma_百度百科 (baidu.com)](https://baike.baidu.com/item/%23pragma/706691?fr=ge_ala)



### #和##

#### 前置:相邻字符串具有自动连接特性

C语言两个相邻的字符串能够自动拼接成一个字符串.

```
int main()
{
    puts("hello"" world");
    const char *str = "hello"" world\n";
    printf(str);
    return 0;
}
```

![image-20240509133454682](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240509133454682.png)

<br>

#### #运算符

##### #运算符的功能:在宏定义中,将宏参数转化成字符串

常搭配字符串连接特性一起使用

##### 用法举例:

```
#define STR(s) #s

int main()
{
    printf("PI: "STR(3.1415926)"\n");
    return 0;
}
```

![image-20240509141118633](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240509141118633.png)

##### 使用场景:

```
#define SQR(x) printf("The square of x is %d.\n", ((x)*(x)));

int main()
{
    SQR(8);
    return 0;
}
```

![image-20240509152413609](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240509152413609.png)

注意到没有，引号中的字符 x 被当作普通文本来处理，而不是被当作一个可以被替换的语言 
符号。
假如你确实希望在字符串中包含宏参数，那我们就可以使用“#”，它可以把语言符号转 
化为字符串。上面的例子改一改：

`#define SQR(x) printf("The square of "#x" is %d.\n", ((x)*(x))); `
再使用

​	`SQR(8);`
则输出的是:
![image-20240509152653034](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240509152653034.png)



<br>

#### ##运算符

##### 功能:

​	将宏参数与特定的符号组合成一个全新的符号

##### 用法举例:

![image-20240509151828264](%E6%96%B0%E5%BB%BA%E6%96%87%E6%9C%AC%E6%96%87%E6%A1%A3.assets/image-20240509151828264.png)





