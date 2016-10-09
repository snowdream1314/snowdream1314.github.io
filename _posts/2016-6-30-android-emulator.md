---
layout: post
title:  "Andriod Studio结合虚拟机调试Android App"
date:   2016-6-30 18:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

### Visual Studio Emulator

#### android studio 虚拟机联网

* 主要是设置虚拟机的DNS和PC的一致

        1.将android sdk目录下platform-tools下的adb添加到系统环境变量
        2.在CMD终端中输入adb shell，进入adb命令行环境
        3.在adb shell 中输入命令getprop查看系统当前的各项属性
            得到模拟器的DNS地址

        　　在结果里可以看到：

        　　[net.dns1]: [10.0.2.3]

        　　[net.dns2]: [10.0.2.4]

        4、把dns改成我们自己的DNS

        　　setprop net.dns1 192.168.1.1

        5.重启虚拟机

---

### Genymotion虚拟机

* 下载地址：[Genymotion](https://www.genymotion.com/);下载附带virtualbox的版本（需要注册才能下载）

* virtualbox里面可以管理Genymotion新建的虚拟机，设置选项里面设置网络为：网络地址转换NAT，虚拟机即可上网

* Genymotion设置里面ADB设置本地的SDK

* Genymotion与Android Studio 进行关联：

    - Android Studio里面 File-->Setting-->plguin，搜索Genymotion，安装Genymotion插件，安装好以后重启Android Studio

* 从Genymotion虚拟机里面退出鼠标和键盘，按键盘右边的CTRL键


---

> 参考文章：[Andriod Studio结合Visual Studio Emulator for Android调试Android App](http://www.dailytech5.com/news_show.aspx?id=24345)；[安装Genymotion模拟器](http://www.cnblogs.com/smyhvae/p/4392611.html?utm_source=tuicool&utm_medium=referral)

---

### 结语：

坚持每天进步一点点...

---