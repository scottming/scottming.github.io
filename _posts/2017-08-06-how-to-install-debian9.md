---
layout: post
title: 如何安装 Debian 9?
subtitle:   
date:       2017-08-06
author:     "Scott"
header-img: "img/160403-debian.png"
catalog:    true
tags:
    - Linux
---

上周 GPU 服务器抽风，老是突然卡死。Ubuntu 总是充满了不稳定，而 Debian 则是稳定的代名词，于是趁周末时间+周一，把服务器重装为 Debian 9。特别感谢 [ibrother](https://github.com/ibrother)，没有他的帮助，搞不定这么复杂的配置。

## 下载 Debian 发型版

建议准备两个 U 盘做启动盘，一个用于体验，一个用于安装系统。

制作 U 盘工具推荐 [Rufus - Create bootable USB drives the easy way](https://rufus.akeo.ie/)，如果无 windows 可以参考一下这个答案 [如何在 Mac 下制作启动 U盘](https://www.zhihu.com/question/19630406/answer/29334393)。

发型版推荐 Debian 9.1.0 KDE，杀手级应用都在 KDE 上，体验盘建议安装这个 [xfce-cD](http://mirrors.ustc.edu.cn/debian-cd/current/arm64/iso-cd/debian-9.1.0-arm64-xfce-CD-1.iso)，安装盘安装 [netinst](http://iso.mirrors.ustc.edu.cn/debian-cd/current/arm64/iso-cd/debian-9.1.0-arm64-netinst.iso)。

## 安装

U 盘 live 体验没问题，即可开始安装。

#### 1. 选择安装模式

进入系统之后，看底部提示，进入 BISO 或 UEFI，然后选择 U 盘作为启动盘， 选择「高级选项」，然后「专家模式」。

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian01.JPG?imageView2/2/w/800)

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian02.JPG?imageView2/2/w/800)


#### 2. 选择语言

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian03.JPG?imageView2/2/w/800)


选择 English - English，然后选择位置，other -> Asia -> China，接着选择 locales，这里一定不能选中文，选择 United Staates - en_US.UTF-8，然后 configure locales，空格选择以下 7 种：

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian04.JPG?imageView2/2/w/800)

最后选择 en_US.UTF-8 作为系统语言。

#### 3. 选择键盘、检测过载 CD

American English，检测挂载，回车即可

#### 4. 从 CD 加载安装程序组件

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian05.JPG?imageView2/2/w/800)


空格选择以下 10 种：

* choose-mirror
* event-modules
* fuse-modules
* parted-udeb
* ppp-modules
* ppp-udeb
* sound-modules
* squashfs-modules
* udf-modules
* virtia-modules

然后 Continue

#### 5. 网络和镜像

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian06.JPG?imageView2/2/w/800)

检测网络硬件，然后如果是 pppoe 则选择 pppoe，非则直接选择 configure the network，一直回车，然后选择镜像，选择中科大 http -> mirros.ustc.edu.cn。

#### 6. 设置用户

这里按正常设置就好，开启隐藏密码模式，不允许 root 登录。

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian07.JPG?imageView2/2/w/800)


#### 7. 选择时钟

直接选择 NTF 即可。

#### 8. 分区

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian09.JPG?imageView2/2/w/800)

简单点可以选择第二个 Guied - use entire disk and set up LVM，复杂点就选 use entire disk and set up encrypted LVM(我服务选这个，一直提示 boot 安装不成功)，然后强制写入 UIFE，选择 NO，分区方案，选 择第 3 个 Separate /home, /var and /tmp partitions，系统盘选择格式化。其他默认即可。

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian10.JPG?imageView2/2/w/800)

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian11.JPG?imageView2/2/w/800)

#### 9. 安装基础系统

选择 linux-image-amd64 -> target inclued drivers needed for the system。

![](http://7xjuve.com1.z0.glb.clouddn.com/blog/170805-debian12.JPG?imageView2/2/w/800)


#### 10. 管理包配置

这里很简单，选择中科大镜像即可，另外 enable source repositories 那个地方选 NO，最后默认 continue 即可。

#### 11. 选择和安装软件

需要桌面的话，则选则：

* Debian desktop environment
* KDE
* print server
* SSH server
* standard system utilities

不需要桌面的话，直接选择最后两个就行。

#### 12. 安装 GRUB boot

这里注意把 GRUB 安装到启动硬盘，然后 Force GRUB installation to the EFI removable media path，选择 no，最后结束安装，黑屏后拔掉 U 盘即可。

## 其他配置

* cuda-dev 和 cuda-tookit 可以直接用 apt-get 下载，然后下载 cudnn，添加到环境变量，另外需创建软链。
* 高效工具推荐 [int32bit/dotfiles: A set of vim, zsh, git, and tmux configuration files.(*nix开发环境一键配置）😀](https://github.com/int32bit/dotfiles)，尤其是 fzf / ag 等工具，简单而强大。
* Vim 推荐 [wklken/k-vim: vim配置](https://github.com/wklken/k-vim)
* 无线网卡推荐这款 [jurobystricky/Netgear-A6210: AC1200 High Gain WiFi USB Adapter Linux kernel driver](https://github.com/jurobystricky/Netgear-A6210)





