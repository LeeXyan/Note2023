[TOC]



### Python中面向对象一些用法

#### super用法

```python
class A(object):
    def func(self):
        print("A")

class B(A):
    def func(self):
        super().func()
        print("B")

class C(A):
    def func(self):
        super().func()
        print("C")

class D(B,C):
    def func(self):
        super().func()
        print("D")


print(D.mro())
D().func()


```

```python
# 输出
[<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
A
C
B
D
```

super是按照mro顺序来寻找当前类的下一个类

在py3中不需要传参数，自动就帮我们寻找当前类的mro循序的下一个类中的同名方法

在py2中的新式类中，需要我们主动传递参数super(子类的名字，子类的对象).函数名()

这样才能够帮我们调用到这个子类的mro顺序的下一个类中的方法

在py2的经典类中，并不支持使用super来找下一个类

在单继承的程序中，super就是找父类

这里举一个vip的示例：

```python
class User:
    def __init__(self,name):
        self.name = name

class VIPUser(User):
    def __init__(self,name,level,start_date,end_date):
        super().__init__(name)
        self.level = level
        self.start_date = start_date
        self.end_date = end_date

cyx = VIPUser("cyx",6,"2020-02-03","2021-05-03")
print(cyx.__dict__)
```

通过输出我们很明显的看到name是在cyx这个对象中的

```python
# 输出
{'name': 'cyx', 'level': 6, 'start_date': '2020-02-03', 'end_date': '2021-05-03'}
```

学完super方法，我们应该思考：

1. 在py3中怎么用？在py2中（新式类/经典类）中怎么用？
2. 在单继承中执行父类的同名方法的时候怎么用？
3. super方法和mro方法的关系是什么？

####  封装

封装：就是把属性或者方法装起来

广义：把属性和方法装起来，外面不能直接调用了，要通过类的名字来调用

```python
class A:
    a = 1
    
print(a)
```

狭义：把属性和方法藏起来，外面不能直接调用了，只能在内部偷偷使用

```python
class User:
    def __init__(self,name,password):
        self.name = name
        self.__pwd = password # 给一个名字的前面加上了双下划线的时候就变成了一属性，使用的实例变量

cyx = User("cyx",123)
print(cyx.pwd)

# 输出
AttributeError: 'User' object has no attribute 'pwd'
```

给一个名字前面加上了双下划线的时候，这个时候就变成了一个私有的

所有的私有的内容或者名字都不能在类的外部调用，只能在类的内部使用了

如果你只能得想看的话那么我们可以自己设置：

```python
class User:
    def __init__(self,name,password):
        self.name = name
        self.__pwd = password

    def get_pwd(self):
        return self.__pwd # 之所以设置为私有的是因为我不仅不想让别人看见，还不想让别人改

cyx = User("cyx",123)
print(cyx.get_pwd())

# 输出
123
```

之所以设置为私有的是因为我不仅不想让别人看见，还不想让别人改。

但是我们如果想改的话也是可以的：

```python
class User:
    def __init__(self,name,password):
        self.name = name
        self.__pwd = password

    def get_pwd(self):
        return self.__pwd

    def change_pwd(self,new_password):
        self.__pwd = new_password
        
cyx = User("cyx",123)
print(cyx.get_pwd())
cyx.change_pwd(456)
print(cyx.get_pwd())
```

使用私有的三种情况：

1. 不想让你看也不想让你改
2. 可以让你看 但不让你改
3. 可以看也可以改 但是要求你按照我的规则改



思考：

1. 加了双下划线的名字为啥不能从类的外部调用了？
2. 私有的内容能不能被子类使用呢？
3. 在其他语言中的数据的级别都有哪些？python的有哪些？

回答①：

```python
class User:
    __Country = "china" # 私有的静态变量

print(User.__dict__)

# 输出
{'__module__': '__main__', '_User__Country': 'china', '__dict__': <attribute '__dict__' of 'User' objects>, '__weakref__': <attribute '__weakref__' of 'User' objects>, '__doc__': None}

```

```
__Country -- > _User__Country
```

