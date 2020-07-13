# [CentOS 设置 oracle 开机自动启动](https://www.cnblogs.com/anzerong2012/p/7943900.html)



### CentOS 设置 oracle 开机自动启动

###### 1、

```
[root@localhost ~]# gedit /etc/oratab
```

文件内容为：

> ```
> #
> 
> # This file is used by ORACLE utilities. It is created by root.sh
> # and updated by the Database Configuration Assistant when creating
> # a database.
> 
> # A colon, ':', is used as the field terminator. A new line terminates
> 
> # the entry. Lines beginning with a pound sign, '#', are comments.
> 
> #
> 
> # Entries are of the form:
> 
> # $ORACLE_SID:$ORACLE_HOME:<N|Y>:
> 
> #
> 
> # The first and second fields are the system identifier and home
> 
> # directory of the database respectively. The third filed indicates
> 
> # to the dbstart utility that the database should , "Y", or should not,
> 
> # "N", be brought up at system boot time.
> 
> #
> 
> # Multiple entries with the same $ORACLE_SID are not allowed.
> 
> #
> #
> orcl:/data/oracle/product/11.2.0/db_1:Y
> ```
>
> 
>

###### 2、

```
[root@localhost init.d]# gedit /etc/rc.d/rc.local
```

文件内容为：

> ```
> #!/bin/bash
> 
> # THIS FILE IS ADDED FOR COMPATIBILITY PURPOSES
> 
> #
> 
> # It is highly advisable to create own systemd services or udev rules
> 
> # to run scripts during boot instead of using this file.
> 
> #
> 
> # In contrast to previous versions due to parallel execution during boot
> 
> # this script will NOT be run after all other services.
> 
> #
> 
> # Please note that you must run 'chmod +x /etc/rc.d/rc.local' to ensure
> 
> # that this script will be executed during boot.
> ```
>
> 

###### 3、

```
[root@localhost init.d]# gedit /etc/init.d/oracle
```

文件内容为：

> ```
> #!/bin/sh
> 
> # chkconfig: 345 61 61
> 
> # description: Oracle 11g R2 AutoRun Servimces
> 
> # /etc/init.d/oracle
> 
> #
> 
> # Run-level Startup script for the Oracle Instance, Listener, and
> 
> # Web Interface
> 
> export ORACLE_BASE=/data/oracle #根据个人情况修改路径
> export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1
> export ORACLE_SID=orcl #改成自己的ORACLE_SID:testsid
> export PATH=$PATH:$ORACLE_HOME/bin
> ORA_OWNR="oracle"
> 
> # if the executables do not exist -- display error
> 
> if [ ! -f $ORACLE_HOME/bin/dbstart -o ! -d $ORACLE_HOME ]
> then
> echo "Oracle startup: cannot start"
> exit 1
> fi
> 
> # depending on parameter -- startup, shutdown, restart
> 
> # of the instance and listener or usage display
> 
> case "$1" in
> start)
> 
> # Oracle listener and instance startup
> 
> su $ORA_OWNR -lc $ORACLE_HOME/bin/dbstart
> echo "Oracle Start Succesful!OK."
> ;;
> stop)
> 
> # Oracle listener and instance shutdown
> 
> su $ORA_OWNR -lc $ORACLE_HOME/bin/dbshut
> echo "Oracle Stop Succesful!OK."
> ;;
> reload|restart)
> $0 stop
> $0 start
> ;;
> *)
> echo $"Usage: `basename $0` {start|stop|reload|reload}"
> exit 1
> esac
> exit 0
> ```
>
> 

###### 4、

```
[root@localhost init.d]# cd /etc/rc.d/init.d
[root@localhost init.d]# chmod +x oracle
```

###### 5、

```
[root@localhost init.d]# ./oracle start
```

运行结果为：

> ```
> ORACLE_HOME_LISTNER is not SET, unable to auto-start Oracle Net Listener
> Usage: /data/oracle/product/11.2.0/db_1/bin/dbstart ORACLE_HOME
> Processing Database instance "orcl": log file /data/oracle/product/11.2.0/db_1/startup.log
> Oracle Start Succesful!OK.
> ```
>
> 

