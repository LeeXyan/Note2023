[TOC]



### 两个装饰器

#### classmethod

```python
class Goods:
    __discount = 0.8
    def __init__(self):
        self.__price = 5
        self.price = self.__price * self.__discount

    @classmethod
    def change_discount(cls,new_discount):
        cls.__discount = new_discount

Goods.change_discount(0.6)
apple = Goods()
print(apple.price)

```

@classmethod 把一个对象绑定的方法修改成一个类方法

第一，在方法中仍然可以引用类中的静态变量

第二，可以不用实例化的对象，就直接用类名在外部调用这个方法

什么时候用**classmethod**

1. 定义了一个方法，默认传self，但是这个self没有被使用
2. 并且你在这个方法里用到了当前的类名，或者你准备使用这个类的内存空间中的名字的时候

一本书上的例子

```python
import time

class Date:
    def __init__(self,year,month,day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def today(cls):
        struct_t = time.localtime()
        date = cls(struct_t.tm_year,struct_t.tm_mon,struct_t.tm_mday)
        return date

date_1 = Date.today()
print(date_1.year)
```

@staticmethod

被装饰的方法称之为静态方法

```python
class User:
    pass

    @staticmethod # 本身是一个普通的函数，被挪到类的内部执行
    def login():
        print("login")
        # 在函数的内部既不会用到self变量，也不用到cls

User.login()
```

总结（能定义到类中的内容）

1. 静态变量             是个所有的对象共享的变量             由对象/类调用 但是不能重复复制
2. 绑定方法             是个自带self参数的函数                   由对象调用
3. 类方法                 是个自带cls参数的函数                    由对象/类调用
4. 静态方法             是个啥都不带的普通函数                 由对象/类调用
5. property属性       是个伪装成属性的方法                     由对象调用 但不加括号