在类的内部使用的时候，自动的把当前这句话所在的名字拼在私有变量前完成变形

回答②：不能

```python
class Foo(object):
    def __init__(self):
        self.func()
    def func(self):
        print("in Foo")

class Son(Foo):
    def func(self):
        print("in son")
Son()

class Foo(object):
    def __init__(self):
        self.__func()
    def __func(self):
        print("in Foo")

class Son(Foo):
    def __func(self):
        print("in son")
Son()

# 输出：
in son
in Foo
```

回答③：

1. public 公有的 类内类外都能用，父类子类都能用                    python支持
2. protect 保护的 类内能用，父类子类都能用，类外不能用      python不支持
3. private 私有的 本类的类内部能用 其他地方不能用				  python支持

#### @property装饰器

把一个方法伪装成一个属性

```python
from math import pi

class Circle:
    def __init__(self,r):
        self.r = r

    @property # 把一个方法伪装成一个属性
    def area(self):
        return pi * self.r**2

c1 = Circle(5)
print(c1.area)
c1.r = 6
print(c1.area)

# 输出
78.53981633974483
113.09733552923255
```

提供一个示例：

```python
import time
class Person:
    def __init__(self,name,birth):
        self.name = name
        self.birth = birth

    @property
    def age(self):
        return time.localtime().tm_year - self.birth

cyx = Person("cyx",2000)
print(cyx.age)
```

property使用的第二个场景：和私有属性一起使用的

```python
class User:
    def __init__(self,usr,pwd):
        self.usr = usr
        self.__pwd = pwd
    
    @property
    def pwd(self):
        return self.__pwd

cyx = User("cyx",2000)
print(cyx.pwd)
```

#### property的进阶用法

```python
class Goods:
    discount = 0.8
    def __init__(self, name, origin_price):
        self.name = name
        self.__price = origin_price

    @property
    def price(self):
        return self.__price * self.discount

    @price.setter # 改变数值
    def price(self,new_value):
        if isinstance(new_value,int):
            self.__price = new_value

apple = Goods("apple",5)
print(apple.price)
apple.price = 10
print(apple.price)
```

#### 反射getattr()

![image-20200417181452942](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200417181452942.png)

这是调用自己文件中的，通过字符创来获取对象，或者方法。

用一段异步爬虫代码看看

