# JavaScript

---

## 1、JavaScript概述

### 1-1.定义

​	一种脚本语言

- 理解

  用于网页交互。

### 1-2.ECMAScript

​	它是JavaScript的一个==标准==。

​	一般简写es。如es6、es5等。

## 2、快速开始

### 2-1.引入

- 内部引入

  可以放在<head>里，也可以放在<body>里

  ```javascript
  <script>
  	alert("hello world!");
  </script>
  ```

- 外部引入

  自己写一个js文件，然后引入。

  ```javascript
  <script src="js/ld.js"></script>
  // 注：不可以用<script src="" />这样的形式写。
  
  <script type="text/javascript"></script>
  这里的type="text/javascript"可以省略，默认就是它。
  ```


### 2-2.基本语法入门

#### 2-2-1.定义变量

```javascript
var num = 1;
var name = "liuwx"
```

#### 2-2-2.条件控制

```javascript
if(布尔值) {

}else {
    
}
```

### 2-3.数据类型

#### 2-3-1.数字

> number类型包含整数、浮点数、负数等所有数字类型

```JavaScript
1	// 整数
1.1 // 浮点数
-1 	// 负数
NaN	// 不是任何数，not a number
NaN != NaN,需要使用isNaN(NaN)方法来判定
Infinity // 表示无穷大
```

#### 2-3-2.字符串

> 字符串类型

```javascript
var str = "124"
```

#### 2-3-3.布尔

> Boolean类型

```javascript
var flag = true;
```

#### 2-3-4.逻辑运算

> 与、或、非

```JavaScript
&& 
||
!
```

#### 2-3-5.比较运算符

> 赋值，对比

```javascript
= : 赋值
== : 类型不一样，值一样，也是true
=== ：类型一样，值也一样，才能使true
```

#### 2-3-6.数组

> array

```javascript
var arr = [1,2,'abc',null,true];
```

#### 2-3-7.对象

> 对象类型

```javascript
var person = {
    name: "liuwx",
    age: 27,
    tags: ["java","web","linux"]
}
```

### 2.4.严格检查模式

> 解决JavaScript语法随意性有可能会导致的问题

```javascript
1：设置 File | Settings | Languages & Frameworks | JavaScript
下JavaScript language version为es6
2：在javascript代码的第一行写'use script'
<script>
 	'use script'   
</script>
```

## 3、数据类型

### 3-1.字符串

#### 3-1-1.写法

```javascript
var str1 = '';
var str2 = "";
var str3 = ``; // 这种情况可以原样输出
    str3 = `a
    		 b
    		  c`;
  可以原样输出。
```

#### 3-1-2.转义

```javascript
\' 
\n
\t
\u4e2d  unicode字符
```

#### 3-1-3.模拟字符串

```javascript
var name = 'liuwx';
console.log("hello ${name}");// 可以直接打印出值！
```

#### 3-1-4.方法

```javascript
常用方法：
 length
 indexOf()
 substring()  [)
 charAt()
 toUpperCase()
注：
  可以直接用str[0]这种方式来取里面的元素！！！
```

### 3-2.数组

#### 3-2-1.定义

> 数组可以存放任意类型的数据。
>
> 它本质上就是存取数据用的，所以学会它的增删改查相关方法即可。

```javascript
var arr = [];
var arr = new Array();
推荐第一种。
```

#### 3-2-2.长度

```javascript
数组的长度是可变的。
var arr = [1,2,3];
arr.length = 5;
这样长度就变成了5。
arr.length = 2;
这样，长度就变成了2，里面的元素3就会被删除掉。
```

#### 3-2-3.常用方法

##### 3-2-3-1.查

```javascript
arr.length
arr[i]
arr.indexOf()
arr.slice()  相当于字符串的substring

```

##### 3-2-3-2.增与减

```javascript
arr.push(); 从数组尾部追加
arr.pop();  从数组尾部弹出一个，数组本身会变化。

arr.unshift(); 从数组头部添加
arr.shift();   从数组头部删除一个，数组本身会变化。

arr.concat();  从数组尾部追加，但是不改变原数组

arr.splice(); 向(从)数组添加(删除)项目，并返回删除的项目。会改变原始数组。
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.splice(2, 0, "Lemon", "Kiwi");
// 删除Orange
fruits.splice(fruits.indexOf("Orange"),1);

```

<img src="https://gitee.com/liuwenxiu/myblogimg/raw/master/img/image-20220302175100229.png" alt="image-20220302175100229" style="zoom:50%;" />

