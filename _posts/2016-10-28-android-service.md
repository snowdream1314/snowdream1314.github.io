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

* Android 进程回收机制

    - 系统出于体验和性能上的考虑，app在退到后台时系统并不会真正的kill掉这个进程，而是将其缓存起来。打开的应用越多，后台缓存的进程也越多。在系统内存不足的情况下，系统开始依据自身的一套进程回收机制来判断要kill掉哪些进程，以腾出内存来供给需要的app。这套杀进程回收内存的机制就叫 Low Memory Killer ，它是基于Linux内核的 OOM Killer（Out-Of-Memory killer）机制诞生。在Low Memory Killer中通过进程的oom_adj与占用内存的大小决定要杀死的进程，oom_adj越小越不容易被杀死。

        ps | grep PackageName //获取你指定的进程信息
        cat /proc/进程ID/oom_adj  //获取进程的oom_adj

    - app退到后台时，其所有的进程优先级都会降低。但是UI进程是降低最为明显的，因为它占用的内存资源最多，系统内存不足的时候肯定优先杀这些占用内存高的进程来腾出资源。所以，为了尽量避免后台UI进程被杀，需要尽可能的释放一些不用的资源，尤其是图片、音视频之类的。
    - 有些手机厂商把这些知名的app放入了自己的白名单中，保证了进程不死来提高用户体验（如微信、QQ、陌陌都在小米的白名单中）。如果从白名单中移除，他们终究还是和普通app一样躲避不了被杀的命运

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

        public int onStartCommand(Intent intent, int flags, int startId) {  
            flags = START_STICKY;  
            return super.onStartCommand(intent, flags, startId);  
        } 

    补充说明：onStartCommand()方法返回的是一个int整型，这个整型可以有以下四个取值：
    - START_STICKY：如果service进程被kill掉，保留service的状态为开始状态，但不保留递送的intent对象。随后系统会尝试重新创建service，由于服务状态为开始状态，所以创建服务后一定会调用onStartCommand(Intent,int,int)方法。如果在此期间没有任何启动命令被传递到service，那么参数Intent将为null。 
    - START_NOT_STICKY：“非粘性的”。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统不会自动重启该服务 
    - START_REDELIVER_INTENT：重传Intent。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统会自动重启该服务，并将Intent的值传入。 
    - START_STICKY_COMPATIBILITY：START_STICKY的兼容版本，但不保证服务被kill后一定能重启。

* 在service 的onDestory里面重启服务

        public void onDestroy() {  
            Intent localIntent = new Intent(); 
            localIntent.setClass(this, MyService.class); //销毁时重新启动Service 
            this.startService(localIntent); 
        } 

        缺点：service被系统杀死的时候并不一定会执行onDestroy

* 提高服务的优先级:在onStartCommand里面调用 startForeground;当stopservice的时候通过在onDestroy里面调用stopForeground去掉。

        public class GrayService extends Service {

        private final static int GRAY_SERVICE_ID = 1001;

        @Override
        public int onStartCommand(Intent intent, int flags, int startId) {
            if (Build.VERSION.SDK_INT < 18) {
                startForeground(GRAY_SERVICE_ID, new Notification());//API < 18 ，此方法能有效隐藏Notification上的图标
            } else {
                Intent innerIntent = new Intent(this, GrayInnerService.class);
                startService(innerIntent);
                startForeground(GRAY_SERVICE_ID, new Notification());
            }

            return super.onStartCommand(intent, flags, startId);
        }

        ...
        ...

        /**
         * 给 API >= 18 的平台上用的灰色保活手段
         */
        public static class GrayInnerService extends Service {

            @Override
            public int onStartCommand(Intent intent, int flags, int startId) {
                startForeground(GRAY_SERVICE_ID, new Notification());
                stopForeground(true);
                stopSelf();
                return super.onStartCommand(intent, flags, startId);
            }

        }
    }

* 

---

> 参考文章：[微信Android客户端后台保活经验分享](https://mp.weixin.qq.com/s?__biz=MzA3ODg4MDk0Ng==&mid=403254393&idx=1&sn=8dc0e3a03031177777b5a5876cb210cc)；[关于 Android 进程保活，你所需要知道的一切](http://www.jianshu.com/p/63aafe3c12af)

---

### 结语：

坚持每天进步一点点...

---