[TOC]



## bash

bash是linux系统的用户界面，提供了用户和操作系统之间的交互，他接受用户的输入，让它送给操作系统执行

- 目前是Linux和mac上默认的shell

- centos默认使用bash

  ```shell
  [root@localhost ~]# echo $SHELL # 显示当前使用的shell
  /bin/bash
  ```

- 查看系统内可以使用的shell

  ```shell
  [root@localhost ~]#cat /etc/shells
  /bin/sh
  /bin/bash
  /sbin/nologin
  /usr/bin/sh
  /usr/bin/bash
  /usr/sbin/nologin
  /bin/tcsh
  /bin/csh
  ```

- 换shell

  ```shell
  chsh -s /bin/csh
  ```



## 显示提示符格式

```shell
[root@localhost ~]# echo $PS1
[\u@\h \W]\$
\u 代表当前登录的用户
\h 代表当前主机的主机名
\W 代表当前的目录
0表示默认字体，1表示加粗，4在字体下方加下划线 5 闪烁 7 代表突出显示
31-37 字体颜色
40-47 表示背景颜色
echo 'PS1="\[\e[1;35mm\][\u@\h \W]\\$\[\e[0m\]"' >> /etc/profile.d/ps.sh #永久生效

echo 'PS1="\[\e[7;47;34m\][\u@\h \W]\\$\[\e[0m\]"' >> /etc/profile.d/ps.sh

```



## 命令

执行命令：输入命令回车

内部命令：shell 自带的命令

- help 显示所有的内部命令

外部命令：第三方提供的命令

查看命令的类型：type

```shell
[root@localhost ~]#type top
top is /usr/bin/top
[root@localhost ~]#type echo 
echo is a shell builtin
```

## 别名

- 查看当前所有的别名

```shell
[root@localhost ~]#alias 
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'

```

- 自定义别名

```shell
[root@localhost etc]#alias cdetc='cd /etc'
# 就是上面这个
[root@localhost etc]#alias 
alias cdetc='cd /etc'
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'

```

- 取消别名

```shell
[root@localhost etc]#unalias cdetc 
[root@localhost etc]#cd
[root@localhost ~]#cdetc
bash: cdetc: command not found...
```

- 只对当前的终端有效

- 设置所有用户都可以用 /etc/bashrc

- 只对当前用户有效 ~/.bashrc

- 执行本身命令

  - \command

  - "command"

  - 'command'

  - path

    - ```shell
      which ls
      ```

      

## 命令格式

command [options.....] [args...]

command 命令本身

options：启动或者关闭命令里面的某些功能

- 长选项：--help --color
- 短选项： -i -l

args：命令的作用体，一般情况下是目录或者文件，用户名等等

注意：

- 短选项是可以合并
- 空格隔开
- ctrl+c 结束命令的执行
- 在同一行执行多个命令用；隔开
- 一个命令可以在多行显示用\连接



## 获取命令的帮助信息

内部命令：

- help command
- man bash（查看全部内部命令）

外部命令：

- command -h
- command --help
- man coomand
- 官方文档

