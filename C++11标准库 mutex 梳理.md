[toc]

## \<mutex\>

### std::call_once函数

多线程操作过程中，std::call_once()内部的回调函数只会被执行一次

> 在某些特定情况下，某些函数只能在多线程环境下调用一次，比如：要初始化某个对象，而这个对象只能被初始化一次，就可以使用std::call_once()来保证函数在多线程环境下只能被调用一次。使用call_once()的时候，需要一个once_flag作为call_once()的传入参数.

函数原型:

```
// 定义于头文件 <mutex>,属于std
template< class Callable, class... Args >
void call_once( std::once_flag& flag, Callable&& f, Args&&... args );
```

- flag：once_flag类型的对象，要保证这个对象能够被多个线程同时访问到
- f：回调函数，可以传递一个有名函数地址，也可以指定一个匿名函数
- args：作为实参传递给回调函数

#### 例程:使用call_once实现的单例模式

```
#include<iostream>
#include<thread>
#include<mutex>

std::once_flag g_flag;

class Singleton {
public:
    Singleton(const Singleton& s) = delete;
    Singleton& operator=(const Singleton&s) = delete;
    static Singleton* GetInstance() {
        std::call_once(g_flag,[](){ std::cout<<"do once:"<<std::this_thread::get_id()<<"\n"; _instance = new Singleton; }); //成员函数中lambda默认隐式捕获this
        std::cout<<std::this_thread::get_id()<<"\n";
        return _instance;
    }

private:
    Singleton(){};
    static Singleton* _instance;
};
Singleton* Singleton::_instance = nullptr;


int main() {
   std::thread t1(Singleton::GetInstance);
   std::thread t2(Singleton::GetInstance);
   std::thread t3(Singleton::GetInstance);
    
    t1.join();
    t2.join();
    t3.join();
    return 0 ;
}
```



