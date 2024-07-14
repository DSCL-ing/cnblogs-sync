[TOC]

## <condition_variable>

> 条件变量是C++11提供的另外一种用于等待的同步机制，它能阻塞一个或多个线程，直到收到另外一个线程发出的通知或者超时时，才会唤醒当前阻塞的线程。条件变量需要和互斥量配合起来使用，C++11提供了两种条件变量：

>条件变量为什么叫变量?
>
>在计算机科学和并发编程中，条件变量是一种用于线程同步的**机制**，它们之所以被称为“变量”，主要有以下几个原因：
>
>1. **状态表示**：条件变量本质上是一个表示状态的对象，这个状态可以被其他线程检查和修改。变量这个词意味着它是一个可以存储和表示某种状态的实体。
>2. **动态性**：条件变量的状态是动态变化的。线程可以等待一个条件变量的某个状态，然后在条件满足时被唤醒。这种动态变化的特性使得它像一个普通的变量，可以在程序运行时不断变化。
>3. **操作性**：条件变量可以通过特定的操作来改变其状态。通常，条件变量有两个主要操作：等待（wait）和通知（signal或broadcast）。这些操作类似于对普通变量进行的读写操作，只不过这些操作影响的是线程的执行流。
>4. **命名约定**：在许多编程语言和库中，条件变量被设计为一种数据结构或对象，并且通常以变量的形式存在于代码中。为了与其他同步机制（如互斥锁、信号量等）区分开来，并保持命名的一致性，使用“变量”这个词来描述它们。
>
>综上所述，条件变量被称为“变量”主要是因为它们具有状态表示的特性，可以通过操作改变状态，并在程序中以变量的形式出现，从而帮助线程实现同步。

### condition_variable类

condition_variable实现在<condition_variable>中,在VS2019中<mutex>内有声明,但是GCC没有.

#### 类方法

1. 线程等待(阻塞/休眠)函数

```
1. void wait (unique_lock<mutex>& lck); //,解锁,进入休眠,等待唤醒
2. template <class Predicate>
void wait (unique_lock<mutex>& lck, Predicate pred);
```

如果线程被该函数阻塞，这个线程会释放占有的互斥锁的所有权，当阻塞解除之后这个线程会重新得到互斥锁的所有权，继续向下执行

- **condition_variable的wait()的lck参数无法直接使用互斥锁,必须搭配std::unique_lock<>类使用**

- wait的第二重载方法中,Pred参数是一个模板参数,用于接收返回值为bool函数或函数对象/lambda表达式.

  每次唤醒在wait队列内休眠的线程时,线程都会检查Rred的值,只有为真时才会继续往下执行,否则继续休眠

  - Pred值为假,线程进入休眠,等待唤醒
  - Pred值为真,线程继续向下执行.



2. 线程通知/唤醒函数 -- (notify:通知)

```
1. void notify_one() noexcept; //在wait队列中唤醒一个
2. void notify_all() noexcept; //全部唤醒
```



3. wait_for和wait_until
   wait_for()函数,waitr_until()函数都和wait()的功能是一样的，只不过多了一个阻塞时长，假设阻塞的线程没有被其他线程唤醒，当阻塞时长用完之后，线程就会自动解除阻塞，继续向下执行。

```
wait_for
a.
	template <class Clock, class Duration>
	cv_status wait_until (unique_lock<mutex>& lck,
	                      const chrono::time_point<Clock,Duration>& abs_time);
b.
	template <class Clock, class Duration, class Predicate>
	bool wait_until (unique_lock<mutex>& lck,
	                 const chrono::time_point<Clock,Duration>& abs_time, Predicate pred);
                 
wait_until
a.
	template <class Clock, class Duration>
	cv_status wait_until (unique_lock<mutex>& lck,
	                      const chrono::time_point<Clock,Duration>& abs_time);
b.
	template <class Clock, class Duration, class Predicate>
	bool wait_until (unique_lock<mutex>& lck,
	                 const chrono::time_point<Clock,Duration>& abs_time, Predicate pred);
```





#### 生产者消费者模型 -- 阻塞队列

##### 单条件变量版

运用:`wait(lck), wait(lck,Pred), notify_all(), condition_variable`

```
#include<iostream> //std::cout 
#include<thread> //std::thread
#include<mutex> //std::mutex, std::unique_lock, std::lock_guard
#include<queue> //std::queue
#include<condition_variable> //std::condition_variable
#include<functional> //std::bind

//设计概要
/*
定长队列+自动管理增删
同步 == 独占互斥锁
- 生产者生产 == 增加 -- bool?生产成功返回true,放不下false --- (生产者一定知道自己已生产)生产:通知消费者
- 消费者消费 == 删除 -- bool?消费成功返回true,没有了false --- (同理)       消费:通知生产者
*/

//单条件变量版 --  简化逻辑
template<class T>
class  BlockQueue {

public:
    bool isEmpty() {
        return _bqueue.empty();
    }
    bool isFull() {
        return _bqueue.size() == _capacity;
    }
    //AddTask
    void Push(const T& t) {
        std::unique_lock<std::mutex> lck(_mtx);
        while (isFull()) {
            cv.wait(lck);
        }
        _bqueue.push(t);
        std::cout << "模拟插入数据..." << t << "\n";
        lck.unlock();
        cv.notify_all();
    }
    //DelTask
    void Pop() {
        std::unique_lock<std::mutex>lck(_mtx);
        cv.wait(lck, [this]() {
            bool flag = isEmpty(); //为空时休眠
            return !flag; //增加可读性的策略
            });
        T t = _bqueue.front();
        _bqueue.pop();
        std::cout << "模拟处理数据..." << t << "\n";
        lck.unlock();
        cv.notify_all();
    }

private:
    std::queue<T> _bqueue;
    std::mutex _mtx;
    std::condition_variable cv;  //必须搭配all
    size_t _capacity = 5; //队列定长大小
    
    //std::condition_variable _not_full;  //非满时唤醒生产者
    //std::condition_variable _not_empty; //非空时唤醒消费者
};


int main() {
    BlockQueue<int> tq;
    auto produce = std::bind(&BlockQueue<int>::Push, &tq, std::placeholders::_1);
    auto consume = std::bind(&BlockQueue<int>::Pop, &tq);
    std::thread t1[5];
    std::thread t2[5];
    for (int i = 0; i < 5; i++) {
        t1[i] = std::thread(produce, i);
        t2[i] = std::thread(consume);
    }

    for (int i = 0; i < 5; i++) {
        t1[i].join();
        t2[i].join();
    }

    return 0;
}
```



### condition_variable_any模板类

condition_variable_any实现在<condition_variable>中,在VS2019中<mutex>内有声明,但是GCC没有.

#### 区别

condition_variable_any与condition_variable的区别是

- condition_variable_any可以直接给wait()函数传互斥锁std::mutex、std::timed_mutex、std::recursive_mutex、std::recursive_timed_mutex四种.
- condition_variable在wait()时必须搭配unique_lock使用

#### 优缺点

- condition_variable实现简单,效率更高.缺点是只能搭配`std::mutex和unique_lock`使用
- condition_variable_any更加灵活,但是实现复杂,效率会低一些,仅仅是低一些,对于现代计算机,这点效率损失不成问题.