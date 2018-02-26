# NFS

## NFS简介

> 网络文件系统**（英语：**Network File System**，缩写作 **NFS**）是一种分布式文件系统协议，最初由Sun Microsystems公司开发，并于1984年发布。其功能旨在允许客户端主机可以像访问本地存储一样通过网络访问服务器端文件。

> NFS和其他许多协议一样，是基于开放网络运算远程过程调用（ONC RPC）协议之上的。它是一个开放、标准的RFC协议，任何人或组织都可以依据标准实现它。

## 版本

>NFSv1 只在SUN公司内部用作实验目的。开发团队在NFSv1的基础上做了重大改进之后将其对外发布，版本NFSv2由此产生。

> NFSv2
>
> NFSv2最初在SunOS 2.0上面实现，1985年发布。
>
> 参与NFSv2设计实现的人包括罗素·桑德柏格（Russel Sandberg）、鲍伯·里昂（Bob Lyon）、比尔·乔伊、史提夫·克莱曼（Steve Kleiman）等。NFSv2 的定义RFC 1094，于1989年3月发布。
>
> NFSv2 最初只是基于 UDP。设计者旨在保持服务器端是无状态的，而将“锁”等机制的实现独立于核心协议之外。这是一个关键决定，它使从服务器故障恢复变得简单：当一个服务器变得不可用时，所有的网络客户端冻结，但一旦服务器恢复，每一个尝试重传的状态都包含在每个RPC里面，这是由客户端存根发起的。这样的设计决策允许UNIX应用程序可以忽视服务器端的问题。
>
> 虚拟文件系统接口很容易模块化地实现一个简单的协议。在1986年2月，诸多操作系统实现了对NFSv2的支持，例如 System V release 2、DOS，以及使用Eunice的VAX/VMS。
>
> 由于 32-bit 的限制，NFSv2 只允读写文件起始2G大小的内容。

> NFSv3
>
> Version 3（RFC 1813，1995年6月）添加如下功能：
>
> 1. 支持 64 bit 文件大小和偏移量，即突破 2GB 文件大小的限制；
> 2. 支持服务端的异步写操作，提升写入性能；
> 3. 在许多响应报文中额外增加文件属性，避免用到这些属性时重新获取；
> 4. 增加 READDIRPLUS 调用，用于在遍历目录时获取文件描述符和文件属性；
> 5. 其他改进。
>
> 在NFSv2发布后不久，NFSv3协议提案就在Sun Microsystems内部被提出，其主要目的是解决NFSv2进行同步写操作的性能问题。1992年7月的实现版本已经解决了NFSv2的许多不足之处，但是大文件支持（64位文件大小和偏移量）这一紧迫的问题还没有解决。这成为迪吉多公司的一个痛点，他们当时推出64位版本的Ultrix，以支持其新推出的64位RISC处理器Alpha 21064。在引入NFSv3时厂商们正在越来越多的支持TCP作为传输层协议。当时有些厂商已经在NFS version 2支持TCP做为传输层，Sun Microsystems 在发布NFSv3时也增加了将TCP作为传输层的支持。使用TCP做传输层使得NFS跨越 WAN 成为可能，并且可以突破 UDP 传输大小8K的限制，使用更大的读写数据单元。

> NFSv4
>
> NFSv4协议（RFC 3010，2000年12月；更新版 RFC 3530，2003年4月），借鉴了AFS（Andrew File System）和SMB/CIFS（Server Message Block）的特性，主要做了如下改进：性能提升，强制安全策略，引入有状态的协议。从NFSv4开始，协议的实现/开发工作不再是由SUN公司主导开发，而是改为由互联网工程任务组（IETF）开发。

> NFSv4.1
>
> NFSv4.1（RFC 5661，2010年1月）旨在为并行访问可横向扩展的集群服务（pNFS扩展）提供协议支持。

> NFSv4.2
>
> NFSv4.2 目前正在开发中。

## NFS启动及对外服务流程

1. 服务端启动rpcbind （NFS是基于RPC的服务，因为启动NFS的时候打开的服务器端口不确定，启动NFS的时候会向RPC服务注册启动的端口号，这样客户端就能通过RPC服务获取NFS的端口号从而连上NFS服务器，所以需要首先启动rpcbind service）
2. 服务端启动nfs service
3. 客户端开启rpcbind service (经测试centos6.9不需要开启rpcbind服务业可以使用)
4. 客户端请求nfs 服务
5. rpc服务返回nfs注册端口给客户端
6. 客户端获取nfs端口请求传输数据

## 环境

* NFS server:172.25.84.39  centos6.9 
* NFS client: 172.25.84.45   centos6.9

## 安装和配置

server:

```Shell
[root@servera ~]# yum groupinstall "NFS file server" -y

[root@servera ~]#[root@servera ~]# mkdir /data
[root@servera ~]# vi /etc/exports
[root@servera ~]# cat /etc/exports
/data   172.25.84.39(rw,sync)

[root@servera ~]# /etc/init.d/rpcbind start
Starting rpcbind:                                          [  OK  ]
[root@servera ~]# /etc/init.d/nfs start
Starting NFS services:                                     [  OK  ]
Starting NFS quotas:                                       [  OK  ]
Starting NFS mountd:                                       [  OK  ]
Starting NFS daemon:                                       [  OK  ]
Starting RPC idmapd:                                       [  OK  ]

[root@servera ~]# rpcinfo -p localhost
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100011    1   udp    875  rquotad
    100011    2   udp    875  rquotad
    100011    1   tcp    875  rquotad
    100011    2   tcp    875  rquotad
    100005    1   udp  54783  mountd
    100005    1   tcp  53856  mountd
    100005    2   udp  55093  mountd
    100005    2   tcp  36191  mountd
    100005    3   udp  35155  mountd
    100005    3   tcp  52726  mountd
    100003    2   tcp   2049  nfs
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    2   tcp   2049  nfs_acl
    100227    3   tcp   2049  nfs_acl
    100003    2   udp   2049  nfs
    100003    3   udp   2049  nfs
    100003    4   udp   2049  nfs
    100227    2   udp   2049  nfs_acl
    100227    3   udp   2049  nfs_acl
    100021    1   udp  60190  nlockmgr
    100021    3   udp  60190  nlockmgr
    100021    4   udp  60190  nlockmgr
    100021    1   tcp  37850  nlockmgr
    100021    3   tcp  37850  nlockmgr
    100021    4   tcp  37850  nlockmgr
[root@servera ~]# showmount -e localhost
Export list for localhost:
/data 172.25.84.39


```

