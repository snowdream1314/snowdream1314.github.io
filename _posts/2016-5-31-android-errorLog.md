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

#### 引用依赖冲突

* 1.dexDebug ExecException finished with non-zero exit value 2

    需要在gradle中配置下面的代码，原因是引用了多个libraries文件

    defaultConfig {
            multiDexEnabled true
    }
    
* 2.Execution failed for task ':app:transformClassesWithJarMergingForDebug'.
> com.android.build.api.transform.TransformException: java.util.zip.ZipException: duplicate entry: android/support/v4/app/BackStackState$1.class

    原因：在项目工程中引用第三方包时编译产生的错误。先生的原因是项目工程中和引用的第三方包中都用到了support-v4这个依赖
    解决办法： 将第三方的依赖改成和项目中的support-v4一样，然后在项目工程中剔除support-v4：
    
        compile ('com.android.support:support-v4:23.3.0') {
            exclude module: 'support-v4'
        }
    
    clean并rebuild工程
    
    很多其他的三方包会引用到support-v4，都要剔除掉。其他的可能冲突的包也要注意，引用进工程的时候要注意是否冲突，要遵循引用唯一的原则。


---

#### Error:(2, 0) Plugin with id ‘com.github.dcendents.android-maven’ not found解决办法

* 在项目的project 的 build.gradle 添加如下代码

        dependencies {
            classpath 'com.android.tools.build:gradle:2.1.0'

            classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.2'
            classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'

        }

---

#### Error:Cause: org/gradle/api/publication/maven/internal/DefaultMavenFactory

* 当你使用的Gradle版本是2.4以上，Android插件版本是1.3.0以上的时候就会出现这个问题，这时候你只需将android-maven-gradle-plugin插件版本改为classpath ‘com.github.dcendents:android-maven-gradle-plugin:1.3’即可

---

#### Execution failed for task ':library-EnhancedPullToRefreshListView:javadoc'.

* 最后查出来是项目中用了中文注释的原因，删掉中文注释或者改成英文就OK了

---

#### Android Studio 预览(preview)报错 Exception raised during rendering: com/android/util/PropertiesMap

* 报错原因：升级了SDK到7.0后，没有下载完全

* 修改下预览右上角对应的SDK版本，改到6.0后正常显示

---

#### android.database.CursorIndexOutOfBoundsException: Index -1 requested, with a size of 4

* 报错原因：游标越界
        
        解决办法：将游标指向第一条
        cursor.moveToFirst()

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---