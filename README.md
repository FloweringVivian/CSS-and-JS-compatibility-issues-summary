# CSS-and-JS-compatibility-issues-summary

CSS和JS兼容性问题总结(PC端和移动端)

## 1. JS兼容性问题
* 判断数据类型

js六大数据类型：String、Number、Boolean、Null、Undefined、Object

前5种基本的数据类型可以用typeof来判断

------

* $.each()中使用return的问题
```javascript
function nextStep(){
    $.each(objList.templateData, function(key,val){
        if(val.next == false){
            alert("列表中含有过期的项，不能进行下一步");
            return;
        }
    });
    userList();
}
```

问题描述：看上面的这段代码，本来我希望的是如果objList.templateData的某一条数据的next值为false的话，就alert提示，并且return，不执行下面的userList()函数，但是实际上这样写还是执行了下面的userList()函数，原因是，$.each进行了封装，本身是个函数，所以return只是return到了$.each这个函数，而不是nextStep()这个函数，看网上的说法，将return换成了return false，也还是不行。

解决办法：换成原生的for循环。

```javascript
function nextStep(){
    for(var i=0;i<objList.templateData.length;i++){
        if(objList.templateData[i].next == false){
            alert("列表中含有过期的项，不能进行下一步");
            return;
        }
    });
    userList();
}
```

## 2. 移动端兼容性问题（H5兼容性问题）
* 父元素使用了transform，子元素position:fixed失效的问题

问题描述：我的页面中有个这样的需求，点击定位，页面从右向左拉出一个菜单，覆盖整个屏幕，这个滑动效果我是用transform写的，可以选择省，但是上面当前位置和取消所占的一行要求position:fixed，下面的省可以滚动，效果如下图：

