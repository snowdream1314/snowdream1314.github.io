---
layout: post
title:  "Python中assert断言"
date:   2016-1-19 10:00:05
categories: python
excerpt: 
---

* content
{:toc}


### Python中使用assert断言

---

#### Python中assert用来判断语句的真假，如果为假的话将触发AssertionError错误

        例如：
        key = ['item']
        assert len(key) == 1
        
        key.pop()
        assert len(key) == 1
        //触发AssertionError错误
        Traceback (most recent call last):
            File "<stdin>", line 1, in <module>
        AssertionError
