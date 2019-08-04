# 三 linux常用命令 下

## 4 帮助命令

**遇到问题怎么办？**
- 不要急于问别人，自己尝试，网络搜索

###  查看帮助文档man
- 原意：manual
- 路径：/usr/bin/man
- 权限：所有用户
```
    man [命令或配置文件]
    获得帮助，可以用/查找
    配置文件不能用绝对路径

命令：
- 查看命令的作用和格式
- 查看命令选项的作用和格式

配置文件：
- 查看配置文件的作用
- 查看配置文件的格式，方便读懂和修改配置文件

既是配置文件也是命令：
1命令的帮助
5配置文件的帮助
man 5 passwd 查看passwd配置文件的帮助
```
```
执行指令man services的时候出现：“没有 services 的手册页条目”。
解决办法：安装man帮助文档包。指令如下：
sudo yum install man-pages
```
```
whatis和apropos
查看简短的命令和配置文件用途信息

    whatis 命令
    apropos 配置文件

查看命令的选项 --help

    date查看当前系统时间
    date 时间（格式看文档）修改系统时间
```

#### info 类似帮助命令

### shell内置命令帮助 help
- 路径：shell内置命令
- 权限：所有用户
```
    help 命令
    获得shell内置命令的帮助文档
```
判断内置命令：
```
    type 命令
    查看命令类型
```

## 5 用户管理命令

### 添加用户useradd
- 权限：root
```
    useradd 用户名
    添加新用户
```

### 设置密码passwd
- 权限：root
```
    passwd 用户名
    更改用户密码
```

### 查看目前登录用户who
- 权限：root
```
    who
    查看目前登录的用户
```
用户名|终端|登录时间|ip
--|--|--|--
root|tty本地终端；pts远程终端|精确到分|远程登录ip

### 详细用户登录信息w
- 权限：root查看所有，其他查看自己
- w 查看目前登录的用户详细信息

11:29:13|up 1 day,| 18:47,|  1 user,|  load average: 0.00, 0.01, 0.05
--|--|--|--|--
当前系统时间| up 系统运行时间，|开机时间，|用户数，|平均负载：1min，5min，15min

USER|     TTY|      FROM|LOGIN@|   IDLE|   JCPU|   PCPU| WHAT
--|--|--|--|--|--|--|--
用户|     登录端|   ip|               登录时间| 空闲时间| 累计占用CPU时间| 当前任务占用CPU时间| 任务名称
root|     pts/0|    101.206.167.109|  11:07 |   1.00s | 0.03s | 0.00s| w

## 6 压缩解压命令

### 常见压缩格式对比

#### 按压缩比
由低到高

#### 原文件是否保留
- 保留
- 不保留

### 压缩命令一览
- .gz压缩文件
    - 压缩：`gzip 文件名`
    - 解压：`gunzip 压缩包.gz（gzip -d 压缩包.gz）`
- .tar打包目录
    - 打包：`tar -cvf 包名.tar 目录`
    - 解包：`tar -xvf 包名.tar`
- .tar.gz
    - 压缩：`tar -zcvf 压缩包名.tar.gz 目录`
    - 解压：`tar -zxvf 压缩包名.tar.gz`
- .bz2高压缩文件
    - 压缩：`bzip2 文件名`
    - 解压：`bunzip2 压缩包.bz2`
- .tar.bz2
    - 压缩：`tar -jcvf 压缩包名.tar.bz2 目录`
    - 解压：`tar -jxvf 压缩包名.tar.bz2`
- .zip
    - 压缩：`zip -q 压缩包名.zip 文件/zip -qr 压缩包名.zip 目录`
    - 解压：`unzip 压缩包.zip`
- .rar(需要下载专用解压工具)

### 压缩文件 gzip
适用于.gz文件
- 压缩：`gzip 文件名`
- 解压：`gunzip 文件名（gzip -d 文件名）`

注意：
- gzip只能压缩文件，没法压缩目录
- 压缩后原文件不会保留

### 压缩目录 tar
适用于.tar/.tar.gz文件
```
打压缩包：tar -zcvf 新文件名.tar.gz 目录名
              -z 压缩
              -c 打包
              -v 显示详细信息
              -f 指定文件名

解压缩包：tar -zxvf 压缩文件名.tar.zip
          -z 解压
          -x 解包
          -v 显示详细信息
          -f 指定解压文件
```
注意：
- 可以压缩目录
- 原目录会保留

### zip
适用于.zip格式

windows和linux默认通用
```
压缩：  zip -qr 压缩后的文件名.zip 文件或目录
            -q 安静模式，不显示压缩过程
            -r 压缩目录
解压：  unzip 压缩文件.zip
```
注意：
- 原文件保留
- 提示压缩比（没有gzip高）

### bzip2
适用于.tar.bz2文件
```
压缩：  bzip2 -k 文件
              -k 产生压缩文件后保留原文件
        tar -jcf 压缩包名.tar.bz2 目录
            -j 压缩为.bz2格式
解压：  bunzip2 -k 压缩包.tar.bz2
                -k 解压后保留压缩包
        tar -jxf 压缩包名.tar.bz2
```
注意：
- bzip2是gzip的升级版，压缩比惊人，通常用来压缩较大的文件

