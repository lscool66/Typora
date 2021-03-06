### CentOS 设置 oracle 开机自动启动

#### 1、编辑oratab文件

```shell
[root@ol7-11g ~]# vim /etc/oratab
```

文件内容为：

```shell
#

# This file is used by ORACLE utilities. It is created by root.sh
# and updated by the Database Configuration Assistant when creating
# a database.

# A colon, ':', is used as the field terminator. A new line terminates

# the entry. Lines beginning with a pound sign, '#', are comments.

#

# Entries are of the form:

# $ORACLE_SID:$ORACLE_HOME:<N|Y:

#

# The first and second fields are the system identifier and home

# directory of the database respectively. The third filed indicates

# to the dbstart utility that the database should , "Y", or should not,

# "N", be brought up at system boot time.

#

# Multiple entries with the same $ORACLE_SID are not allowed.

#
#
orcl:/data/oracle/product/11.2.0/db_1:Y
```



#### 2、编辑/etc/rc.d/rc.local

```shell
[root@ol7-11g init.d]# vim /etc/rc.d/rc.local
```

文件内容为：

```shell
#!/bin/bash

# THIS FILE IS ADDED FOR COMPATIBILITY PURPOSES

#

# It is highly advisable to create own systemd services or udev rules

# to run scripts during boot instead of using this file.

#

# In contrast to previous versions due to parallel execution during boot

# this script will NOT be run after all other services.

#

# Please note that you must run 'chmod +x /etc/rc.d/rc.local' to ensure

# that this script will be executed during boot.
```



#### 3、创建/etc/init.d/oracle

```shell
[root@ol7-11g init.d]# vim /etc/init.d/oracle
```

文件内容为：

```shell
#!/bin/sh

# chkconfig: 345 61 61

# description: Oracle 11g R2 AutoRun Servimces

# /etc/init.d/oracle

#

# Run-level Startup script for the Oracle Instance, Listener, and

# Web Interface

export ORACLE_BASE=/data/oracle #根据个人情况修改路径
export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1
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



#### 4、赋予/etc/rc.d/init.d执行权限

```shell
[root@ol7-11g init.d]# cd /etc/rc.d/init.d
[root@ol7-11g init.d]# chmod +x oracle
```

#### 5、测试脚本

```shell
[root@ol7-11g init.d]# ./oracle start
```

运行结果为：

```shell
ORACLE_HOME_LISTNER is not SET, unable to auto-start Oracle Net Listener
Usage: /data/oracle/product/11.2.0/db_1/bin/dbstart ORACLE_HOME
Processing Database instance "orcl": log file /data/oracle/product/11.2.0/db_1/startup.log
Oracle Start Succesful!OK.
```



```shell
[root@ol7-11g init.d]# ./oracle stop
```

运行结果为：

```shell
ORACLE_HOME_LISTNER is not SET, unable to auto-stop Oracle Net Listener
Usage: /data/oracle/product/11.2.0/db_1/bin/dbshut ORACLE_HOME
Processing Database instance "orcl": log file /data/oracle/product/11.2.0/db_1/shutdown.log
Oracle Stop Succesful!OK.
```



#### 6、修改/data/oracle/product/11.2.0/db_1/bin/dbstart文件

```shell
[root@ol7-11g init.d]# vim /data/oracle/product/11.2.0/db_1/bin/dbstart
```

文件修改内容为（替换掉ORACLE_HOME_LISTNER=$1）：

```shell
...
# First argument is used to bring up Oracle Net Listener
#ORACLE_HOME_LISTNER=$1
ORACLE_HOME_LISTNER=$ORACLE_HOME
...
```



#### 7、修改/data/oracle/product/11.2.0/db_1/bin/dbshut

```
[root@ol7-11g init.d]# vim /data/oracle/product/11.2.0/db_1/bin/dbshut
```

文件修改内容为（替换掉ORACLE_HOME_LISTNER=$1）：

```shell
...
# The this to bring down Oracle Net Listener
#ORACLE_HOME_LISTNER=$1
ORACLE_HOME_LISTNER=$ORACLE_HOME
if [ ! $ORACLE_HOME_LISTNER ] ; then
...
```



#### 8、测试脚本

```shell
[root@ol7-11g init.d]# ./oracle start
Processing Database instance "orcl": log file /data/oracle/product/11.2.0/db_1/startup.log
Oracle Start Succesful!OK.
```





#### 9、添加启动服务

```shell
[root@ol7-11g init.d]# chkconfig --add oracle
[root@ol7-11g init.d]# chkconfig --list oracle

Note: This output shows SysV services only and does not include native
      systemd services. SysV configuration data might be overridden by native
      systemd configuration.

      If you want to list systemd services use 'systemctl list-unit-files'.
      To see services enabled on particular target use
      'systemctl list-dependencies [target]'.

oracle    0:off   1:off   2:on    3:on    4:on    5:on    6:off

```
