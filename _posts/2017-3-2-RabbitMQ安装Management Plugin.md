---
title: RabbitMQ安装Management Plugin
category: Java
layout: post
---
<h2>{{page.title}}</h2>
<p>1.在目录 <b>D:\RabbitMQ Server\rabbitmq_server-3.6.6\sbin</b> 下输入以下命令：</p>
<p><b>rabbitmq-plugins enable rabbitmq_management</b></p>
<p>2.如果报错，在输入以下命令：</p>
<p><b>rabbitmq-server stop</b></p>
<p><b>rabbitmq-server start</b></p>
<p>3.访问 <b>http://localhost:15672/</b>，并使用默认用户 <b>guest</b>登录，密码也为 <b>guest</b></p>
