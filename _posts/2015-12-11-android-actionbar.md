---
layout: post
title:  "Android App 之 Actionbar"
date:   2015-12-11 12:08:05
categories: Android
excerpt: 
---

* content
{:toc}

### Android actionbar 

---

#### 按钮就是不显示问题

* 按照官网教程编码，设置的2个按钮就是不显示，上网一查是Android UI本身逻辑的问题。overflow button或overflow menu在新的Android 3.0+的系统中，默认是不显示的。

* 对于很多PAD来说：ActionBar中空间足够显示的话，那么对应各个menu菜单，都直接显示在ActionBar中；

>对于很多手机来说：ActionBar中没有足够的控件显示所有的菜单的话，余下的菜单，就被藏起来了->只有有物理菜单（MENU）键的Android设备，点击MENU键，才能出现多余的菜单；

---

####问题解决

*来自牛人的破解···

        public class MyActivity extends ActionBarActivity {
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                ...
                 
                getOverflowMenu();
            }
         
            //force to show overflow menu in actionbar for android 4.4 below
            private void getOverflowMenu() {
                 try {
                    ViewConfiguration config = ViewConfiguration.get(this);
                    Field menuKeyField = ViewConfiguration.class.getDeclaredField("sHasPermanentMenuKey");
                    if(menuKeyField != null) {
                        menuKeyField.setAccessible(true);
                        menuKeyField.setBoolean(config, false);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }

---


> 参考文章：[官方培训教程中文版](http://hukai.me/android-training-course-in-chinese/basics/actionbar/setting-up.html)
            []

---

### 结语：

坚持每天进步一点点...

---