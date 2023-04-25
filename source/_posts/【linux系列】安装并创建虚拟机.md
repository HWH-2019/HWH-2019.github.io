---
title: 【linux系列】安装并创建虚拟机
author: HWH
tags:
  - Linux
  - Virtual Machine
categories:
  - - Tutorial
abbrlink: ee5717e8
date: 2022-12-03 20:02:01
---


### 安装准备
- [下载 VMwave workstation pro 16](https://drive.google.com/file/d/1XZQPF-R-hmgBtO8CUCdq2WxjFL3ZxCrJ/view?usp=sharing)

### 安装虚拟机
- 下载解压压缩包后双击运行 `VMware-workstation-full-16.1.0-17198959.exe` 文件。
- 按照安装引导程序安装，一般默认选项下一步即可。
- 遇到填写授权码时，将压缩包中的授权码填入确认即可。

注：安装虚拟机的过程比较简单，在此不再赘述，需要注意的是安装路径**不要有中文**，（这点得养成习惯，安装软件路径一般不包含中文）

### 创建虚拟机
- 安装好虚拟机后启动虚拟机，如下图

![](https://img-blog.csdnimg.cn/ce703040c2f74af49064ce6244e3bdfe.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 点击 `Create a New Virtual Machine` 开始创建一个新的虚拟机，这里选 `Custom` 具有更多更详细的配置，如下图

![](https://img-blog.csdnimg.cn/336d724cad824a44bc0a5e2fa773e6e1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 点击下一步，进入选择虚拟机硬件兼容性，一般默认，**选择下一步**

![](https://img-blog.csdnimg.cn/76588ca576584092b36de0a584e884cc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入选择客户操作系统安装，这里选择 **我将稍后安装操作系统**，点击下一步

![](https://img-blog.csdnimg.cn/cce8cb1707a948f2b3dfab9ea5547d3d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入选择将安装的操作系统类型，本次选择安装 **Linux系统**，**Ubuntu**，点击下一步

![](https://img-blog.csdnimg.cn/8bce66e87f824f399cf813fbc3a60e47.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入选择虚拟机名字和虚拟机创建路径，根据自己的喜好修改好后点击下一步

![](https://img-blog.csdnimg.cn/21c961811a1d4867b0027543476c72e5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入处理器配置，可以根据自己电脑的逻辑处理器个数和内核个数进行修改(一般情况下默认即可，如果需要有更高的性能可以修改)，这里选择默认，点击下一步

![](https://img-blog.csdnimg.cn/6a6cfc837d864c158b39797a14da0985.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入内存分配，VMware 会自动检测你的电脑内存大小并给出建议(这里可以根据需求自行调整，只要不超过所建议的最大内存即可)，这里我修改为 **3072MB**，即 **3G 内存**，根据自己的喜好修改好后点击下一步

![](https://img-blog.csdnimg.cn/9cccd081ea4347c6b19f8e6c89bc778f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入网络类型配置，这里选择 `Use network address translation(NAT)` ，点击下一步

![](https://img-blog.csdnimg.cn/d1179a5976644b85989b0e6f352a3b40.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入选择 选择I/O控制器类型，这里默认 ，点击下一步

![](https://img-blog.csdnimg.cn/bddaa9c871504fcbb3ec81124d9a0085.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入选择磁盘类型，默认为固态硬盘(SCSI) ，如有需求可以自行修改，这里选择 **SCSI** ，点击下一步

![](https://img-blog.csdnimg.cn/447abdea2add4e8eb8aa0dfc1b132188.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入选择磁盘，这里选择 **创建一个新的虚拟磁盘** ，点击下一步

![](https://img-blog.csdnimg.cn/7f5f0c6ceb524b449f56a29f33702b40.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入选择磁盘空间大小，如果此虚拟机之后只是用来随意玩耍，分配的磁盘空间大小 **20G** 即可，如果此虚拟机之后是用来开发或者研究，建议分配空间不要小于 **40G** (这里只是我个人使用总结的经验)，这里我分配了 **40G** ，之后选项默认，点击下一步

![](https://img-blog.csdnimg.cn/6b8d5bafa6f943f0b65e7d73876cca30.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入 指定磁盘文件 ，这里默认，点击下一步

![](https://img-blog.csdnimg.cn/5b9f907e5cb446719d31f5caf8f5c7be.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 最后确认好配置后，点击 `Finish`

![](https://img-blog.csdnimg.cn/20d168c85c944b71908ab9c0b5027b2c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 至此一个虚拟机便创建成功

![](https://img-blog.csdnimg.cn/0dae195f66d5477aa5bdb146e2c553b8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

### 配置虚拟机
- 点击 `Edit virtual machine settings` 进入虚拟机配置

![](https://img-blog.csdnimg.cn/9260ca88b40c443f85c1afacb9f48f44.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 在虚拟机关闭时可以进入虚拟机配置界面对虚拟机参数进行配置
- 【必配】 USB类型，默认USB支持为**2.0**，需要将USB切换到**3.1**。

![](https://img-blog.csdnimg.cn/a335382a7e5a435194012f627d1a8784.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

### 总结
- 上面便是安装并创建虚拟机的整体流程，如遇到问题欢迎评论区留言。
- 当然虚拟机还有个重要的部分便是安装操作系统
- 如何安装操作系统(Ubuntu 为例)请移步 [【linux系列】VMware虚拟机下安装Ubuntu](https://blog.csdn.net/h2763246823/article/details/122523076)
