# Kettle数据库资源库中文乱码问题

2019年05月27日 10:40:27 [JasonWangQB](https://me.csdn.net/weixin_44294381) 阅读数 35



> 转载：[kettle插入更新资源库转换文件时中文乱码解决方法](http://www.028888.net/archives/2018_03_1785.html)

1. 设置数据库字符集编码为UTF-8

2. 进入资源库连接配置页面
   ![在这里插入图片描述](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20190527103543563.png)
   ![资源库连接配置页面](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20190527103253909.png)

   点击选项，在选项处，增加如下值`characterEncoding:utf8`

   defaultFetchSize 500

   useCursorFetch true

   点击高级，在高级处的增加如下需要执行的sql语句`set names utf8;`
   ![在这里插入图片描述](https://raw.githubusercontent.com/lscool66/cloudimg/master/img/20190527103928589.png)