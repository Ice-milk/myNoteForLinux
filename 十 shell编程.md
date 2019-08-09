# 十 shell编程

## 1 基础正则表达式

### 1.1 正则表达式与通配符

- 正则表达式用来在**文件中**匹配符合条件的**字符串**，正则是**包含匹配**。`grep`、`awk`、`sed`等命令可以支持正则表达式
- 通配符涌来了匹配符合条件的**文件名**，通配符是**完全匹配**。`ls`、`find`、`cp`这些命令不支持正则表达式，所以只能使用shell自己的通配符来进行匹配

### 1.2 基础正则表达式

| 元字符    | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| *         | 前一个字符匹配0次或任意多次                                  |
| .         | 匹配除了换行符外任意一个字符                                 |
| ^         | 匹配行首。例如^hello匹配以hello开头的行                      |
| $         | 匹配行尾。例如hello$会匹配以hello结尾的行                    |
| []        | 匹配中括号中指定的任意**一个字符**。例如`[0-9][a-z]`匹配数字和小写字母组成的两个字符 |
| [^]       | 匹配除中括号内字符以外的任意一个**不是换行符**的字符。例如`[^0-9]`匹配任意一个非数字字符 |
| \         | 转义符                                                       |
| `\{n\}`   | 表示其前面的字符恰好出现n次。<br />例如`[0-9]\{4\}`匹配4位数字，`[1][3-8][0-9]\{9\}`匹配手机号码 |
| `\{n,\}`  | 表示其前面的字符出现不小于n次。例如`[0-9]\{2,\}`表示两位及以上的数字 |
| `\{n,m\}` | 表示其前面的字符至少出现n次，最多出现m次。<br />例如`[a-z]\{6,8\}`匹配6-8位小写字母 |

典型的正则表达式：

#### 例子

```shell
# * 前一个字符匹配0次或任意多次

grep "a*" 文件名
#匹配所有内容，包括空白行，没有意义

grep "aa*" 文件名
#匹配至少包含一个a的行

grep "aaa*" 文件名
#匹配至少包含两个a的行
```

```shell
# . 匹配除了换行以外的任意一个字符

grep "s..d" 文件名
#匹配在s和d之间有两个字符的行

grep "s.*d" 文件名
#匹配在s和d之间有任意字符

grep ".*" 文件名
#匹配所有内容
```

```shell
# ^ 匹配行首 $ 匹配行尾

grep "^M" 文件名
#匹配以M开头的行

grep "n$" 文件名
#匹配以n结尾的行

grep -n "^$" 文件名
#匹配空白行，-n显示行号
```

```shell
# [] 匹配中括号中指定的任意一个字符 [^] 匹配除中括号内字符以外的任意一个不是换行符的字符

grep "^[0-9]" 文件名
#匹配以数字开头的行
grep "^[^0-9]" 文件名
#匹配不是以数字开头的行，但不包括空白行
```

```shell
# \ 转义符

grep "\.$" 文件名
#匹配以.结尾的行
```

```shell
# \{n\} 表示其前面的字符恰好出现n次
# \{n,m\} 表示其前面的字符出现大于n次，小于m次

grep "[0-9]\{3\}" 文件名
#匹配含有三个连续数字的行
```

## 2 字符截取命令

### 2.1 cut字段提取命令

```shell
cut [选项] 文件名
	-f 列号	提取第几列
	-d 分隔符	按照指定分隔符分割列

cut -d ":" -f 1,3 /etc/passwd
#提取用户名和UID
```

```shell
#cut命令的局限

df -h | cut -d " " -f 1,3
#cut的分隔符是非常严格的，多个空格作为分隔符就很难区分
```

-   cut适用于严格的分隔符如：`:`、`tab`
-   cut不适用于分隔符为多个空格这样的情况
-   能用cut就用cut，因为相对简单，不易出错

### 2.2 printf命令(awk常用)

