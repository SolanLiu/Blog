---
layout: post
#标题配置
title:  Win10重装系统
#时间配置
date:   2017-08-18 01:08:00 +0800
#大类配置
categories: document
#小类配置
tag: 教程
---

* content
{:toc}


# 1. 制作Win10启动U盘的准备工作
1. 下载好Win10系统镜像文件；
2. 准备一个容量在4GB以上的U盘或者移动硬盘；
3. 下载免费的USB启动盘制作工具：Rufus中文绿色版；

# 2. 制作Win10启动U盘
1. 将U盘连接到电脑，以管理员身份运行Rufus，确认软件的“设备”一项中选中的是U盘的盘符（图1处）；

![]({{'/styles/images/Win10/image1.PNG' | prepend: site.baseurl }})

2. 点击图2处的[光驱图标按钮]来选择下载好的Win10 ISO格式镜像文件；
3. 在图3处的[分区方案和目标系统类型]处通过下拉菜单会有三种类型可选（鼠标悬停会出现对应的说明），我们一般选择[用于BIOS或MBR计算机的UEFI-CSM分区方案]，这是适用于大多数传统BIOS和新型UEFI主板的方案。
4. 新卷标（图4处）可以随意填写制作好后的U盘名称，可以保持默认，也可以改一改（建议只用英文+数字）；
5. 其它选项保持与上图一样即可，点击图5处[开始]按钮即可进行制作了（点击后，软件将会清除U盘上所有的数据）；

![]({{'/styles/images/Win10/image2.PNG' | prepend: site.baseurl }})

6. 最后等待Rufus制作进度完成，制作完成后，打开资源管理器就可以看到我们的制作成果了！

![]({{'/styles/images/Win10/image3.PNG' | prepend: site.baseurl }})

# 3. 通过U盘安装Win10教程
1. 重启或开机时，一般会出现台式机主板品牌的LOGO（笔记本为电脑品牌的LOGO），这时快速按下[Delete]按键或F8、F10、F11或F12，不同品牌不一样，随后进入主板的BIOS设置；
2. 若BIOS界面是英文界面，就找到“Boot”相关的选项，若BIOS界面是中文界面，就找“启动”或“引导”相关的字眼，把启动项改成“USB-HDD”（每台电脑的选项名称不一样），然后保存并退出，重启电脑即可从USB启动；

![]({{'/styles/images/Win10/image4.PNG' | prepend: site.baseurl }})

3. 插上制作好得Win10安装盘并重启电脑，即会在启动过程中看到类似[Press any key to boot from CD/DVD...]或者[Start booting from USB device...]之类的语句，敲下键盘任意字母按键，即可进入Win10的安装过程，之后就跟着微软的安装界面一步步进行安装即可。

![]({{'/styles/images/Win10/image5.PNG' | prepend: site.baseurl }})