---
layout: post
title:  "MySQL"
date:   2015-11-18 16:08:05
categories: MySQL 
excerpt: 
---

* content
{:toc}

### MySQL的相关操作（基于python-mysql）

---

#### 连接到mysql（和python-mysql无关）
        
* 安装好Mysql后。把安装目录下的Mysql.exe的文件路径加入系统环境变量中，启动终端后即可执行mysql命令

* 链接数据库的命令格式为：mysql -u 用户名 -p 密码 ；第一次运行mysqld -u root 即以root用户登录，root用户是默认的起始用户，可以用来链接数据库服务器。连接以后就可以执行sql语句生成用户密码等，下次就可以用新的用户名和密码登录。

* 注意用户名前可以有空格也可以没有空格，但是密码前必须没有空格，否则让你重新输入密码。

        启动关闭服务：
        net start/stop mysql
        
        登录：
        mysql -hlocalhost -uroot -p        其中-h表示服务器名，localhost表示本地；-u为数据库用户名，root是mysql默认用户名；-p为密码，如果设置了密码，可直接在-p后链接输入，如：-p123456，用户没有设置密码，显示Enter password时，直接回车即可。
        
        注意：如果mysql没有安装在C盘下，则需要先使用DOS命令进入mysql的安装目录下的bin目录中，才能执行上述命令

        显示系统信息：
        show variables like 'version'；#一条命令以分号结束！！！
        show variables like '%auto_incre%'; #显示字段自增的设置
        输出：
        Variable_name            | Value |
        +--------------------------+-------+
        | auto_increment_increment | 1     |
        | auto_increment_offset    | 1
        
-----

#### 增删改查数据（基于python-mysql）
        
       

* 插入数据：

        db.insert("cv_video_episodes", ve_video_id=video_id, ve_video_name=video_name, ve_episode_id=episode_id)        
        

* 查看
     
        查询值为空的记录,要用 ：字段 is null
        SELECT * FROM `yjk_eb_deal` WHERE d_ds_id=6 AND d_deal_url is null;
        
        查询最新的一条记录：
        SELECT * FROM 表名 ORDER BY id DESC LIMIT 0,1 ;

* 删除
        
       
        
 * 更新，也即修改数据       
        
       db.execute("update cv_video_detail_msg_copy set cv_played_count=%s, cv_video_rate=%s, cv_comment_num=%s, cv_support_num=%s, \
                        cv_update_schedule=%s, cv_update_status=%s", played_count, video_rating, comment_num, support_num, update_schedule, update_status)
        
-----

#### 其他

* 动态增加字段

        格式：alter table 表名 add 字段名 字段类型和长度等设置

        try:
            for i in range(1):
                sql = "alter table db_test add key%s text" %i
                curs.execute(sql)
        except Exception,e:
            print e
        
* 设置字段自增

        只能主键设置自增！
        基本命令格式如下:
        ALTER TABLE 表名 MODIFY 列名 column_definition
        
        其中column_definition字段需要包括以下内容:
        1、该列的数据类型及AUTO_INCREMENT字段；
        2、如该列必须为NOT NULL及PRIMARY KEY（UNIQUE）属性。如果不是，需添加相应定义。
        
        例：
        ALTER TABLE `cv_video_detail_msg_copy` MODIFY cv_id INT auto_increment;
        
        #无符号整型
        ALTER TABLE `cv_video_detail_msg_copy` MODIFY cv_id INT UNSIGNED auto_increment; 
        
        注:只有int类型且为primary key 才可以使用auto_increment.
        
        #删除表中的内容，让表字段重新从1开始自增
        truncate 表名
        
        给主键添加自增属性
        alter table 表名 MODIFY 键名 INT UNSIGNED AUTO_INCREMENT;
        
* 设置字段自增的2个系统变量：auto_increment_offset和auto_increment_increment

        auto_increment_increment  控制列中的值的增量值，也就是步长。取值范围是1 .. 65535.
        auto_increment_offset  确定AUTO_INCREMENT列值的起点，也就是初始值。取值范围是1 .. 65535.
        
        设置方法为:
        SET @@auto_increment_offset=1;
        SET @@auto_increment_increment=1;

