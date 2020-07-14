### 0、系统配置

#### 硬盘空间越大越好我这里500g

#### 虚拟环境至少2g内存

#### 下载安装包（需要注册账号）

https://www.oracle.com/database/technologies/112010-linx8664soft.html



![image-20200713135640969](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200713135640969.png)



### 1、添加gpg

http://yum.oracle.com/faq.html#a10



![image-20200713131058423](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200713131058423.png)





```sh
[root@centos706 ~]# wget https://yum.oracle.com/RPM-GPG-KEY-oracle-ol7 -O /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
--2020-07-13 01:15:08--  https://yum.oracle.com/RPM-GPG-KEY-oracle-ol7
Resolving yum.oracle.com (yum.oracle.com)... 23.60.72.193
Connecting to yum.oracle.com (yum.oracle.com)|23.60.72.193|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1011 [text/plain]
Saving to: ‘/etc/pki/rpm-gpg/RPM-GPG-KEY-oracle’

100%[============================================================================================================>] 1,011       --.-K/s   in 0s

2020-07-13 01:15:11 (102 MB/s) - ‘/etc/pki/rpm-gpg/RPM-GPG-KEY-oracle’ saved [1011/1011]

[root@centos706 ~]# gpg --quiet --with-fingerprint /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpg: new configuration file `/root/.gnupg/gpg.conf' created
gpg: WARNING: options in `/root/.gnupg/gpg.conf' are not yet active during this run
pub  2048R/EC551F03 2010-07-01 Oracle OSS group (Open Source Software group) <build@oss.oracle.com>
      Key fingerprint = 4214 4123 FECF C55B 9086  313D 72F9 7B74 EC55 1F03

```

### 2、添加yum源

https://yum.oracle.com/getting-started.html#installing-software-from-oracle-linux-yum-server



![image-20200713133002695](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200713133002695.png)





```shell
[root@centos706 ~]# echo "[ol7_latest]" >> /etc/yum.repos.d/ol7-temp.repo
[root@centos706 ~]# echo "name=Oracle Linux $releasever Latest ($basearch)" >> /etc/yum.repos.d/ol7-temp.repo
[root@centos706 ~]# echo 'baseurl=https://yum.oracle.com/repo/OracleLinux/OL7/latest/$basearch/' >> /etc/yum.repos.d/ol7-temp.repo 
[root@centos706 ~]# echo "gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle" >> /etc/yum.repos.d/ol7-temp.repo
[root@centos706 ~]# echo "gpgcheck=1" >> /etc/yum.repos.d/ol7-temp.repo
[root@centos706 ~]# echo "enabled=1" >> /etc/yum.repos.d/ol7-temp.repo
[root@centos706 ~]# cat /etc/yum.repos.d/ol7-temp.repo
[ol7_latest]
name=Oracle Linux  Latest ()
baseurl=https://yum.oracle.com/repo/OracleLinux/OL7/latest/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
#注意上面第三行是单引号


[root@centos706 ~]# yum install oraclelinux-release-el7
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.bfsu.edu.cn
 * extras: mirrors.bfsu.edu.cn
 * updates: mirrors.bfsu.edu.cn
base                                                                                                                           | 3.6 kB  00:00:00
extras                                                                                                                         | 2.9 kB  00:00:00
ol7_latest                                                                                                                     | 2.7 kB  00:00:00
updates                                                                                                                        | 2.9 kB  00:00:00
(1/3): ol7_latest/x86_64/group                                                                                                 | 660 kB  00:00:03
(2/3): ol7_latest/x86_64/updateinfo                                                                                            | 2.9 MB  00:00:05
(3/3): ol7_latest/x86_64/primary_db                                                                                            |  34 MB  00:00:35
Resolving Dependencies
--> Running transaction check
---> Package oraclelinux-release-el7.x86_64 0:1.0-12.1.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================================================================
 Package                                       Arch                         Version                            Repository                        Size
======================================================================================================================================================
Installing:
 oraclelinux-release-el7                       x86_64                       1.0-12.1.el7                       ol7_latest                        19 k

Transaction Summary
======================================================================================================================================================
Install  1 Package

Total download size: 19 k
Installed size: 29 k
Is this ok [y/d/N]: y
Downloading packages:
warning: /var/cache/yum/x86_64/7/ol7_latest/packages/oraclelinux-release-el7-1.0-12.1.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID ec551f03: NOKEY
Public key for oraclelinux-release-el7-1.0-12.1.el7.x86_64.rpm is not installed
oraclelinux-release-el7-1.0-12.1.el7.x86_64.rpm                                                                                |  19 kB  00:00:01
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
Importing GPG key 0xEC551F03:
 Userid     : "Oracle OSS group (Open Source Software group) <build@oss.oracle.com>"
 Fingerprint: 4214 4123 fecf c55b 9086 313d 72f9 7b74 ec55 1f03
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : oraclelinux-release-el7-1.0-12.1.el7.x86_64                                                                                        1/1
  Verifying  : oraclelinux-release-el7-1.0-12.1.el7.x86_64                                                                                        1/1

Installed:
  oraclelinux-release-el7.x86_64 0:1.0-12.1.el7

Complete!


[root@centos706 ~]# mv /etc/yum.repos.d/ol7-temp.repo /etc/yum.repos.d/ol7-temp.repo.disabled
[root@centos706 ~]# ls /etc/yum.repos.d/
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo    CentOS-Vault.repo          ol7-temp.repo.disabled
CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-Sources.repo  CentOS-x86_64-kernel.repo

```

