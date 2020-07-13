## [linux sshd 开机自启动](https://www.cnblogs.com/yubestman/p/4107734.html)

Redflag linux安装之后，要想通过ssh远程访问，需要手动执行 service sshd start，这样sshd服务才开启。 

通过chkconfig可以将sshd加入到系统服务中。 


  [root@localhost ~]# chkconfig sshd on   

   可以再查看sshd的运行级别状态： 


  [root@localhost ~]# chkconfig --list sshd 

​       sshd   0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭 


   以后重启之后，就可以直接通过ssh远程访问了。 



分类: [linux](https://www.cnblogs.com/yubestman/category/613699.html)

标签: [sshd](https://www.cnblogs.com/yubestman/tag/sshd/)