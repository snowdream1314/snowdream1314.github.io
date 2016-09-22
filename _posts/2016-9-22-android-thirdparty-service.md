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
        if (sp.getKeyBoolean(DDZQConstant.SharePreference_PushSetting)) {
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
                        FLDLog.e("setMyUserProfile:"+user_id);
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
                    XGPushManager.unregisterPush(DDZQApplication.getInstance());
                    sp.setKeyBoolean(Constant.SharePreference_PushSetting, false);
                }
            }
        });

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---