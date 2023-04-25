---
title: 【快速解决】conda 报错ValueError check_hostname requires server_hostname
author: HWH
tags:
  - Anaconda
categories:
  - Solutions
abbrlink: 82614af8
date: 2022-11-20 15:14:46
---


## 写在前面
- 我的环境：
  - Anaconda 4.10.1
  - Win10

## 快速解决
![报错内容](https://img-blog.csdnimg.cn/71c803243b62421b8895e9c65a7dce27.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 根据报错信息可以判断出是网络连接问题，检查后是因为我设置了代理，只需要给conda也配置上代理即可
- 在 `C:\users\username\` 下找到并打开 `.condarc` 文件，文件可能隐藏，可以在文件资源管理器中设置查看->显示隐藏的文件
-`.condarc` 文件可能为空也可能有内容，只需要添加上如下语句:

```powershell
proxy_servers: {http: 127.0.0.1:port, https: 127.0.0.1:port}
```
- 注意：将 `port` 换成自己的端口，可以在Win10的设置代理中的手动设置部分找到。
