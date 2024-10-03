[toc]



## 认识

### ifstream等都是basic_ifstream的别名

例如:

using ifstream = basic_ifstream<...>;

using ios = ios_base ...



## 文件基本操作

### 文件创建

使用ofstream(filename)或fstream时进行写入时

如果文件名对应的文件不存在,则都会对其进行创建新文件.



### 文件读写



#### 头文件简介

\<ifstream>:打开文件读操作 -- 将文件内容读取到内存(string ,char[],...等内存缓冲区)中
\<ofstream>:打开文件写操作 -- 将内存中数据写到文件中
fstream:可读可写 
...

#### 打开文件

##### 1. 打开方式



- 以ascii方式打开
  ios::in  读方式打开文件
  ios::out 写方式打开文件 (也具有创建功能)
  ios::app 追加方式打开文件

- 以二进制方式读写
  ios::binary 

- 附加操作

  ios::ate 打开已有文件,文件指针在文件末尾
  ios::trunc 文件不存在的情况下具有创建功能,一般以ios::trunc|ios::in
  ios::nocreate 不创建方式
  ios::noreplace 不替换方式,清除

- 缺省值是 ios::in|ios::out  ,即可读可写

##### 2. 判断文件打开是否成功

- 流对象!运算符和bool运算符重载  if(!fstream) ? 打开失败 : 打开成功

- 流对象fs.is_open()  true成功,false失败
  

#### 关闭文件

  `流对象.close();`

#### 文件读写

- 以流插入方式写入
  `fp<<"str1"<<"\t"<<"str2"<<;`
- 流提取
  `fp>>buffer1>>buffer2; //默认文件指针从头开始,以空格为分隔符`
      

- 字符

  ```
  char key;
  fs.get(key); //读fs中的一个字符到key中,指针后移
  fs.put(key); //像fs中写入一个key,指针后移
  ```

- 二进制

  ```
  char str[100] = "";
  fs.read(str,8);//读fs中8个字符到str
  fs.write(str,9); //将str中的前9个写到fs
  ```

  



## 文件指针

### ifstream

 - 移动文件指针
 
  ```
    basic_istream& seekg( pos_type pos ); (1)
    basic_istream& seekg( off_type off, std::ios_base::seekdir dir ); (2)
    ```

 Example:
 
 ```
 移动到end相对于pos的位置
 seekg(0,ifs.end()); // 移动到文件末尾+0的位置,即文件末尾
 ```
 
 
 
 - 获取指针的当前位置/获取指针相对于起始位置的长度
 
    ```
    pos_type tellg();
    ```
    
    Example:
    
    ```
    size_t pos = ifs.tellg();
    ```

参数:
    pos	-	设置输入位置指示器到的绝对位置。 //用于(1)
    off	-	设置输入位置指示器到的相对位置（正数或负数）。用于(2)
    dir	-	定义应用相对偏移量的基位置。它可以是下列常量之一：
dir:  常量	    解释
    ios::beg	流的开始
    ios::end	流的结尾
    ios::cur	流位置指示器的当前位置

流对象fs.eof() //文件结束标志

### ofstream

```
    seekp(long int pos);
    seekp(long int pos, ios_base::seekdir begin);//把begin移动到pos
```

参数:
    pos	-	设置输入位置指示器到的绝对位置。 //用于(1)
    off	-	设置输入位置指示器到的相对位置（正数或负数）。用于(2)
    dir	-	定义应用相对偏移量的基位置。它可以是下列常量之一：
dir:  常量	    解释
    ios::beg	流的开始
    ios::end	流的结尾
    ios::cur	流位置指示器的当前位置

流对象fs.eof() //文件结束标志



### 其他

std::noskipws //no skip word space 不跳过间隔(符)
fs>>std::noskipws>>(char*)buffer;  //即先读到noskipws,并且把空格\换行也读了进来,然后传给buffer.这样就能保持读的数据一致
//string需要测试,有特性


//指针和数组下标一样,从0开始,pos是下标
*/

//stringstream
/*
有自动类型转换特性...

*/

//manipulators 操纵符
/*
https://zh.cppreference.com/w/cpp/io/manip
https://legacy.cplusplus.com/reference/ios/

*/





## getline

### 声明

```
istream& getline (istream&  is, string& str, char delim);
istream& getline (istream&& is, string& str, char delim);

istream& getline (istream&  is, string& str);
istream& getline (istream&& is, string& str);
```



### 描述:

```
Note that any content in str before the call is replaced by the newly extracted sequence.
请注意，调用前str中的任何内容都将被新提取的序列替换。

Each extracted character is appended to the string as if its member push_back was called.
每个提取的字符都被附加到字符串中，就像其成员push_back被调用一样。
```

1. 一次读取一行,遇到分隔符(换行)停止,下次读取跳过换行符读取
2. 第一次调用时会清空str
3. 之后每一次调用都会追加至str



### 使用

```
#include <iostream>
#include <string>

int main ()
{
  std::string name;

  std::cout << "Please, enter your full name: ";
  std::getline (std::cin,name);
  std::cout << "Hello, " << name << "!\n";

  return 0;
}
```



## ignore

### 声明:

```
basic_istream& ignore( std::streamsize count = 1, int_type delim = Traits::eof() );
```



### 描述:

```
Extracts characters from the input sequence and discards them, until either n characters have been extracted, or one compares equal to delim.
  从输入序列中提取字符并丢弃它们，直到提取了n个字符，或者有一个字符等于delim。

The function also stops extracting characters if the end-of-file is reached. If this is reached prematurely (before either extracting n characters or finding delim), the function sets the eofbit flag.
  如果到达文件末尾，该函数也会停止提取字符。如果过早达到此值（在提取n个字符或查找delim之前），该函数将设置eofbit标志。
```

 从输入序列中提取字符并丢弃它们，直到提取了n个字符，或者有一个字符等于delim。delim也会被丢弃;



### 参数:

| count | -    | 要提取的字符数                     |
| ----- | ---- | ---------------------------------- |
| delim | -    | 提取停止处的分隔字符。也会被提取。 |



### 用途:

可以搭配getline一起使用,在连续读取时,可以方便舍弃掉一些分隔符;



exam:

```
   std::cout << "请输⼊联系⼈年龄: ";
   int age = 0;
   std::cin>>age;
   pi->set_age(age);
   std::cin.ignore(256,'\n');  //cin读完后,缓冲区还会剩分隔符及后面的数据,如果下次读取是getline则会把这个分隔符一并读取,可能导致得到不想要的结果;因此可以清理一下
   for(int i = 0; ;i++) {
     std::cout<<"请输入联系人电话"<<i+1<<"(只输入回车完成电话新增):";
     std::string number;
     getline(std::cin,number);
     if(number.empty())//getline会忽略分割符(换行),下次会跳过
     {
       break;
     }
     pi->add_phone()->set_number(number);
   }

   std::cout<<"添加联系人成功"<<std::endl;
```

