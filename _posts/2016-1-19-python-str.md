---
layout: post
title:  "Python中class内置函数__str__"
date:   2016-1-19 9:30:05
categories: python
excerpt: 
---

* content
{:toc}


### Python中class内置函数__str__

---

#### str__方法和__init__方法类似，都是一些特殊方法，所以前后都有双下划线，它用来返回对象的字符串表达式。在我们编写一个新的Python类的时候，总是在最开始位置写一个初始化方法__init__，以便初始化对象，然后会写一个__str__方法，方面我们调试程序。

        例如：
        class Friend():
        
            def __init__(self,name):
                self.name = name
            def __str__(self):
                return "Friend: %s" %self.name
        
        if __name__ == 'main':
            friend = Friend('wang')
            print friend
            
        没有__str__方法时，输出的是实例friend的存储地址，如：<__main__ .Friend instance at 0x7ff3596c>；
        有__str__方法时，print会调用__str__方法，输出的是__str__方法中的return内容，如：Friend:wang 

---

#### __str__方法的限制

 * __str__方法只能返回str，并且只能有self这一个参数