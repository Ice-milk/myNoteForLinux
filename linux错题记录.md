# linux错题记录

## 1. 下列哪一个不是linux的基础组件

kernel

shell

GUI

*包管理器*



Linux系统一般有4个主要部分：内核、shell、文件系统和应用程序。

Linux内核主要由五个子系统组成：进程调度，内存管理，虚拟文件系统，网络接口，进程间通信。

操作系统的组成要素：

操作系统 = 内核 + 系统程序

系统程序 = 编译环境 + API

编译环境 = 编译程序 + 连接程序 + 装载程序

API = 系统调用 + 语言库函数(C、C++、Java等等)

AUI = shell + 系统服务例程(如x服务器等)+ 应用程序(浏览器，字处理，编辑器等)

## 2. 下列哪个不是linux常用的shell

SH

Bash

Zsh

*XSH*



不同的shell具备不同的功能，shell还决定了Linux中默认的shell是/bin/bash，流行的shell有ash、bash、ksh、csh、zsh等，不同的shell都有自己的特点以及用途。

bash
大多数Linux系统默认使用的shell，bash shell是Bourne shell 的一个免费版本，它是最早的Unix shell,bash还有一个特点，可以通过help命令
来查看帮助。包含的功能几乎可以涵盖shell所具有的功能，所以一般的shell脚本都会指定它为执行路径。

csh
C shell 使用的是“类C”语法，csh是具有C语言风格的一种shell，其内部命令有52个，较为庞大。目前使用的并不多，
已经被/bin/tcsh所取代。

ksh
Korn shell 的语法与Bourne shell相同，同时具备了C shell的易用特点。许多安装脚本都使用ksh,ksh 有42条内部命令，与bash相比有一定的限制性。

tcsh
tcsh是csh的增强版，与C shell完全兼容。

sh
是一个快捷方式，已经被/bin/bash所取代。

nologin
指用户不能登录

zsh
目前Linux里最庞大的一种shell：zsh。它有84个内部命令，使用起来也比较复杂。一般情况下，不会使用
该shell。

## 3. 网络操作系统

linux、unix、windows NT

## 4. 查看linux负载的命令

top、uptime、free

