---
layout: post
title:  "Python 内建函数"
date:   2015-10-10 15:36:05
categories: python
excerpt: 
---

* content
{:toc}

### 记录用到的Python内建函数

---

#### Python --- 内建函数

---

 * Python isdigit() 方法检测字符串是否只由数字组成。

        语法：
        str.isdigit() #无参数
        
        返回值：如果字符串只包含数字则返回 True 否则返回 False
        
---
       
 *  Python内建的filter()函数用于过滤序列。

        语法：
        filter(f,[1,2,3])   #filter()接收一个函数和一个序列。
        
        返回值：filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。
        返回值得类型是“str”
        
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

 * Python内建的map()函数

        语法：
        map(f,[1,2,3])  #map()函数接收两个参数，一个是函数，一个是序列
        
        返回值：map()函数把将传入的函数依次作用到序列的每个元素，并把结果作为新的list返回
        
        例：函数f(x)=x*x，要把这个函数作用在一个list [1, 2, 3, 4, 5, 6, 7, 8, 9]上，就可以用map()实现
        
            def f(x):
                return x * x

            map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
            
            #结果：[1, 4, 9, 16, 25, 36, 49, 64, 81]
            
---

 *  Python内建的reduce()函数

        语法：
        reduce(f, [x1, x2, x3, x4])     #reduce函数接收两个参数，一个是函数，一个是序列
        
        返回值：reduce函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算
        
        例：对一个序列求和
        
            def add(x, y):
                return x + y
                
            reduce(add, [1, 3, 5, 7, 9])
            
            #结果：25
            
* reduce()函数在3.0里面不能直接用的，它被定义在了functools包里面，需要引入包

---

 *  Python内建的startswith()函数 

        语法：
        string.startswith(str, beg=0,end=len(string)) 或 string[beg:end].startswith(str)
        
        参数说明：
        string：  被检测的字符串
        str：      指定的字符或者子字符串。（可以使用元组，会逐一匹配）
        beg：    设置字符串检测的起始位置（可选）
        end：    设置字符串检测的结束位置（可选）
        如果存在参数 beg 和 end，则在指定范围内检查，否则在整个字符串中检查

        返回值
        如果检测到字符串，则返回True，否则返回False。默认空字符为True

        函数解析：如果字符串string是以str开始，则返回True，否则返回False
        
        例：用于if判断
        a = "/0-15-2"
        if a.startswith('/0') : 
            print "true"
        else :
            print "false"
        
        #输出： true
   
---

 * Python内建的join()函数用于将序列中的元素以指定的字符连接生成一个新的字符串
 
        语法：
        str.join(sequence)
        
        参数说明：
        sequence -- 要连接的元素序列
        
        返回值：
        返回通过指定字符连接序列中元素后生成的新字符串
        
        例：
        str = "-";
        seq = ("a", "b", "c"); # 字符串序列
        print str.join( seq );
        
        #输出：a-b-c
   
---

* Python内建的chr(x)函数返回整数i对应的ASCII字符。

        参数x：取值范围[0, 255]之间的正数。
        例：
        chr(97)
        #输出：'a'

---

* Python内建的ord(x)函数返回整数ASCII字符x对应的十进制整数。

        参数x：ascii字符。
        例：
        chr('a')
        #输出：97

---

* Python内建的cmp(x,y) 函数用于比较2个对象，如果 x < y 返回 -1, 如果 x == y 返回 0, 如果 x > y 返回 1。

        例：
        cmp(1,2)
        #输出：-1
        
        cmp('a','a')
        #输出：0

---

* Python内建的 enumerate()函数，可以在每次循环中同时得到下标和元素：

        例：
        a = 'abcdef'
        for (index,value) in enumerate(a):
        print index,a
        
        #输出：
            0 a
            1 b
            2 c
            3 d
            4 e
            5 f

---

* Python内建的 zip()函数的功能，就是从多个列表中，依次各取出一个元素。每次取出的(来自不同列表的)元素合成一个元组，合并成的元组放入zip()返回的列表中。zip()函数起到了聚合列表的功能：

        例：
        ta = [1,2,3]
        tb = [9,8,7]
        tc = ['a','b','c']
        for (a,b,c) in zip(ta,tb,tc):
            print(a,b,c)
        
        输出：
        (1, 9, 'a')
        (2, 8, 'b')
        (3, 7, 'c')
        
---

* 其他内建函数：

        round(2.6)             # 四舍五入取整，也就是3.0
        max([1,5,2,9])         # 求最大值
        min([9,2,-4,2])        # 求最小值
        sorted([1,5,3])        # 返回正序的序列，也就是[1,3,5]
        reversed([1,5,3])      # 返回反序的序列，也就是[3,5,1]
        
---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---