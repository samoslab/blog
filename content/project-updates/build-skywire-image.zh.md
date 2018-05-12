

+++
title = "Samos -- 如何打造Skycoin miner的镜像"
tags = [
    "project",
]
date = "2018-05-05"
categories = [
    "developement",
]
description = "如何打造Skycoin miner的镜像."
+++

![screenshot](https://user-images.githubusercontent.com/26845312/32426764-3495e3d8-c282-11e7-8fe8-8e60e90cb906.png)



# 编译原始镜像

从github上下载源码

https://github.com/samoslab/OrangpiH5


编译环境:Ubuntu 14.04.5 LTS

不建议使用root用户进行编译.

OrangpiH5的源码根目录下输入

./build.sh

进入编译画面后

图片: 

![screenshot](/img/skywire-miner/1.png)

1 OrangePi Prima(internal version)

进入画面

图片: ![screenshot](/img/skywire-miner/2.png)

输入用户的root密码.

进入画面

图片: ![screenshot](/img/skywire-miner/3.png)

按照需要进行编译,但首次编译的顺序必须是按如下顺序,否则会报错.

2->4->2->3->5->1->0

当最后执行完0操作后.

最终生成镜像文件为 

out/OrangePiH5_Prima.img

# 制作安装镜像

把上一步生成的OrangePiH5_Prima.img拷贝到一个工作目录下.

mkdir temp

mount -o loop,offset=73400320 OrangePiH5_Prima.img   temp

- 设置镜像类型

输入命令

nano temp/etc/bootsamos.sh

看到如下脚本:

cd /usr/local/skywire/go/bin

#nohup ./manager -web-dir /usr/local/skywire/go/src/github.com/skycoin/skywire/static/skywire-manager > /dev/null 2>&1 &

sleep 3

nohup ./node -connect-manager -manager-address 192.168.0.2:5998 -manager-web 192.168.0.2:8000 -discovery-address discovery.samos.io:5999-021192b6e41286d6799cb7ebf8c65ccc24c5d0bf0c9d98d6c48b6$

cd /

如果该镜像为manager镜像,则把一下这句的#号去掉,自动启动manager

#nohup ./manager -web-dir /usr/local/skywire/go/src/github.com/skycoin/skywire/static/skywire-manager > /dev/null 2>&1 &

修改后直接保存退出

如果该镜像为node则直接跳过这一步.

- .设置镜像IP

输入命令

nano temp/etc/network/interfaces.d/eth0 

看到如下配置

auto eth0

  iface eth0 inet static

\#    address 192.168.0.2   (manager)

\#   hwaddress ether 00:21:3c:32:f4:42 (manager)

\#    address 192.168.0.3 (node1)

\#    hwaddress ether 00:21:3c:32:f4:43 (node1)

\#    address 192.168.0.4 (node2)

\#    hwaddress ether 00:21:3c:32:f4:44 (node2)

\#    address 192.168.0.5 (node3)

\#    hwaddress ether 00:21:3c:32:f4:45 (node3)

\#    address 192.168.0.6 (node4)

\#    hwaddress ether 00:21:3c:32:f4:46 (node4)

\#    address 192.168.0.7 (node5)

\#    hwaddress ether 00:21:3c:32:f4:47 (node5)

\#    address 192.168.0.8 (node6)

\#    hwaddress ether 00:21:3c:32:f4:48 (node6)

\#    address 192.168.0.9 (node7)

\#    hwaddress ether 00:21:3c:32:f4:49 (node7)

    netmask 255.255.255.0

    gateway 192.168.0.1

dns-nameservers 192.168.0.1 8.8.8.8

根据需要编译的目标节点按照(...)中的指示把相应的#号去掉.例如需要编译manager

则把

\#    address 192.168.0.2 (manager)

\#   hwaddress ether 00:21:3c:32:f4:42 (manager)

这两句前面的#去掉

修改完成后保存退出

- .生成目标镜像

输入如下命令保存已修改镜像

sync

umount temp

目标镜像img修改完成.

- .压缩发布目标镜像

输入如下命令

tar -zcvf 目标目录/目标文件名.tar.gz OrangePiH5_Prima.img

此处分别给出manager和node的全部压缩命令样例

tar -zcvf dist-manager/manager.tar.gz OrangePiH5_Prima.img

tar -zcvf dist-manager/node-1-03.tar.gz OrangePiH5_Prima.img

tar -zcvf dist-manager/node-2-04.tar.gz OrangePiH5_Prima.img

tar -zcvf dist-manager/node-3-05.tar.gz OrangePiH5_Prima.img

tar -zcvf dist-manager/node-4-06.tar.gz OrangePiH5_Prima.img

tar -zcvf dist-manager/node-5-07.tar.gz OrangePiH5_Prima.img

tar -zcvf dist-manager/node-6-08.tar.gz OrangePiH5_Prima.img

tar -zcvf dist-manager/node-7-09.tar.gz OrangePiH5_Prima.img

至此所有的目标镜像全部发布生成完毕.

# 安装

- .windows:

从官网下载已发布的tar.gz文件

解压得到镜像文件OrangePiH5_Prima.img

下载Win32DiskImager安装运行

图片: ![screenshot](/img/skywire-miner/4.png)

映像文件选择OrangePiH5_Prima.img,设备选择目标TF卡.其他必须按图中的配置

然后点击写入,等待进度条完成.把TF卡取入插入目标矿机.

浏览器打开 http://192.168.0.2:8000. 默认密码是: 1234.

- .linux:

输入如下命令,按照实际修改自己的目标盘

dd if=OrangePiH5_Prima.img of=/dev/目标盘

注意:

* .矿机第一次启动比较慢请耐性等候,建议等待3分钟.

* .TF卡容量需要比镜像大.当完成初始化后,系统容量自动扩容到目标TF卡容量,无需
用户再自行配置.

* .node可以按照用户需要自行选择安装的个数,但是必须至少具备一个manager.



# QA

- .编译过程中出现错误

output/pack/out/u-boot.fex cant be open

create package failed

ERROR: dragonsecboot boot_package.cfg failed

解决:没有按照编译顺序执行先编译uboot

- .编译过程中出现错误

make[3]: *** 没有规则可以创建“drivers/input/misc/input_demo.o”需要的目标“drivers/input/misc/input_demo.c”。 停止。

make[2]: *** [drivers/input/misc] 错误 2

make[1]: *** [drivers/input] 错误 2

make: *** [drivers] 错误 2

解决:vi kernel/drivers/input/misc/Makefile

找到obj-m += input_demo.o 删除

- .编译过程出现错误

kernel/modules.builtin no found 

解决:没有按照编译顺序执行先编译kernel

- .编译过程出现错误

I: Running command: chroot rootfs /debootstrap/debootstrap --second-stage

chroot: 无法运行命令"/debootstrap/debootstrap": Exec format error

解决:安装如下插件

apt-get install fakeroot

apt-get install debootstrap

apt-get install schroot

apt-get install binfmt-support qemu qemu-user-static u-boot-tools

# 加入telegram

Samos: https://t.me/samosnetwork

Skycoin: https://t.me/Skycoin





