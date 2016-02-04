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
        在cmder下输入：
        ssh 服务器名@IP
        然后根据提示输入登录密码即可
        
        文件传输：
        从服务器下载文件到本机中：
        scp abc@IP(服务器)：/home/abc/xxxx(文件)  /xxxx(本机目录)
        从本机上传文件到服务器：
        scp /xxxx(本机目录)  abc@IP(服务器)：/home/abc/xxxx(文件) 
        
---

> 参考文章：