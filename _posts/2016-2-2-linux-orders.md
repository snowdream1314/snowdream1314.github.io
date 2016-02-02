---
layout: post
title:  "linux 常用命令之后台运行"
date:   2016-2-2 10:55:05
categories: linux
excerpt: 
---

* content
{:toc}


#### CTRL-z 和fg

* CTRL-z 将当前进程挂起到后台暂停运行，执行一些别的操作，然后再用 fg 来将挂起的进程重新放回前台(也可用
bg 来将挂起的进程放在后台)继续运行

---

#### nohup 和 &

* nohup 的用途就是让提交的命令忽略 hangup 信号,这样可以让程序在后台运行，即使登出终端或者关机，程序还是会继续运行。在要处理的命令前加上 nohup 即可，标准输出和标准错误缺省会被重定向到 nohup.out 文件中。一般我们可在结尾加上"&"来将命令同时放入后台运行，也可用">filename 2>&1"来更改缺省的重定向文件名。

        例如：
        nohup python query_deals_origin_url.py &
        #会返回一个进程号，如：15300
        返回：nohup: ignoring input and appending output to `nohup.out'说明执行成功
        
        ps -ef 列出所有进程,ps -ef |grep 可以查看具体的进程,如:
        ps -ef | grep 15300 或者 ps -ef | grep query_deals_origin_url
        返回：
        root    15300  8303  9 10:36 pts/2    00:02:58 python query_deals_origin_url.py
        root    15305 15300  0 10:36 pts/2    00:00:00 [Xvfb] <defunct>
        root    17853  8303  0 11:07 pts/2    00:00:00 grep --color=auto 15300

#### setsid

* setsid也能达到和nohup一样的使我们的进程避免中途被中断。与nohup的忽略 HUP 信号不同的是，setsid是使我们的进程不属于接受 HUP 信号的终端的子进程，类似于为我们的程序开了另一个独立的父进程，从而使我们的程序不受当前的终端HUP信号的影响。
       
        例如：在爬虫目录下执行:
        
        setsid scrapy crawl gzharticle
        会返回一个进程号并开始运行爬虫gzharticle,查询进程信息:
        ps -ef | grep gzharticle

#### disown 

* disown 用于操作放入后台的作业忽略HUP信号.当已经提交命令,想加 nohup 或者 setsid 已经为时已晚时，可以用考虑disown.
    
        格式为：
        disown -h jobspec 来使某个作业(作业号为jobspec)忽略HUP信号。
        disown -ah  来使所有的作业都忽略HUP信号。
        disown -rh 来使正在运行的作业忽略HUP信号。
        
        获取作业号命令为：jobs
        
* 获取作业号特殊情况：没有把当前命令作为作业来运行,要获取作业号可以先CTRL-z 的用途就是将当前进程挂起（Suspend），然后我们就可以用jobs命令来查询它的作业号，再用bg jobspec来将它放入后台并继续运行。需要注意的是，如果挂起会影响当前进程的运行结果，请慎用此方法。

* 使用过 disown 之后，会将把目标作业从作业列表中移除，我们将不能再使用jobs来查看它，但是依然能够用ps -ef查找到它。

#### screen 

* 当有大量的命令需要在稳定的后台里运行时，可以用screen命令.简单的说，screen 提供了 ANSI/VT100 的终端模拟器，使它能够在一个真实终端下运行多个全屏的伪终端.此时 bash 是 screen 的子进程，而 screen 是 init（PID为1）的子进程。那么当 ssh 断开连接时，HUP 信号自然不会影响到 screen 下面的子进程了。  

        screen -dmS session name来建立一个处于断开模式下的会话（并指定其会话名）。
        screen -list 来列出所有会话。
        screen -r session name来重新连接指定会话。
        
* 在使用screen 创建的会话下，不用给每个命令前都加上“nohup”或者“setsid”,即使图吃当前终端命令也会继续执行.       
        
---

> 参考文章：[linux命令里面 nohup命令和 &](http://zhidao.baidu.com/link?url=dBRZCxAgtUFx0QWuzu_3Q_Kdvfl6ajUUD3_QWMoAnY4if3Cb3UnpUYDPnZS-255BpZxWrt9mJW4S6S1DOGpOiK)