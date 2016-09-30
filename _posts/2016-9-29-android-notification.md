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
        lunchIntent.setClass(context, MainActivity.class);//目标activity

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
        
* 从通知栏点击进入后，要处理相关逻辑，需要在目标activity，此处即为MainActivity的onResume方法里提取bundle里面的内容以便进行跳转等

        //推送点击进入处理
        private void pushHandle() {
            //推送点击进入
            Bundle bundle = getIntent().getBundleExtra("xxx");
            if(bundle != null){
                try {

                    String scheme = bundle.getString("scheme");

                    Uri uri = Uri.parse(scheme);
                    String from_tab = uri.getQueryParameter("from_tab");

                    SchemeHandler handler = new SchemeHandler(this, null);
                    handler.handler(scheme);    //利用handle跳转

                    getIntent().removeExtra("xxx");

                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }
        
* 特别的，当程序运行在后台，比如在按home键回到桌面时，这时收到消息通知，点击进入MainActivity的onResume方法时会发现bundle为空，这是因为intent没有更新的缘故。这种情况下在调用onResume之前会先调用onNewIntent方法：

        //程序后台运行时，更新intent，以便处理此时收到的推送
        @Override
        protected void onNewIntent(Intent intent) {
            super.onNewIntent(intent);  //最新的intent
            Log.e("onNewIntent", " called");

            setIntent(intent);
            getIntent().putExtras(intent);//将最新的intent共享出去

        }
        
        接着会调用onResume，此时即可处理通知消息

* 通知图标显示问题：需要把图标放到对应的res资源文件夹下面，setSmallIcon(R.mipmap.push)设置的是小图标.

        defaultConfig {
            applicationId "xxx"
            minSdkVersion 15
            targetSdkVersion 19     //此外大于20的都会有兼容问题，如果通知图标显示有问题可以试着改成20以下的版本
            versionCode 1
            versionName "1.0"
        }
        
---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---