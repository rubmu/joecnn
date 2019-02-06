---
title: VirtualBox共享文件
date:  2018/10/16
tags: []
---
`VirtualBox` 与 `Linux` 虚拟机 可以通过 `VBoxGuestAdditions` 插件进行文件共享。

##### 1. 设置共享文件夹
在 `VirtualBox` 的**共享文件夹**设置中添加设置。

##### 2. 安装 VBoxGuestAdditions 插件
> 需要在 `linux` 虚拟机中安装

* 将 `$VirtualBox\VBoxGuestAdditions.iso` 装载到光驱

* 挂载 cdrom：
```bash
$ mkdir /media/cdrom && mount /dev/sr0 /media/cdrom
```

* 安装插件：
```bash
$ cd /media/cdrom &&  ./VBoxLinuxAdditions.run
```

##### 3. 验证挂载
```
$ mount -t vboxsf share ~/share
$ df | grep 'share'
```

##### 4. 问题与解决
- mount: unknown filesystem type 'vboxsf'.
 > 未成功安装 VBoxGuestAdditions 插件

- VBoxGuestAdditions 安装失败.
 > 查看 /var/log/vbxadd-install.log 错误信息，一般为依赖的包未安装  
 > `$ yum install gcc kernel-devel kernel-headers dkms make bzip2`

- ERROR: Kernel configuration is invalid.
 > 此错误是编译的 VBoxGuestAdditions 版本太老，新的 kernel 不必指定include/linux/autoconf.h，更新virtualbox即可

- Please install the Linux kernel "header" files matching the current kernel for adding new hardware support to the system.
 > 此问题为默认安装的 kernel-devel 包与当前内核版本不同，重新安装对应包即可  
 > `$ sudo yum install -y "kernel-devel-$(uname -r)"`