printf是格式化输出命令，不算是字符截取命令，但是它常用在`awk`语句中，作为标准输出格式

```shell
printf "输出类型输出格式" 输出内容
输出类型：
	%ns		输出字符串。n是数字代表几个字符
	%ni		输出整数。n是数字代表几个数字
	%m.nf	输出浮点数。m和n是数字，代表输出的整数位数和小数位数。
			如%8.2f代表输出8位浮点数，其中2位小数，6位整数
输出格式：
	\a		警告音
	\b		退格
	\f		清屏
	\n		换行
	\r		回车
	\t		水平制表符
	\v		垂直制表符

例子：
printf %s 1 2 3 4 5 6
1 2 3 4 5 6没有换行

printf %s %s %s 1 2 3 4 5 6
%s %s 1 2 3 4 5 6

printf "%s %s %s" 1 2 3 4 5 6
1 2 34 5 6

printf "%S %S %S \n" 1 2 3 4 5 6
1 2 3
4 5 6

#printf无法使用管道符，只能使用$()内置命令来传递字符数据
printf "输出类型输出格式" $(cat 文件名)
```

-   awk中支持print和printf输出
    -   print会在每个输出后自动加入一个换行符(linux默认没有print命令)
    -   printf是标准格式输出命令，并不会自动加入换行符，如果要换行需要加入换行符

### 2.3 awk命令

awk命令是较为复杂的命令，在字符截取命令中，对于初学者而言，能用cut就用cut，cut办不到的事情再考虑用awk

#### 命令基本格式

```shell
awk '条件1{动作1} 条件2{动作2} ... ' 文件名
条件：
	一般使用关系表达式作为条件
	x>10		判断变量x是否大于10
	x>=10
	x<=10
动作：
	格式化输出
	流程控制语句

df -h | awk '{printf $1 "\t" $5 "\n"}'
#输出磁盘名和已用空间
df -h | awk '{print $1 "\t" $5}'
```

#### 指定分隔符

-   条件BEGIN
    -   在读取数据之前，先执行动作
    -   `awd 'BEGIN{print "This is a transcript"} {print $2 "\t" $6}' 文件名`
    -   执行的结果会先显示This is a transcript，再输出数据
-   FS内置变量
    -   `awk 'BEGIN{FS=":"} {print $1 "\t" $3} /etc/passwd'`
    -   指定分隔符，输出用户名和UID
    -   也可以用`awk -f "分隔符" '{}'`
-   条件END
    -   在所有动作执行完之后执行
    -   `awk 'END{print "The End"} {print $2 "\t" $6}' 文件名`
    -   执行会先输出数据，最后一行输出The End

#### 关系运算符

-   `>`、`>=`、`<=`

### 2.4  sed命令

​		sed是一种几乎包括在所有UNIX平台的轻量级**流编辑器**。sed主要是用来将数据进行**选取、替换、删除、新增**的命令。可以修改文件内容，也支持修改管道符数据。

```shell
sed [选项] '[动作]' 文件名
选项：
	-n		一般sed命令会把所有数据输出，如果加入此选项，只会输出经过sed处理过的行
	-e		允许对输入数据应用多条sed命令编辑
	-i		用sed的修改结果直接修改原文件内容
动作：
	a\		追加，在当前行后添加一行或多行。添加多行时，除最后一行外，每行行末需要用\代表数据未完结
	c\		行替换，用c后面的字符串替换原数据行，替换多行时，除最后一行外，每行行末尾用\代表数据未完结
	d		删除，删除指定行
	p		打印，输出指定行
	s		字串替换，用一个字符串替换另外一个字符串。格式为：航范围s/旧字串/新字串/g
```

例子：

