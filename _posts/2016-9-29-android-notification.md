---
layout: post
title:  "Android 消息通知"
date:   2016-9-29 18:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Android 消息推送通知

        NotificationManager manger = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);

        Intent lunchIntent = new Intent();
        lunchIntent.setClass(context, MainActivity.class);

        if (!jsonObject.isNull("scheme")) {
            String value = jsonObject.getString("scheme");
            Bundle bundle1 = new Bundle();
            bundle1.putString("scheme", value);
            lunchIntent.putExtra("xxx", bundle1);

        }

        PendingIntent pendingIntent= PendingIntent.getActivity(context, 1, lunchIntent, PendingIntent.FLAG_ONE_SHOT);

        NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(context);
        mBuilder.setContentTitle("我是标题")//设置通知栏标题
                .setContentText(title) //设置通知栏显示内容
                .setContentIntent(pendingIntent) //设置通知栏点击意图
                //  .setNumber(number) //设置通知集合的数量
                .setTicker("我是标题") //通知首次出现在通知栏，带上升动画效果的
                //					.setLargeIcon( )
                .setWhen(System.currentTimeMillis())//通知产生的时间，会在通知信息里显示，一般是系统获取到的时间
                .setPriority(Notification.PRIORITY_DEFAULT) //设置该通知优先级
                //  .setAutoCancel(true)//设置这个标志当用户单击面板就可以让通知将自动取消
                .setOngoing(false)//ture，设置他为一个正在进行的通知。他们通常是用来表示一个后台任务,用户积极参与(如播放音乐)或以某种方式正在等待,因此占用设备(如一个文件下载,同步操作,主动网络连接)
                .setDefaults(Notification.DEFAULT_VIBRATE|Notification.DEFAULT_SOUND)//向通知添加声音、闪灯和振动效果的最简单、最一致的方式是使用当前的用户默认设置，使用defaults属性，可以组合
                //Notification.DEFAULT_ALL   添加声音 // requires VIBRATE permission
                .setSmallIcon(R.mipmap.push);//设置通知小ICON
                
        Notification notification = mBuilder.build();
        notification.defaults= Notification.DEFAULT_SOUND;
        notification.flags = Notification.FLAG_AUTO_CANCEL;
        manger.notify(new Random().nextInt(), notification);

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---