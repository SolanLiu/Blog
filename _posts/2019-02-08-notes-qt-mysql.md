---
layout: post
#标题配置
title:  Qt+MySQL
#时间配置
date:   2019-02-08 01:08:00 +0800
#大类配置
categories: notes
#小类配置
tag: Qt
---

* content
{:toc}




![图像识别的主要过程]({{'/styles/images/qt/图1.png' | prepend: site.baseurl }})

&emsp;&emsp;驱动层为具体的数据库和SQL接口层之间提供了底层的桥梁；SQL接口层提供了对数据库的访问，其中，QSqlDatabase类用来创建连接，QSqlQuery类可以使用SQL语句实现与数据库交互；用户接口层实现了将数据库的数据链接到窗口部件上。  
## 1. 连接数据库
### 1.1 SQL数据库驱动

![图像识别的主要过程]({{'/styles/images/qt/图2.png' | prepend: site.baseurl }})
 
## 2. 创建数据库连接
&emsp;&emsp;QSqlDatabase db = QSqlDatabae::addDatabase(“QMYSQL”);  ---创建一个连接对象，默认连接  
&emsp;&emsp;db.setHostName(“bigblue”);  ---设置主机名  
&emsp;&emsp;db.setDatabaseName(“flightdb”);  ---设置数据库名  
&emsp;&emsp;db.setUserName(“acarlson”);  ---设置用户名  
&emsp;&emsp;db.setPassword(“luTbSbAs”);  ---设置密码  
&emsp;&emsp;bool ok = db.open();  ---打开该连接以便使用  
&emsp;&emsp;QSqlDatabase first= QSqlDatabae::addDatabase(“QMYSQL”);  ---创建一个连接对象，默认连接  
&emsp;&emsp;QSqlDatabase db = QSqlDatabae::addDatabase(“QMYSQL”);  ---创建一个连接对象，默认连接  

**1. 传入MYSQL的图片背景出现空白**   

&emsp;&emsp;在MYSQL中，max\_allowed\_packet的默认值为1M，所以，如果往Mysql数据库导入超过1M的文本文件或图片文件时，插入和更新会被max\_allowed\_packet参数限制而导入失败。
  
**2. 将图片存入数据库的方法**  

-  一般的网站产品数据存放在数据库中，商品图片是上传到文件服务器，然后通过http服务器浏览商品图片。eg:电商网站  
-  将图片放入数据库中存放在BLOB的方法可以解决脏数据问题（删除数据库中的数据不能将图片删除），但图片不能太大，数量不多，访问量不大。eg：公安的身份证系统。
MySQL中有四种BLOB类型，TinyBlob(最大255Byte)， Blob(最大65K)， MediunBlob(16M)， LongBlob(最大4G)。  
-  删除数据的时候去检查图片如果存在先删除图片，再删除数据的方法，这种方案也非完美解决方案，存在图片先被删除，程序出错SQL没有运行，或者反之。  
-  使删除图片成为事物处理中的一个环节。  

&emsp;&emsp;开发了三个UDF供事务处理，可穿插使用在BEGIN\COMMIT\ROLLBACK中  
&emsp;&emsp;image\_check(filename) --检查图片是否存在.   
&emsp;&emsp;image\_remove(filename) --删除图片.   
&emsp;&emsp;image\_rename(oldfile,newfile) --更改图片文件名.   
&emsp;&emsp;image\_md5sum(filename) --md5sum 主要用户图片是否被更改过.  
&emsp;&emsp;开发UDF你需要安装下面的软件包：sudo apt-get install pkg-config  
&emsp;&emsp;sudo apt-get install libmysqlclient-dev  
&emsp;&emsp;sudo apt-get install gcc gcc-c++ make automake autoconf  
&emsp;&emsp;编译udf，最后将so文件复制到 /usr/lib/mysql/plugin/  
&emsp;&emsp;./configure --prefix=/srv/mysql --with-mysql=/usr/bin/mysql\_config  
&emsp;&emsp;*装载*  
&emsp;&emsp;create function image\_check returns boolean soname 'images.so';  
&emsp;&emsp;create function image\_remove returns boolean soname 'images.so';  
&emsp;&emsp;create function image\_rename returns boolean soname 'images.so';  
&emsp;&emsp;create function image\_md5sum returns string soname 'images.so';  
&emsp;&emsp;*卸载*   
&emsp;&emsp;drop function image\_check;  
&emsp;&emsp;drop function image\_remove;   
&emsp;&emsp;drop function image\_rename;    
&emsp;&emsp;drop function image\_md5sum;
    
- 触发器  

&emsp;&emsp;insert触发器的任务： 插入记录的时候通过image_check检查图片是否正常上传，如果非没有上传，数据插入失败。如果上传成功再做image_md5sum 进行校验100% 正确后插入记录  
&emsp;&emsp;delete触发器的任务： 检查删除记录的时候，首先去改图片文件名，然后删除该记录，最后删除图片，删除成功。如果中间环境失败，记录会rollback，图片会在次修改文件名改回来。