---
title: Eclipse 快速创建运行 Javaweb 项目
author: HWH
tags:
  - Eclipse
  - Java Web
  - Java
categories:
  - - Tutorial
  - - Tools
abbrlink: d81ea67b
date: 2022-11-20 15:10:04
---

## 使用环境
- Eclipse IDE for Enterprise Java Developers.
	- Version: 2020-03 (4.15.0)
	- Build id: 20200313-1211
- Tomcat
	- Version: apache-tomcat-9.0.46

## 创建项目
- 选择 File -> New -> Dynamic Web Project

![](https://img-blog.csdnimg.cn/a9352cb6ece04477ad4c729a93a13c27.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 注意：有可能找不到 Dynamic Web Project 选项，可以进入 Other，在其中的 Web 文件夹中寻找到，如下图：

![](https://img-blog.csdnimg.cn/c9775232e5f449a0bc80abc49566d412.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

![](https://img-blog.csdnimg.cn/938f9b2b5396402c89f037a7da380581.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)


- 填入项目名后可以直接点击 Finish，如果需要修改项目的目录结构可以点 Next 进行下一步设置

![](https://img-blog.csdnimg.cn/e464d7fac99a42c998146348503e72d6.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 点击 Finish 后就会创建一个web项目

![](https://img-blog.csdnimg.cn/deace37901bb424cb80e72eac019c752.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

## 运行项目
- 首先配置运行环境，此处需要用到 Tomcat，(本教程默认已经安装 Tomcat 且可以正常上使用)
- 进入到 Window -> Preferences -> Server -> Runtime Environments 在此处进行 eclipse 与本地 Tomcat 的关联，(默认应该没有tomcat配置，因为我已经配置过一次所以有配置好的一个运行环境)

![](https://img-blog.csdnimg.cn/bfb97d1c0892499caae927d9d7e32a09.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 点击 Add 按钮后，选择你对应的 Tomcat 版本，(由于我用的是 apache-tomcat-9.0.46, 此处选择 Tomcat v9.0，注意此 eclipse 版本没有 Tomcat v10.0 选项)，点击 Next

![](https://img-blog.csdnimg.cn/9d51d42bf27448db90f50443ee8439e5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 点击 Browse 选择 Tomcat 安装路径，切换 JRE，然后点击 Finish 即可完成运行环境的配置

![](https://img-blog.csdnimg.cn/a3b8a97cde2e4e0eb72e595ae2a3218d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

## 测试配置

- 右键 WebContent 新建一个 jsp 文件

![](https://img-blog.csdnimg.cn/d7f5dba6b4fe43ebbbf92689f13bc2cf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 编写测试代码

![](https://img-blog.csdnimg.cn/8be11a858220424f9e1112fa75551a8d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 可以直接点击顶部绿三角运行项目，项目将运行在 tomcat 上

![](https://img-blog.csdnimg.cn/02b3c7666fe443c18941f6351e725796.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 运行成功则出现如下界面

![](https://img-blog.csdnimg.cn/8bb5497d2661488b82418739bc645a65.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

## 个性化配置
- 进入到 Window -> Preferences -> Server -> Launching 界面可以设置Launching 的效果

![](https://img-blog.csdnimg.cn/68096efcf3c1478a99a527622d6e3e73.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

## 遇到的问题
- **The superclass "javax.servlet.http.HttpServlet" was not found on the Java Build Path**
- 解决办法
- 右击项目名 进入如下路径 点击 Configure Build Path

![](https://img-blog.csdnimg.cn/6cef1e6081d0455db5d8e7503302fc1d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 选择 Java Build Path，切换到 Libraries 标签页下，点击 ClassPath，然后点击 Add Library
-  [Classpath 和 Modulepath 的区别](https://bbs.csdn.net/topics/392628907?list=10230532)(本人暂时并不明白 Classpath 和 Modulepath 的区别，感兴趣的可以参考以上的文章)

![](https://img-blog.csdnimg.cn/b7d8e8ace2b44dcf98f676ec3c637cb7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 选择 Server Runtime

![](https://img-blog.csdnimg.cn/93bb59349e9f450da833d250767e4aa7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 选择配置好的 Tomcat，点击 Finish 即可解决(如果空白则表示 tomcat 配置失败)

![](https://img-blog.csdnimg.cn/4732000b6d5044ca8990aa6eff76bace.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
