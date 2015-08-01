---
layout: post
title:  "爬虫之BeautifulSoup"
date:   2015-07-30 14:30:05
categories: web
excerpt: 
---

* content
{:toc}


### 学习爬虫，记录一下BeautifulSoup模块

---

####XP下载安装Beautifulsoup

* 地址  [Beautifulsoup](https://pypi.python.org/pypi/beautifulsoup4/4.3.2)

* 安装好后直接在文件中导入即可：

        from bs4 import BeautifulSoup  

---

####Beautifulsoup使用

* 首先建立soup对象：

        url = 'http://www.idianhui.com/invest/detail.html?borrowid=11'
        req = urllib2.Request(url)
            
        response = urllib2.urlopen(req)
        mypage = response.read()
        soup = Beautifulsoup(mypage)
        
* 获取特定的标签：

        capitalli = soup.find('li', class_='account')#获取class为'account'的li标签
        
* 获取标签里的内容：

        capital = capitalli.find('p').get_text()
        
* 标签里有多个子标签并列时：先获取全部内容，再用正则分别取特定部分

        repaymodeul = soup.find('ul', class_='clearfix')
        repaymodeli = repaymodeul.get_text()# 获取li标签下面的全部内容
        
        repaymode = re.search(u"还款方式.*",repaymodeli).group()#还款方式
        statenow = re.search(u"当前状态.*",repaymodeli).group().replace("\n","").replace("\r","").replace("\t","").replace(" ","")#当前状态
        
---            

> 参考文章：[静觅博客](http://cuiqingcai.com/1319.html)

---

### 结语：

坚持每天进步一点点...

---