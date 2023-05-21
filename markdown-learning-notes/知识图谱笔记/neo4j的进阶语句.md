[TOC]



## 2.查询某个标签的所有节点

```mysql
match (m:Movie) retrun m.title
```

结果：

```mysql
╒═════════════════════════════════╕
│"p.title"                        │
╞═════════════════════════════════╡
│"The Matrix"                     │
├─────────────────────────────────┤
│"The Matrix Reloaded"            │
├─────────────────────────────────┤
│"The Matrix Revolutions"         │
├─────────────────────────────────┤
│"The Devil's Advocate"           │
├─────────────────────────────────┤
│"A Few Good Men"                 │
├─────────────────────────────────┤
│"Top Gun"                        │
├─────────────────────────────────┤
│"Jerry Maguire"                  │
├─────────────────────────────────┤
│"Stand By Me"                    │
├─────────────────────────────────┤
│"As Good as It Gets"             │
├─────────────────────────────────┤
│"What Dreams May Come"           │
├─────────────────────────────────┤

```



## 3.匹配标签

符号--意为相关的，这个关系不带又类型和方向

```mysql
match (p:Person{name:'Keanu Reeves'}) -- (m:Movie) return m.title limit 10 //Person类中名字是Keanu Reeves并且与movie有联系的
```

结果：

```mysql
╒════════════════════════╕
│"m.title"               │
╞════════════════════════╡
│"The Devil's Advocate"  │
├────────────────────────┤
│"The Replacements"      │
├────────────────────────┤
│"The Matrix Reloaded"   │
├────────────────────────┤
│"Johnny Mnemonic"       │
├────────────────────────┤
│"The Matrix Revolutions"│
├────────────────────────┤
│"The Matrix"            │
├────────────────────────┤
│"Something's Gotta Give"│
└────────────────────────┘
```

### 查找关系

##### 1.外向关系

关系的方向通过-->或者<--来表示

```mysql
match (p:Person{name:"Keanu Reeves"}) --> (m:Movie) return m.title
```

结果：

```mysql
╒════════════════════════╕
│"m.title"               │
╞════════════════════════╡
│"The Devil's Advocate"  │
├────────────────────────┤
│"The Replacements"      │
├────────────────────────┤
│"The Matrix Reloaded"   │
├────────────────────────┤
│"Johnny Mnemonic"       │
├────────────────────────┤
│"The Matrix Revolutions"│
├────────────────────────┤
│"The Matrix"            │
├────────────────────────┤
│"Something's Gotta Give"│
└────────────────────────┘
```

##### 2.有向关系和变量

当需要过滤关系中的属性，或者返回关系的时候，变量就很有必要了。

```mysql
match (n:Person{name:"Keanu Reeves"}) - [r] ->(m:Movie)
return type(r)
```

结果：

```mysql
╒══════════╕
│"type(r)" │
╞══════════╡
│"ACTED_IN"│
├──────────┤
│"ACTED_IN"│
├──────────┤
│"ACTED_IN"│
├──────────┤
│"ACTED_IN"│
├──────────┤
│"ACTED_IN"│
├──────────┤
│"ACTED_IN"│
├──────────┤
│"ACTED_IN"│
└──────────┘
```

##### 3.匹配关联关系

```mysql
match (m:Movie{title:"The Matrix Reloaded"}) <- [:ACTED_IN] - (a:Person)
// 返回所有人，这个人与电影The Matrix Reloaded有关
return m,a
```

##### 4.匹配多种关系类型

![image-20201101001513467](https://i.loli.net/2020/11/01/kzb2SfidxIc4WAB.png)

当需要匹配多种关系中的一种时，可以通过竖线（|）将多个关系连在一起。

```mysql
match (m:Movie{title:"The Devil's Advocate"}) <- [:ACTED_IN|:DIRECTED] - (p:Person)
return m,p
```

结果：

```mysql
╒═════════════════╕
│"p.name"         │
╞═════════════════╡
│"Keanu Reeves"   │
├─────────────────┤
│"Taylor Hackford"│
├─────────────────┤
│"Charlize Theron"│
├─────────────────┤
│"Al Pacino"      │
└─────────────────┘
```

#### 关系的深度

##### 1.带有特殊字符的关系

某些时候数据库中可能会有非字母字符的类型，或者中间含有空格。可以使用反引号"`"将他们括起来

##### 2.多个关系

```mysql
match (charlie {name:'Charlie Sheen'}) - [:ACTION_ON] -> (movie) <- [:DIRECTED] - (director)
return movie.title,director.name
// 返回'Charlie Sheen'演过的电影和这个电影的导演
```















