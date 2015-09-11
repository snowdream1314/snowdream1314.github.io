---
layout: post
title:  "Python之urlparse"
date:   2015-09-11 17:30:05
categories: python
excerpt: 
---

* content
{:toc}


### 记录下编写爬虫中遇到的问题 ： 提取URL中的参数

---

#### Python urlparse模块

* python中使用urlparse包中的urlparse方法来解析url

* urlparse模块主要是把url拆分为6部分，并返回元组。并且可以把拆分后的部分再组成一个url。主要有函数有urljoin、urlsplit、urlunsplit、urlparse等。

* urlparse.urlparse(urlstring[, scheme[, allow_fragments]])将urlstring解析成6个部分，它从urlstring中取得URL，并返回元组 (scheme, netloc, path, parameters, query, fragment)，但是实际上是基于namedtuple，是tuple的子类。它支持通过名字属性或者索引访问的部分URL，每个组件是一串字符，也有可能是空的。组件不能被解析为更小的部分，%后面的也不会被解析，分割符号并不是解析结果的一部分，除非用斜线转义，注意，返回的这个元组非常有用，例如可以用来确定网络协议(HTTP、FTP等等 )、服务器地址、文件路径，等等。

* 例如：
        import urlparse
        
        url=urlparse.urlparse('http://www.baidu.com/index.php?username=guol')
        >>> print url
        ParseResult(scheme='http', netloc='www.baidu.com', path='/index.php', params='', query='username=guol', fragment='')
        >>> print url.netloc
        www.baidu.com
        
* 主要就用到这个方法
        
---

### 还有分析网页源码中的JSON格式文本可以用这个网站： [Online JSON Viewer](http://jsonviewer.stack.hu/)

---


> 参考文章：[Python2.7 urlparse学习](http://my.oschina.net/guol/blog/95699)

---

### 结语：

坚持每天进步一点点...

---