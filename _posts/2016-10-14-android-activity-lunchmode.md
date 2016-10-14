---
layout: post
title:  "Android Activity启动模式"
date:   2016-10-14 11:00:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Activity启动模式

> 在 manifest 文件中声明 activity 时，你可以利用 <activity> 元素的 launchMode 属性来设定 activity 与 task 的关系。 launchMode 属性可设为四种启动模式：

* "standard" （默认模式）:

    默认值。系统在启动 activity 的 task 中创建一个新的 activity 实例，并把 intent 传送路径指向它。该 activity 可以被实例化多次，各个实例可以属于不同的 task，一个 task 中也可以存在多个实例。

* "singleTop":

    如果 activity 已经存在一个实例并位于当前 task 的栈顶，则系统会调用已有实例的onNewIntent()方法把 intent 传递给已有实例，而不是创建一个新的 activity 实例。activity 可以被实例化多次，各个实例可以属于不同的 task，一个 task 中可以存在多个实例（但仅当 back stack 顶的 activity 实例不是该 activity 的）。
    比如，假定 task 的 back stack 中包含了根 activity A 和 activities B、C、D（顺序是 A-B-C-D；D 在栈顶）。
    这时过来一个启动 D 的 intent。如果 D 的启动模式是默认的"standard"，则会启动一个新的实例，栈内容变为 A-B-C-D-D。
    但是，如果 D 的启动模式是"singleTop"，则已有的 D 实例会通过onNewIntent():接收这个 intent，因为该实例位于栈顶——栈中内容仍然维持 A-B-C-D 不变。当然，如果 intent 是要启动 B 的，则 B 的一个新实例还是会加入栈中，即使 B 的启动模式是"singleTop"也是如此。

    > 注意： 一个 activity 的新实例创建完毕后，用户可以按回退键返回前一个 activity。 但是当 activity 已有实例正在处理刚到达的 intent 时，用户无法用回退键回到 onNewIntent() 中 intent 到来之前的 activity 状态。

* "singleTask":

    系统将创建一个新的 task，并把 activity 实例作为根放入其中。但是，如果 activity 已经在其它 task 中存在实例，则系统会通过调用其实例的onNewIntent() 方法把 intent 传给已有实例，而不是再创建一个新实例。 此 activity 同一时刻只能存在一个实例。

    > 注意： 虽然 activity 启动了一个新的 task，但用户仍然可以用回退键返回前一个 activity。

    补充：

    关于singleTask这个网上颇有争议，google api说singTask模式只能启动一个task，且总是位于栈底，这个也不是完全正确

    分2种情况：

    　　1、如果在同一个应用(apk)中使用singleTask，刚不在栈底，对应于下面的情况一

    　　2、如果从不同应用启动一个singleTask的activity，刚依赖于此activity所在的栈，如果之前没有运行过，则新建栈处于栈底，如果有运行过，则有可能不在栈底，对应于情况二

        　情况一：如果在本程序中启动singleTask的activity：假设ActivityA是程序的入口，是默认的模式（standard）,ActivityB是singleTask 模式，由ActivityA启动，刚ActivityB不会位于栈底，不是根元素，不会启动新的task，此种情况ActivityB会和ActivityA在一个栈中，位于ActivityA上面

    　　  情况二：如果ActivityB由另外一个程序启动：假设apkA是情况一中的应用，apkB是另外一个测试程序，在apkB中启动apkA中的ActivityB，再分两种情况，如果ActivityB未启动过，刚ActivityB会位于栈底，是根元素，会启动新的task；如果ActivityB启动过，则ActivityB保持原来的位置不变，在栈底或者栈顶，移除掉ActivityB之上所有的activity(如果有)

* "singleInstance":

    除了系统不会把其它 activity 放入当前实例所在的 task 之外，其它均与"singleTask"相同。activity 总是它所在 task 的唯一成员；它所启动的任何 activity 都会放入其它 task 中。

> 除了标准模式以外， 都需要复写onNewIntent()方法,以便获取最新的Intent:

        @Override
        protected void onNewIntent(Intent intent) {
            super.onNewIntent(intent);
            Log.e("onNewIntent", " called");

            setIntent(intent);
            getIntent().putExtras(intent);//将最新的intent共享出去

        }
        

---

> 参考文章：[Tasks and Back Stack](http://www.android-doc.com/guide/components/tasks-and-back-stack.html)

---

### 结语：

坚持每天进步一点点...

---