### 3、修改主机名

https://oracle-base.com/articles/11g/oracle-db-11gr2-installation-on-oracle-linux-7

![image-20200714083042862](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200714083042862.png)

```shell
[root@centos706 ~]# echo "192.168.131.106 ol7-112.localdomain  ol7-112" >> /etc/hosts
[root@centos706 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.131.106 ol7-112.localdomain  ol7-112
[root@centos706 ~]# hostnamectl set-hostname ol7-112.localdomain
[root@centos706 ~]# cat /etc/hostname
ol7-112.localdomain
```



### 4、安装Oracle11gR2预装包

https://oracle-base.com/articles/11g/oracle-db-11gr2-installation-on-oracle-linux-7



![image-20200714081621519](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200714081621519.png)

```shell
[root@centos706 ~]# yum search oracle-rdbms
Loaded plugins: fastestmirror, langpacks
Repository ol7_latest is listed more than once in the configuration
Loading mirror speeds from cached hostfile
 * base: mirrors.bfsu.edu.cn
 * extras: mirrors.bfsu.edu.cn
 * updates: mirrors.bfsu.edu.cn
ol7_UEKR5                                                                                                                      | 2.5 kB  00:00:00
(1/2): ol7_UEKR5/x86_64/updateinfo                                                                                             |  65 kB  00:00:02
(2/2): ol7_UEKR5/x86_64/primary_db                                                                                             | 8.5 MB  00:00:10
============================================================= N/S matched: oracle-rdbms ==============================================================
oracle-rdbms-server-11gR2-preinstall.x86_64 : Sets the system for Oracle single instance and Real Application Cluster install for Oracle Linux 7
oracle-rdbms-server-12cR1-preinstall.x86_64 : Sets the system for Oracle Database single instance and Real Application Cluster install for Oracle
                                            : Linux 7

  Name and summary matches only, use "search all" for everything.
```

