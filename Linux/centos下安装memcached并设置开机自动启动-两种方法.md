## [centos下安装memcached并设置开机自动启动-两种方法](https://www.cnblogs.com/walter371/p/5047201.html)

方法一：

 

安装memcached
yum install memcached

启动服务并初始化
service memcached start -p 11211 -l 127.0.0.1 -d

设置memcached 开机自动启动
\#chkconfig memcached on

 

方法二：

1、先下载memcached 和libevent。

​      libevent 最新的稳定版: wget http://monkey.org/~provos/libevent-1.4.14b-stable.tar.gz

​     引用网页：<http://www.monkey.org/~provos/libevent/> 

​       memcached 最新的稳定版：wget  http://memcached.googlecode.com/files/memcached-1.4.5.tar.gz

   引用网页:<http://code.google.com/p/memcached/downloads/list>

2、   安装libevent

tar zxvf libevent-1.4.14b-stable.tar.gz

cd libevent-1.4.14b-stable

./configure --prefix=/usr/local/libevent/

make

make install

cd ..

2、安装memcached 

tar zxvf  memcached-1.4.5.tar.gz

cd memcached-1.4.5

./configure --prefix=/usr/local/memcached --with-libevent=/usr/local/libevent/

make 

make install

ln -s /usr/local/libevent/lib/libevent-1.4.so.2 /lib/libevent-1.4.so.2

3、启动 memcached

启动参数说明：

-d 选项是启动一个守护进程，

-m 是分配给Memcache使用的内存数量，单位是MB，默认64MB 

-M return error on memory exhausted (rather than removing items)

-u 是运行Memcache的用户，如果当前为root 的话，需要使用此参数指定用户。

-l 是监听的服务器IP地址，默认为所有网卡。

-p 是设置Memcache的TCP监听的端口，最好是1024以上的端口

-c 选项是最大运行的并发连接数，默认是1024

-P 是设置保存Memcache的pid文件 

-f chunk size growth factor (default: 1.25) 

-I Override the size of each slab page. Adjusts max item size(1.4.2版本新增)

 

也可以启动多个守护进程，但是端口不能重复 

/usr/local/memcached/bin/memcached -d -m 64 -u root -l 127.0.0.1 -p 11211 -c 128 -P /tmp/memcached.pid

3、设置开机自动启动

vi /etc/rc.d/rc.local

然后在最后增加一句

/usr/local/memcached/bin/memcached -d -m 64 -u root -l 127.0.0.1 -p 11211 -c 128 -P /tmp/memcached.pid

 

停止memcached 服务  kill -9 `cat /tmp/memcached.pid`

ok

 

经过测试可以用

 

 

启动memcached

引用

\# /usr/local/bin/memcached -d -m 2048  -u root -l 192.168.1.20 -p 12111 -c 1024 -P /tmp/memcached.pid 

参数说明： 

-d	启动为守护进程 

-m <num>	分配给Memcached使用的内存数量，单位是MB，默认为64MB 

-u <username>	运行Memcached的用户，仅当作为root运行时 

-l <ip_addr>	监听的服务器IP地址，默认为环境变量INDRR_ANY的值 

-p <num>	设置Memcached监听的端口，最好是1024以上的端口 

-c <num>	设置最大并发连接数，默认为1024 

-P <file>	设置保存Memcached的pid文件，与-d选择同时使用 

 



分类: [linux](https://www.cnblogs.com/walter371/category/612157.html), [php](https://www.cnblogs.com/walter371/category/612158.html)

标签: [memcached](https://www.cnblogs.com/walter371/tag/memcached/), [centos](https://www.cnblogs.com/walter371/tag/centos/), [linux](https://www.cnblogs.com/walter371/tag/linux/), [php](https://www.cnblogs.com/walter371/tag/php/)