## 7 网络命令

### 给用户发信息write
- `write 用户名`给指定用户发送信息，以ctrl+D结束
- 写错了用`ctrl+回格`删除
- 用户需在线

### 给所有用户发信息wall
- 原意：write all
- `wall 信息`给所有在线用户发消息
- 自己也会收到
- 可能会打断别人的工作（慎用）

### 测试网络连通性ping
```
ping 选项 IP地址
     -c 指定发送次数
测试网络连通性
```
- 如果不指定次数，用`ctrl+C`停止，否则根本停不下来
- time表示网络延迟
- 重点看packet loss 丢包率，越高网络越差
- ping 自己的ip可以查看自己的网端是否畅通

### 查看和设置网卡信息ifconfig
- 原意：interface configure 接口设置
- 权限：root
- `ifconfig`查看网卡信息
- `ifconfig 网卡名称 IP地址`临时设置网卡的IP地址（关掉终端后就失效了）
- `eth0`：本地网卡1；`lo`：回访网卡，`localhost：127.0.0.1`
```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.10  netmask 255.255.240.0  broadcast 172.17.15.255
        //当前ip地址 子网掩码 广播地址
        inet6 fe80::5054:ff:fe16:254e  prefixlen 64  scopeid 0x20<link>
        //ipv6地址信息
        ether 52:54:00:16:25:4e  txqueuelen 1000  (Ethernet) 
        //网卡硬件地址 以太网
        RX packets 1228083  bytes 157777241 (150.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        //接收数据包总数及异常信息
        TX packets 1249160  bytes 176326398 (168.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0 
        //发送数据包总数及异常信息
        collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)//本地回访
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
- 常用信息：当前ip，mac地址

### 收发邮件mail
- `mail 用户名`给用户发送邮件
- `ctrl+D`输入结束并发送
- `mail`查看接收邮件列表，输入对应序号就能查看对应邮件内容
- 读完`h`返回列表，`d 序号`删除邮件，`q`退出
- 相较于`write`，不会打断他人工作，更合适，但都用得少

### 列出登录信息last
- `last`列出目前与过去登入系统的用户信息
- `wtmp begins Tue Jul 23 16:41:30 2019`系统第一次启动时间
- 用户名为`reboot`是重启日志
- `lastlog`查看所有用户的最后登录时间，包括一些系统伪用户
- `lastlog -u 用户id`只查看某用户的登录信息

### 网络路径traceroute
- `traceroute 网站或ip`显示数据包到主机间的路径
    - `traceroute www.baidu.com`显示到达Baidu的网络路径
- 网络追踪技术的原理

### 网络状态netstat
显示网络相关信息
```
netstat 选项
        -t 查询TCP协议的网络信息
        -u 查询UDP协议的网络信息
        -l 查看监听端口
        -r 查看路由，网关
        -n 显示IP地址和端口号
        
netstat -ltun 查看本机监听的端口
netstat -an   查看本机所有的网络连接
netstat -rn   查看本机路由表
```
- TCP就像打电话，先互相确认建立连接，再传输信息，更可靠
- UDP就像发短信，直接向服务器发送信息，迅速，但不一定可靠

### 配置网络setup
Redhat系列特有的网络、服务配置工具
- 永久修改网络和服务的配置，例如开机自启服务和IP地址

### 挂载命令mount
挂载U盘或光盘
```
挂载：
mount [-t 文件系统] 设备文件名 挂载点
mount [-t iso9660] /dev/sr0 /mnt/cdrom
[]可以省略
卸载：
unmont 设备文件名

设备文件名：默认dev/sr0
```

## 8 关机重启命令

### 关机重启shutdown
```
shutdown 选项 时间
         -c 取消前一个关机命令
         -h 关机
         -r 重启

shutdown -h now
shutdown -r 20:30
```
更安全，数据不易丢失，不易造成硬件损坏
- 关机和重启前，先关闭服务，特别是资源消耗大的服务和进程，否则强制关机容易造成软件和硬件损坏
- 建议使用shutdown命令
- 远程服务器，禁止关机！禁止关机！禁止关机！
- 谨慎操作！谨慎操作！谨慎操作！

### 其他关机命令
- halt
- poweroff 相当于直接断电
- init 0

### 其他重启命令
- reboot
- init 6

### 系统运行级别

```
0              Power-off the machine
6              Reboot the machine
2, 3, 4, 5     Start runlevelX.target unit
1, s, S        Enter rescue mode
q, Q           Reload init daemon configuration
u, U           Reexecute init daemon
```
- NFS服务：network file system网络文件系统，共享服务，安全级别不够高，而且各个系统都有方便安全的文件传输方式，没必要用它

### 退出登录logout
- 养成习惯，用完退出，防止数据泄露危险发生