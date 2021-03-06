---
layout: post
#标题配置
title:  Distribute
#时间配置
date:   2019-01-28 01:08:00 +0800
#大类配置
categories: notes
#小类配置
tag: Computer Network
---

* content
{:toc}



# 分布式系统

- 分布式系统

&emsp;&emsp;分布式系统（distributed system）是建立在网络之上的软件系统。正是因为软件的特性，所以分布式系统具有高度的内聚性和透明性。因此，网络和分布式系统之间的区别更多的在于高层软件（特别是操作系统），而不是硬件。内聚性是指每一个数据库分布节点高度自治，有本地的数据库管理系统。透明性是指每一个数据库分布节点对用户的应用来说都是透明的，看不出是本地还是远程。在分布式数据库系统中，用户感觉不到数据是分布的，即用户不须知道关系是否分割、有无副本、数据存于哪个站点以及事务在哪个站点上执行等。  
&emsp;&emsp;在一个分布式系统中，一组独立的计算机展现给用户的是一个统一的整体，就好像是一个系统似的。系统拥有多种通用的物理和逻辑资源，可以动态的分配任务，分散的物理和逻辑资源通过计算机网络实现信息交换。系统中存在一个以全局的方式管理计算机资源的分布式操作系统。通常，对用户来说，分布式系统只有一个模型或范型。在操作系统之上有一层软件中间件（middleware）负责实现这个模型。一个著名的分布式系统的例子是万维网（World Wide Web），在万维网中，所有的一切看起来就好像是一个文档（Web页面）一样。  
&emsp;&emsp;在计算机网络中，这种统一性、模型以及其中的软件都不存在。用户看到的是实际的机器，计算机网络并没有使这些机器看起来是统一的。如果这些机器有不同的硬件或者不同的操作系统，那么，这些差异对于用户来说都是完全可见的。如果一个用户希望在一台远程机器上运行一个程序，那么，他必须登陆到远程机器上，然后在那台机器上运行该程序。
分布式系统和计算机网络系统的共同点是：多数分布式系统是建立在计算机网络之上的，所以分布式系统与计算机网络在物理结构上是基本相同的。
他们的区别在于：分布式操作系统的设计思想和网络操作系统是不同的，这决定了他们在结构、工作方式和功能上也不同。网络操作系统要求网络用户在使用网络资源时首先必须了解网络资源，网络用户必须知道网络中各个计算机的功能与配置、软件资源、网络文件结构等情况，在网络中如果用户要读一个共享文件时，用户必须知道这个文件放在哪一台计算机的哪一个目录下；分布式操作系统是以全局方式管理系统资源的，它可以为用户任意调度网络资源，并且调度过程是“透明”的。当用户提交一个作业时，分布式操作系统能够根据需要在系统中选择最合适的处理器，将用户的作业提交到该处理程序，在处理器完成作业后，将结果传给用户。在这个过程中，用户并不会意识到有多个处理器的存在，这个系统就像是一个处理器一样。  

- 分布式软件系统

&emsp;&emsp;分布式软件系统(Distributed Software Systems)是支持分布式处理的软件系统,是在由通信网络互联的多处理机体系结构上执行任务的系统。它包括分布式操作系统、分布式程序设计语言及其编译(解释)系统、分布式文件系统和分布式数据库系统等。
分布式操作系统:负责管理分布式处理系统资源和控制分布式程序运行。它和集中式操作系统的区别在于资源管理、进程通信和系统结构等方面。
分布式程序设计语言:用于编写运行于分布式计算机系统上的分布式程序。一个分布式程序由若干个可以独立执行的程序模块组成，它们分布于一个分布式处理系统的多台计算机上被同时执行。它与集中式的程序设计语言相比有三个特点：分布性、通信性和稳健性。
分布式文件系统:具有执行远程文件存取的能力,并以透明方式对分布在网络上的文件进行管理和存取。
分布式数据库系统:由分布于多个计算机结点上的若干个数据库系统组成,它提供有效的存取手段来操纵这些结点上的子数据库。分布式数据库在使用上可视为一个完整的数据库,而实际上它是分布在地理分散的各个结点上。当然,分布在各个结点上的子数据库在逻辑上是相关的。
分布式邮件系统:分布式邮件系统的部署设计，即同一域名下，跨地域部署的邮件系统。适用 于在各地设有分部的政府机构或者大型集团，有效管理各地的人员结构，同时提高了邮件服务器应用效率。 
分布式邮件系统由多个数据中心组成，大量分支机构或较小的分散站点与数据中心的连接。分支机构需要建立自己的邮件服务器，来加快处理当地分支机构的邮件。承载相应的数据处理量。以提高邮件处理能力，邮件收发速度，邮件功能模块化。

- 固有的分布式应用

&emsp;&emsp;许多应用是固有分布式的。这些应用是突发模式（burstmode）而非批量模式（bulk mode）。这方面的实例有事务处理和Internet Javad,程序。
这些应用的性能取决于吞吐量（事务响应时间或每秒完成的事务数）而不是一般多处理机所用的执行时间。  
&emsp;&emsp;对于一组用户而言， 分布式系统有一个特别的应用称为计算机支持的协同工作（Computer Supported Cooperative Working，CSCW）或群件（groupware）， 支持用户协同工作。另一个应用是分布式会议， 即通过物理的分布式网络进行电子会议。同样，多媒体远程教学也是一个类似的应用。  
&emsp;&emsp;为了达到互操作性，用户需要一个标准的分布式计算环境，在这个环境里，所有系统和资源都可用。
DCE（分布式计算环境）是OSF（开放系统基金会）开发的分布式计算技术的工业标准集。它提供保护和控制对数据访问的安全服务、容易寻找分布式资源的名字服务、以及高度可伸缩的模型用于组织极为分散的用户、服务和数据。D C E可在所有主要的计算平台上运行， 并设计成支持异型硬件和软件环境下的分布式应用。  
&emsp;&emsp;DCE已经被包括TRANSVARL在内的一些r一商实现。TRANSVARL是最早的多厂商组（multi vendor team）的成员之一，它提出的建议已成为DCE体系结构的基础。在中可以找到利用DCE开发分布式应用的指南。  
&emsp;&emsp;一些其它标准基于一个特别的模型，比如CORBA（公用对象请求代理程序体系结构），它是由OMG （对象管理组）和多计算机厂商联盟开发的一个标准。CORBA使用面向对象模型实现分布式系统中的透明服务请求。  
&emsp;&emsp;工业界有自己的标准，比如微软的分布式构件对象模型（DCOM）和Sun Microsystem公司的Java Beans。
