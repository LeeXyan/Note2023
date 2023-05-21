[TOC]

## 样式操作

---

### 样式类操作

#### 前端代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .c1 {
            background-color: purple;
            height: 200px;
            width: 200px;
        }

        .c2 {
            background-color: green;
        }
    </style>
</head>
<body>
<div class="c1"></div>
<script src="../jquery.js"></script>
</body>
</html>
```

#### jq代码：

```js
$(".c1").addClass("c2")  // 添加指定的CSS类名
	S.fn.init [div.c1.c2, prevObject: S.fn.init(1)]
$(".c1").removeClass("c2") // 移除指定的CSS类名
	S.fn.init [div.c1, prevObject: S.fn.init(1)]
$(".c1").toggleClass("c2") // 如果原本没有就添加指定的类名，如果有就删除指定的类名
	S.fn.init [div.c1.c2, prevObject: S.fn.init(1)]
$(".c1").hasClass("c1") // 查看是否有这个类名
	true
```

### css样式操作

---

```js
原生js
	标签.style.color = "red";
jquery
	$(".c1").css("background-color","red") // 添加css样式
	$(".c1").css({"background-color":"yellow","width":"300px"}) // 添加多个css样式
```

### 位置操作

---

#### 前端代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .c1 {
            background-color: purple;
            height: 100px;
            width: 100px;
            position: relative;
        }

        .c2 {
            background-color: green;
            width: 50px;
            height: 50px;
            position: absolute;
            top: 20px;
            right: 20px;
        }
    </style>
</head>
<body>

<div class="c1">
    <div class="c2">

    </div>
</div>
<script src="../jquery.js"></script>
</body>
</html>
```

#### jq代码

---

```js
$(".c1").offset() // 查看距离当前窗口的位置，可改变位置，查的是相对位置
$(".c1").offset({"top":"200","left":"200"})  // 改变值
$(".c2").position(); // 查的是绝对位置
	{top: 20, left: 30}
$(".c2").offset();
	{top: 28, left: 38}

$(window).scrollTop() // 滚轮向下移动的距离
$(window).scrollLeft()
```



### jQuery绑定点击事件：

---

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .c1 {
            background-color: red;
            height: 200px;
            width: 200px;
        }
    </style>
</head>
<body>

<div class="c1"></div>

<script src="../jquery.js"></script>

<script>
    // 绑定点击事件
    $(".c1").click(function () {
            $(this).css("background-color","yellow"); //this是dom对象，所以我们要用$包装一下，使得变得jQuery对象。
    })

</script>
</body>
</html>
```

### 事件结合位置操作 + 滚轮事件

---

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

用户名：<input type="text" id="username">
<div>
    选择性别 :
    <input type="radio" name="sex" value="1">男
    <input type="radio" name="sex" value="2">女
    <input type="radio" name="sex" value="3">二椅子

</div>

<div>
    选择爱好
    <input type="checkbox" name="hobby" value="1">喝酒
    <input type="checkbox" name="hobby" value="2">烫头
    <input type="checkbox" name="hobby" value="3">植发
    <input type="checkbox" name="hobby" value="4">泡脚
    <input type="checkbox" name="hobby" value="5">大保健

</div>

<div>
    城市：
    <select name="" id="city">
        <option value="1">湖北孝感</option>
        <option value="2">广东深圳</option>
        <option value="3">山东烟台</option>
        <option value="4">黑龙江哈尔滨</option>
    </select>
</div>

<div style="margin-top: 100px">
    城市：
    <select name="" id="citys" multiple>
        <option value="1">湖北孝感</option>
        <option value="2">广东深圳</option>
        <option value="3">山东烟台</option>
        <option value="4">黑龙江哈尔滨</option>
    </select>
</div>

<script src="../jquery.js"></script>
</body>
</html>
```

### 尺寸

---

#### 前端代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .c1 {
            width: 100px;
            height: 100px;
            border: 2px solid red;
            background-color: green;
            padding: 20px 30px;
        }
    </style>
</head>
<body>

<div class="c1">

</div>

<script src="../jquery.js"></script>
</body>
</html>

```

#### jq代码：

---

```js
$(".c1").height();  // content高度
	100
$(".c1").width();  // content 宽度
	100
$(".c1").innerHeight();  // content高度 + padding高度
	140
$(".c1").innerWidth();  // content宽度 + padding 宽度 
	160
$(".c1").outerWidth();  // content宽度 + padding宽度 + border宽度
	164
$(".c1").outerHeight();  // content高度 + padding高度 + border高度
	144
```

### 文本操作

---

#### 前端代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .c1 {
            width: 100px;
            height: 100px;
            border: 2px solid red;
            background-color: green;
            padding: 20px 30px;
        }
    </style>
</head>
<body>

<div class="c1">
    我本将心向明月，
    <span>
        奈何明月找秃驴
    </span>
</div>

<script src="../jquery.js"></script>
</body>
</html>
```

#### jq代码：

