---
layout: post
title:  "Android Monkey测试"
date:   2016-10-31 13:24:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Android Monkey

* Monkey测试原理：monkey测试的原理就是利用socket通讯的方式来模拟用户的按键输入，触摸屏输入，手势输入等，看设备多长时间会出异常。当Monkey程序在模拟器或设备运行的时候，如果用户出发了比如点击，触摸，手势或一些系统级别的事件的时候，它就会产生随机脉冲，所以可以用Monkey用随机重复的方法去负荷测试你开发的软件。

* Monkey程序由Android系统自带，使用Java语言写成，在Android文件系统中的存放路径是：/sdk/sdk/tools/lib/monkey.jar

#### Monkey 使用

* 要获取Monkey命令自带的简单帮助，在CMD中执行命令：

        adb shell monkey –help

* 参数：-p 用于限制测试的包名，指定包之后，Monkey将只允许系统启动指定的APP。如果不指定包，Monkey将允许系统启动设备中的所有APP。

        adb shell monkey -p com.example.sellclientapp  100      //指定一个包，即让Monkey程序模拟100次随机用户事件
        adb shell monkey -p com.htc.Weather –p com.htc.pdfreader  -p com.htc.photo.widgets 100      //指定多个包

* 参数: -v 用于指定反馈信息级别（信息级别就是日志的详细程度），总共分3个级别

    - 日志级别 Level 0 ：仅提供启动提示、测试完成和最终结果等少量信息

        adb shell monkey -p com.htc.Weather –v 100

    - 日志级别 Level 1：提供较为详细的日志，包括每个发送到Activity的事件信息

        adb shell monkey -p com.htc.Weather –v -v 100

    - 日志级别 Level 2：最详细的日志，包括了测试中选中/未选中的Activity信息

        adb shell monkey -p com.htc.Weather –v -v –v 100

* 参数: -v 用于指定伪随机数生成器的seed值，如果seed相同，则两次Monkey测试所产生的事件序列也相同的。
    
    - Monkey测试1：adb shell monkey -p com.htc.Weather –s 10 100
    - Monkey 测试2：adb shell monkey -p com.htc.Weather –s 10 100

    - 两次测试的效果是相同的，因为模拟的用户操作序列（每次操作按照一定的先后顺序所组成的一系列操作，即一个序列）是一样的。

* 下面是一个更为典型的命令行示例，它启动指定的应用程序，并向其发送500个伪随机事件：

        adb shell monkey -p your.package.name -vvv 500 > monkeytest.txt  

        -p表示对象包  -v 为 verbose的缩写（信息级别就是日志的详细程度）,就是详细输出事件等级,这个3个v就是输出等级1至3的所有事件.(使用管道命令将输出结果放到一个文本里面方便查看)

---

> 参考文章：[Monkey测试简介](http://www.cnblogs.com/manuosex/p/3215270.html);[Android 程序员必须掌握的三种自动化测试方法](http://www.open-open.com/lib/view/open1452002658792.html);[Android初体验之Monkey和MonkeyRunner](http://blog.csdn.net/mad1989/article/details/38087737)

---

### 结语：

坚持每天进步一点点...

---