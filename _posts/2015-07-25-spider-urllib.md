---
layout: post
title:  "爬虫之urllib/ullib2/httplib2"
date:   2015-07-27 20:50:05
categories: web
excerpt: 
---

* content
{:toc}


### 学习爬虫，记录一下python里和爬虫相关的模块urllib,urllib2

---

####设置Headers
            
        import urllib,urllib2
            
        url = 'http://www.zhihu.com/#signin'
        values = {'username':'abc', 'password':'123456'}
        user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'  (可以在浏览器调试工具中获得)
        headers = {'User-Agent':user_agent}
        data = urllib.urlencode(values)
        request = urllib2.Request(url, data, headers)
        response = urllib2.urlopen(request)
        page = response.read()

---

####在Headers中加入referer

        headers = {'User-Agent':user_agent,'Referer':'http://.....'}(浏览器调试工具里有)

---

####Proxy代理设置
        
        import urllib2
        
        enable_proxy = True
        proxy_handler = urllib2.ProxyHandler({"http" : 'http://some-proxy.com:8080'})
        null_proxy_handler = urllib2.ProxyHandler({})
        if enable_proxy:
            opener = urllib2.build_opener(proxy_handler)
        else:
            opener = urllib2.build_opener(null_proxy_handler)
        urllib2.install_opener(opener)

---

####URLError异常

        import urllib2

        req = urllib2.Request('http://...')
        try:
            urllib2.urlopen(req)
        except urllib2.URLError, e:
            if hasattr(e,"code"):
                print e.code
            if hasattr(e,"reason"):
                print e.reason
        else:
            print "OK"

---

####利用cookie模拟登录网站

        import urllib,urllib2
        import cookielib
         
        filename = 'cookie.txt'
        #声明一个MozillaCookieJar对象实例来保存cookie，之后写入文件
        cookie = cookielib.MozillaCookieJar(filename)
        opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cookie))
        postdata = urllib.urlencode({
                    'stuid':'XXXXX',
                    'pwd':'XXXX'
                })
        #登录URL
        loginUrl = 'http://xxxxx'
        #模拟登录，并把cookie保存到变量
        result = opener.open(loginUrl,postdata)
        #保存cookie到cookie.txt中
        cookie.save(ignore_discard=True, ignore_expires=True)
        #利用cookie请求访问另一个网址，此网址是成绩查询网址
        gradeUrl = 'http://xxxxx'
        #请求访问另一个网址
        result = opener.open(gradeUrl)
        print result.read()
            
---

#### 捕获404异常 ：

        status = urllib.urlopen(articleurl).code
        if status == 404 :  #有个别链接失效
            print "return 404 error"
            continue
                     
---
> 参考文章：[静觅博客](http://cuiqingcai.com/968.html)

---

### 结语：

坚持每天进步一点点...

---