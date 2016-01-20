---
layout: post
title:  "Python-Rabbitmq"
date:   2016-1-20 10:30:05
categories: rabbitmq
excerpt: 
---

* content
{:toc}


### Rabbitmq

---

#### win7/8安装Rabbitmq

* RabbitMQ 是建立在强大的Erlang OTP平台上，因此安装Rabbit MQ的前提是安装[Erlang](http://www.erlang.org/download/otp_win32_R16B03.exe)

* 然后安装[rabbitmq](http://www.rabbitmq.com/download.html), 默认安装的Rabbit MQ 监听端口是5672.

* 使用Rabbit MQ 管理插件，可以更好的可视化方式查看Rabbit MQ 服务器实例的状态：激活方式：在终端使用命令：

        "C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin\rabbitmq-plugins.bat" enable rabbitmq_management

* 重启服务：

        net stop RabbitMQ && net start RabbitMQ

* 使用rabbitmqctl控制台命令来创建用户，密码，绑定权限等：如：

        C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin>rabbitmqctl.bat add_user name password

* 以上最好在管理员权限下运行

---

#### 第一个Queue通信

        首先建立一个Connection，然后建立Channels，在channel上建立队列

        生产者
        sender.py
        
        # -*- coding: UTF-8 -*-
        import pika

        connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
        channel = connection.channel()

        channel.queue_declare(queue='hello',durable=True)#指定durable参数为真，队列将持久化；

        channel.basic_publish(exchange='', routing_key='hello', body='hello world!',properties=pika.BasicProperties(delivery_mode = 2,))
        print "[x] sent 'hello world!'"

        connection.close()

> * 参数设置解释：

* durable=True:指定durable参数为真，队列将持久化；
     
* properties=pika.BasicProperties(delivery_mode = 2,): #启用消息持久化，可以防止RabbitMQ Server 重启或者crash引起的数据丢失。
        
        消费者
        receiver.py 
        
        # -*- coding: UTF-8 -*-
        import pika

        connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
        channel = connection.channel()

        channel.queue_declare(queue='hello',durable=True)

        print '[*] waiting for messages. To exit press CTRL+C'

        def callback(ch, method, properties, body):
            print "[x] received %r" % (body)
            ch.basic_ack(delivery_tag = method.delivery_tag)
        
        channel.basic_qos(prefetch_count=1)#同一时间点值处理一个message        
        channel.basic_consume(callback, queue='hello', no_ack=False)
        channel.start_consuming()

> * 参数设置解释：

*  ch.basic_ack(delivery_tag = method.delivery_tag):告诉rabbitmq消息已经正确处理。如果没有这条代码，Consumer退出时，Message会重新分发。然后RabbitMQ会占用越来越多的内存，由于RabbitMQ会长时间运行，因此这个“内存泄漏”是致命的。
     
* no_ack=True:默认为假。设置no-ack参数为真，每次Consumer接到数据后，而不管是否处理完成，RabbitMQ Server会立即把这个Message标记为完成，然后从queue中删除了。为了保证数据不被丢失，RabbitMQ支持消息确认机制，即acknowledgments。为了保证数据能被正确处理而不仅仅是被Consumer收到，那么我们不能采用no-ack。而应该是在处理完数据后发送ack。
    
* channel.basic_qos(prefetch_count=1)公平分发。这样RabbitMQ就会使得每个Consumer在同一个时间点最多处理一个Message。换句话说，在接收到该Consumer的ack前，他它不会将新的Message分发给它。
    
* 分别在2个终端运行sender.py 和receiver.py 即可。

* 产生pika.exceptions.ConnectionClosed错误，是因为没有开启rabbitmq服务。

        C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin
        rabbitmq-server start
        
* 显示当前的queue，以及queue中的消息数

        C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin
        rabbitmqctl.bat list_queues
        //输出
        Listing queues ...
        hello   1

* 其他命令：

        C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin
        
        打开关闭服务
        rabbitmq-server start
        rabbitmq-server stop
        
        打开关闭应用,启动应用，和关闭命令配合使用，达到清空队列的目的
        rabbitmqctl start_app
        rabbitmqctl stop_app
        
        清除所有队列
        rabbitmqctl reset
        
        添加用户： 
        rabbitmqctl add_user root root

        设置权限：
        rabbitmqctl set_permissions -p / root ".*" ".*" ".*"
        
        查看用户： 
        rabbitmqctl list_users
        
---

> 参考文章：[RabbitMQ从入门到精通](http://blog.csdn.net/column/details/rabbitmq.html)