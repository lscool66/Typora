# CentOS7下使用yum快速安装配置oracle数据库

## 实验环境

```markdown
操作系统：CentOS Linux release 7.3.1611 (Core)
IP： 192.168.230.141
```

## 原理

使用yum工具安装oracle提供的preinstall包，它将自动执行一些与配置步骤：

- 自动下载并安装 Oracle Grid Infrastructure 和 Oracle Database 11g 第 2 版 (11.2.0.3) 安装过程所需的任何额外的软件包和特定软件版本，并通过 yum 或 up2date 功能处理软件包依赖关系。
- 创建用户 oracle 和组 oinstall（针对 OraInventory）、dba（针对 OSDBA），供数据库安装期间使用。（出于安全目的，该用户没有默认口令，且不能远程登录）。要启用远程登录，请使用 passwd 工具设置一个口令。）
- 修改 /etc/sysctl.conf 中的内核参数以更改共享内存、信号、最大文件描述符数量等设置。
- 设置 /etc/security/limits.conf 中的软硬 shell 资源限制，如锁定内存地址空间、打开的文件数量、进程数和核心文件大小。
- 对于 x86_64 计算机，在内核中设置 numa=off。

如此可简化很多配置步骤

## 配置yum的repo文件

进入yum配置文件夹，添加oracle的yum信息库

```vim
[root@localhost ~]# cd /etc/yum.repos.d/
[root@localhost yum.repos.d]# ls
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo    CentOS-Vault.repo
CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-Sources.repo
[root@localhost yum.repos.d]# rm public-yum-ol7.repo 
rm: remove regular file ‘public-yum-ol7.repo’? y
[root@localhost yum.repos.d]# wget http://public-yum.oracle.com/public-yum-ol7.repo
--2017-10-09 19:26:42--  http://public-yum.oracle.com/public-yum-ol7.repo
Resolving public-yum.oracle.com (public-yum.oracle.com)... 23.49.60.35, 173.222.148.42
Connecting to public-yum.oracle.com (public-yum.oracle.com)|23.49.60.35|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 6947 (6.8K) [text/plain]
Saving to: ‘public-yum-ol7.repo’

100%[=========================================================================================>] 6,947       --.-K/s   in 0s      

2017-10-09 19:26:44 (354 MB/s) - ‘public-yum-ol7.repo’ saved [6947/6947]

[root@localhost yum.repos.d]# ls
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo    CentOS-Vault.repo
CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-Sources.repo  public-yum-ol7.repo123456789101112131415161718192021
```

使用yum安装oracle预安装文件，这里我们选择11g的版本为实验对象

