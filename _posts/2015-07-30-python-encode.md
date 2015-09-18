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
            
        import urllib,urllib2,json,codecs
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
        a['title'] =title   #中文标题
        
        b.append(a)     #存入list
        
        fileName ='invest.txt'
        f = open(fileName,'w+')
        
        #模块codecs提供了一个open()方法，可以指定一个编码打开文件，使用这个方法打开的文件读取返回的将是unicode。有中文时会先将GBK编码的str解码为unicode再编码为UTF-8写入
        with codecs.open(filename, 'w+', 'utf-8') as f:
            f.write(json.dumps(a,indent=4,sort_keys=True,ensure_ascii=False))
            f.close()
        
        
---        

####去除字符串中的空格、换行符(\n)、tab(\t)、新行(\r)

        str..replace("\n","").replace("\r","").replace("\t","").replace(" ","")
        
* strip()函数可以删除对象中特定的字符，包括空格：

        声明：s为字符串，rm为要删除的字符序列
        s.strip(rm)        删除s字符串中开头、结尾处，位于 rm删除序列的字符
        s.lstrip(rm)       删除s字符串中开头处，位于 rm删除序列的字符
        s.rstrip(rm)      删除s字符串中结尾处，位于 rm删除序列的字符
        当rm为空时，默认删除空白符（包括'\n', '\r',  '\t',  ' ')
        
        a = 'abc\r\n'
        a.strip()
        #'abc'

        
* 去除字符串中间的空格：
        s = "d      d"
        s1 = ''join(s.split())
        s2 = ' 'join(s.split())
        print s1
        print s2 
        
        # 输出为：
        dd
        d d       
        
---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---