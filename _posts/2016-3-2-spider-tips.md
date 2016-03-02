---
layout: post
title:  "爬虫 技巧"
date:   2016-03-2 16:08:05
categories: spider
excerpt: 
---

* content
{:toc}

#### 这篇文章主要记录一些写爬虫过程中的一些 Tips

---

#### Google Chrome的使用

* Google Chrome 是一个很好的调试工具。其中的 Network 的 XHR 功能可以跟踪网页的ajax请求，获取源地址。比较常见的2种情况是 “加载更多” 和 点击执行js代码来更新的网页。对于这种情况，开启 XHR以后就可以获取资源的源地址，从而可以进一步抓取

* Chrome的 Network  还可以显示浏览器请求的其他重要信息，如：Headers, Response, Cookies等。其中Headers中可以查看请求是否有跳转（Status Code为302,301），其他信息可以用来模拟浏览器，防止爬虫被禁。常见的字段有：User-Agent,Host, Referer, Upgrade-Insecure-Requests,以及Cookie.

---

#### 其他

* [Online JSON Viewer](http://jsonviewer.stack.hu/) 是一个在线解析Json数据的工具，可以把Json格式的数据粘贴进去，查看Json的结构
  
---


> 参考文章：

---

### 结语：

坚持每天进步一点点...

---