# 讲一讲Linux中MySQL的基础东西

[TOC]

### 下载

首先进入我们的镜像网站

```
http://mirror.sohu.com/
```

找到连接后，之后找到我们的rpm包，之后wget下载

![image-20201018085620693](https://i.loli.net/2020/10/18/vnytoYRMuV4rQsf.png)

下载完之后进行解压：

```mysql
tar -xf 你的压缩文件
```

之后进行安装rpm包：

```shell
yum install -y *.rpm
```

![image-20201018090305934](https://i.loli.net/2020/10/18/dfJTVPmul31jLKX.png)



## 配置文件的位置

```shell
/etc/my.cnf
```



不用理会

```shell
mkdir -p /mydata/mysql&chown mysql -R /mydata/mysql/

```



## 默认的安装位置：

```shell
/var/lib/mysql
```

### 启动

```
systemctl start mysqld
```



报错信息：

```shell
2019-08-30T11:18:22.976635Z 0 [Warning] Can't create test file /mydata/mysql/localhost.lower-test
2019-08-30T11:18:22.976687Z 0 [Note] /usr/sbin/mysqld (mysqld 5.7.27) starting as process 2788 ...
2019-08-30T11:18:22.980289Z 0 [Warning] Can't create test file /mydata/mysql/localhost.lower-test
2019-08-30T11:18:22.980338Z 0 [Warning] Can't create test file /mydata/mysql/localhost.lower-test
解决办法：setenforce 0

```



### 查看默认密码所在位置

```shell
/var/log/mysqld.log  # 日志所在位置
```

```shell
[root@localhost opt]#grep 'pass' /var/log/mysqld.log 
2020-10-18T01:10:52.260835Z 1 [Note] A temporary password is generated for root@localhost: /elfX97lBdr=

```



### 改密码

```
mysql_secure_installation
```

![image-20201018092857247](https://i.loli.net/2020/10/18/gTq7VMJ63RtSCpF.png)

但是我报错了，因为我改了他socket文件的目录，你们没有改的应该是没有关系的

![image-20201018093054876](https://i.loli.net/2020/10/18/aUN6hLo1qPXO5dt.png)

更改了一下配置文件，现在就可以修改密码了

```
输入root密码
是否要修改密码
是否要修改root密码（大小写、数字、特殊字符）
是否要删除匿名用户
是否禁止root远程登录
是否要删除test数据库
是否要刷新表的权限
```

简单来说就一路y就好了

## 密码校验规则

```SHELL
设置密码的校验规则
mysql> set global validate_password_policy=0;
0 校验级别最低，只校验密码的长度，长度可以设定
1 必须包括大写字母、小写字母、数字、特殊字符
2 必须满足上面两条，并追加，对于密码中任意连续的4个（或者4个以上） 字符不能是字典中的单词
mysql> set global validate_password_length=3; 修改密码的最短长度
```

## 创建用户

```shell
create user 'username'@'ip' identified by 'password';
全部ip的话则是%
```

## 查看权限

```shell
show grants;
```

## 关于MySQL中的主从

### 关于主从的定义：

主从同步使得数据可以从一个数据库服务器复制到其他服务器上，在复制数据时，一个服务器充当主服务器（master），其余的服务器充当从服务器（slave）。因为复制是异步进行的，所以从服务器不需要一直连接着主服务器，从服务器甚至可以通过拨号断断续续地连接主服务器。通过配置文件，可以指定复制所有的数据库，某个数据库，甚至是某个数据库上的某个表。

### 使用主从的好处

1. 通过增加从服务器来提高数据库的性能，在主服务器上执行写入和更新，在从服务器上向外提供读功能，可以动态地调整从服务器的数量，从而调整整个数据库的性能。
2. 提高数据安全-因为数据已复制到从服务器，从服务器可以终止复制进程，所以，可以在从服务器上备份而不破坏主服务器相应数据
3. 在主服务器上生成实时数据，而在从服务器上分析这些数据，从而提高主服务器的性能

#### 主从同步机制

Mysql服务器之间的主从同步是基于二进制日志机制，主服务器使用二进制日志来记录数据库的变动情况，从服务器通过读取和执行该日志文件来保持和主服务器的数据一致。

在使用二进制日志时，主服务器的所有操作都会被记录下来，然后从服务器会接收到该日志的一个副本。从服务器可以指定执行该日志中的哪一类事件（譬如只插入数据或者只更新数据），默认会执行日志中的所有语句。

每一个从服务器会记录关于二进制日志的信息：文件名和已经处理过的语句，这样意味着不同的从服务器可以分别执行同一个二进制日志的不同部分，并且从服务器可以随时连接或者中断和服务器的连接。

主服务器和每一个从服务器都必须配置一个唯一的ID号（在my.cnf文件的[mysqld]模块下有一个server-id配置项），另外，每一个从服务器还需要通过CHANGE MASTER TO语句来配置它要连接的主服务器的ip地址，日志文件名称和该日志里面的位置（这些信息存储在主服务器的数据库里）

## MySQL的主从配置

主服务器上MySQL添加的配置如下

```mysql
server-id=1 # 设置id,保证唯一就好
log-bin=/mydata/log/master-bin # 启动binlog日志，就是哪个
sync_binlog = 1 # 确保主从复制事务安全
```

主服务器执行如下sql

```mysql
mysql> grant replication slave on *.* to 'slave'@'192.168.21.131' identified by '1234';
# 创建一个从服务器的用户
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql> flush privileges;
# 刷新
Query OK, 0 rows affected (0.01 sec)
mysql> show master status\G
*************************** 1. row ***************************
             File: master-bin.000002 # 二进制文件
         Position: 1398 # 大小
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 
1 row in set (0.00 sec)

```

从服务配置如下：

```mysql
server-id =12 # 唯一id
relay_log =/mydata/log/slave-log #binlog传到备机被写道relaylog里，备机的slave sql线程从relaylog里读取然后应用到本地。

sync_binlog = 1 # 确保主从复制事务安全
read-only=ON
```

从服务器执行如下sql：

```mysql
# 连接主库
change master to master_host='192.168.21.128',master_user='slave',master_password='1234';
# 启动进程
start slave;
# 查看状态
show slave status\G
```

![image-20201019214840898](https://i.loli.net/2020/10/19/QNZE6doqp9HunP5.png)

看见这两个yes就代表配置成功！！

然后你在主数据库中创建一个数据库：

![image-20201019215005249](https://i.loli.net/2020/10/19/snPTMz4DAcNXSFe.png)

查看从数据库：

![image-20201019215033048](https://i.loli.net/2020/10/19/4IsWxCrDw19PjBz.png)

没有问题，成功了！

## 备份

mysqldump

- 锁表
- 备份特别慢，适用于数据量较小
- 不可以做增量备份
- 单线程

```SHEL
 -A, --all-databases 所有的库
 -B 指定备份的库
 —F 备份前刷新日志
 --flush-privileges 刷新授权表
 -p 密码
 -u 用户
 -P 端口
 触发器
 存储过程和存储函数
 ## 备份
 mysqldump -uroot -S /mydata/mysql/mysql.sock -A -p > mysql.sql
 ## 恢复一
 直接在数据库里面source mysql.sql文件
 ## 恢复二
 mysql -uroot -p < mysql.sql
```

## xtrabackup

```
https://www.percona.com/doc/percona-xtrabackup/2.4/installation/yum_repo.html
```

- 多进程
- 支持增量备份
- 锁行

```SHELL
安装
yum install https://repo.percona.com/yum/percona-release-latest.noarch.rpm # 安装yum仓库
yum install -y percona-xtrabackup-24
--target-dir=name # 指定备份生成的目录
-u # 一用户
-p # 密码
--backup 备份
--prepare 准备
--databases=name    filtering by list of databases.
--databases-file=name 配置文件
```

### 创建一个用户

```SHELL
mysql> create user 'backup'@'localhost' identified by 'backup'; # 创建一个用户
Query OK, 0 rows affected (0.00 sec)

mysql> grant reload,lock tables,process,replication client on *.* to 'backup'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

```

## 备份

```SHELL
xtrabackup --backup --target-dir=/mydata/backup/base -ubackup -pbackup --socket=/mydata/mysql/mysql.sock

# -ubackup -pbackup --> 账号密码

### 看得如下信息，代表成功
xtrabackup: Transaction log of lsn (5480853) to (5480862) was copied.
190902 09:25:09 completed OK!
```

## 恢复

#### 准备恢复数据的文件

```SHELL
xtrabackup --prepare --target-dir=/mydata/backup/base
InnoDB: Starting shutdown...
InnoDB: Shutdown completed; log sequence number 5482536
190902 09:28:12 completed OK!
```

#### 恢复文件

```SHELL
cd /mydata/backup/base
cp -rf hello /mydata/mysql/
chown mysql.mysql /mydata/mysql/hello/ -R
```

#### 恢复全部文件

```SHELL
xtrabackup --copy-back --target-dir=/mydata/backup/base
chown mysql.mysql * -R
# 重启
```

### 增量备份

```shell
xtrabackup --backup --target-dir=/mydata/backup/t1 --incremental-basedir=/mydata/backup/base -uroot -p --socket=/mydata/mysql/mysql.sock
# --incremental-dir 全备的路径
xtrabackup --backup --target-dir=/mydata/backup/t2 --incremental-basedir=/mydata/backup/t1 -uroot -p --socket=/mydata/mysql/mysql.sock
# --incremental-basedir应该是上一次的增量备份目录
xtrabackup --prepare --apply-log-only --target-dir=/mydata/backup/base
```

### 恢复(只能全部删除以后再恢复)

```shell
xtrabackup --prepare --apply-log-only --target-dir=/mydata/backup/base
xtrabackup --prepare --apply-log-only --target-dir=/mydata/backup/base --incremental-dir=/mydata/backup/t1
xtrabackup --prepare --apply-log-only --target-dir=/mydata/backup/base --incremental-dir=/mydata/backup/t2
xtrabackup --copy-back --target-dir=/mydata/backup/base
chown mysql.mysql * -R
systemctl restart mysqld
```



