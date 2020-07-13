# 通过Xmanager远程安装Oracle11.2到Linux

环境：Xmanager4.0   CentOS6(2核 2G)   oracle11.2

参考文章：https://www.linuxidc.com/Linux/2017-04/142470.htm

https://www.linuxidc.com/Linux/2017-08/146528.htm

https://www.cnblogs.com/songyuejie/p/6372534.html

https://blog.csdn.net/haibusuanyun/article/details/16336593

https://blog.csdn.net/mchdba/article/details/73322561

看了网上很多文章，总是有这样那样的小问题；自己也是第二次装了，但是时间太长，还是要百度，小问题也多，索性自己整理一篇，省的以后东找西找。



安装环境设置：

一、 安装必要的软件包：

yum -y install binutils compat-libstdc++-33 compat-libstdc++-33.i686 elfutils-libelf elfutils-libelf-devel gcc gcc-c++ glibc glibc.i686 glibc-common glibc-devel glibc-devel.i686 glibc-headers ksh libaio libaio.i686 libaio-devel libaio-devel.i686 libgcc libgcc.i686 libstdc++ libstdc++.i686 libstdc++-devel make sysstat  ld-linux.so.2  unixODBC unixODBC-devel

yum install libXp  libXp.i686    //否则会报java Exception

二、添加用户组：

groupadd oinstall  
groupadd dba
useradd -g oinstall -G dba oracle
passwd oracle

三、创建安装目录：

mkdir -p /usr/local/oracle/product/12.1.0/db_1  
chown -R oracle:oinstall /usr/local/oracle 
chmod -R 775 /usr/local/oracle



用root帐户在/etc下建立文件oraInst.loc,写入:

inventory_loc=/usr/local/oraInventory
inst_group=oinstall

赋予目录权限

setfacl -m user:oracle:rwx /usr/local/oraInventory/

查看目录权限

getfacl /usr/local/oraInventory/

四、修改内核参数：

vim /etc/sysctl.conf

fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 2097152
kernel.shmmax = 2147483648
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576

保存后sysctl -p #立即生效设置的参数

五、修改用户设置:
	1. 限制选项
	vim /etc/security/limits.conf
	oracle          soft    nproc  2047
	oracle          hard    nproc  16384
	oracle          soft    nofile  1024
	oracle          hard    nofile  65536

	2. 验证选项
	vim /etc/pam.d/login
	session required /lib64/security/pam_limits.so
	session required pam_limits.so
	
	3. 配置文件
	vim /etc/profile	
	if [ $USER = "oracle" ]; then
			if [ $SHELL = "/bin/ksh" ]; then
				  ulimit -p 16384
				  ulimit -n 65536
			else
				  ulimit -u 16384 -n 65536
			fi
	fi	
	------------------------------------
	vim  /home/oracle/.bash_profile
	export ORACLE_BASE=/usr/local/oracle	
	export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1
	    export ORACLE_SID=orcl
	export ORACLE_TERM=xterm
	export PATH=$ORACLE_HOME/bin:/usr/sbin:$PATH
	    export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
	    export LANG=C
	    export NLS_LANG=AMERICAN_AMERICA.AL32UTF8
	------------------------------------------
	
	另外如果centos中有openjdk要删除：yum remove "openjdk"

六、安装Xmanager4并设置

在window上安装Xmanage4（具体使用可以看上面参考连接，也可自己百度），

在liunx上如果没有安装图形界面，则需要先安装。

yum groupinstall "X Window System"

yum groupinstall Desktop #好像不用安装也可以

yum install xterm

yum install xclock #测试用，好像可以不用安装



安装oracle

用oracle用户登陆linux

下载并解压oracle数据库（linux.x64_11gR2_database）到用户目录，

在Windows上启动Xstart





进入到解压好的安装包目录/home/oracle/database，运行 runInstaller开始安装Oracle

（如果在虚拟机上安装了图形界面的话可以在linux上自己操作，不用在Xmanager上折腾）





运行runInstaller命令后，就跟在Linux机或window上打开应用一样使用了。

不过我这个Xmanager好像版本太老了，有点问题，弹出的图形窗口鼠标不好使，只能用键盘（tab、alt+英文字母、回车）。

















下面这里自己用都选dba了。





上图安装检查，按照提示信息一个一个解决。有些系统报错是因为现有的包的版本比检测要高，最后忽略即可。（点击Check_Again 多检查几次。
Swap size问题：
安装过程中可以忽略这个错误，自己的测试环境，忽略没啥影响，安装完再加也可以的，生产环境就要处理。

增加SWAP交换分区有两种方法：
1. 使用dd命令新建一个文件并挂载为SWAP
2. 增加一个新交换分区（略）
  #########################################################
  1.使用DD命令创建一个文件
  [root@bys3 ~]# dd if=/dev/zero of=/root/swap bs=1024 count=1024000   
  ---这里bs=是bytes,count是 blocks 个块，这里的就是1024bytes=1K，1024000K=1000M
  [root@bys3 ~]# mkswap /root/swap
  [root@bys3 ~]# swapon /root/swap
  [root@bys3 ~]# free -m
  -- 查看是否增加成功
  设置开机自动挂载-
  [root@bys3 ~]# vi /etc/fstab 
  #添加下面行
  /root/swap              swap                    swap    defaults        0 0
  [root@bys3 ~]# mount -a    --无报错，修改/etc/fstab成功
  重启后查看：--SWAP空间包含了新增加的空间。安装完再验证。
  [oracle@bys3 ~]$ free -m 
  ###############################################################################















下面要配置监听器

















创建数据库：













上图，数据库自用，使用密码都配成一样了，

























创建数据库的这个过程花的时间是最多的。

到这里数据库安装完成。

设置Oracle数据库开机启动

vim /etc/oratab





vim /etc/rc.d/rc.local



su - oracle -c "/usr/local/oracle/product/11.2.0/db_1/bin/dbstart"

su - oracle -c "/usr/local/oracle/product/11.2.0/db_1/bin/lsnrctl start"

重新启动linux

测试Oracle数据库自动启动成功！
--------------------- 
版权声明：本文为CSDN博主「要懂得舍得」的原创文章，遵循CC 4.0 by-sa版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/zyw23zyw23/article/details/80471007