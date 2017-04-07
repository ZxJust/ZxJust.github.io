---
title: 搭建MongoDB Cluster
category: DataBase
layout: post
---

本文主要介绍在window本地搭建MongoDB集群的一些步骤，所有配置都被配置为服务了，其中包含2台路由节点、3台配置节点、2个分片，每个分片都是复制集，目录如下：![MongoDB Cluster目录图片](/assets/img/modules/MongoDB Cluster.png)

### 配置sharding1

```
mongod --shardsvr --profile 0 --dbpath D:\MongoDBCluster\Data\Sharding1-A-27021 --logpath D:\MongoDBCluster\Log\Sharding1-A-27021.txt --journal --directoryperdb --logappend --nohttpinterface  --replSet rs1  --bind_ip localhost --port 27021 --install --serviceName MongoDB1-A-27021 --serviceDisplayName MongoDB1-A-27021

mongod --shardsvr --profile 0 --dbpath D:\MongoDBCluster\Data\Sharding1-M-27022 --logpath D:\MongoDBCluster\Log\Sharding1-M-27022.txt --journal --directoryperdb --logappend --nohttpinterface  --replSet rs1  --bind_ip localhost --port 27022 --install --serviceName MongoDB1-M-27022 --serviceDisplayName MongoDB1-M-27022

mongod --shardsvr --profile 0 --dbpath D:\MongoDBCluster\Data\Sharding1-S-27023 --logpath D:\MongoDBCluster\Log\Sharding1-S-27023.txt --journal --directoryperdb --logappend --nohttpinterface  --replSet rs1  --bind_ip localhost --port 27023 --install --serviceName MongoDB1-S-27023 --serviceDisplayName MongoDB1-S-27023
```

### 配置sharding2

```
mongod --shardsvr --profile 0 --dbpath D:\MongoDBCluster\Data\Sharding2-A-27024 --logpath D:\MongoDBCluster\Log\Sharding2-A-27024.txt --journal --directoryperdb --logappend --nohttpinterface  --replSet rs2  --bind_ip localhost --port 27024 --install --serviceName MongoDB2-A-27024 --serviceDisplayName MongoDB2-A-27024

mongod --shardsvr --profile 0 --dbpath D:\MongoDBCluster\Data\Sharding2-M-27025 --logpath D:\MongoDBCluster\Log\Sharding2-M-27025.txt --journal --directoryperdb --logappend --nohttpinterface  --replSet rs2  --bind_ip localhost --port 27025 --install --serviceName MongoDB2-M-27025 --serviceDisplayName MongoDB2-M-27025

mongod --shardsvr --profile 0 --dbpath D:\MongoDBCluster\Data\Sharding2-S-27026 --logpath D:\MongoDBCluster\Log\Sharding2-S-27026.txt --journal --directoryperdb --logappend --nohttpinterface  --replSet rs2  --bind_ip localhost --port 27026 --install --serviceName MongoDB2-S-27026 --serviceDisplayName MongoDB2-S-27026
```
其中：
*   dbpath：数据存放目录
*   logpath：日志存放路径
*   pidfilepath：进程文件，方便停止mongodb
*   directoryperdb：为每一个数据库按照数据库名建立文件夹存放
*   logappend：以追加的方式记录日志
*   replSet：replica set的名字
*   bind_ip：mongodb所绑定的ip地址
*   port：mongodb进程所使用的端口号，默认为27017
*   oplogSize：mongodb操作日志文件的最大大小。单位为Mb，默认为硬盘剩余空间的5%
*   fork：以后台方式运行进程(window不支持)
*   noprealloc：不预先分配存储

### 配置configServer

