## [Centos7下yum安装mongodb](https://www.cnblogs.com/flying1819/articles/9035408.html)



**阅读目录**

- [Centos7下yum安装mongodb](https://www.cnblogs.com/flying1819/articles/9035408.html#_label0)
- [done](https://www.cnblogs.com/flying1819/articles/9035408.html#_label1)

[回到顶部](https://www.cnblogs.com/flying1819/articles/9035408.html#_labelTop)

### Centos7下yum安装mongodb

## 简介

- MongoDB 是一个基于分布式 文件存储的NoSQL数据库
- 由C++语言编写，运行稳定，性能高
- 旨在为 WEB 应用提供可扩展的高性能数据存储解决方案
- 查看[官方网站](https://www.mongodb.com/)

#### MongoDB特点

- 模式自由 :可以把不同结构的文档存储在同一个数据库里
- 面向集合的存储：适合存储 JSON风格文件的形式
- 完整的索引支持：对任何属性可索引
- 复制和高可用性：支持服务器之间的数据复制，支持主-从模式及服务器之间的相互复制。复制的主要目的是提供冗余及自动故障转移
- 自动分片：支持云级别的伸缩性：自动分片功能支持水平的数据库集群，可动态添加额外的机器
- 丰富的查询：支持丰富的查询表达方式，查询指令使用JSON形式的标记，可轻易查询文档中的内嵌的对象及数组
- 快速就地更新：查询优化器会分析查询表达式，并生成一个高效的查询计划
- 高效的传统存储方式：支持二进制数据及大型对象（如照片或图片）

#### Packages包说明

MongoDB官方源中包含以下几个依赖包：
mongodb-org: MongoDB元数据包，安装时自动安装下面四个组件包：
1.mongodb-org-server: 包含MongoDB守护进程和相关的配置和初始化脚本。
2.mongodb-org-mongos: 包含mongos的守护进程。
3.mongodb-org-shell: 包含mongo shell。
4.mongodb-org-tools: 包含MongoDB的工具： mongoimport, bsondump, mongodump, mongoexport, mongofiles, mongooplog, mongoperf, mongorestore, mongostat, and mongotop。

## **安装步骤**

#### **1.配置MongoDB的yum源**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
vim /etc/yum.repos.d/mongodb-org-3.4.repo
#添加以下内容：
[mongodb-org-3.4]  
name=MongoDB Repository  
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/  
gpgcheck=1  
enabled=1  
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc

#这里可以修改 gpgcheck=0, 省去gpg验证
[root@localhost ~]# yum makecache      
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

#### **2.安装MongoDB**

**安装命令：**

```
yum -y install mongodb-org
```

安装完成后

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
已安装:
  mongodb-org.x86_64 0:3.4.14-1.el7

作为依赖被安装:
  mongodb-org-mongos.x86_64 0:3.4.14-1.el7          mongodb-org-server.x86_64 0:3.4.14-1.el7
  mongodb-org-shell.x86_64 0:3.4.14-1.el7           mongodb-org-tools.x86_64 0:3.4.14-1.el7

完毕！
[root@adminset yum.repos.d]#
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

**查看mongo安装位置 :**

```
whereis mongod
```

 

**查看修改配置文件 ：**

```
 vim /etc/mongod.conf
```

#### **3.启动MongoDB** 


**启动mongodb ：**

```
systemctl start mongod.service
```

**停止mongodb ：**

```
systemctl stop mongod.service
```

**查到mongodb的状态：**

```
systemctl status mongod.service
```

#### **4.外网访问需要关闭防火墙：**

**关闭firewall：**

```
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
```

 

#### **5.启动Mongo shell**

**命令：**

```
mongo 
```

查看数据库：

```
show dbs
```

#### **6.设置mongodb远程访问：**

编辑mongod.conf注释**bindIp**,并重启mongodb.(这句配置代表只能本机使用，所以需注释)

```
vim /etc/mongod.conf
```

重启mongodb使修改生效：

```
systemctl restart mongod.service
```

 

**到这里就可以正常使用mongodb了**