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

* urlparse 还可以编码解码URL。以get方式提交数据的时候，会在url中添加key=value这样的字符串，所以在value中是不允许有’='，因此要对其进行编码；与此同时服务器接收到这些参数的时候，要进行解码，还原成原始的数据。在爬取网站信息的时候，有时候要获取URL里的信息，就可以用urlparse方法

        import urlparse
        from urlparse import unquote

        url = "http://www.google.com/support/contact/bin/request.py?entity=%7B%22author%22:%22AIe9_BEW4fia2hKVVTrlUwNzhLS-jMdh3isj0rMd7_Cw85R1-YlRNFkUwoDyhH4aMj7AdHsW5A1po8BinbxspAuLBdB-or_3YzCMNXZKYrb50MIIJCZEpb4%22,%22groups%22:%5B%22general%22,%2254296%7C700726330%22%5D,%22trustedMerchantId%22:%22MID_54316%22%7D&amp;client=242&amp;contact_type=anno&amp;hl=en_US"
        a= urlparse.urlparse(url).query    # 获取？后面的内容
        b = unquote(a)     #解码获得的内容
        c = urlparse.parse_qs(b,True)  # 获得一个字典
        d = json.loads(c['entity'][0]) # 最终得到了包含需要信息的JSON格式的字典
        print d.['groups'][-1]
        
        # 输出最终需要的数据
        54296|700726330
                
* 主要就用到这个方法
        
---

#### 还有分析网页源码中的JSON格式文本可以用这个网站： [Online JSON Viewer](http://jsonviewer.stack.hu/)

---


> 参考文章：[Python2.7 urlparse学习](http://my.oschina.net/guol/blog/95699)

---

### 结语：

坚持每天进步一点点...

---