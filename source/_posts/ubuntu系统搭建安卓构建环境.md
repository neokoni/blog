---
title: ubuntu系统搭建安卓构建环境
date: 2024-08-24 12:06:34
tags: 
    - 折腾
    - 教程
    - Android
    - 笔记
banner:
    type: img
    bgurl: ../picture/ubuntu搭建安卓14构建环境/banner.webp
cover: ../picture/ubuntu搭建安卓14构建环境/banner.webp
---
## 前言
之前在kvm虚拟机编译安卓，但是由于qemu虚拟磁盘的特性，最终磁盘文件大小已经大于设置的磁盘文件的最大容量，然后盘就满了  

kvm虚拟机就直接暂停了，也没法使用其他Linux的livecd，一挂载也会暂停  

使用各种办法挽救，速度忒慢，索性直接重建环境，比那些操作快，毕竟没什么重要文件  

`本篇算是一个笔记，给自己记录，方便以后使用`
## 环境介绍
系统: `ubuntu 24.04 LTS`  

## 更换源与更新系统
这个差不多都会吧，更换源在镜像站一般都有教程  
推荐两个[清华源](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)和[中科大源](https://mirrors.ustc.edu.cn/help/ubuntu.html)

更新系统:
```bash
sudo apt update && sudo apt upgrade
```
## 安装依赖
在这解释一下各个包，基础列表来自[安卓官方文档1](https://source.android.google.cn/docs/setup/start/initializing?hl=zh-cn#installing-required-packages-ubuntu-1804)和[安卓官方文档2](https://source.android.google.cn/source/initializing?hl=zh-cn#installing-the-jdk)  

然后是做出的修改，修正找不到包的问题，包括`git-core`，`libncurses5`和`lib32ncurses5-dev`，由于ubuntu已经占不到这些包了，所以替换成`git`，`libncurses6`和`lib32ncurses-dev`  

然后是同步源码的工具`repo`，不需要手动设置PATH来用，源里有，就是deb系的似乎更新比较慢，会报版本不是最新的警告，其他的发行版没见过有这个  

`ccache`这个在`build/envsetup.sh`没定义或手动启用是不会自动开的，介于大多类原生的编译脚本里有，所以也带了

然后是构建内核需要用的的包`libssl-dev`，解决编译内核时提示<openssl/bio.h>缺失的问题
```bash
sudo apt install git gnupg flex bison build-essential zip curl zlib1g-dev libc6-dev-i386 libncurses6 lib32ncurses-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig libssl-dev openjdk-8-jdk repo ccache
```
## 设置git信息
源码同步会提示要求设置git的信息，这里就不多说了，直接设置一下就行  
```bash
git config --global user.email 你的GITHUB邮箱(或其他?)
git config --global user.name  你的GITHUB用户名(或其他?)
```
## 初始化repo同步清单
首先，创建一个文件夹用来存放源码，然后进入这个文件夹，比如我这叫`android`
```bash
mkdir android && cd android
```
接着设置repo的下载地址，默认是被墙的谷歌源，我们可以替换为[清华大学的git-repo](https://mirrors.tuna.tsinghua.edu.cn/help/git-repo/)源
```bash
export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo'
```
接着就是初始化repo，比如我这里同步`risingOS`源码，可以执行这条命令。

这里`https://github.com/RisingTechOSS/android`是清单地址，在前面加上`https://mirror.ghproxy.com/`以使用国内加速，也可以[自建](https://github.com/hunshcn/gh-proxy)  

结尾加上`--depth=1`设置克隆深度为1,减少不必要的空间占用
```bash
repo init -u https://mirror.ghproxy.com/https://github.com/RisingTechOSS/android -b fourteen --git-lfs --depth=1
```
如果是AOSP官方源码或者lineages源码，可以使用国内镜像源，教程参考[清华源文档](https://mirrors.tuna.tsinghua.edu.cn/help/lineageOS/)

可用的源推荐[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)和[中科大源](https://mirrors.ustc.edu.cn/help/aosp.html)，不同之处就是替换`url`的问题

## 修改清单指向加速源
使用你喜欢的编辑器编辑`.rpeo/manifests`文件夹，依次编辑`default.xml`和存在的其他`.xml`文件

对于`https://github.com`的链接，在`链接`最前面加上`https://mirror.ghproxy.com/`

对于`https://android.googlesource.com`的链接，将`链接`更换为`AOSP镜像源`所要求的url

示例：
```xml
  <remote  name="aosp"
           fetch="https://mirrors.ustc.edu.cn/aosp"/>

  ...

  <remote name="staging"
          fetch="https://mirror.ghproxy.com/https://github.com/RisingOS-staging/"
          sync-c="true"
          sync-j="4"
          revision="refs/heads/fourteen" />

  <remote name="rising"
          fetch="https://mirror.ghproxy.com/https://github.com/RisingTechOSS/"
          sync-c="true"
          sync-j="4"
          revision="refs/heads/fourteen" />
```
## 同步源码
这一步应该都会，因为大多数的清单里都会包含同步源码

此处为[risingOS](https://github.com/RisingTechOSS/android?tab=readme-ov-file#getting-started)的清单里的同步命令
```bash
repo sync -c --no-clone-bundle --optimized-fetch --prune --force-sync -j$(nproc --all)
```
`-j`后的线程数这个命令是默认按照CPU的核心数，但是按照镜像源的说法，比如这里的中科大源，请酌情设置线程数


{% note warning %}
由于硬盘 I/O 资源有限，Git 服务器每 IP 限制 5 个并发连接。 repo sync 命令默认使用 4 个并发连接，请勿使用 -j 参数增加并发连接数。
{% endnote %}

## 小脚本？
虽然设置完加速与镜像后，可以做到不开`魔法`也可以顺利克隆，但是如果你的网络实在不好，容易失败

这里有个简单的`bash脚本`可以帮助你自动重试，直到同步成功或手动退出
```bash
#!/bin/bash

echo "======start repo sync======"

# 执行 repo sync 命令
repo sync

# 使用 while 循环检查命令的退出状态
while [ $? -eq 1 ]; do
    echo "======sync failed, re-sync again======"
    sleep 3
    repo sync
done
```
使用方法就是将脚本保存为`repo_sync.sh`，然后`chmod +x repo_sync.sh`，然后执行`./repo_sync.sh`即可
