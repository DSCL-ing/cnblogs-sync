#  memset()

## 描述

C 库函数 **void \*memset(void \*str, int c, size_t n)** 用于将一段内存区域设置为指定的值。

memset() 函数将指定的值 c 复制到 str 所指向的内存区域的前 n 个字节中，这可以用于将内存块清零或设置为特定值。

在一些情况下，需要快速初始化大块内存为零或者特定值，memset() 可以提供高效的实现。

在清空内存区域或者为内存区域赋值时，memset() 是一个常用的工具函数。

## 声明

void *memset(void *ptr, int value, size_t num);

下面是 memset() 函数的声明。

```
void *memset(void *str, int c, size_t n)
```

## 参数

- **str** -- 指向要填充的内存区域的指针。
- **c** -- 要设置的值，通常是一个无符号字符。
- **n** -- 要被设置为该值的字节数。

## 返回值

该值返回一个指向存储区 str 的指针。

## 注意事项

- `memset()` 并不对指针 `ptr` 指向的内存区域做边界检查，因此使用时需要确保 `ptr` 指向的内存区域足够大，避免发生越界访问。
- `memset()` 的第二个参数 `value` 通常是一个 `int` 类型的值，但实际上只使用了该值的低8位。这意味着在范围 `0` 到 `255` 之外的其他值可能会产生未定义的行为。
- `num` 参数表示要设置的字节数，通常是通过 `sizeof()` 或其他手段计算得到的。



## 模拟实现

```
void* my_memset(void* ptr, int value, size_t num)
{
	assert(ptr);
	void* ret = ptr;
	
	//循环逐字节拷贝
	while (num--)
	{
		*(char*)ptr = (char)value;
		++(char*)ptr;
	}
	return ret;
}
```

