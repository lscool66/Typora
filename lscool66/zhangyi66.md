[TOC]





# 1、word 批量添加图片

```vbscript
alt+f11
f5
输入
Sub PicWithCaption()
    Dim xFileDialog As FileDialog
    Dim xPath, xFile As Variant
    On Error Resume Next
    Set xFileDialog = Application.FileDialog(msoFileDialogFolderPicker)
    If xFileDialog.Show = -1 Then
        xPath = xFileDialog.SelectedItems.Item(1)
        If xPath <> "" Then
            xFile = Dir(xPath & "\*.*")
            Do While xFile <> ""
                If UCase(Right(xFile, 3)) = "PNG" Or _
                    UCase(Right(xFile, 3)) = "TIF" Or _
                    UCase(Right(xFile, 3)) = "JPG" Or _
                    UCase(Right(xFile, 3)) = "GIF" Or _
                    UCase(Right(xFile, 3)) = "BMP" Then
                    With Selection
                        .InlineShapes.AddPicture xPath & "\" & xFile, False, True
                        .InsertAfter vbCrLf
                        .MoveDown wdLine
                        .Text = xPath & "\" & xFile & Chr(10)
                        .MoveDown wdLine
                    End With
                End If
                xFile = Dir()
            Loop
        End If
    End If
End Sub
```

# 2、Oracle导入导出数据

```bat
exp  账号/密码@IP/实例名  file=D:\文件名.dmp owner=(账号)
imp  账号/密码@IP/实例名 file=D:\文件名.dmp full=y
exp sxgl/!QAZ2wsx@192.168.131.7/orcl file=sxgl.dmp owner=(sxgl)
imp sxgl/!QAZ2wsx@192.168.131.31/orcl file=sxgl.dmp full=y
如遇到
IMP-00017: 由于 ORACLE 错误 959, 以下语句失败:
IMP-00003: 遇到 ORACLE 错误 959
ORA-00959: 表空间 'APPROVE' 不存在
错误创建对应的表空间重新导入即可
如果用system用户导出的数据需要指定来（tysp_1）去用户(tysp)
exp system/123@192.168.131.7/orcl file=sxgl.dmp owner=(sxgl)
imp tysp/!QAZ2wsx@192.168.131.7/orcl fromuser=tysp_1 touser=tysp file=C:\Users\zhang yi\Downloads\Compressed\tysp_1.20190902225959.dmp
```

# 3、网络相关命令

```bat
tracert 192.168.88.1
E:\tools>arp-ping.exe 192.168.2.235
Reply that 38:91:D5:E4:47:3C is 192.168.2.235 in 0.904ms
Reply that 38:91:D5:E4:47:3C is 192.168.2.235 in 1.030ms
Reply that 38:91:D5:E4:47:3C is 192.168.2.235 in 1.056ms
Reply that 38:91:D5:E4:47:3C is 192.168.2.235 in 1.048ms

Ping statistics for 192.168.2.235/arp
     4 probes sent.
     4 successful, 0 failed.
Approximate trip times in milli-seconds:
     Minimum = 0.904ms, Maximum = 1.056ms, Average = 1.010ms
```

# 4、linux 学习

