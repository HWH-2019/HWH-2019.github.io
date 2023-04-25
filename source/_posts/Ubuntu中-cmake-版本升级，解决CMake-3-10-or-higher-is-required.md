---
title: Ubuntu中 cmake 版本升级，解决CMake 3.10 or higher is required
author: HWH
math: true
mermaid: true
tags:
  - Ubuntu
  - Linux
  - CMake
categories:
  - - Solutions
abbrlink: ec8aef5d
date: 2022-12-10 20:53:28
---


### 写在前面
- 今天在跑 SLAM十三讲 第三章的 visualizeGeometry 配套代码时，需要安装 `Pangolin` 库，下载源码编译时报错 `CMake 3.10 or higher is required`，记录一下解决方法。

### 解决方法
- 卸载原有的cmake

```
sudo apt autoremove cmake
```

- 进入 `/usr/bin` 目录（cmake 默认安装路径）
- 下载源码压缩包并解压（需要其他版本只需修改相应版本即可）

```
wget https://github.com/Kitware/CMake/releases/download/v3.10.0/cmake-3.10.0.tar.gz
tar -zxvf cmake-3.10.0.tar.gz
```

- 如果出现无法下载可以浏览器访问网址直接下载
- 安装相关依赖

```
sudo apt install libssl-dev build-essential
```

- 安装

```
sudo ./bootstrap
sudo make
sudo make install

# 一步到位
sudo ./bootstrap && sudo make && sudo make install
```