##### 3-2-3-3.改

```javascript
arr.join(","); 将数组元素按逗号拼接
arr.sort();    将数组排序
arr.reverse()  将数组排序进行反转
```

### 3-3.对象

#### 3-3-1.定义

> 若干个键值对。
>
> ==其中键一定是字符串类型，值可以是任意类型==

```javascript
var person = {
    "name": "liuwx",
    "age": 28,
    "address": "成都"
}
```

#### 3-3-2.常用操作

```javascript
取值：
  person.name
添加属性：
  person.weight = 60;
删除属性：
  delete person.weight;
判断属性是否存在：
  "age" in person 
  返回true
  
```

### 3-4.流程控制

#### 3-4-1.if判断

```javascript
if() {
    
}else if() {
    
}else {
    
}
```

#### 3-4-2.while循环

```javascript
while(i<100) {
    i++;
    console.log(i);
}
```

#### 3-4-3.for i

```javascript
for (let i = 0;i < arr.length;i++) {
    
}
```

#### 3-4-4.for in

```javascript
for(var index in arr) {
    console.log(arr[index]);
}
index是下标
```

#### 3-4-5.for of es6新特性

```javascript
for(var item of arr) {
    console.log(item);
}
item是元素
```

#### 3-4-6.forEach es6新特性

```javascript
arr.forEach(function(index,item) {
    
});

es6写法
arr.forEach((index,item) => {
    
});
```

### 3-5.Map & Set

> ES6的新特性

#### 3-5-1.Map

> key value键值对

```javascript
var map = new Map([["tom",22],["jack",13],["alex",25]]);

添加
map.set("ajax",33);

获取
map.get("ajax");

是否存在
map.has("ajax");

删除
map.delete("ajax");
```

#### 3-5-2.Set

> 无序不可重复的元素
>
> 如果重复了，会自动去重

```javascript
var set = new Set([1,2,3]);

添加
set.set([4,5,6]);
> Set(4) {1, 2, 3, Array(3)}
set.add(8);
> Set(4) {1, 2, 3, 8, Array(3)}

是否存在
set.has(8);
> true

删除
set.delete(7);
```

## 4.函数

### 4-1.函数定义

> 函数几乎和方法一样。
>
> 区别就是：
>
> ​	方法是对象里的，函数是放在对象外的

```javascript
定义方式一：
function fun(args) {
    ...
}
    
定义方式二：
var fun = function(args) {
    
}
注意：需return才能返回值。

例如：
 var abs = function(x) {
    if(x>=0) {
        return x;
    }else {
        return -x;
    }
}
函数调用：
abs(10);
```

### 4-2.特殊情况处理

> 如果函数需要参数确不传入参数

```javascript
var abs = function(x) {
    if(typeof x != 'number') {
        throw '请传入数字！'
    }
    if(x>=0) {
        return x;
    }else {
        return -x;
    }
}
```

### 4-3.多个参数问题

> 一个函数，传入了多个参数。
>
> 比如abs(1,2,3);
>
> 这种情况该如何处理

#### 4-3-1.arguments参数

> 这是js免费赠送的关键字，
>
> 里面包含了所有的参数。

```javascript
var abs = function(x) {
    if(typeof x != 'number') {
        throw '请传入数字！'
    }
    if(x>=0) {
        return x;
    }else {
        return -x;
    }
    
    if(arguments.length > 1) {
        console.log(arguments);
    }
}
```

#### 4-3-2.rest参数

> 因为arguments中包含了所有的参数，
>
> 如果我想要使用除了x以外的参数，
>
> 是否可以直接获取？
>
>  可以，使用rest参数。

```javascript
var abs = function(x,...rest) {
    
    if(x>=0) {
        return x;
    }else {
        return -x;
    }
    
    if(rest.length > 1) {
        console.log(rest);
    }
}
```

### 4-4.变量作用域

#### 4-4-1.一个函数的内外

> 函数内部定义的变量，不可以在外部使用。

```javascript
<script>
    function fun1() {
        var x = 1;
    }
    console.log(x);
</script>
报错：
 Uncaught ReferenceError: x is not defined
```

#### 4-4-2.多个函数的同名变量

> 多个函数有同名变量，互不影响。

```javascript
<script>
    function fun1() {
    var x = 1;
    console.log(x);
    }
    function fun2() {
        var x= 2;
        console.log(x);
    }
    fun1();
    fun2();
</script>

都可以正常打印。
```

#### 4-4-3.变量自动提升作用域

> JavaScript内部执行引擎会自动提升变量作用域。

