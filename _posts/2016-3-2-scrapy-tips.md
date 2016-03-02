---
layout: post
title:  "scrapy笔记之Tips"
date:   2016-3-2 18:12:05
categories: scrapy 
excerpt: 
---

* content
{:toc}

#### 使用scrapy过程中的点滴

* 利用 -a 向蜘蛛传递参数 
    
        scrapy crawl XXX -a update = True
        
        #方法1
        class youku_video_spider(Spider):
    
            name = "youku"
            
            def __init__(self, update=False):
                self.allowed_domains = "baidu.com"
                self.start_urls = ["http://www.baidu.com/"]
                self.update = update
            
            def parse(self, response):
                print "parse"
                
                self.parseEpisodes(self.update)
                
        #方法2，新建一个spider类：
        class YJKSpider(Member):
            crawl_category=False
            
            def __init__(self, **kwargs):
                
                reload(sys)
                sys.setdefaultencoding('utf-8')
                super(YJKSpider, self).__init__(**kwargs)
                
                if kwargs.has_key("crawl_category"):
                    self.crawl_category = True
                    
        class youku_video_spider(YJKSpider)：
                
---

> 参考文章：
