# Linux 与虚拟化管理(PVE)

## PVE

PVE(Proxmox VE)是一个基于debian进行二次封装开发的一个虚拟机管理程序，可以托管虚拟机还有lxc容器环境。

### Cloud Image

Cloud-image 可以理解为一种已经装好系统后的磁盘打包后的镜像，它不同于常规的iso server镜像，你需要进行复杂的安装过程，配置内容，才能进行使用，在一些云服务商内，我们基本是很快做到系统安装的，这里就是采用了cloud-image，它可以直接挂在到虚拟机磁盘上，直接作为一个操作系统使用，目前很多发行版提供了cloud-image镜像

### Cloud Init

CloudInit在pve中，类似于在安装系统时候，能够让你快速进行初始化一些内容。比如最常见的用户名与密码，ip地址获取的方式，ssh公钥配置等。

对于支持Cloud Init的Cloud Image 镜像，快速使用Cloud Init初始化系统的方式如下：

1. 点击`硬件`，点击新增`cloud init 设备`

2. 选择磁盘池后点击创建

3. 进入cloud init 栏目，填入初始化的信息

4. 点击重新生成镜像

5. 进入系统，已经初始化好了


当初始化完成后，想进行修改，在cloud init里面修改后，点击重新生成镜像即可

   

## LXC与Docker容器 

从PVE 9.1开始，PVE的LXC容器正式支持OCI标准容器，即可以接入任何符合OCI标准的容器，Docker里绝大多数的镜像就是OCI镜像。

LXC容器与Docker容器殊途同归，甚至在实现方式上都是类似的，他们都是共享宿主机内核，隔离用户态执行。但是LXC容器就是与PVE生态高度集成，虽然利用的是OCI容器标准，但是每个容器你可以想管理虚拟机一样进行管理，进行快照，统一管理模式。

## Linux

### 分区扩容（非LVM）

在使用pve时候，有时候使用cloud image 装完系统后，系统的磁盘过小。这个时候进行扩容的化，首先在pve进行扩容磁盘后，会发现在系统内空间仍然不足，这个时候使用命令lsblk进行查看，发现磁盘却是是扩了，但是系统并没有使用到空闲区域。

```shell
root@debian:~# lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda       8:0    0   53G  0 disk
├─sda1    8:1    0  2.9G  0 part /
├─sda14   8:14   0    3M  0 part
└─sda15   8:15   0  124M  0 part /boot/efi
sr0      11:0    1    4M  0 rom
sr1      11:1    1 1024M  0 rom
```



这个时候需要把空闲区域进行分配给这个磁盘，这个时候我们会使用到一个工具，就是`growpart`，首先使用这个工具进行扩展分区。

```shell
growpart /dev/sda 1
```

然后进行df -Th 进行分区的挂载点查看，我的为ext4。

然后执行下面点命令进行扩展文件系统。

```shell
resize2fs /dev/sda1
```

然后再次进行查看df -h

这个时候发现扩容已经成功了。

（对于部分的系统，如Debian-Generic-Cloud版本的cloud-image，当你扩容扣会自动执行上面的操作）

## 创建虚拟机

创建虚拟机中，基本创建的流程略过。其中最为重要的是对于Cloud-Image形式安装的操作系统的磁盘，在创建虚拟机时的磁盘，后面需要删除，删除后，导入qcow镜像，再走上面的流程进行扩容。