```javascript
function fun3() {
    var x =1;
    x = y +2;
    console.log(y);
    var y = 3;
}
fun3();

报错： undefined
说明： y已经定义，只是没有赋值。如果y没有定义它会报is not defined
因此：前端开发会在一开始定义所有的变量。
function fun3() {
    var x = 1,
        y = 3,
        h,k;
    x = y +2;
    console.log(y);
}
```

#### 4-4-4.函数内部的函数

> 1.函数内部拥有函数，那么内部函数可以调用外部函数的变量，外部函数不能调用内部函数的变量
>
> 2.如果内外有同名变量，那么按照先从内部找的原则，如果在内部找到了变量，外部有也不用了，内部没有找到，就会用外部的。

```javascript
1： 
function fun4() {
    var x  =1;
    function fun5() {
        var y = 2;
    }
    var z = x + y;
    console.log(z);
    fun5();
}
fun4();

报错： 
 y is not defined
说明：
 外部函数不能调用内部函数的变量。

2：
    function fun4() {
        var x  =1;
        function fun5() {
            var x = 2;
            console.log(x);
        }
        fun5();
    }
    =fun4();
打印：
 2
说明：
 如果内外有同名变量，那么按照先从内部找的原则，如果在内部找到了变量，外部有也不用了，内部没有找到，就会用外部的。
```

#### 4-4-5.全局变量

> 全局变量就是定义在<script>下，不在任何函数内部。
>
> 全局变量默认绑定在window里。

```javascript
<script>
    var x = 1;
    console.log(x);
    alert(window.x);
</script>
x是全局变量，它绑定在window对象里。
alert可以直接使用，说明它也绑定在window里。

var old_alert = window.alert;
old_alert(222);
可以将alert赋给其他对象。
也就是说你可以修改window的方法。
如下：
old_alert = function (arg) {

}
old_alert(333); 
这样old_alert就不能弹框了！

```

```javascript
因为所有的全局变量都绑定在window中，所以在项目开发中，
多个前端程序员协作开发，难免会有名称重复，
因此约定使用一个对象来定义自己的变量和方法。
var campaignApp = {
    name: '',
    function add() {
        
    }
}
```



#### 4-4-6.局部变量

> ES6新特性，使用let定义局部变量。
>
> 建议变量定义都是用let，
>
> 不过很多浏览器并不支持es6.

```javascript
function fun2() {
    for (var i = 0; i < 10; i++) {
        console.log(i);
    }
    console.log("------")
    console.log(i+1);
}
fun2();
结果：
 11是可以被输出的，说明i的作用域超出了for循环。
 
function fun2() {
     for (let i = 0; i < 10; i++) {
         console.log(i);
     }
     console.log("------")
     console.log(i+1);
 }
fun2();
结果：
   Uncaught ReferenceError: i is not defined
```

#### 4-4-7.只读变量

> 只读变量就是常量。使用关键字const

```javascript
es5中定义常量约定使用大写字母，但是你还是可以改，所以就存在隐患。
es6中新增了const关键字，用它来定义常量后，不可更改。

function fun3() {
    const PI = '3.14';
    console.log(PI);
    PI= '3.15';
}
fun3();
报错：
 Uncaught TypeError: Assignment to constant variable.
```

### 4-5.方法

> 在对象内部，就是方法。
>
> 其定义等与函数一样，它两只是位置不一样而已。

```javascript
var liuwx = {
    name: 'liuwenxiu',
    birth: 2000,
    age: function () {
        let now = new Date().getFullYear();
        return now - this.birth;
    }
}

liuwx.age();
22
liuwx.birth
2000
调用方法需要使用括号。
```

```javascript
关于this
function getAge() {
    let now = new Date().getFullYear();
    return now - this.birth;
}
var liuwx = {
    name: 'liuwenxiu',
    birth: 2000,
    age:getAge
}
this:谁来调用我，我就代表谁。
liuwx对象调用getAge，所以getAge方法里的this代表liuwx，
所以 this.birth = liuwx.birth

如果直接调用getAge(),
报：NaN
因为getAge是属于全局方法，全局方法又绑定在window里，
但是window里并没有birth属性，所以this.birth就是NaN。
所以你可以定义全局变量：
var birth  = 1993;
getAge()
29
```

> apply(),每个函数都可以使用。
>
>   getAge.apply(liuwx,[]);

