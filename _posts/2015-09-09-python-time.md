---
layout: post
title:  "Python之time"
date:   2015-09-09 13:30:05
categories: python
excerpt: 
---

* content
{:toc}


### 碰到 *time* 模块，记录下用过的方法

---

#### Python time strptime()方法

* Python time strptime() 函数根据指定的格式把一个时间字符串解析为时间元组。

* strptime()方法语法：time.strptime(string[, format])

        #!/usr/bin/python
        import time

        struct_time = time.strptime("30 Nov 00", "%d %b %y")
        print "returned tuple: %s " % struct_time
        
        输出：
        returned tuple: (2000, 11, 30, 0, 0, 0, 3, 335, -1)

---

#### Python中时间日期格式化符号：

* %y 两位数的年份表示（00-99）
* %Y 四位数的年份表示（000-9999）
* %m 月份（01-12）
* %d 月内中的一天（0-31）
* %H 24小时制小时数（0-23）
* %I 12小时制小时数（01-12）
* %M 分钟数（00=59）
* %S 秒（00-59）
* %a 本地简化星期名称
* %A 本地完整星期名称
* %b 本地简化的月份名称
* %B 本地完整的月份名称
* %c 本地相应的日期表示和时间表示
* %j 年内的一天（001-366）
* %p 本地A.M.或P.M.的等价符
* %U 一年中的星期数（00-53）星期天为星期的开始
* %w 星期（0-6），星期天为星期的开始
* %W 一年中的星期数（00-53）星期一为星期的开始
* %x 本地相应的日期表示
* %X 本地相应的时间表示
* %Z 当前时区的名称
* %% %号本身


---

#### Python time mktime()方法

* Python time mktime() 函数执行与gmtime(), localtime()相反的操作，它接收struct_time对象作为参数，返回用秒数来表示时间的浮点数。

* mktime()方法语法：time.mktime(t)

        #!/usr/bin/python
        import time

        t = (2009, 2, 17, 17, 3, 38, 1, 48, 0)
        secs = time.mktime( t )
        print "time.mktime(t) : %f" %  secs
        print "asctime(localtime(secs)): %s" % time.asctime(time.localtime(secs))
        
        输出：
        time.mktime(t) : 1234915418.000000
        asctime(localtime(secs)): Tue Feb 17 17:03:38 2009

---

#### Python datetime 模块
        
        import datetime
        
        date1 = '09/17/2015'
        date2 = datetime.datetime.strptime(date1,"%m/%d/%Y")
        date3 = date2.strftime("%Y-%m-%d")
        print date2
        print date3
        
        输出：
        # 2015-09-17 00:00:00
        # 2015-09-17

* datetime.datetime.now()获取系统当前时间
    
        import datetime
        #获得当前时间
        now = datetime.datetime.now()  ->这是时间数组格式   #time.time()返回的是浮点数形式
        #获取日期：
        date = now.date()
        #转换为指定的格式:
        otherStyleTime = now.strftime("%Y-%m-%d %H:%M:%S")
       
* 计算时间差 

        threeDayAgo = (datetime.datetime.now() - datetime.timedelta(days = 3))      #三天前时间
        threeHoursAgo = (datetime.datetime.now() - datetime.timedelta(hours = 3))     #三小时前时间
        threeMinutesAgo = (datetime.datetime.now() - datetime.timedelta(minutes = 3))   #三分钟前时间
        
        #注意 days/hours/minutes都必须是整数。不能是字符类型
        
        #上面返回的都是datetieme对象 例如 datetime.datetime(2015,10,12,12,38,11,57000)
        
        #获取日期：
        date = threeDayAgo.date() #datetime.date(2015, 10, 9)
        
        #转换为其他格式时间
        datetime.datetime.strftime(date,"%Y-%m-%d") #转换成了字符串 '2015-10-09'
        time.mktime(threeDayAgo.timetuple()) #转换成了浮点数形式

 * 修改获取的时间

        a = '201510161100'
        b = datetime.datetime.strptime(a,"%Y%m%d%H%M")
        b
        #输出：datetime.datetime（2015,10,16,11,0）
        
        b.replace(hour=23)
        b 
        #输出：datetime.datetime（2015,10,16,23,0）
        #还可以改year,day 
        
        #此外：
        c = datetime.datetime.today().date()
        c 
        #输出：datetime.date(2015, 10, 16)
        c.replace(day=11)
        #输出：datetime.date(2015, 10, 11)
---

#### 待解决的问题

        updatetimeitem = list.find("dl", {"class":"list_con clearfix"}).find("span", {"class":"dp_time"}).get_text().strip()
        if "今天".decode('utf-8') in updatetimeitem :     #时间格式为“今天10:24”
            date =  datetime.datetime.strftime (datetime.datetime.today().date(),"%Y%m%d") #日期
            
            # time = filter(lambda x:x.isdigit(), updatetimeitem) #时间
            # updatetime = date + time    这样写下面mktime会报错，'unicode ...没有mktime属性之类的'
            
            updatetime = datetime.datetime.strftime (datetime.datetime.today().date(),"%Y%m%d") + filter(lambda x:x.isdigit(), updatetimeitem)  #这样写通过
            
            item.updatetime = time.mktime(time.strptime(updatetime, "%Y%m%d%H%M")) 
            
---

> 参考文章：[RUNOOB.COM](http://www.runoob.com/python/att-time-mktime.html)

---

### 结语：

坚持每天进步一点点...

---