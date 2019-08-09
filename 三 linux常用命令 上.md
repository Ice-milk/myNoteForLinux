#  三 linux常用命令 上

## 1 文件处理命令

### 1.1 命令格式与目录处理命令ls

- **命令格式**：`命令 [-选项] [参数]`
    - 例：`ls -la /etc`
- **说明**：
    - 个别命令使用不遵循此格式
    - 当有多个选项时，可以写在一起`-l -a=-la`
    - 简化选项与完整选项：`-a等于--all`
- 显示目录文件`ls`
```
命令原意：list
路径：/bin/ls
权限：所有用户
功能描述：显示目录文件
    ls -a（--all）显示所有，查看隐藏文件
    隐藏文件的意义：这是一个系统文件，用户除非知道自己在干啥，
    否则不需要查看和修改这些文件

    ls -l(--long)h（human人性化显示）显示文件详细信息
    ll 是ls -l的简化版，方便输入

    ls -ld查看目录本身的详细信息
    
    ls -i（inode）查看文件的id
```
文件详细信息显示格式：
权限|引用系数|所有者u（user）|所属组g（group）|大小|修改时间|文件名
--|--|--|--|--|--|--
drwxr-xr-x| 3 |root| root| 4.0K| Jul 23 18:39 |download

|文件权限||||
--|--|--|--
文件类型|rw-|r--|r--
-二进制文件，d目录，l软链接文件|u所有者|g所属组|o其他
-|r读|w写|x执行

### 1.2 目录处理命令

#### 创建目录mkdir

```
原意：make dirctories
路径：/bin/mkdir
权限：所有用户
    mkdir -p [目录名]
    创建新目录
    -p递归创建
例：mkdir -p /tmp/myvideo/mv
即使myvideo和mv都是不存在的目录，也会连续创建
```

#### 打开目录cd

```
原意：change directory
路径：shell内置
权限：所有用户
    cd [目录]
    切换目录
    cd ..回到上级目录
```
> `.`表示当前目录`..`表示当前目录的上一级目录

#### 显示当前路径pwd
```
原意：print working directory
路径：/bin/pwd
权限：所有用户
    pwd
    显示当前目录
```

#### 删除空目录rmdir
```
原意：remove empty directories
路径：/bin/rmdir
权限：所有用户
    rmdir [目录名]
    删除空目录
```

#### 复制cp
```
原意：copy
路径：/bin/cp
权限：所有用户
    cp -rp [原文件或目录] [目标目录]
       -r复制目录
       -p保留文件属性(修改时间不变)
    复制文件或目录
    可以同时改名，目标目录指定新名称即可
```

#### 移动(剪切)，改名mv
```
原意：move
路径：/bin/mv
权限：所有用户
    mv -r [原文件或目录] [目标目录]
       -r 目录
    剪切文件
    
    mv [原名] [新名]
    修改文件名
```

#### 删除rm
```
原意：remove
路径：/bin/rm
权限：所有用户
    rm -rf [文件或目录]
       -r删除目录
       -f（force）强制执行
    删除文件
```

### 1.3 文件处理命令

#### 创建空文件touch
```
路径：/bin/touch
权限：所有用户
    touch [文件名]
    创建空文件
文件命名注意：
1. 后缀名对系统没有意义
2. 可以包含特殊符号，如空格，但不推荐，操作麻烦
```

#### 显示文件内容cat
```
路径：/bin/cat
权限：所有用户
    cat -n [文件名]
        -n显示行号
    显示文件内容
    
    tac
    反向显示文件内容（最后一行变到第一行）
```

#### 分页显示文件内容more
```
路径：/bin/more
权限：所有用户
    more [文件名]
        空格或f 翻页
        (enter) 换行
        q或Q    退出
分页显示文件内容
```

#### 可向上翻页less
```
路径：/usr/bin/less
权限：所有用户
    less [文件名]
分页显示文件内容，可向上翻页，可搜索查找
    /(搜索内容)
    n查找下一个
```

#### 查看文件前几行head
```
head -n [行号] [文件名]
     -n n是数字，显示文件的前n行（看注释）
    默认10行
```

#### 查看文件后几行tail
```
tail -n指定行数
     -f动态显示（监控日志）
```

### 1.4 链接命令ln

- 原意：link
- 路径：/bin/ln
- 权限：所有用户
```
ln -s [原文件] [目标文件]
   -s创建软链接
```
- 功能：生成链接文件
```
ln -s /etc/issue /tmp/issue.soft
创建/etc/issue的软链接/tmp/issue.soft
ln /etc/issue /tmp/issue.hard
创建/etc/issue的硬链接/tmp/issue.hard
```
软链接和硬链接的区别：
- 软链接
    - 文件类型l，所有用户满权限，类似于快捷方式，但文件权限最终决定于指向的文件
    - 文件大小很小，只是符号链接
    - 箭头指向源文件/tmp/issue.soft -> /etc/issue
- 硬链接（用得少，备份）
    - 相当于`cp -p +同步更新`，即使删除原文件，硬链接文件依然能够访问
    - 可以通过i节点识别，硬链接文件i节点相同`ls -i`
    - 不能跨分区
    - 不能针对目录使用

### 1.5 统计文件行数wc -l

