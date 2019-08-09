# 十一 Linux服务管理

## 1 服务的简介和分类

### 1.1 服务的分类

-   Linux服务
    -   RPM包默认安装的服务
        -   独立的服务
            -   apache运行时一直在内存中
            -   xinetd
            -   响应快，但是会一致占用内存
        -   基于xinetd服务
            -   rsync通过xinetd响应客户端
            -   响应较慢，但平时不占用内存
    -   源码包安装的服务

### 1.2 启动与自启动

-   服务启动：就是在当前系统中让服务运行，并提供功能
-   服务自启动：自启动是让服务在系统开机或重启之后，随着系统的启动而自动启动服务

### 1.3 查询已安装的服务

-   RPM包安装的服务
    -   `chkconfig --list`查看服务自启动状态，可以看到RPM包安装的非系统服务
    -   这只会看到非系统服务，系统服务要用`systemctl list-unit-files`来查看
-   源码包安装的服务
    -   查看服务安装位置，一般是`/usr/local`下

#### RPM包安装服务和源码包安装服务的区别

-   安装位置不同
    -   源码包在指定位置，一般是`/usr/local/`
    -   RPM包安装在默认位置

## 2 RPM包安装服务的管理

### 2.1 独立服务的管理

#### RPM包安装服务的位置

RPM包安装在默认位置：

-   `/etc/init.d/`启动脚本位置
-   `/etc/sysconfig/`初始化环境配置文件位置
-   `/etc/`默认安装配置文件位置
-   `/etc/xinetd.conf`xinetd配置文件
-   `/etc/xinetd.d/`基于xinetd服务的启动脚本
-   `/var/lib/`服务产生的数据存放位置
-   `/var/log/`日志

centos7用systemctl来取代了service的服务管理功能，服务安装的位置也有所改变

```
To make use of service
files simply invoke "systemctl", which will output a list of all
currently running services (and other units). Use "systemctl
list-unit-files" to get a listing of all known unit files, including
stopped, disabled and masked ones. Use "systemctl start
foobar.service" and "systemctl stop foobar.service" to start or stop a
service, respectively. For further details, please refer to
systemctl(1).
```

#### 独立服务的启动

-   标准方法`/etc/init.d/独立服务名 start|stop|status|restart|`
-   Redhat简化的服务命令`service 独立服务名 start|stop|status|restart|`
    -   `service --status-all`列出系统中所有RPM包安装的服务的运行状态
    -   该命令centos7中已经被systemctl代替

#### 独立服务的自启动

-   `chkconfig [--level 运行级别] [独立服务名] [on|off]`

```shell
chkconfig --list
netconsole      0:off   1:off   2:off   3:off   4:off   5:off   6:off
network         0:off   1:off   2:on    3:on    4:on    5:on    6:off
chkconfig --level 2345 netconsole on
#设置netconsole开机自启
chkconfig netconsole off
#取消开机自启
```

-   修改`/etc/rc.d/rc.local`文件（推荐）
    -   所有自启的服务，一目了然
-   使用ntsysv命令管理自启动

### 2.2 基于xinetd服务的管理（了解）

#### 安装xinetd与telnet

超级守护进程xinetd现在用的很少了，只有一些古老的服务仍在使用，系统默认没有安装

```
#安装
yum -y install xinetd
yum -y install telnet-server
```

telnet服务漏洞很多，不安全，不建议安装，这里只是为了学习了解，用完就删

#### xinetd服务的启动

```
vim /etc/xinetd.d/服务
将文件内的disable=yes改为disable=no
service xinetd.d restart
```

#### xinetd服务自启动

-   `chkconfig telnet on`
-   `ntsysv`

xinetd的启动和自启动是相互关联的，启动时就会自启动，停止时就会关闭自启动

可见xinetd的淘汰是有道理的

## 3 源码包安装服务的管理

#### 启动

使用绝对路径，调用启动脚本来启动。不同的服务的启动脚本和路径可能都有所不同，可以通过源码包的安装说明来查看如何启动

#### 自启动

`vim /etc/rc.d/rc.local`加入启动命令

#### 让源码包安装的服务被服务管理命令识别

-   service识别

    创建启动脚本到服务默认目录下的软链接`ln -s 脚本绝对路径 服务默认路径/etc/init.d/`

    但是并不建议，不便于区分两种安装方式

-   chkconfig和ntsysv识别

    修改启动脚本，加入两行注释：

    ```
    vi /etc/init.d/apache
    #chkconfig: 35 86 76
    *指定脚本可以被chkconfig命令管理，
    *格式为chkconfig: 运行级别 启动顺序 关闭顺序
    运行级别就是/etc/init.d下的
    #description: 说明，随意
    
    checkconfig --add 服务名
    加入
    checkconfig --del 服务名
    删除
    ```

    也不建议，源码包安装和RPM包安装还是需要区分开

## 4 服务管理总结

