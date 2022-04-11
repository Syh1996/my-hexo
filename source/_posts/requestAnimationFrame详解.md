---
title: requestAnimationFrame详解
date: 2021-02-21 11:54:43
tags: JavaScript
---



为什么要说它，源于看到的一道面试题：问题是用js实现一个无限循环的动画。

首先想到的是定时器

```javascript
<!doctype html>
<html lang="en">
<head>
    <title>Document</title>
    <style>
        #e{
            width: 100px;
            height: 100px;
            background: red;
            position: absolute;
            left: 0;
            top: 0;
            zoom: 1;
        }
    </style>
</head>
<body>
<div id="e"></div>
<script>


    var e = document.getElementById("e");
    var flag = true;
    var left = 0;

    function render() {
        if(flag == true){
            if(left>=100){
                flag = false
            }
            e.style.left = ` ${left++}px`
        }else{
            if(left<=0){
                flag = true
            }
            e.style.left = ` ${left--}px`
        }
    }
    setInterval(function(){
         render()
    },1000/60)

</script>
</body>
</html>
```

可以说是完美实现！

至于时间间隔为什么是1000/60,这是因为大多数屏幕渲染的时间间隔是每秒60帧。

既然setInterval可以搞定为啥还要用requestAnimationFrame呢？最直观的感觉就是，添加api的人是个大神级牛人，我只能怀疑自己。

所以搜索相关问题发现以下两点

#### requestAnimationFrame 比起 setTimeout、setInterval的优势主要有两点：

##### 1、requestAnimationFrame 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒60帧。

##### 2、在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的的cpu，gpu和内存使用量。

直接上代码：



```javascript
<!doctype html>
<html lang="en">
<head>
    <title>Document</title>
    <style>
        #e{
            width: 100px;
            height: 100px;
            background: red;
            position: absolute;
            left: 0;
            top: 0;
            zoom: 1;
        }
    </style>
</head>
<body>
<div id="e"></div>
<script>


    var e = document.getElementById("e");
    var flag = true;
    var left = 0;

    function render() {
        if(flag == true){
            if(left>=100){
                flag = false
            }
            e.style.left = ` ${left++}px`
        }else{
            if(left<=0){
                flag = true
            }
            e.style.left = ` ${left--}px`
        }
    }

    //requestAnimationFrame效果
    (function animloop() {
        render();
        window.requestAnimationFrame(animloop);
    })();

</script>
</body>
</html>
```

我没有添加各个浏览器的兼容写法，这里只说用法。

效果是实现了，不过我想到两个问题。

1、怎么停止requestAnimationFrame？是否有类似clearInterval这样的类似方法？

第一个问题：答案是确定的 必须有：cancelAnimationFrame()接收一个参数 requestAnimationFrame默认返回一个id，cancelAnimationFrame只需要传入这个id就可以停止了。



```javascript
<!doctype html>
<html lang="en">
<head>
    <title>Document</title>
    <style>
        #e{
            width: 100px;
            height: 100px;
            background: red;
            position: absolute;
            left: 0;
            top: 0;
            zoom: 1;
        }
    </style>
</head>
<body>
<div id="e"></div>
<script>


    var e = document.getElementById("e");
    var flag = true;
    var left = 0;
    var rafId = null


    function render() {
        if(flag == true){
            if(left>=100){
                flag = false
            }
            e.style.left = ` ${left++}px`
        }else{
            if(left<=0){
                flag = true
            }
            e.style.left = ` ${left--}px`
        }
    }

    //requestAnimationFrame效果
    (function animloop(time) {
        console.log(time,Date.now())
        render();
        rafId = requestAnimationFrame(animloop);
        //如果left等于50 停止动画
        if(left == 50){
            cancelAnimationFrame(rafId)
        }
    })();

    //setInterval效果
    // setInterval(function(){
    //     render()
    // },1000/60)

</script>
</body>
</html>
```

![img](https://upload-images.jianshu.io/upload_images/15081804-df8b351c84ea0d64.gif?imageMogr2/auto-orient/strip|imageView2/2/w/744/format/webp)

附上一个效果图。也可直接capy代码测试。

2、如果我想动画频率降低怎么做，为什么不考虑加快呵呵 当前刷新频率已经是屏幕的刷新频率了再快也没有意义了

这个略微麻烦点

默认情况下，requestAnimationFrame执行频率是1000/60,大概是16ms多执一次。

如果我们想每50ms执行一次怎么办呢？

requestAnimationFrame执行条件类似递归调用 （说的是类似）别咬我，既然这样的话我们能否自定一个时间间隔再执行呢？当然定时器这么low的东西我们就不考虑了，都已经抛弃它用rAF了（都快结束了我才想起写简写太他妈长了），

这个思路来源于我几年前搞IM的一个项目，服务端推送消息为了减小包的大小不给时间戳，这个我们做前端的都知道，我们虽然很牛逼 不过用户更牛逼，万一改了时间就不好玩了。

解决方案是 当和服务端通信时 记录下一个时间差，（时间差等于服务端时间-本地时间）不管正负我们只要这个时间差。这样每当我们接受到消息 或者发送消息的时候我们就拿本地时间和是价差相加。这样就可以保证和服务端时间是一致的了，思路是不是很牛逼哈哈。

撤了半天我们通过以上思路来解决下rAF改变间隔的问题

上代码



```javascript
<!doctype html>
<html lang="en">
<head>
    <title>Document</title>
    <style>
        #e{
            width: 100px;
            height: 100px;
            background: red;
            position: absolute;
            left: 0;
            top: 0;
            zoom: 1;
        }
    </style>
</head>
<body>
<div id="e"></div>
<script>


    var e = document.getElementById("e");
    var flag = true;
    var left = 0;
    //当前执行时间
    var nowTime = 0;
    //记录每次动画执行结束的时间
    var lastTime = Date.now();
    //我们自己定义的动画时间差值
    var diffTime = 40;

    function render() {
        if(flag == true){
            if(left>=100){
                flag = false
            }
            e.style.left = ` ${left++}px`
        }else{
            if(left<=0){
                flag = true
            }
            e.style.left = ` ${left--}px`
        }
    }

    //requestAnimationFrame效果
    (function animloop() {
        //记录当前时间
        nowTime = Date.now()
        // 当前时间-上次执行时间如果大于diffTime，那么执行动画，并更新上次执行时间
        if(nowTime-lastTime > diffTime){
            lastTime = nowTime
            render();
        }
        requestAnimationFrame(animloop);

    })()
</script>
</body>
</html>
```