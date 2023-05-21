### 获取本地的IP地址和电脑名称

---

**文档中的定义**

```java
public class InetAddressextends Object implements Serializable
    此类表示Internet协议（IP）地址。 
```

**一些常用的方法**

```java
static InetAddress getByName​(String host) 
	确定主机名称的IP地址。  
String getHostAddress​() 返回文本显示中的IP地址字符串。  
String getHostName​() 获取此IP地址的主机名。  
```

**使用例子**

```java
public static void main(String[] args) throws UnknownHostException {
        InetAddress address = InetAddress.getByName("192.168.0.114");

        // 获取ip地址的主机名
        String name = address.getHostName();

        // 返回文本显示的IP地址字符串
        String ip = address.getHostAddress();

        System.out.println("主机名 " + name);
        System.out.println("IP地址 "+ ip);
    }
```

### UDP发送数据

---

![image-20200609231409437](https://i.loli.net/2020/06/09/J8wK5kQImZ37Nx6.png)

![image-20200609231500403](https://i.loli.net/2020/06/09/H8eEdhMVQwjJRKx.png)

下面我们来看一下具体的用到的方法的文档描述

```java
public class DatagramSocket extends Object implements Closeable
    此类表示用于发送和接收数据报数据包的套接字。 
    数据报套接字是分组传送服务的发送或接收点。 在数据报套接字上发送或接收的每个数据包都被单独寻址和路由。 从一个机器发送到另一个机器的多个分组可以不同地路由，并且可以以任何顺序到达。 
```

构造方法

```java
DatagramSocket​() 构造数据报套接字并将其绑定到本地主机上的任何可用端口。  
```

常用的方法

```
void close​() 关闭此数据报套接字。  
void send​(DatagramPacket p) 从此套接字发送数据报包。  
```

以上我们可以看见DatagramPacket，那么DatagramPacket是什么呢，我们继续去帮助文档里面看一下

```java
public final class DatagramPacket extends Object
	该类表示数据报包。 
	数据报包用于实现无连接分组传送服务。 仅基于该数据包中包含的信息，每个消息从一台机器路由到另一台机器。 从一台机器发送到另一台机器的多个分组可能会有不同的路由，并且可能以任何顺序到达。 包传送不能保证。 
```

构造方法

```java
DatagramPacket​(byte[] buf, int length, InetAddress address, int port) 
    构造一个数据包，发送长度为 length的数据包到指定主机上的指定端口号。  
DatagramPacket​(byte[] buf, int length) 
    构造一个 DatagramPacket用于接收长度为 length数据包。  
    
```

常用方法

```java
byte[] getData​() 返回数据缓冲区。  
```

使用小例子

```java
public static void main(String[] args) throws IOException {
        // 创建发送端的socket的对象
        // DatagramSocket() 构造数据包套接字并将其绑定到本地主机上的任何可用接口
        DatagramSocket ds = new DatagramSocket();

        // 创建数据，并把数据打包
        // DatagramPacket​(byte[] buf, int length, InetAddress address, int port)
        // 构造一个数据包，发送长度为 length的数据包到指定主机上的指定端口号。
        byte[] bys = "hello java".getBytes();
//        int lenght = bys.length;
//        InetAddress address = InetAddress.getByName("192.168.0.114");
//        int port = 10086;
        DatagramPacket dp = new DatagramPacket(bys,bys.length,InetAddress.getByName("192.168.0.114"),10086);

        // 调用DatagramSocket对象的方法发送数据
        // void send​(DatagramPacket p) 从此套接字发送数据报包。
        ds.send(dp);

        // 关闭发送端
        ds.close();
    }
```

### UDP接收数据

---

接收数据的步骤：

1. 创建接收端的Socket对象(datagramSocket)
2. 创建一个数据包，用于接收数据
3. 调用DatagramSocket对象的方法接收数据
4. 解析数据，并利用数据
5. 关闭接送端

```java
    public static void main(String[] args) throws IOException {
        DatagramSocket ds = new DatagramSocket(10086);

        byte[] bys = new byte[1024];
        // 指定包裹
        DatagramPacket dp = new DatagramPacket(bys, bys.length);

        ds.receive(dp);

        byte[] s = dp.getData(); // 拿到的是数据缓冲区

        int len = s.length; // 拿到的是实际的长度
        String dataString = new String(s, 0, len);
        System.out.println(dataString);

        ds.close();

    }
```

---

### UDP聊天室练习

发送端

```java
public static void main(String[] args) throws IOException {
        DatagramSocket ds = new DatagramSocket();

        //键盘录入数据
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String line;
        while ((line=br.readLine())!=null){
            if("886".equals(line)){
                break;
            }
            //创建数据并打包
            byte[] bys = line.getBytes();
            DatagramPacket dp = new DatagramPacket(bys,bys.length, InetAddress.getByName("192.168.0.114"),12345);

            // 调用DatagramSocket发送数据
            ds.send(dp);

        }

        // 关闭发送端
        ds.close();

    }
```

接收端

```java
public static void main(String[] args) throws IOException {
        DatagramSocket ds = new DatagramSocket(12345);

        while(true) {
            byte[] bys = new byte[1024];
            DatagramPacket dp = new DatagramPacket(bys, bys.length);

            ds.receive(dp);

            System.out.println(new String(dp.getData(), 0, dp.getLength()));
        }
//        ds.close();
    }
```

---

### TCP发送数据

![image-20200610155100960](https://i.loli.net/2020/06/10/oMrwnFdO6G2Cfsq.png)

socket的文档讲解

```java
public class Socket extends Object implements Closeable
    该类实现客户端套接字（也称为“套接字”）。 套接字是两台机器之间通讯的端点。
```

构造方法

```java
 Socket​(InetAddress address, int port) 
     创建流套接字并将其连接到指定IP地址的指定端口号。  
  Socket​(String host, int port) 
     创建流套接字并将其连接到指定主机上的指定端口号。  
```

常用方法

```JAVA
OutputStream getOutputStream​() 返回此套接字的输出流。  
```

![image-20200610162014961](https://i.loli.net/2020/06/10/iADKCW7kBvjhtud.png)

客户端小例子

```java
public static void main(String[] args) throws IOException {
        Socket s = new Socket("192.168.0.114",10000);

        // 获取输出流 应该是字节流
        // OutputStream getOutputStream​() 返回此套接字的输出流。
        OutputStream os = s.getOutputStream();
        os.write("hello java".getBytes());

        s.close();
    }
```

### TCP接收数据

---

**文档描述**

```java
public class ServerSocket extends Object implements Closeable
    这个类实现了服务器套接字。 服务器套接字等待通过网络进入的请求。 它根据该请求执行一些操作，然后可能将结果返回给请求者。 
```

**构造方法**

```
ServerSocket​(int port) 创建绑定到指定端口的服务器套接字。  
```

**方法**

```
Socket accept​() 侦听要连接到此套接字并接受它。  
```

TCP服务器例子

```java
public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(10001);

        // 服务器先不是要获取数据，而是先要监听
        Socket s = ss.accept();

        InputStream is = s.getInputStream();
        byte[] bys = new byte[1024];
        int len = is.read(bys); // int read​(byte[] b) 从输入流中读取一些字节数，并将它们存储到缓冲器阵列 b 。  
        
        String data = new String(bys,0,len);
        System.out.println(data);

        s.close();
        ss.close();
    }
```

![image-20200610162753758](https://i.loli.net/2020/06/10/whlzcLPKip8QWrC.png)

---

### 实战练习

**(1) 我们通过使用键盘输入让服务器收取到我们的数据，输入输出使用BufferedReader，BufferedWriter的方法**

客户端

```java
public class ClientDemo {
    public static void main(String[] args) throws IOException {
        // 创建客户端Socket对象
        Socket s = new Socket("192.168.0.114",10000);

        // 来自键盘录入
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(s.getOutputStream())); // 自己包装

        String line;
        while((line = br.readLine())!=null){
            if("886".equals(line)){
                break;
            }
            
            bw.write(line);
            bw.newLine();
            bw.flush();
        }

        // 释放资源
        s.close();
    }
}
```

服务器

```java
public class ServerDemo {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(10000);

        // 监听客户端的连接 返回Socket对象
        Socket s = ss.accept();

        // 获取输入流
//        InputStream is = s.getInputStream();
//        InputStreamReader isr = new InputStreamReader(is);
//        BufferedReader be = new BufferedReader(isr);
        BufferedReader br = new BufferedReader(new InputStreamReader(s.getInputStream()));

        String line;
        while ((line = br.readLine())!=null){
            System.out.println(line);
        }

        ss.close();
    }
}
```

(2)