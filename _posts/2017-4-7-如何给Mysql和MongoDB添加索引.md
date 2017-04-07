---
title: 如何给Mysql和MongoDB添加索引
category: DataBase
layout: post
---

### Mysql

####   1.普通索引
这是最基本的索引，它没有任何限制

*   直接创建索引
```
CREATE INDEX index_name ON table_name(column(length))
```
*   修改表结构的方式添加索引
```
ALTER TABLE table_name ADD INDEX index_name ON (column(length))
```
*   创建表的时候同时创建索引
```
CREATE TABLE `table` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`title` char(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL ,
`time` int(10) NULL DEFAULT NULL ,
PRIMARY KEY (`id`),
INDEX index_name (title(length))
)
```
*   删除索引
```
DROP INDEX index_name ON table
    ```

####   2.唯一索引
索引列的值必须唯一，但允许有空值（注意和主键不同）；如果是组合索引，则列值的组合必须唯一。

*   创建唯一索引
```
CREATE UNIQUE INDEX indexName ON table_name(column(length))
```
*   修改表结构
```
ALTER TABLE table_name ADD UNIQUE indexName ON (column(length))
```
*   创建表时直接指定
```
CREATE TABLE `table` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`title` char(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL ,
`time` int(10) NULL DEFAULT NULL ,
PRIMARY KEY (`id`),
UNIQUE indexName (title(length))
);
```

####   3.全文索引
fulltext索引仅可用于 MyISAM 表;对于大容量的数据表，生成全文索引是一个非常消耗时间非常消耗硬盘空间的做法。

*   创建表时添加全文索引
```
CREATE TABLE `table` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`title` char(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL ,
`time` int(10) NULL DEFAULT NULL ,
PRIMARY KEY (`id`),
FULLTEXT (content)
);
```
*   修改表结构添加全文索引
```
ALTER TABLE article ADD FULLTEXT index_content(content)
```
*   直接创建索引
```
CREATE FULLTEXT INDEX index_content ON article(content)
```

####   4.单列索引、多列索引、组合索引（最左前缀）
*   MySQL只能使用一个索引，在执行查询时，MySQL会从多个索引中选择一个限制最为严格的索引

*   创建组合索引
```
ALTER TABLE article ADD INDEX index_titme_time (title(50),time(10))
其实是相当于分别建立了下面两组索引：(title,time)、(title)
```

####   5.注意
*   MySQL只对以下操作符才使用索引
```
<,<=,=,>,>=,between,in,以及某些时候的like(不以通配符%或_开头的情形)
```

*   使用索引提高了查询速度，同时却会降低更新表的速度

*   索引不会包含有NULL值的列

*   使用短索引，可以提高查询速度，而且可以节省磁盘空间和I/O操作

*   聚集索引与非聚集索引

动作描述  | 使用聚集索引	 | 使用非聚集索引
列经常被分组排序	 | 使用	 | 使用
返回某范围内的数据	 | 使用	 | 不使用
一个或极少不同值	 | 不使用	 | 不使用
小数目的不同值 | 	使用	 | 不使用
大数目的不同值	 | 不使用	 | 使用
频繁更新的列	 | 不使用	 | 使用
外键列	 | 使用	 | 使用
主键列	 | 使用	 | 使用
频繁修改索引列	 | 不使用	 | 使用

### MongoDB

*   创建一般索引
    ```
    默认命名索引名称
    db.user.ensureIndex({"name"：1})

    自定义索引名称
    db.user.ensureIndex({"name"：1},{"name"："myindex"})
    ```
*   创建联合索引
    ```
    db.user.ensureIndex({"name"：1,"age"：1})
    ```
*   创建唯一索引
    ```
    db.user.ensureIndex({"name"：1},{"unique"：true})
    ```
*   删除索引
    ```
    db.runCommand({"dropIndexes"："collectionName","index"："myindex"})
    ```
