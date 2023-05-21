Java 学习之路 --> 基础内容

1.接口里面的方法有且只能是抽象方法，但是抽象类的方法不一定需要抽象方法

2.抽象类是对事务的抽象，而接口是对行为的抽象

3.分析方式，从具体倒抽象，实现方式，从抽象到具体

4.System有两个常用的方法

```java
static long currentTimeMillis​() 返回当前时间（以毫秒为单位）。  
static void exit​(int status) 终止当前运行的Java虚拟机。  
```

#### 5.Integer包装类

```java
MIN_VALUE:
public static final int MIN_VALUE持有最小值的常数为 int可以为-2 31 。 

MAX_VALUE
public static final int MAX_VALUE
    
Integer​(int value) 已过时。 
很少使用这个构造函数。 静态工厂valueOf(int)通常是一个更好的选择，因为它可能产生明显更好的空间和时间性能。 
应该使用Integer.valueOf
    
    
Integer​(String s) 已过时。 
很少使用这个构造函数。 使用parseInt(String)将字符串转换为int原语，或使用valueOf(String)将字符串转换为Integer对象。 
应该使用Integer.parseInt
    
int intValue​() 返回 Integer的值作为 int 。 
```

#### 6.Class Arrays

```java
static String toString​(int[] a) 返回指定数组的内容的字符串表示形式。  
static void sort​(int[] a) 按照数字顺序排列指定的数组。  
    
// 其他类型的也可以
```

#### 7.Date 构造方法

```java
public class DateDemo01 {
    public static void main(String[] args) {
        //Date​() 分配一个 Date对象并对其进行初始化，以便它表示分配的时间，以最接近的毫秒为单位。
        Date t1 = new Date();
        System.out.println(t1);    //Tue Jun 09 09:43:38 CST 2020
                                    // 很明显重写了toString的方法

        
        //Date​(long date) 分配一个 Date对象，并将其初始化为表示从标准基准时间（称为“时代”）即1970年1月1日00:00:00 GMT起的指定毫秒数。  
        long date = 1000*60*60;
        Date t2 = new Date(date);
        System.out.println(t2); //Thu Jan 01 09:00:00 CST 1970
    }
}
```

#### 8.Date常用方法

```java
 public static void main(String[] args) {
       // long getTime​() 返回自1970年1月1日以来，由 Date对象表示的00:00:00 GMT的毫秒数。
       // void setTime​(long time) 将此 Date对象设置为1970年1月1日00:00:00 GMT后的 time毫秒的时间点。

        Date d = new Date();
        System.out.println(d.getTime());//1591667299256
        System.out.println(System.currentTimeMillis()); //1591667376693

        long time = 1000*60*60;
        d.setTime(time);
        System.out.println(d);
    }
```

#### 9.SimpleDateFormat与Calendar

日期和时间格式由*日期和时间模式*字符串指定。  在日期和时间模式字符串中，从`'A'`到`'Z'`以及从`'a'`到`'z'`引号的字母被解释为表示日期或时间字符串的组件的模式字母。  可以使用单引号（ `'` ）引用文本，以避免解释。  `"''"`代表单引号。 所有其他字符不被解释;  在格式化过程中，它们只是复制到输出字符串中，或者在解析过程中与输入字符串匹配。 

定义了以下图案字母（所有其他字符从`'A'`到`'Z'`和从`'a'`到`'z'`被保留）： 

![image-20200609095254891](https://i.loli.net/2020/06/09/qTxJz6FAdHfVXmR.png)

```java
    public static void main(String[] args) throws ParseException {
        Date d = new Date();
//        SimpleDateFormat sdf = new SimpleDateFormat();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH-mm-ss");
        String s = sdf.format(d);//20-6-9 上午9:55,2020-06-09 09-56-39
        System.out.println(s);

        String s2 = "2019-09-08 11:11:11";
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(sdf2.parse(s2)); //Sun Sep 08 11:11:11 CST 2019

        Calendar c = Calendar.getInstance();

        System.out.println(c.get(Calendar.YEAR)+c.get(Calendar.DATE));

        c.add(Calendar.DATE,-5);
        c.add(Calendar.MONTH,+10);

        System.out.println(c.get(Calendar.YEAR)+" "+c.get(Calendar.MONTH)+" "+c.get(Calendar.DATE));

        c.set(2018,1,5);
        System.out.println(c.get(Calendar.YEAR)+" "+c.get(Calendar.MONTH)+" "+c.get(Calendar.DATE));
/*        2029
        2021 3 4
        2018 1 5*/
    }
```

 ![image-20200609102724964](https://i.loli.net/2020/06/09/tORS1e5sixzwAdb.png)