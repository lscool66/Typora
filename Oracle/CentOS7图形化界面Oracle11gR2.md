# CentOS7图形化界面Oracle11gR2

[TOC]

## 一、检查硬件要求



### 1、内存要求：

要求：内存最小1G，推荐2G或者更高。

```shell
#查看命令,下列是我的内存
[root@centos7-minimal opt]# grep MemTotal /proc/meminfo
MemTotal:         995924 kB
```

PS：还有其他硬件要求可以直接去官网([传送门](https://docs.oracle.com/cd/E11882_01/install.112/e47689/pre_install.htm#LADBI1098))查看，这里不再叙述。



### 2、安装包：

- linux.x64_11gR2_database_1of2.zip

- linux.x64_11gR2_database_2of2.zip

  PS:官方下载地址：[传送门](https://www.oracle.com/technetwork/cn/database/enterprise-edition/downloads/index.html)；
  
  [服务器无法访问互联网所需离线软件包]
  https://pan.baidu.com/s/1JsCmtzGlv_QvLBVNxXkDmw

## 二、环境准备

### 1、安装必要的工具

```shell
#wget：下载工具；zip：打包工具；unzip：解压工具
[root@centos7-minimal ~]# yum -y install wget zip unzip xterm xorg-x11-xauth
```

- PS：如果已经有了就不需重复安装

### 2、关闭防火墙

```shell
#查看防火墙状态
[root@centos7-minimal ~]# systemctl status firewalld
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
[root@centos7-minimal ~]# systemctl stop firewalld

#禁用防火墙
[root@centos7-minimal ~]# systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.

#确认防火墙状态
[root@centos7-minimal ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)

3月 04 14:31:15 localhost.localdomain systemd[1]: Starting firewalld - dynamic firewall daemon...
3月 04 14:31:15 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall daemon.
3月 04 14:36:34 centos7-minimal.micserver systemd[1]: Stopping firewalld - dynamic firewall daemon...
3月 04 14:36:35 centos7-minimal.micserver systemd[1]: Stopped firewalld - dynamic firewall daemon.
```

- PS：不关闭防火墙，远程连接会提示连接超时，也可以通过开放对应端口如下

```shell
firewall-cmd --permanent --zone=public --add-port=1521/tcp
firewall-cmd --reload
firewall-cmd --zone=public --list-ports
```

### 3、关闭Selinux

```shell
[root@centos7-minimal ~]# sed -i "s/SELINUX=enforcing/SELINUX=disabled/" /etc/selinux/config
[root@centos7-minimal ~]# setenforce 0
#查看Selinux状态
[root@centos7-minimal ~]# /usr/sbin/sestatus -v
```

### 4、安装Oracle依赖包

```shell
#通过安装Oracle YUM 源来安装所依赖的包
[root@centos7-minimal ~]# cd /etc/yum.repos.d 
[root@centos7-minimal yum.repos.d]# wget http://public-yum.oracle.com/public-yum-ol7.repo

#导入RPM-GPG-KEY-oracle
[root@centos7-minimal yum.repos.d]# wget http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol7 -O /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle

#安装oracle-rdbms-server-11gR2-preinstall快速配置Oracle安装环境
[root@centos7-minimal yum.repos.d]# yum install oracle-rdbms-server-11gR2-preinstall -y

#安装完后查看后台日志内容
[root@centos7-minimal yum.repos.d]# more /var/log/oracle-rdbms-server-11gR2-preinstall/results/orakernel.log
```

```shell
#离线安装方式
[root@centos7-minimal oracle-rdbms-server-11gR2-preinstall]# yum localinstall *.rpm
```

- PS：#oracle-rdbms-server-11gR2-preinstall包所干的事情

```
（1）自动安装oracle所需的RPM包
（2）自动创建oracle用户和group组
（3）自动配置/etc/sysctl.conf内核参数
（4）自动配置/etc/security/limits.conf参数
```

## 三、安装前配置

### 1、修改oracle用户密码

```shell
#修改oracl用户密码
[root@centos7-minimal oracle-rdbms-server-11gR2-preinstall]# passwd oracle
更改用户 oracle 的密码 。
新的 密码：
无效的密码： 密码少于 8 个字符
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
```

### 2、用Oracle登录用户

```shell
#重新打开一个bash切换为Oracle用户登录系统
[oracle@centos7-minimal ~]$
```

### 3、上传安装包到服务器

```shell
#上传安装包到服务器
[oracle@centos7-minimal ~]$ ll
总用量 2295592
-rw-r--r-- 1 oracle oinstall 1239269270 9月  29 15:48 linux.x64_11gR2_database_1of2.zip
-rw-r--r-- 1 oracle oinstall 1111416131 9月  29 15:49 linux.x64_11gR2_database_2of2.zip
```

### 4、解压oracle安装包

```shell
#解压安装包
[oracle@centos7-minimal ~]$ unzip linux.x64_11gR2_database_1of2.zip
[oracle@centos7-minimal ~]$ unzip linux.x64_11gR2_database_2of2.zip
[oracle@centos7-minimal ~]$ ll
总用量 2295592
drwxr-xr-x 8 oracle oinstall        128 8月  21 2009 database
-rw-r--r-- 1 oracle oinstall 1239269270 9月  29 15:48 linux.x64_11gR2_database_1of2.zip
-rw-r--r-- 1 oracle oinstall 1111416131 9月  29 15:49 linux.x64_11gR2_database_2of2.zip
[oracle@centos7-minimal ~]$
```

### 5、配置oracle用户环境变量

```shell
#配置环境变量
[oracle@centos7-minimal ~]$ vim .bash_profile
#立即生效配置文件
[oracle@centos7-minimal ~]$ source .bash_profile
#增加
export ORACLE_BASE=/home/oracle/app/oracle
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_1
export ORACLE_SID=orcl
export PATH=$ORACLE_HOME/bin:$PATH
export LANG=en_US.utf8
```

## 四、开始安装

### 1、运行安装程序

```shell
#运行安装程序
[oracle@centos7-minimal ~]$ cd database/
[oracle@centos7-minimal database]$ ./runInstaller
Starting Oracle Universal Installer...

Checking Temp space: must be greater than 120 MB.   Actual 11210 MB    Passed
Checking swap space: must be greater than 150 MB.   Actual 2047 MB    Passed
Checking monitor: must be configured to display at least 256 colors.    Actual 16777216    Passed
Preparing to launch Oracle Universal Installer from /tmp/OraInstall2019-09-29_04-00-57PM. Please wait ...[oracle@centos7-minimal database]$
#确保安装x11才能出现窗口
```

### 2、反选I wish to receive security updates via My Oracle Support 

==点击next==

![1569744199491](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744199491.png)

### 4、确定点击==yes==

![1569744234616](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744234616.png)

### 5、选择创建并配置数据库（create and configure a database）

==点击next==

![1569744241449](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744241449.png)

### 6、选择server class

点击==next==

![1569744247600](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744247600.png)

### 7、选择单实例安装

点击==next==

![1569744253104](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744253104.png)

### 8、可以选择 [典型安装]() 也可以选择 [高级安装]()

我这里选择的是==典型安装==

点击==next==

![1569744257569](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744257569.png)

### 9、以下是默认生成的安装路径，如果不会配置只需配置密码即可

点击==next==

![1569744274048](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744274048.png)

密码不符合规范点击==yes==即可

![1569744293816](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744293816.png)

默认路径即可

- 点击==next==

![1569744301489](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744301489.png)

### 10、生成响应文件

![1569744317848](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744317848.png)

- 有检测失败的==忽略==即可

![1569744360244](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744360244.png)

- 勾选 ==ignore all==

![Oracle11gR2忽略警告](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/Oracle11gR2%E5%BF%BD%E7%95%A5%E8%AD%A6%E5%91%8A.png)

- 可选保存==响应文件==

![Oracle11gR2保存相应文件](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/Oracle11gR2%E4%BF%9D%E5%AD%98%E7%9B%B8%E5%BA%94%E6%96%87%E4%BB%B6.png)

### 11、开始安装显示安装进度

![1569744420605](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/Oracle11gR2%E5%AE%89%E8%A3%85%E8%BF%9B%E5%BA%A6.png)

### 12、如果安装过程中，在link binaries阶段出现2个错误

```shell
#第一个是关于ins_ctx.mk，log显示：
/lib64/libstdc++.so.5: undefined reference to `memcpy@GLIBC_2.14'

#原因据说是由于本机的glibc版本高于2.14（实际为2.17）。解决方法：
yum install glibc-static

#该软件包包含一个静态链接库：/usr/lib64/libc.a

#修改/home/oracle/app/oracle/product/11.2.0/dbhome_1/ctx/lib/ins_ctx.mk，
[root@centos7-minimal oracle-rdbms-server-11gR2-preinstall]# vim /home/oracle/app/oracle/product/11.2.0/dbhome_1/ctx/lib/ins_ctx.mk
#将

ctxhx: $(CTXHXOBJ)
       $(LINK_CTXHX) $(CTXHXOBJ) $(INSO_LINK)

#修改为：

ctxhx: $(CTXHXOBJ)
       -static $(LINK_CTXHX) $(CTXHXOBJ) $(INSO_LINK) /usr/lib64/stdc.a

#点击Retry即可。

#第二个错误是”Error in invoking target 'agent nmhs' of makefile'/app/oracle/product/11.2.0/db_1/sysman/lib/ins_emagent.mk.' 

#解决方法，在makefile中添加链接libnnz11库的参数：

#修改/home/oracle/app/oracle/product/11.2.0/dbhome_1/sysman/lib/ins_emagent.mk，
[root@centos7-minimal oracle-rdbms-server-11gR2-preinstall]# vim /home/oracle/app/oracle/product/11.2.0/dbhome_1/sysman/lib/ins_emagent.mk
#将

$(MK_EMAGENT_NMECTL)

#修改为：

$(MK_EMAGENT_NMECTL) -lnnz11

#点击Retry即可。
```

- 按上述修改完相关文件==retry==即可

![1569744874105](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744874105.png)

- 继续安装

![1569744888725](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569744888725.png)

### 13、如果没有配置==hosts==会出现如下错误

![1569745082872](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569745082872.png)

```shell
#只需修改hosts文件retry即可
[root@centos7-minimal oracle-rdbms-server-11gR2-preinstall]# vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.131.7 centos7-minimal
```

- 继续安装

![1569745216134](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569745216134.png)

### 14、接下来会自动创建数据库

![1569745283207](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569745283207.png)

![1569745564001](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569745564001.png)

- 安装成功点击==ok==即可

![1569745958600](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569745958600.png)

- 接下来会出现要你使用root账户执行两个文件，新打开一个窗口登录root账户执行即可

![1569745991431](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569745991431.png)

### 15、最后点击finish即可

![1569746121936](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/1569746121936.png)

### 16、查看监听状态

```shell
#查看监听状态，监听安装完默认是启动的
[oracle@centos7-minimal database]$ lsnrctl status
LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 29-SEP-2019 16:40:50
Copyright (c) 1991, 2009, Oracle.  All rights reserved.
Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 11.2.0.1.0 - Production
Start Date                29-SEP-2019 16:20:15
Uptime                    0 days 0 hr. 20 min. 34 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Listener Log File         /home/oracle/app/oracle/diag/tnslsnr/centos7-minimal/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.131.7)(PORT=1521)))
Services Summary...
Service "orcl" has 1 instance(s).
  Instance "orcl", status READY, has 1 handler(s) for this service...
