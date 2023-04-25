---
title: create-electron-app安装配置
author: HWH
tags:
  - Electron
categories:
  - - Tools
  - - Tutorial
abbrlink: 9858c2c8
date: 2022-11-20 14:39:01
---


## 写在前面
-  本文章分为三个部分
	- Node.js安装配置
	- Git安装配置
	- 安装create-electron-app
- 如果已安装过Node.js和Git可适当跳过
- Node.js的配置较为重要，确保按教程修改
- 遇到一切问题请仔细分析报错
- 确保修改环境变量时所填路径无误


## Node.js安装配置

### 下载

- [官网](https://nodejs.org/en/)下载node.js不建议使用最新版本

- 需要安装其他版本的可以访问，[node.js其他版本](https://nodejs.org/download/release/)

- 根据自己的系统选择对应的版本，此处以`v14.17.0`为例

- 下载[node-v14.17.0-x64.msi](https://nodejs.org/download/release/v14.17.0/node-v14.17.0-x64.msi)

### 安装

- 运行`.msi`文件，除了修改安装路径，均点击下一步

- 安装完成后打开`cmd`

- 执行`node -v`，如果正确显示版本则安装成功

- 执行`npm -v` ，如果正确显示版本则安装成功

- 如果安装路径为C盘，注意`cmd`用管理员身份打开

- 如果执行上面命令出错，检查环境变量是否正确。

### 配置

#### 配置npm安装全局模块和缓存路径

- 在node.js的安装目录下新建两个文件夹 node_global和node_cache

- `cmd`执行`npm config set prefix "D:\nodejs\node_global"`

- `cmd`执行`npm config set cache "D:\nodejs\node_cache"`

- 执行成功后配置环境变量

- **系统变量**：新建系统变量，变量名`NODE_PATH`，变量值`D:\nodejs\node_modules`

- **用户变量**：在Path中修改node的默认路径`C:\Users\users\AppData\Roaming\npm` 为`D:\nodejs\node_global`

- `cmd`执行`npm install vue -g`，安装完成后查看`D:\nodejs\node_global\node_modules`路径下是否出现vue文件夹，出现则修改成功。

- 注意：上述要修改成的路径以自己的实际路径为准，关注`node_global`和`node_cache`即可。

#### 修改npm镜像

- `cmd`执行如下命令

- `npm config set ELECTRON_MIRROR http://npm.taobao.org/mirrors/electron/`

- 修改环境变量，新建用户变量

- 变量名`ELECTRON_MIRROR`，变量值`http://npm.taobao.org/mirrors/electron/`

- 重启`cmd`

---

## Git安装配置

### 下载

- [国内镜像](https://npm.taobao.org/mirrors/git-for-windows/)下载

- 此处以`v2.30.0`为例

- 下载[Git-2.30.0-64-bit.exe](https://npm.taobao.org/mirrors/git-for-windows/v2.30.0.windows.1/Git-2.30.0-64-bit.exe)



### 安装

- 运行`.exe`文件，除了修改安装路径，均点击下一步

- 安装完成后打开`cmd`执行`git --version`

- 若未出现版本号，则检查系统环境变量


---
## 安装create-electron-app

### 安装

- 安装yarn，`cmd`执行`npm install yarn -g`

- 安装create-electron-app，`cmd`执行`yarn global add create-electron-app`

- 如果出现yarn不是可执行的命令，请检查环境变量的配置

### 配置

- create-electron-app将安装到`D:\nodejs\node_global\bin`目录下

- 用户变量的Path添加create-electron-app的安装目录，即添加`D:\nodejs\node_global\bin`

### 测试

- 到希望安装的目录下打开`cmd`

- 执行`create-electron-app test`，即创建名为test的项目

- 如果报有关node install.js的错误

- 进入项目的`node_modules\electron\`下，`cmd`执行`node install.js`

- 上述执行完成无报错后，可在`test\`下执行`npm start`，启动项目
