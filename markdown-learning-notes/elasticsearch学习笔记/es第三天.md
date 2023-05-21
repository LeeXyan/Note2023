



# 💮目录

[TOC]

## 最左前缀查询（match_phrase_prefix）

当我们在浏览器查询`beautiful`这个单词的时候，我们会出现不会拼的情况，我们就希望但我们输入前几个字母的时候收索引擎就会自动给我们显示出`beautiful`这个单词，

![image-20210204094211889](https://gitee.com/yxinmiracle/pic/raw/master/20210204094211.png)

比如百度就是这么做的，那么，在es中应该怎么做呢？

数据准备：

```tiki wiki
PUT d1/doc/1
{
  "title":"beautiful girl"
}

PUT d1/doc/2
{
  "title":"baeu"
}

PUT d1/doc/3
{
  "title":"beautiful"
}
```

查询：

```tiki wiki
GET d1/doc/_search
{
  "query": {
    "match_phrase_prefix": {
      "title": "beau"
    }
  }
}
```

结果：

![image-20210204094530523](https://gitee.com/yxinmiracle/pic/raw/master/20210204094530.png)



## multi_match查询

数据准备：

```tiki wiki
# 查询title和content中都有关键字python的
PUT d2/doc/1
{
  "title":"python是世界上最好的语言",
  "content":"是的,你说的没错，python就是这样的"
}
```

需求：查询title和content中都有关键字python的的数据

查询：

```tiki wiki
GET d2/doc/_search
{
  "query": {
    "multi_match": {
      "query": "python",
      "fields": ["title","content"]
    }
  }
}
```

结果：

![image-20210204094718603](https://gitee.com/yxinmiracle/pic/raw/master/20210204094718.png)

## term与match的区别

### 前言

term查询查找包含文档精确的倒排索引的词条。也就是精确查找

### 区别

1. `match`是经过`analyer`的，也就是说，文档首先被分析器给处理了。根据不同的分析器，分析的结果也稍显不同，然后再根据分词结果进行匹配
2. `term`则不经过分词，他是直接去倒排索引中查找了精确的值了

数据准备

```tiki wiki
PUT w10
{
  "mappings": {
    "doc":{
      "properties":{
        "t1":{
          "type":"text"
        },
        "t2":{
          "type":"keyword"
        }
      }
    }
  }
}

PUT w10/doc/1
{
  "t1":"hi single dog",
  "t2":"hi single dog"
}

```

我们将t2的索引类型置为keyword，这样这个索引在存储的过程中就不会被分词。

使用match来查询t1：

```tiki wiki
GET w10/doc/_search
{
  "query": {
    "match": {
      "t1": "hi"
    }
  }
}
```

![image-20210204100751308](https://gitee.com/yxinmiracle/pic/raw/master/20210204100751.png)

可见是有数据的

使用match来查询t2：

```
GET w10/doc/_search
{
  "query": {
    "match": {
      "t2": "hi"
    }
  }
}
```

![image-20210204100818485](https://gitee.com/yxinmiracle/pic/raw/master/20210204100818.png)

查询出来的数据为空

用term来查询t2，只查询hi：

```
GET w10/doc/_search
{
  "query": {
    "term": {
      "t2": {
        "value": "hi"
      }
    }
  }
}
```

可以发现结果是查询不出东西的：

![image-20210204103423676](https://gitee.com/yxinmiracle/pic/raw/master/20210204103423.png)

用term来查询t2，查询hi single dog：

```
GET w10/doc/_search
{
  "query": {
    "term": {
      "t2": {
        "value": "hi single dog"
      }
    }
  }
}

```

![image-20210204103452361](https://gitee.com/yxinmiracle/pic/raw/master/20210204103452.png)

可以看见只有输入完整的词语es才能够查询出来

## 词组，词条建议器（phrase，term）

### 词条建议器

词条建议器就是当用户输入一句话或者一个词的时候，可能会有错误，比如我们的`elasticsearch`打成 了`elasticsercha`类似的情况，这个时候就会进行词条的建议来给用户纠错，数据准备：

```tiki wiki
PUT s4
{
  "mappings": {
    "doc":{
      "properties":{
        "title":{
          "type":"text",
          "analyzer":"standard"
        }
      }
    }
  }
}

PUT s4/doc/1
{
  "title":"Lucene is cool"
}

PUT s4/doc/2
{
  "title":"Elasticsearch builds on top of lucene"
}

PUT s4/doc/3
{
  "title":"Elasticsearch rocks"
}

PUT s4/doc/4
{
  "title":"elasticsearch is rock solid"
}
```

代码：

```tiki wiki
# 词条
GET s4/doc/_search
{
  "suggest": {
    "my_s": {
      "text": "lucen and elasticsercha rook",
      "term": {
        "field": "title"
      }
    }
  }
}
```

结果：

![image-20210205110221322](https://gitee.com/yxinmiracle/pic/raw/master/20210205110221.png)

可以看出词条建议词是将我们每一个词语进行分词之后再来纠错。

### 词组建议器

词组建议器是将我们一句话看成一个整体来进行纠错

数据依旧是跟上面的数据是一样的

代码：

```tiki wiki
# 词组
GET s4/doc/_search
{
  "suggest": {
    "my_s": {
      "text": "lucen and elasticsercha rook",
      "phrase": {
        "field": "title"
      }
    }
  }
}
```

结果：

![image-20210205110803487](https://gitee.com/yxinmiracle/pic/raw/master/20210205110803.png)

#### 词组建议器加高亮

我们的纠错结果还可以进行高亮的展示：

```tiki wiki
# 纠错词组 加高亮
GET s4/doc/_search
{
  "suggest": {
    "my_s": {
      "text": "lucen and elasticsercha rook",
      "phrase": {
        "field": "title",
        "highlight":{
          "pre_tag":"<b style='color:red;'>",
          "post_tag":"</b>"
        }
      }
    }
  }
}
```

结果：

![image-20210205111100110](https://gitee.com/yxinmiracle/pic/raw/master/20210205111100.png)

## 完成建议器

我们来看一下自动完成的建议器——是一个导航功能，提供自动完成、搜索功能，可以在用户输入时引导用户查看相关结果，从而提高搜索精度。但并不适用于拼接检查或者像`term`和`phrase`建议那样的功能。
如果说在2000年左右，自动完成还是很炫酷的功能，那么现在它是必备的了——任何没有自动完成功能的搜索引擎都是很古老的。用户期望一个良好的自动完成来帮助用户实现更快的（特别是移动端）以及更好的（比如输入`e`，搜索引擎就应该知道用户想要查找的是`elasticsearch`）搜索。
一个优秀的自动完成将降低搜索引擎的负载，特别是在用户有一些快速搜索可用时，也就是直接跳转到主流的搜索结果而无须执行完整的搜索。
除此之外，一个优秀的自动完成必须是和快速的、相关的：

- 快速是因为它必须在用户不断输入的时候产生建议。
- 相关则是用户并不希望建议一个没有搜索结果或者没有用处的结果。

那我们依靠之前学过的`match_phrase_prefix`最左前缀查询来完成该功能，但是这样的查询可能不够快，因为理想的情况下，搜索引擎需要在用户输入下一个字符前返回建议结果。
完成建议器和后面的上下文建议器可以帮助用户构建更快的自动完成，它们是基于Lucene的suggest建议模块而构建的，将数据保存在内存中的有限状态转移机中（FST）。FST实际上是一种图。它可以将词条以压缩和易于检索的方式来存储。

![image-20210205113247222](https://gitee.com/yxinmiracle/pic/raw/master/20210205113247.png)

上图展示了词条`index、search、suggest`是如何存储的。当然实际中的实现更加复杂，比如它允许我们添加权重。
FST（[Finite StateTransducers](https://en.wikipedia.org/wiki/Finite-state_transducer)），通常中文译作有限状态传感器，FST目前在语音识别和自然语言搜索、处理等方向被广泛应用。
FST的功能更类似于字典，Lucene4.0在查找Term时使用了FST算法，用来快速定位Term的位置。FST的数据结构可以理解成（`key:value`）的形式。
在同义词过滤器（[SynonymFilter](http://lucene.apache.org/core/3_6_0/api/all/org/apache/lucene/analysis/synonym/SynonymFilter.html)）的实现中甚至可以用[HashMap](https://baike.baidu.com/item/hashmap)代替，不过相比较于HashMap，它的优点是：

- 以O(1)的时间复杂度找到key对应的value。
- 以字节的方式来存储所有的Term，重复利用Term Index的前缀和后缀，使Term - Index小到可以放进内存，减少存储空间，不过相对的也会占用更多的cpu资源。
- FST还可以用来快速确定term是否在系统中。

### 完成建议器：completion suggester

为了告诉elasticsearch我们准备将建议存储在自动完成的FST中，需要在映射中定义一个字段并将其`type`类型设置为`completion`：

```
# 完成建议器
PUT s5
{
  "mappings": {
    "doc":{
      "properties":{
        "title":{
          "type":"completion",
          "analyzer":"standard"
        }
      }
    }
  }
}
```

数据准备：

```
PUT s5/doc/1
{
  "title":"Lucene is cool"
}

PUT s5/doc/2
{
  "title":"Elasticsearch builds on top of lucene"
}

PUT s5/doc/3
{
  "title":"Elasticsearch rocks"
}

PUT s5/doc/4
{
  "title":"elasticsearch is rock solid"
}

# my_s建议，my_term纠错
GET s5/doc/_search
{
  "suggest": {
    "text": "lucen",
    "my_s": {
      "completion":{
        "field":"title"
      }
    },
    "my_term":{
      "term":{
        "field":"title"
      }
    }
  }
}
```

结果：

![image-20210205113515914](https://gitee.com/yxinmiracle/pic/raw/master/20210205113516.png)