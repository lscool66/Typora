# vmware-install.pl安装VMware Tools



因为需要测试linux环境下shell调用mongodb的方法，所以装了一下虚拟机，系统是linux CentOS 7 64bit。

讲一下VMware Tools的安装顺序：

1.获取安装文件

VMTool的安装文件在VM虚拟机安装目录下的linux.iso中。

![img](https://raw.githubusercontent.com/lscool66/imgs/master/20180419165226755)

在虚拟机的光驱设置选项中，选中linux.iso：

![img](https://raw.githubusercontent.com/lscool66/imgs/master/20180419165326973)

![img](https://raw.githubusercontent.com/lscool66/imgs/master/20180419165339234)

之后就可以进入linux环境下，调用mount命令将cdrom（光驱）中的文件linux.iso解压到mnt文件夹下：

```shell
mount /dev/cdrom /mnt/
```

![img](https://raw.githubusercontent.com/lscool66/imgs/master/20180419170234612)

其中的VMwareTools*.tar.gz就是安装需要的文件了

建议先复制到/tmp底下，避免以后从虚拟光驱中提取文件时被覆盖掉。

解压后调用vmware-tools-distrib中的vmware-install.pl文件，如果执行时出现以下反馈：

![img](https://raw.githubusercontent.com/lscool66/imgs/master/20180419170710655)

说明执行pl类型文件的perl未安装，安装perl环境即可。

```shell
yum install perl*
```

之后调用vmware-install.pl文件，进入安装流程。

其中可能会出现多次选择输入yes or no，如果遇到

1.无法找到gcc的合法路径

安装gcc和g++编译器

```shell
yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake
```

2.无法找到generic kernel headers的合法路径

安装相关文件

```shell
yum -y update  
yum -y install kernel-headers kernel-devel gcc
```

之后reboot重启再重新调用vmtool安装程序即可

正常获取GCC和kernel header path：

![img](https://raw.githubusercontent.com/lscool66/imgs/master/20180420124150774)

之后就可以安装成功了（注：中间除了这两个选择no以外，其他询问是否的都选yes，默认地址的都回车确认即可）

另外关于文件分享功能：

![img](https://raw.githubusercontent.com/lscool66/imgs/master/20180420124606747)

在分享文件夹中设置对应的路径即可，linux下可以在/mnt/hgfs中获取到对应的文件。