![image-20240628223855820](https://img2023.cnblogs.com/blog/2921710/202407/2921710-20240713185143554-636222465.png)

注意:call_once在执行时会阻塞其他线程

> `std::call_once`通过内部互斥器和原子操作来确保只有一个线程能够进入初始化代码区域。这意味着当多个线程等待执行某个需初始化的函数时，第一个获得执行权的线程将会执行该函数，而其他线程则会等待。这种机制确保了初始化过程的线程安全性，避免了竞态条件的发生。同时，`std::call_once`还处理了异常情况，如果在函数执行过程中抛出异常，则`call_once`不会认为该函数已经成功执行。这样，后续的线程仍然可以尝试进入执行，从而保证函数能够成功被执行一次。

![image-20240629104811181](https://img2023.cnblogs.com/blog/2921710/202407/2921710-20240713185143159-177102088.png)

补充:

call_once实现的过程中使用了lambda表达式,而其中lambda表达式并未指明捕获方式,一般来说未指明时lambda不会捕获任何的外部变量.

但是为什么在成员函数中lambda不指定捕获类型却能捕获到成员变量呢?因为**lambda在成员函数中隐式地捕获了当前对象的this指针**,因此能够访问到成员变量.

这种便利也带来了一定的风险，因为在没有显式捕获的情况下，lambda表达式对成员变量的修改可能会影响到对象的状态,这点需要程序员注意.



### std::mutex类 -- 独占互斥锁

解决多线程数据混乱的方案就是进行线程同步,最常用的就是互斥锁.

C++11中一共提供了四种互斥锁：

1. std::mutex：独占的互斥锁，不能递归使用
2. std::timed_mutex：带超时的独占互斥锁，不能递归使用
3. std::recursive_mutex：递归互斥锁，不带超时功能
4. std::recursive_timed_mutex：带超时的递归互斥锁

互斥锁在有些资料中也被称之为互斥量.

这些锁类都是不可拷贝,不可赋值

#### 成员函数

1. void lock();

   常用,阻塞上锁

2. bool try_lock();

   try_lock,尝试上锁,线程不阻塞,返回值能够用于程序逻辑分支,即遇到锁后可以做其他事.

   例程:

   ```
   #include<iostream>
   #include<thread>
   #include<cstdlib>
   #include<mutex>
   
   int g_val;
   std::mutex mtx;
   
   void other() {
       std::cout << "do other thing" << "\n";
   }
   
   void func() {
       while (true) {
           _sleep(100);
           while (mtx.try_lock() == false)
               other();
           std::cout << g_val++ << "\n";
           mtx.unlock();
       }
   }
   
   int main() {
       std::thread t1(func);
       std::thread t2(func);
       std::thread t3(func);
   }
   ```

   ![image-20240629124646492](https://img2023.cnblogs.com/blog/2921710/202407/2921710-20240713185142788-550847285.png)

3. void unlock();

   解锁

   注意,必须要上了锁的对象才有资格解锁.其他对象解锁程序会奔溃(mutex对象内会记录上锁线程的id,解锁时会进行id判定.)

   ![image-20240629125133502](https://img2023.cnblogs.com/blog/2921710/202407/2921710-20240713185142329-1273591809.png)

死锁:

- 一个执行流连续上次上同一把锁.
- 多个执行流互相等待对方解锁



### std::recursive_mutex类 -- 递归互斥锁

>  递归,就是可以多次使用

递归互斥锁std::recursive_mutex允许同一线程多次获得互斥锁，可以用来解决同一线程需要多次获取互斥量时死锁的问题

#### 使用注意:

- 使用递归互斥锁的场景往往都是可以简化的，使用递归互斥锁很容易放纵复杂逻辑的产生，从而导致bug的产生
- 递归互斥锁比独占互斥锁实现复杂,考虑更多,因此比非递归互斥锁效率要低一些。
- 递归互斥锁虽然允许同一个线程多次获得同一个互斥锁的所有权，但最大次数并未具体说明，一旦超过一定的次数，就会抛出std::system错误。

#### 描述:

递归互斥锁,即同一个线程可以多次对该锁进行加锁操作.

形象来看,就是递归互斥锁就是一扇门,线程是主人,一扇门对应一把钥匙,主人能带着钥匙离开,能够使用钥匙重复开门.具有记忆功能

而独占互斥锁没有记忆功能,不能带走钥匙,用完离开后锁就不认人了.

### std::timed_mutex类 -- 超时互斥锁

std::timed_mutex是超时独占互斥锁,体现在获取互斥锁资源时增加了两个超时等待的函数.

因为不知道获取锁资源需要等待多长时间，为了保证不一直等待下去，设置了一个超时时长，超时后线程就可以解除阻塞去做其他事情了。



#### 描述:

  和mutex的try_lock一样,只是try_lock不阻塞,try_lock_for会阻塞一定时长.



#### 成员函数:

```
void lock();
bool try_lock();
void unlock();


// std::timed_mutex比std::_mutex多出的两个成员函数
template <class Rep, class Period>
  bool try_lock_for (const chrono::duration<Rep,Period>& rel_time);

template <class Clock, class Duration>
  bool try_lock_until (const chrono::time_point<Clock,Duration>& abs_time);
```

- try_lock_for函数是当线程获取不到互斥锁资源的时候，让线程阻塞一定的时间长度
- try_lock_until函数是当线程获取不到互斥锁资源的时候，让线程阻塞到某一个指定的时间点



try_lock_for/until的返回值：

  当得到互斥锁的所有权之后，函数会马上解除阻塞，返回true，如果阻塞的时长用完或者到达指定的时间点之后，函数也会解除阻塞，返回false



### std::recursive_timed_mutex类

关于递归超时互斥锁std::recursive_timed_mutex的使用方式和std::timed_mutex一样,也是5个成员函数，只不过它可以允许一个线程多次获得互斥锁所有权，而std::timed_mutex只允许线程获取一次互斥锁所有权。

另外，递归超时互斥锁std::recursive_timed_mutex也拥有和std::recursive_mutex一样的弊端，不建议频繁使用。



### std::lock_guard模板类

RAII技术,守护锁.



#### 函数原型:

```
// 类的定义，定义于头文件 <mutex>
template< class Mutex >
class lock_guard;

// 常用构造函数
explicit lock_guard( mutex_type& m );

lock_guard(const lock_guard&) = delete;
lock_guard& operator=(const lock_guard&) = delete;
```



### std::unique_lock模板类

前面讲到lock_guard能够RAII,但是仅限于简单的加锁解锁场景,因为lock_guard本身是不支持拷贝的,如果需要函数传参,返回等情况下,lock_guard就无能为力了.

unique_lock就是为此而实现,一般与条件变量搭配使用.

#### 成员方法
使用方法与mutex基本一样
```
1. lock()
2. try_lock()
3. try_lock_for()	
4. try_lock_until()
5. unlock()	
```