```javascript
关于apply
在上例中，如果非要使用getAge方法，并且要指定其中的birth是liuwx对象里的birth呢？
解：
  使用apply(),这个是每一个函数都有的方法，
里面传两个参数，this指定的对象，以及参数。
getAge.apply(liuwx,[]);
22
```

## 5.内部对象

> 标准对象类型
>
> typeof 123
> 'number'
>
> 
>
> typeof '123'
> 'string'
>
> 
>
> typeof true
> 'boolean'
>
> 
>
> typeof []
> 'object'
>
> 
>
> typeof {}
> 'object'
>
> 
>
> typeof NaN
> 'number'
>
> 
>
> typeof Math.abs
> 'function'
>
> 
>
> typeof undefined
> 'undefined'

### 5-1.日期

> 关于日期相关。

#### 5-1-1.常用属性

```javascript
 var date = new Date();
    date.getFullYear(); // 年
    date.getMonth();   // 月 0-11
    date.getDate();    // 日
    date.getDay();     // 星期几 0-6
    date.getHours();   //小时
    date.getMinutes(); //分
    date.getSeconds(); //秒
    date.getTime();    // 当前毫秒值
    date.toLocaleString(); // '2022/3/6 22:26:00'
```

### 5-2.JSON

> 轻量级的数据交换格式，
>
> 前台数据交换给后台，后台数据交换给前台等
>
> key:value形式

```javascript
JSON是个内置对象，可以直接使用
JSON.parse();	 将json字符串解析为对象。
JSON.stringfy(); 将对象转换为json字符串。
```

对象与JSON字符串的区别：

- JSON的key都是string类型的，需要带引号。而对象不用。

## 6.面向对象编程

> 面向对象编程的语言：java，JavaScript，c##

### 6-1.定义对象

```javascript
class Student {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }
    study() {
        console.log(this.name +" love study!");
    }
}
var student = new Student('liuwx',27);
```

### 6-2.对象继承

```javascript
class Tong extends Student{
    constructor(name,age,hoby) {
        super(name,age);
        this.hoby = hoby;
    }
    song() {
        console.log(this.name + "  " +this.age +" 的hoby是："+this.hoby);
    }
}
var tong = new Tong("tong",23,"sing song");
tong.song();
打印：
tong  23 的hoby是：sing song
```

### 6-3.原型链

> 所有的对象的父类都是Object，Object也是继承Object

<img src="D:\liuwx\it study\前台\JS\JavaScript.assets\84d8810caa3349e989a3a6c1de44f52atplv-k3u1fbpfcp-zoom-in-crop-mark1304000.awebp" alt="img" style="zoom:60%;" />

## 7.操作BOM对象

> 浏览器对象模型（Browser Object Model）

**核心**

​	IE

​	Chrome

​	Safari

​	FireFox

**三方**

​	qq浏览器

​	360浏览器

### 7-1.Window对象（重要）

> 代表浏览器窗口

```javascript
window.innerHeight
150
window.innerWidth
1280
window.outerHeight
760
window.outerWidth
1280
```

### 7-2.Navigator对象

> 代表浏览器对象，就是浏览器的信息

```javascript
navigator.appVersion
'5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36'

navigator.userAgent
'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36'
```

### 7-3.Document对象（重要）

> 代表Dom对象，Html DOm对象

```javascript
document.title
// 获取cookie
document.cookie
'OGPC=19027679-1:; OGP=-19027679:; 1P_JAR=2022-03-07-14'


<dl id = 'app'>
    <dd>aa</dd>
    <dt>bb</dt>
    <dt>cc</dt>
</dl>
<script>
    var domApp = document.getElementById("app");
</script>
```

### 7-4.Screen

> 代表电脑当前屏幕

```javascript
screen.width
screen.height
```

### 7-5.History

> 代表浏览器历史

```javascript
history.back();
history.forward();
```

### 7-6.Location（重要）

> 代表当前页面的url信息

```javascript
location.host
location.href
location.assign('https://www.baidu.com');//可以直接跳转某个页面 
location.reload();//重新加载页面。
```

## 8.操作DOM对象

> 文档对象模型(Document Object Model)

**核心就是一颗文档树，对其进行增删改查**

- 增加：增加一个dom节点
- 修改：修改一个dom节点
- 查找：查找一个dom节点
- 删除：删除一个dom节点

**什么是dom节点呢?**

其实就是dom树的一个节点，比如说：一个div，一个h标签，一个table等等。

### 8-1.获取DOM对象

> 获取到的dom对象都是数组！！！