```
[root@localhost init.d]# ./oracle stop
```

运行结果为：

> ```
> ORACLE_HOME_LISTNER is not SET, unable to auto-stop Oracle Net Listener
> Usage: /data/oracle/product/11.2.0/db_1/bin/dbshut ORACLE_HOME
> Processing Database instance "orcl": log file /data/oracle/product/11.2.0/db_1/shutdown.log
> Oracle Stop Succesful!OK.
> ```
>
> 

###### 6、

```
[root@localhost init.d]# gedit /data/oracle/product/11.2.0/db_1/bin/dbstart
```

文件修改内容为（替换掉ORACLE_HOME_LISTNER=$1）：

> ```
> 省略...
> \# First argument is used to bring up Oracle Net Listener
> \#ORACLE_HOME_LISTNER=$1
> ORACLE_HOME_LISTNER=$ORACLE_HOME
> 省略...
> ```
>
> 

###### 7、

```
[root@localhost init.d]# gedit /data/oracle/product/11.2.0/db_1/bin/dbshut
```

文件修改内容为（替换掉ORACLE_HOME_LISTNER=$1）：

> ```
> 省略...
> \# The this to bring down Oracle Net Listener
> \#ORACLE_HOME_LISTNER=$1
> ORACLE_HOME_LISTNER=$ORACLE_HOME
> if [ ! $ORACLE_HOME_LISTNER ] ; then
> 省略...
> ```
>
> 

###### 8、

```
[root@localhost init.d]# ./oracle start
```

运行结果为

> ```
> Processing Database instance "orcl": log file /data/oracle/product/11.2.0/db_1/startup.log
> Oracle Start Succesful!OK.
> ```
>
> 

###### 9、

```
[root@localhost init.d]# ln -s /etc/rc.d/init.d/oracle /etc/rc2.d/S61oracle
[root@localhost init.d]# ln -s /etc/rc.d/init.d/oracle /etc/rc3.d/S61oracle
[root@localhost init.d]# ln -s /etc/rc.d/init.d/oracle /etc/rc4.d/S61oracle
[root@localhost init.d]# ln -s /etc/rc.d/init.d/oracle /etc/rc0.d/S61oracle
[root@localhost init.d]# ln -s /etc/rc.d/init.d/oracle /etc/rc6.d/S61oracle
```

###### 10、

```
[root@localhost init.d]# ll /etc/rc0.d/
```

> ```
> total 0
> lrwxrwxrwx. 1 root root 32 Nov 15 01:04 K43vmware-tools-thinprint -> ../init.d/vmware-tools-thinprint
> lrwxrwxrwx. 1 root root 20 Nov 14 22:30 K50netconsole -> ../init.d/netconsole
> lrwxrwxrwx. 1 root root 17 Nov 14 22:30 K90network -> ../init.d/network
> lrwxrwxrwx. 1 root root 22 Nov 15 01:04 K99vmware-tools -> ../init.d/vmware-tools
> lrwxrwxrwx 1 root root 23 Dec 1 17:31 S61oracle -> /etc/rc.d/init.d/oracle
> ```
>
> 

###### 11、

```
[root@localhost init.d]# chkconfig --level 234 oracle on
[root@localhost init.d]# chkconfig --add oracle
[root@localhost init.d]# chkconfig --list oracle
```

> ```
> Note: This output shows SysV services only and does not include native
> systemd services. SysV configuration data might be overridden by native
> systemd configuration.
> 
> If you want to list systemd services use 'systemctl list-unit-files'.
> To see services enabled on particular target use
> 'systemctl list-dependencies [target]'.
> 
> oracle 0:on 1:off 2:on 3:on 4:on 5:on 6:on
> ```
>
> 



标签: [CentOS 7](https://www.cnblogs.com/anzerong2012/tag/CentOS%207/), [Linux](https://www.cnblogs.com/anzerong2012/tag/Linux/), [oracle](https://www.cnblogs.com/anzerong2012/tag/oracle/)