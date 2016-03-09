---
layout: post
title:  "scrapy笔记之Tips"
date:   2016-3-2 18:12:05
categories: scrapy 
excerpt: 
---

* content
{:toc}

### 使用scrapy过程中的点滴

---

#### 利用 -a 向蜘蛛传递参数 
    
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

#### 一次启动多个爬虫的方法

* 官方文档提供的几种在script里面运行spider的方法

        1、Run Scrapy from a script

        import scrapy
        from scrapy.crawler import CrawlerProcess
        class MySpider(scrapy.Spider):
          # Your spider definition
          ...
        process = CrawlerProcess({
          'USER_AGENT': 'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1)'
        })
        process.crawl(MySpider)
        process.start() # the script will block here until the crawling is finished
        
        
        2、Running multiple spiders in the same process

        通过 CrawlerProcess
        import scrapy
        from scrapy.crawler import CrawlerProcess
        class MySpider1(scrapy.Spider):
          # Your first spider definition
          ...
        class MySpider2(scrapy.Spider):
          # Your second spider definition
          ...
        process = CrawlerProcess()
        process.crawl(MySpider1)
        process.crawl(MySpider2)
        process.start() # the script will block here until all crawling jobs are finished
        
        
        通过 CrawlerRunner
        import scrapy
        from twisted.internet import reactor
        from scrapy.crawler import CrawlerRunner
        from scrapy.utils.log import configure_logging
        class MySpider1(scrapy.Spider):
          # Your first spider definition
          ...
        class MySpider2(scrapy.Spider):
          # Your second spider definition
          ...
        configure_logging()
        runner = CrawlerRunner()
        runner.crawl(MySpider1)
        runner.crawl(MySpider2)
        d = runner.join()
        d.addBoth(lambda _: reactor.stop())
        reactor.run() # the script will block here until all crawling jobs are finished
        
        
        通过CrawlerRunner和链接(chaining) deferred来线性运行
        from twisted.internet import reactor, defer
        from scrapy.crawler import CrawlerRunner
        from scrapy.utils.log import configure_logging
        class MySpider1(scrapy.Spider):
          # Your first spider definition
          ...
        class MySpider2(scrapy.Spider):
          # Your second spider definition
          ...
        configure_logging()
        runner = CrawlerRunner()
        @defer.inlineCallbacks
        def crawl():
            yield runner.crawl(MySpider1)
            yield runner.crawl(MySpider2)
            reactor.stop()
        crawl()
        reactor.run() # the script will block here until the last crawl call is finished


* 通过自定义scrapy命令的方式来运行

        1、创建commands目录

        mkdir commands
        注意：commands和spiders目录是同级的
        
        2、在commands下面添加一个文件crawlall.py
        
        from scrapy.commands import ScrapyCommand  
        from scrapy.crawler import CrawlerRunner
        from scrapy.utils.conf import arglist_to_dict
        class Command(ScrapyCommand):
            requires_project = True
            def syntax(self):  
                return '[options]'  
            def short_desc(self):  
                return 'Runs all of the spiders'  
            def add_options(self, parser):
                ScrapyCommand.add_options(self, parser)
                parser.add_option("-a", dest="spargs", action="append", default=[], metavar="NAME=VALUE",
                          help="set spider argument (may be repeated)")
                parser.add_option("-o", "--output", metavar="FILE",
                          help="dump scraped items into FILE (use - for stdout)")
                parser.add_option("-t", "--output-format", metavar="FORMAT",
                          help="format to use for dumping items with -o")
            def process_options(self, args, opts):
                ScrapyCommand.process_options(self, args, opts)
                try:
                  opts.spargs = arglist_to_dict(opts.spargs)
                except ValueError:
                  raise UsageError("Invalid -a value, use -a NAME=VALUE", print_help=False)
            def run(self, args, opts):
                #settings = get_project_settings()

                spider_loader = self.crawler_process.spider_loader
                for spidername in args or spider_loader.list():
                  print "*********cralall spidername************" + spidername
                  self.crawler_process.crawl(spidername, **opts.spargs)
                self.crawler_process.start()
                
        这里主要是用了self.crawler_process.spider_loader.list()方法获取项目下所有的spider，然后利用self.crawler_process.crawl运行spider
        
        3、commands命令下添加__init__.py文件
        
        4、settings.py目录下创建setup.py（ 这一步去掉也没影响，不知道官网帮助文档这么写有什么具体的意义。 ）
        
            from setuptools import setup, find_packages

            setup(name='scrapy-mymodule',
              entry_points={
                'scrapy.commands': [
                  'crawlall=cnblogs.commands:crawlall',
                ],
              },
             )
        这个文件的含义是定义了一个crawlall命令，cnblogs.commands为命令文件目录，crawlall为命令名。     
        
        
        5. 在settings.py中添加配置：
        
            COMMANDS_MODULE = 'cnblogs.commands'
            
        6. 运行命令scrapy crawlall
        
---

> 参考文章：[同时运行多个scrapy爬虫的几种方法](http://www.tuicool.com/articles/UnUBbuJ)