```shell
sed -n '2p;3q' 文件名
#只输出文件第二行,并在读取第三行时退出，可以提高效率

sed '2,4d' 文件名
#删除文件第二行到第四行的数据，但不修改文件本身

sed '2a hello' 文件名
#在第二行后追加一行hello

sed '2i hello \ world' 文件名
#在第二行前插入两行数据

sed '2c No such person' 文件名
#将第二行替换为No such person


#字串替换
sed 's/旧字串/新字串/g' 文件名

sed '3s/74/99/g' 文件名
#把文件第三行的74替换为99，但不写入文件

sed '/74/ s/74/99/g'
#sed支持采用正则进行匹配和替换，考虑字符串替换的需求中，不防加上地址以提高速度。如果事先不知道在哪一行，可以先用正则匹配以调高效率。

sed -e '/student/ s/student//g;/teacher/ s/teacher//g' 文件
#把全文student和teacher全部替换为空
```

## 3 字符处理命令

### 3.1 排序命令sort

```shell
sort [选项] 文件名
	 -f			忽略大小写
	 -n			以数值型进行排序，默认使用字符串型排序
	 -r			反向排序
	 -t			指定分隔符，默认分隔符是制表符
	 -k n[,m]	按照指定的字段范围排序。从第n字段开始，m字段结束
```

例子：

```shell
sort /etc/passwd
#排序用户信息文件

sort -r /etc/passwd
#反向排序

sort -t ":" -k 3,3 /etc/passwd
#指定分隔符为:，用第三字段开头，第三字段结尾排序，就是只用第三字段排序
```

### 3.2 统计命令wc

```shell
wc [选项] 文件名
	-l		只统计行数
	-w		只统计单词数
	-m		只统计字符数
```

## 4 条件判断

### 4.1 按照文件类型进行判断

| 测试选项 | 作用                                 |
| -------- | ------------------------------------ |
| -d 文件* | 判断文件是否存在，是否为目录文件     |
| -e 文件* | 判断文件是否存在                     |
| -f 文件* | 判断文件是否存在，是否为普通文件     |
| -L 文件* | 判断文件是否存在，是否为链接文件     |
| -s 文件* | 判断文件是否存在，是否非空           |
| -b 文件  | 判断文件是否存在，是否为块设备文件   |
| -c 文件  | 判断文件是否存在，是否为字符设备文件 |
| -p 文件  | 判断文件是否存在，是否为管道文件     |
| -S 文件  | 判断文件是否存在，是否为套接字文件   |

```shell
test -e /root/install.log
[ -e /root/install.log ] #常用
#判断文件是否存在，表现在$?里

#常用写法
[ -e /root ] && echo "yes" || echo "no"
#中括号内两边一定要有空格
```

### 4.2 按照文件权限进行判断

| 测试选项 | 作用                                 |
| -------- | ------------------------------------ |
| -r 文件  | 判断该文件是否存在，是否有读权限     |
| -w 文件  | 判断该文件是否存在，是否有写权限     |
| -x 文件  | 判断该文件是否存在，是否有执行权限   |
| -u 文件  | 判断该文件是否存在，是否拥有SUID权限 |
| -g 文件  | 判断该文件是否存在，是否拥有SGID权限 |
| -k 文件  | 判断该文件是否存在，是否拥有SBit权限 |

```shell
[ -w /root ] && echo "yes" || echo "no"
#所有者，所属组，其他人，任意一个有写权限，就为真
```

### 4.3 两个文件之间进行比较

| 测试选项        | 作用                                            |
| --------------- | ----------------------------------------------- |
| 文件1 -nt 文件2 | 判断文件1是否比文件2新                          |
| 文件1 -ot 文件2 | 判断文件1是否比文件2旧                          |
| 文件1 -ef 文件2 | 判断文件1是否和文件2Inode号一致，用于判断硬链接 |

### 4.4 两个整数之间比较

| 测试选项      | 作用                           |
| ------------- | ------------------------------ |
| int1 -eq int2 | 判断两整数是否**相等**         |
| int1 -ne int2 | 判断两整数是否**不等**         |
| int1 -gt int2 | 判断整数1是否**大于**整数2     |
| int1 -lt int2 | 判断整数1是否**小于**整数2     |
| int1 -ge int2 | 判断整数1是否**大于等于**整数2 |
| int1 -le int2 | 判断整数1是否**小于等于**整数2 |

