---
layout: post
title:  "Python 内建函数"
date:   2015-10-10 15:36:05
categories: Python
excerpt: 
---

* content
{:toc}

###记录用到的Python内建函数

#### Python --- 内建函数

---

##### Python isdigit() 方法检测字符串是否只由数字组成。

        语法：
        str.isdigit() #无参数
        
        返回值：如果字符串只包含数字则返回 True 否则返回 False
---
       
##### Python内建的filter()函数用于过滤序列。

        语法：
        filter(f,[1,2,3])   #filter()接收一个函数和一个序列。
        
        返回值：filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。
        
        例：在一个list中，删掉偶数，只保留奇数
        
            def is_odd(n):
                return n % 2 == 1

            filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15])
            # 结果: [1, 5, 9, 15]
        
        例：提取一个字符串中的所有数字
        
            a = "更新于： 2015年10月10日 15:30"
            
            filter(lambda x:x.isdigit(),a)
            
            #结果： 201510101530
            
---

##### Python内建的map()函数

        语法：
        map(f,[1,2,3])  #map()函数接收两个参数，一个是函数，一个是序列
        
        返回值：map()函数把将传入的函数依次作用到序列的每个元素，并把结果作为新的list返回
        
        例：我们有一个函数f(x)=x*x，要把这个函数作用在一个list [1, 2, 3, 4, 5, 6, 7, 8, 9]上，就可以用map()实现
        
            def f(x):
                return x * x

            map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
            
            #结果：[1, 4, 9, 16, 25, 36, 49, 64, 81]
            
---

##### Python内建的reduce()函数

        语法：
        reduce(f, [x1, x2, x3, x4])     #reduce函数接收两个参数，一个是函数，一个是序列
        
        返回值：reduce函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算
        
        例：对一个序列求和
        
            def add(x, y):
                return x + y
                
            reduce(add, [1, 3, 5, 7, 9])
            
            #结果：25
---


> 参考文章：[廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/00141861202544241651579c69d4399a9aa135afef28c44000)

---

### 结语：

坚持每天进步一点点...

---