## 😁目录

[TOC]



## nginx下载

网址：

```
http://nginx.org/
http://nginx.org/download/nginx-1.18.0.tar.gz
```

下载完后编译：



```shell
./configure --help

yum install gcc zlib2-devel pcre-devel openssl-devel

 ./configure --prefix=/opt/nginx --with-http_ssl_module --with-http_stub_status_module

make && make install
```



配置环境变量：

```shell
[root@localhost nginx]# vim /etc/profile.d/nginx.sh

vim中写入：
	PATH=/opt/nginx/sbin:$PATH
	
source /etc/profile.d/nginx.sh # 重读一下
```



启动：

```shell
nginx
ps -ef|grep nginx # 查看启动起来了嘛
ss -tnlp # 查看端口
```





## 目录结构

```shell
[root@localhost nginx]#ls
conf  html  logs  sbin
conf 配置文件
html 存放静态文件 index.html 是默认的欢迎页面
logs 日志目录
sbin 二进制文件
启动以后会生成一个主进程，根据配置文件的选项来生成子进程（工作进程），主进程不负责处理用户的请求，用来转发用户的请求，真正负责处理用户请求的是子进程
```

## 命令格式

```shell
./sbin/nginx -h
nginx version: nginx/1.16.1
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit 显示版本号
  -V            : show version and configure options then exit 显示版本+编译时选项
  -t            : test configuration and exit  测试配置文件
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /opt/nginx/)
  -c filename   : set configuration file (default: conf/nginx.conf)
  -g directives : set global directives out of configuration file
```

## 配置文件

```shell
#user  nobody;    使用哪个用户来启动子进程
worker_processes  1; #工作进程的个数，配置成cpu的核心数-1或者-2
# cpu亲缘性绑定，让nginx的子进程工作在哪个核心上

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    #use [epoll|select|poll];
    worker_connections  102400; # 每一个子进程可以处理的连接数
}


http {
    include       mime.types; #导入
    default_type  application/octet-stream; # 默认的请求方式
    # 定义日志格式
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    #定义日志并定义日志格式
    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65; # 保持长连接的超时时间

    #gzip  on;

    server {
        listen       80; #监听端口
        server_name  localhost; 

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;  # 指定静态文件地址
            index  index.html index.htm; # 指定默认的index页面
        }
		# 错误页面 找不到页面
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        # 错误页面 服务端错误
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

## 404页面

```SHELL
error_page  404              /404.html;
```

## root和alias的区别

```shell
 location /img {
 	root /data/img;
 }
root /data/img 里面必须有/img
 location /img {
 	alias /data/img;
 }
 alias /data/img 里面不需要有 /img
```

## 域名

```shell
server_name ms.s22.com
```

## 多域名访问

```shell
  server  {
        listen 80;
        server_name www.taobao.com taobao.com;
        location / {
        root /data/taobao;
        index index.html;
        }

}
   server  {
        listen 80;
        server_name www.jd.com jd.com;
        location / {
        root /data/jd;
        index index.html;
        }
        }
```

### 默认server

```shell
listen 80 default_server;
```

## nginx负载均衡

**服务器一**中的配置：

![image-20201020215708957](https://i.loli.net/2020/10/20/wB8RyuZhMgSHNWj.png)

**服务器二**中的配置

![image-20201020215946754](https://i.loli.net/2020/10/20/SmA4CoEqXlh8VjY.png)

### 权重

weight

```shell
upstream django {
	server 192.168.21.128:81 weight=3;
	server 192.168.21.131:81
}
 server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        proxy_pass http://django;
        }
        }
