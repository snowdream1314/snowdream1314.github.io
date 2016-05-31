---
layout: post
title:  "Python 特殊方法"
date:   2015-2-3 11:36:05
categories: python
excerpt: 
---

* content
{:toc}

---

### 记录用到的Python特殊方法

---

#### Python --- 特殊方法

* 特殊方法名的前后各有两个下划线。特殊方法又被成为魔法方法(magic method)，定义了许多Python语法和表达方式。Python一切皆对象。当对象中定义了特殊方法的时候，Python也会对它们有“特殊优待”。

* 可以通过dir()来查看对象所拥有的特殊方法，比如dir(1)

---

*  特殊方法之__call__()
       
*  在Python中，函数也是一种对象。实际上，任何一个有__call__()特殊方法的对象都被当作是函数。
 
        class SampleMore(object):
            def __call__(self, a):
                return a + 5

        add = SampleMore()     # A function object
        print(add(2))          # Call function    
        map(add, [2, 4, 5])    # Pass around function object   
            
---

*  特殊方法之__enter__()和__exit__()

*  任何定义了__enter__()和__exit__()方法的对象都可以用于上下文管理器。(上下文管理器的语法形式是with...as...)
 
        # customized object

        class VOW(object):
            def __init__(self, text):
                self.text = text
            def __enter__(self):
                self.text = "I say: " + self.text    # add prefix
                return self                          # note: return an object
            def __exit__(self,exc_type,exc_value,traceback):
                self.text = self.text + "!"          # add suffix


        with VOW("I'm fine") as myvow:
            print(myvow.text)

        print(myvow.text)
            
---

*  特殊方法之__getattr__()和_getattribute__()

*  在Python中，可以用__getattr__(self, name)来查询即时生成的属性，__getattr__可以将所有的即时生成属性放在同一个函数中处理。

        class bird(object):
            feather = True

        class chicken(bird):
            fly = False
            def __init__(self, age):
                self.age = age
            def __getattr__(self, name):
                if name == 'adult':
                    if self.age > 1.0: return True
                    else: return False
                else: raise AttributeError(name)

        summer = chicken(2)

        print(summer.adult)
        summer.age = 0.5
        print(summer.adult)

        print(summer.male)

* Python中还有一个__getattribute__特殊方法，用于查询任意属性。__getattr__只能用来查询不在__dict__系统中的属性。
         
* 此外__setattr__(self, name, value)和__delattr__(self, name)可用于修改和删除属性。它们的应用面更广，可用于任意属性。  

* 还可以使用函数 hasattr(obj, attr_name)   # attr_name是一个字符串,来确认一个对象是否具有某个属性

        a = [1,2,3]
        print(hasattr(a,'append'))
        #True
          
---

> 参考文章：[Python深入01 特殊方法与多范式](http://www.cnblogs.com/vamei/archive/2012/11/19/2772441.html)

---

### 结语：

坚持每天进步一点点...

---