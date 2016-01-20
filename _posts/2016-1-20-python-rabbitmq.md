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

* RabbitMQ 是建立在强大的Erlang OTP平台上，因此安装Rabbit MQ的前提是安装Erlang[http://www.erlang.org/download/otp_win32_R16B03.exe]

* 然后安装rabbitmq[http://www.rabbitmq.com/download.html] , 默认安装的Rabbit MQ 监听端口是5672.

* 使用Rabbit MQ 管理插件，可以更好的可视化方式查看Rabbit MQ 服务器实例的状态：
    激活方式：在终端使用命令："C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin\rabbitmq-plugins.bat" enable rabbitmq_management

* 重启服务：net stop RabbitMQ && net start RabbitMQ

* 使用rabbitmqctl控制台命令来创建用户，密码，绑定权限等：如：C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin>rabbitmqctl.bat add_user name password

* 以上最好在管理员权限下运行

---

#### 第一个Queue通信

        生产者
        sender.py
        
        # -*- coding: UTF-8 -*-
        import pika

        connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
        channel = connection.channel()

        channel.queue_declare(queue='hello',durable=True)

        channel.basic_publish(exchange='', routing_key='hello', body='hello world!')
        print "[x] sent 'hello world!'"

        connection.close()
        
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
            
        channel.basic_consume(callback, queue='hello', no_ack=True)
        channel.start_consuming()
        
* 分别在2个终端运行sender.py 和receiver.py 即可。

* 产生pika.exceptions.ConnectionClosed错误，是因为没有开启rabbitmq服务。在C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin目录下，运行rabbitmq-server start即可

* 在C:\Program Files\RabbitMQ Server\rabbitmq_server-3.6.0\sbin下运行rabbitmqctl.bat list_queues命令会显示当前的queue，以及queue中的消息数。如：
        Listing queues ...
        hello   1