```js
$(".c1").text() // 取得所有匹配元素的内容，只有文本内容，没有标签
    "
        我本将心向明月，

            奈何明月找秃驴

    "
$(".c1").html()  // 取得第一个匹配元素的html内容，包含标签
    "
        我本将心向明月，
        <span>
            奈何明月找秃驴
        </span>
    "
$(".c1").text("<h3>你好cyx</h3>")  // 设置所有匹配元素的内容，不识别标签，将标签作文文本插图进去
	S.fn.init [div.c1, prevObject: S.fn.init(1)]
$(".c1").html("<h3>你好cyx</h3>")  // 设置所有匹配元素的html内容，识别标签，能够表现出标签的效果
	S.fn.init [div.c1, prevObject: S.fn.init(1)]
```

### 值操作

---

#### 前端代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

用户名：<input type="text" id="username">
<div>
    选择性别 :
    <input type="radio" name="sex" value="1">男
    <input type="radio" name="sex" value="2">女
    <input type="radio" name="sex" value="3">二椅子

</div>

<div>
    选择爱好
    <input type="checkbox" name="hobby" value="1">喝酒
    <input type="checkbox" name="hobby" value="2">烫头
    <input type="checkbox" name="hobby" value="3">植发
    <input type="checkbox" name="hobby" value="4">泡脚
    <input type="checkbox" name="hobby" value="5">大保健

</div>

<div>
    城市：
    <select name="" id="city">
        <option value="1">湖北孝感</option>
        <option value="2">广东深圳</option>
        <option value="3">山东烟台</option>
        <option value="4">黑龙江哈尔滨</option>
    </select>
</div>

<script src="../jquery.js"></script>
</body>
</html>
```

#### jq以及js：

---

##### 获取input里的值：

```js
$("#username").val(); // jq
"adsasdasd"

document.getElementById("username").value;  // js
"adsasdasd"
```

##### 选择框取值：

```html
<input type="radio">
```

###### 代码：

```
$(":radio:checked").val();
```

##### checkbox取值，代码

```js
//$(":checkbox:checked")

for (var i = 0;i<$(":checkbox:checked").length;i++){
    console.log($(":checkbox:checked").eq(i).val())

}

// 一下是输出
3
4
5
```

##### 单选select获取值代码

```js
$("#city").val();
	"1"
```

##### 多选select

```js
$("#citys").val()
(2) ["2", "3"]
```

### 设置值

---

#### input框设置值

```js
$("#username").val("miracle")
```

#### type="radio"设置值

```js
$(":radio").val(["1"])  // 其中1是value值
```

#### type="checkbox"设置值

```js
$("[name='hobby']").val(["1","2","3"])
```

#### type="select"单选设置值

```js
$("#city").val("2")
```

#### type="select"多选设置值

```js
$("#citys").val(["2","3","4"])
```

### 属性操作

---

```js
attr(attrName)  // 返回一个匹配元素的元素值
attr(attrName,attrValue) // 为所有匹配的元素设置一个属性值
attr({'k1':v1,"k2":v2})  // 添加多个属性
removeAttr()  // 从每一个匹配的元素中删除一个属性
-------------------------------------------------------------------------------
-------------------------------------------------------------------------------
$(".c1").attr("xx","oo");  // 设置单个塑性
$(".c1").attr({"xx1":"oo1","xx2":"oo2"});  // 设置多个属性
$(".c1").attr("xx1");// 返回一个匹配元素的元素值
	"oo1"
$(".c1").removeAttr("xx1"); // 删除属性

prop -- 针对的是checked\selected\disabled....

查看标签是否有select属性，也就是是否被选中
	attr $(":checked").attr("checked"); // check -- undefined
	prop $(":checked").attr("checked"); // true -- false


通过属性设置的方式来设置是否被选中
 	$(":radio").eq(2).prop("checked",true); //true和flase不用加引号
	$(":radio").eq(2).prop("checked",false);
```

### 文档处理

---

```js
添加到指定元素内部的后面
	$(A).append(B)// 把B追加到A
	$(A).appendTo(B)// 把A追加到B
	// 添加字符串照样能识别标签  *****
	$('#d1').append('<a href="http://www.jd.com">京东</a>');
	----------------------------------------------------------------------------------------
添加到指定元素内部的前面
	$(A).prepend(B)// 把B前置到A
	$(A).prependTo(B)// 把A前置到B
	示例
		$('a').prependTo($('div'));
	----------------------------------------------------------------------------------------
添加到指定元素外部的后面
	$(A).after(B)// 把B放到A的后面
	$(A).insertAfter(B)// 把A放到B的后面
	----------------------------------------------------------------------------------------
添加到指定元素外部的前面
	$(A).before(B)// 把B放到A的前面
	$(A).insertBefore(B)// 把A放到B的前面
	
	----------------------------------------------------------------------------------------
移除和清空元素
	remove()// 从DOM中删除所有匹配的元素。
	empty()// 删除匹配的元素集合中所有的子节点，包括文本被全部删除，但是匹配的元素还
	$('div').remove();
	$('div').empty();
	----------------------------------------------------------------------------------------
   替换
	replaceWith()
	replaceAll()
	示例:
		var a = document.createElement('a')
		a.href = 'http://www.baidu.com';
		a.innerText = 'xxx';
		
		$('span').replaceWith(a);
		$(a).replaceAll('span');
    ----------------------------------------------------------------------------------------
   clone()克隆
	<button class="btn">屠龙宝刀,点击就送!</button>	

    $('.btn').click(function () {
        // var a = $(this).clone(); //克隆标签
        var a = $(this).clone(true);  //连带事件一起克隆
        $(this).after(a);

    })
```