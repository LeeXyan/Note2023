

# 💮目录

[TOC]

## es环境配置
- java jdk：1.8及以上
    - JAVA_HOME
    - Path
- elasticsearch
    - 解压即安装
- kibana
    - 解压即安装
    

**注意**
kibana和es、ik分词的版本保持一致



## 增删改查操作

es第一天

我们在es中进行增删改查可以进入kibana-web界面进行，界面大概效果如下：

![image-20210131095547735](https://gitee.com/yxinmiracle/pic/raw/master/20210131095554.png)



### 增

---

增加操作我们使用的是**PUT**关键字，具体代码如下：

```tiki wiki
# a1这个索引下面，doc这个类型下面创建1篇文档
PUT a1/doc/1
{
  "name":"cyx的哈哈哈哈",
  "age":18
}
```

点击运行之后会有如下的输出信息：

![image-20210131100102572](https://gitee.com/yxinmiracle/pic/raw/master/20210131100135.png)



但是我们运行的时候会有卡顿，这是为什么呢？因为在第一次创建该a1索引的时候会先去检查之前有没有这个a1索引，如果有的话就不创建，如有没有才给创建，所以就多了一个检查的过程，我们可以在创建一个a1节点，这样速度就会很快了

![image-20210131100504416](https://gitee.com/yxinmiracle/pic/raw/master/20210131100504.png)



### 查

---

取数据的关键词是**GET**，实例代码如下：

```tiki wiki
GET a1/doc/1
```

结果：

![image-20210131100805361](https://gitee.com/yxinmiracle/pic/raw/master/20210131100805.png)

#### GET一个文档里面的全部信息那我们应该如何得到呢？

```tiki wiki
GET a1/doc/_search
```

结果如下所示：

![image-20210131101146092](https://gitee.com/yxinmiracle/pic/raw/master/20210131101146.png)



#### 查询一个集群里面的所有索引

```
GET _cat/indices
```

结果：

![image-20210131101735160](https://gitee.com/yxinmiracle/pic/raw/master/20210131151426.png)





#### DSL结构化的查询语句

要求：查询所有age为18的数据：

```
GET a1/doc/_search
{
  "query":{
    "match": {
      "age": 18
    }
  }
}
```

查询结果如下图所示：
![image-20210131152107417](https://gitee.com/yxinmiracle/pic/raw/master/20210131152107.png)



#### match与查询match_phrase查询的区别

match查询会将查询的词语进行分词，比如我们现在的数据是：

![image-20210131153107277](https://gitee.com/yxinmiracle/pic/raw/master/20210131153107.png)

现在需要搜索tags中含有有钱这个词语的数据有哪些，假如我们使用match关键字查询的话，这样会它会将有钱拆分为有和钱，然后将tags中包含有和钱这两个字的数据查询出来，按照符合程度叫降序排列，如下所示：

![image-20210131153336411](https://gitee.com/yxinmiracle/pic/raw/master/20210131153336.png)

要是我们使用**match_phrase**查询的话，该关键字是词语查询，这样只有是这个词语的才会搜索出来

```
GET a1/doc/_search
{
  "query": {
    "match_phrase": {
      "tags": "有钱"
    }
  }
}
```

![image-20210131153447598](https://gitee.com/yxinmiracle/pic/raw/master/20210131153447.png)

#### slop关键字

这个关键字是词距的意思

```
GET a1/doc/_search
{
  "query": {
    "match_phrase": {
      "docx": {
        "query":"中国世界",
        "slop":1
      }
    }
  }
}
```

![image-20210131154213487](https://gitee.com/yxinmiracle/pic/raw/master/20210131154213.png)

### 改

改语句的关键字为POST：

```tiki wiki
POST a1/doc/2/_update
{
  "doc":{
    "age":14
  }
}
```

效果如下：

![image-20210131151420671](https://gitee.com/yxinmiracle/pic/raw/master/20210131151420.png)

### 删

---

删除操作的关键字是**DELETE**，代码：

```
DELETE a1/doc/3
```

#### 都删除

```
DELETE a1/
```

e

## 快速上手

- 逻辑设计，我们可以把elasticsearch与关系型数据做个客观对比：

|   Relational DB    |  Elastisearch   |
| :----------------: | :-------------: |
| 数据库（database） | 索引（indices） |
|    表（tables）    |      types      |
|     行（rows）     |    documents    |
|  字段（columns）   |     fields      |

elasticsearch（集群）中可以包含多个索引（数据库），每个索引中可以包含多个类型（表），每个类型下又包含多个文档（行），每个文档中又包含多个字段（列）。

- 物理设计，在elasticsearch后台是如何处理这些数据的呢？elasticsearch将每个索引划分为多个分片，每份分片又可以在集群中的不同服务器间迁移。

### 逻辑设计：文档、类型、索引

一个索引类型中，包含多个文档，比如说文档1，文档2。当我们索引一篇文档时，可以通过这样的顺序找到它：`索引`▷`类型`▷`文档ID`，通过这个组合我们就能索引到某个具体的文档。
注意：ID不必是整数，实际上它是个字符串。

### 文档

之前说elasticsearch是面向文档的，那么就意味着索引和搜索数据的最小单位是文档，`elasticsearch`中，文档有几个重要属性：

- 自我包含，一篇文档同时包含字段和对应的值，也就是同时包含`key:value`
- 可以是层次型的，一个文档中包含自文档，复杂的逻辑实体就是这么来的
- 灵活的结构，文档不依赖预先定义的模式，我们知道关系型数据库中，要提前定义字段才能使用，在elasticsearch中，对于字段是非常灵活的，有时候，我们可以忽略该字段，或者动态的添加一个新的字段。
- 文档是无模式的，也就是说，字段对应值的类型可以是不限类型的。

尽管我们可以随意的新增或者忽略某个字段，但是，每个字段的类型非常重要，比如一个年龄字段类型，可以是字符串也可以是整型。因为elasticsearch会保存字段和类型之间的映射及其他的设置。这种映射具体到每个映射的每种类型（[因此带来的问题](https://www.cnblogs.com/Neeo/articles/10393961.html)），这也是为什么在elasticsearch中，类型有时候也称为映射类型。

### 类型

类型是文档的逻辑容器，就像关系型数据库一样，表格是行的容器。
类型中对于字段的定义称为映射，比如`name`映射为字符串类型。
我们说文档是无模式的，它们不需要拥有映射中所定义的所有字段，比如新增一个字段，那么elasticsearch是怎么做的呢？elasticsearch会自动的将新字段加入映射，但是这个字段的不确定它是什么类型，elasticsearch就开始猜，如果这个值是18，那么elasticsearch会认为它是整型。
但是elasticsearch也可能猜不对，所以最安全的方式就是提前定义好所需要的映射，这点跟关系型数据库殊途同归了，先定义好字段，然后再使用，别整什么幺蛾子。后面在讨论更多关于映射的东西。

### 索引

索引是映射类型的容器，elasticsearch中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他设置。然后它们被存储到了各个分片上了。
我们来研究下分片是如何工作的。

### 物理设计：节点和分片

一个集群包含至少一个节点，而一个节点就是一个elasticsearch进程。节点内可以有多个索引。
默认的，如果你创建一个索引，那么这个索引将会有5个分片（**primary shard**，又称主分片）构成，而每个分片又有一个副本（**replica shard**，又称复制分片），这样，就有了10个分片。

那么这个索引是如何存储在集群中的呢？

![image-20210131145909407](https://gitee.com/yxinmiracle/pic/raw/master/20210131145909.png)

上图是一个有3个节点的集群，可以看到主分片和对应的复制分片都不会在同一个节点内，这样有利于某个节点挂掉了，数据也不至于丢失。
实际上，一个分片是一个Lucene索引，一个包含倒排索引的文件目录，倒排索引的结构使得elasticsearch在不扫描全部文档的情况下，就能告诉你哪些文档包含特定的关键字。
不过，等等，倒排索引是什么鬼？

### 倒排索引

elasticsearch使用的是一种称为倒排索引的结构，采用Lucene倒排索作为底层。这种结构适用于快速的全文搜索，一个索引由文档中所有不重复的列表构成，对于每一个词，都有一个包含它的文档列表。
倒排列表（Posting List）记录了词条对应的文档集合，由倒排索引项（Posting）组成。

倒排索引项主要包含如下信息：

- 文档id，用于获取原始信息。
- 词条频率（TF，Term Frequency），记录该词条在文档中出现的次数，用于后续相关性算分。
- 位置（Position），记录词条在文档中的分词位置（多个），用于做短语搜索（Phrase Query）。
- 偏移（Offset），记录词条在文档的开始和结束位置，用于做高亮显示。

以`搜索引擎`为例：

| 文档id |            文档内容             |
| :----: | :-----------------------------: |
|   1    | elasticsearch是最流行的搜索引擎 |
|   2    |    Python是世界上最好的语言     |
|   3    |      搜索引擎是如何诞生的       |

上述文档的倒排索引列表是这样的：

| DocID | TF   | Position | Offset  |
| ----- | ---- | -------- | ------- |
| 1     | 1    | 2        | <18,22> |
| 3     | 1    | 0        | <0,4>   |

关于文档1，`DocID`是1无需多说，`TF`是1表示`搜索引擎`在文档内容中出现一次，`Position`指的是分词后的位置，首先要说文档内容会被分为`elasticsearch`、`最流行`、`搜索引擎`3部分，从0开始计算，`搜索引擎`的`Position`是2；`Offset`是`搜索引擎`这个字符在文档中的位置。

文档3中`搜索引擎`在文档中出现一次（TF:1），并且出现在文档的开始位置（Position：0），那么`Offset`的位置就是`<0,4>`无疑了。

再比如说，现在有两个文档， 每个文档包含如下内容：

```
Study every day, good good up to forever		# 文档1包含的内容
To forever, study every day, good good up	# 文档2包含的内容
```

为了创建倒排索引，我们首先要将每个文档拆分成独立的词（或称为词条或者tokens），然后创建一个包含所有不重复的词条的排序列表，然后列出每个词条出现在哪个文档：

| term    | doc_1 | doc_2 |
| :------ | :---- | :---- |
| Study   | √     | ×     |
| To      | ×     | √     |
| every   | √     | √     |
| forever | √     | √     |
| day     | √     | √     |
| study   | ×     | √     |
| good    | √     | √     |
| every   | √     | √     |
| to      | √     | ×     |
| up      | √     | √     |

现在，我们试图搜索`to forever`，只需要查看包含每个词条的文档：

| term    | doc_1 | doc_2 |
| :------ | :---- | :---- |
| to      | √     | ×     |
| forever | √     | √     |
| total   | 2     | 1     |

两个文档都匹配，但是第一个文档比第二个匹配程度更高。如果没有别的条件，现在，这两个包含关键字的文档都将返回。

再来看一个示例，比如我们通过博客标签来搜索博客文章。那么倒排索引列表就是这样的一个结构：

![image-20210131150800814](https://gitee.com/yxinmiracle/pic/raw/master/20210131150800.png)

如果要搜索含有`python`标签的文章，那相对于查找所有原始数据而言，查找倒排索引后的数据将会快的多。只需要查看标签这一栏，然后获取相关的文章ID即可。

### elasticsearch的索引和Lucene的索引对比

在elasticsearch中，`索引`这个词被频繁使用，这就是术语的使用。
并且elasticsearch将索引被分为多个分片，每份分片是一个Lucene的索引。所以一个elasticsearch索引是由多个Lucene索引组成的。别问为什么，谁让elasticsearch使用Lucene作为底层呢！