```vim
[root@localhost yum.repos.d]# yum install oracle
oracleasm-support.x86_64                        oracle-logos.noarch
oracle-database-server-12cR2-preinstall.x86_64  oracle-rdbms-server-11gR2-preinstall.x86_64
oraclelinux-release.x86_64                      oracle-rdbms-server-12cR1-preinstall.x86_64
[root@localhost yum.repos.d]# yum install oracle-rdbms-server-11gR2-preinstall.x86_64 
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package oracle-rdbms-server-11gR2-preinstall.x86_64 0:1.0-5.el7 will be installed
--> Processing Dependency: gcc-c++ for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: kernel-uek for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: compat-libcap1 for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: ksh for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: libaio-devel for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: compat-libstdc++-33 for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: libstdc++-devel for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Running transaction check
---> Package compat-libcap1.x86_64 0:1.10-7.el7 will be installed
---> Package compat-libstdc++-33.x86_64 0:3.2.3-72.el7 will be installed
---> Package gcc-c++.x86_64 0:4.8.5-16.el7 will be installed
---> Package kernel-container.x86_64 0:3.10.0-0.0.0.2.el7 will be installed
---> Package ksh.x86_64 0:20120801-34.el7 will be installed
---> Package libaio-devel.x86_64 0:0.3.109-13.el7 will be installed
---> Package libstdc++-devel.x86_64 0:4.8.5-16.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================================
 Package                                          Arch               Version                          Repository              Size
===================================================================================================================================
Installing:
 oracle-rdbms-server-11gR2-preinstall             x86_64             1.0-5.el7                        ol7_latest              21 k
Installing for dependencies:
 compat-libcap1                                   x86_64             1.10-7.el7                       base                    19 k
 compat-libstdc++-33                              x86_64             3.2.3-72.el7                     base                   191 k
 gcc-c++                                          x86_64             4.8.5-16.el7                     base                   7.2 M
 kernel-container                                 x86_64             3.10.0-0.0.0.2.el7               ol7_latest             2.6 k
 ksh                                              x86_64             20120801-34.el7                  base                   883 k
 libaio-devel                                     x86_64             0.3.109-13.el7                   base                    13 k
 libstdc++-devel                                  x86_64             4.8.5-16.el7                     base                   1.5 M

Transaction Summary
===================================================================================================================================
Install  1 Package (+7 Dependent packages)

Total download size: 9.8 M
Installed size: 29 M
Is this ok [y/d/N]: y
Downloading packages:
(1/8): compat-libcap1-1.10-7.el7.x86_64.rpm                                                                 |  19 kB  00:00:00     
(2/8): compat-libstdc++-33-3.2.3-72.el7.x86_64.rpm                                                          | 191 kB  00:00:00     
(3/8): ksh-20120801-34.el7.x86_64.rpm                                                                       | 883 kB  00:00:00     
(4/8): libaio-devel-0.3.109-13.el7.x86_64.rpm                                                               |  13 kB  00:00:00     
(5/8): libstdc++-devel-4.8.5-16.el7.x86_64.rpm                                                              | 1.5 MB  00:00:00     
warning: /var/cache/yum/x86_64/7/ol7_latest/packages/kernel-container-3.10.0-0.0.0.2.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID ec551f03: NOKEY
Public key for kernel-container-3.10.0-0.0.0.2.el7.x86_64.rpm is not installed
(6/8): kernel-container-3.10.0-0.0.0.2.el7.x86_64.rpm                                                       | 2.6 kB  00:00:00     
(7/8): oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64.rpm                                            |  21 kB  00:00:01     
(8/8): gcc-c++-4.8.5-16.el7.x86_64.rpm                                                                      | 7.2 MB  00:00:01     
-----------------------------------------------------------------------------------------------------------------------------------
Total                                                                                              6.1 MB/s | 9.8 MB  00:00:01     
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle


GPG key retrieval failed: [Errno 14] curl#37 - "Couldn't open file /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle"12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970
```

直接使用yum安装会报错：Couldn’t open file /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle

经查，这是由于我使用的不是oracle Linux导致，强制跳过gpg检查即可：