Service "orclXDB" has 1 instance(s).
  Instance "orcl", status READY, has 1 handler(s) for this service...
The command completed successfully

#如果监听没有启动，可以通过下列命令启动
[oracle@centos7-minimal ~]$ lsnrctl start
LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 29-SEP-2019 21:52:32

Copyright (c) 1991, 2009, Oracle.  All rights reserved.

Starting /home/oracle/app/oracle/product/11.2.0/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 11.2.0.1.0 - Production
System parameter file is /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Log messages written to /home/oracle/app/oracle/diag/tnslsnr/centos7-minimal/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.131.7)(PORT=1521)))

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 11.2.0.1.0 - Production
Start Date                29-SEP-2019 21:52:34
Uptime                    0 days 0 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Listener Log File         /home/oracle/app/oracle/diag/tnslsnr/centos7-minimal/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.131.7)(PORT=1521)))
The listener supports no services
The command completed successfully

```

## 五、安装及连接遇到的问题解决

### ORA-12170:TNS:连接超时

```linux
查看linux系统的防火墙是否关闭，或者数据库端口是否开放
firewall-cmd --permanent --zone=public --add-port=1521/tcp
firewall-cmd --reload
firewall-cmd --zone=public --list-ports

```

### ORA-12514 TNS 监听程序当前无法识别连接描述符中请求服务

```shell
#打开文件夹
[oracle@centos7-minimal database]$ cd /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin
[oracle@centos7-minimal admin]$ ls
listener.ora  samples  shrept.lst  shellnet.ora  tnsnames.ora