```shell
[root@centos706 ~]# yum install oracle-rdbms-server-11gR2-preinstall
Loaded plugins: fastestmirror, langpacks
Repository ol7_latest is listed more than once in the configuration
Loading mirror speeds from cached hostfile
 * base: mirrors.bfsu.edu.cn
 * extras: mirrors.bfsu.edu.cn
 * updates: mirrors.bfsu.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package oracle-rdbms-server-11gR2-preinstall.x86_64 0:1.0-6.el7 will be installed
...
...
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================================================================
 Package                                              Arch                   Version                                 Repository                  Size
======================================================================================================================================================
Installing:
 oracle-rdbms-server-11gR2-preinstall                 x86_64                 1.0-6.el7                               
...
...
Transaction Summary
======================================================================================================================================================
Install  1 Package  (+47 Dependent packages)
Upgrade             (  5 Dependent packages)

Total download size: 62 M
Is this ok [y/d/N]: y
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
warning: /var/cache/yum/x86_64/7/base/packages/compat-libcap1-1.10-7.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY:--:-- ETA
Public key for compat-libcap1-1.10-7.el7.x86_64.rpm is not installed
(1/53): compat-libcap1-1.10-7.el7.x86_64.rpm                                                                                 ...
...
------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                 1.1 MB/s |  62 MB  00:00:54
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-8.2003.0.el7.centos.x86_64 (@anaconda)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : libgcc-4.8.5-39.0.3.el7.x86_64                                                                               ...
...
Installed:
  oracle-rdbms-server-11gR2-preinstall.x86_64 0:1.0-6.el7

Dependency Installed:
  compat-libcap1.x86_64 0:1.10-7.el7                compat-libstdc++-33.x86_64 0:3.2.3-72.el7      cpp.x86_64 0:4.8.5-39.0.3.el7
  gcc.x86_64 0:4.8.5-39.0.3.el7                     gcc-c++.x86_64 0:4.8.5-39.0.3.el7              glibc-devel.x86_64 0:2.17-307.0.1.el7.1
  glibc-headers.x86_64 0:2.17-307.0.1.el7.1         gssproxy.x86_64 0:0.7.0-28.el7                 kernel-container.x86_64 0:3.10.0-0.0.0.2.el7
  kernel-headers.x86_64 0:3.10.0-1127.13.1.el7      keyutils.x86_64 0:1.5.8-3.el7                  ksh.x86_64 0:20120801-142.0.1.el7
  libICE.x86_64 0:1.0.9-9.el7                       libSM.x86_64 0:1.2.2-2.el7                     libX11.x86_64 0:1.6.7-2.el7
  libX11-common.noarch 0:1.6.7-2.el7                libXau.x86_64 0:1.0.8-2.1.el7                  libXext.x86_64 0:1.3.3-3.el7
  libXi.x86_64 0:1.7.9-1.el7                        libXinerama.x86_64 0:1.1.3-2.1.el7             libXmu.x86_64 0:1.1.2-2.el7
  libXrandr.x86_64 0:1.5.1-2.el7                    libXrender.x86_64 0:0.9.10-1.el7               libXt.x86_64 0:1.1.5-3.el7
  libXtst.x86_64 0:1.2.3-1.el7                      libXv.x86_64 0:1.0.11-1.el7                    libXxf86dga.x86_64 0:1.1.4-2.1.el7
  libXxf86misc.x86_64 0:1.0.3-7.1.el7               libXxf86vm.x86_64 0:1.1.4-1.el7                libaio-devel.x86_64 0:0.3.109-13.el7
  libbasicobjects.x86_64 0:0.1.1-32.el7             libcollection.x86_64 0:0.7.0-32.el7            libdmx.x86_64 0:1.1.3-3.el7
  libevent.x86_64 0:2.0.21-4.el7                    libini_config.x86_64 0:1.3.1-32.el7            libmpc.x86_64 0:1.0.1-3.el7
  libnfsidmap.x86_64 0:0.25-19.el7                  libpath_utils.x86_64 0:0.2.1-32.el7            libref_array.x86_64 0:0.1.5-32.el7
  libstdc++-devel.x86_64 0:4.8.5-39.0.3.el7         libverto-libevent.x86_64 0:0.2.5-4.el7         libxcb.x86_64 0:1.13-1.el7
  mpfr.x86_64 0:3.1.1-4.el7                         nfs-utils.x86_64 1:1.3.0-0.66.0.1.el7          psmisc.x86_64 0:22.20-16.el7
  xorg-x11-utils.x86_64 0:7.5-23.el7                xorg-x11-xauth.x86_64 1:1.0.9-1.el7

Dependency Updated:
  glibc.x86_64 0:2.17-307.0.1.el7.1    glibc-common.x86_64 0:2.17-307.0.1.el7.1  libgcc.x86_64 0:4.8.5-39.0.3.el7  libgomp.x86_64 0:4.8.5-39.0.3.el7
  libstdc++.x86_64 0:4.8.5-39.0.3.el7

Complete!
```

### 5、其他配置

https://oracle-base.com/articles/11g/oracle-db-11gr2-installation-on-oracle-linux-7

![image-20200714081243235](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200714081243235.png)



#### 修改Oracle用户密码

```shell
[root@centos706 ~]# passwd oracle
Changing password for user oracle.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
```

#### 禁用selinux

```shell
[root@centos706 ~]# sed -i "s/SELINUX=disabled/SELINUX=permissive/" /etc/selinux/config
[root@centos706 ~]# setenforce Permissive
setenforce: SELinux is disabled
[root@centos706 ~]# cat /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=permissive
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

#### 关闭防火墙

```shell
[root@centos706 ~]# systemctl stop firewalld
[root@centos706 ~]# systemctl disable firewalld
```

#### 创建文件夹并赋予用户权限

```shell
[root@centos706 ~]# mkdir -p /u01/app/oracle/product/11.2.0/dbhome_1
[root@centos706 ~]# chown -R oracle:oinstall /u01
[root@centos706 ~]# chmod -R 775 /u01
```



#### Oracle用户添加环境变量

![image-20200714132237807](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200714132237807.png)

**上图为官网给出环境变量不太美观缺少几项**

**建议使用我给出的环境变量如下：**

```shell
[root@centos706 ~]# su oracle
[oracle@ol7-112 root]$ cd
[oracle@ol7-112 ~]$ vim .bash_profile
```



```shell
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin

export PATH

# Oracle Settings
export TMP=/tmp
export TMPDIR=$TMP

export ORACLE_HOSTNAME=ol7-112.localdomain
export ORACLE_UNQNAME=orcl
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/dbhome_1
export ORA_INVENTORY=/u01/app/oraInventory
export ORACLE_SID=orcl
export ORACLE_TERM=xterm
export DATA_DIR=$ORACLE_BASE/oradata
export RECOVERY_AREA=$ORACLE_BASE/recovery_area

export PATH=/usr/sbin:$PATH
export PATH=$ORACLE_HOME/bin:$PATH

export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
```



### 6、安装数据库



#### 上传数据库文件

```shell
[oracle@ol7-112 u01]$ ll
total 2295592
drwxrwxr-x 3 oracle oinstall         20 Jul 13 01:53 app
-rw-r--r-- 1 oracle oinstall 1239269270 Jul 13 00:54 linux.x64_11gR2_database_1of2.zip
-rw-r--r-- 1 oracle oinstall 1111416131 Jul 13 01:11 linux.x64_11gR2_database_2of2.zip
```



#### 解压数据库

```shell
[oracle@ol7-112 u01]$ unzip linux.x64_11gR2_database_1of2.zip
[oracle@ol7-112 u01]$ unzip linux.x64_11gR2_database_2of2.zip
[oracle@ol7-112 u01]$ ll
total 2295592
drwxrwxr-x 3 oracle oinstall         20 Jul 13 01:53 app
drwxr-xr-x 8 oracle oinstall        128 Aug 20  2009 database
-rw-r--r-- 1 oracle oinstall 1239269270 Jul 13 00:54 linux.x64_11gR2_database_1of2.zip
-rw-r--r-- 1 oracle oinstall 1111416131 Jul 13 01:11 linux.x64_11gR2_database_2of2.zip
```

#### 配置响应文件

https://oracle-base.com/articles/misc/oui-silent-installations



![image-20200714083354488](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200714083354488.png)

#### 响应文件如下，具体配置项请自行查阅

```shell
[oracle@ol7-112 ~]$ cat /u01/database/response/db_install.rsp | grep -v "#"|grep -v "^$"
```



```properties
oracle.install.responseFileVersion=/oracle/install/rspfmt_dbinstall_response_schema_v11_2_0
oracle.install.option=INSTALL_DB_AND_CONFIG
ORACLE_HOSTNAME=${ORACLE_HOSTNAME}
UNIX_GROUP_NAME=oinstall
INVENTORY_LOCATION=${ORA_INVENTORY}
SELECTED_LANGUAGES=en,en_GB
ORACLE_HOME=${ORACLE_HOME}
ORACLE_BASE=${ORACLE_BASE}
oracle.install.db.InstallEdition=EE
oracle.install.db.isCustomInstall=false
oracle.install.db.customComponents=oracle.server:11.2.0.0,oracle.sysman.ccr:10.2.7.0.0,oracle.xdk:11.2.0.0,oracle.rdbms.oci:11.2.0.0,oracle.network:11.2.0.0,oracle.network.listener:11.2.0.0,oracle.rdbms:11.2.0.0,oracle.options:11.2.0.0,oracle.rdbms.partitioning:11.2.0.0,oracle.oraolap:11.2.0.0,oracle.rdbms.dm:11.2.0.0,oracle.rdbms.dv:11.2.0.0,orcle.rdbms.lbac:11.2.0.0,oracle.rdbms.rat:11.2.0.0
oracle.install.db.DBA_GROUP=dba
oracle.install.db.OPER_GROUP=dba
oracle.install.db.CLUSTER_NODES=
oracle.install.db.config.starterdb.type=GENERAL_PURPOSE
oracle.install.db.config.starterdb.globalDBName=${ORACLE_UNQNAME}
oracle.install.db.config.starterdb.SID=${ORACLE_SID}
oracle.install.db.config.starterdb.characterSet=AL32UTF8
oracle.install.db.config.starterdb.memoryOption=true
oracle.install.db.config.starterdb.memoryLimit=900 #特别注意此内存选项
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
oracle.install.db.config.starterdb.automatedBackup.ospwd=oracle
oracle.install.db.config.starterdb.storageType=FILE_SYSTEM_STORAGE
oracle.install.db.config.starterdb.fileSystemStorage.dataLocation=${DATA_DIR}
oracle.install.db.config.starterdb.fileSystemStorage.recoveryLocation=${RECOVERY_AREA}
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



