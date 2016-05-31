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

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---