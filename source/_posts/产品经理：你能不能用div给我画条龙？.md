---
title: 产品经理：你能不能用div给我画条龙？
date: 2021-07-13 17:11:06
tags: canvas
---

事情是这样的，前天上午产品经理说想要做一个心愿墙，问我能不能行

我心想，这太容易了，但为了多摸一天鱼，我说还是**有点挑战**的

结果下午，产品经理和设计师就给我发来了设计参考

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4d686084e16d4dc88b5b17a1d7cd0e1a~tplv-k3u1fbpfcp-watermark.image)

他们说，心愿墙的设计大致是这样的，每个用户的心愿都是一个气泡，而客户的品牌是”龙“，我们希望在前端页面里用气泡呈现一个龙形的设计，每个气泡都会浮动，鼠标移上去变大，点击后展示心愿详情。

当时我的内心是这样的

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/447ed6e19cc04dc1aa00499729d12eeb~tplv-k3u1fbpfcp-watermark.image)

我摸鱼的一天要泡汤了吗？

**谁都不能阻止我摸鱼**

但首先要解决最核心的问题

# 龙从哪里来？

设计师说了，他可以给我一条由气泡组成的龙的设计稿，我说那等你设计稿给我，我再研究把。结果他说，已经有了，你就用这个吧

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/862c493c8bd34b2e8dd3713f9be40d5f~tplv-k3u1fbpfcp-watermark.image)

**我的刀呢？**

# 互动问题

> 请在评论区留下你遇到过的最奇葩的需求

# 拆解需求

遇到不靠谱的产品经理和设计师，前端工程师真是惨。我们顶着最后交付成品的`巨锅`，所有`deadline`感觉都只是用来压榨前端工程师的。

我们只能靠自己，因为

**谁都不能阻止我摸鱼**

- 需求1：有鼠标交互效果（太简单）
- 需求2：气泡要浮动（css动画，easy）
- 需求3：气泡组成一条龙

此时我脑海里响起这首烂大街的歌

> 左边跟我一起画个龙，在你右边画一道彩虹~

```
诶，画个龙
用什么画，canvas
canvas能获得指定区域的像素点阵
```

卧槽，有招儿了

# 代码时间

先用图片搜索找一张龙的剪影

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/10b4df2d7b9b424fb431753167337851~tplv-k3u1fbpfcp-watermark.image)

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8098e5ef8d6b46288cfd2292dccc2b65~tplv-k3u1fbpfcp-watermark.image)

## 将图片绘制到canvas中

```javascript
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");

var image = new Image();
image.src = "dragon.jpg";
image.onload = function(){
        canvas.width = image.width;
        canvas.height = image.height;

        ctx.drawImage(image,0,0);
}
复制代码
```

## 获取并裁剪画布的点阵信息

```javascript
var imageData = ctx.getImageData(0,0,image.width,image.height).data;
ctx.fillStyle = "#ffffff";
ctx.fillRect(0,0,image.width,image.height);

var gap = 6;

for (var h = 0; h < image.height; h+=gap) {
    for(var w = 0; w < image.width; w+=gap){
            var position = (image.width * h + w) * 4;
            var r = imageData[position], g = imageData[position + 1], b = imageData[position + 2];

            if(r+g+b==0){
                    ctx.fillStyle = "#000";
                    ctx.fillRect(w,h,4,4);
                }
    }
}
复制代码
```

现在我们获得了这样一条龙的点阵信息

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab8c4d11d4e54e0980f0eb02b5426131~tplv-k3u1fbpfcp-watermark.image)

## 通过点阵信息生成气泡dom

```javascript
var dragonContainer = document.getElementById("container");
var dragonScale = 2;

for (var h = 0; h < image.height; h+=gap) {
    for(var w = 0; w < image.width; w+=gap){
            var position = (image.width * h + w) * 4;
            var r = imageData[position], g = imageData[position + 1], b = imageData[position + 2];

            if(r+g+b==0){
                    var bubble = document.createElement("img");
                    bubble.src = "bubble.png";
                    bubble.setAttribute("class","bubble");

                    var bubbleSize = Math.random()*10+20;
                    bubble.style.left = (w*dragonScale-bubbleSize/2) + "px";
                    bubble.style.top = (h*dragonScale-bubbleSize/2) + "px";
                    bubble.style.width = bubble.style.height = bubbleSize+"px";
                    bubble.style.animationDuration = Math.random()*6+4 + "s";

                    dragonContainer.appendChild(bubble);
                }
    }
}
复制代码
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/74fea9a084a14630b925b091adfb6f6c~tplv-k3u1fbpfcp-watermark.image)



作者：大帅老猿链接：https://juejin.cn/post/6963476650356916254  来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### 关于疑问

var position = (image.width * h + w) * 4;  公式获取当前点?

![图片](https://gitee.com/xian_hong_xiao/assets/raw/master/img/juzhen.png)



### 参考原码

``````html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }
      .bubble {
        position: absolute;
        background-color: red;
      }
      .wrap {
        position: relative;
        position: absolute;
        top: 0;
      }
      canvas {
        box-sizing: border-box;
      }
      .wrap > div {
        position: absolute;
        transition: all 0.5s;
        cursor: pointer;
      }
      .wrap > div:hover {
        transform: scale(20);
        z-index: 999;
      }
    </style>
  </head>
  <body>
    <canvas id="canvas"></canvas>
    <div class="wrap"></div>
  </body>
</html>
<script>
  var canvas = document.getElementById("canvas");
  var ctx = canvas.getContext("2d");
  var list = [{ x: -100, y: -100 }];
  var image = new Image();
  image.src = "dragon.jpg";
  image.onload = function () {
    canvas.width = image.width;
    canvas.height = image.height;
    ctx.drawImage(image, 0, 0);
    var imageData = ctx.getImageData(0, 0, image.width, image.height).data;
    function fn() {
      var gap = 6;
      var dragonScale = 2;
      for (var h = 0; h < image.height; h += gap) {
        for (var w = 0; w < image.width; w += gap) {
          var position = (image.width * h + w) * 4;
          var r = imageData[position],
            g = imageData[position + 1],
            b = imageData[position + 2];
          if (r + g + b >= 0 && r + g + b <= 159) {
            let w1 = w - 10;
            let h1 = h - 10;
            var fillStyle = `rgba(${parseInt(Math.random() * 255)},${parseInt(
              Math.random() * 255
            )},${parseInt(Math.random() * 255)})`;
            var bubble = document.createElement("div");
            var bubbleSize = Math.random() * 10 + 20;
            bubble.style.left = w1 + "px";
            bubble.style.top = h1 + "px";
            bubble.style.width = "5px";
            bubble.style.height = "5px";
            bubble.style.animationDuration = Math.random() * 6 + 4 + "s";
            bubble.style.backgroundColor = fillStyle;
            console.log(document.getElementsByClassName("wrap")[0]);
            document.getElementsByClassName("wrap")[0].appendChild(bubble);
          }
        }
      }
    }
    fn();
    setInterval(() => {
      fn();
    }, 3000);
    ctx.clearRect(0, 0, image.width, image.height);
  };
</script>

``````





### 效果图

![龙](https://gitee.com/xian_hong_xiao/assets/raw/master/img/long.png)