```python
from lxml import etree
from urllib import parse
from models import Block,Hot
import re
import aiohttp
import asyncio
from concurrent.futures import ThreadPoolExecutor


threadpool=ThreadPoolExecutor(100)

class CrawlData:
    def __init__(self):
        # 获取各大热榜的主页
        self.urls = {
            'V3EX': 'https://www.v2ex.com/?tab=hot',
            'Github': 'https://github.com/trending',
            'WeiBo': 'https://s.weibo.com/top/summary',
            'ZhiHu': 'https://www.zhihu.com/hot',
            "Bilibili": "https://www.bilibili.com/ranking?spm_id_from=333.851.b_7072696d61727950616765546162.3",
            "BaiDu": "https://tophub.today/n/Jb0vmloB1G",
            "Badminton":"http://www.aiyuke.com/view/cate/index.htm",
        }
        # 将请求头加上，反爬
        self.headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36',
        }

        self.ZhiHuheaders = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36',
            'cookie': '_zap=1821c44a-4066-400e-aae6-875dcbab513a; d_c0="AEAXcdi2tBCPTi8JGJ-Y6SJ3Y15uQAjGm2g=|1579785434"; _ga=GA1.2.538754871.1582636841; _xsrf=biw59XkAA7UMcB76Y03zXdAPEXZHZHPJ; Hm_lvt_98beee57fd2ef70ccdd5ca52b9740c49=1585123638,1585531236,1585910003,1586157459; _gid=GA1.2.1368376126.1586157459; l_n_c=1; r_cap_id="MGU3ZWMxOWIyMGRmNDM0Y2JhOTQ4OTUwZWQxM2YxM2M=|1586157464|76ef605c5d97d0e1b0562b75540e9fc888b49885"; cap_id="OGJlNzdkYzA5YWNiNDE5OWFhNjJmNTFhMTkyMmJkZmM=|1586157464|afcf1532547df3ebacf7aa547a2e59697d54de50"; l_cap_id="NGE5NjFiZjc5MWM3NDk1N2JlZjRhOTA4ZDVhZDk0NmQ=|1586157464|4f92a78f7a6276a18b15136a8e999cd2ffaeb174"; n_c=1; atoken_expired_in=7200; capsion_ticket="2|1:0|10:1586157474|14:capsion_ticket|44:MWIzZDc0YTk4NzU3NGNmNWJlNmNjNWJjYTU0NzNkMzU=|1e026964a38276b3f75762bde75111b5a6ca8ab6ee3a6ca7f160f95c2cb9dbb4"; atoken=32_JZhSGC6cM27WBjf4xCjeHjqmAHt3sgHqy7KZb6Zwj8eMNp1sr1MDAmSYcExH3zmfHJQrKgPUjMtWZ92_wEVC9RyghqalI4lXw3wMa_IMQUc; client_id="bzNwMi1qZ0x6UnQydFlDWW04SWY5a2NhQUc3SQ==|1586157512|9dc7cb3a79b36dca963a8eb0db6cef321c6282c6"; z_c0="2|1:0|10:1586157551|4:z_c0|92:Mi4xSksxakdnQUFBQUFBUUJkeDJMYTBFQ1lBQUFCZ0FsVk43aVY0WHdBNUxrTm9LeUljLUF2NUU0MjBtT0pzWTQxUlJB|50db53f7842a52c80a749fe5b65bb3355ce07f39f293ecd9751dbe50190e7d4b"; tshl=; tst=h; Hm_lpvt_98beee57fd2ef70ccdd5ca52b9740c49=1586157556; SESSIONID=5fftJW8DqEkKR4uFj3WHQop32N5BJcezrHItC6h1It3; KLBRSID=0a401b23e8a71b70de2f4b37f5b4e379|1586157559|1586157456; JOID=UVsdBUmqRBy3s3JYFq91gxcr3UoMnyl0x_gla1zaAXzy5UctW1aWnOyyeV0cBjM2nYdhVLmyO_dqVUunnelr6Uw=; osd=Vl8RB06tQBC1tHVcGq1yhBMn300LmyV2wP8hZ17dBnj-50AqX1qUm-u2dV8bATc6n4BmULWwPPBuWUmgmu1n60s=',
        }

        self.BaiDuheaders = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36',
            "cookie": "M_distinctid=1714eb13b8f1c1-005e7e1e45cadc-6701b35-1fa400-1714eb13b90d8; CNZZDATA1276310587=1825773749-1586163124-https%253A%252F%252Fwww.baidu.com%252F%7C1586163124; Hm_lvt_3b1e939f6e789219d8629de8a519eab9=1586163171,1586165245; Hm_lpvt_3b1e939f6e789219d8629de8a519eab9=1586165245",
        }

    async def getConn(self, name):
        url = self.urls[name]

        # 由于知乎必须要有cookie，所以如果代码用不了可以更新cookie
        if name == "ZhiHu":
            self.headers = self.ZhiHuheaders

        if name == "BaiDu":
            self.headers = self.BaiDuheaders

        async with aiohttp.ClientSession(headers=self.headers) as session:
            async with session.get(url) as response:
                if response.status == 200:
                    if name == "Bilibili":
                        html = await response.text()
                        return html
                    else:
                        soup = etree.HTML(await response.text())
                        return soup
                else:
                    print(f"不能获取{name}的数据")
                    return None

    async def GetWeiBo(self):
        soup = await self.getConn("WeiBo")
        items = soup.xpath('//div[@class="data"]/table/tbody/tr')
        for item in items:
            title = item.xpath('td[2]/a/text()')[0]
            url = parse.urljoin('https://s.weibo.com', item.xpath('td[2]/a/@href')[0])

            threadpool.submit(Hot.addHot, title=str(title), url=str(url), block='WeiBo', content=' ')


    async def GetZhiHu(self):
        soup = await self.getConn("ZhiHu")
        items = soup.xpath('//div[@class="HotList-list"]/section')
        for item in items:
            title = item.xpath('div[2]/a/h2/text()')[0]
            content = item.xpath('div[2]/a/p/text()')
            url = item.xpath('div[2]/a/@href')[0]
            if content:
                content = content[0].strip()
            else:
                content = ''

            threadpool.submit(Hot.addHot, title=str(title), url=str(url), block='ZhiHu', content=content)

            #print(title, url, content)


    async def GetGithub(self):
        soup = await self.getConn("Github")
        items = soup.xpath('//article[@class="Box-row"]')
        for item in items:
            title = item.xpath('h1/a/@href')[0].strip().replace('/', '')
            url = item.xpath('h1/a/@href')[0].strip()
            try:
                content = item.xpath('p[contains(@class,"col-9")]/text()')[0].strip()
            except:
                content = ''
            if title:
                url = parse.urljoin('https://github.com/', url)
                #print(title, content, url)

            threadpool.submit(Hot.addHot, title=str(title), url=str(url), block='Github', content=content)


    async def GetBilibili(self):
        html = await self.getConn('Bilibili')
        dataList = re.findall(
            r'<div class="content"><div class="img"><a href="(.*?)" target="_blank"><div class="lazy-img cover"><img alt="(.*?)" ',
            html)
        for data in dataList:
            name = data[1]
            url = data[0]

            threadpool.submit(Hot.addHot, title=str(name), url=str(url), block='Bilibili', content=' ')

            print(name, url)

    async def GetBaiDu(self):
        soup = await self.getConn("BaiDu")
        bodys = soup.xpath('//table[@class="table"]/tbody/tr')[:50]
        for body in bodys:
            title = body.xpath('td[@class="al"]/a/text()')[0]
            url = body.xpath('td[@class="al"]/a/@href')[0]

            threadpool.submit(Hot.addHot, title=str(title), url=str(url), block='BaiDu', content=' ')

            print(title, url)

    async def GetV3EX(self):
        soup = await self.getConn('V3EX')
        if soup:
            items = soup.xpath('//div[@id="Main"]/div[@class="box"]/div[contains(@class,"cell")]')[1:]
            for item in items:
                title = item.xpath('table//td[3]/span/a/text()')
                url = item.xpath('table//td[3]/span/a/@href')
                if title and url:
                    url = parse.urljoin('https://www.v2ex.com/', url[1])

                    threadpool.submit(Hot.addHot, title=str(title), url=str(url), block='V3EX', content=' ')
                    # Hot.addHot(title=str(title[0]), url=str(url), block='V3EX', content='')

        else:
            print('GetV3EX Error Done')

    async def GetBadminton(self):
        soup = await self.getConn("Badminton")
        news_list = soup.xpath('//div[@class="news_list_main"]/div')[1:-1]
        for new in news_list:
            title = new.xpath('div[@class="desc"]/h1/a/text()')[0]
            source = "来源于：" + new.xpath('div[@class="desc"]/p/a/text()')[0] + "，"
            times = "发表于：" + ("".join(new.xpath('div[@class="desc"]/p/text()[2]'))).split("·")[1].strip()
            content = source + times
            href = new.xpath('div[@class="desc"]/h1/a/@href')[0]
            threadpool.submit(Hot.addHot, title=str(title), url=str(href), block='Badminton', content=content)



def ExceGetData(spider, value):
    # 获取属性对象，将它加入到tasks的任务中
    dataType = getattr(spider, "Get" + value)
    return dataType


def main():
    allData = [
        'Github',
        'WeiBo',
        'ZhiHu',
        "Bilibili",
        "BaiDu",
        "V3EX",
        "Badminton"
    ]

    spider = CrawlData()

    tasks = []
    loop = asyncio.get_event_loop()

    for value in allData:
        print(f"开始爬{value}")
        Block.addBlock(value)
        func = ExceGetData(spider, value)
        tasks.append(func())

    loop.run_until_complete(asyncio.wait(tasks))

if __name__ == '__main__':
    main()
```