---
layout: post
title:  "Python之threading模块"
date:   2015-07-26 21:20:05
categories: python
excerpt: 
---

* content
{:toc}


### 学习一下threading模块

---

#### 进程和线程：

* 对于操作系统来说，一个任务就是一个进程（Process），比如打开一个浏览器就是启动一个浏览器进程，打开一个记事本就启动了一个记事本进程，打开两个记事本就启动了两个记事本进程。
* 在一个进程内部，要同时干多件事，就需要同时运行多个“子任务”，我们把进程内的这些“子任务”称为线程（Thread）。

---

> Python的标准库提供了两个模块：_thread和threading，_thread是低级模块，threading是高级模块，对_thread进行了封装。绝大多数情况下，我们只需要使用threading这个高级模块。

---

#### threading 模块

* 启动一个线程就是把一个函数传入并创建Thread实例，然后调用start()开始执行：

        import time, threading

        # 新线程执行的代码:
        def loop():
            print('thread %s is running...' % threading.current_thread().name)
            n = 0
            while n < 5:
                n = n + 1
                print('thread %s >>> %s' % (threading.current_thread().name, n))
                time.sleep(1)
            print('thread %s ended.' % threading.current_thread().name)

        print('thread %s is running...' % threading.current_thread().name)
        t = threading.Thread(target=loop, name='LoopThread')
        t.start()   #target为要运行的函数，args为函数的参数，name为线程的名字
        t.join()#等到线程结束
        print('thread %s ended.' % threading.current_thread().name

        执行结果：
        
        thread MainThread is running...
        thread LoopThread is running...
        thread LoopThread >>> 1
        thread LoopThread >>> 2
        thread LoopThread >>> 3
        thread LoopThread >>> 4
        thread LoopThread >>> 5
        thread LoopThread ended.
        thread MainThread ended.
        
* 由于任何进程默认就会启动一个线程，我们把该线程称为主线程，主线程又可以启动新的线程，Python的threading模块有个current_thread()函数，它永远返回当前线程的实例。主线程实例的名字叫MainThread，子线程的名字在创建时指定，我们用LoopThread命名子线程。名字仅仅在打印时用来显示，完全没有其他意义，如果不起名字Python就自动给线程命名为Thread-1，Thread-2……

* 更通用的方法是创建一个Thread的子类，单独放在一个模块中:

        import threading

        class MyThread(threading.Thread):
            def __init__(self, func, args, name=''):
                threading.Thread.__init__(self)
                self.name = name 
                self.func = func
                self.args = args
                
            def getResult(self):
                return self.res
                
            def run(self):
                self.res = self.func(*self.args)
                
---

 > * 多线程和多进程最大的不同在于，多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响，而多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了。这时就得给线程加把锁，由于锁只有一个，无论多少线程，同一时刻最多只有一个线程持有该锁，所以，不会造成修改的冲突。
 
* 创建一个锁就是通过threading.Lock()来实现

        balance = 0
        lock = threading.Lock()

        def change_it(n):
            # 先存后取，结果应该为0:
            global balance
            balance = balance + n
            balance = balance - n
        
        def run_thread(n):
            for i in range(100000):
                # 先要获取锁:
                lock.acquire()
                try:
                    # 放心地改吧:
                    change_it(n)
                finally:
                    # 改完了一定要释放锁:
                    lock.release()

* 当多个线程同时执行lock.acquire()时，只有一个线程能成功地获取锁，然后继续执行代码，其他线程就继续等待直到获得锁为止。

> 获得锁的线程用完后一定要释放锁，否则那些苦苦等待锁的线程将永远等待下去，成为死线程。所以我们用try...finally来确保锁一定会被释放。

---

#### 多进程 vs.多线程

* 多进程模式最大的优点就是稳定性高，因为一个子进程崩溃了，不会影响主进程和其他子进程。
* 多进程模式的缺点是创建进程的代价大，在Unix/Linux系统下，用fork调用还行，在Windows下创建进程开销巨大。另外，操作系统能同时运行的进程数也是有限的，在内存和CPU的限制下，如果有几千个进程同时运行，操作系统连调度都会成问题。

* 多线程模式通常比多进程快一点，但是也快不到哪去。在Windows下，多线程的效率比多进程要高。多线程模式致命的缺点就是任何一个线程挂掉都可能直接造成整个进程崩溃，因为所有线程共享进程的内存。

---

#### 分布式进程

* Python的multiprocessing模块不但支持多进程，其中managers子模块还支持把多进程分布到多台机器上。一个服务进程可以作为调度者，将任务分布到其他多个进程中，依靠网络通信。由于managers模块封装很好，不必了解网络通信的细节，就可以很容易地编写分布式多进程程序。



> 参考文章：[廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431929340191970154d52b9d484b88a7b343708fcc60000)

---

### 结语：

坚持每天进步一点点...

---