为什么不直接用符号比较？

也可以直接用符号比较

=和==是同意，通常用=

### 4.5 字符串的判断

| 测试选项       | 作用                       |
| -------------- | -------------------------- |
| -z 字符串      | 判断字符串是否为空         |
| -n 字符串      | 判断字符串是否有值         |
| 字串1 = 字串2  | 判断字串1是否和字串2相等   |
| 字串1 != 字串2 | 判断字串1是否和字串2不相等 |

### 4.5 多重条件判断

| 测试选项       | 作用                                     |
| -------------- | ---------------------------------------- |
| 判断1 -a 判断2 | 逻辑与，判断1和判断2都成立，最终结果为真 |
| 判断1 -o 判断2 | 逻辑或                                   |
| !判断          | 逻辑非                                   |

## 5 流程控制

### 5.1 if语句

#### 单分支if条件语句

```shell
if [ 条件判断式 ] ; then
	程序
fi
或者
if [ 条件判断式 ]
	then
		程序
fi
#[]与条件判断式之间有空格
```

-   `if`语句使用`fi`结尾
-   `[ 条件判断式 ]`是`test`命令判断，中括号与条件判断式之间必须有空格
-   `then`后面跟符合条件后执行的程序，可以放在`[]`后用`;`隔开，也可以换行写，就不需要`;`了

例子：

```shell
#!/bin/bash
#统计根分区使用率
 
rate=$(df -h | grep "/dev/vda1" | awk '{print $5}' | cut -d "%" -f1)
#把根分区使用率作为变量值赋予变量rate

if [ $rate -ge 80 ]
	then
		echo "Warning! /dev/vda1 is full!!"
fi

```

#### 双分支if条件语句

```shell
if [ 条件判断式 ]
	then
		条件成立，执行程序
	else
		条件不成立，执行程序
fi
```

例1：

```shell
#!/bin/bash
#备份mysql数据库

ntpdate asia.pool.ntp.org &>/dev/null
#同步系统时间
date=$(date +%y%m%d)
#把当前系统时间按照“年月日”格式赋予变量date
size=$(du -sh /var/lib/mysql)
#统计mysql数据库大小，并把大小赋予size变量

if [ -d /tmp/dbbak ]
	then
		echo "Date : $date!" > /tmp/dbbak/dbinfo.txt
		echo "Date size : $size" >> /tmp/dbbak/dbinfo.txt
		cd /tmp/dbbak
		tar -zcf	mysql-lib-$date.tar.gz /var/lib/mysql dbinfo.txt &>/dev/null
		rm -rf /tmp/dbbak/dbinfo.txt
	else
		mkdir /tmp/dbbak
		echo "Date : $date!" > /tmp/dbbak/dbinfo.txt
		echo "Date size : $size" >> /tmp/dbbak/dbinfo.txt
		cd /tmp/dbbak
		tar -zcf	mysql-lib-$date.tar.gz /var/lib/mysql dbinfo.txt &>/dev/null
		rm -rf /tmp/dbbak/dbinfo.txt
fi
```

重要文件的备份至少都需要每天执行一次

例子2：

```shell
#!/bin/bash
#判断apache是否启动，每15min执行一次，保证apache正常运行

port=$(nmap -sT 192.168.1.156 | grep tcp | grep http | awk '{print $2}')
#使用nmap命令扫描服务器，并截取apache服务的状态，赋予变量port
if [ "$port" == "open" ]
	then
		echo "$(date) httpd is ok!" >> /tmp/autostart-acc.log
	else
		/etc/rc.d/init.d/httpd restart &>/dev/null
		echo "$(date) restart httpd !!" >> /tmp/autostart-err.log
fi
```

#### 多分支if条件语句

