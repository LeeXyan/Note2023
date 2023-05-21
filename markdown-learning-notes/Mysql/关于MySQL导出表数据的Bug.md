[TOC]

# 前言：

本文可以先阅读完再跟着做。



### Mysql查询出的数据导出为csv

---

最近遇到同学的一个需求，要求差寻一些数据然后导出给他，因为之前也有做过类似的事情，觉得可以一下就搞定，但是居然出现乱码又出现没有分列的情况，真实头疼，然后就决定写这篇博客来记录一下。

#### 配置文件的设置

---

需要在配置文件中加上一下这句话：

![image-20200927092305169](https://i.loli.net/2020/09/27/9cNIe7rp8YKMHQw.png)

以文本格式给出：

```
#导出文件需要
secure_file_priv="D:/"
```

#### 重启MySQL（以管理员模式打开命令行）

---

##### 关闭MySQL

```mysql
net stop mysql
```

##### 开启MySQL

```
net start mysql
```

然后就可以查询出你的数据进行导出了

#### 导出命令

---

```mysql
mysql>  select * from app01_studentdata where dormitory  = "xxxx" into outfile "D:/out1.csv";

Query OK, 593 rows affected (0.02 sec)

```

然后这里导出之后直接是乱码，真实让人脑袋疼，怎么解决呢？跟着一下的步骤

右击你导出的csv，点击编辑，然后点击以下的文件

![image-20200927092948636](https://i.loli.net/2020/09/27/TKFvf97ODjyWuH5.png)

然后点击另存为：

![image-20200927093047225](https://i.loli.net/2020/09/27/obrlt8evNBaRkL7.png)

选择改为ANSI

![image-20200927093109231](https://i.loli.net/2020/09/27/y3EQvUWfOCGHjaz.png)

保存 就可以解决乱码问题了！

### 数据没有分列的问题

---

解决了导出和乱码的问题，有发现了没有分列，这个好办了，只需要在导出的命令后面加上一句话就好了：

```mysql
select * from app01_studentdata where dormitory  = "xxxx" into outfile "D:/out2.csv" fields terminated by ',' lines terminated by '\n';
```

大功告成！