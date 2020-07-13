# CentOS7（linux）下安装Oracle 11gR2 出现的问题及解决方案

2019年03月18日 22:32:45 [qq_29964641](https://me.csdn.net/qq_29964641) 阅读数 59



## 1.运行 ./runInstaller 出现256颜色 不通过问题

 ![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827204051960?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjIzNDQ1Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 解决方案：  切换到root，输入命令xhost +  运行后如下图可切换到oracle安装

![img](https://img-blog.csdnimg.cn/2019021515300976.png)

## 2. 运行 ./runInstaller 安装界面出现乱码问题

解决方案： 
export NLS_LANG=AMERICAN_AMERICA.UTF8 
export LC_ALL=C

执行命令source /home/oracle/.bash_profile，让配置立即生效

## 3.运行 ./runInstaller 出现小白点小白条界面无法显示完全问题以及画面加载卡住

解决方案： 
运行安装程序时使用 ./runInstaller -jreLoc /usr/lib/jvm/jre-1.8.0

 

# 4、一系列包问题

```
安装过程中，在link binaries阶段出现2个错误，第一个是关于ins_ctx.mk，log显示：

/lib64/libstdc++.so.5: undefined reference to `memcpy@GLIBC_2.14'

原因据说是由于本机的glibc版本高于2.14（实际为2.17）。解决方法：

yum install glibc-static

该软件包包含一个静态链接库：/usr/lib64/libc.a

修改/home/oracle/app/oracle/product/11.2.0/dbhome_1/ctx/lib/ins_ctx.mk，将

ctxhx: $(CTXHXOBJ)
       $(LINK_CTXHX) $(CTXHXOBJ) $(INSO_LINK)

修改为：

ctxhx: $(CTXHXOBJ)
       -static $(LINK_CTXHX) $(CTXHXOBJ) $(INSO_LINK) /usr/lib64/stdc.a

点击Retry即可。

第二个错误是”Error in invoking target 'agent nmhs' of makefile'/app/oracle/product/11.2.0/db_1/sysman/lib/ins_emagent.mk.' 

解决方法，在makefile中添加链接libnnz11库的参数：

修改/home/oracle/app/oracle/product/11.2.0/dbhome_1/sysman/lib/ins_emagent.mk，将

$(MK_EMAGENT_NMECTL)

修改为：

$(MK_EMAGENT_NMECTL) -lnnz11

点击Retry即可。
```




# 5.创建监听的时候（netca）点击Next 没有反应

解决方案： 
vi /etc/hosts 增加自己的ip和主机名

![img](https://img-blog.csdn.net/20170827232944460?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjIzNDQ1Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)