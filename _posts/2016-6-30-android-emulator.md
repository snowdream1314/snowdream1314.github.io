---
layout: post
title:  "Andriod Studio结合Visual Studio Emulator for Android调试Android App"
date:   2016-6-30 18:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

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

> 参考文章：[Andriod Studio结合Visual Studio Emulator for Android调试Android App](http://www.dailytech5.com/news_show.aspx?id=24345)

---

### 结语：

坚持每天进步一点点...

---