### 7、 开始安装



```shell
[oracle@ol7-112 ~]$ /u01/database/runInstaller -silent -force -ignorePrereq -responseFile /u01/database/response/db_install.rsp
Starting Oracle Universal Installer...

Checking Temp space: must be greater than 120 MB.   Actual 502718 MB    Passed
Checking swap space: must be greater than 150 MB.   Actual 2047 MB    Passed
Preparing to launch Oracle Universal Installer from /tmp/OraInstall2020-07-13_04-28-49AM. Please wait ...[oracle@ol7-112 response]$ [WARNING] [INS-30011] The password entered does not conform to the Oracle recommended standards.
   CAUSE: Oracle recommends that the ADMIN password entered should be at least 8 characters in length, contain at least 1 uppercase character, 1 lower case character and 1 digit [0-9].
   ACTION: Provide a password that conforms to the Oracle recommended standards.
[WARNING] [INS-30011] The password entered does not conform to the Oracle recommended standards.
   CAUSE: Oracle recommends that the ADMIN password entered should be at least 8 characters in length, contain at least 1 uppercase character, 1 lower case character and 1 digit [0-9].
   ACTION: Provide a password that conforms to the Oracle recommended standards.
You can find the log of this install session at:
 /u01/app/oraInventory/logs/installActions2020-07-13_04-28-49AM.log
```

#### 查看日志文件