Client:

```Shell
[root@CentOS6 ~]# mount -t nfs 172.25.84.45:/data /mnt
mount: wrong fs type, bad option, bad superblock on 172.25.84.45:/data,
       missing codepage or helper program, or other error
       (for several filesystems (e.g. nfs, cifs) you might
       need a /sbin/mount.<type> helper program)
       In some cases useful info is found in syslog - try
       dmesg | tail  or so

[root@CentOS6 ~]# yum install rpcbind nfs-utils -y
[root@CentOS6 ~]# mount -t nfs 172.25.84.45:/data /mnt
[root@CentOS6 ~]# df -h
Filesystem          Size  Used Avail Use% Mounted on
/dev/sda1           9.5G  1.5G  7.6G  17% /
tmpfs               931M     0  931M   0% /dev/shm
172.25.84.45:/data  9.5G  1.5G  7.6G  17% /mnt

```

## 常用文件及参数配置

* /etc/exports  --> NFS服务的主要配置文件
* /var/lib/nfs/etab --> 记录NFS分享出来的目录的完整权限设定值

/etc/exports的具体配置可以参考man exports的example

> ```shell
>    /               master(rw) trusty(rw,no_root_squash)
>    /projects       proj*.local.domain(rw)
>    /usr            *.local.domain(ro) @trusted(rw)
>    /home/joe       pc001(rw,all_squash,anonuid=150,anongid=100)
>    /pub            *(ro,insecure,all_squash)
>    /srv/www        -sync,rw server @trusted @external(ro)
>    /foo            2001:db8:9:e54::/64(rw) 192.0.2.0/24(rw)
>    /build          buildhost[0-9].local.domain(rw)
> ```

访问权限选项

- 设置输出目录只读：ro
- 设置输出目录读写：rw

用户映射选项

- all_squash：将远程访问的所有普通用户及所属组都映射为匿名用户或用户组（nfsnobody）；
- no_all_squash：与all_squash取反（默认设置）；
- root_squash：将root用户及所属组都映射为匿名用户或用户组（默认设置）；
- no_root_squash：与rootsquash取反；
- anonuid=xxx：将远程访问的所有用户都映射为匿名用户，并指定该用户为本地用户（UID=xxx）；
- anongid=xxx：将远程访问的所有用户组都映射为匿名用户组账户，并指定该匿名用户组账户为本地用户组账户（GID=xxx）；

注意用户权限分两类，nfs里的权限和共享目录的权限，尽量把共享目录改成nfsnobody属主和属组

Client端没权限创建文件：

```Shell
[root@CentOS6 ~]# cd /mnt
[root@CentOS6 mnt]# ll
total 0
[root@CentOS6 mnt]# touch a
touch: cannot touch `a': Permission denied

```

Server端查看并修改权限

```Shell
[root@servera data]# ls -ld /data/
drwxr-xr-x 2 root root 4096 Feb 26 19:08 /data/
[root@servera data]# chown nfsnobody.nfsnobody /data
[root@servera data]#

```

Client端重新创建文件：

```Shell
[root@CentOS6 mnt]# touch a
[root@CentOS6 mnt]# ll
total 0
-rw-r--r-- 1 nfsnobody nfsnobody 0 Feb 26 19:52 a
[root@CentOS6 mnt]#

```

客户端mount参数查看

```Shell
[root@CentOS6 ~]# grep mnt /proc/mounts
172.25.84.45:/data/ /mnt nfs4 rw,relatime,vers=4,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,port=0,timeo=600,retrans=2,sec=sys,clientaddr=172.25.84.39,minorversion=0,local_lock=none,addr=172.25.84.45 0 0

```

服务端默认配置参数查看,默认有个no_all_squash参数，建议生产环境配置all_squash在/etc/exports里面，这样非root用户创建的文件也会被压缩成nfsnobody（默认系统有nfsnodoby并且GID,UID是65535，centos5可能需要自己设置）

```Shell
[root@servera data]# cat /var/lib/nfs/etab
/data   172.25.84.39(rw,sync,wdelay,hide,nocrossmnt,secure,root_squash,no_all_squash,no_subtree_check,secure_locks,acl,anonuid=65534,anongid=65534,sec=sys,rw,root_squash,no_all_squash)

```

## 常用命令

* exportfs  [-aruv]

  * -a 全部挂载或卸载 /etc/exports中的内容 
  * -r 重新读取/etc/exports 中的信息 ，并同步更新/etc/exports、/var/lib/nfs/xtab
  * -u 卸载单一目录（和-a一起使用为卸载所有/etc/exports文件中的目录）
  * -v 在export的时候，将详细的信息输出到屏幕上

  ​

* rpcinfo -p nfsserver_ip

* showmount -e nfsserver_ip

* mount -o 参数 ip:/sharefolder /mnt

* umount -lf /mnt 强制卸载/mnt目录