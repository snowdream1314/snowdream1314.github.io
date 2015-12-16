---
layout: post
title:  "重定向之urllib/ullib2/httplib2"
date:   2015-12-16 14:50:05
categories: web
excerpt: 
---

* content
{:toc}


### 记录一下python里的urllib2和httplib2模块关于重定向方面的用法

---

#### urllib2会自动处理重定向（301/302），得到最终的链接
            
        import urllib2
        url = "http://www.55haitao.com/g/4137-d-234178"  
        
        loop_count = 0
        while loop_count < 5 :  #有时候会返回503错误，多请求几次
            loop_count +=1
            try:
                request = urllib2.Request(url)
                opener = urllib2.build_opener()
                f = opener.open(request)
                print f.url
                return f.url
                
            except Exception, e :
                print str(e)
            
---

#### httplib2 也能跟踪重定向，还可以限制跟踪重定向的次数,默认是5

* Http.request(uri[, method="GET", body=None, headers=None, redirections=DEFAULT_MAX_REDIRE)

        import httplib2
        url = "http://www.55haitao.com/g/4137-d-234178"  
        
        loop_count = 0
        while loop_count < 5 :  #有时候会返回503错误，多请求几次
            loop_count +=1
            try :
            h = httplib2.Http()
            resp, content = h.request(url, "GET", redirections=10)
            realurl = resp['content-location']      #获取请求头中的url
            print realurl
            return realurl
                
            except Exception, e :
                print str(e)
                
---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---