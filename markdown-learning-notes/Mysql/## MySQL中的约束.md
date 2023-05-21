## MySQL中的约束

- 约束某一个字段不能为空 not null
- 不能重复 unique
- 无符号 int unsigned
- 默认值是什么 default
- 自增 auto_increment 只对数字有效 自带非空约束 至少是unique后才能使用
- 主键 primary key
  - 主键为了保证表中的每一条数据的该字段都是表格中的唯一值。换言之，它是用来独一无二地确认一个表格中的每一行数据。
    主键可以包含一个字段或多个字段。当主键包含多个栏位时，称为组合键 (Composite [Key](https://www.baidu.com/s?wd=Key&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)),也可以叫联合主键。
    主键可以在建置新表格时设定 (运用 CREATE TABLE 语句)，或是以改变现有的表格架构方式设定 (运用 ALTER TABLE)。
    主键必须唯一，主键值非空；可以是单一字段，也可以是多字段组合。
- 外键
  - Foreign key(自己的字段) references 外表(外表字段)
  - 外表字段必须至少是唯一的

**注意：用了not null后没有插入值是会报错的**

```mysql
mysql> create table t11(id int unsigned not null,name char(18) not null);  
Query OK, 0 rows affected (0.04 sec)                                       
                                                                           
mysql> insert into t11(id) values(1);                                      
ERROR 1364 (HY000): Field 'name' doesn t have a default value

mysql> insert into t11 values(1,"cyx");                                    
Query OK, 1 row affected (0.01 sec)                                        
                                                                           
mysql> select * from t11;                                                  
+----+------+                                                              
| id | name |                                                              
+----+------+                                                              
|  1 | cyx  |                                                              
+----+------+                                                              
1 row in set (0.00 sec)                                                    
```

#### 设置默认值 default

```mysql
mysql> create table t12(id int unsigned not null,name char(18) not null,gender enum('m','f') not null default 'm');
Query OK, 0 rows affected (0.04 sec)

mysql> insert into t12(id,name) values(1,'cyx');
Query OK, 1 row affected (0.01 sec)

mysql> select * from t12;
+----+------+--------+
| id | name | gender |
+----+------+--------+
|  1 | cyx  | m      |
+----+------+--------+
1 row in set (0.00 sec)
```

#### 不能重复 unique

```mysql
mysql> create table t13(id1 int unique,id2 int);
Query OK, 0 rows affected (0.04 sec)

mysql> insert into t13 values(1,1);
Query OK, 1 row affected (0.01 sec)

mysql> insert into t13 values(1,2); # 重复之后报错了
ERROR 1062 (23000): Duplicate entry '1' for key 't13.id1'

mysql> insert into t13 values(2,1);
Query OK, 1 row affected (0.01 sec
                          
mysql> select * from t13;
+------+------+
| id1  | id2  |
+------+------+
|    1 |    1 |
|    2 |    1 |
+------+------+
2 rows in set (0.00 sec)                          
```

#### 联合唯一 就像是ip+端口 unique(字段一,字段二)

```mysql
mysql> create table t14(id int,server_name char(18),ip char(15),port char(5),unique(ip,port));
Query OK, 0 rows affected (0.04 sec)
mysql> insert into t14 values(1,'mysql','127.0.0.1','3306');
Query OK, 1 row affected (0.01 sec)

mysql> insert into t14 values(2,'ftp','127.0.0.1','3306'); # 重复了 就报错
ERROR 1062 (23000): Duplicate entry '127.0.0.1-3306' for key 't14.ip'
mysql> insert into t14 values(2,'ftp','127.0.0.1','3307');
Query OK, 1 row affected (0.01 sec)

mysql> select * from t14;
+------+-------------+-----------+------+
| id   | server_name | ip        | port |
+------+-------------+-----------+------+
|    1 | mysql       | 127.0.0.1 | 3306 |
|    2 | ftp         | 127.0.0.1 | 3307 |
+------+-------------+-----------+------+
2 rows in set (0.00 sec)
```

#### 非空+唯一约束

第一个被定义为非空+唯一的那一列会成为这张表的primary key（主键）

一张表只能有一个主键

```mysql
mysql> create table t15(id int not null unique,username char(18) not null unique);
Query OK, 0 rows affected (0.06 sec)

mysql> desc t15;
+----------+----------+------+-----+---------+-------+
| Field    | Type     | Null | Key | Default | Extra |
+----------+----------+------+-----+---------+-------+
| id       | int      | NO   | PRI | NULL    |       | # PRI就是主键的意思
| username | char(18) | NO   | UNI | NULL    |       |
+----------+----------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

#### 自增    



```mysql
mysql> create table t20(id int unique auto_increment,name char(30));
Query OK, 0 rows affected (0.04 sec)

mysql> desc t20;
+-------+----------+------+-----+---------+----------------+
| Field | Type     | Null | Key | Default | Extra          |
+-------+----------+------+-----+---------+----------------+
| id    | int      | NO   | PRI | NULL    | auto_increment |
| name  | char(30) | YES  |     | NULL    |                |
+-------+----------+------+-----+---------+----------------+
2 rows in set (0.00 sec)

mysql> insert into t20(name) values('cyx'); # 就不用管id了
Query OK, 1 row affected (0.01 sec)

mysql> insert into t20(name) values('cyx2');
Query OK, 1 row affected (0.01 sec)

mysql> select * from t20;
+----+------+
| id | name |
+----+------+
|  1 | cyx  |
|  2 | cyx2 |
+----+------+
2 rows in set (0.00 sec)
```

#### 外键

---

涉及到外键的操作是

```mysql
foreign key(class_id) references class(cid) on update cascade on delete cascade
```

```mysql
 on update cascade # 这是级联更新的意思
```

```mysql
 on delete cascade # 这是级联删除的意思
```

以下是示例：

```mysql
mysql> create table class(cid int primary key auto_increment,cname char(12) not null,startd date);
Query OK, 0 rows affected (0.04 sec)

mysql> create table stu(id int primary key auto_increment,name char(12) not null,gender enum('m','f') default 'm',class_id int,foreign key(class_id) references class(cid) on update cascade on delete cascade);
Query OK, 0 rows affected (0.06 sec)

mysql> insert into class values(1,'zhongshan',now());
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> insert into class values(2,'zhongshan2',20190506);
Query OK, 1 row affected (0.01 sec)

mysql> select * from class;
+-----+------------+------------+
| cid | cname      | startd     |
+-----+------------+------------+
|   1 | zhongshan  | 2020-06-01 |
|   2 | zhongshan2 | 2019-05-06 |
+-----+------------+------------+
2 rows in set (0.00 sec)

mysql> insert into stu values(1,'cyx','m',2),(2,'cyx2','m',2),(3,'cyx3','m',1);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> select * from stu;
+----+------+--------+----------+
| id | name | gender | class_id |
+----+------+--------+----------+
|  1 | cyx  | m      |        2 |
|  2 | cyx2 | m      |        2 |
|  3 | cyx3 | m      |        1 |
+----+------+--------+----------+
3 rows in set (0.00 sec)

mysql> update class set cid=3 where cid = 2;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from stu;
+----+------+--------+----------+
| id | name | gender | class_id |
+----+------+--------+----------+
|  1 | cyx  | m      |        3 |
|  2 | cyx2 | m      |        3 |
|  3 | cyx3 | m      |        1 |
+----+------+--------+----------+
3 rows in set (0.00 sec)
```

### 级联更新，级联删除

---

```mysql
create table stu(
sid int UNSIGNED primary key auto_increment,
name varchar(20) not null)
TYPE=InnoDB charset=utf8;

create table sc(
scid int UNSIGNED primary key auto_increment,
sid int UNSIGNED not null,
score varchar(20) default '0',
index (sid),   --外键必须加索引
FOREIGN KEY (sid) REFERENCES stu(sid) ON DELETE CASCADE ON UPDATE CASCADE)
TYPE=InnoDB charset=utf8;
```