```shell
[root@ol7-112 ~]# tail -f /u01/app/oraInventory/logs/installActions2020-07-13_04-28-49AM.log
INFO: Installation in progress
INFO: Extracting files to '/u01/app/oracle/product/11.2.0/dbhome_1'.
INFO: Extracting files to '/u01/app/oracle/product/11.2.0/dbhome_1'.
INFO: Performing fastcopy operations based on the information in the file 'oracle.server_EE_exp_1.xml'.
INFO: Performing fastcopy operations based on the information in the file 'racfiles.jar'.
INFO: Performing fastcopy operations based on the information in the file 'oracle.server_EE_dirs.lst'.
INFO: Performing fastcopy operations based on the information in the file 'oracle.server_EE_filemap.jar'.
INFO: Performing fastcopy operations based on the information in the file 'oracle.server_EE_1.xml'.
INFO: Performing fastcopy operations based on the information in the file 'setperms1.sh'.
INFO: Number of threads for fast copy :1
...
INFO: Setting variable 'ROOTSH_LOCATION' to '/u01/app/oracle/product/11.2.0/dbhome_1/root.sh'. Received the value from a code block.
INFO: Setting variable 'ROOTSH_STATUS' to '1'. Received the value from a code block.
INFO: InstallProgressMonitor: Completed phase 8
INFO: Install Phase 2 JRE files in Scratch :0
INFO: Checkpoint:Failed Checkpoint found returning it for getAllFailedCheckPoints.
INFO: Checkpoint:Failed Checkpoint found returning null for getLastFailedCheckPoint.
INFO: Checkpoint:Index file written and updated
INFO: OiicSaveInvWCCE:Before saving the inventory. This is a install session.
INFO: OiicSaveInvWCCE:Before saving the inventory. The platform is unix.
INFO: OiicSaveInvWCCE:Before saving the inventory. The root.sh location is /u01/app/oracle/product/11.2.0/dbhome_1/root.sh
INFO: OiicSaveInvWCCE:Before saving the inventory. The root.sh location exists.
INFO: Initializing OUI save inventory
INFO: Saving global variables for component oracle.server
INFO: Saving global variables completed
INFO: Saving the install inventory which has the access of 1
INFO:

INFO: Start output from spawned process:
INFO: ----------------------------------
...

INFO: ... GenericInternalPlugIn.handleProcess() entered.
INFO: ... GenericInternalPlugIn: getting configAssistantParmas.
INFO: ... GenericInternalPlugIn: checking secretArguments.
INFO: ... GenericInternalPlugIn: starting read loop.
INFO: Read: SYS_PASSWORD_PROMPT
INFO: Processing: SYS_PASSWORD_PROMPT for argument tag -sysPassword
INFO: Read: SYSTEM_PASSWORD_PROMPT
INFO: Processing: SYSTEM_PASSWORD_PROMPT for argument tag -systemPassword
INFO: Read: DBSNMP_PASSWORD_PROMPT
INFO: Processing: DBSNMP_PASSWORD_PROMPT for argument tag -dbsnmpPassword
INFO: Read: SYSMAN_PASSWORD_PROMPT
INFO: Processing: SYSMAN_PASSWORD_PROMPT for argument tag -sysmanPassword
INFO: Read: HOSTUSER_PASSWORD_PROMPT
INFO: Processing: HOSTUSER_PASSWORD_PROMPT for argument tag -hostUserPassword
INFO: End of argument passing to stdin
INFO: Read: Copying database files
INFO: Read: 1% complete
INFO: Read: 3% complete
INFO: Read: 37% complete
INFO: Read: Creating and starting Oracle instance
INFO: Read: 40% complete
INFO: Read: 45% complete
INFO: Read: 50% complete
INFO: Read: 55% complete
INFO: Read: 56% complete
INFO: Read: 57% complete
INFO: Read: 60% complete
INFO: Read: 62% complete
INFO: Read: Completing Database Creation
INFO: Read: 66% complete
INFO: Read: 70% complete
INFO: Read: 73% complete
INFO: Read: 85% complete
INFO: Read: 96% complete
INFO: Read: 100% complete
    INFO: Read: Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/orcl/orcl.log" for further details.
INFO: Completed Plugin named: Oracle Database Configuration Assistant
INFO:
INFO:
INFO: ConfigClient.executeToolsInAggregate action performed
INFO: Exiting ConfigClient.executeToolsInAggregate method
INFO: Calling event ConfigToolsExecuted
INFO: Adding ExitStatus SUCCESS_MINUS_RECTOOL to the exit status set
INFO: ConfigClient.saveSession method called
INFO: Calling event ConfigSessionEnding
INFO: ConfigClient.endSession method called
INFO: Completed Configuration
INFO: Number of root scripts to be executed = 2
INFO: Shutting down OUISetupDriver.JobExecutorThread
INFO: Cleaning up, please wait...
INFO: Dispose the install area control object
INFO: Update the state machine to STATE_CLEAN
INFO: All forked task are completed at state setup
INFO: Completed background operations
INFO: Moved to state <setup>
INFO: Waiting for completion of background operations
INFO: Completed background operations
INFO: Validating state <setup>
WARNING: Validation disabled for the state setup
INFO: Completed validating state <setup>
INFO: Verifying route success
INFO: Waiting for completion of background operations
INFO: Completed background operations
INFO: Executing action at state finish
INFO: FinishAction Actions.execute called
INFO: Completed executing action at state <finish>
INFO: Waiting for completion of background operations
INFO: Completed background operations
INFO: Moved to state <finish>
INFO: Waiting for completion of background operations
INFO: Completed background operations
INFO: Validating state <finish>
WARNING: Validation disabled for the state finish
INFO: Completed validating state <finish>
INFO: Terminating all background operations
INFO: Terminated all background operations
INFO: Successfully executed the flow in SILENT mode
INFO: Finding the most appropriate exit status for the current application
INFO: Exit Status is 3
INFO: Shutdown Oracle Database 11g Release 2 Installer
INFO: Unloading Setup Driver
```

#### 查看创建实例日志