得到的结果是：
访问128的3次，才访问131的一次
```

### ip_hash

每个请求的ip做hash运算，这样每个固定的访客都会被负载到后端固定的机器

```shell
upstream django {
	ip_hash;
	server 192.168.21.128:81
	server 192.168.21.131:81
}
```

### backup

当前面的都访问不到，则请求backup的备份,只要有一个通，则不会走backup

```SHELL
upstream django {
	server 192.168.21.128:81;
	server 192.168.21.131:81;
	server 192.168.21.131:82 backup;
}
```



看完上面想必大家都把nginx配置好了，那我们就要开始下载配置uwsgi了，为什么我们要用uwsgi呢？

下面让我们来了解了解。



### WSGI 协议

WSGI：是一种协议规范，起到规范参数的作用，就像告诉公路一样，规定超车靠右行，速度不低于90km/h，等。但这一切都是对双方进行沟通，比如，重庆到武汉这条高速路，这儿重庆和武汉就各为一端，他们之间的行车规范就按照WSGI规则即可。我们现在需要记住，WSGI沟通的双方是wsgi server (比如uWSGI） 要和 wsgi application（比如django ）



### uWSGI

uWGSI：是一个web服务器，或者wsgi server服务器，他的任务就是接受用户请求，由于用户请求是通过网络发过来的，其中用户到服务器端之间用的是http协议，所以我们uWSGI要想接受并且正确解出相关信息，我们就需要uWSGI实现http协议，没错，uWSGI里面就实现了http协议。所以现在我们uWSGI能准确接受到用户请求，并且读出信息。现在我们的uWSGI服务器需要把信息发给Django，我们就需要用到WSGI协议，刚好uWSGI实现了WSGI协议，所以。uWSGI把接收到的信息作一次简单封装传递给Django，Django接收到信息后，再经过一层层的中间件，于是，对信息作进一步处理，最后匹配url，传递给相应的视图函数，视图函数做逻辑处理......后面的就不叙述了，然后将处理后的数据通过中间件一层层返回，到达Djagno最外层，然后，通过WSGI协议将返回数据返回给uWSGI服务器，uWSGI服务器通过http协议将数据传递给用户。这就是整个流程。

这个过程中我们似乎没有用到uwsgi协议，但是他也是uWSGI实现的一种协议，鲁迅说过，存在即合理，所以说，他肯定在某个地方用到了。我们过一会再来讨论

我们可以用这条命令：python manage.py runserver，启动Django自带的服务器，具体叫什么名字，我真不知道（知道的可以留言）。DJango自带的服务器（runserver 起来的 HTTPServer 就是 Python 自带的 simple_server）。是默认是单进程的，对于同一个http请求，总是先执行一个，其他等待，一个一个串行执行。无法并行。而且django自带的web服务器性能也不好，只能在开发过程中使用。于是我们就用uWSGI代替了。

### 为什么有了uWSGI为什么还需要nginx？

因为nginx具备优秀的静态内容处理能力，然后将动态内容转发给uWSGI服务器，这样可以达到很好的客户端响应。支持的并发量更高，方便管理多进程，发挥多核的优势，提升性能。这时候nginx和uWSGI之间的沟通就要用到uwsgi协议。



### 杂谈

django 的并发能力真的是令人担忧，这里就使用 nginx + uwsgi 提供高并发

nginx 的并发能力超高，单台并发能力过万（这个也不是绝对），在纯静态的 web 服务中更是突出其优越的地方，由于其底层使用 epoll 异步IO模型进行处理，使其深受欢迎

做过运维的应该都知道，

**Python需要使用nginx + uWSGI 提供静态页面访问，和高并发**

**php 需要使用 nginx + fastcgi 提供高并发，**

**java 需要使用 nginx + tomcat 提供 web 服务**





既然是这样，我们还不赶紧配置起来？

### 安装

```
pip3 install uwsgi -i https://pypi.douban.com/simple
```

准备你的django程序，这里我们放在我们的/opt/data/下

![image-20201021142254101](https://i.loli.net/2020/10/21/wgYdKtqshaOZ4FV.png)

这次我们配置的名称是Nfu_demo

然后我们去配置我们的uwsgi的配置文件

### 配置配置文件

uwsgi的配置文件在/etc/uwsgi.ini内

```ini
[uwsgi]
# 对外提供http服务的端口
http = :8080
# 用于和Nginx交互的接口，与uwsgi_pass搭配使用
socket = :9009
# django项目的目录
chdir = /opt/data/Nfu_demo
# django项目的wsgi文件
wsgi-file = Nfu_demo/wsgi.py
module = Nfu_demo.wsgi:application

# 进程个数
processes = 4
# 线程个数
threads = 2

# 清除临时文件
vacuum = true

# log文件
daemonize = /opt/data/Nfu_demo/django.log

# 不用自己重启
py-autoreload = 1

```

nginx中的配置：

```nginx
server{
        listen 80;
        server_name www.yxinmiracle.com;
        location / {
                include uwsgi_params;
        #       proxy_pass http://127.0.0.1:8080;
                uwsgi_pass 127.0.0.1:9009;
        }
        location /static { # 动静分离需要配置的
                root /opt/data/Nfu_demo;
        }
   }
```

### 动静分离

在django的配置中要写入

```python
SATAIC_ROOT = os.path.join(BASE_DIR,'static')
```

执行命令

```python
python3 manage.py collectstatic #用来收集静态文件
```

开始！

```
uwsgi --ini /etc/uwsgi.ini
```

如果开始不了的话我们用

```
python3 manage.py runserver 0.0.0.0:8080
```

查看一下是不是有一些bug，运行成功后再用uwsgi去运行