```shell
[root@localhost yum.repos.d]# yum install oracle-rdbms-server-11gR2-preinstall.x86_64 --nogpgcheck
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package oracle-rdbms-server-11gR2-preinstall.x86_64 0:1.0-5.el7 will be installed
--> Processing Dependency: gcc-c++ for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: kernel-uek for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: compat-libcap1 for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: ksh for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: libaio-devel for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: compat-libstdc++-33 for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Processing Dependency: libstdc++-devel for package: oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64
--> Running transaction check
---> Package compat-libcap1.x86_64 0:1.10-7.el7 will be installed
---> Package compat-libstdc++-33.x86_64 0:3.2.3-72.el7 will be installed
---> Package gcc-c++.x86_64 0:4.8.5-16.el7 will be installed
---> Package kernel-container.x86_64 0:3.10.0-0.0.0.2.el7 will be installed
---> Package ksh.x86_64 0:20120801-34.el7 will be installed
---> Package libaio-devel.x86_64 0:0.3.109-13.el7 will be installed
---> Package libstdc++-devel.x86_64 0:4.8.5-16.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================================
 Package                                          Arch               Version                          Repository              Size
===================================================================================================================================
Installing:
 oracle-rdbms-server-11gR2-preinstall             x86_64             1.0-5.el7                        ol7_latest              21 k
Installing for dependencies:
 compat-libcap1                                   x86_64             1.10-7.el7                       base                    19 k
 compat-libstdc++-33                              x86_64             3.2.3-72.el7                     base                   191 k
 gcc-c++                                          x86_64             4.8.5-16.el7                     base                   7.2 M
 kernel-container                                 x86_64             3.10.0-0.0.0.2.el7               ol7_latest             2.6 k
 ksh                                              x86_64             20120801-34.el7                  base                   883 k
 libaio-devel                                     x86_64             0.3.109-13.el7                   base                    13 k
 libstdc++-devel                                  x86_64             4.8.5-16.el7                     base                   1.5 M

Transaction Summary
===================================================================================================================================
Install  1 Package (+7 Dependent packages)

Total size: 9.8 M
Installed size: 29 M
Is this ok [y/d/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : libstdc++-devel-4.8.5-16.el7.x86_64                                                                             1/8 
  Installing : gcc-c++-4.8.5-16.el7.x86_64                                                                                     2/8 
  Installing : ksh-20120801-34.el7.x86_64                                                                                      3/8 
  Installing : libaio-devel-0.3.109-13.el7.x86_64                                                                              4/8 
  Installing : kernel-container-3.10.0-0.0.0.2.el7.x86_64                                                                      5/8 
  Installing : compat-libcap1-1.10-7.el7.x86_64                                                                                6/8 
  Installing : compat-libstdc++-33-3.2.3-72.el7.x86_64                                                                         7/8 
  Installing : oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64                                                           8/8 
  Verifying  : oracle-rdbms-server-11gR2-preinstall-1.0-5.el7.x86_64                                                           1/8 
  Verifying  : gcc-c++-4.8.5-16.el7.x86_64                                                                                     2/8 
  Verifying  : libstdc++-devel-4.8.5-16.el7.x86_64                                                                             3/8 
  Verifying  : compat-libstdc++-33-3.2.3-72.el7.x86_64                                                                         4/8 
  Verifying  : compat-libcap1-1.10-7.el7.x86_64                                                                                5/8 
  Verifying  : kernel-container-3.10.0-0.0.0.2.el7.x86_64                                                                      6/8 
  Verifying  : libaio-devel-0.3.109-13.el7.x86_64                                                                              7/8 
  Verifying  : ksh-20120801-34.el7.x86_64                                                                                      8/8 

Installed:
  oracle-rdbms-server-11gR2-preinstall.x86_64 0:1.0-5.el7                                                                          

Dependency Installed:
  compat-libcap1.x86_64 0:1.10-7.el7             compat-libstdc++-33.x86_64 0:3.2.3-72.el7   gcc-c++.x86_64 0:4.8.5-16.el7         
  kernel-container.x86_64 0:3.10.0-0.0.0.2.el7   ksh.x86_64 0:20120801-34.el7                libaio-devel.x86_64 0:0.3.109-13.el7  
  libstdc++-devel.x86_64 0:4.8.5-16.el7         

Complete!
```

如此，便完成了oracle的预配置，查看日志文件，了解到底做了什么操作:

