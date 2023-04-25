---
title: 'Electron-vue出现GET http://localhost:9080/__webpack_hmr net::ERR_ABORTED解决方案'
author: HWH
tags:
  - Electron
  - Vue
categories:
  - Solutions
abbrlink: bb41c791
date: 2022-11-20 14:58:48
---


## 使用版本
- 以下是我解决此问题时使用的electron和vue等的一些版本信息

![版本信息](https://img-blog.csdnimg.cn/b7cfc72382bd4247b81f8b269370a592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBA6Z6g5p2J,size_61,color_FFFFFF,t_70,g_se,x_16)

- 【附】经过测试 `electron` 的版本为 `13.1.4` 时也能解决

## 解决方案
-  将项目下的 `.electron-vue/dev-runner.js` 文件打开
- 定位到第二个 `WebpackDevServer`

![WebpackDevServer 位置图示](https://img-blog.csdnimg.cn/af8cafd21fb9445589638684a5910140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBA6Z6g5p2J,size_54,color_FFFFFF,t_70,g_se,x_16)

- 将第 `69` 行的注释打开

![69 行注释打开后图示](https://img-blog.csdnimg.cn/49e10e6c5247402482fa3ff064acf636.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBA6Z6g5p2J,size_39,color_FFFFFF,t_70,g_se,x_16)

- 重启项目即可解决 `GET http://localhost:9080/__webpack_hmr net::ERR_ABORTED` 报红的问题

## 解决总结
- 感觉是因为之前 `app.use(hostMiddleware` 被注释，因此 `webpack-hot-middleware` 中间件未能被正常的加载而导致了热部署出现了问题。
- 当前对` webpack` 尚不了解，分析的有问题希望大佬指出。
