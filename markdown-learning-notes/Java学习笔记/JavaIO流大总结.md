### File类概述和构造方法

---

**文档描述**

```java
public class File extends Object implements Serializable, Comparable<File>
    文件和目录路径名的抽象表示。 
```

**构造方法**

```java
File​(File parent, String child) 从父抽象路径名和子路径名字符串创建新的 File实例。  
File​(String pathname) 通过将给定的路径名字符串转换为抽象路径名来创建新的 File实例。  
File​(String parent, String child) 从父路径名字符串和子路径名字符串创建新的 File实例。  

```

**实战练习**

```java
public static void main(String[] args) {
        File f1 = new File("D:\\file_test\\java.txt"); //最简单的File构造方法

        System.out.println(f1); // File类重写的toString的方法

        File f2 = new File("D:\\file_test","java.txt");

        System.out.println(f2);

        File f3 = new File("D:\\file_test");
        File f4 = new File(f3,"java.txt");
        System.out.println(f4);
    }
```

### File类创建文件功能

---

```java
public static void main(String[] args) throws IOException {
        // createNewFile 如果文件存在就返回false,如果文件不存在我们就创建并返回true
        File f1 = new File("D:\\file_test\\java.txt");
        System.out.println(f1.createNewFile());

        // mkdir 如果目录存在就返回false,如果文件不存在我们就创建并返回true
        File f2 = new File("D:\\file_test\\cyxdir");
        System.out.println(f2.mkdir());

        //创建多级目录
        File f3 = new File("D:\\file_test\\cyxdir2\\cyxdir3");
        System.out.println(f3.mkdirs());

        //注意mkdir就是创建目录，要根据我们的方法来判断
    }
```

### File类判断和获取功能

---

![image-20200612090533027](https://i.loli.net/2020/06/12/fhWtkp8YN1smubF.png)

```java
 public static void main(String[] args) {
        File f1 = new File("FileTest/java.txt");

        System.out.println(f1.isDirectory());
        System.out.println(f1.isFile());
        System.out.println(f1.exists()); //file是否存在

        System.out.println(f1.getAbsolutePath());
        System.out.println(f1.getPath());//本身封装的路径
        System.out.println(f1.getName());

        System.out.println("----------");

        File f2 = new File("D:\\file_test");

        String[] strArray = f2.list();
        for (String s:strArray){
            System.out.println(s);
        }

        System.out.println("-------");

        File[] fileArray = f2.listFiles();

        for (File file:fileArray){
            //System.out.println(file); // 对象会调用toString，输出绝对路径

//            System.out.println(file.getName());

            if (file.isFile()){
                System.out.println(file);
            }

        }


    }
```

### File类的删除功能

---

![image-20200612090659140](https://i.loli.net/2020/06/12/3XOMiCyE9KtI7QB.png)

```java
public static void main(String[] args) throws IOException {
        File f1 = new File("FileTest/java.txt");
        System.out.println(f1.createNewFile());

        //删除 java.txt
        System.out.println(f1.delete());

        File f2 = new File("FileTest/javaDIar");
        System.out.println(f2.mkdir());

        System.out.println(f2.delete());

        File f3 = new File("FileTest/JAVAdir");
        System.out.println(f3.mkdir());
        File f4 = new File("FileTest/JAVAdir/java.txt");
        System.out.println(f4.createNewFile());
    }
```

### 字节流

---

![image-20200612091239664](https://i.loli.net/2020/06/12/SxFIYsvG2zqkRTJ.png)

### 字节流写数据

---