#修改listener.ora，这是修改前的
[oracle@centos7-minimal admin]$ vi listener.ora
# listener.ora Network Configuration File: /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
# Generated by Oracle configuration tools.

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
      (ADDRESS = (PROTOCOL = TCP)(HOST = centos7-minimal)(PORT = 1521))
    )
  )

ADR_BASE_LISTENER = /home/oracle/app/oracle

#修改后的，192.168.211.42是我虚拟机的ip
[oracle@centos7-minimal admin]$ cat listener.ora
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

LISTENER =(DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = centos7-minimal)(PORT = 1521)))
ADR_BASE_LISTENER = /home/oracle/app/oracle

#修改tnsnames.ora，这是修改前的
[oracle@centos7-minimal admin]$ vi tnsnames.ora
# tnsnames.ora Network Configuration File: /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/tnsnames.ora
# Generated by Oracle configuration tools.

ORCL =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = centos7-minimal)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )

#修改后的
[oracle@centos7-minimal admin]$ cat tnsnames.ora
# tnsnames.ora Network Configuration File: /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/tnsnames.ora
# Generated by Oracle configuration tools.

orcl =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = centos7-minimal)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = orcl)
    )
  )

#关闭监听服务，有时候关闭不了，提示没有权限操作监听服务，解决方法下一个问题
[oracle@centos7-minimal admin]$ lsnrctl stop 

LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 04-MAR-2019 16:25:32

Copyright (c) 1991, 2009, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=192.168.211.42)(PORT=1521)))
The command completed successfully

#开启监听服务
[oracle@centos7-minimal admin]$ lsnrctl start 

LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 04-MAR-2019 16:27:20

Copyright (c) 1991, 2009, Oracle.  All rights reserved.

Starting /home/oracle/app/oracle/product/11.2.0/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 11.2.0.1.0 - Production
System parameter file is /home/oracle/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Log messages written to /home/oracle/app/oracle/diag/tnslsnr/centos7-minimal/listener/alert/log.xml
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
Listener Log File         /home/oracle/app/oracle/diag/tnslsnr/centos7-minimal/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.211.42)(PORT=1521)))
Services Summary...
Service "orcl" has 1 instance(s).
  Instance "orcl", status UNKNOWN, has 1 handler(s) for this service...
The command completed successfully

#登入
[oracle@centos7-minimal admin]$ shellplus / as sysdba

shell*Plus: Release 11.2.0.1.0 Production on Mon Mar 4 16:27:54 2019

Copyright (c) 1982, 2009, Oracle.  All rights reserved.

Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL> 

#立即关闭数据库服务
SQL> shutdown immediate
Database closed.
Database dismounted.
ORACLE instance shut down

