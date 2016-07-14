---
layout: post
title:  "Android Jni"
date:   2016-7-14 11:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

### Android studio 下的Jni开发

* Android 很容易被反编译，一些关键性的加密字符串什么的可以利用Jni，提高安全性。

---

#### 直接使用.so文件

* 在app目录下，与java文件夹平级处新建文件夹jinLibs，将编译生成好的libs文件夹的下几个文件直接拷贝过来就行

* 使用

        static {
            System.loadLibrary("wdcore");
        }

        public native String WDKey();

---

#### 使用源码文件

* 在app目录下，与java文件夹平级处新建文件夹jni，把源文件拷贝到jni文件夹下

* 在 local.properties 添加sdk和ndk路径配置：

        ndk.dir=C\:\\Users\\CB-Ye\\AppData\\Local\\Android\\sdk\\ndk-bundle
        sdk.dir=C\:\\Users\\CB-Ye\\AppData\\Local\\Android\\sdk
        
* app下的build.gradle配置NDK模块:

        defaultConfig {
            applicationId "com.caibo.weidu"
            minSdkVersion 15
            targetSdkVersion 23
            versionCode 1
            versionName "1.0"
            multiDexEnabled true

            ndk {
                moduleName = "wdcore"
                abiFilters "armeabi", "armeabi-v7a", "x86", "arm64-v8a","mips", "mips64", "x86_64"
            }
        }

* 使用

        static {
            System.loadLibrary("wdcore");
        }

        public native String WDKey();

---

#### 生成.so文件

* 新建目录，并新建jni文件夹

* 在jni文件夹下编写C/C++源文件：
        
        方法名称： Java + 包名 + 方法名
        jstring为返回值

        #include <string.h>  
        #include <jni.h>  
        
        jstring Java_com_caibo_weidu_util_DES_WDDesKey( JNIEnv* env, jobject thiz ) {  
            return (*env)->NewStringUTF(env, "hello");  
        }  
        
* 编写Android.mk文件：

        LOCAL_PATH := $(call my-dir)  
  
        include $(CLEAR_VARS)  

        LOCAL_MODULE    := wdcore  //引用的库名，即编译后生成的.so文件为libwdcore.so，引用的话直接System.loadLibrary("wdcore");
        LOCAL_SRC_FILES := wdcore.c  //源文件名
          
        include $(BUILD_SHARED_LIBRARY)  

* 在源文件目录下打开终端，执行ndk-build就会生成libs文件夹，里面即.so文件

---

> 参考文章：[Android Studio下jni应用](http://www.cnblogs.com/flyme/p/4431762.html)、[andorid jni入门教程一之helloworld](http://www.cnblogs.com/gisdream/p/3522190.html)

---

### 结语：

坚持每天进步一点点...

---