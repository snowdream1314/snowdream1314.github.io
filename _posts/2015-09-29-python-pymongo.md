---
layout: post
title:  "PyMongo"
date:   2015-09-29 16:08:05
categories: PyMongo 
excerpt: 
---

* content
{:toc}

### 今天用到了新的数据库PyMongo

---

#### 连接到PyMongo

        import pymongo
        
        # 创建连接
        client = pymongo.MongoClient(local_mongodb_domain, local_mongodb_port)  #参数分别是数据库的IP地址和连接端口，比如（'127.0.0.1',27017）
        
        # 连接数据库
        db = client.xxqmon  or db = db.client['xxqmon']  # xxqmon 为数据库名
        
        # 连接到聚集（聚集就类似于Mysql中的table)
        tab = db.collectionname  or tab = db['collectionname']  #collectionname为对应数据库(即xxqmon)的某个表
        
        # 以上2句也可以合并成一句
        tab = client.xxqmon.collectionname  or  tab = client['xxqmon']['collectionname']
        
        -----
        
        #得到tab以后，就可以进行相关的一些数据库操作
        dic = {"name":"jim","score":"90","location":"china"}
        
        #插入数据
        tab.insert(dic)  #直接就已经插入数据库了，没有类似Mysql的commit语句
        tab.insert([dic1,dic2...])  #批量插入多条数据
        
        # 查找数据
        tab.find({"name":"jim"})  #查询所有符合记录
        tab.find({"name":"jim"}).count()  #统计符合条件的数据
        tab.find_one({"name":"jim"})  #查询符合的单条记录
        

---


> 参考文章：[PyMongo官方文档](http://api.mongodb.org/python/current/api/pymongo/collection.html)

---

### 结语：

坚持每天进步一点点...

---