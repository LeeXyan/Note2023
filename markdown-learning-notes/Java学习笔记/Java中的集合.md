[TOC]

### set集合

---

```
public interface Set<E> 是一个接口
extends Collection<E>  不包含重复元素的集合。 
```

他是继承与Collection，没有带索引的方法，不能用普通for循环遍历

**HashSet实现了他**

```java
public class HashSet<E> extends AbstractSet<E> implements Set<E>, Cloneable, Serializable
    该类实现Set接口，由哈希表（实际为HashMap实例）支持。 对集合的迭代顺序不作任何保证; 特别是，它不能保证订单在一段时间内保持不变。 这个类允许null元素。 
```

一个例子

```java
public static void main(String[] args) {
        Set<String> set = new HashSet<String>();

        set.add("Hello");
        set.add("Java");
        set.add("Hello");

        for (String s:set) {
            System.out.println(s);
        }
    }
```

---

### 哈希值

哈希值：是JDK根据对象的**地址**或者**字符串**或者**数值**算出来的int类型的数值

其中Object类中有个可以返回哈希值的方法

```java
int hashCode​() 返回对象的哈希码值。  
```

```
    public static void main(String[] args) {
        Student s = new Student("cyx",18);

        // 同一个对象多次调用Hashcode方法返回的哈希值的相同的
        System.out.println(s.hashCode());//460141958
        System.out.println(s.hashCode());

        System.out.println("----------");

        // 默认情况下 不用对象的哈希值是不相同的
        Student s2 = new Student("cyx",18);
        System.out.println(s2.hashCode()); // 1163157884

        System.out.println("hello".hashCode()); // 99162322
        System.out.println("java".hashCode()); // 3254818

        System.out.println("-------------");
        System.out.println("你好".hashCode()); // 652829
        System.out.println("我好".hashCode()); // 801164
    }
```

![image-20200609105625876](https://i.loli.net/2020/06/09/KbAqadzlNDmXUyZ.png)

---

### HashSet集合的概述和特点

```java
public class HashSet<E> extends AbstractSet<E> implements Set<E>, Cloneable, Serializable
    该类实现Set接口，由哈希表（实际为HashMap实例）支持。 对集合的迭代顺序不作任何保证; 特别是，它不能保证订单在一段时间内保持不变。 这个类允许null元素。 
```

**对集合的迭代顺序不作任何保证，并且不包含重复的元素**

HashSet保证集合的元素的唯一性是通过equal的方法和hashcode的方法结合进行验证。

hashcode的方法是用来验证元素的地址是否相同

equal如果地址相同则判断元素是否相同



我们实战一下，创建一个学生类

```java
public class Student {
    public String name;
    public int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```

然后我们再测试类中添加几个学生类再hashset中

```java
public static void main(String[] args) {
    HashSet<Student> hs = new HashSet<Student>();

    Student s1 = new Student("ccc",30);
    Student s2 = new Student("cc2",30);
    Student s3 = new Student("cc3",30);
    Student s4 = new Student("ccc",30);

    hs.add(s1);
    hs.add(s2);
    hs.add(s3);
    hs.add(s4);

    for(Student s : hs){
        System.out.println(s);
    }

}
```

这个时候输出的是

```
Student{name='cc3', age=30}
Student{name='cc2', age=30}
Student{name='ccc', age=30}
Student{name='ccc', age=30}
```

不对啊，明明是唯一的啊，但是为什么有两个重复的呢？ 原因是我们没有重写equal的方法和hashcode的方法，我们再学生类中重写一下就好了，在学生类中加入如下两个方法

```java
 @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age &&
                Objects.equals(name, student.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
```

再运行一下测试类

```
Student{name='ccc', age=30}
Student{name='cc3', age=30}
Student{name='cc2', age=30}
```

成功解决

---

### LinkedHashSet集合

```java
public class LinkedHashSet<E> extends HashSet<E> implements Set<E>, Cloneable, Serializable
    哈希表和链表实现的Set接口，具有可预测的迭代次序。 该实现与HashSet不同之处在于它保持双向链接列表的所有条目。 该链表定义了迭代排序，它是将元素插入集合（ 插入顺序 ） 的顺序 。 请注意，如果一个元件被重新插入到组插入顺序不受影响 。 
也是一样没用重复的元素
```

由哈希表保证元素唯一

具体测试类方式如下

```java
public static void main(String[] args) {
        LinkedHashSet<String> link = new LinkedHashSet<String>();

        link.add("Hello");
        link.add("Java");
        link.add("World");

        for(String s : link){
            System.out.println(s);
        }
    
// out put
Hello
Java
World
```

---

### TreeSet集合

```java
public class TreeSet<E> extends AbstractSet<E> implements NavigableSet<E> Cloneable, SerializableA NavigableSet实现基于TreeMap 。
```

可以看见并没没用实现set接口啊，那是怎么回事呢？我们进入NavigableSet接口看看

```java
public interface NavigableSet<E>extends SortedSet<E>
```

再进入SortedSet看看

```java
public interface SortedSet<E> extends Set<E>
```

可以知道TreeSet是间接继承了Set接口

TreeSet文档中的一些描述

```java
A NavigableSet实现基于TreeMap 。 的元件使用其有序natural ordering ，或由Comparator集合创建时提供，这取决于所使用的构造方法。
```

那么什么是natural ordering，我们进去看看，是一个接口

```java
public interface Comparable<T>
    该接口对实现它的每个类的对象强加一个整体排序。 这个排序被称为类的自然排序 ，类的compareTo方法被称为其自然比较方法 。 
```

如果我们要实现自然排序我们就要让这个类去实现这个接口

TreeSet的构造方法中可以指定比较器进行排序

![](https://i.loli.net/2020/06/09/mGhA2PzyCbst7fJ.png)

使用的方式

```java
public static void main(String[] args) {

        TreeSet<Integer> ts = new TreeSet<Integer>();

        ts.add(10); // 自动装箱了
        ts.add(20);
        ts.add(80);
        ts.add(70);

        ts.add(20);

        for(Integer i:ts){
            System.out.println(i);
        }
    }
```

实例运用

学生类，如果需要排序的话要实现接口Comparable

```java
public class Student implements Comparable<Student>{
    public String name;
    public int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
    
    @Override
    public int compareTo(Student student) {

        int num = this.age-student.age;
        int num2 = num==0?this.name.compareTo(student.name):num;
        return num2;
    }
}
```

测试类

```java
 public static void main(String[] args) {
        TreeSet<Student> ts = new TreeSet<Student>();

        Student s1 = new Student("林丹",36);
        Student s2 = new Student("李忠伟",67);
        Student s3 = new Student("大帅哥",38);

        Student s4 = new Student("小帅哥",38);

        ts.add(s1);
        ts.add(s2);
        ts.add(s3);
        ts.add(s4);

        for(Student i:ts){
            System.out.println(i);
        }
    }
    
//out put
Student{name='林丹', age=36}
Student{name='大帅哥', age=38}
Student{name='小帅哥', age=38}
Student{name='李忠伟', age=67}
```

也可以使用匿名内部类的方式

![image-20200609133955857](https://i.loli.net/2020/06/09/OgyzXC2xeYBcW6H.png)

![image-20200609141043491](https://i.loli.net/2020/06/09/9EbaXznMv24ltoG.png)



### 可变参数

---

![image-20200609161644896](https://i.loli.net/2020/06/09/xbaoTn9KhuOj2CE.png)











