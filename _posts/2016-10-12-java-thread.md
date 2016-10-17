---
layout: post
title:  "Java线程间通信"
date:   2016-10-12 16:44:05
categories: Java
excerpt: 
---

* content
{:toc}

---

#### Java线程间通信

* 程序示例：

        //生产者和消费者问题
        public class LearnThreadCommunication {

            public static void main(String[] args) {
                Box box = new Box();
                Producer producer = new Producer(box);
                Consumer consumer = new Consumer(box);
                producer.start();
                consumer.start();
            }
        }

        class Box{
            public int boxValue = 0;
        }


        //生产者
        class Producer extends Thread {
            private Box box;

            public Producer(Box box) {
                super();
                this.box = box;
            }

            public void run(){
                for(int i=1;i<6;i++){
                    synchronized (box) {
                        while(box.boxValue!=0){
                            try {
                                System.out.println("Producer：Box非空，生产者等待");
                                //释放对象锁，交出控制权，使本线程进入休眠等待状态
                                box.wait();
                            } catch (InterruptedException e) {
                                e.printStackTrace();
                            }
                        }
                        box.boxValue=i;
                        System.out.println("Producer:box中放入了"+i);
                        //唤醒该对象等待队列上的等待线程，归还控制权
                        box.notify();
                    }
                }
            }
        }


        //消费者
        class Consumer extends Thread {

            private Box box;

            public Consumer(Box box) {
                this.box = box;
            }

            public void run(){
                for(int i=1;i<6;i++){
                    synchronized (box) {
                        while(box.boxValue==0){
                            try {
                                System.out.println("Consumer:Box是空的，消费者等待");
                                box.wait();
                            } catch (InterruptedException e) {
                                e.printStackTrace();
                            }
                        }
                        System.out.println("Consumer:box中取出了"+i);
                        box.boxValue=0;
                        box.notify();
                    }
                }
            }
        }


* 测试结果：

        Consumer:Box是空的，消费者等待
        Producer:box中放入了1
        Producer：Box非空，生产者等待
        Consumer:box中取出了1
        Consumer:Box是空的，消费者等待
        Producer:box中放入了2
        Producer：Box非空，生产者等待
        Consumer:box中取出了2
        Consumer:Box是空的，消费者等待
        Producer:box中放入了3
        Producer：Box非空，生产者等待
        Consumer:box中取出了3
        Consumer:Box是空的，消费者等待
        Producer:box中放入了4
        Producer：Box非空，生产者等待
        Consumer:box中取出了4
        Consumer:Box是空的，消费者等待
        Producer:box中放入了5
        Producer：Box非空，生产者等待
        Consumer:box中取出了5

* 分析：

        wait():
        wait();
        wait(timeout);
        wait(timeout, nanos);
        当一个对象调用wait()方法的时候，当前作用于他的线程(ThreadA)就会暂时释放对象锁(monitor)，让其他线程(ThreadB)可以访问该线程。而ThreadA进入等待状态，直到wait()中设置的时间到了，或者其他线程重新唤醒ThreadA。

        notify():
        box.notify();
        box.notifyAll();
        notify()方法能够唤醒一个正在等待该对象的monitor的线程，当有多个线程都在等待该对象的monitor的话，则只能唤醒其中一个线程。如果需要唤醒所有线程，可以用notifyAll()方法。

* 注意：

    1.使用wait()和notify()方法时，当前线程必须拥有该对象的monitor
    2.一个线程被唤醒并不代表它立刻就获得了该对象的monitor,只有在当前线程释放monitor以后，它才能够获得。任一时刻，一个兑现的monitor只能被一个线程拥有。——“朕一日不死，你永远是太子！”
    3.用notify()方法唤醒正在等待的多个线程中的一个时，唤醒哪一个是不确定的。
    4.用notifyAll()方法唤醒正在等待的所有线程后，哪一个线程未来将会得到monitor也是不确定的。


---

> 参考文章：[java多线程同步与线程间通信](http://www.jianshu.com/p/8327678f97e3)

---

### 结语：

坚持每天进步一点点...

---