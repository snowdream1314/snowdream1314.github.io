---
layout: post
title:  "Android 嵌入第三方服务"
date:   2016-9-22 10:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

### Android 嵌入第三方服务，包括信鸽推送、友盟统计、百度更新、阿里百川等

---

#### 信鸽推送四部曲

* 首先，当然是下载最新的信鸽SDK,[腾讯信鸽下载中心](http://xg.qq.com/xg/ctr_index/download)；下载到SDK以后,将里面libs文件夹中的jar文件和.so文件添加到项目中

* 在AndroidManifest文件中添加权限和信鸽相关的内容,[Android SDK快速接入](http://developer.qq.com/wiki/xg/Android%E6%8E%A5%E5%85%A5/Android%20SDK%E5%BF%AB%E9%80%9F%E6%8E%A5%E5%85%A5/Android%20SDK%E5%BF%AB%E9%80%9F%E6%8E%A5%E5%85%A5.html)

* 以上配置好以后，在工程的主Activity，如MainActivity中，启动信鸽服务和注册APP,主要的方法就是XGPushManager.registerPush(context);context用全局Application

* 在需要设置开启关闭的地方编写相关的逻辑，如ToggleButton：

        final MySharedPreference sp = new MySharedPreference(UserSettingActivity.this);
        if (sp.getKeyBoolean(Constant.SharePreference_PushSetting)) {
            pushToggleButton.setChecked(true);
        } else {
            pushToggleButton.setChecked(false);
        }
    
        pushToggleButton.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton compoundButton, boolean isChecked) {
                if (isChecked) {
                    if (UserUtil.isLogin(UserSettingActivity.this)) {
                        String user_id = UserUtil.getMyUserProfile(UserSettingActivity.this).getUser_id();
                        Log.e("setMyUserProfile:"+user_id);
                        if(user_id!=null && !"".equals(user_id)){
                            XGPushManager.registerPush(UserSettingActivity.this, user_id);
                        }
                    }else {
                        XGPushManager.registerPush(Application.getInstance());
                    }
                    Context context = getApplicationContext();
                    Intent service = new Intent(context, XGPushService.class);
                    context.startService(service);

                    sp.setKeyBoolean(Constant.SharePreference_PushSetting, true);

                }else {
                    XGPushManager.unregisterPush(Application.getInstance());
                    sp.setKeyBoolean(Constant.SharePreference_PushSetting, false);
                }
            }
        });