```shell
[root@localhost yum.repos.d]# vim /var/log/oracle-rdbms-server-11gR2-preinstall/results/orakernel.log 

Adding group oinstall with gid 54321
Adding group dba
Adding user oracle with user id 54321, initial login group oinstall, supplementary group dba and  home directory /home/oracle
Changing ownership of /home/oracle to oracle:oinstall
Please set password for oracle user
uid=54321(oracle) gid=54321(oinstall) groups=54321(oinstall),54322(dba)
Creating oracle user passed

Saving a copy of the initial sysctl.conf
Verifying  kernel parameters as per Oracle recommendations...
Adding fs.file-max = 6815744
Adding kernel.sem = 250 32000 100 128
Adding kernel.shmmni = 4096
Adding kernel.shmall = 1073741824
Adding kernel.shmmax = 4398046511104
Adding kernel.panic_on_oops = 1
Adding net.core.rmem_default = 262144
Adding net.core.rmem_max = 4194304
Adding net.core.wmem_default = 262144
Adding net.core.wmem_max = 1048576
Adding net.ipv4.conf.all.rp_filter = 2
Adding net.ipv4.conf.default.rp_filter = 2
Adding fs.aio-max-nr = 1048576
Adding net.ipv4.ip_local_port_range = 9000 65500
Setting kernel parameters as per oracle recommendations...
Altered file /etc/sysctl.conf
Saved a copy of the current file in /etc/sysctl.d/99-oracle-rdbms-server-11gR2-preinstall-sysctl.conf
Check /etc/sysctl.d for backups
Verifying & setting of kernel parameters passed

Setting user limits using /etc/security/limits.conf

Verifying oracle user OS limits as per Oracle recommendations...
Adding oracle soft nofile  1024
Adding oracle hard nofile  65536
Adding oracle soft nproc  16384
Adding oracle hard nproc  16384
Adding oracle soft stack  10240
Adding oracle hard stack  32768
Adding oracle hard memlock  134217728
Adding oracle soft memlock  134217728
Setting oracle user OS limits as per Oracle recommendations...
Altered file /etc/security/limits.conf
Original file backed up at /var/log/oracle-rdbms-server-11gR2-preinstall/backup/Oct-09-2017-18-53-12
Verifying & setting of user limits passed

Saving a copy of /etc/default/grub file in /etc/default/grub-initial.orabackup
Saving a copy of /etc/default/grub in /var/log/oracle-rdbms-server-11gR2-preinstall/backup/Oct-09-2017-18-53-12...
Verifying kernel boot parameters as per Oracle recommendations...
old boot params: "crashkernel=auto rhgb quiet"
new boot params: "crashkernel=auto rhgb quiet numa=off"

old boot params: "crashkernel=auto rhgb quiet numa=off"
new boot params: "crashkernel=auto rhgb quiet numa=off transparent_hugepage=never"

Setting kernel boot parameters as per Oracle recommendations...
G_DIR=/boot/grub2
Default kernel is ->  3.10.0-693.2.2.el7.x86_64
Default saved_entry is -> CentOS Linux (3.10.0-693.2.2.el7.x86_64) 7 (Core)
Default saved_entry_line is ->  linux16 /vmlinuz-3.10.0-693.2.2.el7.x86_64
Saving a copy of grubenv... in /var/log/oracle-rdbms-server-11gR2-preinstall/backup/Oct-09-2017-18-53-12
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-693.2.2.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-693.2.2.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-514.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-514.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-358841bb4583417589c4aa102424e4fc
Found initrd image: /boot/initramfs-0-rescue-358841bb4583417589c4aa102424e4fc.img
done
The saved kernel 3.10.0-693.2.2.el7.x86_64 is now at position - 0
Boot parameters will be effected on next reboot
Altered file /etc/default/grub
Copy of the changed file is in - /etc/default/grub-oracle-rdbms-server-11gR2-preinstall.orabackup
Copy of the original file is in - /var/log/oracle-rdbms-server-11gR2-preinstall/backup/Oct-09-2017-18-53-12
Verifying & setting of boot parameters passed

Trying to add NOZEROCONF parameter...
Taking a backup of existing file to /etc/sysconfig/network.orabackup
Successfully added parameter NOZEROCONF to /etc/sysconfig/network
Setting /etc/sysconfig/network parameters passed

Disabling Transparent Hugepages.
Refer Oracle Note:1557478.1

Disabling defrag.
Refer Oracle Note:1557478.1

Taking a backup of old config files under /var/log/oracle-rdbms-server-11gR2-preinstall/backup/Oct-09-2017-18-53-
```

修改oracle用户的密码：

```vim
[root@localhost yum.repos.d]# passwd oracle
Changing password for user oracle.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.123456
```

## 2、安装oracle数据库

新建目录OraDB11g并将下载好的oracle安装包放入其中

```vim
[root@localhost yum.repos.d]# mkdir -p /home/OraDB11g
[root@localhost yum.repos.d]# cd /home/OraDB11g/
[root@localhost OraDB11g]#  unzip p13390677_112040_Linux-x86-64_1of7.zip
[root@localhost OraDB11g]#  unzip p13390677_112040_Linux-x86-64_2of7.zip1234
```

切换至oracle用户，执行安装命令

