---
layout: post
title:  "Android 错误日志"
date:   2016-5-31 11:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

## 记录工作中写APP过程中调试的错误及解决办法

---

#### 包冲突

* Multiple dex files define Lcom/ta/utdid2/android/utils/UTDID;项目引用的JAR包有重复的地方

    原因:项目中集成了Umengjar包和淘宝的一个插件包，然后里面都包含有一个设备标识生成库(UTDID.jar)的插件包，导致编译冲突
    参考文章：[ Multiple dex files define Lcom/ta/utdid2/android/utils/UTDID](http://blog.csdn.net/caiwenfeng_for_23/article/details/47162429)

---

#### mipmap文件夹和drawable文件夹使用不当

*  Bad XML block: header size 28024 or total size 1702240364 is larger than data size 417

    原因：把自定义的xml文件放在了mipmap文件夹下
    记录：Android Studio中，推荐把图片等资源放在mipmap文件夹下，其他的自定义的xml文件放在drawable文件夹下，如selector等

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---