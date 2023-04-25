---
title: 【linux系列】实现基于模块的文件系统
author: HWH
math: true
mermaid: true
tags:
  - Linux
  - 操作系统
categories:
  - - Tutorial
abbrlink: 8495c1d2
date: 2022-12-10 20:21:58
---


### 编译环境
- 虚拟机操作系统： `ubuntu-20.04.3-desktop-amd64`
- 原系统内核版本：`5.11.0-46-generic`
- 内核源码版本：`5.11.1`

### 修改任务
#### 基础任务
- 修改XFS文件系统的源代码，实现新的文件系统。
- 至少需要修改文件系统的名称。
- 可以动态加载和卸载新的文件系统。
- 列出操作系统中包含的文件系统列表，其中要有自己改名后的文件系统。

#### 进阶任务
- 重新编译Linux内核，以模块编译方式，将文件系统编入内核。
### 修改准备
-  [下载内核源码](https://cdn.kernel.org/pub/linux/kernel/)
- 进入到 **fs** 文件夹下，复制 **xfs** 源码，以防修改错误导致无法恢复

```powershell
sudo cp -r xfs hwhxfs
```

- 如下图所示

![](https://img-blog.csdnimg.cn/9cd52b588d764949982a61e2cc3b4dc8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

### 修改源码
- 所需修改的文件，均在 **hwhxfs** 文件夹下

```powershell
Makefile

xfs_super.c

xfs_sysctl.c
```

#### 修改 Makefile
- 使用 **gedit** 打开 **Makefile**

```powershell
sudo gedit Makefile
```
- 修改 `ccflags-y`

```powershell
# 修改后
ccflags-y += -I $(src)		# needed for trace events
ccflags-y += -I $(src)/libxfs
```

- 修改 `obj-$(CONFIG_XFS_FS)`

```powershell
# 修改后
obj-$(CONFIG_XFS_FS)		+= hwhxfs.o
```

- 修改左侧所有 **xfs** 为自己的名称，这里均修改为 **hwhxfs**

![](https://img-blog.csdnimg.cn/e06721e0dab64188a7e4ea80d84a15d9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)


#### 修改 xfs_super.c
- 使用 **gedit** 打开 **xfs_super.c**

```powershell
sudo gedit xfs_super.c
```

- 搜索 `"xfs"`

![](https://img-blog.csdnimg.cn/66f7dc6bee604c82bf04884f288d0d46.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 将这两个对应修改为自己的名字，这里修改为 **hwhxfs**，如下图

![](https://img-blog.csdnimg.cn/52a4913edb734d8189cc8fd407c33847.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 修改 xfs_sysctl.c
- 使用 **gedit** 打开 **xfs_sysctl.c**

```powershell
sudo gedit xfs_sysctl.c
```

- 搜索 `"xfs"`

![](https://img-blog.csdnimg.cn/2cf639677438443991a8c4b68ddee214.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 将其对应修改为自己的名字，这里修改为 **hwhxfs**，如下图

![](https://img-blog.csdnimg.cn/a4a25988647c492e829d9ae351125050.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

### 编译模块
- 确保在 **hwhxfs** 目录下
- 执行编译命令

```powershell
sudo make -C /usr/src/linux-5.11.1/ M=$PWD
```

- 编译成功后会在 **hwhxfs** 目录下生成 **hwhxfs.ko** 文件

![](https://img-blog.csdnimg.cn/f06bb8b43ff7488a96adf853e9411234.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

### 安装模块
- 添加依赖

```powershell
sudo modprobe libcrc32c
```
注：一般会缺少此依赖，并在安装模块时报告如下错误

```powershell
insmod: ERROR: could not insert module hwhxfs.ko: Unknown symbol in module
```
可以使用 `modinfo  ./hwhxfs.ko | grep depend` 命令查看需要加载的依赖

---

- 安装模块

```powershell
sudo insmod hwhxfs.ko
```
- 查看已安装模块

```powershell
lsmod
```

效果如下：
![](https://img-blog.csdnimg.cn/339e67de5fac49f9addafd9264e17e60.png)

---

### 挂载文件系统
- 为什么要进行挂载呢？
	- 每个文件系统都有独立的inode、区块、超级区块等信息，这个文件系统要能够链接到目录树才能被我们使用。而将文件系统与目录树结合的操作称之为挂载。
- 挂载文件系统的命令

```powershell
mount -t 文件系统 设备文件名 挂载点
```

- 文件系统：这里已经准备好了 **hwhxfs** 文件系统
- 挂载点：一个目录，是文件系统的入口，我们可以在 `/mnt` 下新建个目录作为挂载点

```powershell
sudo mkdir /mnt/xfs
```

- 设备文件名：设备文件，我们需要对磁盘进行分区以分配给文件系统一个存储设备
- 查看磁盘信息

```powershell
sudo parted /dev/sda print
```

![](https://img-blog.csdnimg.cn/12c6b8dc02a44d17a71fb2d1e352fa65.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)


- 分区表格式有两种，即MBR（msdos）和GPT，MBR使用 `fdisk` 分区，GPT使用 `gdisk` 进行分区。
- 这里使用 `fdisk` 进行分区

```powershell
sudo fdisk /dev/sda
```

- 按 `n` 新建分区
- 若出现 **无空闲扇区可用** 表明当前磁盘空间不足无法进行分区

![](https://img-blog.csdnimg.cn/8534677c0a884bea9dc9d51952a7094c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 此时如果是虚拟机，可以通过添加磁盘来解决
- 关机打开虚拟机设置，添加磁盘

![](https://img-blog.csdnimg.cn/f28a1c4361524a6ab873dc91e77e2800.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 一律下一步，分配磁盘空间时分配 **1G** 即可，更多也可以

![](https://img-blog.csdnimg.cn/8a7099861f4a4217a9bd8680b636c474.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 添加新磁盘完成后，重启虚拟机
- 查看新增磁盘的名字

```powershell
lsblk -l
```

![](https://img-blog.csdnimg.cn/ad1660d6b39b4078819de7a4a7d48fee.png)

- 用 `fdisk` 对新增磁盘进行分区（建议先输入 m 获取 fdisk 使用帮助）

```powershell
sudo fdisk /dev/sdb
```

![](https://img-blog.csdnimg.cn/61a6cdfc91ee455f8ae6ef8a98a7c4e0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 安装 **xfsprogs**

```powershell
sudo apt install xfsprogs
```

- 格式化分区，将分区格式化为 **xfs** 类型

```powershell
sudo mkfs.xfs /dev/sdb4
```

- 挂载文件系统

```powershell
sudo mount -t hwhxfs /dev/sdb4 /mnt/xfs
```

- 使用 `df -h` 命令查看

![](https://img-blog.csdnimg.cn/c5e54d8f636a4fa3951c2b464cfeb373.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 卸载文件系统

```powershell
sudo umount /mnt/xfs
```

### 进阶任务
- 将文件系统以模块的形式编入内核
- 所需修改的文件如下

```powershell
fs/Kconfig
fs/Makefile

fs/hwhxfs/Kconfig
fs/hwhxfs/Makefile
```


#### 修改 fs/hwhxfs/Kconfig
- 将 **Kconfig** 中所有 `XFS` 替换为 `HWHXFS`

![](https://img-blog.csdnimg.cn/b977abb6eab540ca939538c84b42d3a8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 修改 fs/hwhxfs/Makefile
- 将 Makefile 中所有 `XFS` 替换为 `HWHXFS`

![](https://img-blog.csdnimg.cn/70b6920910784279b4b2413cf8654e30.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 修改 fs/Kconfig
- 在 **Kconfig** 中搜索 `xfs`，对应添加一行 `source`

![](https://img-blog.csdnimg.cn/0167fac1c7de42f89e11d1c98ba51c98.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 修改 fs/Makefile
- 在 **Makefile** 中搜索 `xfs`，对应添加一行 `obj-`

![](https://img-blog.csdnimg.cn/1ebe5b92f122429ab35233b583687afc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

---
#### 编译安装
- 修改配置

```powershell
sudo make gconfig
```

- 在 `File systems` 下找到 `HWHXFS` 设置为模块编译，如下图

![](https://img-blog.csdnimg.cn/99e5537c91c54499960416b0f000790d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 保存配置
- 编译安装内核，重启
- 使用 `modprobe hwhxfs` 安装模块
- 内核编译相关问题可以参考我之前的文章，[【linux系列】内核编译最新教程+常见问题总结](https://blog.csdn.net/h2763246823/article/details/122522326)
