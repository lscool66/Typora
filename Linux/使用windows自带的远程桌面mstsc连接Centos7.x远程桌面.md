# 14_使用windows自带的远程桌面mstsc连接Centos7.x远程桌面

# 一、说明

这里，我希望用windows远程访问centos图形界面。xmanager连接centos远程桌面，有以下问题：

- 只能有一个用户同时使用xmanger连接远程桌面，多个用户同时登录不行。
- centos上，因为gnome硬件加速的原因，导致Xdmcp不可用，而基于xdmcp的xmanager也就无法使用了。

如果 **直接使用VNC**，配置又相对麻烦一些。而且还要在windows上安装一个RealVNC软件。**我们希望找到一个配置简单，连接方便的方案。** 所以，这里我使用了 **XRDP服务器**。

相关工具材料：

- 一台安装了centos系统的电脑（我的是centos7）。
- 一台安装了windows系统的电脑（我的是win7）。

# 二、安装配置XRDP

下面的很多操作需要root用户权限，所以，我们先切换为root用户：

```
sudo su - root
```

## 安装epel库

查询是否已经安装epel库:

```
rpm -qa|grep epel
```

如果 epel库 没有安装，则安装它：

```
yum install epel-release
```

## 安装xrdp

安装xrdp服务：

```
yum install xrdp
```

因为Xrdp最终会自动启用VNC，所以必须安装tigervnc-server，否则xrdp无法使用。安装vnc：

```
yum install tigervnc-server
```

为root用户设置VNC密码：

```
vncpasswd root
```

修改 **xrdp最大连接数**（使用默认值，不修改也是可以的） ：`vim /etc/xrdp/xrdp.ini`（默认是32）：

```
max_bpp=32
```

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-22102d3532506b84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/679/format/webp)

  xrdp最大连接数设置

## 关闭防火墙

这里，我们要确保两台机器可以ping通，能够相互访问。我这里是在局域网内测试，所以我直接关闭防火墙：

```
systemctl stop firewalld.service
```

设置开机不启动防火墙：

```
systemctl disable firewalld.servie
```

## 关闭SElinux

SElinux应该关闭它。**查看SElinux状态**：

```
sestatus 
```

如果是临时关闭SElinux：

```
setenforce 0
```

不过，我们要永久关闭SElinux：`vim /etc/selinux/config`

```
SELINUX=disabled
```

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-d30fe917f110013d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/643/format/webp)

  永久关闭SELINUX

## 启动XRDP

启动xrdp服务：

```
systemctl start xrdp
```

设置xrdp服务 **开机自启动** ：

```
systemctl enable xrdp
```

# 三、远程连接测试体验

下面，就是激动人心的时刻了。我们可以找到windows自带的远程桌面连接：**附件** -> **远程桌面连接**（或者打开 **运行** ，输入mstsc）

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-9a4043f069ce3456.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/427/format/webp)

  远程桌面连接

然后就打开了 **远程桌面连接** 这个软件，然后输入你想连接的 **centos电脑的ip地址**，选择centos上已有的一个 **用户名**：

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-4e028da861ac6394.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/411/format/webp)

  图片.png

然后输入 **vnc密码：**

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-fe8894b544c5ffd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

  vnc密码

这时，就看到了远程桌面了，这个界面和物理主机上看到的一样：

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-205fc07b4257a3f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

  远程桌面

不过你会发现，本地主机win7和远程centos之间，**不能进行粘贴复制**。这是mstsc功能不足导致的，后面使用MobaXterm连接可以解决这个问题。

# 四、其它连接方式

## 使用MobaXterm连接

MobaXterm 这个软件，在这里 **相当于** win7自带的 **远程桌面软件** mstsc 。使用MobaXterm替代mstsc的好处是，可以进行 **粘贴复制** 操作。也就是win7复制，直接可以粘贴到Centos上，或者Centos复制直接粘贴到win7上。

打开 **Session** -> **RDP** ，输入将要远程操控的主机IP，以及可用的用户，端口默认是**3389** ：

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-3b59c0fafbb6ffb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

  打开MobaXterm

点击 **OK**，接下来输入密码登录即可。在点击全屏显示时，如果你希望 **高清全屏显示**，MobaXterm连接前，**选中一个会话右键，编辑会话（edit session） -> 高级设置（advanced） -> 显示（display）** 设置合理的分辨率。一般是 `1920x1080` ，根据远程桌面的分辨率而定。

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-427794db2b86867f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

  高清显示设置

# 五、注销操作

如果直接关闭MobaXterm，远程桌面还是没有注销的，用户还在 **占用Centos资源**。所以，当你 **不用了的时候**，记得 **进行注销操作**，以减少远程主机的开销：

- 

  ![img](https://upload-images.jianshu.io/upload_images/9181524-3d6fd7dfdb140bd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/622/format/webp)

  注销

# 五、参考文章

- [《Windows10远程连接CentOS7（搭建Xrdp服务器）》](https://jingyan.baidu.com/article/a3aad71a12e5b6b1fb009693.html)

