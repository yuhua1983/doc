#rsync

##rsync简介

> rsync是Unix下的一款应用软件，它能同步更新两处计算机的文件与目录，并适当利用差分编码以减少数据传输量。rsync中的一项同类软件不常见的重要特性是每个目标的镜像只需发送一次。rsync可以拷贝／显示目录内容，以及拷贝文件，并可选压缩以及递归拷贝。
>
> 在常驻模式（daemon mode）下，rsync默认监听TCP端口873，以原生rsync传输协议或者通过远程shell如RSH或者SSH提供文件。SSH模式下，rsync客户端运行程序必须同时在本地和远程机器上安装。
>
> rsync是以GNU通用公共许可证发行的自由软件。

[rsync官网](https://rsync.samba.org/)

## rsync模式

>
> ```Shell
>    Local:  rsync [OPTION...] SRC... [DEST]       #本地模式，不加目标参数类似ls
>
>
>    Access via remote shell:       #远程模式，实现不同server间的数据传输
>      Pull: rsync [OPTION...] [USER@]HOST:SRC... [DEST]
>      Push: rsync [OPTION...] SRC... [USER@]HOST:DEST
>      
>    Access via rsync daemon:       #使用rsync进程传输数据，需要一方启动rsync daemon服务
>      Pull: rsync [OPTION...] [USER@]HOST::SRC... [DEST]
>            rsync [OPTION...] rsync://[USER@]HOST[:PORT]/SRC... [DEST]
>      Push: rsync [OPTION...] SRC... [USER@]HOST::DEST
>            rsync [OPTION...] SRC... rsync://[USER@]HOST[:PORT]/DEST
>
>    Usages with just one SRC arg and no DEST arg will list the source files
>    instead of copying.
> ```

例子:

* 本地

```shell
[userb@serverb ~]$ rsync .
drwx------        4096 2018/02/28 13:02:26 .
-rw-r--r--          18 2017/03/23 08:15:00 .bash_logout
-rw-r--r--         176 2017/03/23 08:15:00 .bash_profile
-rw-r--r--         124 2017/03/23 08:15:00 .bashrc
```



* 通过ssh远程传输，注意/home/userb/userbdir后面加不加“/”结果是不一样的，不加“/”表示传输整个userbdir目录，包括userbdir目录本身，加上“/”表示只传输 userbdir目录下面的内容

```shell
[userb@serverb ~]$ rsync -avz /home/userb/userbdir  usera@servera:/home/usera/  
The authenticity of host 'servera (192.168.122.10)' can't be established.
RSA key fingerprint is 46:bf:a8:14:ee:ad:3c:9f:b9:d4:82:31:ea:b2:82:ad.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'servera,192.168.122.10' (RSA) to the list of known hosts.
usera@servera's password:
Permission denied, please try again.
usera@servera's password:
sending incremental file list
userbdir/
userbdir/1
userbdir/10
userbdir/2
userbdir/3
userbdir/4
userbdir/5
userbdir/6
userbdir/7
userbdir/8
userbdir/9

sent 495 bytes  received 206 bytes  66.76 bytes/sec
total size is 0  speedup is 0.00

[userb@serverb ~]$ rsync -avz /home/userb/userbdir/  usera@servera:/home/usera/
usera@servera's password:
sending incremental file list
./
1
10
2
3
4
5
6
7
8
9

sent 469 bytes  received 205 bytes  192.57 bytes/sec
total size is 0  speedup is 0.00

```

* 通过rsync daemon服务传输需要有一台开通rsync daemon进程的机器作为服务器。servera作为rsync server，serverb作为client

```Shell
[userb@serverb ~]$ rsync -avz a rsync_user@servera::backup --password-file=/home/userb/rsync.password
sending incremental file list

sent 37 bytes  received 8 bytes  90.00 bytes/sec
total size is 0  speedup is 0.00

```



