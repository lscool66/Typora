# [linux的zip和unzip用法](https://www.cnblogs.com/dyh-air/articles/9069642.html)



###### linux中提示没有unzip命令解决方法

如果你如法使用unzip命令解压.zip文件，可能是你没有安装unzip软件，下面是安装方法

命令： yum list | grep zip/unzip   #获取安装列表

安装命令： yum install zip    #提示输入时，请输入y；

安装命令：yum install unzip #提示输入时，请输入y；

 

###### unzip 和 unzip 解压文件到指定的目录

  Linux 常用的压缩命令有 gzip 和 zip，两种压缩包的结尾不同：zip 压缩的后文件是 *.zip ，而 gzip 压缩后的文件 *.gz 

相应的解压缩命令则是 gunzip 和 unzip 

gzip 命令： 

\# gzip test.txt 

它会将文件压缩为文件 test.txt.gz，原来的文件则没有了，解压缩也一样 

\# gunzip test.txt.gz 

它会将文件解压缩为文件 test.txt，原来的文件则没有了，为了保留原有的文件，我们可以加上 -c 选项并利用 linux 的重定向 

\# gzip -c test.txt > /root/test.gz 

这样不但可以将原有的文件保留，而且可以将压缩包放到任何目录中，解压缩也一样 

\# gunzip -c /root/test.gz > ./test.txt 

zip 命令： 

\# zip test.zip test.txt 

它会将 test.txt 文件压缩为 test.zip ，当然也可以指定压缩包的目录，例如 /root/test.zip 

\# unzip test.zip 

它会默认将文件解压到当前目录，如果要解压到指定目录，可以加上 -d 选项 

\# unzip test.zip -d /root/ 



分类 [linux](https://www.cnblogs.com/dyh-air/category/1220762.html)

标签 [linux](https://www.cnblogs.com/dyh-air/tag/linux/)