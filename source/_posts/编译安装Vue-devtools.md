---
title: 编译安装Vue-devtools
author: HWH
tags:
  - Vue
categories:
  - - Tools
  - - Tutorial
abbrlink: 6d37a98f
date: 2022-11-20 14:27:39
---



## 安装Vue-devtool环境要求
- 安装环境要求node在6以上，npm在3以上
- 此教程测试使用的环境
	- Vue-devtools add-remote-devtools
	- npm 6.14.13
	- node v14.17.0
## clone 或者下载
- clone和下载地址
github网址: [https://github.com/vuejs/vue-devtools/tree/add-remote-devtools](https://github.com/vuejs/vue-devtools/tree/add-remote-devtools)
clone地址: [https://github.com/vuejs/vue-devtools.git](https://github.com/vuejs/vue-devtools.git)
- 若选择下载压缩包，先解压到需要的目录，建议修改文件夹名，此处我修改为`vue-devtools`

## npm 编译
- 打开`cmd`，切换到`vue-devtools`目录下
- 输入`npm install`，回车执行
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210607114721598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)

- 待执行完毕后输入`npm run build`，回车执行
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210607114732359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)

## Chrome 安装
- 打开谷歌浏览器->设置->更多工具->扩展程序
- 点击加载已解压的扩展程序
- 找到`vue-devtools`目录下的`shells`文件夹
- 选择`shells`文件夹中的`Chrome`文件夹
- 点击选择文件夹，安装成功

## 使用
- 打开`cmd`，切换到`vue-devtools`目录下
- 执行`npm run dev`
- 浏览器访问[http://localhost:8080/](http://localhost:8080/)
- 可查看vue实例
- 在使用vue编写的网页上，插件图标会亮起，点击可使用。

## 写在最后
- 通过此教程安装的Vue-devtools版本为`Vue.js devtools
4.0.1`，而Chrome网上应用商店下载安装的版本为`Vue.js devtools
5.3.4`，尝试编译Vue-devtools的next版本，但编译失败，此问题仍需解决。
- 待学习vue.js后将详细介绍Vue-devtools的使用。
