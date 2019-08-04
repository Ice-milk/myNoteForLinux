# 二 linux系统安装-远程登录管理工具
## 虚拟机的网络配置
1. 桥接：通过windows中的真实网卡直接连接到物理网络，由路由分配ip，就像一台真正的电脑连接入本地网络
2. NAT：通过虚拟网卡VMnet8连接，只能和本地电脑通信，但能访问互联网
3. Host-only：通过虚拟网卡VMnet1连接,只能和本地电脑通信，而且无法访问互联网

>桥接过后无法联网？
在虚拟机网络编辑器查看网卡连接是否正确。
## 最小化安装后联网(ip地址配置)
1. 默认状态下最小化安装使用NetworkManager这个服务来控制联网的，但是这个配置在配置生产环境服务器时一般不会使用，而是使用系统自带的network服务，更加稳定，因此先将NetworkManager服务关闭，命令是：
```
systemctl stop NetworkManager
systemctl disable NetworkManager
```
2. 关闭以后，我们来编辑network服务的配置文件，默认路径是：
```
/etc/sysconfig/network-scripts/ifcfg-ensXX
```
在这里XX指的是你的服务器网卡编号。我这里是33，于是命令就是：
```
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```
3. 最小化安装的情况下，默认的获取IP方式BOOTPROTO是dhcp，而且默认不会开机自启动，即ONBOOT="no"，而对于生产环境而言，默认情况下使用固定的IP地址，而且网卡要随系统开机自启动，所以设置：
```
BOOTPROTO=dhcp
ONBOOT=no
改为
BOOTPROTO=static(也可以保持DHCP，通过设置路由来固定ip)
ONBOOT=yes
```
4. 修改完成后，在文件的后面加上下面的内容：（使用DHCP跳过此步）
```
IPADDR=192.168.1.6
PREFIX=24
GATEWAY=192.168.1.1
DNS1=202.103.24.68
```
上面的配置依次是IP地址、掩码、网关和DNS，你可以根据你自己的环境填入合适的内容。
5. 填写完成后保存退出，然后重新启动network服务，命令是：
```
systemctl restart network
```
启动完成没有报错后，执行命令
```
ip addr
```
查看IP地址信息。可以看到已经获取到正确的配置，这个时候用ping命令测试一下，可以看到可以ping通百度的主页，说明网络正常，可以正常联网，到这里网络环境配置完毕。
```
ping www.baidu.com
[ctrl+c]停止ping
```
## 换国内yum源和epel源（aliyun和网易云）

> 什么是EPEL?
EPEL的全称叫 Extra Packages for Enterprise Linux 。EPEL是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。装上了 EPEL之后，就相当于添加了一个第三方源。

1. 首先进入/etc/yum.repos.d/目录下，新建一个repo_bak目录，用于保存系统中原来的repo文件:
```
cd /etc/yum.repos.d
mkdir repo_bak
mv *.repo repo_bak/
```
2. 在CentOS中配置使用网易和阿里的开源镜像
- 到网易和阿里开源镜像站点下载系统对应版本的repo文件
```
curl -O http://mirrors.aliyun.com/repo/Centos-7.repo
curl -O http://mirrors.163.com/.help/CentOS7-Base-163.repo
ls
显示下面几个文件：
Centos-7.repo  CentOS-Base-163.repo  repo_bak
```
3. 因为国内源基本都升级了https，所以需要修改一下Centos-7.repo文件，把里面的所有http:改为https:
4. 清除系统yum缓存并生成新的yum缓存
```
yum clean all
yum makecache
```
4. 安装epel源
```
yum list | grep epel-release
yum install -y epel-release
```
5. 使用阿里开源镜像提供的epel源
```
curl -O http://mirrors.aliyun.com/repo/epel-7.repo
或者
wget -O /etc/yum.repos.d/epel-7.repo http://mirrors.aliyun.com/repo/epel-7.repo
```
>安装wget：yum install wget
6. 再次清除系统yum缓存，并重新生成新的yum缓存
```
yum clean all
yum makecache
```
7. 查看系统可用的yum源和所有的yum源
```
查看正在使用的源
yum repolist enabled
查看所有源
yum repolist all
```

## 安装ifconfig
>ifconfig命令可以用于查看、配置、启用或禁用指定网络接口，如配置网卡的IP地址、掩码、广播地址、网关等，功能不可谓不丰富。但是，CentOS 7默认已不再安装此命令，其中很多功能用ip addr指令替代了。

>考虑到 既有的很多管理工具或脚本都调用了此功能命令（ifconfig），如果将这些工具直接迁移过来会报错，如果对这个指令用其它指令进行替换，及对这些工具升级，则增加了工作量，还增加了出错的风险。
所以，在CentOS 7 系统中 安装 ifconfig 命令很有必要。
1. 查找含有ifconfig的包
```
yum search ifconfig
```
2.找到需要安装net-tools.x86_64
```
yum install net-tools.x86_64
以下命令输出正常即安装成功：
ifconfig
```
## yum常用命令
```
列出所有可更新的软件清单
yum check-update

安装所有更新软件
yum update

仅安装指定的软件
yum install <package_name>

仅更新指定的软件
yum update <package_name>

用YUM安装软件包
yum install <package_name>

用YUM删除软件包
yum remove <package_name>
```
## SecureCRT远程连接 winSCP文件传输
df查看磁盘空间
乱码调整：选项-->会话选项：更改外观字体，编码格式

