---
layout: post
title:  "Android 动画"
date:   2016-7-13 11:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

### Android 动画相关内容

---

#### Activity之间的跳转动画

* 跳转的动画如果不自己设置编写的话一般都是由系统定制厂商定制，不同的手机风格都不一样

* activity 之间的跳转动画主要的有2中实现方式，一种是定义主题的形式，这是全局的；另一种就是overridePendingTransition()方法;两种方法都需要自己编写动画文件，也可以使用系统的

---

#### 动画文件
       
       文件位置放在:res\anim
       <set xmlns:android="http://schemas.android.com/apk/res/android"
            android:interpolator="@android:anim/decelerate_interpolator">

            <translate
                android:duration="300"
                android:fromYDelta="100%p"  //Y方向位置变化从100%p 到0，即从屏幕下方进入到屏幕
                android:toYDelta="0"/>

            <alpha
                android:duration="300"
                android:fromAlpha="0.0"     //透明度从0.0变化到1.0,0.0为完全透明，1.0为完全不透明
                android:toAlpha="1.0"/>

        </set>

---

#### 设置主题

* 在 res\values\styles编写自定义主题
        
        <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
            <!-- Customize your theme here. -->
            <item name="colorPrimary">@color/colorPrimary</item>
            <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
            <item name="colorAccent">@color/colorAccent</item>
            <item name="android:windowAnimationStyle">@style/activityAnim</item>
        </style>


        <!-- 使用style方式定义activity切换动画 --> //
        <style name="activityAnim">
            <item name="android:activityOpenEnterAnimation">@anim/slide_in_top</item>   //对应的动画文件
            <item name="android:activityOpenExitAnimation">@anim/slide_in_top</item>
        </style>
        
        activityOpenEnterAnimation // 用于设置打开新的Activity并进入新的Activity展示的动画
        activityOpenExitAnimation  // 用于设置打开新的Activity并销毁之前的Activity展示的动画
        activityCloseEnterAnimation  // 用于设置关闭当前Activity进入上一个Activity展示的动画
        activityCloseExitAnimation  // 用于设置关闭当前Activity时展示的动画
        
* 在AndroidManifest文件中对应的Activity设置style就可以了

---

#### overridePendingTransition()方法

        overridePendingTransition(enterAnim,exitAnim);
        enterAnim为进入动画，exitAnim为退出动画
        
        overridePendingTransition方法一般在startActivity()方法和finish()方法后面调用，也可以在onCreate()方法里面调用，不过要在setContentView()之前;

---

### 透明度动画

        private int zhishuNum = -1;

        private AlphaAnimation toLight, toDark;

        if (!initZhishu) {
            initZhishu = true;
            zhishuNum = 0;
            toLight = new AlphaAnimation(1, 0);//透明度动画
            toLight.setDuration(2000);//动画持续时间
            toLight.setFillAfter(true);//执行完停留在执行完的状态
            toLight.setStartOffset(1000);//开始前等待时间
            toLight.setRepeatCount(0);//动画重复次数

            toDark = new AlphaAnimation(0, 1);
            toDark.setDuration(1500);//动画持续时间
            toDark.setFillAfter(true);//执行完停留在执行完的状态
            toDark.setStartOffset(1000);//开始前等待时间
            toDark.setRepeatCount(0);//动画重复次数

            zhishuNameTextView.setText(weather.getZhishus().get(zhishuNum).getName() + "：" + weather.getZhishus().get(zhishuNum).getValue());
            zhishuDatailTextView.setText(weather.getZhishus().get(zhishuNum).getDetail());
            zhishuLinearLayout.setAnimation(toLight);
            toLight.startNow();

            toLight.setAnimationListener(new Animation.AnimationListener() {
                @Override
                public void onAnimationStart(Animation animation) {
                }

                @Override
                public void onAnimationEnd(Animation animation) {
                    zhishuNum += 1;
                    if (zhishuNum == 11) zhishuNum = 0;
                    zhishuLinearLayout.setAnimation(toDark);
                    zhishuNameTextView.setText(weather.getZhishus().get(zhishuNum).getName() + "：" + weather.getZhishus().get(zhishuNum).getValue());
                    zhishuDatailTextView.setText(weather.getZhishus().get(zhishuNum).getDetail());
                    toDark.startNow();
                }

                @Override
                public void onAnimationRepeat(Animation animation) {

                }
            });

            toDark.setAnimationListener(new Animation.AnimationListener() {
                @Override
                public void onAnimationStart(Animation animation) {

                }

                @Override
                public void onAnimationEnd(Animation animation) {
                    zhishuLinearLayout.setAnimation(toLight);
                    toLight.startNow();
                }

                @Override
                public void onAnimationRepeat(Animation animation) {

                }
            });
        }

---

> 参考文章：[实现activity跳转动画的五种方式](http://blog.csdn.net/qq_23547831/article/details/51821159)


---

### 结语：

坚持每天进步一点点...

---