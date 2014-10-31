---
layout: post
title: 制作openstack用的centos6.5镜像
category: cloud
description: 制作openstack用的centos6.5镜像

---

Author:[Hyphen](http://weibo.com/344736086)



### 目的：

在centos6.5操作系统环境下制作一个centos6.5的kvm镜像,安装cloud-init,能自动扩展根分区


### 一、制作环境：

操作环境是在openstack平台开一个实例，装的是centos6.5，镜像来自：http://cloud.centos.org/centos/6.5/images/CentOS-6-x86_64-GenericCloud-20140929_01.qcow2

centos社区制作的镜像，不支持自动扩展根分区，导致创建实例时不论你指定硬盘大小是多大，它都是7G多点。


### 二、安装软件：


yum groupinstall Virtualization "Virtualization Client"
yum install libvirt libguestfs-tools

### 三、制作过程：

#### 1、下载一个最小的centos6.5的iso文件：
wgethttp://mirrors.163.com/centos/6.5/isos/x86_64/CentOS-6.5-x86_64-minimal.iso

#### 2、创建一个空的镜像文件：
	qemu-img create -f qcow2 centos-6.5.qcow2 5G
#### 3、创建命令：
	virt-install  --name centos-6.5 --ram 1024 --cdrom=CentOS-6.5-x86_64-minimal.iso --disk centos-6.5.qcow2,format=qcow2 --grap	hics vnc,listen=0.0.0.0 —noautoconsole --os-type=linux --os-variant=rhel6
#### 4、系统安装过程：
分区只分一个,挂载到“/”，格式为ext4；
不要swap,boot等分区
#### 5、初始化镜像：
##### (1)安装完系统后，点击重启，其实在virsh 命令下看这个虚拟机，已经是关机状态了，要用命令启动它
    virsh start centos-6.5
注意：启动过程非常慢，只能等待，不要在启动时重启它，不然出问题。
##### (2)通过vnc-viewer连接过去，如果你有跑多个虚拟机，可以用下面的命令来查看这个虚拟机的vnc端口
	virsh vncdisplay centos-6.5
##### (3)要使nova console-log 能将实例启动过程输出到实例启动日志中，要在文件/boot/grub/menu.lst 中kernel参数中增加下面的内容:
    kernel ...（省略n个参数）... console=tty0 console=ttyS0,115200n8
##### (4)修改网络信息 /etc/sysconfig/network-scripts/ifcfg-eth0 （删掉mac信息)如下:
	TYPE=Ethernet
	DEVICE=eth0
	ONBOOT=yes
	BOOTPROTO=dhcp
	NM_CONTROLLED=no
    增加一行到/etc/sysconfig/network ：
	NOZERCONF=yes
	
为了使实例支持两张网卡,复制多一个ifcif-eth1文件:
	
	cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth1
	sed -i "s/eth0/eth1/" /etc/sysconfig/network-scripts/ifcfg-eth1
##### （5）增加epel源、更新系统，安装git：
	yum install -y http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
	yum -y distro-sync
	yum -y install git
##### （6）安装ACPI服务，能让宿主机对虚拟机进行开关机等电源管理操作
	yum install acpid
	chkconfig acpid on
##### （7）安装linux rootfs resize，使得实例启动时可以自动扩展根分区
	cd /tmp
	git clone https://github.com/flegmatik/linux-rootfs-resize.git
	cd linux-rootfs-resize
	./install
##### （8）安装cloud-init
	yum install -y cloud-utils cloud-init parted
   修改配置文件/etc/cloud/cloud.cfg ，在cloud_init_modules 下面增加:        
   	
   	- resolv-conf
##### （9）卸载epel源,关机：	
	poweroff
##### 6、善后操作
###### （1）清除网络相关硬件生成信息
	virt-sysprep -d centos-6.5
###### （2）压缩镜像
	virt-sparsify --compress /tmp/centos-6.5.qcow2 centos-6.5-cloud.qcow2
镜像制作到此结束

##### 参考文档：
[http://docs.openstack.org/image-guide/content/centos-image.html#d6e899](http://docs.openstack.org/image-guide/content/centos-image.html#d6e899)
[https://openstack.redhat.com/Creating_CentOS_and_Fedora_images_ready_for_Openstack](http://docs.openstack.org/image-guide/content/centos-image.html#d6e899)