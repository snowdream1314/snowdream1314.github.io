---
layout: post
title:  "Python编码问题汇总"
date:   2015-07-30 20:50:05
categories: python
excerpt: 
---

* content
{:toc}


### 记录一下python里和编码相关的问题

---

####把list或dict中的中文写入文件

> * 方法是是把list或dict转化成josn格式的字符串输出
            
        import urllib,urllib2,json
        from bs4 import BeautifulSoup  
            
        url = 'http://www.idianhui.com/invest/detail.html?borrowid=11'
        req = urllib2.Request(url)
            
        response = urllib2.urlopen(req)
        mypage = response.read()
        unicodepage = mypage.decode("utf-8")
        #print unicodepage
      
        soup = BeautifulSoup(unicodepage)
        print soup.find('h1').get_text()
        a = {}
        b = []
        title = soup.find('h1').get_text()
        a['title'] =title
        
        b.append(json.dumps(a,indent=4,ensure_ascii=False))
        
        fileName ='invest.txt'
        f = open(fileName,'w+')
        
        for item in b :
            item = item.encode('utf-8')
            f.write(item)
        f.close()
        
---        

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---