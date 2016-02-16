---
layout: post
title:  "linux 之vim编辑文件常用命令"
date:   2016-2-16 11:12:05
categories: linux
excerpt: 
---

* content
{:toc}

#### vi的基本概念

* 基本上vi可分为三种操作状态，分别是命令模式(Command mode)、插入模式(Insert mode)和底线命令模式(Last line mode)

        1. Comand mode：控制屏幕光标的移动，字符或光标的删除，移动复制某区段及进入Insert mode下，或者到Last line mode。
　　    2. Insert mode：唯有在Insert mode下，才可做文字数据输入，按Esc等可回到Comand mode。
　　    3. Last line mode：将储存文件或离开编辑器，也可设置编辑环境，如寻找字符串、列出行号等。

* 基本流程命令：

        #打开文件：
        vi 文件名；如 vi /etc/text
        
        #找到对应要编辑的内容
        Ctrl+B：屏幕往后移动一页。[常用]
　　    Ctrl+F：屏幕往前移动一页。[常用]
        0 (数字零)：移动文章的开头。[常用]
　　    G：移动到文章的最后。[常用]
        w：光标跳到下个word的开头。[常用]
　　    $：移到光标所在行的行尾。[常用]
        0：移到该行的开头位置。[常用]
        #：移到该行的第#个位置，例：51、121。[常用]
        
        #编辑文件，输入
        i: 进入插入模式，可以编辑文件，删除或是增加
        ESC: 退回命令模式，可以执行查找命令，如Ctrl+B；Ctrl+F
        
        #退出,输入':'加以下命令
        q：按q就离开，有时如果无法离开vi，可搭配“!：强置离开vi，如“q!”
　　    qw：一般建议离开时，搭配w一起使用，如此离开时还可存文件
    　　如：
        :q!    强制退出，不保存
        :wq!   强制退出并保存
        
---

> 参考文章：[linux命令vi文本编辑器的使用方法](http://zhidao.baidu.com/link?url=MWfTrUDNOWRbcCXX4hY_1w5Rv8g-x4fAkzobFfwQFxcxp6b4xJWTdqs6bpKX9w8cWASMEEu7HqfD9hRCb-2beK) [Linux vi/vim菜鸟教程](http://www.runoob.com/linux/linux-vim.html)