---
layout: post
title:  "Android Service后台保活"
date:   2016-10-28 16:44:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Android Service后台保活相关知识

* Service 常用结构

        public class ExampleService extends Service
        {
            int mStartMode; // indicates how to behave if the service is killed
            IBinder mBinder; // interface for clients that bind
            boolean mAllowRebind; // indicates whether onRebind should be used

            //第一次启动会调用
            @Override
            public void onCreate()      
            {
                // The service is being created
            }

            //服务运行后，再次开启服务不会重新创建，只会重复调用onStartCommand
            @Override
            public int onStartCommand(Intent intent, int flags, int startId)
            {
                // The service is starting, due to a call to startService()
                return mStartMode;
            }

            @Override
            public IBinder onBind(Intent intent)
            {
                // A client is binding to the service with bindService()
                return mBinder;
            }

            @Override
            public boolean onUnbind(Intent intent)
            {
                // All clients have unbound with unbindService()
                return mAllowRebind;
            }

            @Override
            public void onRebind(Intent intent)
            {
                // A client is binding to the service with bindService(),
                // after onUnbind() has already been called
            }

            @Override
            public void onDestroy()
            {
                // The service is no longer used and is being destroyed
            }
        }

* Service启动方式可以是：

    - 通过其他组件调用 startService()；这种service可以无限地运行下去，必须调用stopSelf()方法或者其他组件调用stopService()方法来停止它。当service被停止时，系统会销毁它。
    - 其他组件（一个客户）调用bindService()绑定： 客户可以通过一个IBinder接口和service进行通信， 通过 unbindService()方法来关闭这种连接，一个service可以同时和多个客户绑定，当所有客户都解除绑定之后，系统会销毁service。
    > 当Service通过startService()启动后又被其他组件绑定时，必须要解除绑定且调用stopSelf()方法或者其他组件调用stopService()方法来停止它。

#### 常用的一些保活方法

* 控制onStartCommand函数的返回值 ： return START_STICKY；

* 在service 的onDestory里面重启服务

        public void onDestroy() {  
            Intent localIntent = new Intent(); 
            localIntent.setClass(this, MyService.class); //销毁时重新启动Service 
            this.startService(localIntent); 
        } 

        缺点：service被系统杀死的时候并不一定会执行onDestroy

* 提高服务的优先级

        Notification notification = new Notification();  
        notification.flags = Notification.FLAG_ONGOING_EVENT;  
        notification.flags |= Notification.FLAG_NO_CLEAR;  
        notification.flags |= Notification.FLAG_FOREGROUND_SERVICE;  
        service.startForeground(1, notification);  

---

> 参考文章：[微信Android客户端后台保活经验分享](https://mp.weixin.qq.com/s?__biz=MzA3ODg4MDk0Ng==&mid=403254393&idx=1&sn=8dc0e3a03031177777b5a5876cb210cc)

---

### 结语：

坚持每天进步一点点...

---