```
mongod --configsvr --profile 0 --dbpath D:\MongoDBCluster\Data\C-27014 --logpath D:\MongoDBCluster\Log\C-27014.txt --storageEngine wiredTiger --journal --directoryperdb --logappend --nohttpinterface  --replSet rsconf  --bind_ip localhost --port 27014 --install --serviceName MongoDB-C-27014 --serviceDisplayName MongoDB-C-27014

mongod --configsvr --profile 0 --dbpath D:\MongoDBCluster\Data\C-27015 --logpath D:\MongoDBCluster\Log\C-27015.txt --storageEngine wiredTiger --journal --directoryperdb --logappend --nohttpinterface  --replSet rsconf  --bind_ip localhost --port 27015 --install --serviceName MongoDB-C-27015 --serviceDisplayName MongoDB-C-27015

mongod --configsvr --profile 0 --dbpath D:\MongoDBCluster\Data\C-27016 --logpath D:\MongoDBCluster\Log\C-27016.txt --storageEngine wiredTiger --journal --directoryperdb --logappend --nohttpinterface  --replSet rsconf  --bind_ip localhost --port 27016 --install --serviceName MongoDB-C-27016 --serviceDisplayName MongoDB-C-27016
```
注意：
*   一般情况，一个configServer对应一个分片
*   从MongoDB 3.2开始，可以将configServer配置为Replica Set,其storageEngine应该为wiredTiger，
    并且configServer<=50
*   如果集群中一个或者两个配置服务器不可用,集群的元信息将变为**可读**
*   可以在mongo shell中使用以下命令访问配置服务器的数据库：use config

### 配置Replica Set-configServer

```
mongo localhost:27015  
use admin  
rs.initiate({ _id:"rsconf", configsvr:true, members:[ {_id:0,host:'localhost:27015'}, {_id:1,host:'localhost:27016'},   
{_id:2,host:'localhost:27014'}] })
```

### 配置router1

```
mongos --configdb rsconf/localhost:27014,localhost:27015,localhost:27016 --logpath D:\MongoDBCluster\Log\R-27019.txt --nohttpinterface --bind_ip localhost --port 27019 --install --serviceName MongoDB-R-27019 --serviceDisplayName MongoDB-R-27019
```

### 配置router2

```
mongos --configdb rsconf/localhost:27014,localhost:27015,localhost:27016 --logpath D:\MongoDBCluster\Log\R-27020.txt --nohttpinterface --bind_ip localhost --port 27020 --install --serviceName MongoDB-R-27020 --serviceDisplayName MongoDB-R-27020
```

### 配置Replica Set-sharding1

```
mongo localhost:27022  
use admin  
rs.initiate({ _id:"rs1", members:[ {_id:0,host:'localhost:27022'}, {_id:1,host:'localhost:27023'},   
{_id:2,host:'localhost:27021',arbiterOnly:true}] })
```

### 配置Replica Set-sharding2

```
mongo localhost:27025  
use admin  
rs.initiate({ _id:"rs2", members:[ {_id:0,host:'localhost:27025'}, {_id:1,host:'localhost:27026'},   
{_id:2,host:'localhost:27024',arbiterOnly:true}] })
```

### 给router1添加sharding

```
mongo localhost:27019    #这里必须连接路由节点  
use admin
db.runCommand( { addshard:"rs1/localhost:27022,localhost:27023,localhost:27021",name:"s1"} );
db.runCommand( { addshard:"rs2/localhost:27025,localhost:27026,localhost:27024",name:"s2"} );
db.runCommand( { listshards : 1 } )
db.runCommand({enableSharding:"test"})      #对数据库切分
db.runCommand( { shardCollection: "test.person",key:{id:1}})   #切分条件
```

### 给router2添加sharding

```
mongo localhost:27020   #这里必须连接路由节点  
use admin
sh.addShard( "rs1/localhost:27022,localhost:27023,localhost:27021")
sh.addShard( "rs2/localhost:27025,localhost:27026,localhost:27024")
db.runCommand( { listshards : 1 } ) #查看分片服务器的配置
sh.enableSharding("test")   #指定需要分片的数据库
sh.shardCollection("test.person", {id : 1})  #指定数据库里需要分片的集合和片键
```

### 测试

```
#这里必须连接路由节点  
mongo localhost:27020  
use test

 #如果指定分片的数据库中有数据，需要先给片键添加索引
db.person.ensureIndex({id:1})

#插入数据
for(var i=0;i<10000;i++){  
... db.person.save({id:i,name:"rose",age:20})  
... }  

#查看分片情况如下
db.stats();
db.person.stats();
printShardingStatus()  

#连接分片主节点
mongo localhost:27022
use test
rs.status()
```