* 还可以配置自己的消息处理模块MessageReceiver,如：
        
        在AndroidManifest文件中添加自己处理消息的接收器
        <!-- 【可选】APP实现的Receiver，用于接收消息透传和操作结果的回调，请根据需要添加 -->
        <!-- YOUR_PACKAGE_PATH.CustomPushReceiver需要改为自己的Receiver： -->
        <receiver
            android:name=".pushreceiver.MessageReceiver"
            android:exported="true">
            <intent-filter>

                <!-- 接收消息透传 -->
                <action android:name="com.tencent.android.tpush.action.PUSH_MESSAGE" />
                <!-- 监听注册、反注册、设置/删除标签、通知被点击等处理结果 -->
                <action android:name="com.tencent.android.tpush.action.FEEDBACK" />
            </intent-filter>
        </receiver>
        
        
        //对应接收器的实现
        public class MessageReceiver extends XGPushBaseReceiver {
        
            // 通知展示
            @Override
            public void onNotifactionShowedResult(Context context,
                    XGPushShowedResult notifiShowedRlt) {
                if (context == null || notifiShowedRlt == null) {
                    return;
                }
        //		show(context, "您有1条新消息, " + "通知被展示 ， " + notifiShowedRlt.toString());
            }

            @Override
            public void onUnregisterResult(Context context, int errorCode) {
                if (context == null) {
                    return;
                }
                String text = "";
                if (errorCode == XGPushBaseReceiver.SUCCESS) {
                    text = "反注册成功";
                } else {
                    text = "反注册失败" + errorCode;
                }
                Log.d(LogTag, text);
                show(context, text);

            }

            @Override
            public void onSetTagResult(Context context, int errorCode, String tagName) {
                if (context == null) {
                    return;
                }
                String text = "";
                if (errorCode == XGPushBaseReceiver.SUCCESS) {
                    text = "\"" + tagName + "\"设置成功";
                } else {
                    text = "\"" + tagName + "\"设置失败,错误码：" + errorCode;
                }
                Log.d(LogTag, text);
                show(context, text);

            }

            @Override
            public void onDeleteTagResult(Context context, int errorCode, String tagName) {
                if (context == null) {
                    return;
                }
                String text = "";
                if (errorCode == XGPushBaseReceiver.SUCCESS) {
                    text = "\"" + tagName + "\"删除成功";
                } else {
                    text = "\"" + tagName + "\"删除失败,错误码：" + errorCode;
                }
                Log.d(LogTag, text);
                show(context, text);

            }
        
            // 通知点击回调 actionType=1为该消息被清除，actionType=0为该消息被点击
            @Override
            public void onNotifactionClickedResult(Context context, XGPushClickedResult message) {
                if (context == null || message == null) {
                    return;
                }
                String text = "";
                if (message.getActionType() == XGPushClickedResult.NOTIFACTION_CLICKED_TYPE) {
                    // 通知在通知栏被点击啦。。。。。
                    // APP自己处理点击的相关动作
                    // 这个动作可以在activity的onResume也能监听，请看第3点相关内容
                    text = "通知被打开 :" + message;


                    String customContent = message.getCustomContent();
                    if (customContent != null && customContent.length() != 0) {
                        try {
                            JSONObject customContentObject = new JSONObject(customContent);
                            if (!customContentObject.isNull("scheme")) {


                                String scheme = customContentObject.getString("scheme");

                                Log.e("通知被打开 :"+scheme);

                            }
                        } catch (JSONException e) {
                            e.printStackTrace();
                        }
                    }

                } else if (message.getActionType() == XGPushClickedResult.NOTIFACTION_DELETED_TYPE) {
                    // 通知被清除啦。。。。
                    // APP自己处理通知被清除后的相关动作
                    text = "通知被清除 :" + message;
                }
                Toast.makeText(context, "广播接收到通知被点击:" + message.toString(), Toast.LENGTH_SHORT).show();

                // APP自主处理的过程。。。
                Log.d(LogTag, text);
                show(context, text);
            }
            
            @Override
            public void onRegisterResult(Context context, int errorCode,
                    XGPushRegisterResult message) {

                if (context == null || message == null) {
                    return;
                }
                String text = "";
                if (errorCode == XGPushBaseReceiver.SUCCESS) {
                    text = message + "注册成功";
                    // 在这里拿token
                    String token = message.getToken();
                } else {
                    text = message + "注册失败，错误码：" + errorCode;
                }
                Log.d(LogTag, text);
                show(context, text);
            }
            
            
            // 消息透传，通过scheme跳转处理
            @Override
            public void onTextMessage(Context context, final XGPushTextMessage message) {
                String text = "收到消息:" + message.toString();

                String titleString = message.getTitle();
                String contentString = message.getContent();

                if(AppUtil.isAppRunning(context)){//App已经在运行

                    Log.e("AppUtil.isAppAlive(context)");

                    final Activity activity = MainActivity.activity;
                    if(activity==null || ! (activity instanceof FragmentActivity)) {

                        Log.e("activity == null || ! (activity instanceof FragmentActivity):"+activity);
                        return;
                    }

                    DialogUtil.showConfirmDialog(activity, titleString, contentString, "打开", new SweetAlertDialog.OnSweetClickListener() {
                        @Override
                        public void onClick(SweetAlertDialog sweetAlertDialog) {//点击后通过scheme跳转

                            String customContent = message.getCustomContent();
                            if (customContent != null && customContent.length() != 0) {
                                try {
                                    JSONObject obj = new JSONObject(customContent);
                                    if (!obj.isNull("scheme")) {
                                        String scheme = obj.getString("scheme");

                                        SchemeHandler handler = new SchemeHandler((FragmentActivity)activity, null);
                                        handler.handler(scheme);

                                        Uri uri = Uri.parse(scheme);
                                        String from_tab = uri.getQueryParameter("from_tab");
                                        if(from_tab!=null){
                                            handler.handler(scheme"://switch_tab?tab="+from_tab+"&pop_to_root=1");
                                        }
                                    }
                                } catch (JSONException e) {
                                    e.printStackTrace();
                                }
                            }

                            DialogUtil.dismissDialog(activity);
                        }
                    });

                }else{

                    Log.e("!!!AppUtil.isAppAlive(context)");

                    NotificationManager manger = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);

                    Intent intent = new Intent(context, LaunchActivity.class);
                    // 获取自定义key-value
                    String customContent = message.getCustomContent();
                    if (customContent != null && customContent.length() != 0) {
                        try {
                            JSONObject obj = new JSONObject(customContent);
                            if (!obj.isNull("scheme")) {
                                String value = obj.getString("scheme");
                                Log.d(LogTag, "get custom value:" + value);

                                Bundle bundle = new Bundle();
                                bundle.putString("scheme", value);
                                bundle.putString("title", titleString);
                                bundle.putString("content", contentString);
                                intent.putExtra(Constant.PUSH_HANDLE_SCHEME_BUNDLE, bundle);

                            }
                        } catch (JSONException e) {
                            e.printStackTrace();
                        }
                    }
                    PendingIntent pendingIntent= PendingIntent.getActivity(context, 1, intent, PendingIntent.FLAG_ONE_SHOT);

                    NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(context);
                    mBuilder.setContentTitle(titleString)//设置通知栏标题
                            .setContentText(contentString) //<span style="font-family: Arial;">//设置通知栏显示内容</span>
                            .setContentIntent(pendingIntent) //设置通知栏点击意图
        //  .setNumber(number) //设置通知集合的数量
                            .setTicker(titleString) //通知首次出现在通知栏，带上升动画效果的
        //					.setLargeIcon( )
                            .setWhen(System.currentTimeMillis())//通知产生的时间，会在通知信息里显示，一般是系统获取到的时间
                            .setPriority(Notification.PRIORITY_DEFAULT) //设置该通知优先级
        //  .setAutoCancel(true)//设置这个标志当用户单击面板就可以让通知将自动取消
                            .setOngoing(false)//ture，设置他为一个正在进行的通知。他们通常是用来表示一个后台任务,用户积极参与(如播放音乐)或以某种方式正在等待,因此占用设备(如一个文件下载,同步操作,主动网络连接)
                            .setDefaults(Notification.DEFAULT_VIBRATE|Notification.DEFAULT_SOUND)//向通知添加声音、闪灯和振动效果的最简单、最一致的方式是使用当前的用户默认设置，使用defaults属性，可以组合
                            //Notification.DEFAULT_ALL   添加声音 // requires VIBRATE permission
                            .setSmallIcon(R.mipmap.icon60);//设置通知小ICON

                    Notification notification = mBuilder.build();
                    notification.defaults= Notification.DEFAULT_SOUND;
                    notification.flags = Notification.FLAG_AUTO_CANCEL;
                    manger.notify(new Random().nextInt(), notification);

                }

            }
        
        }

---

#### 百度更新

* 下载SDK包，将里面的jar文件和values、drawables资源文件添加到项目中

* 在AndroidManifest文件中添加权限和相应的设置

* 程序入口启动服务：

        //initBaiduUpdate
        BDAutoUpdateSDK.uiUpdateAction(this, new UICheckUpdateCallback() {
            @Override
            public void onCheckComplete() {
                FLDLog.e("update oncheck complete");
            }
        });

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---