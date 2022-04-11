---
title: 移动端处理1px的问题
date: 2022-01-05 17:27:57
tags: js

---

### 方法1，使用meta+rem缩放

*  window.devicePixelRatio获取像素密度
* dpr = 1/ devicePixo;  获取像素比
* 对meta进行缩放，缩放比例为dpr。（缩放后边线还原了1px,但是内容也被缩放了)
* 对内容缩放进行还原

``````html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        *{
            margin:0;
            padding: 0;
        }
        .box{
            width:1rem;
            height: 1rem;
           /*  width:100px;
            height: 100px; */
            background: red;
            border: 1px solid blue;
        }
    </style>
</head>
<body>
    <div class="box"></div>
    <button id="resetMeta">还原meta</button>
</body>
</html>
<script>
    function getDevicePixo (){
        const devicePixo = window.devicePixelRatio;
        const dpr = 1/ devicePixo;
        const meta = document.querySelector("meta[name='viewport']");
        meta.setAttribute('content','width=device-width, initial-scale='+dpr);
        const fontsize = document.documentElement.clientWidth / dpr; //100
        document.querySelector("html").style.fontSize=fontsize+'px' 
 
        return dpr;
    }
    window.onresize=function(){
        console.log(getDevicePixo());
    }

      
    document.querySelector("#resetMeta").onclick=function(){
        const meta = document.querySelector("meta[name='viewport']");
        meta.setAttribute('content','width=device-width, initial-scale='+1);
    }
</script>
``````



### 方法二   使用媒体查询  min-device-pixel-ratio

``````css
.border { border: 1px solid #999 }
@media screen and (-webkit-min-device-pixel-ratio: 2) {
　　.border { border: 0.5px solid #999 }
}
@media screen and (-webkit-min-device-pixel-ratio: 3) {
　　.border { border: 0.333333px solid #999 }
}
``````

[原文：https://blog.csdn.net/aiyin9982/article/details/101685016](https://blog.csdn.net/aiyin9982/article/details/101685016)

