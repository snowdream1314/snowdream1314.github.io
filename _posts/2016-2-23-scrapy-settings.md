---
layout: post
title:  "scrapy笔记之探索settings"
date:   2016-2-23 13:12:05
categories: scrapy 
excerpt: 
---

* content
{:toc}

#### settings.py常用设置解析

* 如何访问设定(How to access settings)：

        设定可以通过Crawler的 scrapy.crawler.Crawler.settings 属性进行访问。其由插件及中间件的 from_crawler 方法所传入:

        class MyExtension(object):

            @classmethod
            def from_crawler(cls, crawler):
                settings = crawler.settings
                if settings['LOG_ENABLED']:
                    print "log is enabled!"

---
                    
#### 常用设置选项解析：

* 项目名称：BOT_NAME

        默认: 'scrapybot'
        Scrapy项目实现的bot的名字(项目名称)。 这将用来构造默认 User-Agent，同时也用来log。
        当您使用 startproject 命令创建项目时其也被自动赋值。

* 并发items: CONCURRENT_ITEMS

        默认: 100
        Item Processor(即 Item Pipeline) 同时处理(每个response的)item的最大值。

* 并发请求: CONCURRENT_REQUESTS

        默认: 16
        Scrapy downloader 并发请求(concurrent requests)的最大值。
        
* 单个网站并发请求限制: CONCURRENT_REQUESTS_PER_DOMAIN

        默认: 8
        对单个网站进行并发请求的最大值。
        
* 单个IP并发请求限制: CONCURRENT_REQUESTS_PER_IP

        默认: 0

        对单个IP进行并发请求的最大值。如果非0，则忽略 CONCURRENT_REQUESTS_PER_DOMAIN 设定， 使用该设定。 也就是说，并发限制将针对IP，而不是网站。

        该设定也影响 DOWNLOAD_DELAY: 如果 CONCURRENT_REQUESTS_PER_IP 非0，下载延迟应用在IP而不是网站上。
        
* item默认类：DEFAULT_ITEM_CLASS

        默认: 'scrapy.item.Item'
        the Scrapy shell 中实例化item使用的默认类。
        
* request默认请求头: DEFAULT_REQUEST_HEADERS

        默认:
        {
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
            'Accept-Language': 'en',
        }
        Scrapy HTTP Request使用的默认header。由 DefaultHeadersMiddleware 产生。
        
* 爬取网站最大允许深度： DEPTH_LIMIT

        默认: 0
        爬取网站最大允许的深度(depth)值。如果为0，则没有限制。
        
* 根据深度调整request优先级： DEPTH_PRIORITY

        默认: 0
        整数值。用于根据深度调整request优先级。
        如果为0，则不根据深度进行优先级调整。
        
* 最大深度数据：DEPTH_STATS

        默认: True
        是否收集最大深度数据。

* 详细深度数据: DEPTH_STATS_VERBOSE

        默认: False
        是否收集详细的深度数据。如果启用，每个深度的请求数将会被收集在数据中。
        
* DNS内存缓存

        默认: True
        是否启用DNS内存缓存(DNS in-memory cache)。
        
* 下载中间件及其顺序： DOWNLOADER_MIDDLEWARES

        默认: {}
        保存项目中启用的下载中间件及其顺序的字典。按数字从小到大执行。
        
* 下载器数据：DOWNLOADER_STATS

        默认: True
        是否收集下载器数据。
        
* 下载延迟：DOWNLOAD_DELAY

        默认: 0

        下载器在下载同一个网站下一个页面前需要等待的时间。该选项可以用来限制爬取速度， 减轻服务器压力。同时也支持小数:

        DOWNLOAD_DELAY = 0.25    # 250 ms of delay
        该设定影响(默认启用的) RANDOMIZE_DOWNLOAD_DELAY 设定。 默认情况下，Scrapy在两个请求间不等待一个固定的值， 而是使用0.5到1.5之间的一个随机值 * DOWNLOAD_DELAY 的结果作为等待间隔。

        当 CONCURRENT_REQUESTS_PER_IP 非0时，延迟针对的是每个ip而不是网站。

        另外您可以通过spider的 download_delay 属性为每个spider设置该设定。

* 下载处理器字典： DOWNLOAD_HANDLERS

        默认: {}
        保存项目中启用的下载处理器(request downloader handler)的字典。 
        
* 下载器超时时间：DOWNLOAD_TIMEOUT

        默认: 180
        下载器超时时间(单位: 秒)。
        
* 用于检测过滤重复请求的类: DUPEFILTER_CLASS

        默认: 'scrapy.dupefilter.RFPDupeFilter'
        用于检测过滤重复请求的类。
        默认的 (RFPDupeFilter) 过滤器基于 scrapy.utils.request.request_fingerprint 函数生成的请求指纹。
        
