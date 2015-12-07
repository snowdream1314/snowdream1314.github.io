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

#### 爬网页将网络数据流写入到本地文件、写文件时常见的一个编码问题

> * 问题：UnicodeEncodeError: 'gbk' codec can't encode character '\xa0' in position ... 

> * 分析：在windows下面编写python脚本，编码问题很严重。

   * 将网络数据流写入文件时时，我们会遇到几个编码：

    1：如python文件第一行的内容 "#encoding='XXX'"的编码是指该python脚本文件本身的编码，无关紧要。只要XXX和文件本身的编码相同就行了。 比如notepad++ "格式"菜单里面里可以设置各种编码，这时需要保证该菜单里设置的编码和encoding XXX相同就行了，不同的话会报错

    2：网络数据流的编码 ： 比如获取网页，那么网络数据流的编码就是网页的编码。需要使用decode解码成unicode编码。

    3：目标文件的编码 ： 要将网络数据流的编码写入到新文件，那么我们需要指定新文件的编码。写文件代码如：f.write(txt)，那么txt是一个字符串，它是通过decode解码过的字符串。关键点就要来了：目标文件的编码是导致标题所指问题的罪魁祸首。如果我们打开一个文件： f = open("out.html","w")，在windows下面，新文件的默认编码是gbk，这样的话，python解释器会用gbk编码去解析我们的网络数据流txt，然而txt此时已经是decode过的unicode编码，这样的话就会导致解析不了，出现上述问题。 解决的办法就是，改变目标文件的编码： f = open("out.html","w",encoding='utf-8')。这样，问题将不复存在
    
    4. 在windows下面的CMD里运行爬虫程序时，有时也会出现编码问题。解决办法是： 修改CMD的默认代码页选项; 或者把一些要输出到屏幕的带有中文的取消。
    
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

### base64编解码

        import base64
        origin_url = "http://www.juanpi.com/ref_click?url=aHR0cDovL2l0ZW0udGFvYmFvLmNvbS9pdGVtLmh0bT9pZD00MzM1Mjc4MDA4NA=="
        #origin_url中的url后面的一串就是经过64编码的，取出来以后解码就可得到最终链接
        url = origin_url.split("url")
        try :   
            real_url_b64 = real_url.split('url')[-1]
            real_url = base64.b64decode(real_url_b64)
            print "[XXXXX] real url3:", real_url
        except Exception,e :
            print str(e)
        
        #解码的时候很容易出现错误："Incorrect padding";原因是有长度限制，需要补齐,具体可上网查询。
        
---
> 参考文章：

---

### 结语：

坚持每天进步一点点...

---