```shell
[root@localhost database]# su - oracle
Last login: Mon Oct  9 18:55:10 PDT 2017 on pts/1
[oracle@localhost ~]$ cd /home/OraDB11g/database/

[oracle@localhost database]$ ./runInstaller 
Starting Oracle Universal Installer...

Checking Temp space: must be greater than 120 MB.   Actual 26804 MB    Passed
Checking swap space: must be greater than 150 MB.   Actual 3968 MB    Passed
Checking monitor: must be configured to display at least 256 colors
    >>> Could not execute auto check for display colors using command /usr/bin/xdpyinfo. Check if the DISPLAY variable is set.    Failed <<<<

Some requirement checks failed. You must fulfill these requirements before

continuing with the installation,

Continue? (y/n) [n] y


>>> Ignoring required pre-requisite failures. Continuing...
Preparing to launch Oracle Universal Installer from /tmp/OraInstall2017-10-09_08-23-40PM. Please wait ...
DISPLAY not set. Please set the DISPLAY and try again.
Depending on the Unix Shell, you can use one of the following commands as examples to set the DISPLAY environment variable:
- For csh:              % setenv DISPLAY 192.168.1.128:0.0
- For sh, ksh and bash:     $ DISPLAY=192.168.1.128:0.0; export DISPLAY
Use the following command to see what shell is being used:
    echo $SHELL
Use the following command to view the current DISPLAY environment variable setting:
    echo $DISPLAY
- Make sure that client users are authorized to connect to the X Server.
To enable client users to access the X Server, open an xterm, dtterm or xconsole as the user that started the session and type the following command:
% xhost +
To test that the DISPLAY environment variable is set correctly, run a X11 based program that comes with the native operating system such as 'xclock':
    % <full path to xclock.. see below>
If you are not able to run xclock successfully, please refer to your PC-X Server or OS vendor for further assistance.
Typical path for xclock: /usr/X11R6/bin/xclock
```

执行失败并报错DISPLAY not set. Please set the DISPLAY and try again.

这是由于未配置图形显示 

根据提示进行处理：

```shell
[oracle@localhost database]$ echo $SHELL
/bin/bash
[oracle@localhost database]$ echo $DISPLAY
```

此时确认本操作系统使用的是bash，所以使用第二种配置方式

```vim
[oracle@localhost database]$ DISPLAY=192.168.1.128:0.0; export DISPLAY
[oracle@localhost database]$ xhost +
xhost:  unable to open display "192.168.1.128:0.0"123
```

发现依然无法使用 

重置DISPLAY配置：

```vim
[oracle@localhost database]$ DISPLAY=192.168.1.128:0.0; export DISPLAY
[oracle@localhost database]$ export DISPLAY=:0
[oracle@localhost database]$ xhost +
access control disabled, clients can connect from any host
[oracle@localhost database]$ xhost + 192.168.230.141
192.168.230.141 being added to access control list
```

配置成功，开始正式安装oracle

```vim
[oracle@localhost database]$ ./runInstaller 
Starting Oracle Universal Installer...

Checking Temp space: must be greater than 120 MB.   Actual 25950 MB    Passed
Checking swap space: must be greater than 150 MB.   Actual 3968 MB    Passed
Checking monitor: must be configured to display at least 256 colors.    Actual 16777216    Passed
Preparing to launch Oracle Universal Installer from /tmp/OraInstall2017-10-09_08-36-12PM. Please wait ...
```

弹出安装界面： 
将接受oracle支持项取消，下一步

!![下一步](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010145422317)

选择跳过软件自动更新服务，下一步

![跳过自动更新](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010145506496)

选择创建一个数据库系统，下一步

![创建数据库](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010145521228)

选择桌面系统（我只是实验环境，不是为了搭建服务器环境），下一步

![选择系统类型](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010145605355)

配置默认数据库实例，并设置密码（我使用的是简单密码，所以会有警告），下一步

![配置数据库安装路径](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010145620086)

选择数据库用户分组，这里使用默认的oinstall组，下一步

![配置环境路径](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010150510571)

自动检测数据库安装环境

![检测安装条件](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010150840670)

这里我检测出来缺少2个包：

- elfutils-libelf-devel-0.97
- pdksh-5.2.14

打包elfutils-libelf-devel

