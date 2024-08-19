[toc]

# 输入/输出操纵符

输入输出操纵符是 C++ 中用于控制输入输出流格式的一组特殊函数或对象。它们通常用于格式化输出，例如设置宽度、精度、对齐方式等，而不涉及数据的实际读写。

- **功能概述**：输入输出操纵符能够控制输出的外观，比如调整对齐方式、设置输出的宽度和精度、控制换行等。
- **使用场景**：让输出看起来更整洁、更易于阅读
- **如何使用**：这些操纵符通常通过`operator <<` 或 `operator >>` 操作符与 `std::cout` 或 `std::cin` 等结合使用。



### 简单示例

- **换行**：`endl` 用于在输出后插入一个换行符，并清空输出缓冲区。
- **设置宽度**：`setw(n)` 用于设置输出的宽度，使得输出更加整齐。
- **设置精度**：`setprecision(n)` 用于设置浮点数输出的小数位数。
- **对齐方式**：`left`, `right`, `internal` 用于设置输出的对齐方式。
- **布尔值格式**：`boolalpha` 用于将布尔值输出为 `true` 或 `false`。
- **基数格式**：`hex`, `oct`, `dec` 用于设置输出的基数，如十六进制、八进制或十进制。





### 输入操纵符（Input Manipulators）

1. **`std::setw(int n)`**: 设置字段宽度为n个字符。例如：
   ```cpp
   std::cout << std::setw(10) << "Hello";
   // 输出为 "     Hello"
   ```

2. **`std::setprecision(int n)`**: 设置浮点数精度为n位。例如：
   ```cpp
   double pi = 3.1415926535;
   std::cout << std::setprecision(3) << pi;
   // 输出为 "3.14"
   ```

3. **`std::fixed`** 和 **`std::scientific`**: 控制浮点数的输出格式。例如：
   
   ```cpp
   double number = 123.456;
   std::cout << std::fixed << number; // 输出固定小数点格式
   std::cout << std::scientific << number; // 输出科学计数法格式
   ```
   
4. **`std::boolalpha`** 和 **`std::noboolalpha`**: 控制布尔值的输出格式。例如：
   
   ```cpp
   bool flag = true;
   std::cout << std::boolalpha << flag; // 输出为 "true"
   ```
   
5. **`std::ws`**: 跳过输入流中的空白字符，直到遇到非空白字符为止。例如：

   ```cpp
   std::string name;
   std::cin >> std::ws;
   std::getline(std::cin, name);
   ```

6. **`std::getline`**: 从输入流中读取一整行字符串，直到换行符为止。

   ```cpp
   std::string line;
   std::getline(std::cin, line);
   ```





### 输出操纵符（Output Manipulators）

1. **`std::noskipws`**: 禁用空白字符的忽略。例如：
   ```cpp
   std::cin >> std::noskipws >> ch;
   ```

2. **`std::showpoint`**: 总是显示浮点数的小数点和末尾的零。例如：
   
   ```cpp
   double value = 3.0;
   std::cout << std::showpoint << value; // 输出为 "3.000"
   ```
   
3. **`std::uppercase`**: 将十六进制数字大写显示。例如：
   
   ```cpp
   int number = 255;
   std::cout << std::uppercase << std::hex << number; // 输出为 "FF"
   ```

4. **`std::endl`**: 输出换行并刷新输出缓冲区。这有时用于确保输出立即显示。例如：
   ```cpp
   std::cout << "Hello, World!" << std::endl;
   ```

2. **`std::hex`**: 将整数以十六进制格式输出。例如：
   
   ```cpp
   int number = 255;
   std::cout << std::hex << number; // 输出为 "ff"
   ```
   
3. **`std::dec`**: 将整数以十进制格式输出（这是默认格式）。例如：
   ```cpp
   int number = 255;
   std::cout << std::dec << number; // 输出为 "255"
   ```

4. **`std::oct`**: 将整数以八进制格式输出。例如：
   ```cpp
   int number = 255;
   std::cout << std::oct << number; // 输出为 "377"
   ```

5. **`std::left` 和 `std::right`**: 控制字段的对齐方式。例如：
   ```cpp
   std::cout << std::left << std::setw(10) << "Hello";  // 左对齐
   std::cout << std::right << std::setw(10) << "World"; // 右对齐
   ```

6. **`std::internal`**: 将符号放在左边而数字右对齐，通常用于负数或带符号的输出。例如：
   ```cpp
   std::cout << std::internal << std::setw(10) << -123;
   ```

7. **`std::setfill(char c)`**: 使用指定的字符填充宽度不足的空白。例如：
   ```cpp
   std::cout << std::setfill('*') << std::setw(10) << 42;
   // 输出为 "********42"
   ```

8. **`std::showbase`**: 和显示整数的进制基数（如十六进制的0x，八进制的0）。例如：
   
   ```cpp
   int number = 255;
   std::cout << std::showbase << std::hex << number; // 输出为 "0xff"
   ```
   
9. **`std::nouppercase`**: 禁止十六进制数字的大写输出。例如：
   
   ```cpp
   int number = 255;
   std::cout << std::nouppercase << std::hex << number; // 输出为 "ff"
   ```
   
10. **`std::noshowbase`**: 禁止显示整数的进制基数。例如：
    ```cpp
    int number = 255;
    std::cout << std::noshowbase << std::hex << number; // 输出为 "ff"
    ```



### 组合使用

- 组合方式1

```cpp
int number = 255;
std::cout << std::setfill('0') << std::setw(10) 
          << std::showbase << std::uppercase << std::hex << number;
```
输出结果为 `"0X000000FF"`。



- 组合方式2:

  通过 `std::setiosflags` 和 `std::resetiosflags` 函数来组合和取消。例子如下：

```cpp
std::cout << std::setiosflags(std::ios::fixed | std::ios::showpoint)
          << std::setprecision(2) << value;
```