```shell
[root@ol7-112 ~]# cat /u01/app/oracle/cfgtoollogs/dbca/orcl/orcl.log
Copying database files
DBCA_PROGRESS : 1%
DBCA_PROGRESS : 3%
DBCA_PROGRESS : 37%
Creating and starting Oracle instance
DBCA_PROGRESS : 40%
DBCA_PROGRESS : 45%
DBCA_PROGRESS : 50%
DBCA_PROGRESS : 55%
DBCA_PROGRESS : 56%
DBCA_PROGRESS : 57%
DBCA_PROGRESS : 60%
DBCA_PROGRESS : 62%
Completing Database Creation
DBCA_PROGRESS : 66%
DBCA_PROGRESS : 70%
DBCA_PROGRESS : 73%
DBCA_PROGRESS : 85%
Enterprise manager configuration succeeded with the following warning -


Error securing Database Control, Database Control has been brought up in non-secure mode. To secure the Database Control execute the following command(s):

 1) Set the environment variable ORACLE_SID to orcl
 2) /u01/app/oracle/product/11.2.0/dbhome_1/bin/emctl stop dbconsole
 3) /u01/app/oracle/product/11.2.0/dbhome_1/bin/emctl config emkey -repos -sysman_pwd < Password for SYSMAN user >
 4) /u01/app/oracle/product/11.2.0/dbhome_1/bin/emctl secure dbconsole -sysman_pwd < Password for SYSMAN user >
 5) /u01/app/oracle/product/11.2.0/dbhome_1/bin/emctl start dbconsole

 To secure Em Key, run /u01/app/oracle/product/11.2.0/dbhome_1/bin/emctl config emkey -remove_from_repos -sysman_pwd < Password for SYSMAN user >

Provisioning archives deployment failed. Please deploy it manually.
DBCA_PROGRESS : 96%
DBCA_PROGRESS : 100%
Database creation complete. For details check the logfiles at:
 /u01/app/oracle/cfgtoollogs/dbca/orcl.
Database Information:
Global Database Name:orcl
System Identifier(SID):orclThe Database Control URL is http://ol7-112.localdomain:1158/em
```

#### 最后提示用root用户执行以下两个脚本



```shell
[oracle@ol7-112 ~]$ The following configuration scripts need to be executed as the "root" user. 
 #!/bin/sh 
 #Root scripts to run

/u01/app/oraInventory/orainstRoot.sh
/u01/app/oracle/product/11.2.0/dbhome_1/root.sh
To execute the configuration scripts:
	 1. Open a terminal window 
	 2. Log in as "root" 
	 3. Run the scripts 
	 4. Return to this window and hit "Enter" key to continue 

Successfully Setup Software.
```

```shell
[root@ol7-112 ~]# /u01/app/oraInventory/orainstRoot.sh
Changing permissions of /u01/app/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.

Changing groupname of /u01/app/oraInventory to oinstall.
The execution of the script is complete.

[root@ol7-112 ~]# /u01/app/oracle/product/11.2.0/dbhome_1/root.sh
Check /u01/app/oracle/product/11.2.0/dbhome_1/install/root_ol7-112.localdomain_2020-07-13_04-48-43.log for the output of root script
```

#### 测试连接成功

![image-20200714084619353](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200714084619353.png)

### 8、设置开机自动启动

#### 1、编辑oratab文件

```shell
[root@ol7-112 ~]# vim /etc/oratab
```

文件内容为：

```properties
# This file is used by ORACLE utilities.  It is created by root.sh
# and updated by the Database Configuration Assistant when creating
# a database.

# A colon, ':', is used as the field terminator.  A new line terminates
# the entry.  Lines beginning with a pound sign, '#', are comments.
#
# Entries are of the form:
#   $ORACLE_SID:$ORACLE_HOME:<N|Y>:
#
# The first and second fields are the system identifier and home
# directory of the database respectively.  The third filed indicates
# to the dbstart utility that the database should , "Y", or should not,
# "N", be brought up at system boot time.
#
# Multiple entries with the same $ORACLE_SID are not allowed.
#
#
orcl:/u01/app/oracle/product/11.2.0/dbhome_1:N
```

修改为

```properties
# This file is used by ORACLE utilities.  It is created by root.sh
# and updated by the Database Configuration Assistant when creating
# a database.

# A colon, ':', is used as the field terminator.  A new line terminates
# the entry.  Lines beginning with a pound sign, '#', are comments.
#
# Entries are of the form:
#   $ORACLE_SID:$ORACLE_HOME:<N|Y>:
#
# The first and second fields are the system identifier and home
# directory of the database respectively.  The third filed indicates
# to the dbstart utility that the database should , "Y", or should not,
# "N", be brought up at system boot time.
#
# Multiple entries with the same $ORACLE_SID are not allowed.
#
#
orcl:/u01/app/oracle/product/11.2.0/dbhome_1:Y
```



#### 2、创建/etc/init.d/oracle

```shell
[root@ol7-112 ~]# vim /etc/init.d/oracle
```

文件内容为：

