---
layout: post
title:  "Android 碰到的问题"
date:   2016-11-01 13:24:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Android 碰到的问题记录

---

* 魅族手机连接Android Studio 调试时显示手机未授权，连不上，但是手机USB调试已经开启

    - 重启手机
    - 关闭重新打开USB调试
    - 重启Android Studio

    - 网上查到的方法都没有解决，最后删除系统环境变量下的“ANDROID_SDK_HOME”解决

* 去掉Activity默认的切换动画：

        <style name="introduce" parent="AppBaseThemeNotTranslucent">
            <item name="android:windowAnimationStyle">@style/animation_null</item>
        </style>

        <style name="animation_null" parent="@android:style/Animation.Activity">
            <item name="android:activityOpenEnterAnimation">@null</item>
            <item name="android:activityOpenExitAnimation">@null</item>
            <item name="android:activityCloseEnterAnimation">@null</item>
            <item name="android:activityCloseExitAnimation">@null</item>
            <item name="android:taskOpenEnterAnimation">@null</item>
            <item name="android:taskOpenExitAnimation">@null</item>
            <item name="android:taskCloseEnterAnimation">@null</item>
            <item name="android:taskCloseExitAnimation">@null</item>
            <item name="android:taskToFrontEnterAnimation">@null</item>
            <item name="android:taskToFrontExitAnimation">@null</item>
            <item name="android:taskToBackEnterAnimation">@null</item>
            <item name="android:taskToBackExitAnimation">@null</item>
        </style>

        在需要去掉Activity默认动画的地方引用Style
        <activity android:name=".main.IntroduceActivity"
            android:screenOrientation="portrait"
            android:theme="@style/introduce">
        </activity>

---

* AndroidStudio导入项目一直卡在Building gradle project info
    
    - AndroidStudio导入项目一直卡在Building gradle project info，实际上是因为你导入的这个项目使用的gradle与你已经拥有的gradle版本不一致，导致需要下载该项目需要的gradle版本，不知是被墙了还是什么原因，反正就是会一直卡住，直至下载完成.

    解决办法：

    - 去官网下载gradle的版本，然后放到本地
    - 直接修改gradle-wrapper.properties文件：找一个能运行的AndroidStudio项目，打开gradle-wrapper.properties，文件目录：项目/gradle/wrapper/gradle-wrapper.properties，复制distributionUrl这一整行的内容，替换对应需要打开的项目即可
        
---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---