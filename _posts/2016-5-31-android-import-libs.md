---
layout: post
title:  "Android studio 添加第三方库"
date:   2016-5-31 14:08:05
categories: Android studio
excerpt: 
---

* content
{:toc}

---

#### 导入*.jar文件

* 在项目目录新建libs文件夹用于存放第三方库

* 直接将下载下来的*.jar文件复制到libs文件夹，Android studio里面会自动更新出现libs文件夹以及添加的库文件

* 右击添加的*.jar文件，选择add as library，对应的build.gradle文件中就会出现添加的库，类似compile files('libs/android-support-v4.jar')

---

#### 导入第三方java类库含源代码包，以PullToRefresh-SwipeMenuListView为例

* 下载对应的zip包，解压得到对应要添加的文件夹Library-PullToRefreshSwipeMenuListView

* 将文件夹拷贝到项目的根目录

* 修改项目的根目录下settings.gadle文件，添加

        include ':app',':library-PullToRefreshSwipeMenuListView'(原来以及添加过其他的库的话就只要在后面加上':library-PullToRefreshSwipeMenuListView')，每个库用逗号分隔

* 修改APP/build.grade文件,添加

        compile(project(':library-PullToRefreshSwipeMenuListView')) {
                exclude module: 'support-v4'
            }
            
* 以上步骤就讲第三方库导入项目了，但编译可能会报错，还要做一些修改

* 在添加的库文件夹下（如Library-PullToRefreshSwipeMenuListView）查看是否有build.gradle文件，没有的话要加上

        apply plugin: 'com.android.library'
        android {
            sourceSets {
                main {
                    manifest.srcFile 'AndroidManifest.xml'
                    java.srcDirs = ['src']
                    res.srcDirs = ['res']   //指定资源文件夹，防止和自己的项目冲突
                }
            }
            compileSdkVersion 23
            buildToolsVersion "23.0.1"
        }
        android.libraryVariants
        dependencies {//添加库本身的依赖
            compile files('libs/android-support-v4.jar')
        }

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---