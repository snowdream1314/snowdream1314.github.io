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

---

#### 其他

* [Online JSON Viewer](http://jsonviewer.stack.hu/) 是一个在线解析Json数据的工具，可以把Json格式的数据粘贴进去，查看Json的结构
  
---


> 参考文章：[轻松自动化---selenium-webdriver](http://www.cnblogs.com/fnng/p/3193955.html)

---

### 结语：

坚持每天进步一点点...

---