# 远程桌面连接CentOS7

```
//安装CentOS桌面
yum upgrade
yum -y groupinstall "X Window System" 
yum -y groupinstall "GNOME Desktop"
startx
//安装xrdp
yum install epel* -y
yum --enablerepo=epel -y install xrdp
//添加防火墙
firewall-cmd --permanent --zone=public --add-port=3389/tcp
firewall-cmd --reload
//启动服务
systemctl start xrdp
systemctl enable xrdp
netstat -tnlp | grep xrdp
```