* 加索引： 

        alter table 表名 add index 索引名 (字段名1[，字段名2 …]);
        例：
        alter table employee add index emp_name (name);
   
* 删除索引：

        alter table 表名 drop index 索引名;
        例：
        alter table employee drop index emp_name;
        
---

#### 其他常用的命令

* 退出MYSQL命令： exit （回车）

* 修改密码:直接在终端中，不需要登录mysql

        格式：mysqladmin -u用户名 -p旧密码 password 新密码
        
        例：给root加个密码。因为开始时root没有密码，所以-p旧密码一项就可以省略了。
        mysqladmin -u root -password 密码

* 增加新用户：

        grant select on 数据库.* to 用户名@登录主机 identified by “密码”        
        例：增加一个用户test1密码为abc，让他可以在任何主机上登录，并对所有数据库有查询、插入、修改、删除的权限。首先用root用户连入MYSQL，然后键入以下命令：
        grant select,insert,update,delete on *.* to [email=test1@”%]test1@”%[/email]” Identified by “abc”;
        注意：增加的用户是十分危险的，你想如某个人知道test1的密码，那么他就可以在internet上的任何一台电脑上登录你的mysql数据库并对你的数据可以为所欲为了。
        例：增加一个用户test2密码为abc,让他只可以在localhost上登录，并可以对数据库mydb进行查询、插入、修改、删除的操作（localhost指本地主机，即MYSQL数据库所在的那台主机），这样用户即使用知道test2的密码，他也无法从internet上直接访问数据库，只能通过MYSQL主机上的web页来访问了。
        
        grant select,insert,update,delete on mydb.* to [email=test2@localhost]test2@localhost[/email] identified by “abc”;
         
        如果你不想test2有密码，可以再打一个命令将密码消掉。
        grant select,insert,update,delete on mydb.* to [email=test2@localhost]test2@localhost[/email] identified by “”;
        
* 创建数据库：创建数据库之前要先连接Mysql服务器

        命令：create database <数据库名>
        
        例：创建数据库并分配用户
        CREATE DATABASE 数据库名;
        GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON 数据库名.* TO 数据库名@localhost IDENTIFIED BY '密码';
        SET PASSWORD FOR '数据库名'@'localhost' = OLD_PASSWORD('密码');
        依次执行3个命令完成数据库创建。

* 通过运行sql文件创建数据库

        方法一 使用cmd命令执行(windows下，unix或linux在的其控制台下)
        【Mysql的bin目录】\mysql –u用户名 –p密码 –D数据库<【sql脚本文件路径全名】，示例：
        D:\mysql\bin\mysql –uroot –p123456 -Dtest<d:\test\ss.sql
        注意：
        A、如果在sql脚本文件中使用了use 数据库，则-D数据库选项可以忽略
        B、如果【Mysql的bin目录】中包含空格，则需要使用“”包含，如：“C:\Program Files\mysql\bin\mysql” –u用户名 –p密码 –D数据库<【sql脚本文件路径全名】

        方法二 进入mysql的控制台后，使用source命令执行
        Mysql>source 【sql脚本文件的路径全名】 或 Mysql>\. 【sql脚本文件的路径全名】，示例：
        source d:\test\ss.sql 或者 \. d:\test\ss.sql

---

#### Mysql中插入emoji表情

* 插入Emoji表情，保存到数据库时报错：

        SQLException: Incorrect string value: '\xF0\x9F\x98\x84' for column 'review' at row 1
        原因：
        UTF-8编码有可能是两个、三个、四个字节。Emoji表情是4个字节，而Mysql的utf8编码最多3个字节，所以数据插不进去。
        
        解决办法：相应的数据库栏位字符集改为：utf8mb4即可
        
---


> 参考文章：[Mysql命令大全](http://www.cnblogs.com/zhangzhu/archive/2013/07/04/3172486.html)

---

### 结语：

坚持每天进步一点点...

---