```shell
[root@localhost yum.repos.d]# yum install elfutils-libelf-devel
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package elfutils-libelf-devel.x86_64 0:0.168-8.el7 will be installed
--> Processing Dependency: pkgconfig(zlib) for package: elfutils-libelf-devel-0.168-8.el7.x86_64
--> Running transaction check
---> Package zlib-devel.x86_64 0:1.2.7-17.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================================
 Package                                  Arch                      Version                          Repository               Size
===================================================================================================================================
Installing:
 elfutils-libelf-devel                    x86_64                    0.168-8.el7                      base                     37 k
Installing for dependencies:
 zlib-devel                               x86_64                    1.2.7-17.el7                     base                     50 k

Transaction Summary
===================================================================================================================================
Install  1 Package (+1 Dependent package)

Total download size: 87 k
Installed size: 163 k
Is this ok [y/d/N]: y
Downloading packages:
(1/2): zlib-devel-1.2.7-17.el7.x86_64.rpm                                                                   |  50 kB  00:00:00     
(2/2): elfutils-libelf-devel-0.168-8.el7.x86_64.rpm                                                         |  37 kB  00:00:00     
-----------------------------------------------------------------------------------------------------------------------------------
Total                                                                                              295 kB/s |  87 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : zlib-devel-1.2.7-17.el7.x86_64                                                                                  1/2 
  Installing : elfutils-libelf-devel-0.168-8.el7.x86_64                                                                        2/2 
  Verifying  : zlib-devel-1.2.7-17.el7.x86_64                                                                                  1/2 
  Verifying  : elfutils-libelf-devel-0.168-8.el7.x86_64                                                                        2/2 

Installed:
  elfutils-libelf-devel.x86_64 0:0.168-8.el7                                                                                       

Dependency Installed:
  zlib-devel.x86_64 0:1.2.7-17.el7                                                                                                 

Complete!

[root@localhost yum.repos.d]# yum install pdksh
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
No package pdksh available.
Error: Nothing to do
```

发现elfutils-libelf-devel包成功打上，但是pdksh包无法找到。 
我去centos的官网上搜索也没找到这个包。后察知这个pdksh包早已弃用，被ksh包代替。只要系统中包含了ksh包就可以忽略这个警告

检查ksh是否存在：

```shell
[root@localhost yum.repos.d]# yum install ksh
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package ksh-20120801-34.el7.x86_64 already installed and latest version
Nothing to do
```

点选check again，现在只剩一个警告了，选择忽略全部，下一步

![再次检查](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010151106713)

观看生成的简报，确认配置无误后，下一步

![生成简报](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010151359306)

弹出信息，确认默认实例的配置情况以及em连接方式，下一步

![确认](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010151658470)

此时弹出提示，需要打开新的窗口按指示执行sh语句

![执行两个脚本](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010151742327)

```shell
[root@localhost ~]# /home/oracle/app/oraInventory/orainstRoot.sh
Changing permissions of /home/oracle/app/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.

Changing groupname of /home/oracle/app/oraInventory to oinstall.
The execution of the script is complete.
[root@localhost ~]# /home/oracle/app/oracle/product/11.2.0/dbhome_1/root.sh 
Performing root user operation for Oracle 11g 

The following environment variables are set as:
    ORACLE_OWNER= oracle
    ORACLE_HOME=  /home/oracle/app/oracle/product/11.2.0/dbhome_1

Enter the full pathname of the local bin directory: [/usr/local/bin]: 
   Copying dbhome to /usr/local/bin ...
   Copying oraenv to /usr/local/bin ...
   Copying coraenv to /usr/local/bin ...


Creating /etc/oratab file...
Entries will be added to the /etc/oratab file as needed by
Database Configuration Assistant when a database is created
Finished running generic part of root script.
Now product-specific root actions will be performed.
Finished product-specific root actions.
```

完成后点击ok，开始正式安装，完成后画面如下图，关闭窗口

![完成安装](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20171010152022821)

## 配置环境变量

切换至oracle配置环境变量

```vim
[root@localhost ~]# su - oracle
Last login: Mon Oct  9 23:18:12 PDT 2017 on pts/3
[oracle@localhost ~]$ ls -a
.  ..  app  .bash_history  .bash_logout  .bash_profile  .bashrc  .cache  .config  .kshrc  .mozilla
[oracle@localhost ~]$ vim .bash_profile 

# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin
export PATH

export ORACLE_SID=orcl
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_1
export PATH=$ORACLE_HOME/bin:$PATH
[oracle@localhost ~]$ source .bash_profile
```

## 验证

使用oracle自带的交互工具连接数据库

```shell
[oracle@localhost ~]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.4.0 Production on Mon Oct 9 23:26:45 2017

Copyright (c) 1982, 2013, Oracle.  All rights reserved.


Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.4.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL> select status from v$instance;

STATUS
------------
OPEN
```

参考[Oracle 公共 yum 信息库](http://public-yum.oracle.com/) 
参考[Oracle 数据库11g文档](https://oracle-base.com/articles/11g/oracle-db-11gr2-installation-on-oracle-linux-7)