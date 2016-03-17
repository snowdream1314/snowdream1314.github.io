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

#### selenium模拟浏览器爬取动态页面

        #coding = utf-8

        from selenium import webdriver
        from selenium.webdriver.support.ui import WebDriverWait
        from bs4 import BeautifulSoup as bs4
        import time

        dr = webdriver.Chrome()
        # driver = webdriver.PhantomJS()
        dr.get('http://v.youku.com/v_show/id_XMTQ4ODEwOTI4OA==.html?from=y1.12-95')
        WebDriverWait(dr,10).until(lambda the_driver: the_driver.find_element_by_id('video_comment_number').get_attribute('title') != '')
        # time.sleep(15)
        # print browser.title.encode('utf-8')
        # data = browser.find_element_by_xpath("//div[@id='fn_comment']/a/span[2]")
        page_soup = bs4(dr.page_source)
        data = page_soup.find("span", {"id":"videoTotalComment"}).get_text()
        # data = browser.find_element_by_id('videoTotalPV').find_element_by_class('num').text
        print "data is %s" % data
        # time.sleep(5)
        dr.quit()

* 需要先配置好环境，安装selenium、PhantomJS、chromedriver

* 由于是运行在远程服务器而不是在本地，服务器显示器关闭的时候，当调用的火狐浏览器时就会报错：The browser appears to have exited before we could connect. The output was: Error: no display specified\n; 调用chrome浏览器时也会报错：unknown error: Chrome failed to start；


---

#### BeautifulSoup相关

* nonetype错误： 有时候网页里明明有的标签却反应找不到，如下一页的链接，可能是因为页面加载的时候没有得到，可以捕捉这个错误，再次加载。

        try: 
            next_page = selector.find("li", {"class":"next"}).find("a")
        except AttributeError:
            print "catch AttributeError"
            selector = load_content(source_url, method='GET')
            next_page = selector.find("li", {"class":"next"}).find("a")

---

#### 有关javascripts脚本方面的

* 有些网站是用ajax异步请求生成的页面，直接爬获取不到数据。这个可以通过查看网页源码来判断。在 Google Chrome的开发者工具XHR中可以查看具体的请求资源过程。

* 一部分网站是用post请求来获取数据渲染网页，其中一般会带有加密的关键字段。有些是通过js计算生成的，这个可以查看请求的js文件。js文件可以用一些格式化js的网站来格式化，这样便于分析具体的代码段。如[http://javascript.51240.com/](http://javascript.51240.com/)

* 此外还需要一些javascripts方面的知识：

    jQuery的each方法：
    如：
    var arr1 = [[1, 4, 3], [4, 6, 6], [7, 20, 9]]     
    $.each(arr1, function(i, item){     
       alert(item[0]);     
    });     
    //其实arr1为一个二维数组，item相当于取每一个一维数组，   
    //item[0]相对于取每一个一维数组里的第一个值   
    //所以上面这个each输出分别为：1   4   7 
    
    js中$.trim()方法：trim函数功能是去掉字符串两端空格符
    
    JavaScript location.search 属性用于设置或取得当前 URL 的查询字串（? 符号及后面的部分）：
    如：
    URL 是：http://www.5idev.com/p-javascript_location.shtml?part=1
    location.search就是?part=1
    
    JavaScript substr(start,length) 方法可在字符串中抽取从 start 下标开始的指定数目的字符。
    如：
    var str="Hello world!"
    document.write(str.substr(3,7))
    输出：lo worl
    
    JavaScript unescape(string) 函数可对通过 escape() 编码的字符串进行解码。
    如：
    var test1="Visit W3School!"
    test1=escape(test1)
    document.write (test1 + "<br />")
    test1=unescape(test1)
    document.write(test1 + "<br />")
    输出：
    Visit%20W3School%21
    Visit W3School!
    
    Python处理HTML转义字符
    如：对于html = '&lt;abc&gt;'
    import HTMLParser
    html_parser = HTMLParser.HTMLParser()
    txt = html_parser.unescape(html)
    #这样就得到了txt = '<abc>'
    反之：
    import cgi
    html = cgi.escape(txt) # 这样又回到了 html = '&lt;abc&gt'
    
---

#### 其他

* [Online JSON Viewer](http://jsonviewer.stack.hu/) 是一个在线解析Json数据的工具，可以把Json格式的数据粘贴进去，查看Json的结构
  
---


> 参考文章：[轻松自动化---selenium-webdriver](http://www.cnblogs.com/fnng/p/3193955.html)、[ChromeDriver executable needs to be available in the path](http://stackoverflow.com/questions/26477603/chromedriver-executable-needs-to-be-available-in-the-path)

---

### 结语：

坚持每天进步一点点...

---