```
Usage: wc [OPTION]... [FILE]...

Print newline, word, and byte counts for each FILE, and a total line if
more than one FILE is specified.  With no FILE, or when FILE is -,
read standard input.  A word is a non-zero-length sequence of characters
delimited by white space.

  -c, --bytes            统计文件字节数
  -m, --chars            统计文件字母数
  -l, --lines            统计文件行数
      --files0-from=F    read input from the files specified by
                           NUL-terminated names in file F;
                           If F is - then read names from standard input
  -L, --max-line-length  输出最长行长度
  -w, --words            输出文件单词数
      --help     display this help and exit
      --version  output version information and exit

```

## 2 权限管理命令

### 2.1 更改文件权限chmod

- 原意：change the permissions mode of a file
- 路径：/bin/chmod
- 权限：文件所有者，root
```
chmod -R [{ugoa}{+-=}{rwx}] [文件或目录]
      [mode=421] [文件或目录]
      r-4
      w-2
      x-1
      -R 递归修改
    修改目录及其目录下的文件为相应权限
```
代表字符|权限|对文件的含义|对目录的含义
--|--|--|--
r|读权限|可以查看文件内容cat/more/less|可以列出目录中的内容ls
w|写权限|可修改文件内容vim|可以在目录中创建删除文件touch/mkdir/rm|rmdir
x|执行权限|可以执行文件|可以进入目录cd
```
拓展：
useradd 添加用户
password 用户名 设置密码
```
普通文件最佳权限分配：目录755文件775

### 2.2 其他权限管理命令

#### 更改所有者chown
- 原意：change file ownership
- 路径：/bin/chown
- 权限：root
```
    chown [用户] [文件/目录]
    改变文件或目录的所有者
```

#### 更改所属组chgrp

- 原意：change file group ownership
- 路径：/bin/chgrp
- 权限：root
```
    chgrp [用户组] [文件或目录]
    更改文件或目录的所属组
    
    拓展：
    groupadd 组名 添加用户组
```

#### 设置默认权限umask
- 原意：the user file-creation mask
- 路径：shell内置命令
- 权限：root
```
    umask -S
    显示当前新建文件的缺省权限，用rwx表示
    缺省创建的文件不能有x权限，所以新建的文本文件没有x，但目录文件可以有（安全考虑）
    
    umask 查询当前缺省权限值
    如果返回为：
    0022
    0特殊权限
     022 --- -w- -w- 三个用户类型的权限值
     777 rwx rwx rwx 逻辑相与非
     ---------------------
     755 rwx r-x r-x
     755 = 777 - 022
    
    umask 077 设置缺省权限为700
```

## 3 文件搜索命令
搜索是资源消耗很大的命令，在服务器运维中使用要避开服务器使用高峰期，尽量不用搜索命令，目录规划清晰，找文件就好找了。
- 搜索命令使用原则：
- 能不用则不用
- 一旦使用选择的范围尽量小
- 搜索条件尽量精准

### 3.1 文件搜索命令find

- 路径：/bin/find
- 权限：所有用户
```
    find [搜索范围] [匹配条件]
    文件搜索
    find /root/ -name download
```
常用find选项：
1. `-name 文件名`
    - 根据文件名查找，区分大小写
    - `-iname`不区分大小写
    - 模糊匹配：
        - 通配符*，匹配字符串，`*init*`就可以查找文件名包含init的文件
        - 通配符?，匹配一个字符
2. `-size 文件大小n`
    - 根据文件大小查找
    - `+n`大于 `-n`小于 `n`等于
    - n单位数据块，一个数据块512字节，0.5k
    - 100M = 204800
3. `-user 所有者`
    - 根据所有者查找
4. `-group 所属组`
    - 根据所属组查找
5. `-cmin 时间t`
    - 根据时间查找
    - `-t`在时间内
    - `-amin` 访问时间access
    - `-cmin` 文件属性修改时间（如权限）change
    - `-mmin` 文件内容修改时间modify
6. `-type 文件类型`
    - f文件 d目录 l软链接
7. `-inum 根据i节点查找`
    - 方便找硬链接
8. `-size +10 -a -size -20`
    - 查找大于5k小于10k的文件
    - `-a`，and，与
    - `-o`，or，或
9. `-name 名称 -exec ls -l {} \;`
    - 按名称查找并显示详细信息
    - `-exeac/-ok 命令 {} \;`对搜索结果执行操作
    - `-ok`会询问，常用于删除

### 3.2 其他搜索命令

#### 快速查找locate
- 路径：/usr/bin/locate
- 权限：所有用户
- `locate 文件名`
- 资源消耗较少
- 有一个文件资料库，实现快速搜索
- 问题：新建文件没有收录入资料库
    - 手动更新命令：`updatedb`
    - 临时文件不在资料库内

常用locate选项：
- `-i` 不区分大小写

#### 命令目录搜索which
- 路径：/usr/bin/which
- 权限：所有用户
```
    which 命令
    搜索命令所在的目录及别名信息
    
    whereis可以搜索命令所在路径和帮助文档
```

#### 文件内容搜索grep
- 路径：/bin/grep
- 权限：所有用户
```
    grep -iv [字符串] [文件]
    在文件中搜寻字符串匹配的行并输出
    -i 不区分大小写
    -v 排除指定字符串
    
    例如：
    去脚本注释#
    grep -v ^# 文件
```
