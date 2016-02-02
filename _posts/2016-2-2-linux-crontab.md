---
layout: post
title:  "linux 常用命令之定时运行"
date:   2016-2-2 17:12:05
categories: linux
excerpt: 
---

* content
{:toc}

#### crontab命令

* crontab命令可以让用户定义一些需要定期执行的命令或脚本

* crontab命令格式：

        crontab [-u user] file
        crontab [-u user] [ -e | -l | -r ]
        
        如：
        crontab -u root filename  #表示设定用户root的crontab服务，命令的内容存放在文件filename中
        crontab -u root -l/e/r  #表示显示/编辑/删除root用户crontab文件的内容，如上面的filename文件的内容。

* 用户所建立的crontab文件中，每一行都代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，格式如下：

        minute   hour   day   month   week   command
        其中：
        minute： 表示分钟，可以是从0到59之间的任何整数。
        hour：表示小时，可以是从0到23之间的任何整数。
        day：表示日期，可以是从1到31之间的任何整数。
        month：表示月份，可以是从1到12之间的任何整数。
        week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
        command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件。
    
* 此外，上述字段中还可以使用特殊字符：

        在以上各个字段中，还可以使用以下特殊字符：
        星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
        逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
        中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
        正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。
        
* 实例：

        * * * * * command           #每1分钟执行一次command
        3,15 * * * * command        #每小时的第3和第15分钟执行
        3,15 8-11 * * * command     #每天在上午8点到11点的第3和第15分钟执行
        3,15 8-11 */2 * * command   #每隔2天在上午8点到11点的第3和第15分钟执行
        3,15 8-11 * * 1 command     #每个星期一的上午8点到11点的第3和第15分钟执行
        45 4 1,10,22 * * /etc/init.d/smb restart    #每月1、10、22日的4 : 45重启smb 
        01 * * * * root run-parts /etc/cron.hourly  #每小时执行/etc/cron.hourly目录内的脚本,去掉run-parts这个参数的话，后面就可以写要运行的某个脚本名
        
---

> 参考文章：[每天一个linux命令（50）：crontab命令](http://www.cnblogs.com/peida/archive/2013/01/08/2850483.html)