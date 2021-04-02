# 十二 linux系统管理

## 1 进程管理

### 1.1 进程查看

#### 进程简介

进程是正在执行的一个程序或命令，每一个进程都是一个运行的实体，都有自己的地址空间，并占用一定的系统资源。

简言之，进程就是正在运行的程序。

在linux中每一个命令都会创建有一个进程。

#### 进程管理的作用

-   判断服务器的健康状态，最主要
-   查看系统中所有的进程
-   杀死进程
    -   kill命令，是在其他结束进程的方式都不生效时才会采用的方式

#### 查看系统中的所有进程ps

`ps aux`查看系统中所有进程，使用BSD操作系统格式

```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 125476  3556 ?        Ss   Jul23   3:27 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root         2  0.0  0.0      0     0 ?        S    Jul23   0:00 [kthreadd]
root     16263  0.0  0.1 116752  3428 pts/1    Ss   13:45   0:00 -bash
root     16317  0.0  0.0 107992   620 pts/1    S+   13:45   0:00 tail -f /var/log/secure
root     16318  0.0  0.0 112712   980 pts/1    S+   13:45   0:00 grep --color=auto -v refused
root     16600  0.0  0.2 112756  4328 ?        Ss   13:47   0:00 /usr/sbin/sshd -D
```

| 项      | 意义                                                         |
| ------- | ------------------------------------------------------------ |
| USER    | 启动进程的用户                                               |
| PID     | 进程的ID                                                     |
| %CPU    | 进程占用的CPU资源                                            |
| %MEM    | 进程占用的物理内存                                           |
| VSZ     | 进程占用的虚拟内存大小kb                                     |
| RSS     | 进程占用物理内存大小kb                                       |
| TTY     | 运行进程的终端，？表示系统内核调用的进程，tty1-7代表本地终端，tty1-6是字符界面终端，tty7是图形终端。pts/0-255代表虚拟终端。 |
| STAT    | 进程状态。常见的：R运行，S休眠，T停止，s包含子进程，+位于后台 |
| START   | 进程启动时间                                                 |
| TIME    | 进程占用的CPU运算时间，**不是启动时间**                      |
| COMMAND | 进程的名字                                                   |

`ps -le`查看系统中所有进程，使用Linux标准命令格式

```
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0     1     0  0  80   0 - 31369 ep_pol ?        00:03:27 systemd
1 S     0     2     0  0  80   0 -     0 kthrea ?        00:00:00 kthreadd
4 S     0 16263 12906  0  80   0 - 29188 do_wai pts/1    00:00:00 bash
0 S     0 16317 16263  0  80   0 - 26998 wait_w pts/1    00:00:00 tail
0 S     0 16318 16263  0  80   0 - 28178 pipe_w pts/1    00:00:00 grep
4 S     0 16600     1  0  80   0 - 28189 poll_s ?        00:00:00 sshd
```

#### top查看系统的健康状态

``` shell
top [选项]
	-d 秒数	指定top命令的更新周期默认3s

进入交互模式后可以执行的命令：
?/h		显示交互模式的帮助
P		以CPU使用率排序，默认
M		以内存使用率排序
N		以PID排序
q		退出
```

重要的前五行：

```
top - 16:08:24 up 16 days, 23:27,  2 users,  load average: 0.00, 0.01, 0.05
Tasks:  76 total,   1 running,  75 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  6.2 sy,  0.0 ni, 93.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1882356 total,    87420 free,   116064 used,  1678872 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  1565820 avail Mem 
```

第一行任务队列信息

| 内容                           | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| 16:08:24                       | 系统时间                                                     |
| up 16 days, 23:27              | 系统运行时间，16天23小时27分                                 |
| 2 users                        | 当前登录用户数                                               |
| load average: 0.00, 0.01, 0.05 | 系统在1,5,15min之前的平均负载，单核处理器正常小于1，大于1表示系统已经超过负载，双核就是2 |

第二行进程信息

| 内容             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| Tasks:  76 total | 系统中的总进程数                                             |
| 1 running        | 1个正在运行                                                  |
| 75 sleeping      | 75个休眠                                                     |
| 0 stopped        | 0个停止                                                      |
| 0 zombie         | 0个僵死，僵尸进程，如果不是0，需要手工检查，必要时kill，通常是程序在终止过程中报错卡死 |

