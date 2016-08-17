---
layout: post
title:  "Andriod Studio打包签名"
date:   2016-8-5 9:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

### 记录Android studio下打包签名相关内容

---

#### gradle自动签名构建实现

* 首先生成签名文件

        Build --> Generate Signed APK --> 设置相关的参数，密码什么的，即可得到签名文件
        
* 将签名文件加入项目中 App下的src同级目录

* 设置gradle

        signingConfigs {
            myConfig {
                storeFile file("xxxxxx.jks")    //生成的签名文件
                keyPassword "******"    //密码
            }
        }

        buildTypes {
            release {
                signingConfig  signingConfigs.myConfig  //添加签名配置
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }
        
* 利用gradle完成app签名构建。

        1.利用android studio为我们内置好的gradle命令：
        
        在Android studio右上角Gradle命令下 --> app --> build --> assembleRelease
        
        2.命令行
        
        在Android studio下方Terminal打开终端：gradle assembleRelease
        
---

> 参考文章：[android studio gradle自动签名构建实现](http://www.tuicool.com/articles/MjqYbiu)，[ Android 使用Android Studio + Gradle 或 命令行 进行apk签名打包](http://blog.csdn.net/shineflowers/article/details/45042485)

---

### 结语：

坚持每天进步一点点...

---