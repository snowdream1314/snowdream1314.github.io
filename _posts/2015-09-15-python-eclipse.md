---
layout: post
title:  "Eclipse"
date:   2015-09-15 9:47:05
categories: Eclipse 
excerpt: 
---

* content
{:toc}

### 记录下使用Eclipse过程中用到的快捷键

---

#### Eclipse 快捷键

* Ctrl+Q 定位到最后编辑的地方
* Ctrl+T 快速显示当前类的继承结构
* Ctrl+Z 全局 撤销 
* Ctrl+S 全局 保存
* Ctrl+A 全局 全部选中
* Ctrl+M 最大化当前的Edit或View (再按则反之)
* Ctrl+/ 注释当前行,再按则取消注释
* Ctrl+D 删除当前行  
* Alt+方向键 将当前行的内容往上或下移动 (上下键)(很多其他软件有后退的功能)、切换文件（左右键）
* Ctrl+Shift+o 导入相应的模块（包）

> 文件切换三种方式

* Ctrl + E, 在右边显示出当前打开的所有文件，可以切换文件
* Ctrl + Pg Up ,Ctrl + Pg Dn ,逐个文件跳跃
* Ctrl + F6，在当前文件里显示当前打开的所有文件

> 显示当前类的所有属性和方法

* Ctrl + O就可以显示当前文件的所有属性和方法。注意是大写字母O,不是数字0。

* Ctrl + W可以关闭当前文件，Ctrl + Shift +Ｗ可以关闭当前打开的所有文件。 关闭当前文件后想重新打开,使用Alt + ← 即可。

> 快速定位当前文件在工程中的位置,即在package视图中的位置

* Alt + Shift +W,显示视图，然后选择package视图   

* Ctrl + H 进行全局搜索和局部搜索
* Ctrl + F7 视图切换
* Ctrl + Shift + ↓ 当你想知道当前方法的下一个方法是哪个的时候，只需按下Ctrl + Shift + ↓即可达到目的，这个快捷键同样适用于变量名。

> 重构

* 假设你要修改方法的参数名字、或者类变量、方法变量，同时这些变量又被很多代码引用着，那么你可以直接使用Alt + Shift + R 统一修改一次即可。
* 如果一个方法的代码太长，你想抽取子方法，那么只要选中某块代码，然后使用Alt + Shift + M,就会弹出抽取视图，输入方法名即可。

* Ctrl + Shift +G,找出调用某个方法的所有类
* Ctrl + K 和Ctrl + Shfit + K,查找文本.当你选中某个文件中的某些字符串时，你可以使用Ctrl + K向下寻找相同的字符串，使用Ctrl + Shfit +ｋ向上寻找相同的字符串。

> 如何快速生成for循环、switch 等代码块

* 想编写一个switch代码块，只需输入switch，然后使用Alt + /

* Ctrl + Shift + Y 变小写,Ctrl +Shift + X变大写。
* Alt + Shift + N 要在当前文件所在的包中，创建一个新的类，可以使用Alt + Shift + N,调出创建视图，选择相关类型即可。
* Ctrl  + N 想在任意目录下创建的话，使用Ctrl  + N
* Ctrl + Shift + P 根据左大括号找到右大括号
* Ctrl + L,输入行数 跳转到文件中的特定行
* Ctrl + shift + l,Eclipse会在右下角弹出快捷键列表

---


> 参考文章：[能够提高开发效率的Eclipse实用操作](http://blog.csdn.net/linsongbin1/article/details/48954353?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

---

### 结语：

坚持每天进步一点点...

---