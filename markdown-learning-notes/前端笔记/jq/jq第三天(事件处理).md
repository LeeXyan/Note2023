[TOC]



# jQuery事件处理

---



## 点击事件

### 前端重要代码

```html
    <style>
        .c1 {
            background-color: #53a5cb;
            width: 200px;
            height: 200px;
        }

    </style>

<div class="c1" id="d1"></div>
```

### jq代码

```js
    $("#d1").click(function () {
        $(this).css("background-color", "red");
    });

```



---



## input框事件

### 前端重要代码

```html
用户名啊用户名： <input type="text" id="inp">
```

### jq代码

```js
    $("input[type='text']").focus(function () {  //光标在input框里面
        $("#d1").css("background-color", "blue");
    });

    $("input[type='text']").blur(function () {  // 光标会在input框外面
        $("#d1").css("background-color", "#53a5cb");
    });
```



---

## 改变事件(change)

### 前端重要代码

```html
<select name="" id="sel">
    <option value="1">cyx</option>
    <option value="2">cyx2</option>
    <option value="3">cyx3</option>
</select>
```

### jq代码

```js
$("#sel").change(function () {  // 监听转换事件
    var optionValue = $("#sel").val();
    console.log($("option[value=" + optionValue + "]").text());  // 打印转换的option标签的值
});
```





---

## 监听鼠标悬浮事件

### 前端重要代码：

```html
<style>
    .c1 {
        background-color: #53a5cb;
        width: 200px;
        height: 200px;
    }
</style>

<div class="c1" id="d1"></div>

```

### jq代码

---

```js
$("#d1").hover(  // 这里跟监听其他事件的写法不一样
    function () {  // 鼠标进入
        $(this).css({'background-color': 'red'});
    },
    function () { // 鼠标离开
        $(this).css({'background-color': 'yellow'});
    }
 );
```



----

## 监听键盘事件：

### jq代码

```js
    // 监听键盘事件
    $(window).keydown(function (e) {
        if (e.keyCode === 37) {
            $('.c1').css({'background-color': 'red'});
        } else if (e.keyCode === 39) {
            $('.c1').css({'background-color': 'green'});
        } else {
            console.log(e.keyCode);
            $('.c1').css({'background-color': 'black'});
        }
    })
```



---

## 疯狂监听input框事件：

### jq代码：

```js
    // 疯狂监听input事件
    $("#inp").on("input",function () {
        console.log($(this).val());
    })
```

![image-20200929103905435](https://i.loli.net/2020/09/29/DMfiBvNe5I7dkLO.png)

# 事件冒泡

---

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #d1{
            background-color: red;
            height: 200px;
        }
        #d2{
            background-color: green;
            height: 100px;
            width: 100px;
        }

    </style>

</head>
<body>

<div id="d1">
    <div id="d2"></div>

</div>


<script src="jquery.js"></script>
<script>
    $('#d1').click(function () {
        alert('父级标签');
    });
    $('#d2').click(function () {
        alert('子级标签');
    });
    

</script>

</body>
</html>
```

上面这个代码的问题就时当我们点击子级标签标签的时候他除了会出先警告子级标签而且还会出现父级标签的警告，但是我们原本的意图只想出现子级标签的警告，所以我们要进行下面的这个代码的改良：

```js
    $('#d1').click(function () {
        alert('父级标签');
    });
    $('#d2').click(function (e) {
        alert('子级标签');
        return false;
        // e.stopPropagation();
    });
```

这样就可以阻止后续事件的发生了

# 事件委托

---

事件委托是通过事件冒泡的原理，利用父标签去捕获子标签的事件，将未来添加进来的某些子标签自动绑定上事件。

```html
事件委托是通过事件冒泡的原理，利用父标签去捕获子标签的事件，将未来添加进来的某些子标签自动绑定上事件。

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="d1">
    <button class="c1">爱的魔力转圈圈</button>

</div>

<script src="jquery.js"></script>
<script>
    // $('.c1').on('click',function () {  // 这里有个问题就是新出现的button按钮是没有添加事件的
    //     alert('xxxxxx');
    //     var btn = document.createElement('button');
    //     $(btn).text('爱的魔力转圈圈');
    //     $(btn).addClass('c1');
    //     console.log(btn);
    //     //添加到div标签里面的后面
    //     $('#d1').append(btn);
    //
    // });

	//将'button' 选择器选中的标签的点击事件委托给了$('#d1');
    $('#d1').on('click','button',function () {  // 通过这样的方式就可以解决了
        alert('xxxxxx');
        var btn = document.createElement('button');
        $(btn).text('爱的魔力转圈圈');
        $(btn).addClass('c1');
        console.log(btn);
        console.log($(this)) //还是我们点击的那个button按钮
        //添加到div标签里面的后面
        $('#d1').append(btn);

    });

</script>
</body>
</html>
```

# 额外内容

---

页面载入和window.onload

```js
1. jquery文件要在使用jquery的代码之前引入

2. js代码最好都放到body标签下面或者里面的最下面来写

3.window.onload
	// window.onload = function () {
    //     $('.c1').click(function () {
    //         $(this).css({'background-color':'green'});
    //     })
    // }


4.页面载入,$(function (){alert('xx');}) -- $(document).ready(function(){});
	页面载入与window.onload的区别
　　　　1.window.onload()函数有覆盖现象，必须等待着图片资源加载完成之后才能调用
　　　　2.jQuery的这个入口函数没有函数覆盖现象，文档加载完成之后就可以调用（建议使用此函数）
　　　　
示例:
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="jquery.js"></script>
    <script>
        // 等待整个页面中的内容全部加载完成之后,触发window.onload对应的函数里面的内容
        // window.onload 有覆盖现象,会被后面的window.onload给重新赋值
        // window.onload = function () {
        //     $('.c1').click(function () {
        //         $(this).css({'background-color':'green'});
        //     })
        // }
        $(function () {  // 这个方式会好一点
            $('.c1').click(function () {
                $(this).css({'background-color':'green'});
            })
        });

    </script>
    <script src="xx.js"></script>


    <style>
        .c1{
            background-color: red;
            height: 200px;
            width: 200px;
        }
    </style>
</head>
<body>

<div class="c1"></div>

<img src="" alt="">


</body>

</html>
　　　　

```

