---
title: 【linux系列】内核编译最新教程+常见问题总结
author: HWH
tags:
  - Linux
  - Kernel
categories:
  - - Tutorial
  - - Summarize
  - - Solutions
abbrlink: 841d2940
date: 2022-12-03 20:20:20
---


### 编译环境
- 虚拟机： `VMware workstation pro 16`
- 虚拟机操作系统： `ubuntu-20.04.3-desktop-amd64`
- 原系统内核版本：`5.11.0-46-generic`
- 内核源码版本：`5.11.1`

### 环境准备
- [安装VMware虚拟机并创建虚拟机](https://blog.csdn.net/h2763246823/article/details/122522464)
- 安装Ubuntu系统
- [下载内核源码](https://cdn.kernel.org/pub/linux/kernel/)
	- 新编译的内核源码版本建议和原系统内核版本相近(大版本一致)
	- 查看系统内核版本命令
		```powershell
		uname -r
		```



### 编译准备
- 建议将内核源码移动到 `/usr/src/`下

```powershell
sudo cp linux-5.11.1.tar.xz /usr/src/
```
- 切换目录到 `/usr/src/` 下

```powershell
cd /usr/src/
```

- 将内核源码解压，这里使用的是 `linux-5.11.1.tar.xz` ，`.xz`为双重压缩，因此先使用 `xz命令`解压

```powershell
sudo xz -d linux-5.11.1.tar.xz
```
- 然后使用 `tar命令` 解压

```powershell
sudo tar -xf linux-5.11.1.tar
```
- 切换到 `linux-5.11.1` 目录下

```powershell
cd linux-5.11.1
```
### 修改配置
#### linux 提供的三种配置命令
```

// 不建议使用
sudo make config


// 建议使用（图形界面，键盘操作）
sudo make menuconfig



// 强烈建议使用（图形界面，鼠标操作）
sudo make gconfig

```
#### 使用 gconfig 配置
- 安装 `make `

```powershell
sudo apt install make
```
- 安装 `gcc`

```powershell
sudo apt install gcc
```
- 安装 `gtk+-2.0` 、`gmodule-2.0` 和 `libglade-2.0`

```powershell
sudo apt install libgtk2.0-dev libglib2.0-dev libglade2-dev
```
- 安装 `flex`

```powershell
sudo apt install flex
```
- 安装 `bison`

```powershell
sudo apt install bison
```
- 启动 gconfig

```powershell
sudo make gconfig
```
![gconfig界面](https://img-blog.csdnimg.cn/c2e9b73fc0a4448ca8447939159789e3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 如果需要修改配置可以在 **gconfig**中图形化进行设置，在这里需要注意，配置选项有时分为三类，分别对应 `N`、`M` 和 `Y`
	- `N` 未选中，即编译时不加入，gconfig中为不选中
	- `M` 编译成模块，gconfig中选中时为 `-` 表示
	- `Y` 编译入内核，gconfig中选中时为 `√` 表示
- 配置好后点击 `Save` 即可保存配置，生成 `.config` 文件
- 通常源码所带的默认配置所需编译的内核和模块很多，编译一次时间大约在 2-3小时左右(与虚拟机的配置和电脑的性能有关)
- 因此在开发中遇到需要编译内核的情况，往往对内核进行裁剪，这里我准备了一份[裁剪过的内核配置](https://drive.google.com/file/d/1kG54AP5PTmyKTvRBmmVBWRQmwQjs5mJN/view?usp=sharing)(裁剪的较多，很多模块缺失，谨慎使用)
- **使用方式**：
	-  【建议】先使用 **gconfig** 生成 `.config` 文件，并做好**备份**
	- 下载 [.config(Google 云端硬盘)](https://drive.google.com/file/d/1kG54AP5PTmyKTvRBmmVBWRQmwQjs5mJN/view?usp=sharing) 或者[.config(百度网盘 提取码 2022 )](https://pan.baidu.com/s/1wWb4qSK69_rADOkFnuiSMg)后替换原本内核中的 `.config`
	- 执行 `sudo make gconfig` 检查是否替换成功，点击 `Save` 保存

#### 编译内核
- 安装 `ssl`

```powershell
sudo apt install libssl-dev
```

- 安装 `elf`

```powershell
sudo apt install libelf-dev
```

- 执行编译命令

```powershell
sudo make -j4
```
- 编译时间大概为半小时，开把游戏，倒杯茶，走几圈。
- 编译成功如下图

![](https://img-blog.csdnimg.cn/53d259b602ad4378b5ada1cf36d096d2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

###  安装内核
- 安装模块

```powershell
sudo make modules_install
```
- 安装成功如下图

![](https://img-blog.csdnimg.cn/0f736189f2cb431eb7b31f991caa84e3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 安装内核

```powershell
sudo make install
```
- 安装成功如下图

![](https://img-blog.csdnimg.cn/48aa76ce139248ee90c309a90d1369b6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)


### 载入新内核
- 重启 Ubuntu

	```powershell
	reboot
	```
- 系统会默认将新安装的内核作为 **grub** 的第一个启动项
- 如果想要自行切换不同内核，在出现载入进度条时，点击 `shift`

![](https://img-blog.csdnimg.cn/ca3044cc312049e7b0ef17200c9761b0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 进入 **grub** 后选择 `ubuntu 高级选项`

![](https://img-blog.csdnimg.cn/a7318e3b593340cfb43dce6e3f5474e5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 回车进入后，上下选择自己想要进入的内核，回车进入

![](https://img-blog.csdnimg.cn/ac1b9c5bf77a4b3c851f8f7b0a245025.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

### 检查内核
- 检查内核版本

	```powershell
	uname -r
	```
- 结果如下

![](https://img-blog.csdnimg.cn/d2f5ef8020da4f5d8aad24f2ed0182c0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)


### 常见问题

```powershell
sudo: make：找不到命令
```
**解决办法：**  安装 `make`

---

```powershell
/bin/sh: 1: gcc: not found
make[1]: *** [scripts/Makefile.host:95：scripts/basic/fixdep] 错误 127
make: *** [Makefile:534：scripts_basic] 错误 2
```
**解决办法：** 安装 `gcc`

---

```powershell
*
* Unable to find the GTK+ installation. Please make sure that
* the GTK+ 2.0 development package is correctly installed.
* You need gtk+-2.0 gmodule-2.0 libglade-2.0
*
make[1]: *** [scripts/kconfig/Makefile:204：scripts/kconfig/gconf-cfg] 错误 1
make: *** [Makefile:601：gconfig] 错误 2

```
**解决办法：** 安装 `gtk+-2.0` 、`gmodule-2.0` 和 `libglade-2.0`

---

```powershell
/bin/sh: 1: flex: not found
make[1]: *** [scripts/Makefile.host:9：scripts/kconfig/lexer.lex.c] 错误 127
make: *** [Makefile:601：gconfig] 错误 2
```

**解决办法：** 安装 `flex`

---

```powershell
/bin/sh: 1: bison: not found
make[1]: *** [scripts/Makefile.host:17：scripts/kconfig/parser.tab.h] 错误 127
make: *** [Makefile:601：gconfig] 错误 2
```

**解决办法：** 安装 `bison`

---

```powershell
scripts/extract-cert.c:21:10: fatal error: openssl/bio.h: 没有那个文件或目录
   21 | #include <openssl/bio.h>
      |          ^~~~~~~~~~~~~~~
compilation terminated.
make[1]: *** [scripts/Makefile.host:95：scripts/extract-cert] 错误 1
make[1]: *** 正在等待未完成的任务....
make: *** [Makefile:1185：scripts] 错误 2
```

**解决办法：** 安装 `ssl`

---

```powershell
error: Cannot generate ORC metadata for CONFIG_UNWINDER_ORC=y, please install libelf-dev, libelf-devel or elfutils-libelf-devel
make: *** [Makefile:1222：prepare-objtool] 错误 1
```

**解决办法：** 安装 `elf`

---

```powershell
进入initramfs
alert：uuid=xxxxxx does not exist，
ls /dev下没有sda
```
**原因分析：** 内核裁剪过度，将 **SCSI** 模块裁剪，**MPT**模块裁剪
**解决办法：** 重新修改内核配置，将 **Device Drivers**下，
- **Fusion MPT device suppot**中所有模块加入
-  **SCSI device support**下与 **SCSI** 有关的模块加入
- **SCSI device support**下 **SCSI low-level drivers** 下 **LSI MPT Fusion SAS 3.0 & SAS 2.0 Device Driver**加入
