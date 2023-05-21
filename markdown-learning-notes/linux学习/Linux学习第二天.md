[TOC]



## Linux文件目录结构

![image-20201004104213617](https://i.loli.net/2020/10/04/LWK8UwIury9cSEB.png)



- 文件和目录被组织成一颗倒置的树状结构
- 文件系统从“/”（根）开始
- 文件名称严格区分大小写（windows是不区分大小写的）
- 隐藏文件以“.”开头
- 文件的分割符为“/”



## 文件命名规范

- 文件字符最长为255个字符
- 包括路劲在内，文件名称最长为**4095**个

- 文件颜色：

![image-20201004104742090](https://i.loli.net/2020/10/04/FIMYCq2wuxKsm1z.png)

- 蓝色 -> 文件夹（目录）

![image-20201004104914122](https://i.loli.net/2020/10/04/BF1YV3ZT6olxaOK.png)

- 绿色 -> 可执行文件
- 红色 -> 压缩文件
- 蓝绿色 -> 连接文件
- 灰色 -> 其他文件
- 白色 -> 文件
- 除了斜杠和NULL，其他所有的字符都可以使用

![image-20201004105251603](https://i.loli.net/2020/10/04/rmQNdi6xlnRDyu7.png)

- 对大小写很敏感

## 文件系统结构

- /boot 存放的是引导文件，内核文件，引导加载
  - 不同的文件去引导不同的顺序
- /bin 所有用户都可以使用的命令
- /sbin 管理类的命令（管理员使用的命令）
- /lib  启动时程序使用的基本库文件
  - 库文件
    - 在window里面以".dll"结尾的文件，给别人提供api的
    - 在linux里面以“.so”结尾
- /lib64 专门存放x86_64系统上的辅助库文件
- /etc 用来存放配置文件的
- /home 普通用户的家目录

![image-20201004131106705](https://i.loli.net/2020/10/04/LbcQ9H14ewj2p8n.png)

- /root 管理员的家目录
- /media 便携式移动设备的挂载点
- /mnt 临时文件的挂载点
- /dev 设备文件和特殊文件的存放位置
- /opt 第三方运用的安装位置
- /tmp 临时文件（放系统启动后会自自动把tmp里面的东西全部删掉，所以有用的东西就不要放在这里面）
- /usr 存放安装程序
- /var 放经常会变化的东西（比如说目录）
- /proc 存放内核启动和进程相关的虚拟文件
- /sys 输出当前系统上的硬件相关的一些文件
- /srv 系统上允许的服务用到的数据

## Linux应用程序的组成

- 二进制文件
  - /bin
  - /sbin
  - /usr/bin
  - /usr/sbin
  - /usr/local/bin
  - /usr/local/sbin
- 库文件
  - /lib
  - /lib64
  - /usr/lib
  - /usr/lib64
  - /usr/local/lib
  - /usr/local/lib64
- 配置文件
  - /etc
  - /etc/name
  - /usr/local/etc
- 帮助文件
  - /usr/share/man
  - /usr/share/doc
  - /usr/local/share/man
  - /usr/local/share/doc

## 绝对路径和相对路径

- 绝对路径
  - 以根开始
  - 完整的文件的存放位置
  - 可以读取到任何一个文件或者文件夹
- 相对路径
  - 不以根开始
  - 相对当前的位置来决定
  - 可以简短的表示一个文件或者文件夹
  - . 当前目录
  - .. 父级目录

## 目录名和基名

```shell
[root@localhost log]#basename /etc/sysconfig/network-scripts/ifcfg-ens33 
ifcfg-ens33
[root@localhost log]#basename /etc/sysconfig/network-scripts
network-scripts
[root@localhost log]#dirname /etc/sysconfig/network-scripts
/etc/sysconfig
[root@localhost log]#dirname /etc/sysconfig/network-scripts/ifcfg-ens33 
/etc/sysconfig/network-scripts
```

## 切换目录

cd 切换目录  change directory

- 可以使用相对路径
- 可以使用绝对路径

### 切换到家目录

```shell
[root@localhost log]#cd 
[root@localhost ~]#
```

### 切换到上一次的目录

```SHELL
[root@localhost ~]#cd /etc/sysconfig/network-scripts/
[root@localhost network-scripts]#cd -
/root
[root@localhost ~]#cd -
/etc/sysconfig/network-scripts
```

## 显示当前的工作目录

pwd print working directory

```SHELL
[root@localhost network-scripts]#pwd
/etc/sysconfig/network-scripts
# 查看链接的真正目录
[root@localhost /]#cd lib
[root@localhost lib]#pwd -P
/usr/lib
```



## 列出目录或者文件

ls list

命令格式：Usage: ls [OPTION]... [FILE]...

```SHELL
ls -a 显示所有文件
ls -l 以长格式显示文件列表
    total 21104
    drwxr-xr-x.  2           root root 26    Jul 24 21:52  Desktop
    权限        硬盘的引用次数  属主 属组  大小   访问时间        文件名称
ls -R 递归显示目录
ls -d 显示目录本身
ls -1（数字1） 文件分行显示
ls -S 安装文件大小排序
ls -r 倒序显示
ls -t 按照时间来排序
ls -lh 显示人类易读的方式
ls -d */ 显示当前目录下的文件夹
l. 只显示隐藏文件
```



## linux下的目录类型

![image-20201004161403937](https://i.loli.net/2020/10/04/oTUpP8wZnAEtxVh.png)

- \- 用来表示文件
- d 用来表示目录
- b 块设备 
- c 字符设备
- l 表示符号链接文件
- s socket套接字

## 查看文件状态

```SHELL
[root@localhost test2020]#stat anaconda-ks.cfg
  File: ‘anaconda-ks.cfg’
  Size: 1747      	Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d	Inode: 33574992    Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Context: system_u:object_r:admin_home_t:s0
Access: 2019-08-22 12:09:03.288000381 +0900 # 访问时间
Modify: 2019-08-22 11:47:12.262947345 +0900 # 修改时间
Change: 2019-08-22 11:47:12.262947345 +0900 # 改动时间
atime 访问时间  查看文件内容就会改变
mtime 修改时间  改变内容发生变化
ctime 改动时间  元数据发生变化
```

## touch

创建空文件和刷新时间，**如果文件存在，则刷新时间，如果文件不存在，则创建文件**

-a 只修改atime和ctime

-m 只修改mtime和ctime

## 文件通配符

- \* 所有
- ？ 匹配的是任意单个字符
- ~ 表示用户的家目录
- [123] 其中一个
- [^123] 取反
- [0-9] 表示数字
- file[a-z] 字母（有坑）缺少Z
- file[A-Z] 字母（有坑）缺少a
- [:lower:] 小写字母
- [:upper:] 大写字母
- [:alpha:] 所有字母 a-zA-Z
- [:alnum:] 表示字母和数字
- [:digit:] 表示数字

## 创建目录

mkdir 创建目录

- -p 递归创建
- -v 显示详细过程

## 显示目录树

安装：

```shell
yum install -y tree
```

tree 显示目录树

-d 只显示文件夹

-L # 只显示#层



## 删除目录

rmdir 只能删除非空目录

-p 递归删除空父目录

-v 显示删除过程

rm -rf 删除非空目录





## 复制文件和文件夹

cp copy 默认情况下是别名，原来本身命令是不提示覆盖的

![image-20201004173000354](https://i.loli.net/2020/10/04/Q89fYBJ1LIz5nKt.png)

```SHELL
Usage: cp [OPTION]... [-T] SOURCE DEST
  or:  cp [OPTION]... SOURCE... DIRECTORY
  or:  cp [OPTION]... -t DIRECTORY SOURCE... 
  -i 显示提示信息
  -n 不覆盖
  -r -R 递归复制
  -d 只复制链接文件，不复制源文件
  -a 归档
  -v 显示过程
  -b 备份原来的文件
  --backup=number 备份文件加上数字
  -p 保留原来的属性
  
```

- 如果源文件是文件的话
  - 目标是文件
    - 目标文件如果不存在的话，则新建目标文件，并把内容写到目标文件中
    - 如果目标文件存在的话，本来的命令是直接覆盖，建议使用-i来提示用户
  - 目标是文件夹
    - 在文件夹中新建一个同名的文件，并把文件内容写到新文件中
- 如果源文件为多个文件的话
  - 目标必须是文件夹，文件夹必须存在，其他情况都会报错
- 如果源文件是文件夹的话
  - 目标文件是文件： 不可以
  - 目标文件必须是文件夹，必须使用-r选项
  - 如果目标文件不存在：则直接创建目标文件夹，并把源文件夹的数据都复制到目标文件夹
  - 如果目标文件存在：
    - 如果是文件的话，则报错
    - 如果是文件夹：则在目标文件夹中创建同名文件夹，并把所有数据都复制到新文件夹

## 移动、重命名

mv move

```SHELL
Usage: mv [OPTION]... [-T] SOURCE DEST
  or:  mv [OPTION]... SOURCE... DIRECTORY
  or:  mv [OPTION]... -t DIRECTORY SOURCE...
-i 提示
-f 强制
-b 备份
--backup=number 备份后面加数字
-v 显示过程
```



## 删除

rm remove

```SHELL
Usage: rm [OPTION]... FILE...
-i 提示
-r -R 递归删除
-f 强制删除
rm -rf 慎用
rm -rf /*
cd /
rm -rf *
```



## 链接

### 软链接

- 相当于windows的快捷方式
- 创建命令 ln -s 源文件 目标文件
- 可以对目录做软链接
- 指向另外的一个文件或者目录的路径，大小是路径的长度的字符
- 对磁盘引用次数没有影响
- 可以跨分区
- 源文件发生改变，软链接会跟着发生变化
- 源文件删除，软链接不能访问

### 硬链接

- 磁盘引用次数会发生变化
- 指向的是硬盘上的同一块区域
- 磁盘的引用数会随着硬链接次数来增加
- 不能对目录做硬链接
- 不能跨越分区
- 源文件发生改变，硬链接也会跟着变化
- 源文件删除以后，硬链接可以访问

## 查看文件类型

file

## 输入和输出

-  标准输入 默认是来自键盘的输入 stdin 0
-  标准输出 默认输出到终端窗口  stdout 1
-  标准错误输出  默认输出到终端窗口 stderr 2

## I/O重定向

\> 覆盖

- \> 将标准输出重定向到文件中
- 2> 将错误输出重定向到文件中
- &> 将所有的输出都重定向到文件中

禁止、允许覆盖

- 禁止覆盖 set -C
- 允许覆盖 set +C

\>> 追加

\>> 将标准输出追加到文件中

2>> 将错误输出追加到文件中

&>> 将所有输出追加到文件中

## 标准输入和错误输入分开保存

```SHELL
[root@localhost ~]#ls f 45yuio > log.log 2> error.log
[root@localhost ~]#cat log.log 
f
[root@localhost ~]#cat error.log 
ls: cannot access 45yuio: No such file or directory

```

## 合并所有的输出

- &> 覆盖重定向
- &>> 追加重定向
- command > file 2>&1
- command >> file 2>&1
- ()：合并多个文件的输出
- /dev/null 黑洞

## 从文件导入stdin

tr 字符替换

```shell
-t 截断
-d 删除
-s 压缩，去重
-c 取反
[root@localhost ~]#tr 'a-z' 'A-Z' < /etc/issue
\S
KERNEL \R ON AN \M
[root@localhost ~]#tr 'a-z' 'A-Z' 
qwertyy
QWERTYY
12345678
12345678
ASDFGHJ
ASDFGHJ
qwertyuio
QWERTYUIO
^C
[root@localhost ~]#tr ab 12
ab
12
abb
122
asdfghjkl
1sdfghjkl
^C
[root@localhost ~]#tr abc 12
ab
12
abc
122
abc
122
^C
[root@localhost ~]#tr ab 123
ab
12
abb
122
avc
1vc
qbc
q2c
abc
12c
[root@localhost ~]#tr -t abc 12
abc
12c
ab
12
[root@localhost ~]#tr -d abc
qwertyui
qwertyui
an^H^H
n

abc

artyibrtyuiocrtyuiop
rtyirtyuiortyuiop
^C
[root@localhost ~]#tr -d abc < /etc/issue
\S
Kernel \r on n \m

[root@localhost ~]#cat /etc/issue
\S
Kernel \r on an \m
[root@localhost ~]#tr -s a
abc
abc
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaabc
abc
^C
[root@localhost ~]#tr -sc a 
aasdaaaaaaa
aasdaaaaaaa
^[[A^H^H^C
[root@localhost ~]#tr -sc a 
aaaaaaaaabbbbbbbbbbbbbccccccccddddddddddd
aaaaaaaaabcd
[root@localhost ~]#tr -dc a
aaaaaaaaaaaabbbbbbbbbb
asdfghjkqwertyuiozxcvbnmxcvbnm,.
aaaaaaaaaaaaa
ctrl+d 结束
[root@localhost ~]#tr -dc "a\n"
asdfghjk
a
wertyujk;l'



asdfghj
a
[root@localhost test]#tr -d a < issue > issue  处理完成以后不能写会到源文件，要写到新的文件中
[root@localhost test]#seq 1 10 > b
[root@localhost test]#cat b
1
2
3
4
5
6
7
8
9
10
[root@localhost test]#tr -d "\n" < b
12345678910[root@localhost test]#tr -d "\n" < b
[root@localhost test]#tr "\n" " " <b
1 2 3 4 5 6 7 8 9 10 [root@localhost test]#tr "\n" " " <b >c
[root@localhost test]#cat c
1 2 3 4 5 6 7 8 9 10 [root@localhost test]#tr " " "\n" <c
1
2
3
4
5
6
7
8
9
10
```

## 多行发送给stdin

```SHELL
# 第一种方式
[root@localhost test]#cat > f1
qwert
wertyui
wertyui
wertyuiopasdfghjk
sdfghjkl
sdfyhjkl;sdfghjkl;xcvb

# 第二种方式
[root@localhost test]#cat > f2 <<EOF
> qwerty
> qwertyu
> wertyui
> qwertyu
> EOF
EOF 不是必须得，只要两个相同就可以
```

## 管道

管道使用“|”来表示

命令1|命令2|命令3

- 把命令1的输出结果当做命令2的输出结果，把命令2的输出结果当成命令3的输入结果
- 默认情况下，管道只能传送标准输出
- 如果需要把错误输出也传递，则需要|&
- 一般用来组合多个命令
- 有一些命令是不接受管道的

```SHELL
[root@localhost test]#ls f1|tr 'a-z' 'A-Z'
F1
[root@localhost test]#ls f
ls: cannot access f: No such file or directory
[root@localhost test]#ls f|tr 'a-z' 'A-Z'
ls: cannot access f: No such file or directory
[root@localhost test]#ls f|&tr 'a-z' 'A-Z'
LS: CANNOT ACCESS F: NO SUCH FILE OR DIRECTORY
[root@localhost test]#echo file{1..20}|touch 
touch: missing file operand
Try 'touch --help' for more information.

```











