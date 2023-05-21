## 目录

[TOC]

## 😉前言

最近了解到了一门新的技术《知识图谱》，这门技术被运用到了许许多多的方面，包括智能问答等等，打算借此兴趣来学习一下有关自然语言处理以及如何实现知识图谱。

所谓知识图谱，就是每一个点与每一个点之间有所联系。这里我们如果使用关系型数据库的话，当然也可以实现，使用多对多的方式，但是一旦我们的数据多了起来，就会十分的冗余，看起来也十分的不友好，所以我们可以运用我们的图数据库，他会很明显的显示出每个点与每个点之间有没有联系，以及有什么样的联系，图数据库有很多，我这里选择使用neo4j图数据库，因为这个图数据库比较成熟，也有很多人使用，与python交互也是十分友好的！

---

# ✨Neo4j

## 🎎关于Neo4j图数据库的下载

在安装neo4j之前，我们需要有jdk环境：

```
JDK安装：https://www.oracle.com/java/technologies/javase-downloads.html
```

neo4j的官网：

```
https://neo4j.com/
Neo4j安装：https://neo4j.com/download-center/
```

配置环境变量后可进行测试，测试命令如下：

```shell
neo4j console
```

如果出现以下画面则就是下载成功：

![image-20201027200259690](https://i.loli.net/2020/10/27/uj6GcvYRy8m4PXn.png)

进入后会让你进行登录，新用户默认的账号密码都是：neo4j



## 🎀Neo4j的使用

​	图数据库也是一个数据库，我们对于一个数据库，我们首先需要如何进行CURD，做一个好的CURD工程师！

​	打开neo4j数据库会有测试用例，我们可以用来先看看效果，请按照图片箭头进行点击：

![image-20201027201156516](https://i.loli.net/2020/10/27/ZK3AIz14LQatOoW.png)

​	之后点击运行箭头：

![image-20201027201425373](https://i.loli.net/2020/10/27/rPZC7hdaeAIMtUo.png)

​	就会出现提示，我们可以点击它给的create语句来生成一系列的数据来看看效果：

![image-20201027201541568](https://i.loli.net/2020/10/27/2R5gHLMVQPsWEU8.png)

​	这样我们一个简单的知识图谱就出现了！

![image-20201027201630351](https://i.loli.net/2020/10/27/Ff8JvDlZTkNtnB7.png)

​	看完效果之后我们就应该开始学习如何CURD了

​	在开始之前，我们先把我们有的数据全部删除掉：

```CQL
MATCH (n)
DETACH DELETE n
```

### 🚚增：

```CQL
create (n:Person {name:'xiaoming',age:18}) // 创捷一个节点，类别是Perseon类，其中name属性为：xiaoming，age属性为：18，这个n，就相当于这个节点的一个变量
```

![image-20201027202801990](https://i.loli.net/2020/10/27/qIZp6bnv4yDha5B.png)

#### 带有关系属性

```cql
create (p:Person{name:"我",age:"31"})-[:包工程{金额:10000}]->(n:Person{name:"好大哥",age:"35"}) // 创建两个节点，他们有着包工程这个联系
```

![image-20201027203009294](https://i.loli.net/2020/10/27/QKI3LEaBmGOnqJR.png)

### 🚈删：

```cql
create(n:Person{name:"Haha",age:1000}) // 创建一个节点
match (n:Person{name:"Haha"}) delete n // 找到这个n，然后把n删了
```

#### 删除关系

```cql
create (n:Person{name:"你",age:"41"})-[:爱情{金额:"998"}]->(p:Person{name:"小哥哥",age:"14"})

match (n:Person{name:"你",age:"41"})-[f:爱情]->(p:Person{name:"小哥哥",age:"14"})
delete f
```

### 🚛改：

```cql
match (t:Person) where id(t)=835 set t:好人 return t
```

![image-20201027204523588](https://i.loli.net/2020/10/27/GLAa4hRZUrNIfJS.png)

#### 加上属性

```cql
match (a:好人) where id(a)=835 set a.战斗力=200 return a
```

![image-20201027204717736](https://i.loli.net/2020/10/27/T8tkp6J7maCnMXb.png)

### 🚠查：

查的操作太多了，我们需要去查文档

```cql
match (p:Person) - [:包工程] -> (n:Person) return p,n // 找到所有Person类并且是有包工程关系的
```



### 💔快速清空数据库：

```cql
MATCH (n)
DETACH DELETE n
```



## 总结

​	以上就是neo4j图数据库的CURD操作了！