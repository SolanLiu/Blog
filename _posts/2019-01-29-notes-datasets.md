---
layout: post
#标题配置
title:  Database Design
#时间配置
date:   2019-01-29 01:08:00 +0800
#大类配置
categories: notes
#小类配置
tag: Computer Network
---

* content
{:toc}

# 数据库设计

&emsp;&emsp;数据库设计就是根据业务系统的具体需要，结合我们所选用地DBMS(数据库管理系统)，为这个业务系统构造出最优的数据存储模型。并建立好数据库中的表结构及表与表之间的关联关系的过程。使之能有效的对应系统中的数据进行存储，并可以高效的对已经存储的数据进行访问。

![图像识别的主要过程]({{'/styles/images/database design/图1.png' | prepend: site.baseurl }})  

## 1. 需求分析

![图像识别的主要过程]({{'/styles/images/database design/图2.png' | prepend: site.baseurl }})

![图像识别的主要过程]({{'/styles/images/database design/图3.png' | prepend: site.baseurl }})

## 2. 逻辑设计

- **第一范式**

&emsp;&emsp;数据库表中的所有字段都是单一属性，不可再分的，这个单一属性是由基本的数据类型所构成的，即第一范式要求数据库中表都是二维表。  

- **第二范式**

&emsp;&emsp;数据库的表中不存在非关键字段对任一候选关键字段的部分函数依赖，即所有单关键字段的表都符合第二范式。

- **第三范式**

&emsp;&emsp;如果数据表中不存在非关键字段对任意候选关键字段的传递函数依赖则符合第三范式。

- **BC范式**

&emsp;&emsp;数据库表中如果不存在任何字段对任一候选关键字段的传递函数依赖则符合BC范式。如果是复合关键字，则复合关键字之间不存在函数依赖关系。


## 3. 物理设计

- **选择合适的数据库管理系统**

![图像识别的主要过程]({{'/styles/images/database design/图4.png' | prepend: site.baseurl }})

![图像识别的主要过程]({{'/styles/images/database design/图5.png' | prepend: site.baseurl }})

- **定义数据库、表及字段的命名规范**

- **选择合适的字段类型**

&emsp;&emsp;当一个列可以选择多种类型时，应该优先考虑数字类型，其次是日期或二进制类型，最后是字符类型，对于相同级别的数据类型，应该优先选择占用空间小的数据类型。

![图像识别的主要过程]({{'/styles/images/database design/图6.png' | prepend: site.baseurl }})

&emsp;&emsp;Char与varchar：如果列中要存储的数据长度差不多一致，则考虑char，否则应该考虑varcha；如果列中的最大数据长度小于50Byte，则考虑char，一般不宜定义大于50Byte的char类型列。  
&emsp;&emsp;decimal与float：非精确数据数据优先选择float。

- **反范式化设计（以空间换时间，提高效率，会导致数据冗余）**

&emsp;&emsp;减少表的关联数量，增加数据的读取效率。


## 4. 维护优化

- **维护数据字典**

&emsp;&emsp;使用第三方工具；利用数据库本身的备注字段（COMMENT）维护数据字典。

- **维护索引**

&emsp;&emsp;选择合适的列建立索引：出现在WHERE\GROUP BY\ORDER BY从句中的列；可选择性高的列放在索引前面；索引中不要包括太长的数据类型。

- **维护表结构**

&emsp;&emsp;使用在线变更表结构的工具；控制表的宽度和大小；对数据字典进行维护。

- **水平拆分和垂直拆分**

&emsp;&emsp;垂直拆分（控制表的宽度）：经常一起查询的列放到一起，text blob等大字段拆分出到附加表中。  
&emsp;&emsp;水平拆分（控制表的大小）：对主键值进行Hash操作。