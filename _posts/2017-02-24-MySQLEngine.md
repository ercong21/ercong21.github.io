---
layout: post
title: MySQL 存储引擎
categories: MySQL
description: MySQL存储引擎
keywords: MySQL, MySQL存储引擎
---

MySQL 存储引擎是 MySQL 学习中十分重要的一部分，今天就让我们一起来学习一下！

### 什么是存储引擎

存储引擎是如何存储数据、如何为存储的数据建立索引和如何更新、查询数据等技术的实现方法。因为在关系数据库中数据以表的形式存储，所以存储引擎也称为表类型。Oracle、SQL Server等只有一种存储引擎，数据存储管理机制都是一样的，而MySQL供多种存储引擎，可根据不同需求选择存储引擎，也可编写自己的存储引擎。

###  MySQL中有哪些存储引擎

#### MyISAM

MySQL最早提供的引擎，分为静态MyISAM、动态MyISAM和压缩MyISAM三种：

- 静态MyISAM：如果表中各数据列长度预先固定好，服务器将自动选择这种表类型。因为数据表中每条记录所占空间都是一样的，所以这种表存取和更新的效率非常高。当数据受损时，恢复工作也容易做
- 动态MyISAM：如果表中出现varchar、xxxtext或xxxBLOB字段，服务器将自动选择这种表类型。相对静态MyISAM，这种表存储空间小，但由于记录长度不一，所以多次修改后，表中数据可能离散存储在内存中，导致执行效率下降。同时，内存也可能出现碎片。因此，这种表要经常用`optimize table`命令或优化工具进行碎片整理
- 压缩MyISAM：以上表都可用myisamchk压缩。这种表减小了占用存储，但压缩后不能再修改，读取也要先解压

但不管是何种MyISAM表，都不支持事务、行级锁和外键约束。

MyISAM表独立于操作系统，可从Windows移植到Linux；每建立一个MyISAM引擎的表tb_Demo，就会在本地磁盘建立三个文件：tb_demo.frm（存储表定义）、tb_demo.MYD（存储数据）和tb_demo.MYI（存储索引）。

MyISAM存储引擎适合以下几种情况：

1. 选择密集型的表。MyISAM存储引擎在筛选大量数据时非常迅速，这是它最突出的优点
2. 插入密集型的表。MyISAM的并发插入特性允许同时选择和插入数据。如管理邮件或Web服务器日志数据

#### InnoDB

InnoDB对MyISAM进一步更新，提供事务、行级锁机制和外键约束的功能，已被很多公司使用。以下场合使用最理想：

1. 更新密集的表。InnoDB存储引擎特别适合处理多重并发的更新请求
2. 事务。InnoDB存储引擎是支持事务的标准MySQL存储引擎
3. 自动灾难恢复。与其它存储引擎不同，InnoDB表能自动从灾难中恢复
4. 外键约束。MySQL支持外键的存储引擎只有InnoDB
5. 支持自动增加列AUTO_INCREMENT属性

#### MEMORY(Heap)

这种类型的表只存在于内存，它存取速度快，常用于临时表，但当mysqld守护进程崩溃时，所有Memory数据都会丢失。它要求表里数据使用的是长度不变的格式，故不能用BLOB和TEXT等长度可变的数据类型，VARCHAR是长度可变的类型，但在MySQL被当做长度不变的CHAR，故可使用。以下几种情况下常用Memory存储引擎：

1. 数据小且频繁访问。内存存放数据会造成内存使用，可通过参数max_heap_table_size控制Memory表最大大小
2. 如果数据临时且要求立即可用，那么可存放在内存表
3. 存储在Memory表中的数据如果突然丢失，不会对应用服务产生实质的负面影响

Memory支持散列索引和B树索引。B树索引优于散列索引的是，可用部分查询和通配查询，也可用<、>和>=等方便数据挖掘。散列索引相等比较快，但范围比较速度慢，因此散列索引适用=和<>操作符，不适合<或>操作符和order by子句。

#### MERGE

MERGE是MyISAM表的组合，表结构须相同，Merge表没有数据，对Merge表操作实际是对内部MyISAM表操作。使用场景：如用12个相同表存储服务器日志数据，当基于12个表生成报表，需要编写多表查询。与其这样，不如合并起来使用一条查询，再删除Merge表，而不影响原来数据。

#### ARCHIVE

Archive是归档的意思，归档后仅支持插入和查询。MySQL 5.5后，Archive就支持索引了。Archive有很好的压缩机制，它使用zlib压缩库，在记录被请求时会实时压缩，所以常被用来当做仓库使用。

### MySql中关于存储引擎的操作

显示当前数据库支持的存储引擎情况：

```mysql
show engines;
```

显示当前所有表的存储引擎情况：

```mysql
show table status;
```

创建数据库表时设置存储引擎：

```mysql
Create table tableName (
	columnName(列名1)  type(数据类型)  attri(属性设置),
	columnName(列名2)  type(数据类型)  attri(属性设置),
	……..
) engine = engineName;
```

修改表的存储引擎：

```mysql
Alter table tableName engine=engineName;
```