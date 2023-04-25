---
title: Ubuntu 安装配置代理(Linux for clash + proxychains)
author: HWH
math: true
mermaid: true
tags:
  - Ubuntu
  - Clash
  - Proxychains
categories:
  - - Tutorial
abbrlink: dbcae627
date: 2022-12-10 21:02:14
---



### 写在前面
- 这两天要装 FSL 软件发现第一步不挂代理是真的慢，所以决定研究一下怎么在 Linux 上挂代理
- 简单搜索后选择使用 `Linux for clash` 和 `proxychains` 解决代理问题
- 网上的教程很多但我试过之后大多会出现一些细节问题，而且千篇一律
- 所以整理一下挂代理的安装配置流程和细节
- 顺嘴提一句 `clash` 是真的好用

### 配置环境
- 虚拟机： `VMware workstation pro 16`
- 虚拟机操作系统： `ubuntu-18.04-desktop-amd64`
- Linux for clash版本：`clash-linux-amd64-v1.11.8`

### Clash 安装配置
#### Linux for clash 下载
- 访问 `clash` 的[版本发布页](https://github.com/Dreamacro/clash/releases)选择合适的版本下载
- 这里也提供我使用的 [clash-linux-amd64-v1.11.8](https://github.com/Dreamacro/clash/releases/download/v1.11.8/clash-linux-amd64-v1.11.8.gz) 快速下载地址
- 可以通过浏览器下载，也可以使用 `wget`下载，`clash` 很小可以自行选择下载方式，以下提供 `wget` 下载命令

```bash
wget https://github.com/Dreamacro/clash/releases/download/v1.11.8/clash-linux-amd64-v1.11.8.gz
```

#### Linux for clash 安装
- 首先对下载下来的压缩包解压，可以采用命令解压或者双击后提取，下面附解压命令

```bash
gzip -d clash-linux-amd64-v1.11.8.gz
```

- 解压后对 `clash-linux-amd64-v1.11.8` 文件赋予执行权限

```bash
chmod +x clash-linux-amd64-v1.11.8
```
> 注意：双击提取后的文件名可能为clash-linux-amd64，对应更改后续命令即可

- 使用 `mv` 命令将文件移动到指定目录，方便全局访问 `clash`

```bash
sudo mv clash-linux-amd64-v1.11.8 /usr/local/bin/clash
```

- 终端执行 `clash -v` 命令检查 `clash` 能否正常运行，正常终端显示结果如下：

```bash
Clash v1.11.8 linux amd64 with go1.19 Fri Aug 26 13:20:30 UTC 2022
```

#### Linux for clash 配置
- `clash` 运行需要依赖相应的 `YAML` 配置文件，默认读取 `$HOME/.config/clash/config.yaml`
- 当没有这份文件的时候，clash 会使用默认配置生成一份，所以我们可以直接运行一下 `clash` ，来获取模版，同时会自动下载 `Country.mmdb` 文件
- 第一次在终端执行 `clash` 的结果如下：

```bash
INFO[0000] Can't find config, create a initial config file
INFO[0000] Can't find MMDB, start download
INFO[0000] Mixed(http+socks) proxy listening at: 127.0.0.1:7890
```

- 此时已获得配置文件的模板，但这模板并没有什么用，`yaml` 文件的内容往往需要代理方提供，这就需要去购买	`VPN`
- 大家可以自行找找，这里所需的 `VPN` 不能是基于软件（仅能在安装它所提供的软件后才可使用）的，而是可以提供订阅链接（即可以取得配置文件的）
- 在取得相应的配置文件后将其改名为 `config.yaml` 并替换相应目录下的初始文件

#### Linux for clash 运行
- 在配置完成后即可在终端执行 `sudo clash` 运行，若无报错则运行成功
- 运行后可以浏览器访问 [Clash控制台](http://clash.razord.top/#/proxies)
- 如果访问失败，打开设置->网络->网络代理，切换为手动，设置下图参数后

![代理配置参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/26626d768c7f4d5faea12ee68e9a0c38~tplv-k3u1fbpfcp-zoom-1.image)

- 访问成功后将进入如下界面：

![clash控制台](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ad51b3f41bf4a689f3ff73361a45fb3~tplv-k3u1fbpfcp-zoom-1.image)

- 点击设置，检查**允许来自局域网的连接**是否开启

![设置页面](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/78e9dc865f5642eba44af72935e75f30~tplv-k3u1fbpfcp-zoom-1.image)

- 访问尝试外网网站，如 [Google](https://www.google.com)
- 至此 `Linux for clash` 已经安装配置好，接下来将介绍如何配置开启自启动，如果不需要的可以跳过下一小节

#### Linux for clash 开机自启
- 自启动的话将 `clash` 托管给 `systemd` 来管理
- 新建 `clash.service` 文件

```bash
sudo gedit /etc/systemd/system/clash.service
```

- 将以下内容写入到文件中，注意修改 `ExecStart` 的路径

```bash
[Unit]
Description=Clash - A rule-based tunnel in Go
Documentation=https://github.com/Dreamacro/clash/wiki
[Service]
OOMScoreAdjust=-1000
ExecStart=/usr/local/bin/clash -f /root/.config/clash/config.yaml
Restart=on-failure
RestartSec=5
[Install]
WantedBy=multi-user.target
```

- `ExecStart` 的路径可以使用以下命令进行查询

```bash
ls $HOME/.config/clash/config.yaml
```

- 配置开机自启，并启动 `clash` 服务

```bash
sudo systemctl enable clash
sudo systemctl start clash
sudo systemctl status clash
```

- `clash` 运行起来之后，会在 `http://127.0.0.1:7890` 这个地址监听 `HTTP_PROXY` 服务
- 接下来需要为 Linux 配置代理，使所有的 http 请求，都经由这个代理服务进出
- Linux 提供了专门的环境变量 `http_proxy` 和 `https_proxy` 进行相关配置
- 采用以下命令设置并将其写入 `.bashrc` 文件中，使得每次开启终端都可以生效

```bash
echo -e "export http_proxy=http://127.0.0.1:7890\nexport https_proxy=http://127.0.0.1:7890" >> ~/.bashrc
```

- 重新打开个终端，配置即可生效

### Proxychains 安装配置
#### 下载安装
- 使用 `apt` 进行安装，命令如下：

```bash
sudo apt-get install proxychains
```

#### 代理配置
- 打开 `/etc/proxychains.conf` 文件

```bash
sudo gedit /etc/proxychains.conf
```

- 在文件最后改成相应的代理方式、地址和端口，根据 `clash` 的配置，修改如下：

```bash
http	127.0.0.1 7890
```

#### 代理使用
- 在命令前加 `proxychains` 即可
- 可以使用一下命令查看是否成功

```bash
 proxychains curl -kIsS https://www.google.com
```

### 写在最后
- 目前上述方法已在 `Ubuntu 18.04` 和 `Ubuntu 16.04` 上测试完成
- 若有其他问题欢迎评论指出
