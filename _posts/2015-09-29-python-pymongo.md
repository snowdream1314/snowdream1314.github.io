---
layout: post
title:  "PyMongo"
date:   2015-09-29 16:08:05
categories: MongoDB
excerpt: 
---

* content
{:toc}

### 今天接触到了新的数据库Mongodb,用到的python模块为PyMongo

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

#### 增删改查数据
        
        # 得到tab以后，就可以进行相关的一些数据库操作
        dic = {"name":"jim","score":"90","location":"china"}

* 插入数据：
        
        #插入数据
        tab.insert(dic)  #直接就已经插入数据库了，没有类似Mysql的commit语句
        tab.insert([dic1,dic2...])  #批量插入多条数据

* 查看
        
        #查看所有数据库
         show dbs

        #查看所有的collection
        show collections
        
        # 查找数据
        tab.find({"name":"jim"})  #查询所有符合记录
        tab.find({"name":"jim"}).count()  #统计符合条件的数据
        tab.find_one({"name":"jim"})  #查询符合的单条记录
        tab.find({"age":20}).limit(10)  #根据条件检索10条记录,限制返回记录数量limit()
        
        查找数据时可以再后面加上 pretty()以易读的方式来读取数据
        > db.user.find().pretty()
        {
                "_id" : ObjectId("5627021a632a927246ecd9c7"),
                "name" : "jack",
                "age" : 23
        }
        
        # 查找的数据排序
        tab.find().sort('name')  #查询结果按name排序，默认为升序        
        db.users.find().sort({age: 1});  # 以年龄升序asc       
        db.users.find().sort({age: -1})  # 以年龄降序desc 
        tab.find().sort('name'，pymongo.ASCENDING)  #查询结果按name排序，ASCENDING为升序，DESCENDING为降序
        tab.find().sort([('name',pymongo.ASCENDING),('score',pymongo.DESCENDING)])  #查询结果按多列排序
        
        #返回3条记录并打印信息 
        db.users.find().limit(3).forEach(function(user) {print('my age is ' + user.age)}); 
        #结果：
        my age is 18 
        my age is 19 
        my age is 20

* 删除
        
        #删除yy=5的记录
        db.foo.remove({'yy':5})

        #删除所有的记录
        db.foo.remove()
        
        #删除collection
        db.collection.drop()

        #删除当前的数据库
        db.dropDatabase()
        
 * 更新，也即修改数据       
        
        # $set修改器
        db.user.update({"name":"jack"}, {$set:{"age":10}})  #把"name"为"jack"的"age"改为10
        
        #数据库中没有，则新增一条
        db.user.update({"name":"jack"}, {$set:{"age":10}}, upsert=true, multi=true)
        #upsert=true表示没有则增加；multi=true表示允许修改多条记录
        
-----

#### 关闭服务
        
        #关闭mongo服务，关闭后就断开了数据库的所有连接
        #服务端口
        CTRL + C    
        
        #客户端口
        use admin
        db.runCommand("shutdown")
        或者db.shutdownServer()

---

#### 如何将MongoDB做一项windows服务启动
        
        mongod --logpath C:\Users\CB-Ye\my_workspace\MongoDB\logs\MongoDB.log --logappend --dbpath C:\Users\CB-Ye\my_workspace\MongoDB\data --directoryperdb --serviceName MongoDB --install
        
        //参数解释：
        --logappend: 日志是以追加的方式输出的;
        --directoryperdb: 每个DB都会新建一个目录；
        --install: 安装；
        
        //注意点：
        log一定要指定一个xxx.log文件,文件不存在也要这么写，会自动创建;
        这条命令要到MongoDB的bin目录下运行;
---

#### 其他常用的命令

        

---


> 参考文章：[PyMongo官方文档](http://api.mongodb.org/python/current/api/pymongo/collection.html)

---

### 结语：

坚持每天进步一点点...

---