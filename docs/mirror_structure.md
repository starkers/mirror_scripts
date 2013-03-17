# 各发行版软件源结构描述

## 综述
各发行版软件源的组织结构和所需服务各有不同，基本分为以下几类:

1. 按硬件结构分类(arch)
2. 按版本号分类(Redhad系)
3. 不分类 (debian系)

本文将简要介绍各主要发行版的软件源中软件包组织结构和同步时的部分同步方法。

## Red Hat系列
Red Hat系列按发行版版本号组织软件包，但仍有一些区别，例如CentOS的结构基本如下
```
|-5.9
   |- addons
        |- i386
        |- x86_64
   |- contrib
        |- i386
        |- x86_64
|- 6
  ....
```
而 Fedora 的一级目录则是`core` （Fedora Core时代的版本），`development`（开发版和rawhide），`releases`和`updates`是分开的，前者是发行版内软件包，后者是差分包，二级目录才是版本。

因此，参见`scripts/fedora.sh`, releases和updates分成两个同步进程并行同步，并且只同步最近3个发行版。


## Debian系列
Debian、Ubuntu、Debian-security等debian系发行版的组织结构主要包括`dists`和`pool`两个文件夹。

`dists`内放当前各个版本的各个仓库的软件包列表和基本信息，还包括netinstall工具等。
`pool`内按仓库存放分类，再按字母顺序排序摆放软件包的 **文件夹**，每个文件夹内，包括该软件包的所有版本和所有架构。

可见，Debian系列若要实现"按版本部分同步"的难度很大，需要读取软件包列表并精确到软件包级别的同步，因此一般都是按架构实现部分同步。即忽略部分架构的软件包。

根据debian官方推荐的同步方式，debian软件源需要经过两次同步，第一次下载所有更新的软件包并且保留旧版本，第二次下载元数据，并同步删除。这个步骤在现版本的同步脚本中被忽略了，需要在下个版本中补上。

## Archlinux
Arch的软件源先按架构分成`i386`和`x86_64`两个文件夹，再按仓库分为多个仓库文件夹，需要注意的是，软件仓库元数据与软件包混放在一起，软件包不再排序分到各个文件夹而是直接放在仓库根目录。各个软件包实际上是指向`pool`这个文件夹内的软链接。

## Gentoo
举了三个例子了，Gentoo的自己研究去。
需要注意的是，`gentoo-portage`的客户端是使用rsync同步软件包列表的(因为没有必要有"软件包列表"这个东西)，因此服务器上必须开rsyncd并包含`gentoo-portage`的配置。