#开启数据库服务
SQL> startup
ORACLE instance started.

Total System Global Area  764121088 bytes
Fixed Size                  2217264 bytes
Variable Size             452987600 bytes
Database Buffers          301989888 bytes
Redo Buffers                6926336 bytes
Database mounted.
Database opened.

#注册
SQL> alter system register;   
System altered.

```

PS：一步都不要少，其实对于修改的这两个文件内容，我猜在配置应答文件的时候配错了，应该直接将我们修改的这些在配置应答文件就配置到对应的地方，不过没有实验，我也是按照别人的博文一步一步来，怕出错不知道怎么修改，到此本地window可以连接数据库了。

- TNS-01190: The user is not authorized to execute the requested listener command

在执行lsnrctl stop 命令时，提示没有权限操作监听服务，原因时当前用户不是启动监听的用户，切换到启动监听服务的用户下执行lsnrctl stop 命令就可以了，因为启动监听服务的用户拥有所有权，其他用户不能操作

- 执行lsnrctl stop或者lsnrctl stop，提示lsnrctl: 未找到命令

切换到oracle用户的时候执行的是 su oracle，正确的是su - oracle

- ORA-01031: insufficient privileges

执行shellplus / as sysdba命令时提示该错误，可以先先切换到别的用户环境下，再切换回来试试。

- 本地window连接没有问题，但是其他人无法连接

检查以下是否能ping通，网络用桥接模式，不然别人ping不通，我用的是NAT模式，导致只能本地连接，别人连接不了我虚拟机的数据库。这种模式下如何ping通我没有查。

- ora-01950:对表空间XXX无权限

在创建表的时候，插入数据提示无权限

```shell
#username 换成没有权限的用户
grant  resource to username
```

## 六、设置数据库自启动

### 1、使用Oracle用户修改两个文件

```shell
vim $ORACLE_HOME/bin/dbstart
ORACLE_HOME_LISTNER=$1
#修改为：
ORACLE_HOME_LISTNER=$ORACLE_HOME
vim $ORACLE_HOME/bin/dbshut
ORACLE_HOME_LISTNER=$1
#修改为：
ORACLE_HOME_LISTNER=$ORACLE_HOME
```

###  2、修改/etc/oratab文件

```shell
[oracle@centos7-minimal ~]# vi /etc/oratab
找到：    orcl:/home/oracle/app/oracle/product/11.2.0/dbhome_1:N   
修改为：  orcl:/home/oracle/app/oracle/product/11.2.0/dbhome_1:Y
```

### 3、新建Oracle服务自启动脚本

```shell
[oracle@centos7-minimal ~]# vi /etc/init.d/oracle
```

- 将以下脚本复制到文件中，保存退出

```shell
#!/bin/sh

# chkconfig: 2345 61 61

# description: Oracle 11g R2 AutoRun Servimces

# /etc/init.d/oracle

#

# Run-level Startup script for the Oracle Instance, Listener, and

# Web Interface