* RFPDupeFilter记录重复请求： DUPEFILTER_DEBUG

        默认: False
        默认情况下， RFPDupeFilter 只记录第一次重复的请求。 设置 DUPEFILTER_DEBUG 为 True 将会使其记录所有重复的requests。
        
* pipeline字典： ITEM_PIPELINES

        默认: {}
        保存项目中启用的pipeline及其顺序的字典。该字典默认为空，值(value)任意。 不过值(value)习惯设定在0-1000范围内。spider返回的items按照value值从小到大经过pipeline
  
* 是否启用日志： LOG_ENABLED

        默认: True
        是否启用logging。

* 日志默认编码：LOG_ENCODING

        默认: 'utf-8'
        logging使用的编码。
        
* 日志文件名： LOG_FILE

        默认: None
        logging输出的文件名。如果为None，则使用标准错误输出(standard error)。

* log最低级别：LOG_LEVEL

        默认: 'DEBUG'
        log的最低级别。可选的级别有: CRITICAL、 ERROR、WARNING、INFO、DEBUG。
        
* 随机下载延迟：RANDOMIZE_DOWNLOAD_DELAY

        默认: True

        如果启用，当从相同的网站获取数据时，Scrapy将会等待一个随机的值 (0.5到1.5之间的一个随机值 * DOWNLOAD_DELAY)。

        该随机值降低了crawler被检测到(接着被block)的机会。某些网站会分析请求， 查找请求之间时间的相似性。

        随机的策略与 wget --random-wait 选项的策略相同。

        若 DOWNLOAD_DELAY 为0(默认值)，该选项将不起作用。

* request允许重定向的最大次数：REDIRECT_MAX_TIMES

        默认: 20
        定义request允许重定向的最大次数。超过该限制后该request直接返回获取到的结果。 对某些任务我们使用Firefox默认值。
        
*  robots.txt策略： ROBOTSTXT_OBEY

        默认: False
        Scope: scrapy.contrib.downloadermiddleware.robotstxt
        如果启用，Scrapy将会尊重 robots.txt策略。更多内容请查看 RobotsTxtMiddleware 。
        
* 爬取的调度器：SCHEDULER

        默认: 'scrapy.core.scheduler.Scheduler'
        用于爬取的调度器。
        自定义：
        SCHEDULER = "smzdm_crawler.scrapy_redis.scheduler.Scheduler"
        
* 默认的USER_AGENT：USER_AGENT

        默认: "Scrapy/VERSION (+http://scrapy.org)"
        爬取的默认User-Agent，除非被覆盖。
---

#### 其他设置

        MEMDEBUG_ENABLED
        默认: False
        是否启用内存调试(memory debugging)。

        MEMDEBUG_NOTIFY
        默认: []
        如果该设置不为空，当启用内存调试时将会发送一份内存报告到指定的地址；否则该报告将写到log中。
        样例:
        MEMDEBUG_NOTIFY = ['user@example.com']
        
        MEMUSAGE_ENABLED
        默认: False
        是否启用内存使用插件。当Scrapy进程占用的内存超出限制时，该插件将会关闭Scrapy进程， 同时发送email进行通知。

        MEMUSAGE_LIMIT_MB
        默认: 0
        Scope: scrapy.contrib.memusage
        在关闭Scrapy之前所允许的最大内存数(单位: MB)(如果 MEMUSAGE_ENABLED为True)。 如果为0，将不做限制。

        MEMUSAGE_NOTIFY_MAIL
        默认: False
        达到内存限制时通知的email列表。
        Example:
        MEMUSAGE_NOTIFY_MAIL = ['user@example.com']

        MEMUSAGE_REPORT
        默认: False
        每个spider被关闭时是否发送内存使用报告。

        MEMUSAGE_WARNING_MB
        默认: 0
        在发送警告email前所允许的最大内存数(单位: MB)(如果 MEMUSAGE_ENABLED为True)。 如果为0，将不发送警告。

        NEWSPIDER_MODULE
        默认: ''
        使用 genspider 命令创建新spider的模块。
        样例:
        NEWSPIDER_MODULE = 'mybot.spiders_dev'

---

#### 判断设置是否已经启用

* 更改默认设置以后，执行scrapy时，控制台会打印log信息：Overridden settings，即表示修改成功

        2016-03-11 15:12:30 [scrapy] INFO: Overridden settings: {'NEWSPIDER_MODULE': 'smzdm_crawler.spiders', 'CONCURRENT_REQUESTS_PER_DOMAIN': 16, 'SPIDER_MODULES': ['smzdm_crawler.spiders'], 'BOT_NAME': 'smzdm_crawler', 'SCHEDULER': 'smzdm_crawler.scrapy_redis.redis_scheduler.Scheduler', 'DOWNLOAD_DELAY': 0.25}

---

> 参考文章：[scrapy 深度 setting设立](http://www.myexception.cn/web/1646523.html)