![](https://github.com/FloweringVivian/CSS-and-JS-compatibility-issues-summary/blob/master/images/img1.jpg)  

结果就是position:fixed失效了，因为他的父元素运用了transform

解决办法：改变布局，让需要position:fixed的元素变成transform元素的兄弟元素，而不是子元素，这样就解决了，如果有更好的解决办法，欢迎大家踊跃发言告知，在此感谢！

------

* 移动端加了overflow:auto导致滚动不流畅的问题

问题描述：不知道大家开发移动端的过程中有没有需要加overflow-x:hidden;overflow-y:auto;类似这种的情况，加了以后发现滚动变得不流畅了。

解决办法：只需要给你加了overflow:auto的标签（例如class="list"）加上一个神奇的css属性即可：

```javascript
.list{
    -webkit-overflow-scrolling:touch;
}
```

* input获得焦点软键盘弹出时，position:fixed的footer遮挡内容区域的问题

问题描述：我的页面中footer是position:fixed的，页面中的input获得焦点，软键盘弹起后，我希望footer隐藏，避免遮挡内容区域。

解决办法：针对ios型号设备，可以通过input获得焦点还是失去焦点来判断，因为ios型号设备软键盘收起时，input会自动失去焦点，而安卓却不一定，安卓必须是点击页面的其他地方，input才会失去焦点，而直接点击软键盘上的完成或者关闭按钮，input不会失去焦点，所以针对安卓，需要使用resize方法，因为对于安卓设备来说，软键盘的弹出和收起，都会触发resize方法。

```javascript
//ios
$("input").bind("focus", function(){
    $("footer").hide();
}).bind("blur", function(){
    $("footer").show();
})

//安卓
var windowHeight = $(window).height();
$(window).resize(function(){
    var nowHeight = $(window).height();
    if(nowHeight < windowHeight){  //说明软键盘弹出
        $("footer").hide();
    }else{  //软键盘收起
        $("footer").show();
    }
})
```

* ios型号设备下输入框默认内阴影

解决办法：

```javascript
Element{
-webkit-appearance: none; 
}
```

## 3. CSS兼容性问题
* z-index兼容性问题

问题描述：假如页面中A和B是兄弟元素，C是A的子元素，D是B的子元素，当我给C和D都设置了绝对定位(position:absolute)，我给C设置了z-index:10; 给D设置了z-index:11; 此时发现在除了IE6、IE7以外的浏览器中正常显示（D元素在C元素的上层，盖住了C元素），但是在IE6和IE7中却没有达到理想状态。

解决办法：给C元素和D元素的父元素，也就是A元素和B元素也设置定位（可以根据实际需求设置相对定位或者绝对定位），并且让A元素的z-index值小于B元素（例如：给A设置z-index:1; 给B设置z-index:2;），此时会发现C元素和D元素的显示效果在IE6和IE7中也达到了理想效果。

小技巧：如果想让一个元素（D）绝对定位置于另一个元素（C）的上层，不光要把D元素的z-index值设置的大于C元素，还要把D元素的父元素的z-index值大于C元素的父元素（注：不一定是父元素，也可以是祖先元素，但要保证两个祖先元素是兄弟元素）。

------

* 最小高度问题

问题描述：IE6下最小高度为19像素，设置比19像素小，也会自动扩展到19像素。

解决办法：加overflow:hidden或者font-size:0

```javascript
<style>  
body{ margin:0;background:#000;}  
.box{height:5px;background:red; font-size:0;}  
/*  
    在IE6下高度小于19px的元素，高度会被当做19px来处理  
    解决办法:font-size:0; 或者 overflow:hidden;  
*/  
</style>  
  
<body>  
<div class="box"></div>  
</body> 
```

------

* IE6中绝对定位位置错误

问题描述：在ie6中，如果参照物没有触发haslayout ，那么绝对定位的容器的left和bottom就会有问题，在高版本浏览器中，如果定位元素的祖先元素没有设置定位样式，则会相对于body来进行定位。

解决办法：在相对定位的父容器上加入 zoom:1 来触发ie的haslayout即可解决。

小技巧：通常我们在设置一个容器为position:relative的时候 ，都会加上zoom:1来解决很多ie下的问题。

------

* IE6中绝对定位1像素偏差

问题描述：在 IE6 下定位元素的父级宽高都为奇数时，那么在 IE6 下定位元素的 right和bottom都有1像素的偏差。

解决办法：设为偶数。

```javascript
<style>  
#box1{width:303px;height:303px;border:1px solid black;position:relative;}  
#box2{width:50px;height:50px;background:#7c1;position:absolute;right:-1px;bottom:-1px;}  
</style>  
  
<body>  
<div id="box1">  
    <div id="box2"></div>  
</div>  
</body> 
```

------

* IE6浮动元素双边距bug

问题描述：IE6下块元素有浮动和横向margin的时候，横向的margin值被放大成两倍。

解决办法：给浮动元素加 display:inline。

```javascript
<style>  
body{margin:0;}  
.wrap{float:left;border:2px solid #000;}  
.box{width:100px;height:100px;background:red;margin:0 100px;float:left;display:inline;}  
/*  
    IE6下的双边距BUG:  
    在IE6下，块元素有浮动和横向margin的时候，横向的margin值会被放大成两倍  
    解决办法: display:inline;  
*/  
</style>  
  
<body>  
<div class="wrap">  
<div class="box"></div>  
</div>  
</body> 
```

------

* IE67下li底部间隙的BUG

问题描述：在ie67下，li本身没浮动，但是li的内容有浮动，li下边就会产生一个间隙。

解决办法：给li设置 float:left或者给li加vertical-align:top。

```javascript
<style>  
.list{ width:300px;margin:0;padding:0;}  
.list li{ list-style:none;height:30px;border:1px solid #000; font-size:12px; line-height:30px; vertical-align:top;}  
.list a{float:left;}  
.list span{float:right;}  
/*  
    IE6，7下li的间隙  
    在IE6，7下li本身没浮动,但是li内容有浮动的时候，li下边就会产生3px的间隙  
    解决办法: 1.给li加浮动 或者 2.给li加vertical-align:top;  
*/  
</style>  
  
<body>  
<ul class="list">  
    <li>  
        <a href="#">文字文字文字文字文字</a>  
        <span>作者</span>  
    </li>  
    <li>  
        <a href="#">文字文字文字文字文字</a>  
        <span>作者</span>  
    </li>  
</ul>  
</body>  
```

------

* ie6 下子级的相对定位

问题描述：ie6 下父级的overflow:hidden;是包不住子级的相对定位的。

解决办法：父级也设为相对定位。

```javascript
<style>  
#box1{width:500px; height:300px; background:blue; overflow:hidden;position:relative;}  
#box2{width:300px; height:500px; background:yellow; position:relative;}  
</style>  
</head>  
<body>  
<div id="box1">  
    <div id="box2"></div>  
</div>  
</body> 
```

------

* 在IE6下子元素margin问题

问题描述：在IE6下父级有边框的时候，子元素的margin值消失。

解决办法：触发父级的haslayout(给父级元素加zoom:1)。

```javascript
<style>  
body{margin:0;}  
.box{background:blue;border:1px solid #000;zoom:1;}  
.div{width:200px;height:200px;background:red;margin:100px;}  
</style>  
  
<body>  
<div class="box">  
    <div class="div"></div>  
</div>  
</body>  
```

------

* 绝对定位元素消失

问题描述：当浮动元素和绝对定位元素是并列关系的时候，在IE6下绝对定位元素会消失。

解决办法：给定位元素外面包个div。

```javascript
<style>  
.box{ width:200px;height:200px;border:1px solid #000; position:relative;}  
span{width:50px;height:50px;background:yellow; position:absolute;right:-20px;top:0;}  
ul{width:150px;height:150px;background:Red;margin:0 0 0 50px;padding:0; float:left; display:inline;}  
</style>  
  
<body>  
<div class="box">  
    <ul></ul>  
    <span></span>  
</div> 
```

------

* 表单控件1px间隙

问题描述：在IE6，7下输入类型的表单控件上下各有1px的间隙。

解决办法：给input加浮动。

```javascript
<style>  
.box{ width:200px;height:32px;border:1px solid red;}  
input{width:100px;height:30px;border:1px solid #000;margin:0;padding:0; float:left;}  
</style>  
<div class="box">  
    <input type="text" />  
</div>
```

------

* 表单控件加border:none无效

问题描述：在IE6，7下输入类型的表单控件加border:none无效。

解决办法：重置input的背景或者加border:0。

```javascript
<style>
.box{ width:200px;height:32px;border:1px solid red;background:yellow;}  
input{width:100px;height:30px;border:none;margin:0;padding:0; float:left; background:#fff;}  
</style> 
<div class="box">  
    <input type="text" />  
</div> 
```

------

* ie6下png

问题描述：ie6不支持png24的图片

```javascript
<!DOCTYPE HTML>  
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">  
<title>无标题文档</title>  
<style>  
body{ background:#000;}  
.box{width:400px;height:400px;background:url(img/png.png);}  
</style>  
<!--[if IE 6]>  
<script src="DD_belatedPNG_0.0.8a.js"></script>  
<script>  
DD_belatedPNG.fix('.box');  
</script>  
<![endif]-->  
</head>  
<body>  
<div class="box"></div>  
</body>  
</html> 
```






