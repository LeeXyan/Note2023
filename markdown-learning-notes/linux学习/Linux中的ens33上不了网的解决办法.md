[TOC]

## 😂解决虚拟机Linux ens33模式上不了问题！

首先，我们先输入：

```shell
ip a
```

查看一下我们的ip地址，但是上不了网的我们看到的是这样的：

```shell
[root@localhost ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:69:05:12 brd ff:ff:ff:ff:ff:ff 
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:e3:d8:d3 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:e3:d8:d3 brd ff:ff:ff:ff:ff:ff
```

接下来就要开始操作了！

```shell
echo "HWADDR=00:0c:29:69:05:12" >> /etc/sysconfig/network-scripts/ifcfg-ens33 
# 注意查看自己的数据，HWADDR不是每个人都一样的
```

然后！我们进入配置文件里面：
![image-20201020235625300](https://i.loli.net/2020/10/20/xqXUyrcM1a67Rbm.png)

将你的这个改成yes，之后开始重启：

```shell
service network restart
```

## 🤐最终办法

如果上面的办法还不行的话，那就试试重新装一个吧，我是装了第二次的时候才可以的，第一个死活不行！