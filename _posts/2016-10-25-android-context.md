---
layout: post
title:  "Andriod getContext和getActivity"
date:   2016-10-25 9:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

* MainActivity.this：表示MainActivity对象，一般用在内部类中指示外面的this，如果在内部类直接用this，指示的是内部类本身。因为MainActivity继承Activity，而Activity继承Context，所以它也可以用来提供Activity Contex；

* this：表示当前对象；一般而言，在哪个类中调用，就是指向该对象。

* getContext()：这个是View类中提供的方法，在继承了View的类中才可以调用，返回的是当前View运行在哪个Activity Context中。

* getActivity():获得Fragment依附的Activity对象。Fragment里边的getActivity()不推荐使用原因如下：这个方法会返回当前Fragment所附加的Activity，当Fragment生命周期结束并销毁时，getActivity()返回的是null，所以在使用时要注意判断null或者捕获空指针异常。

> 在Fragment中获取上下文Context一般用getActivity:

        全局Application:
        public class Application extends Application {


            private static Application instance;

            @Override
            public void onCreate() {
                super.onCreate();
                instance = this;
            }

            public static Application getInstance(){
                return instance;
            }


        }


        在Fragment中直接使用getContext方法容易产生空指针异常，覆写getContext方法
        private Activity activity;

        public Context getContext() {
            activity = getActivity();

            if (activity == null) {
                return HSApplication.getInstance();
            }

            return activity;
        }


---

### 结语：

坚持每天进步一点点...

---