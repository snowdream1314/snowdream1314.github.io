---
layout: post
title:  "Android Fragment"
date:   2016-5-26 18:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### 使用Fragment的过程中创建一个构造函数传递两个需要的变量，报错

* eclipse报出两个错：

        This fragment should provide a default constructor (a public constructor with no arguments) (com.example.TestFragment)
        Avoid non-default constructors in fragments: use a default constructor plus Fragment#setArguments(Bundle) instead
        
* 修复：
        
        官网的格式：
        public static ChildCatsFragment instance(Context context, String childCatsId, String session, String deviceId) {
            ChildCatsFragment fragment = new ChildCatsFragment();
            fragment.mContext = context;
            fragment.session = session;
            fragment.childCatsId = childCatsId;
            fragment.deviceId = deviceId;

            return fragment;
        }

---

> 参考文章：[android中Fragment的构造函数](http://blog.csdn.net/anobodykey/article/details/22503413)

---

### 结语：

坚持每天进步一点点...

---