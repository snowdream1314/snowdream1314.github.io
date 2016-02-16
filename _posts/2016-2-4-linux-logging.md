---
layout: post
title:  "linux 常用命令之远程登录"
date:   2016-2-3 11:12:05
categories: linux
excerpt: 
---

* content
{:toc}

#### cmder + ssh

* [cmder](http://cmder.net/) 是windows下CMD的加强版，集成了很多常用的linux命令，像ls、cat、grep等,甚至是git...

* [ssh](http://baike.baidu.com/link?url=ta0aJGDGl6Z5D7u0I5BynxWEddD_Bc-rltimCQfJrdzyFtuYPV_Z0oBqII-WoaEhGgsPTBfxZmiQ3Q-hgwQ3MIIvGJOwaT4909Ps0LqyptK) 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。

* 远程登录linux服务器：
        
        登录：
        在cmder下输入：ssh 服务器名@IP,如：
        ssh root@127.0.0.1
        然后根据提示输入登录密码即可
        
        文件传输：
        从服务器下载文件到本机中：
        scp abc@IP(服务器)：/home/abc/xxxx(文件)  /xxxx(本机目录)
        从本机上传文件到服务器：
        scp /xxxx(本机目录)  abc@IP(服务器)：/home/abc/xxxx(文件) 
        
---

#### 查看日志

* linux 日志文件夹的默认存储路径是：/var/log

* 日志是一种文本文件，因此可以使用查看文本文件的命令来查看日志。

        cat /var/log/messages 查询日志的全部内容
        head -5 /var/log/messages 查询日志的前5行
        tail -5 /var/log/messages 查询日志的最新5行
        sed -n '5,10p' /var/log/messages 查询日志的5到10行

---

#### 切换用户

* Linux系统中用户切换的命令为su，语法为：

        su [-fmp] [-c command] [-s shell] [--help] [--version] [-] [USER [ARG]]
        参数说明

        -f ， –fast：不必读启动文件（如 csh.cshrc 等），仅用于csh或tcsh两种Shell。

        -l ， –login：加了这个参数之后，就好像是重新登陆一样，大部分环境变量(例如HOME、SHELL和USER等)都是以该使用者(USER)为主，并

        且工作目录也会改变。如果没有指定USER，缺省情况是root。

        -m， -p ，–preserve-environment：执行su时不改变环境变数。

        -c command：变更账号为USER的使用者，并执行指令（command）后再变回原来使用者。

        –help 显示说明文件
        –version 显示版本资讯

        USER：欲变更的使用者账号，
        ARG: 传入新的Shell参数。
        
* su [user]切换到其他用户，但是不切换环境变量，su - [user]则是完整的切换到新的用户环境。明显的一点改变是：su - [user]会切换到目标用户的根目录

---

> 参考文章：