export ORACLE_BASE=/home/oracle/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/dbhome_1
export ORACLE_SID=orcl
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
echo $"Usage: `basename $0` {start|stop|restart|reload}"
exit 1
esac
exit 0
```

### 4、更改oracle脚本的执行权限

```shell
[root@localhost oracle]# chmod a+x /etc/init.d/oracle
```

### 5、检查脚本能否执行

```shell
[root@localhost oracle]# /etc/init.d/oracle start         #启动oracle脚本
[root@localhost oracle]# /etc/init.d/oracle stop          #关闭oracle脚本
[root@localhost oracle]# /etc/init.d/oracle restart       #重启oracle脚本
```

### 6、添加执行权限并建立链接

建立链接将启动脚本添加到系统服务并设置自启动

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

chkconfig: 2345 61 61

表明脚本应该在运行级 2, 3, 4, 5 启动，启动优先权为61，停止优先权为 61。

修改服务运行等级(虽然脚本里写过，但还是重新设置一下)，可以自行设置oracle脚本的运行级别

```shell
root@localhost oracle]# chkconfig --level 2345 oracle on
```

说明：设置oracle脚本在运行级别为2、3、4、5时，都是on（开启）状态，off为关闭

### 7、查看oracle自动启动设置

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

### 8、手动创建符号链接文件

- （执行效果和执行chkconfig --add oracle是一样，作为知识笔记记录，可以不执行）

```shell
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc0.d/K61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc1.d/K61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc2.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc3.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc4.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc5.d/S61oracle
[root@localhost oracle]# ln –s /etc/rc.d/init.d/oracle /etc/rc6.d/K61oracle

```

### 9、oracle的启动或关闭管理

```shell
#启动
[root@localhost oracle]# service oracle start
#停止
[root@localhost oracle]# service oracle stop
#重启
[root@localhost oracle]# service oracle restart
```

## 七、数据库字符集修改

PS:没有操作过，从参考文档摘过来的

注意事项：修改字符集前先将数据库进行备份

此处演示将ZHS16GBK字符集修改为AL32UTF8

### 1、修改server端字符集

登录shellpus查看字符集设置

```shell
[oracle@localhost ~]$ shellplus /nolog
shell*Plus: Release 11.2.0.1.0 Production on Wed Jan 24 13:55:51 2018
Copyright (c) 1982, 2009, Oracle.  All rights reserved.
SQL> conn /as sysdba
Connected to an idle instance.                  #数据库未启动，先启动数据库。最好将数据库设未开机启动
SQL> startup
SQL> conn /as sysdba
Connected.                                      #连接成功
SQL> select userenv('language') from dual;      #server端字符集查询

USERENV('LANGUAGE')
----------------------------------------------------
AMERICAN_AMERICA.ZHS16GBK

```

依次执行如下命令

```shell
SQL>SHUTDOWN IMMEDIATE;
SQL>STARTUP MOUNT;
SQL>ALTER SYSTEM ENABLE RESTRICTED SESSION;
SQL>ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0;
SQL>ALTER SYSTEM SET AQ_TM_PROCESSES=0;
SQL>ALTER DATABASE OPEN;
SQL>ALTER DATABASE CHARACTER SET INTERNAL_USE AL32UTF8;
SQL>SHUTDOWN IMMEDIATE;
SQL>STARTUP;

SQL> select userenv('language') from dual;
USERENV('LANGUAGE')
----------------------------------------------------
AMERICAN_AMERICA.AL32UTF8
SQL> 

```

### 2、修改client端字符集

查看系统环境变量设置的字符集(client端字符集)

```shell
[oracle@localhost ~]$ cat /home/oracle/.bash_profile
...
PATH=$PATH:$HOME/.local/bin:$HOME/bin
export PATH

export ORACLE_BASE=/home/oracle/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/dbhome_1
export ORACLE_SID=orcl
export ORACLE_TERM=xterm
export PATH=$ORACLE_HOME/bin:/usr/sbin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export LANG=en_US.utf8
export NLS_LANG=AMERICAN_AMERICA.ZHS16GBK                #客户端字符集

进入编辑界面，将ZHS16GBK改为AL32UTF8，保存退出
[oracle@localhost ~]$ vim /home/oracle/.bash_profile
使配置生效
[oracle@localhost ~]$ source /home/oracle/.bash_profile

```

## [八、参考文档](https://oracle-base.com/)

### [官方文档](https://oracle-base.com/articles/11g/oracle-db-11gr2-installation-on-oracle-linux-7)