```shell
if [ 条件判断式1 ]
	then
		当条件判断式1成立时，执行程序1
elif [ 条件判断式2 ]
	then
		当条件判断式1不成立，条件判断式2成立时，执行程序2
elif ...

else 
	当所有条件都不成立时，最后执行此程序
fi
```

例：

```shell
#!/bin/bash
#判断用户输入的文件类型

read -p "please input a filename: " file
#接受键盘的输入，并赋予变量file

if [ -z "$file" ]
#判断file变量是否为空
	then
		echo "Error, please input a filename"
		exit 1
elif [ ! -e "$file" ]
#判断file的值是否存在
	then
		echo "Your input is not a file!"
		exit 2
elif [ -f "$file" ]
#判断是否为普通文件
	then
		echo "$file is a regulare file!"
elif [ -d "$file" ]
#判断是否为目录文件
	then
		echo "$file is a directory!"
else
	echo "$file is an other file!"
fi
```

用if做错误判断的时候，一旦程序出错就应该用`exit 返回值`退出脚本，否则程序还会继续往下执行

### 5.2 case语句

#### 多分支case条件语句

与if的不同：case只能判断一种条件关系

``` shell
case $变量名 in
	"值1")
		如果变量的值为值1，则执行程序1
		;;
	"值2")
		如果变量的值为值2，则执行 程序2
		;;
	...
	*)
		如果变量以上值都不是，则执行此程序
		;;
esac
#适用于列表选项
#注意程序过后要有两个分号;;
#值要用""包围
#最后的 * 不用引号包围
```

### 5.3 for循环

#### 语法一`for...in...`

```shell
for 变量 in 值1 值2 值3...
	do
		程序
	done
```

例子：

```shell
#!/bin/bash
#批量解压

cd ~/download
ls *.tar.gz > ls.log
for i in $(cat ls.log)
	do
		tar -zxf $i &> /dev/null
	done
rm -rf ~/download/ls.log

#for会自动以空格或换行符为分隔符来循环
```

#### 语法二`for ((...))`

```
for ((初始值;循环控制条件;变量变化))
	do
		程序
	done
```

例子1：

```shell
#!/bin/bash
#从1加到100

s=0
for ((i=1;i<=100;i++))
	do
		s=$(($s+$i))
	done
echo "The sum is: $s"
#这种循环加的运算效率比较低，用awk进行循环加效率要高几倍
```

例子2：

```shell
#!/bin/bash
#批量添加指定数量的用户

read -p "Please input user name: " -t 30 name
read -p "Please input the number of users: " -t 30 num
read -p "please input the password of users: " -t 30 pass
if [ ! -z "$name" -a ! -z "$num" -a ! -z "$pass" ]
#判断非空
	then
		y=$(echo $num | sed 's/[0-9]//g')
		#判断数字
			if [ -z "$y" ]
				then
				#$num是全数字，开始循环添加用户
					for((i=1;i<=$sum;i++))
						do
							/usr/sbin/useradd $name$i &>/dev/null
							echo $pass | /usr/bin/passwd --stdin "$name$i" &>/dev/null
						done
			fi
fi
```

### 5.4 while循环与until循环

#### while循环

while循环是不定循环，也称条件循环。只要条件判断式成立，循环就会一直继续，直到条件判断式不成立，循环才会停止。

```shell
while [ 条件判断式 ]
	do
		程序
	done
```

例1：

```shell
#!/bin/bash
#从1加到100

i=1
s=0
while [ $i -le 100 ]
#如果变量i的值小于等于100，则执行循环
	do
		s=$(($s+$i))
		i=$(($i+1))
	done
echo "The sum is: $s"
```

#### until循环

until循环，和while循环相反，条件判断式不成立则进入循环。

```shell
until [ 条件判断式 ]
	do
		程序
	done

#要实现和上面例子一样的算法，只需要将条件判断式小于等于 -le改为大于 -gt就可以了
```

