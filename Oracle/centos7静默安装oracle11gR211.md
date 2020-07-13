# centos7静默安装oracle11gR2



### 文章目录

[TOC]



### 一、检查硬件要求

#### **1、内存要求：**

要求：内存最小1G，推荐2G或者更高。

```linux
#查看命令,下列是我的内存
[root@oradb ~]# grep MemTotal /proc/meminfo
MemTotal:        1874276 kB
```

PS：还有其他硬件要求可以直接去官网([传送门](https://docs.oracle.com/cd/E11882_01/install.112/e47689/pre_install.htm#LADBI1098))查看，这里不再叙述。

#### **2、安装包：**

  - linux.x64_11gR2_database_1of2.zip
  - linux.x64_11gR2_database_2of2.zip

  PS:官方下载地址：[传送门](https://www.oracle.com/technetwork/cn/database/enterprise-edition/downloads/index.html)；

### 二、环境准备

- #### **1、安装必要的工具**

```linux
#wget：下载工具；zip：打包工具；unzip：解压工具
[root@zhangyi-fedora ~]# yum -y install wget zip unzip
```

PS：如果已经有了就不需重复安装

- **关闭防火墙**

```shell
#查看防火墙状态
[root@zhangyi-fedora ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since 一 2019-03-04 14:31:15 CST; 4min 32s ago
     Docs: man:firewalld(1)
 Main PID: 693 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─693 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

3月 04 14:31:15 localhost.localdomain systemd[1]: Starting firewalld - dynamic firewall daemon...
3月 04 14:31:15 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall daemon.

#关闭防火墙
[root@zhangyi-fedora ~]# systemctl stop firewalld

#禁用防火墙
[root@zhangyi-fedora ~]# systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.

#确认防火墙状态
[root@zhangyi-fedora ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)

3月 04 14:31:15 localhost.localdomain systemd[1]: Starting firewalld - dynamic firewall daemon...
3月 04 14:31:15 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall daemon.
3月 04 14:36:34 zhangyi-fedora.micserver systemd[1]: Stopping firewalld - dynamic firewall daemon...
3月 04 14:36:35 zhangyi-fedora.micserver systemd[1]: Stopped firewalld - dynamic firewall daemon.
```

PS：不关闭防火墙，远程连接会提示连接超时，也可以通过开放对应端口如下

```shell
firewall-cmd --permanent --zone=public --add-port=1521/tcp
firewall-cmd --reload
firewall-cmd --zone=public --list-ports
```



- **关闭Selinux**

```linux
[root@zhangyi-fedora ~]# sed -i "s/SELINUX=enforcing/SELINUX=disabled/" /etc/selinux/config
[root@zhangyi-fedora ~]# setenforce 0
#查看Selinux状态
[root@zhangyi-fedora ~]# /usr/sbin/sestatus -v
```

- **安装Oracle依赖包**

```linux
#通过安装Oracle YUM 源来安装所依赖的包
[root@zhangyi-fedora ~]# cd /etc/yum.repos.d 
[root@zhangyi-fedora yum.repos.d]# wget http://public-yum.oracle.com/public-yum-ol7.repo

#导入RPM-GPG-KEY-oracle
[root@zhangyi-fedora yum.repos.d]# wget http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol7 -O /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle

#安装oracle-rdbms-server-11gR2-preinstall快速配置Oracle安装环境
[root@zhangyi-fedora yum.repos.d]# yum install oracle-rdbms-server-11gR2-preinstall -y

#安装完后查看后台日志内容
[root@zhangyi-fedora yum.repos.d]# more /var/log/oracle-rdbms-server-11gR2-preinstall/results/orakernel.log
```

PS：#oracle-rdbms-server-11gR2-preinstall包所干的事情

```
（1）自动安装oracle所需的RPM包
（2）自动创建oracle用户和group组
（3）自动配置/etc/sysctl.conf内核参数
（4）自动配置/etc/security/limits.conf参数
```



- Fedora 30 server版 缺包 libnsl

```
yum install libnsl*
```

### 三、开始安装

- #### **创建安装目录(目录权限很关键不要搞错了)**

```shell
#创建安装的目录
[root@zhangyi-fedora ~]# mkdir -p /home/oracle/app/oracle/product/11.2.0/dbhome_1

#更改oracle目录的属主
[root@zhangyi-fedora ~]# chown oracle:oinstall -R /home/oracle/app

#更改oracle目录的权限
[root@zhangyi-fedora ~]# chmod 755  -R /home/oracle/app/oracle
```

- #### **配置oracle用户环境变量**

```shell
#切换到oracle用户环境
[root@zhangyi-fedora ~]# su - oracle

#编辑bash_profile文件，追加下列内容
[oracle@zhangyi-fedora ~]$ vi .bash_profile
export TMP=/tmp     #安装oracle软件过程中使用的临时文件目录
export TMPDIR=$TMP    #安装oracle软件过程中使用的临时文件目录
export ORACLE_BASE=/home/oracle/app/oracle   #Oracle的BASE目录，所有关于Oracle的文件全部存放在这个目录中
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_1  #安装Oracle软件存放的目录
export ORACLE_SID=orcl   #将要创建的数据库实例的名字
export ORACLE_TERM=xterm  #安装的时候指定终端的定义资源文件xterm表示窗口方式，rt100表示终端调试模式
export PATH=/usr/sbin:$PATH   
export PATH=$ORACLE_HOME/bin:$PATH   #SHELL可执行文件的搜索路径
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib   #库文件的搜索路径
export CLASSPATH=$ORACLE_HOME/jre:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib #java的class文件执行搜索的bin路径
export EDITOR=vim   #在oracle操作环境下嵌入使用的文本编辑工具
export LANG=en_US.utf8   #oracle用户这个客户端所识别的字符集
export NLS_DATE_FORMAT='YYYY-MM-DD HH24:MI:SS' #oracle用户这个客户端所识别的时间显式格式

#使环境变量生效
[oracle@zhangyi-fedora ~]$ source .bash_profile
```

- **解压oracle安装包**

```shell
#将安装包上传到home/oracle/app文件夹下，可以通过FTP,rz命令等等上传到linux，这里不叙述了
[oracle@zhangyi-fedora ~]$ cd /home/oracle/app
[oracle@zhangyi-fedora home/oracle/app]$ ls
linux.x64_11gR2_database_1of2.zip  linux.x64_11gR2_database_2of2.zip  oracle

#解压安装包
[oracle@zhangyi-fedora home/oracle/app]$ unzip linux.x64_11gR2_database_1of2.zip
[oracle@zhangyi-fedora home/oracle/app]$ unzip linux.x64_11gR2_database_2of2.zip
#多了database文件夹
[oracle@zhangyi-fedora home/oracle/app]$ ls
database  linux.x64_11gR2_database_1of2.zip  linux.x64_11gR2_database_2of2.zip  oracle

[oracle@zhangyi-fedora home/oracle/app]$ ls -lrt
总用量 2295592
drwxr-xr-x. 8 oracle oinstall        128 8月  21 2009 database
-rw-r--r--. 1 root   root     1111416131 3月   1 11:34 linux.x64_11gR2_database_2of2.zip
-rw-r--r--. 1 root   root     1239269270 3月   1 11:35 linux.x64_11gR2_database_1of2.zip
drwxr-xr-x. 3 oracle oinstall         21 3月   4 14:56 oracle

#Oracle静默安装需要用到的应答文件
[oracle@zhangyi-fedora response]$ cd /home/oracle/app/database/response
[oracle@zhangyi-fedora response]$ ll
总用量 76
-rw-rw-r--. 1 oracle oinstall 44969 2月  14 2009 dbca.rsp#创建数据库应答
-rw-rw-r--. 1 oracle oinstall 22557 8月  15 2009 db_install.rsp#安装应答
-rwxrwxr-x. 1 oracle oinstall  5740 2月  26 2009 netca.rsp#建立监听、本地服务名等网络设置的应答
```

- **配置应答文件**

```shell
#修改安装应答
[oracle@zhangyi-fedora response]$ vi db_install.rsp
#可以通过改命令查看文件内容，下列是修改后的内容值
[oracle@zhangyi-fedora response]$ cat db_install.rsp | grep -v "#"|grep -v "^$"
oracle.install.responseFileVersion=/oracle/install/rspfmt_dbinstall_response_schema_v11_2_0
oracle.install.option=INSTALL_DB_AND_CONFIG
ORACLE_HOSTNAME=zhangyi-fedora #(不知道的可以通过hostname命令查询 ps:需要配置/etc/hosts文件末尾加上主机IP 主机名称)
UNIX_GROUP_NAME=oinstall
INVENTORY_LOCATION=/home/oracle/app/oracle/oraInventory
SELECTED_LANGUAGES=en,zh_CN
ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_1
ORACLE_BASE=/home/oracle/app/oracle
oracle.install.db.InstallEdition=EE
oracle.install.db.isCustomInstall=false
oracle.install.db.customComponents=
oracle.install.db.DBA_GROUP=dba
oracle.install.db.OPER_GROUP=dba
oracle.install.db.CLUSTER_NODES=
oracle.install.db.config.starterdb.type=GENERAL_PURPOSE
oracle.install.db.config.starterdb.globalDBName=orcl
oracle.install.db.config.starterdb.SID=orcl
oracle.install.db.config.starterdb.characterSet=AL32UTF8
oracle.install.db.config.starterdb.memoryLimit=1565
oracle.install.db.config.starterdb.memoryOption=true
oracle.install.db.config.starterdb.installExampleSchemas=true
oracle.install.db.config.starterdb.enableSecuritySettings=true
oracle.install.db.config.starterdb.password.ALL=oracle
oracle.install.db.config.starterdb.password.SYS=
oracle.install.db.config.starterdb.password.SYSTEM=
oracle.install.db.config.starterdb.password.SYSMAN=
oracle.install.db.config.starterdb.password.DBSNMP=
oracle.install.db.config.starterdb.control=DB_CONTROL
oracle.install.db.config.starterdb.gridcontrol.gridControlServiceURL=
oracle.install.db.config.starterdb.dbcontrol.enableEmailNotification=false
oracle.install.db.config.starterdb.dbcontrol.emailAddress=
oracle.install.db.config.starterdb.dbcontrol.SMTPServer=
oracle.install.db.config.starterdb.automatedBackup.enable=true
oracle.install.db.config.starterdb.automatedBackup.osuid=oracle
oracle.install.db.config.starterdb.automatedBackup.ospwd=123
oracle.install.db.config.starterdb.storageType=FILE_SYSTEM_STORAGE
oracle.install.db.config.starterdb.fileSystemStorage.dataLocation=/home/oracle/app/oracle/oradata
oracle.install.db.config.starterdb.fileSystemStorage.recoveryLocation=/home/oracle/app/oracle/recovery_area
oracle.install.db.config.asm.diskGroup=
oracle.install.db.config.asm.ASMSNMPPassword=
MYORACLESUPPORT_USERNAME=
MYORACLESUPPORT_PASSWORD=
SECURITY_UPDATES_VIA_MYORACLESUPPORT=false
DECLINE_SECURITY_UPDATES=true
PROXY_HOST=
PROXY_PORT=
PROXY_USER=
PROXY_PWD=
```

- 新建一个文件，没有此文件会报错(暂未明确) 

```linux
vim /home/oracle/oraInst.loc 
#其内容如下：
inventory_loc=/home/oracle/app/app/oraInventory
inst_group=oinstall
#开放权限:
chown oracle:oinstall /home/oracle/oraInst.loc
chmod 664 /home/oracle/oraInst.loc
```


- 安装数据库软件

```shell
#进行安装 
#ps 如果安装包里自带的jre有问题建议在安装命令后加参数 -jerLoc 本地java路径
#如果临时文件夹满了建议清理后在尝试 rm -rf /tmp/Ora*
[oracle@zhangyi-fedora database]$ /home/oracle/app/database/runInstaller -silent -force -ignorePrereq -responseFile  /home/oracle/app/database/response/db_install.rsp 
正在启动 Oracle Universal Installer...

检查临时空间: 必须大于 120 MB。   实际为 11292 MB    通过
检查交换空间: 必须大于 150 MB。   实际为 2047 MB    通过
准备从以下地址启动 Oracle Universal Installer /tmp/OraInstall2019-03-04_03-38-54PM. 请稍候...[oracle@zhangyi-fedora database]$ [WARNING] [INS-32055] 主产品清单位于 Oracle 基目录中。
   原因: 主产品清单位于 Oracle 基目录中。
   操作: Oracle 建议将此主产品清单放置在 Oracle 基目录之外的位置中。
[WARNING] [INS-32055] 主产品清单位于 Oracle 基目录中。
   原因: 主产品清单位于 Oracle 基目录中。
   操作: Oracle 建议将此主产品清单放置在 Oracle 基目录之外的位置中。
可以在以下位置找到本次安装会话的日志:
 /home/oracle/app/oracle/oraInventory/logs/installActions2019-03-04_03-38-54PM.log
 
#ctrl+c退出
#跟踪安装进度
[oracle@zhangyi-fedora database]$ cd /home/oracle/app/oracle/oraInventory/logs/
[oracle@zhangyi-fedora logs]$ tail -f installActions*log
...
#静等两三分钟，会跳出下列内容,表示安装成功
[oracle@zhangyi-fedora database]$ 以下配置脚本需要以 "root" 用户的身份执行,需要新打开一个ssh。
 #!/bin/sh 
 #要运行的 Root 脚本

/home/oracle/app/oracle/oraInventory/orainstRoot.sh
/home/oracle/app/oracle/product/11.2.0/dbhome_1/root.sh
要执行配置脚本, 请执行以下操作:
         1. 打开一个终端窗口
         2. 以 "root" 身份登录
         3. 运行脚本
         4. 返回此窗口并按 "Enter" 键继续

Successfully Setup Software.

#切换到root用户执行脚本
[oracle@zhangyi-fedora database]$ su root
密码：
[root@zhangyi-fedora database]# cd 
[root@zhangyi-fedora ~]# /home/oracle/app/oracle/oraInventory/orainstRoot.sh
更改权限/home/oracle/app/oracle/oraInventory.
添加组的读取和写入权限。
删除全局的读取, 写入和执行权限。

更改组名/home/oracle/app/oracle/oraInventory 到 oinstall.
脚本的执行已完成。

[root@zhangyi-fedora ~]# /home/oracle/app/oracle/product/11.2.0/dbhome_1/root.sh
Check /home/oracle/app/oracle/product/11.2.0/dbhome_1/install/root_zhangyi-fedora.micserver_2019-03-04_15-45-29.log for the output of root script
```

- **安装监听**（正在测试）

```linux
#切换到oracle用户
[root@zhangyi-fedora ~]# su - oracle

#打开database
[oracle@zhangyi-fedora home/oracle/app]$ cd /home/oracle/app/database

#安装应答
[oracle@zhangyi-fedora database]$ $ORACLE_HOME/bin/netca /silent /responseFile /home/oracle/app/database/response/netca.rsp
正在对命令行参数进行语法分析:
参数"silent" = true
参数"responsefile" = /home/oracle/app/database/response/netca.rsp
完成对命令行参数进行语法分析。
Oracle Net Services 配置:
完成概要文件配置。
Oracle Net 监听程序启动:
    正在运行监听程序控制: 
      /home/oracle/app/oracle/product/11.2.0/dbhome_1/bin/lsnrctl start LISTENER
    监听程序控制完成。
    监听程序已成功启动。
监听程序配置完成。
成功完成 Oracle Net Services 配置。退出代码是0

#查看监听状态，监听安装完默认是启动的
[oracle@zhangyi-fedora database]$ lsnrctl status

LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 04-MAR-2019 15:49:31

Copyright (c) 1991, 2009, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 11.2.0.1.0 - Production
Start Date                04-MAR-2019 15:48:42
Uptime                    0 days 0 hr. 0 min. 48 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Listener Log File         /home/oracle/app/oracle/diag/tnslsnr/zhangyi-fedora/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=zhangyi-fedora.micserver)(PORT=1521)))
The listener supports no services
The command completed successfully

#如果监听没有启动，可以通过下列命令启动
[oracle@zhangyi-fedora database]$  lsnrctl start
```

- **静默dbca建立数据库**（正在测试）

```linux
#编辑dbca.rsp文件
[oracle@zhangyi-fedora database]$ vi /home/oracle/app/database/response/dbca.rsp
#进入编辑模式之后，shif+:键，输入set nu 命令是文件显示行数 （ps vim 有此功能 vi没有）
GDBNAME = "orcl.oradb" #78行，全局数据库名字 sid+hostname
SID = "orcl" #149行
CHARACTERSET = "AL32UTF8" #415行，编码
NATIONALCHARACTERSET= "UTF8" #425行

#开始安装，输入的SYS,SYSTEM口令自己定义，是SYS,SYSTEM用户的登陆密码，之后登陆该用户需要用到
[oracle@zhangyi-fedora database]$ $ORACLE_HOME/bin/dbca -silent -responseFile /home/oracle/app/database/response/dbca.rsp
输入 SYS 用户口令: 
 
输入 SYSTEM 用户口令:

复制数据库文件
1% 已完成
3% 已完成
11% 已完成
18% 已完成
26% 已完成
37% 已完成
正在创建并启动 Oracle 实例
40% 已完成
45% 已完成
50% 已完成
55% 已完成
56% 已完成
60% 已完成
62% 已完成
正在进行数据库创建
66% 已完成
70% 已完成
73% 已完成
85% 已完成
96% 已完成
100% 已完成
有关详细信息, 请参阅日志文件 "/home/oracle/app/oracle/cfgtoollogs/dbca/orcl/orcl.log"。

#安装完成后一般自动启动数据库，如果没有输入下列命令启动数据库
[oracle@zhangyi-fedora database]$ shellplus  / as sysdba

shell*Plus: Release 11.2.0.1.0 Production on Mon Mar 4 16:03:00 2019

Copyright (c) 1982, 2009, Oracle.  All rights reserved.


Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

shell> startup #启动数据库
shell> quit #退出
```

到此数据库全部安装完成，以上是按照别人博客安装的，但是window还无法连接改数据库，下列问题列出遇到的问题以及解决

### 四、安装及连接遇到的问题解决

- **ORA-12170:TNS:连接超时**

```linux
查看linux系统的防火墙是否关闭，或者数据库端口是否开放
firewall-cmd --permanent --zone=public --add-port=1521/tcp
firewall-cmd --reload
firewall-cmd --zone=public --list-ports
```

- **ORA-12514 TNS 监听程序当前无法识别连接描述符中请求服务**

```shell
#打开文件夹
[oracle@zhangyi-fedora database]$ cd /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin
[oracle@zhangyi-fedora admin]$ ls
listener.ora  samples  shrept.lst  shellnet.ora  tnsnames.ora

#修改listener.ora，这是修改前的
[oracle@zhangyi-fedora admin]$ vi listener.ora
# listener.ora Network Configuration File: /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
# Generated by Oracle configuration tools.

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
      (ADDRESS = (PROTOCOL = TCP)(HOST = zhangyi-fedora)(PORT = 1521))
    )
  )

ADR_BASE_LISTENER = /home/oracle/app/oracle

#修改后的，192.168.211.42是我虚拟机的ip
[oracle@zhangyi-fedora admin]$ cat listener.ora
# listener.ora Network Configuration File:/home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
# Generated by Oracle configuration tools.

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = orcl)
      (ORACLE_HOME = /home/oracle/app/oracle/product/11.2.0/dbhome_1)
      (SID_NAME = orcl)
    )
  )

LISTENER =(DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = zhangyi-fedora)(PORT = 1521)))
ADR_BASE_LISTENER = /home/oracle/app/oracle


#修改tnsnames.ora，这是修改前的
[oracle@zhangyi-fedora admin]$ vi tnsnames.ora
# tnsnames.ora Network Configuration File: /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/tnsnames.ora
# Generated by Oracle configuration tools.

ORCL =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = zhangyi-fedora)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )

#修改后的
[oracle@zhangyi-fedora admin]$ cat tnsnames.ora
# tnsnames.ora Network Configuration File: /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/tnsnames.ora
# Generated by Oracle configuration tools.

orcl =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = zhangyi-fedora)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = orcl)
    )
  )
  
  #关闭监听服务，有时候关闭不了，提示没有权限操作监听服务，解决方法下一个问题
  [oracle@zhangyi-fedora admin]$ lsnrctl stop 

LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 04-MAR-2019 16:25:32

Copyright (c) 1991, 2009, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=192.168.211.42)(PORT=1521)))
The command completed successfully

#开启监听服务
[oracle@zhangyi-fedora admin]$ lsnrctl start 

LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 04-MAR-2019 16:27:20

Copyright (c) 1991, 2009, Oracle.  All rights reserved.

Starting /home/oracle/app/oracle/product/11.2.0/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 11.2.0.1.0 - Production
System parameter file is /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Log messages written to /home/oracle/app/oracle/diag/tnslsnr/zhangyi-fedora/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.211.42)(PORT=1521)))

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=192.168.211.42)(PORT=1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 11.2.0.1.0 - Production
Start Date                04-MAR-2019 16:27:20
Uptime                    0 days 0 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Listener Log File         /home/oracle/app/oracle/diag/tnslsnr/zhangyi-fedora/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.211.42)(PORT=1521)))
Services Summary...
Service "orcl" has 1 instance(s).
  Instance "orcl", status UNKNOWN, has 1 handler(s) for this service...
The command completed successfully

#登入
[oracle@zhangyi-fedora admin]$ shellplus / as sysdba

shell*Plus: Release 11.2.0.1.0 Production on Mon Mar 4 16:27:54 2019

Copyright (c) 1982, 2009, Oracle.  All rights reserved.


Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

shell> 

#立即关闭数据库服务
shell> shutdown immediate
Database closed.
Database dismounted.
ORACLE instance shut down

#开启数据库服务
shell> startup
ORACLE instance started.

Total System Global Area  764121088 bytes
Fixed Size                  2217264 bytes
Variable Size             452987600 bytes
Database Buffers          301989888 bytes
Redo Buffers                6926336 bytes
Database mounted.
Database opened.

#注册
shell> alter system register;   
System altered.
```

PS：一步都不要少，其实对于修改的这两个文件内容，我猜在配置应答文件的时候配错了，应该直接将我们修改的这些在配置应答文件就配置到对应的地方，不过没有实验，我也是按照别人的博文一步一步来，怕出错不知道怎么修改，到此本地window可以连接数据库了。

- **TNS-01190: The user is not authorized to execute the requested listener command**

在执行lsnrctl stop 命令时，提示没有权限操作监听服务，原因时当前用户不是启动监听的用户，切换到启动监听服务的用户下执行lsnrctl stop 命令就可以了，因为启动监听服务的用户拥有所有权，其他用户不能操作

- **执行lsnrctl stop或者lsnrctl stop，提示lsnrctl: 未找到命令**

切换到oracle用户的时候执行的是 su oracle，正确的是su - oracle

- **ORA-01031: insufficient privileges**

执行shellplus / as sysdba命令时提示该错误，可以先先切换到别的用户环境下，再切换回来试试。

- **本地window连接没有问题，但是其他人无法连接**

检查以下是否能ping通，网络用桥接模式，不然别人ping不通，我用的是NAT模式，导致只能本地连接，别人连接不了我虚拟机的数据库。这种模式下如何ping通我没有查。

- **ora-01950:对表空间XXX无权限**

在创建表的时候，插入数据提示无权限

```shell
#username 换成没有权限的用户
grant  resource to username
```

### 五、设置数据库自启动

#### 方法一：

1. 修改dbstart、dbshut文件

```shell
vi $ORACLE_HOME/bin/dbstart
ORACLE_HOME_LISTNER=$1
#修改为：
ORACLE_HOME_LISTNER=$ORACLE_HOME
vi $ORACLE_HOME/bin/dbshut
ORACLE_HOME_LISTNER=$1
#修改为：
ORACLE_HOME_LISTNER=$ORACLE_HOME
```

2. 修改/etc/oratab文件

```shell
#/home/oracle/app/oracle/product/11.2.0/dbhome_1/ 这个是自己安装路径，只需要将N改为Y
[oracle@zhangyi-fedora ~]# vi /etc/oratab
orcl:/home/oracle/app/oracle/product/11.2.0/dbhome_1/:N
#修改为：
orcl:/home/oracle/app/oracle/product/11.2.0/dbhome_1/:Y
```

3. 把lsnrctl start和dbstart添加到rc.local文件中

```shell
#将下面两句加入到rc.local文件中，路径换成自己的；oracle用户下如果没有权限可以切换到root用户
[root@zhangyi-fedora ~]# vi /etc/rc.d/rc.local
su - oracle -lc "/home/oracle/app/oracle/product/11.2.0/dbhome_1/bin/lsnrctl start"
su - oracle -lc "/home/oracle/app/oracle/product/11.2.0/dbhome_1/bin/dbstart"
```

3. 添加执行权限

```shell
[oracle@zhangyi-fedora ~]$ su root
[root@zhangyi-fedora ~]# chmod +x /etc/rc.d/rc.local
```

4. 重启系统，然后查看一下是否自启动成功。

#### 方法二：（没有操作过，从参考文档摘过来的）

1. ##### 修改/etc/oratab文件

```shell
[oracle@zhangyi-fedora ~]# vi /etc/oratab
找到：    orcl:/home/oracle/app/oracle/product/11.2.0/dbhome_1:N   
修改为：  orcl:/home/oracle/app/oracle/product/11.2.0/dbhome_1:Y
```

##### 2、新建Oracle服务自启动脚本

```shell
    [oracle@zhangyi-fedora ~]# vi /etc/init.d/oracle
```

将以下脚本复制到文件中，保存退出

```shell
#!/bin/sh

# chkconfig: 2345 61 61

# description: Oracle 11g R2 AutoRun Servimces

# /etc/init.d/oracle

#

# Run-level Startup script for the Oracle Instance, Listener, and

# Web Interface

export ORACLE_BASE=/home/oracle/app/oracle #根据个人情况修改路径
export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/dbhome_1
export ORACLE_SID=orcl #改成自己的ORACLE_SID:testsid
export PATH=$PATH:$ORACLE_HOME/bin
ORA_OWNR="oracle"

# if the executables do not exist -- display error

if [ ! -f $ORACLE_HOME/bin/dbstart -o ! -d $ORACLE_HOME ]
then
echo "Oracle startup: cannot start"
exit 1
fi

# depending on parameter -- startup, shutdown, restart

# of the instance and listener or usage display

case "$1" in
start)

# Oracle listener and instance startup

su $ORA_OWNR -lc $ORACLE_HOME/bin/dbstart
echo "Oracle Start Succesful!OK."
;;
stop)

# Oracle listener and instance shutdown

su $ORA_OWNR -lc $ORACLE_HOME/bin/dbshut
echo "Oracle Stop Succesful!OK."
;;
reload|restart)
$0 stop
$0 start
;;
*)
echo $"Usage: `basename $0` {start|stop|reload|reload}"
exit 1
esac
exit 0
```
##### 3、更改oracle脚本的执行权限

```shell
    [root@localhost oracle]# chmod a+x /etc/init.d/oracle
```
##### 4、检查脚本能否执行

```shell
    [root@localhost oracle]# /etc/init.d/oracle start            #启动oracle脚本
    [root@localhost oracle]# /etc/init.d/oracle stop             #关闭oracle脚本
    [root@localhost oracle]# /etc/init.d/oracle restart          #重启oracle脚本
```

##### 5、添加执行权限并建立链接

###### 建立链接将启动脚本添加到系统服务并设置自启动

```shell
    [root@localhost oracle]# chkconfig --add oracle
    #ps 找不到命令的系统请安装chkconfig
    yum install chkconfig
```

ps:当这个命令被执行的时候，会去脚本文件oracle中寻找# chkconfig: 2345 61 61这行注释，并解析这行注释，根据解析结果分别在

```
/etc/rc.d/rc2.d
/etc/rc.d/rc3.d
/etc/rc.d/rc4.d
/etc/rc.d/rc5.d
```

中创建符号连接文件S61oracle，此文件在系统启动时根据运行级别执行，此文件是指向/etc/init.d/oracle文件。启动时系统向此文件发送一个start参数，执行oracle文件中的start分支。另外还会在

```
/etc/rc.d/rc0.d
/etc/rc.d/rc1.d
/etc/rc.d/rc6.d
```

中创建符号连接文件K61oracle，此文件在系统关闭时执行，此文件也指向/etc/init.d/oracle文件，关闭时系统向此文件发送一个stop参数，执行oracle文件中的stop分支。

**#** chkconfig: 2345 61 61

**#** 表明脚本应该在运行级 2, 3, 4, 5 启动，启动优先权为61，停止优先权为 61。

修改服务运行等级(虽然脚本里写过，但还是重新设置一下)，可以自行设置oracle脚本的运行级别

```shell
root@localhost oracle]# chkconfig --level 2345 oracle on
```

说明：设置oracle脚本在运行级别为2、3、4、5时，都是on（开启）状态，off为关闭

###### 查看oracle自动启动设置

```shell
[root@localhost oracle]# chkconfig --list oracle
oracle          0:关    1:关    2:开    3:开    4:开    5:开    6:关
#等级0表示：表示关机
#等级1表示：单用户模式
#等级2表示：无网络连接的多用户命令行模式
#等级3表示：有网络连接的多用户命令行模式
#等级4表示：不可用
#等级5表示：带图形界面的多用户模式
#等级6表示：重新启动
```

手动创建符号链接文件（执行效果和执行chkconfig --add oracle是一样，作为知识笔记记录，可以不执行）

```shell
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc0.d/K61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc1.d/K61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc2.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc3.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc4.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc5.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc6.d/K61oracle
```

##### 6、oracle的启动或关闭管理

```shell
    启动
    [root@localhost oracle]# service oracle start
    停止
    [root@localhost oracle]# service oracle stop
    重启
    [root@localhost oracle]# service oracle restart
```

### 六、数据库字符集修改

PS:没有操作过，从参考文档摘过来的

注意事项：修改字符集前先将数据库进行备份

此处演示将ZHS16GBK字符集修改为AL32UTF8

1、修改server端字符集
登录shellpus查看字符集设置

```shell
[oracle@localhost ~]$ shellplus /nolog
shell*Plus: Release 11.2.0.1.0 Production on Wed Jan 24 13:55:51 2018
Copyright (c) 1982, 2009, Oracle.  All rights reserved.
shell> conn /as sysdba
Connected to an idle instance.                  #数据库未启动，先启动数据库。最好将数据库设未开机启动
shell> startup
shell> conn /as sysdba
Connected.                                      #连接成功
shell> select userenv('language') from dual;      #server端字符集查询


USERENV('LANGUAGE')
----------------------------------------------------
AMERICAN_AMERICA.ZHS16GBK
```

依次执行如下命令

```shell
shell>SHUTDOWN IMMEDIATE;
shell>STARTUP MOUNT;
shell>ALTER SYSTEM ENABLE RESTRICTED SESSION;
shell>ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0;
shell>ALTER SYSTEM SET AQ_TM_PROCESSES=0;
shell>ALTER DATABASE OPEN;
shell>ALTER DATABASE CHARACTER SET INTERNAL_USE AL32UTF8;
shell>SHUTDOWN IMMEDIATE;
shell>STARTUP;

shell> select userenv('language') from dual;
USERENV('LANGUAGE')
----------------------------------------------------
AMERICAN_AMERICA.AL32UTF8
shell> 
```

2、修改client端字符集
查看系统环境变量设置的字符集(client端字符集)

```shell
[oracle@localhost ~]$ cat /home/oracle/.bash_profile
...
PATH=$PATH:$HOME/.local/bin:$HOME/bin
export PATH

export ORACLE_BASE=/usr/oracle
export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/dbhome_1
export ORACLE_SID=orcl
export ORACLE_TERM=xterm
export PATH=$ORACLE_HOME/bin:/usr/sbin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export LANG=C
export NLS_LANG=AMERICAN_AMERICA.ZHS16GBK                #客户端字符集

进入编辑界面，将ZHS16GBK改为AL32UTF8，保存退出
[oracle@localhost ~]$ vim /home/oracle/.bash_profile
使配置生效
[oracle@localhost ~]$ source /home/oracle/.bash_profile
```

[参考文档]

[https://docs.oracle.com/cd/E11882_01/install.112/e47689/toc.htm(官网)](https://docs.oracle.com/cd/E11882_01/install.112/e47689/toc.htm(%E5%AE%98%E7%BD%91))

<https://blog.csdn.net/lqdyx/article/details/78999761>

<https://www.cnblogs.com/nichoc/p/6417505.html>

<https://www.cnblogs.com/VoiceOfDreams/p/8308601.html>