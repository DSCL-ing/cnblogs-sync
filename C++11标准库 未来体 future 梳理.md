[TOC]

## \<future\>

标准库提供了一些工具来获取异步任务（即在单独的线程中启动的函数）的返回值，并捕捉其所抛出的异常。这些值在*共享状态*中传递，其中异步任务可以写入其返回值或存储异常，而且可以由持有该引用该共享态的 [std::future](https://zh.cppreference.com/w/cpp/thread/future) 或 [std::shared_future](https://zh.cppreference.com/w/cpp/thread/shared_future) 实例的线程检验、等待或是操作这个状态。

[并发支持库 (C++11 起) - cppreference.com](https://zh.cppreference.com/w/cpp/thread#future)

### future模板类

概念:

[future - C++ Reference (cplusplus.com)](https://legacy.cplusplus.com/reference/future/future/)

future类用于给线程提供一个方便管理和获取异步操作的结果的功能.

这个类允许将一个异步操作封装为一个未来可获取结果的对象，从而实现了高效的并发编程。

注意:

1. future本身没有set方法,必须搭配promise进行使用.
2. future对象要在需要传递结果的线程间可见(能够看见同一个future) -- 引用传递

#### 成员函数:

- 定义

```
template< class T > class future;
template< class T > class future<T&>;
template<>          class future<void>; //空 -- 不需要返回结果的future.
```

- 构造函数

```
future() noexcept; 						//空对象
future( future&& other ) noexcept;  	//只允许移动
future( const future& other ) = delete; //禁止拷贝
```

- operator=

```
future& operator=( future&& other ) noexcept; 		//只允许移动
future& operator=( const future& other ) = delete;  //禁止拷贝
```

- get

取出future对象内保存的数据.是阻塞函数,如果目标future所在线程一直没有set_value,则会一直等待,直到set_value,成功获取到数据后解除阻塞.

```
//三个重载函数,根据future的数据类型自动决定
T get();
T& get();
void get(); //空future对象没有东西传的时候使用
```

- wait

阻塞等待,直到目标线程set_value.和get类似,只是没有返回值.

```
void wait() const;
```

- wait_for & wait_until

阻塞等待一段时长和等待到某时刻.之后会返回future的状态

```
template< class Rep, class Period >
std::future_status wait_for( const std::chrono::duration<Rep,Period>& timeout_duration ) const;

template< class Clock, class Duration >
std::future_status wait_until( const std::chrono::time_point<Clock,Duration>& timeout_time ) const;
```

| 常量                    | 解释                                         |
| ----------------------- | -------------------------------------------- |
| future_status::deferred | 子线程中的任务函仍未启动(配合std::async生效) |
| future_status::ready    | 子线程中的任务已经执行完毕，结果已就绪       |
| future_status::timeout  | 子线程中的任务正在执行中，指定等待时长已用完 |

- share

返回多个线程共享的future对象,即[shared_future](https://legacy.cplusplus.com/reference/future/shared_future/)模板类

```
shared_future<T> share();
```

返回值:`shared_future<T>(std::move(*this))`





### promise类

promise类内封装了future对象,用于管理和使用future对象.

- 定义

```
template< class R > class promise;
template< class R > class promise<R&>;
template<>          class promise<void>;
```

- 构造函数

```
promise();
promise( promise&& other ) noexcept; 	  //
promise( const promise& other ) = delete; //禁止拷贝
```

- get_future

在`std::promise`类内部管理着一个`future`类对象，调用`get_future()`就可以得到这个`future`对象了

```
std::future<T> get_future();
```



- set_value系列

| [set_value](https://zh.cppreference.com/w/cpp/thread/promise/set_value) | 设置结果为指定值 (公开成员函数)                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [set_value_at_thread_exit](https://zh.cppreference.com/w/cpp/thread/promise/set_value_at_thread_exit) | 设置结果为指定值，同时仅在线程退出时分发提醒 (公开成员函数)  |
| [set_exception](https://zh.cppreference.com/w/cpp/thread/promise/set_exception) | 设置结果为指示异常 (公开成员函数)                            |
| [set_exception_at_thread_exit](https://zh.cppreference.com/w/cpp/thread/promise/set_exception_at_thread_exit) | 设置结果为指示异常，同时仅在线程退出时分发提醒 (公开成员函数) |

1. set_value

存储要传出的 `value` 值，并立即让状态就绪，这样数据被传出其它线程就可以得到这个数据了。重载的第四个函数是为`promise<void>`类型的对象准备的。

```
void set_value( const R& value );
void set_value( R&& value );
void set_value( R& value );
void set_value();
```

2. set_value_at_thread_exit

存储要传出的 value 值，但是不立即令状态就绪。在当前线程退出时，子线程资源被销毁，再令状态就绪。

```
void set_value_at_thread_exit( const R& value );
void set_value_at_thread_exit( R&& value );
void set_value_at_thread_exit( R& value );
void set_value_at_thread_exit();
```



#### promise的使用例程:

```
#include <iostream>
#include <thread>
#include <functional>
#include <chrono>
#include <future>

void func(std::promise<std::string>& pro) {
    std::this_thread::sleep_for(std::chrono::seconds(1));
    pro.set_value("hello world");
    std::this_thread::sleep_for(std::chrono::seconds(1));
}

int main() {

    std::promise<std::string> pro;
    std::thread t1(func, std::ref(pro));

    auto fut = pro.get_future();
    while (true) {
        bool flag = false;
        std::future_status ret = fut.wait_for(std::chrono::seconds(1));
        switch (ret) {
        case std::future_status::deferred:              //只有async函数才有效
            std::cout << "异步线程还未启动" << "\n";
            break;
        case std::future_status::ready:
            std::cout << "结果已就绪" << "\n";
            flag = true;
            break;
        case std::future_status::timeout:
            std::cout << "等待超时" << "\n";
            break;
        default:
            std::cout << "结果异常" << "\n";
        }
        if (flag == true) {
            break;
        }
        std::cout << "do something..." << "\n";
        std::this_thread::sleep_until(std::chrono::system_clock::now() + std::chrono::seconds(1));
    }
    std::cout << "输出结果::" << fut.get() << "\n";

    t1.join();
    return 0;
}
```











### packaged_task模板类

packaged_task包装了一个可调用对象包装器类对象,内部管理着一个future对象.

与promise用法类似.区别是promise只管理着future对象,而packaged_task还具备可调用对象的功能.

- 定义

```
template< class > class packaged_task;
template< class R, class ...Args >
class packaged_task<R(Args...)>; 		//必须显式指定模板参数
```

- 构造函数

```
packaged_task() noexcept; //不可传给子线程,那有什么用?

template <class F>
explicit packaged_task( F&& f );

packaged_task( const packaged_task& ) = delete;  //禁止拷贝

packaged_task( packaged_task&& rhs ) noexcept;
```



- get_future

和promise类似

```
std::future<R> get_future();
```



#### 例程:

```
#include <iostream>
#include <thread>
#include <functional>
#include<chrono>
#include<future>
#include<string>

class Base {
public:
    using funcptr = std::string(*)(std::string, int num);

public:
    std::string operator()(std::string msg) {
        return std::string("operator(): " + msg);
    }
    operator funcptr() { //若类型转换成函数,可以通过仿函数方式直接使用对象()进行调用;但如果与operator()的函数指针类型相同,则只会使用operator(),而不会调用operator T(),即operator T()被隐藏. 
        return Showmsg; //为了区分两者,可使用不同的函数指针类型.
    }
    static std::string Showmsg(std::string msg, int num) {
        return std::string("showmsg(): " + msg + std::to_string(num));
    }
    std::string func(std::string msg) {
        return std::string("func(): " + msg);
    }
private:
};

int main() {
    std::cout << Base()("hello") << "\n";       //operator ()()
    std::cout << Base()("hello", 1) << "\n";   //operator T()()
    std::cout << Base() << "\n";                //operator T()

    Base b;
    std::packaged_task<std::string(std::string)> p_task1(b);
    std::packaged_task<std::string(std::string, int)> p_task2(b);
    std::packaged_task<std::string(std::string, int)> p_task3(std::bind(Base::Showmsg, "hello", 3));  //最好的用法,是让bind自动推导模板参数 
    std::packaged_task<std::string(std::string)> p_task4(std::bind(&Base::func, &b,std::placeholders::_1));

    p_task1("p_tast1 hello");
    p_task2("p_tast2 hello", 2);
    p_task3("p_task3 hello", 3);
    p_task4("p_task4 hello");

    std::future<std::string> fut1 = p_task1.get_future();
    std::future<std::string> fut2 = p_task2.get_future();
    std::future<std::string> fut3 = p_task3.get_future();
    std::future<std::string> fut4 = p_task4.get_future();

    puts("");
    std::cout << fut1.get() << "\n";
    std::cout << fut2.get() << "\n";
    std::cout << fut3.get() << "\n";
    std::cout << fut4.get() << "\n";

    return 0;
}
```

![image-20240712155251859](https://img2023.cnblogs.com/blog/2921710/202407/2921710-20240713185139542-426444492.png)





### async模板函数

async是C++11提供用于简化并发编程的模板函数,async函数同样封装有future对象,能够将异步任务的结果(return_value)保存在内部的future对象中,且该future对象为async的返回值,便于提取异步任务的结果.

>  函数模板 `std::async` 异步地运行函数 f，并返回最终将保有该函数调用结果的 std::future。

```
template< class Function, class... Args>
std::future<std::result_of_t<std::decay_t<Function>(std::decay_t<Args>...)>>
    async( Function&& f, Args&&... args );

template< class Function, class... Args >
std::future<std::result_of_t<std::decay_t<Function>(std::decay_t<Args>...)>>
    async( std::launch policy, Function&& f, Args&&... args );
```

参数:

f：可调用对象，这个对象在子线程中被作为任务函数使用

Args：传递给 f 的参数（实参）

policy：可调用对象·f的执行策略

| 策略                                      | 说明                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| std::launch::async                        | 调用async函数时创建新的线程执行任务函数                      |
| std::launch::deferred                     | 调用async函数时不执行任务函数，直到调用了future的get()或者wait()时才执行任务（这种方式不会创建新的线程） |
| std::launch::async\|std::launch::deferred | 自动选择异步或延迟执行任务，具体取决于系统负载和资源可用性等因素。 |

注:policy == std::launch::async|std::launch::deferred时,和默认不带policy参数的async行为是完全一样的,都是未定义的.

> 此时如果 policy 是 [std::launch::async](http://zh.cppreference.com/w/cpp/thread/launch) | [std::launch::deferred](http://zh.cppreference.com/w/cpp/thread/launch) 或者设置了额外位，那么就会回退到推迟调用或其他由实现定义的策略。
>
> [std::async - cppreference.com](https://zh.cppreference.com/w/cpp/thread/async)



优点:

使用async()函数，是多线程操作中最简单的一种方式，不需要自己创建线程对象，并且可以得到子线程函数的返回值。



#### 例程:

```
#include <iostream>
#include <thread>
#include <functional>
#include<chrono>
#include<future>
#include<string>

int func(std::string msg,int x) {
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::cout<<"thread_id: "<<std::this_thread::get_id() << "  msg: " << msg << "\n";
    return x; //返回给async中的future对象.
}

int main() {
    std::cout<<"main thread_id: "<<std::this_thread::get_id()<<"\n";
    std::future<int> fut1 = std::async(std::launch::async,func,"fut1",1);           //立即创建新一个线程执行
    std::future<int> fut2 = std::async(std::launch::deferred,func,"fut2",2);        //fut.get()调用后,在主线程执行
    auto fut3 = std::async(std::launch::async|std::launch::deferred,func,"fut3",3); //交由系统策略自动决定.
    
    std::cout<<fut1.get()<<"\n";
    std::cout<<fut2.get()<<"\n";
    std::cout<<fut3.get()<<"\n";

    return 0;
}
```

![image-20240712200349884](https://img2023.cnblogs.com/blog/2921710/202407/2921710-20240713185138594-548151646.png)





### shared_future模板类

>  引用:[C++11中std::shared_future的使用_std shared future-CSDN博客](https://blog.csdn.net/fengbingchun/article/details/104118831)
>
>  [std::shared_future - cppreference.com](https://zh.cppreference.com/w/cpp/thread/shared_future)



>C++11中的std::shared_future是个模板类。与std::future类似，std::shared_future提供了一种访问异步操作结果的机制；
>
>不同于std::future，std::shared_future允许多个线程等待同一个共享状态；
>
>不同于std::future仅支持移动操作，std::shared_future既支持移动操作也支持拷贝操作，而且多个shared_future对象可以引用相同的共享状态。
>
>std::shared_future还允许一旦共享状态就绪就可以多次检索共享状态下的值(A shared_future object behaves like a future object, except that it can be copied, and that more than one shared_future can share ownership over their end of a shared state. They also allow the value in the shared state to be retrieved multiple times once ready)。
>
>std::shared_future对象可以通过std::future对象隐式转换，也可以通过显示调用std::future::share显示转换，在这两种情况下，原std::future对象都将变得无效。
>
>共享状态(shared state)的生存期至少要持续到与之关联的最后一个对象被销毁为止。与std::future不同，通过shared_future::get检索的值不会释放共享对象的所有权。
>
>当你需要具有std::future的多个有效拷贝时会用到std::shared_future；或者多个使用者使用std::future时也会用到std::shared_future。

1. 构造函数：

   (1).不带参数的默认构造函数，此对象没有共享状态，因此它是无效的，但是它可以被赋予一个有效值；

   (2).拷贝构造：与const shared_future& x具有相同的共享状态，并与之共享所有权；

   (3).支持移动构造。

2. 析构函数：销毁shared_future对象，它是异常安全的。如果对象有效(即它可以访问共享状态)，则将其与对象接触关联；如果它是与共享状态关联的唯一对象，则共享对象本身也将被销毁。

3. get函数：

   (1).当共享状态就绪时，返回存储在共享状态中的值的引用(或抛出异常)。

   (2).如果共享状态尚未就绪(即provider提供者尚未设置其值或异常)，则该函数将阻塞调用的线程直到就绪。

   (3).当共享状态就绪后，则该函数将解除阻塞并返回(或抛出)，但与future::get不同，不会释放其共享状态，允许其它shared_future对象也访问存储的值。

   (4).std::shared_future<void>::get()不返回任何值，但仍等待共享状态就绪才返回或抛出。

4. operator=：

   (1).拷贝赋值：该对象与const shared_future& rhs关联到相同的共享状态，并与之共享所有权；

   (2).移动赋值：该对象获取shared_future&& rhs的共享状态，rhs不再有效。

5. valid函数：检查共享状态的有效性，返回当前的shared_future对象是否与共享状态关联。

6. wait函数：

   (1).等待共享状态就绪。

   (2).如果共享状态尚未就绪(即提供者尚未设置其值或异常)，则该函数将阻塞调用的线程直到就绪。

   (3).当共享状态就绪后，则该函数将解除阻塞并void返回。

7. wait_for函数：

   (1).等待共享状态在指定的时间内(time span)准备就绪。

   (2). 如果共享状态尚未就绪(即提供者尚未设置其值或异常)，则该函数将阻塞调用的线程直到就绪或已达到设置的时间。

   (3).此函数的返回值类型为枚举类future_status。此枚举类有三种label：ready：共享状态已就绪；timeout：在指定的时间内未就绪；deferred：共享状态包含了一个延迟函数(deferred function)。

   (4).如果共享状态包含了一个延迟函数，则该函数不会阻塞，立即返回一个future_status::deferred值。

8. wait_until函数：

   (1). 等待共享状态在指定的时间点(time point)准备就绪。

   (2). 如果共享状态尚未就绪(即提供者尚未设置其值或异常)，则该函数将阻塞调用的线程直到就绪或已达到指定的时间点。

   (3).此函数的返回值类型为枚举类future_status。

   (4).如果共享状态包含了一个延迟函数，则该函数不会阻塞，立即返回一个future_status::deferred值。

