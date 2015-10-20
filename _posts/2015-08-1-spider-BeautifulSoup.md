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
        
        capitalli = soup.find('li', text = "Next") #获取标签文本为"Next"的标签
        
        例： <a href="page-2.html" style="text-decoration:underline;">Next</a>
        next_page = soup.find("a", text="Next")
        
* 获取标签里的内容和特定的属性值：

        capital = capitalli.find('p').get_text()
        capital = capitalli.find('p').attrs['xxx']
        
* 标签里有多个子标签并列时：先获取全部内容，再用正则分别取特定部分

        repaymodeul = soup.find('ul', class_='clearfix')
        repaymodeli = repaymodeul.get_text()# 获取li标签下面的全部内容
        
        repaymode = re.search(u"还款方式.*",repaymodeli).group()#还款方式
        statenow = re.search(u"当前状态.*",repaymodeli).group().replace("\n","").replace("\r","").replace("\t","").replace(" ","")#当前状态

---

#### 注意事项： 

 *  汉字在IF条件中：
     
        if "分钟".decode('utf-8') in updatetimeitem  #一般都要加decode('utf-8')，否则会出现编码错误

 *  通过标签属性筛选
 
        find("img", {"alt":True})       #表示有alt属性的img标签  
        find("img", {"alt":None})       #表示没有alt属性的img标签，也可以写False
        find("img", {"alt":None,"class":True})      #可以多个属性过滤

 *  选择合适的标签属性
 
        item.image = list.find("div", {"class":"hlist-list-pic"}).find("img", {"alt":True}).attrs['data-src']
        #在取图片的标签时要注意区分动态链接，即选择合适的attrs，一般选没有下划线即不含其它链接的
        
 *  链接
 
        item.href = "http://www.huihui.cn" + str (item.href.find("a", {"data-log":True}).attrs['href'])
        #上面的情况最好加上 str（）
        
 *  看清标签之间的关系，特别是没有三角形的标签，即找对标签之间的父子关系
 
---            

> 参考文章：[静觅博客](http://cuiqingcai.com/1319.html)

---

### 结语：

坚持每天进步一点点...

---