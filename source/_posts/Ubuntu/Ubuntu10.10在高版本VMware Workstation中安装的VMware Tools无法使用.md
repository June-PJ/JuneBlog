---
title: Ubuntu10.10在高版本VMware Workstation中安装的VMware Tools无法使用
date: '2023-04-21 10:28:16'
description: "高版本VMware Workstation的VMware Tools不兼容问题"
cover: https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/Ubuntu.jpg
categories:
  - Ubuntu
tags:
  - Ubuntu
  - VMware
  - VMware Tools
---

## 1.原因

高版本VMware Workstation中下载的VMware Tools镜像与Ubuntu10.10不适配

## 2.解决方法

### 2.1 降低版本

将高版本的VMware Workstation更换为低版本的，如VMware Workstation Pro 12

### 2.2 下载open-vm-tools

要下载open-vm-tools，需要更换apt的国内软件源

首先执行以下命令：

```bash
sudo gedit /etc/apt/sources.list
```

然后将以下代码粘贴覆盖原文件：

```plaintext
# deb cdrom:[Ubuntu 10.10 _Maverick Meerkat_ - Release i386 (20101007)]/ maverick main restricted
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.

deb http://old-releases.ubuntu.com/ubuntu maverick main restricted
deb-src http://old-releases.ubuntu.com/ubuntu maverick main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://old-releases.ubuntu.com/ubuntu maverick-updates main restricted
deb-src http://old-releases.ubuntu.com/ubuntu maverick-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://old-releases.ubuntu.com/ubuntu maverick universe
deb-src http://old-releases.ubuntu.com/ubuntu maverick universe
deb http://old-releases.ubuntu.com/ubuntu maverick-updates universe
deb-src http://old-releases.ubuntu.com/ubuntu maverick-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to 
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://old-releases.ubuntu.com/ubuntu maverick multiverse
deb-src http://old-releases.ubuntu.com/ubuntu maverick multiverse
deb http://old-releases.ubuntu.com/ubuntu maverick-updates multiverse
deb-src http://old-releases.ubuntu.com/ubuntu maverick-updates multiverse

## Uncomment the following two lines to add software from the 'backports'
## repository.
## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any
```

然后执行以下命令更新软件源：

```bash
sudo apt-get update
```

接下来执行以下命令进行更新：

```bash
sudo apt-get upgrade
```

最后执行以下命令安装open-vm-tools：

```bash
sudo apt-get install open-vm-tools
```

## 相关链接

**本文可能涉及的代码出自以下博客文章，十分感谢下面各位大佬的分享**

落日蘑菇-[vmware tools 失效问题解决方式（Ubuntu 22 以及其他系统）](https://zhuanlan.zhihu.com/p/548394510)