```
top - 16:08:24 up 16 days, 23:27,  2 users,  load average: 0.00, 0.01, 0.05
Tasks:  76 total,   1 running,  75 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  6.2 sy,  0.0 ni, 93.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1882356 total,    87420 free,   116064 used,  1678872 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  1565820 avail Mem 
```

第三行CPU信息

| 内容             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| %Cpu(s):  0.0 us | 用户模式占用的CPU                                            |
| 6.2 sy           | 系统模式占用的CPU                                            |
| 0.0 ni           | 改变过优先级的用户进程占用的CPU                              |
| 93.8 id          | *空闲的CPU                                                   |
| 0.0 wa           | 等待输入/输出的进程占用的CPU                                 |
| 0.0 hi           | 硬中断请求服务占用的CPU                                      |
| 0.0 si           | 软中断请求服务占用的CPU                                      |
| 0.0 st           | steal time 虚拟时间百分比，有虚拟机时，虚拟CPU等待实际CPU的时间占比 |

第四行物理内存信息

第五行虚拟内存信息

top没必要随时开着，减少资源浪费，要用打开，不用关了就是

#### 查看进程树pstree

```
pstree [选项]
		-p		显示进程的PID
		-u		显示进程的所属用户
```

### 1.2 终止进程

#### kill命令

`kill -l`查看可用的进程信号

常用的进程信号

| 信号代号 | 信号名称 | 说明                                                     |
| -------- | -------- | -------------------------------------------------------- |
| *1       | SIGHUP   | 重启，该信号让进程立即关闭，然后重新读取配置文件重启     |
| 2        | SIGINT   | 终止前台进程，相当于ctrl+c                               |
| 8        | SIGFPE   | 在发生致命运算错误时发出，如浮点运算错误，溢出，除数为零 |
| *9       | SIGKILL  | 强制终止                                                 |
| 14       | SIGALRM  | 时钟定时信号，alarm函数使用该信号                        |
| *15      | SIGTERM  | 正常终止，kill的默认信号。                               |
| 18       | SIGCONT  | 暂停的应用恢复运行                                       |
| 19       | SIGSTOP  | 暂停前台进程，相当于ctrl+z                               |

`kill -1 PID`重启进程

`kill -9 PID`强制终止进程

#### killall命令

```
killall [选项] [信号] 进程名
按照进程名杀死进程
		-i	交互式，询问是否杀死某个进程
		-I	忽略进程名大小写
```

`killall -9 httpd`终止Apache进程

#### pkill命令

```
pkill [选项] [信号] 进程名
按照进程名终止命令
		-t 终端号	按终端号踢出用户，杀死相关所有进程
```

`pkill -9 -t tty1`踢出本地终端1

## 2 工作管理

### 2.1 把进程放入后台

-   后台运行，命令末尾加&
    -   `tar -zcf etc.tar.gz /etc &`
-   后台暂停，ctrl+z

### 2.2 查看后台工作

```
jobs -l
	 -l	显示工作的PID
+代表最近一个放入后台的工作，也就是工作恢复时，默认恢复的工作。
-代表倒数第二个放入后台的工作
```

### 2.3 恢复后台工作

-   `fg %工作号`把工作恢复前台执行
    -   %可以省略，但工作号和PID不同，工作号通过jobs可以查看
    -   不加工作号默认恢复最近的后台工作

-   `bg %工作号`把工作恢复到后台执行
    -   如果命令和前台有交互，则无法在后台执行

## 3 系统资源查看

### 3.1 vmstat命令监控系统资源

`vmstat [刷新延时 刷新次数]`

例如`vmstat 1 3`表示1s刷新一次，总共刷新3次

```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 3  0      0  77820 151836 1508004    0    0     2    25    6   18  1  0 99  0  0
 0  0      0  77820 151836 1508004    0    0     0     0  102  182  0  1 99  0  0
 0  0      0  77308 151836 1508008    0    0     0     0  170  311  1  0 99  0  0
```