```shell
netstat -tnlp | grep java
firewall-cmd --permanent --zone=public --add-port=3389/tcp
firewall-cmd --reload
firewall-cmd --zone=public --list-ports
localectl set-locale LANG=zh_CN.utf8
localectl set-locale LANG=en_US.utf8
locale
locale -a
rpm -qa |grep createrepo
chkconfig sshd on
chkconfig --list sshd
systemctl enable sshd
systemctl start mysqld.service
yum repolist enabled | grep "mysql.*-community.*"
systemctl status mysqld.service
systemctl status firewalld
ps -ef|grep LISTENER
lsnrcl start

yum install cockpit
systemctl enable cockpit.socket
systemctl start cockpit
vi /etc/fstab
vi /etc/grub2.cfg
conn system/123@orcl as sysdba
yum -y install 包名（支持*） ：自动选择y，全自动
yum install 包名（支持*） ：手动选择y or n
yum remove 包名（不支持*）
rpm -ivh 包名（支持*）：安装rpm包
rpm -e 包名（不支持*）：卸载rpm包
yum install oracle-rdbms-server-11gR2-preinstall.x86_64
yum install oracle-rdbms-server-11gR2-preinstall -y
yum remove oracle-rdbms-server-11gR2-preinstall.x86_64
export LANG=en_US.utf8
yum install yum-plugin-downloadonly
yum install --downloadonly --downloaddir=./ oracle-rdbms-server-11gR2-preinstall
yum install yum-utils
yumdownloader --resolve --destdir=/root/mypackages/ httpd
rpm -ivh *.rpm
rpm -Uvh update/*.rpm
lsblk
服务器名称建议host+服务器ip的方式命名，如host-172-20-20-1，修改服务器名称命令hostnamectl set-hostname 主机名
所有服务器关闭selinux，执行命令：setenforce 0，并修改配置文件/etc/selinux/config：SELINUX=disabled
ulimit -a


```

# 5、Linux设置Java环境变量

```properties
set java environment
JAVA_HOME=/usr/java/jdk1.8.0_211
JRE_HOME=/usr/java/jdk1.8.0_211/jre            
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```

# 6、VMware序列号

```
VV502-47Z16-M85AQ-Q7PX9-QLA8D
```

# 7、teamviewer

```
192.168.2.252
953 300 888
13303683212
自助机一楼
1044539311
自助机二楼
1036171058
1036171058
```

# 8、桦南

```
通用审批218.10.17.5:8081/Inspur.Dzzw.WebApproval
网办后台218.10.17.5:8082
并联审批218.10.17.5:8083/Inspur.Dzzw.WebParallel
网盘http://218.10.17.6:8084/WebDiskServerDemo/doc?doc_id=
事项管理http://218.10.17.5:8088/Inspur.Ecgap.PowerManager
大厅管理http://218.10.17.4:8084
谷歌地图：46.2628064308,130.5625880590
百度地图：46.2691787425,130.5689247947
腾讯高德：46.2628000000,130.5625800000
图吧地图：46.2673255900,130.5454183400
谷歌地球：46.2608555900,130.5551583400
北纬N46°15′39.08″ 东经E130°33′18.57″
桦南县 230822000000
版本管理账号
hljsjmsshuananxian
123456

明义乡 明义乡受理部门
梨树乡 梨树乡受理部门
金沙乡 受理部门
闫家镇 
孟家岗镇
转自 10690067777776：
尊敬的桦南县政务服务中心您好：您的账号已开户成功。企业编号：250424，用户名：admin0，初始密码：hn_admin123；登录网址：hl.ums86.com，登录后建议您先进行短信发送测试后再进行使用；【产品演示专用】

```

# 9、mysql

```shell
mysqldump -uroot -proot123 dv_db_wf1> dv_db_wf1.sql
mysqldump -uroot -p!QAZ2wsx dv_db_wf > dv_db_wf.sql
update user set password=password("root123") where user="root";
mysqldump -uroot -p123 -h 192.168.131.31 dv_db_om > dv_db_om.sql 2>nul
```

# 10、inspur.dzzw

```
1、新添加配置项 app.bpm.workflow.url.out 配置值为外网的流程图展示连接地址
2、新添加配置项 app.bpm.workflowimage.url.out 配置值为外网流程图标示已办未办环节地址
3、新添加配置项 app.bpm.url.out 配置值为外网单点链接的bpm设计页面地址
4、approve.outAccessIp配置值为外网审批路径地址

一、approve.download.outUrl将这个配置项去掉（配置这个只访问外网，内网不能）
二、approve.outAccessIp配置成外网访问地址（审批）
三、approve.download.OutRootUrl将这个配置成外网的网盘地址
四、approve.download.url 这个配置成内网的网盘地址

http://192.168.1.5:8083/web/parallel/updateNewItemIdByOldItemId

```