```javascript
<dl id = 'app'>
    <dd>aa</dd>
<dt>bb</dt>
<dt>cc</dt>
</dl>
<h1>大标题</h1>
<div class="aaa"><span>kkk</span></div>
    <script>
        document.getElementsByClassName("aaa");
        document.getElementsByTagName("h1");
        document.getElementById("app")
    </script>
获取到的dom对象都是数组！！！
```

### 8-2.更新DOM对象

```javascript
let app = document.getElementById("app");
app.style.color = 'red'
'red'
app.style.padding = '2em'
'2em'
app.innerHTML = '<dt>cc</dt>'  //会把app对象覆盖。
app.innerHTML = app.innerHTML + '<dt>dd</dt>'// 追加dd，不会覆盖。
'<dt>cc</dt>'
app.innerText = 'dd'
'dd'
```

### 8-3.删除DOM对象

```javascript
<dl id='app'>
    <dd>aa</dd>
    <dt>bb</dt>
    <dt>cc</dt>
</dl>
<script>
    document.getElementById("app")
</script>

app.removeChild(app.children[0]);
特点：
 删除是个动态的动作，当你删除第一个后，第二个就变成了第一个。
```

### 8-4.插入DOM对象

```javascript
<li id="js">JavaScript</li>
<ul id="list">
    <li id="se">JavaSE</li>
    <li id = 'ee'>JavaEE</li>
    <li id = 'me'>JavaME</li>
</ul>
<script>
    let list = document.getElementById("list");
    let js = document.getElementById("js");
    let se = document.getElementById("se");

    list.insertBefore(js,se); // 将js插入到es的前面
	list.appendChild(js);     // 将js追加到list的子元素的最后一位。
</script>
```

## 9.操作表单

> 获取form表单的值，
>
> 以及提交表单，
>
> 提交表单前要进行验证
>
> md5表单提交前进行密码加密

```javascript
<script src="https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.min.js"></script>
<form action="" method="post" onsubmit="save()">
    <div>
        <span>姓名：</span>
        <input id="uname" type="text" name="username" required>
    </div>
    <div>
        <span>性别：</span>
        <span id="sex">
            <input type="radio" name="male" value="male">男
            <input type="radio" name="female" value="female">女
        </span>
    </div>
    <div>
        <span>密码：</span>
        <input id="pwd" type="password" name="password" >
    </div>
    <button type="submit">提交</button>
</form>
<script>
    function save() {
        alert("2122")
        var name = document.getElementById("uname").value;
        let pwd = document.getElementById("pwd").value;
        console.log("23232323");
        var pwd2 = md5(pwd);

        console.log(pwd2+"...");
        console.log(name);
        console.log(pwd);
        return true;
    }
</script>
```

## 10.JQuery

> jQuery 是一个 JavaScript 库。
>
> 里面包含了很多方法，使得操作dom等更加方便。

> $(选择器).action();

### 10-1 初识JQuery

```html
使用之前需要先引入jq
<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
<body>
    <a href="" id = "atab">点我</a>
<script>
    $("#atab").click(function () {
        alert(111);
    });
</script>
</body>
```

### 10-2.JQuery事件

> 无非就是鼠标事件，键盘事件，页面事件这三类

#### 10-2-1.鼠标事件

```javascript
mousedown([[data],fn])		// 鼠标按下，不用松开
mouseenter([[data],fn])		// 鼠标指针穿过元素时
mouseleave([[data],fn])		// 鼠标指针离开元素时(被选元素，不包括子元素)
mousemove([[data],fn])		// 当鼠标指针在指定的元素中移动时
mouseout([[data],fn])		// 当鼠标指针从元素上移开时（包括子元素）
mouseover([[data],fn])		// 当鼠标指针位于元素上方时
mouseup([[data],fn])		// 当在元素上放松鼠标按钮时
...
详细可参考：
 https://jquery.cuishifeng.cn/index.html
```

#### 10-2-2.键盘事件

```javascript
keydown([[data],fn])		// 当键盘或按钮被按下时，发生 keydown 事件。
    $(window).keydown(function(event){
      switch(event.keyCode) {
        // ...
        // 不同的按键可以做不同的事情
        // 不同的浏览器的keycode不同
        // 更多详细信息:     https://unixpapa.com/js/key.html
        // 常用keyCode： 空格 32   Enter 13   ESC 27
      }
    });
keypress([[data],fn])
keyup([[data],fn])			// 当按钮被松开时，发生 keyup 事件。它发生在当前获得焦点的元素上。
mousedown([[data],fn]
```

#### 10-2-3.页面事件

```javascript
```

