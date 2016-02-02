---
layout: post
title:  "Python Dict"
date:   2015-10-9 15:08:05
categories: python
excerpt: 
---

* content
{:toc}

### Python --- Dict

---

#### python的Dict合并

        dict1={1:[1,11,111],2:[2,22,222]}
        dict2={3:[3,33,333],4:[4,44,444]}
        合并两个字典得到类似
        {1:[1,11,111],2:[2,22,222],3:[3,33,333],4:[4,44,444]}
        
        方法1：
        dictMerged1=dict(dict1.items()+dict2.items())
        
        方法2：
        dictMerged2=dict(dict1, **dict2)
        
        方法2等同于：

        dictMerged=dict1.copy()
        dictMerged.update(dict2)

        或者

        dictMerged=dict(dict1)
        dictMerged.update(dict2)
        
        方法2比方法1速度快很多     
        
---


> 参考文章：

---

### 结语：

坚持每天进步一点点...

---