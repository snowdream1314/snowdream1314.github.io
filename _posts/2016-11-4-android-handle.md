---
layout: post
title:  "Andriod 异步消息处理"
date:   2016-11-4 9:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Handler 、 Looper 、Message

* 简单的应用：更新progressBar进度条

        Handler handler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                if (msg.arg1 > 100) msg.arg1 = 0;
                progressBar.setProgress(msg.arg1);
                handler.postDelayed(myThread, 1000);
            }
        };

        handler.post(myThread);

        Runnable myThread = new Runnable() {
            int i = 0;
            @Override
            public void run() {
                Log.e("run", i+"");
                i += 10;
                Message msg = handler.obtainMessage();
                msg.arg1 = i;
                if (i == 100) {
                    i = 0;
                    handler.removeCallbacks(myThread);
                    handler.removeMessages(msg.arg1);
                }
                handler.sendMessage(msg);
            }
        };

* 异步消息处理线程启动后会进入一个无限的循环体之中，每循环一次，从其内部的消息队列中取出一个消息，然后回调相应的消息处理函数，执行完成一个消息后则继续循环。若消息队列为空，线程则会阻塞等待。Looper负责的就是创建一个MessageQueue，然后进入一个无限循环体不断从该MessageQueue中读取消息，而消息的创建者就是一个或多个Handler 。

---

* Looper主要是prepare()和loop()两个方法。

* Looper主要作用：

    - 与当前线程绑定，保证一个线程只会有一个Looper实例，同时一个Looper实例也只有一个MessageQueue。
    - loop()方法，不断从MessageQueue中去取消息，交给消息的target属性的dispatchMessage去处理。

---

#### 整个异步处理消息的流程

* 首先Looper.prepare()在本线程中保存一个Looper实例，然后该实例中保存一个MessageQueue对象；因为Looper.prepare()在一个线程中只能调用一次，所以MessageQueue在一个线程中只会存在一个。

* Looper.loop()会让当前线程进入一个无限循环，不断从MessageQueue的实例中读取消息，然后回调msg.target.dispatchMessage(msg)方法。

* Handler的构造方法，会首先得到当前线程中保存的Looper实例，进而与Looper实例中的MessageQueue相关联。

* Handler的sendMessage方法，会给msg的target赋值为handler自身，然后加入MessageQueue中。

* 在构造Handler实例时，我们会重写handleMessage方法，也就是msg.target.dispatchMessage(msg)最终调用的方法。

* 产生一个Message对象，可以new，也可以使用Message.obtain()方法；两者都可以，但是更建议使用obtain方法，因为Message内部维护了一个Message池用于Message的复用，避免使用new 重新分配内存。

        Message message = myHandler.obtainMessage();    //获取一个Message对象，并且绑定myHandle
        Message message = Message.obtain();     //获取一个Message对象，没有绑定handle

---

#### 线程间通信

        //主线程MainActivity中：
        myButton01.setOnClickListener(new Button.OnClickListener() {  
            @Override  
            public void onClick(View v) {  

                new Thread() {  //开启子线程
                    @Override  
                    public void run() {  
                        System.out.println("the thread id is "  
                                + Thread.currentThread().getId());  
                        System.out.println("the thread name is"  
                                + Thread.currentThread().getName());  

                        /** 定义消息对象 并且赋予内容 最后发送给UI线程 
                          *这里这个方法要说明一下 myHandler.obtainMessage()-->obtainMessage这个方法用来获得消息对象 
                          *但是这个消息对象会关联调用它的Handler对象。所以就会关联myHandler对象 而这个myHandler对象是在UI线程里边创建的 
                          * 所以很显然我们这消息会发给主线程的消息队列 我想这对大家来说根本没有任何难度 
                          */  
                        Message message = myHandler.obtainMessage();  
                        message.obj = "message";  

                        //把消息发送给目标对象，目标对象就是 myHandler 就是关联我们得到的那个消息对象的Handler  
                        message.sendToTarget();  

                    }  
                    //启动线程  
                }.start();  

            }  
        });  

* Handle是和生成的时候所在的线程绑定的，并和绑定的线程的Looper和MessageQueue绑定；在子线程中通过handle.obtainMessage()生成消息并绑定到主线程的Handle，便可以将子线程中的消息发给主线程的Handle.
---

> 参考文章：[Android 异步消息处理机制 让你深入理解 Looper、Handler、Message三者关系](http://blog.csdn.net/lmj623565791/article/details/38377229)

---

### 结语：

坚持每天进步一点点...

---