```shell
#!/bin/sh

# chkconfig: 2345 61 61

# description: Oracle 11g R2 AutoRun Servimces

# /etc/init.d/oracle

#

# Run-level Startup script for the Oracle Instance, Listener, and

# Web Interface

export ORACLE_BASE=/u01/app/oracle
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



#### 3、赋予/etc/rc.d/init.d执行权限

```shell
[root@ol7-112 ~]# chmod +x /etc/init.d/oracle
[root@ol7-112 ~]# ll /etc/init.d/oracle
-rwxr-xr-x. 1 root root 1007 Jul 13 20:59 /etc/init.d/oracle
```

#### 4、测试脚本

```shell
[root@ol7-112 ~]# /etc/init.d/oracle stop
ORACLE_HOME_LISTNER is not SET, unable to auto-stop Oracle Net Listener
Usage: /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbshut ORACLE_HOME
Processing Database instance "orcl": log file /u01/app/oracle/product/11.2.0/dbhome_1/shutdown.log
Oracle Stop Succesful!OK.
[root@ol7-112 ~]# /etc/init.d/oracle start
ORACLE_HOME_LISTNER is not SET, unable to auto-start Oracle Net Listener
Usage: /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbstart ORACLE_HOME
Processing Database instance "orcl": log file /u01/app/oracle/product/11.2.0/dbhome_1/startup.log
Oracle Start Succesful!OK.
```



#### 5、修改/u01/app/oracle/product/11.2.0/dbhome_1/bin/dbstart文件

```shell
[root@ol7-112 ~]# vim /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbstart
#或者
[root@ol7-112 ~]# sed -i 's/ORACLE_HOME_LISTNER=$1/ORACLE_HOME_LISTNER=$ORACLE_HOME/' /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbstart
[root@ol7-112 ~]# cat /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbstart |grep ORACLE_HOME_LISTNER=
#ORACLE_HOME_LISTNER=$1
ORACLE_HOME_LISTNER=$ORACLE_HOME
```

文件修改内容为（替换掉ORACLE_HOME_LISTNER=$1）：

```shell
省略...
# First argument is used to bring up Oracle Net Listener
#ORACLE_HOME_LISTNER=$1
ORACLE_HOME_LISTNER=$ORACLE_HOME
省略...
```

![dbstartimage-20200714091324230](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/dbstartimage-20200714091324230.png)



#### 6、修改/u01/app/oracle/product/11.2.0/dbhome_1/bin/dbshut

```shell
[root@ol7-112 ~]# vim /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbshut
#或者
[root@ol7-112 ~]# sed -i 's/ORACLE_HOME_LISTNER=$1/ORACLE_HOME_LISTNER=$ORACLE_HOME/' /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbshut
[root@ol7-112 ~]# cat /u01/app/oracle/product/11.2.0/dbhome_1/bin/dbshut |grep ORACLE_HOME_LISTNER=
#ORACLE_HOME_LISTNER=$1
ORACLE_HOME_LISTNER=$ORACLE_HOME
```

文件修改内容为（替换掉ORACLE_HOME_LISTNER=$1）：

```shell
省略...
# The this to bring down Oracle Net Listener
#ORACLE_HOME_LISTNER=$1
ORACLE_HOME_LISTNER=$ORACLE_HOME
省略...
```

![image-20200714091148534](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/image-20200714091148534.png)





#### 7、测试脚本

```shell
[root@ol7-112 ~]# /etc/init.d/oracle stop
Processing Database instance "orcl": log file /u01/app/oracle/product/11.2.0/dbhome_1/shutdown.log
Oracle Stop Succesful!OK.
[root@ol7-112 ~]# /etc/init.d/oracle start
Processing Database instance "orcl": log file /u01/app/oracle/product/11.2.0/dbhome_1/startup.log
Oracle Start Succesful!OK.
[root@ol7-112 ~]# /etc/init.d/oracle restart
Processing Database instance "orcl": log file /u01/app/oracle/product/11.2.0/dbhome_1/shutdown.log
Oracle Stop Succesful!OK.
Processing Database instance "orcl": log file /u01/app/oracle/product/11.2.0/dbhome_1/startup.log
Oracle Start Succesful!OK.
```



#### 8、添加启动服务

```shell
[root@ol7-112 ~]# chkconfig --add oracle
[root@ol7-112 ~]# chkconfig --list oracle

Note: This output shows SysV services only and does not include native
      systemd services. SysV configuration data might be overridden by native
      systemd configuration.

      If you want to list systemd services use 'systemctl list-unit-files'.
      To see services enabled on particular target use
      'systemctl list-dependencies [target]'.

oracle          0:off   1:off   2:on    3:on    4:on    5:on    6:off
```

