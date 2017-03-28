---
title: RabbitMQ安装Management Plugin
category: Java
layout: post
---

1.在目录 D:\RabbitMQ Server\rabbitmq_server-3.6.6\sbin 下输入以下命令：
>rabbitmq-plugins enable rabbitmq_management

2.如果报错，在输入以下命令：
>rabbitmq-server stop
>
>rabbitmq-server start

3.访问 http://localhost:15672/，并使用默认用户 guest登录，密码也为 guest