### 3.2 dmesg开机时内核检测信息

```
dmesg
查看开机自检信息，内容很多
[    0.000000] Initializing cgroup subsys cpuset
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 3.10.0-957.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) ) #1 SMP Thu Nov 8 23:39:32 UTC 2018

dmesg | grep CPU
查看CPU信息
[    0.000000] smpboot: Allowing 1 CPUs, 0 hotplug CPUs
[    0.000000] setup_percpu: NR_CPUS:5120 nr_cpumask_bits:1 nr_cpu_ids:1 nr_node_ids:1
[    0.000000] PERCPU: Embedded 38 pages/cpu @ffff98657fc00000 s118784 r8192 d28672 u2097152
[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.000000]  RCU restricting CPUs from NR_CPUS=5120 to nr_cpu_ids=1.
[    0.037313] mce: CPU supports 10 MCE banks
[    0.113001] smpboot: CPU0: Intel(R) Xeon(R) CPU E5-26xx v4 (fam: 06, model: 4f, stepping: 01)
[    0.116030] Performance Events: unsupported p6 CPU model 79 no PMU driver, software events only.
[    0.119201] Brought up 1 CPUs
```

linux查看硬件信息的重要方法

### 3.3 free命令查看内存使用状态

```
free [选项]
	 -b		以字节为单位显示
	 -k		以KB为单位显示，默认
	 -m		以MB为单位显示
	 -g		以GB为单位显示

free -m
              total        used        free      shared  buff/cache   available
Mem:           1838         138          77           0        1622        1503
Swap:             0           0           0
```

#### 缓冲和缓存的区别

简单说，缓冲和缓存都是RAM，缓存(cache)是用来加速数据读取的，而缓冲(buffer)是加速数据写入的。

### 3.4 查看CPU信息

`cat /proc/cpuinfo`

### 3.5 uptime命令

显示系统的启动时间和平均负载，也就是top和w命令的第一行

### 3.6 查看系统与内核信息

```
uname [选项]
		-a		查看系统所有相关信息
		-r		查看内核版本
		-s		查看内核名称
```

#### 判断当前系统位数

`file /bin/ls`

通过查看文件系统类型可以看到系统位数

#### 查询当前linux系统的发行版本

`Cat /etc/xxx-release` 

XX为发行版名称。如`centos-release`

### 3.7 列出进程打开或使用的文件信息

```
lsof [选项]
	 -c 字符串		只列出以字符串开头的进程打开的文件
	 -u 用户名		只列出某个用户的进程打开的文件
	 -p pid			列出某个PID进程打开的文件
```

## 4 系统定时任务

### 4.1 crond服务管理与访问控制

-   重启`systemctl restart crond`
-   自启动`chkconfig crond on`

### 4.2 用户的crontab设置

```
crontab [选项]
		-e	编辑crontab定时任务
		-l	查询crontab任务
		-r	删除当前用户的所有crontab任务
```

```
crontab -e
编辑定时任务
格式
* * * * * command
```

| 项目    | 含义                 | 范围                    |
| ------- | -------------------- | ----------------------- |
| 第一个* | 一小时当中的第几分钟 | 0-59                    |
| 第二个* | 一天当中的第几个小时 | 0-23                    |
| 第三个* | 一个月当中的第几天   | 1-31                    |
| 第四个* | 一年当中的第几个月   | 1-12                    |
| 第五个* | 一周当中的星期几     | 0-7（0和7都代表星期日） |

| 特殊符号 | 含义                     |
| -------- | ------------------------ |
| *        | 代表任何时间             |
| ，       | 代表不连续时间           |
| -        | 代表连续的时间范围       |
| */n      | 代表每隔多久执行一遍命令 |

例如：

```
45 22 * * * 命令
每天的22点45分执行命令

0 17 * * 1 命令
每周一的17点执行命令

0 5 1,15 * * 命令
每月1号和15号5点执行命令

40 4 * * 1-5 命令
每周1-，4点40执行命令

*/10 4 * * * 命令
每天4点，每隔10min执行一次命令

0 0 1,15 * 1 命令
每周1和每月1号15号都会执行，但这种写法有些混乱
```