![image-20201002200212573](https://i.loli.net/2020/10/02/ozIjaxUNCYe1VJL.png)

```shell
[root@localhost ~]#python --help
usage: python [option] ... [-c cmd | -m mod | file | -] [arg] ...
[] 可选项
<> 表示变化的数据
... 表示列表
a|b|c 或者
-abc 表示-a -b -c
{} 表示分组
```



## man

 

```shell
man man
```

```shell
1   Executable programs or shell commands  #用户命令
 2   System calls (functions provided by the kernel) # 系统调用
 3   Library calls (functions within program libraries) # 库的调用
 4   Special files (usually found in /dev) #设备文件与特殊文件
 5   File formats and conventions eg /etc/passwd # 配置文件格式
 6   Games #游戏
 7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7) # 杂项
 8   System administration commands (usually only for root) # 管理类的命令
 9   Kernel routines [Non standard] # 内核的API
 退出q
 翻屏 空格
 翻行 回车
 man 章节 passwd
```

## bash快捷键

- ctrl+l 清屏 相当于clear
- ctrl+o 执行当前的命令，并显示当前的命令
- ctrl+s 锁屏
- ctrl+q 解锁
- ctrl+c 终止命令
- ctrl+z 挂起命令
- ctrl+a 光标移动到行首
- ctrl+e光标移动到行尾
- ctrl+k 删除光标后面的字
- ctrl+u 删除光标前面的字
- alt+r 删除一整行



## tab键

- 命令补全
  - 内部命令
  - 外部命令：根据环境变量定义的路径，从前往后依次查找，自动匹配第一个查找到的内容
  - 如果用户给的命令只有唯一一个匹配，则直接补全
  - 如果有多个匹配，则需要在按tab键将所有匹配到的结果展示出来
- 目录补全
  - 把用户给定的字符作为文件的开头，如果有唯一一个匹配则直接补全
  - 如果有多个匹配，则需要再次按tab键把所有的匹配到的结果展示出来

## 引号

```SHELL
[root@localhost ~]#name=alexdsb
[root@localhost ~]#echo "$name"
alexdsb
[root@localhost ~]#echo '$name'
$name
[root@localhost ~]#echo "wo shi `tty`"
wo shi /dev/pts/2
[root@localhost ~]#tty
/dev/pts/2
[root@localhost ~]#echo "wo shi $(tty)"
wo shi /dev/pts/2
```





## 命令历史

- 可以使用上下箭头来查找之前执行过的命令
- 存放文件是~/.bash_history
- 执行的命令是history
- 执行上一条命令
  - 上箭头
  - ！！
  - ！-1
  - ctrl+p 回车
- 调用上一条命令的最后一个值 esc .
- !# 指定第多少条命令
- ！-# 指定倒数第#条命令
- ！string 用来最近一次匹配到的命令（从下往上）
- ctrl+r 搜索命令
- ctrl+g 取消搜索
- history # 显示最后#条命令

## 命令展开

```SHELL
touch file{1..20}
seq 0 2 10
echo file{1..20..2}
```

## echo 回显

```SHELL
echo -e 'dadasda\ndasdasd'
echo -e '\a' #播放声音
```



## 查看用户登录信息

```shell
[root@localhost ~]#whoami # 显示当前的登录用户
root
[root@localhost ~]#who am i #显示当前登录用户的详细信息
root     pts/2        2019-08-22 15:54 (192.168.21.1)
[root@localhost ~]#w 显示所有的用户并显示执行的命令
 16:27:54 up  5:19,  9 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty2                      11:37    4:48m  0.02s  0.02s -bash
wu       tty3                      11:39    4:48m  0.02s  0.02s -bash
root     :0       :0               11:30   ?xdm?   1:28   0.36s /usr/libexec/gnome-session-binary --session gnome-classi
root     pts/0    :0               11:35    4:43m  0.03s  0.03s bash
root     pts/1    192.168.21.1     12:11    4:03m  0.02s  0.02s -bash
root     pts/2    192.168.21.1     15:54    2.00s  0.18s  0.03s w
wu       pts/3    192.168.21.1     12:27    2:26   0.05s  0.05s -bash
```





## date

```SHELL
[root@localhost ~]#date  显示当前的时间
Thu Aug 22 16:30:06 CST 2019 
Usage: date [OPTION]... [+FORMAT]
  or:  date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
[root@localhost ~]#date 010923102018 #修改时间
Tue Jan  9 23:10:00 CST 2018
[root@localhost ~]#date
Tue Jan  9 23:10:18 CST 2018
[root@localhost ~]#ntpdate time.windows.com #同步网络服务器时间
unix元年 1970-01-01
[root@localhost ~]#date
Thu Aug 22 16:35:44 CST 2019
[root@localhost ~]#date
Thu Aug 22 16:35:47 CST 2019
[root@localhost ~]#date
Thu Aug 22 16:35:48 CST 2019
[root@localhost ~]#date +%a
Thu
[root@localhost ~]#date +%A
Thursday
[root@localhost ~]#date +%F
2019-08-22
[root@localhost ~]#date +%H
16
[root@localhost ~]#date +%I
04
[root@localhost ~]#date +%m
08
[root@localhost ~]#date +%d
22
[root@localhost ~]#date +%M
38
[root@localhost ~]#date +%h
Aug
[root@localhost ~]#date +%c
Thu 22 Aug 2019 04:38:42 PM CST
[root@localhost ~]#date +%T
16:39:01
[root@localhost ~]#date +%y
19
[root@localhost ~]#date +%Y
2019
[root@localhost ~]#date +%Y/m/%d
2019/m/22
[root@localhost ~]#date +%Y/%m/%d
2019/08/22
[root@localhost ~]#date +%s
1566463197
[root@localhost ~]#date +%W
33
```

## 显示时区

```shell
[root@localhost ~]#timedatectl 
      Local time: Thu 2019-08-22 16:42:43 CST
  Universal time: Thu 2019-08-22 08:42:43 UTC
        RTC time: Thu 2019-08-22 08:42:43
       Time zone: Asia/Shanghai (CST, +0800)
     NTP enabled: no
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
[root@localhost ~]#timedatectl set-timezone Asia/Tokyo
```



## 日历

cal

cal -y 一年的日历

cal # 显示某一年的日历



## 关机重启

- shutdown 默认1分钟之后关机
  - shutdown -c 取消关机
- shutdow -r 重启
- TIME
  - now 立即
  - +n n分钟之后
  - hh：mm  指定时间
- 关机命令
  - poweroff
  - halt
  - init 0
- 重启
  - reboot
    - -f 强制
    - -p 关机
  - init 6





## 系统环境变量查看

```shell
echo $PATH
```





## 额外

如果你觉得链接慢的话，你可以输入下面两个命令

```shell
[root@localhost ~]#echo "UseDNS no" >> /etc/ssh/sshd_config 
[root@localhost ~]#systemctl restart sshd
```

