

# 💮目录

[TOC]

## 进阶查询

### sort排序

代码：

```tiki wiki
GET a1/doc/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ]
}
```

`desc`是降序排序，`asc`是升序

结果如下：
![image-20210131212249663](https://gitee.com/yxinmiracle/pic/raw/master/20210131212249.png)

**注意，并不是所有的数据类型都能排序**！！！！！！！**

### 分页查询

随着数据量的不端增大，查询结果也展示的越来越长，很多时候我们仅仅只是查询几条数据，不用全部显示出来。这个时候就要用到分页查询了。

#### 只要指定的数据量

这个代码的意思是从第0个开始给2个

```tiki wiki
GET a1/doc/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 2
}
```

### 布尔查询

查询city为河北的

```tiki wiki
# 查询city是河北的
GET a1/doc/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "city": "河北"
          }
        }
      ]
    }
  }
}
```

结果：![image-20210131220621232](https://gitee.com/yxinmiracle/pic/raw/master/20210131220621.png)

#### must关键字--and

查询city是河北的and age为25

```tiki wiki
# 查询city是河北的and age为25
GET a1/doc/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "city": "河北"
          }
        },
        {
          "match": {
            "age": 18
          }
        }
      ]
    }
  }
}
```

结果：

![image-20210131220747733](https://gitee.com/yxinmiracle/pic/raw/master/20210131220747.png)

#### should关键字--or

查询city是河北的 ，或者age为25

```tiki wiki
# 查询city是河北的 ，或者age为25
GET a1/doc/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "city": "河北"
          }
        },
        {
          "match": {
            "age": 18
          }
        }
      ]
    }
  }
}
```

结果：

![image-20210131220914579](https://gitee.com/yxinmiracle/pic/raw/master/20210131220914.png)

#### must_not关键字--not

查询city不是河北，age也不是18的

```tiki wiki
GET a1/doc/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "city": "河北"
          }
        },
        {
          "match": {
            "age": 18
          }
        }
      ]
    }
  }
}
```

结果：

![image-20210131221012169](https://gitee.com/yxinmiracle/pic/raw/master/20210131221012.png)

#### filter关键字--大于小于

查询city是河北和年龄大于18

```tiki wiki
# 查询city是河北， 年龄大于18
GET a1/doc/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "city": "河北"
          }
        }
      ],
      "filter": {
        "range": {
          "age": {
            "gt": 18 
          }
        }
      }
    }
  }
}
```

结果：

![image-20210131221124859](https://gitee.com/yxinmiracle/pic/raw/master/20210131221124.png)

查询city是河北和年龄大于等于18

```tiki wiki
# 查询city是河北，年龄大于等于18
GET a1/doc/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "city": "河北"
          }
        }
      ],
      "filter": {
        "range": {
          "age": {
            "gte": 18 
          }
        }
      }
    }
  }
}
```

结果：

![image-20210131221243248](https://gitee.com/yxinmiracle/pic/raw/master/20210131221243.png)

小结：

- `must`：与关系，相当于关系型数据库中的`and`。
- `should`：或关系，相当于关系型数据库中的`or`。
- `must_not`：非关系，相当于关系型数据库中的`not`。
- `filter`：过滤条件。
- `range`：条件筛选范围。
- `gt`：大于，相当于关系型数据库中的`>`。
- `gte`：大于等于，相当于关系型数据库中的`>=`。
- `lt`：小于，相当于关系型数据库中的`<`。
- `lte`：小于等于，相当于关系型数据库中的`<=`。

### 减少输出字段，结果过滤

当我们在获取数据的时候，不太希望所有的数据都展示出来就像`select name from user`的sql语句一样，可以单独将`name`字段提取出来，这样个操作的代码是加上`_source`字段进行筛选。

```tiki wiki
# 查询city是河北，年龄大于等于18, 查询出来得数据只要city
GET a1/doc/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "city": "河北"
          }
        }
      ],
      "filter": {
        "range": {
          "age": {
            "gte": 18 
          }
        }
      }
    }
  },
  "_source": ["city"]
}
```

结果：

![image-20210201094132711](https://gitee.com/yxinmiracle/pic/raw/master/20210201094210.png)



### 高亮查询

当我们想在搜索关键字的时候加高亮显示，我们就可以使用高亮查询，假设我们这里想在描述中查询带有`丽质`词语的数据，并且加上高亮

```tiki wiki
GET a1/doc/_search
{
  "query": {
    "match": {
      "desc": "丽质"
    }
  },
  "_source": ["desc"],
  "highlight": {
    "fields": {
      "desc":{}
    }
  }
}
```

结果：

![image-20210201095036313](https://gitee.com/yxinmiracle/pic/raw/master/20210201095036.png)

都给套上了`<em></em>`标签

#### 自己加样式

要是想自己设定样式可以这么做：

```tiki wiki
GET a1/doc/_search
{
  "query": {
    "match": {
      "desc": "丽质"
    }
  },
  "_source": ["desc"],
  "highlight": {
    "pre_tags": "<b style='color :grenn'>", 
    "post_tags": "</b>", 
    "fields": {
      "desc":{}
    }
  }
}


```

结果是：
![image-20210201095436740](https://gitee.com/yxinmiracle/pic/raw/master/20210201095436.png)

### 聚合函数

最大最小求平均就可以使用聚合函数

#### 求平均年龄（avg），其中的`my_avg`就相当与一个别名

```tiki wiki
# 求平均年龄
GET a1/doc/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "my_avg": {
      "avg": {
        "field": "age"
      }
    }
  }
}
```

#### 求最大的年龄值（max）：

```tiki wiki
GET a1/doc/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "NAME": {
      "max": {
        "field": "age"
      }
    }
  },
  "size": 0
}

```

![image-20210201100245988](https://gitee.com/yxinmiracle/pic/raw/master/20210201100246.png)

#### 求和（sum）：

求年龄的和

```tiki wiki
GET a1/doc/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "my_sum": {
      "sum": {
        "field": "age"
      }
    }
  },
  "size": 0
}
```

结果：

![image-20210201100530263](https://gitee.com/yxinmiracle/pic/raw/master/20210201100530.png)

### 分组查询（range）左包右不包

现在想要查询的所有人的年龄段，并且按照`15-20，20-25，25，30`分组，并且算出每组的平均年龄

```tiki wiki
GET a1/doc/_search
{
  "size": 0,
  "query": {
    "match_all": {}
  },
  "aggs": {
    "avg_group": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 15,
            "to": 25
          },
          {
            "from": 25,
            "to":30
          },
          {
            "from": 30,
            "to": 35
          }
        ]
      }
    }
  }
}
```

结果：

![image-20210201101201897](https://gitee.com/yxinmiracle/pic/raw/master/20210201101202.png)

## Mapping

我们已经自由奔放够了！

我们应该知道，在关系型数据库中，必须先定义表结构，才能插入数据，并且，表结构不会轻易改变。而我们呢，我们怎么玩elasticsearch的呢？

所以，接下来，我们研究一下`mappings`这个小老弟，到底是怎么回事！

#### 映射是什么？

其实，映射`mappings`没那么神秘！说白了，就相当于原来由elasticsearch自动帮我们定义表结构。现在，我们要自己来了，旨在创建索引的时候，有更多定制的内容，更加的贴合业务场景。OK，坐好了，开车！
`elasticsearch`中的映射用来定义一个文档及其包含的字段如何存储和索引的过程。例如，我们可以使用映射来定义：

- 哪些字符串应该被视为全文字段。
- 哪些字段包含数字、日期或者地理位置。
- 定义日期的格式。
- 自定义的规则，用来控制动态添加字段的的映射。

身为吃瓜群众的小老弟，不懂没关系，往下走！

#### 映射类型

每个索引都有一个映射类型（这话必须放在elasticsearch6.x版本后才能说，之前版本一个索引下有多个类型），它决定了文档将如何被索引。

映射类型有：

- 元字段（meta-fields）：元字段用于自定义如何处理文档关联的元数据，例如包括文档的`_index`、`_type`、`_id`和`_source`字段。
- 字段或属性（field or properties）：映射类型包含与文档相关的字段或者属性的列表。

还不懂，没关系，继续往下走！

#### 字段的数据类型

- 简单类型，如文本（`text`）、关键字（`keyword`）、日期（`date`）、整形（`long`）、双精度（`double`）、布尔（`boolean`）或`ip`。
- 可以是支持`JSON`的层次结构性质的类型，如对象或嵌套。
- 或者一种特殊类型，如`geo_point`、`geo_shape`或`completion`。

为了不同的目的，以不同的方式索引相同的字段通常是有用的。例如，字符串字段可以作为全文搜索的文本字段进行索引，也可以作为排序或聚合的关键字字段进行索引。或者，可以使用标准分析器、英语分析器和法语分析器索引字符串字段。

这就是多字段的目的。大多数数据类型通过fields参数支持多字段。

#### 映射约束

在索引中定义太多的字段有可能导致映射**爆炸**！因为这可能会导致内存不足以及难以恢复的情况，为此。我们可以手动或动态的创建字段映射的数量：

- index.mapping.total_fields.limit：索引中的最大字段数。字段和对象映射以及字段别名都计入此限制。默认值为1000。
- index.mapping.depth.limit：字段的最大深度，以内部对象的数量来衡量。例如，如果所有字段都在根对象级别定义，则深度为1.如果有一个子对象映射，则深度为2，等等。默认值为20。
- index.mapping.nested_fields.limit：索引中嵌套字段的最大数量，默认为50.索引1个包含100个嵌套字段的文档实际上索引101个文档，因为每个嵌套文档都被索引为单独的隐藏文档。

### mappings之dynamic的三种状态

#### 前言

一般的，`mapping`则又可以分为动态映射（dynamic mapping）和静态（显式）映射（explicit mapping）和精确（严格）映射（strict mappings），具体由`dynamic`属性控制。

#### 动态（"dynamic":true）

动态的就是默认的，也就是之前的操作都是可以的，我们在es中怎么去设定dynamic的三种形态呢？

```tiki wiki
# 动态
PUT a2
{
  "mappings": {
    "doc":{
      "dynamic":true,
      "properties":{
        "name":{
          "type":"text"
        },
        "age":{
          "type":"long"
        }
      }
    }
  }
}
```

通过这行代码我们可以看出当前索引的一些配置

```
GET a2/_mapping
```

结果：

```json
{
  "a2" : {
    "mappings" : {
      "doc" : {
        "dynamic" : "true",
        "properties" : {
          "age" : {
            "type" : "long"
          },
          "name" : {
            "type" : "text"
          }
        }
      }
    }
  }
}
```

可以清楚的看出来`"dynamic" : "true"`

之前的所有操作都是可以做的，这里就不做演示

#### 静态（"dynamic":false）

静态索引的建立如下：

```tiki wiki
PUT a3
{
  "mappings": {
    "doc":{
      "dynamic":false,
      "properties":{
        "name":{
          "type":"text"
        },
        "age":{
          "type":"long"
        }
      }
    }
  }
}
```

这个时候我们往这个a3索引来添加一些数据对比一下静态与动态的区别

```tiki wiki
PUT a3/doc/1
{
  "name":"ccyxy",
  "age":19
}

PUT a3/doc/2
{
  "name":"cyx",
  "age":19,
  "city":"河北"
}

PUT a3/doc/3
{
  "age":19,
  "city":"河北"
}
```

这里添加的数据都是成功的，我们现在来查询一下`name:cyx`的数据看看

```tiki wiki
GET a3/doc/_search
{
  "query": {
    "match": {
      "name": "cyx"
    }
  }
}
```

结果：

![image-20210201113753887](https://gitee.com/yxinmiracle/pic/raw/master/20210201113753.png)

是可以将数据查询出来的，并且还包含我们未设定的字段`city`，那如果我们使用`city`字段来进行查询呢？

我们来查询一下`city:河北`的数据

```tiki wiki
GET a3/doc/_search
{
  "query": {
    "match": {
      "city": "河北"
    }
  }
}
```

结果：

![image-20210201113935429](https://gitee.com/yxinmiracle/pic/raw/master/20210201113935.png)

可以看出结果是没有数据，

可以看到elasticsearch并没有为新增的`sex`建立映射关系。所以查询不到。
当elasticsearch察觉到有新增字段时，因为`dynamic:false`的关系，会忽略该字段，但是仍会存储该字段。
在有些情况下，`dynamic:false`依然不够，所以还需要更严谨的策略来进一步做限制。

#### 严格（"dynamic":"strict"）

创建代码：

```tiki wiki
PUT a4
{
  "mappings": {
    "doc":{
      "dynamic":"strict",
      "properties":{
        "name":{
          "type":"text"
        },
        "age":{
          "type":"long"
        }
      }
    }
  }
}

```

当我们加入创建字段中有的数据时：

```
PUT a4/doc/1
{
  "name":"ccyxy",
  "age":19
}
```

这个插入是成功的

当我们要加入未创建字段city时：

```tiki wiki
PUT a3/doc/2
{
  "name":"cyx",
  "age":19,
  "city":"河北"
}
```

结果：

![image-20210201114444647](https://gitee.com/yxinmiracle/pic/raw/master/20210201114444.png)

可以看出在严格模式下是不可以这么做的

错误提示，严格动态映射异常！说人话就是，当`dynamic:strict`的时候，elasticsearch如果遇到新字段，会抛出异常。
上述这种严谨的作风洒家称为——严格模式！

#### 小结

- 动态映射（dynamic：true）：动态添加新的字段（或缺省）。
- 静态映射（dynamic：false）：忽略新的字段。在原有的映射基础上，当有新的字段时，不会主动的添加新的映射关系，只作为查询结果出现在查询中。
- 严格模式（dynamic： strict）：如果遇到新的字段，就抛出异常。

一般静态映射用的较多。就像`HTML`的`img`标签一样，`src`为自带的属性，你可以在需要的时候添加`id`或者`class`属性。
当然，如果你非常非常了解你的数据，并且未来很长一段时间不会改变，`strict`不失为一个好选择。



### Mapping的其他设置

#### index

首先来创建一个`mappings`：

```tiki wiki
PUT m4
{
  "mappings": {
    "doc": {
      "dynamic": false,
      "properties": {
        "name": {
          "type": "text",
          "index": true
        },
        "age": {
          "type": "long",
          "index": false
        }
      }
    }
  }
}
```

可以看到，我们在创建索引的时候，为每个属性添加一个`index`参数。那会有什么效果呢？
先来添加一篇文档：

```
PUT m4/doc/1
{
  "name": "王莹",
  "age": 18
}
```

再来查询看效果：

```
GET m4/doc/_search
{
  "query": {
    "match": {
      "name": "王莹"
    }
  }
}

GET m4/doc/_search
{
  "query": {
    "match": {
      "age": 18
    }
  }
}
```

以`name`查询没问题，但是，以`age`作为查询条件就有问题了：

```json

  "error": {
    "root_cause": [
      {
        "type": "query_shard_exception",
        "reason": "failed to create query: {\n  \"match\" : {\n    \"age\" : {\n      \"query\" : 18,\n      \"operator\" : \"OR\",\n      \"prefix_length\" : 0,\n      \"max_expansions\" : 50,\n      \"fuzzy_transpositions\" : true,\n      \"lenient\" : false,\n      \"zero_terms_query\" : \"NONE\",\n      \"auto_generate_synonyms_phrase_query\" : true,\n      \"boost\" : 1.0\n    }\n  }\n}",
        "index_uuid": "GHBPeT5pRnSi3g6DkpIkow",
        "index": "m4"
      }
    ],
    "type": "search_phase_execution_exception",
    "reason": "all shards failed",
    "phase": "query",
    "grouped": true,
    "failed_shards": [
      {
        "shard": 0,
        "index": "m4",
        "node": "dhkqLLTsRemm7qEgRdpvTg",
        "reason": {
          "type": "query_shard_exception",
          "reason": "failed to create query: {\n  \"match\" : {\n    \"age\" : {\n      \"query\" : 18,\n      \"operator\" : \"OR\",\n      \"prefix_length\" : 0,\n      \"max_expansions\" : 50,\n      \"fuzzy_transpositions\" : true,\n      \"lenient\" : false,\n      \"zero_terms_query\" : \"NONE\",\n      \"auto_generate_synonyms_phrase_query\" : true,\n      \"boost\" : 1.0\n    }\n  }\n}",
          "index_uuid": "GHBPeT5pRnSi3g6DkpIkow",
          "index": "m4",
          "caused_by": {
            "type": "illegal_argument_exception",
            "reason": "Cannot search on field [age] since it is not indexed."
          }
        }
      }
    ]
  },
  "status": 400
}
```

返回的是报错结果，这其中就是`index`参数在起作用。

小结：`index`属性默认为`true`，如果该属性设置为`false`，那么，`elasticsearch`不会为该属性创建索引，也就是说无法当做主查询条件。

#### copy_to

现在，再来学习一个`copy_to`属性，该属性允许我们将多个字段的值复制到组字段中，然后将组字段作为单个字段进行查询。

```tiki wiki
PUT m5
{
  "mappings": {
    "doc": {
      "dynamic":false,
      "properties": {
        "first_name":{
          "type": "text",
          "copy_to": "full_name"
        },
        "last_name": {
          "type": "text",
          "copy_to": "full_name"
        },
        "full_name": {
          "type": "text"
        }
      }
    }
  }
}

PUT m5/doc/1
{
  "first_name":"tom",
  "last_name":"ben"
}
PUT m5/doc/2
{
  "first_name":"john",
  "last_name":"smith"
}

GET m5/doc/_search
{
  "query": {
    "match": {
      "first_name": "tom"
    }
  }
}

GET m5/doc/_search
{
  "query": {
    "match": {
      "full_name": "tom"
    }
  }
}
```

上例中，我们将`first_name`和`last_name`都复制到`full_name`中。并且使用`full_name`查询也返回了结果：

```tiki wiki
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "m5",
        "_type" : "doc",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "first_name" : "tom",
          "last_name" : "ben"
        }
      }
    ]
  }
}
```

返回结果表示查询成功。那么想要查询`tom`或者`smith`该怎么办？

```tiki wiki
GET m5/doc/_search
{
  "query": {
    "match": {
      "full_name": {
        "query": "tom smith",
        "operator": "or"
      }
    }
  }
}
```

结果，都是查询到的：

![image-20210201163422998](https://gitee.com/yxinmiracle/pic/raw/master/20210201163423.png)

要是将关系转为and

```tiki wiki
GET m5/doc/_search
{
  "query": {
    "match": {
      "full_name": {
        "query": "tom smith",
        "operator": "and"
      }
    }
  }
}
```

结果：

![image-20210201163514060](https://gitee.com/yxinmiracle/pic/raw/master/20210201163514.png)

`copy_to`还支持将相同的属性值复制给不同的字段。

```tiki wiki
PUT m6
{
  "mappings": {
    "doc": {
      "dynamic":false,
      "properties": {
        "first_name":{
          "type": "text",
          "copy_to": "full_name"
        },
        "last_name": {
          "type": "text",
          "copy_to": ["field1", "field2"]
        },
        "field1": {
          "type": "text"
        },
        "field2": {
          "type": "text"
        }
      }
    }
  }
}


PUT m6/doc/1
{
  "first_name":"tom",
  "last_name":"ben"
}
PUT m6/doc/2
{
  "first_name":"john",
  "last_name":"smith"
}
```

上例中，只需要将`copy_to`的字段以数组的形式封装即可。无论是通过`field1`还是`field2`都可以查询。
小结：

- `copy_to`复制的是属性值而不是属性
- `copy_to`如果要应用于聚合请将`filddata`设置为`true`
- 如果要将属性值复制给多个字段，请用数组，比如`copy_to:["field1", "field2"]`

### 对象属性

现在，有一个个人信息文档如下：

```tiki wiki
PUT m7/doc/1
{
  "name":"tom",
  "age":18,
  "info":{
    "addr":"北京",
    "tel":"10010"
  }
}
```

首先，这样嵌套多层的`mappings`该如何设计呢？

```tiki wiki
PUT m7
{
  "mappings": {
    "doc": {
      "dynamic": false,
      "properties": {
        "name": {
          "type": "text"
        },
        "age": {
          "type": "text"
        },
        "info": {
          "properties": {
            "addr": {
              "type": "text"
            },
            "tel": {
              "type" : "text"
            }
          }
        }
      }
    }
  }
}
```

现在如果要以`info`中的`tel`为条件怎么写查询语句呢？

```tiki
GET m7/doc/_search
{
  "query": {
    "match": {
      "info.tel": "10086"
    }
  }
}
```

上例中，`info`既是一个属性，也是一个对象，我们称为`info`这类字段为对象型字段。该对象内又包含`addr`和`tel`两个字段，如上例这种以嵌套内的字段为查询条件的话，查询语句可以以字段点子字段的方式来写即可。

## settings设置

### 设置主、复制分片

在创建一个索引的时候，我们可以在`settings`中指定分片信息：

```tiki wiki
PUT a9
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  }
}
```

`number_of_shards`主分片

`number_of_replicas`副主分片

## ignore_above

长度超过`ignore_above`设置的字符串将不会被索引或存储（个人认为会存储，但不会为该字段建立索引，也就是该字段不能被检索）。 对于字符串数组，`ignore_above`将分别应用于每个数组元素，并且不会索引或存储比`ignore_above`更长的字符串元素。

```tiki wiki
PUT w1
{
  "mappings": {
    "doc":{
      "properties":{
        "t1":{
          "type":"keyword",
          "ignore_above": 5
        },
        "t2":{
          "type":"keyword",
          "ignore_above": 10   
        }
      }
    }
  }
}
PUT w1/doc/1
{
  "t1":"elk",          
  "t2":"elasticsearch"  
}
GET w1/doc/_search   
{
  "query":{
    "term": {
      "t1": "elk"
    }
  }
}

GET w1/doc/_search  
{
  "query": {
    "term": {
      "t2": "elasticsearch"
    }
  }
}
```

其中`"t1": "elk"`是可以返回数据的，但是` "t2": "elasticsearch"`这个并不能返回数据，因为设置了`"ignore_above": 10   `，他已经超过了长度10

①，该字段将忽略任何超过10个字符的字符串。
②，此文档已成功建立索引，也就是说能被查询，并且有结果返回。
③，该字段将不会建立索引，也就是说，以该字段作为查询条件，将不会有结果返回。
④，有结果返回。
⑤，则将不会有结果返回，因为`t2`字段对应的值长度超过了`ignove_above`设置的值。

该参数对于防止Lucene的术语字节长度限制也很有用，限制长度是`32766`。
注意，该ignore_above设置可以利用现有的领域进行更新[PUT地图API](https://www.elastic.co/guide/en/elasticsearch/reference/7.0/indices-put-mapping.html)。
对于值`ignore_above`是字符数，但Lucene的字节数为单位。如果您使用带有许多非ASCII字符的UTF-8文本，您可能需要设置限制，`32766 / 4 = 8191`因为UTF-8字符最多可占用4个字节。
如果我们观察上述示例中，我们可以看到在设置映射类型时，字段的类型是`keyword`，也就是说`ignore_above`参数仅针对于`keyword`类型有用。