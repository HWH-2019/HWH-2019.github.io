---
title: 【Linux系列】VMware虚拟机下安装Ubuntu
author: HWH
tags:
  - Linux
  - Virtual Machine
  - Ubuntu
  - VMware
categories:
  - - Tutorial
abbrlink: a2e8e6df
date: 2022-12-03 20:06:04
---

### 安装环境
- 本机操作系统：`Windows10/11`
- 虚拟机： `VMware workstation pro 16`
- 虚拟机操作系统： `ubuntu-20.04.3-desktop-amd64`


### 安装准备
- [安装VMware虚拟机并创建虚拟机](https://blog.csdn.net/h2763246823/article/details/122522464)
- [下载Ubuntu操作系统镜像](https://ubuntu.com/download/desktop)

### 加载镜像
- 点击 `Edit virtual machine settings` 进入虚拟机配置
- 点击 `CD/DVD`，选择下载Ubuntu镜像文件位置，如下图

![](https://img-blog.csdnimg.cn/bf05b4c75c1048e6b79462bb86a4ebf0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 确认后启动虚拟机

### 安装系统
- 虚拟机启动后将会先进行磁盘检查，然后进入操作系统安装引导界面。
![](https://img-blog.csdnimg.cn/bb865f3831c742a792d26b68165f5ea3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
注：下方出现提示 **切勿点击** `I Finished Installing`， **点击右上角叉**就行
- 进入选择语言，默认为 `English` ，这里选择`简体中文`，点击 `安装Ubuntu`
![](https://img-blog.csdnimg.cn/2490f180c6f14aae93413450b4ebe196.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
![](https://img-blog.csdnimg.cn/f13cb80fe2fb45f08e436fe57b6e0432.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
注：下方提示安装 VMware Tools，选择 `Remind Me Later`，点 `Install Tools` 可能会出现些未知的错误

- 进入选择键盘布局界面，默认即可，选择继续
![](https://img-blog.csdnimg.cn/414a77e25d6d4e43a07f0d03e81f4ced.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
注：从这开始时常会出现，下方下一步的选项在界面外部，鼠标无法点击到，这里提供一个方法，按住 `F7`，然后再用鼠标拖动引导界面的窗口就可将下面的内容移动上来(游戏本可能需要按`Fn`)
![](https://img-blog.csdnimg.cn/4324f521b02a415f89b7d65776fcca21.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
- 进入更新和其他软件界面，这里默认，选择继续
![](https://img-blog.csdnimg.cn/1c87de7f37cc4935bd5b271d3cb60c0f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
- 进安装类型，这里默认，选择现在安装
![](https://img-blog.csdnimg.cn/a9fa9c2e66e34834862fec885b27d3de.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
- 提示格式化磁盘，点击继续
![](https://img-blog.csdnimg.cn/a581ae2fc2eb4c039190a7e7961bb83b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
- 进入选择时区，这里选择 `Shanghai`，点击继续
![](https://img-blog.csdnimg.cn/0aa1be7418844156ae059c3cb37d1332.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
- 进入设置用户名，密码，填写后，点击继续
![](https://img-blog.csdnimg.cn/be442e5c93464ee39f83f381b3d7f382.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
- 安装引导结束，开始安装，大约等待时间为半小时后即可安装成功，现在可以泡杯茶去走一圈。

- 安装完成后重启

### 总结
- 按照上述流程一般能正常安装 Ubuntu，但有时候会出现一些问题(解决办法是自己总结的，如果有好的思路可以在评论区评论哦，会及时更新)
- **【问题1】进入 Network boot**

![](https://img-blog.csdnimg.cn/41e674bf72134cba81f04748b1b9d975.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

**解决办法：**
1. 重启，是不是进入的时候按了 `F12`
2.  重头来过

- **【问题2】虚拟机内桌面很小**
**解决办法：** [安装 VMware Tools]()
