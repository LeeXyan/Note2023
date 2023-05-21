### Linux上python的下载

去到python的官网下载，我下载的是xz版本

![image-20201021150342785](https://i.loli.net/2020/10/21/z6FjTH1E7vcZG9K.png)

将压缩包放入自己的linux系统中

之后解压文件：

```shell
tar -xf ........
```

准备编译

```shell
# 编译前可能需要安装一些依赖环境
yum install zlib2-devel openssl-devel
yum install zlib-devel openssl-devel
# 开始编译
./configure --prefix=/opt/python37
```

安装：

```shell
make && make install
```

请静静的等待.....

最后配置环境变量！

```shell
vim /etc/profile.d/python.sh
----------------------------
PATH=/opt/python37/bin:$PATH
----------------------------
source /etc/profile.d/python.sh
```

