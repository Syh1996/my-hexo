---
title: js三角函数
date: 2021-08-12 15:39:38
tags: js

---

**sin是这个角的对边和斜边的比。**

cos一是这个角挨着的那条边和斜边的比；tan是这个角的对边和邻边的比。

在平面直角坐标系xOy中设∠β的始边为x轴的正半轴，设点P（x，y）为∠β的终边上不与原点O重合的任意一点，设r=OP，令∠β=∠α，则：sin a=y/r；cos a=x/r。

[![img](https://iknow-pic.cdn.bcebos.com/730e0cf3d7ca7bcb52cb2850ae096b63f624a819?x-bce-process%3Dimage%2Fresize%2Cm_lfit%2Cw_600%2Ch_800%2Climit_1%2Fquality%2Cq_85%2Fformat%2Cf_jpg)](https://iknow-pic.cdn.bcebos.com/730e0cf3d7ca7bcb52cb2850ae096b63f624a819)

2 π  == 360度



const a = Math.sin(num  * π   / 180) == BC / AC

const b = Math.cos(num *  π  / 180 ) = AB/ AC



#### 公式原理 

![公式](https://gitee.com/xian_hong_xiao/assets/raw/master/img/sanjiao.png)

#### js实现圆周运动

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
      .circle {
        position: absolute;
        left: 500px;
        top: 500px;
        border: 2px solid red;
        border-radius: 50%;
        margin-left: -150px;
        margin-top: -150px;
        width: 300px;
        height: 300px;
      }
      .rect {
        position: absolute;
        width: 10px;
        height: 10px;
        background-color: blue;
        top: 350px;
        left: 500px;
      }
      .red{
          background: red;
      }
      .blue{
          background-color: blue;
      }
      .orange{
          background-color: orange;
      }
      .black{
          background-color: black;
      }
      .pink{
          background-color: pink;
      }
    </style>
  </head>
  <body>
    <div class="circle"></div>
    <div class=" rect rect1 red"></div>
    <div class="rect rect2 blue"></div>
    <div class="rect rect3 orange"></div>
    <div class="rect rect4 black"></div>
    <div class="rect rect5 pink"></div>
  </body>
</html>
<script>
  (function () {
  /*  
   普通使用
   var num = 0;
    var circle = document.querySelector(".circle");
    var middleX = circle.offsetLeft + circle.offsetWidth / 2;
    var middleY = circle.offsetTop + circle.offsetHeight / 2;

    function ani() {
      if (num === 360) {
        num = 0;
      } else {
        num++;
      }

      var line1Y = Math.sin((num * Math.PI) / 180) * 150;
      var line2X = Math.cos((num * Math.PI) / 180) * 150;
      console.log(line1Y, line2X);
      document.querySelector(".rect").style.cssText = `;left:${
        line2X + middleX
      }px;top:${line1Y + middleY}px`;
    }
    ani();
    setInterval(ani, 100); */
   
      
      
    //封闭类
    class AniClass {
      constructor() {
        this.num = 0;
        this.circle = document.querySelector(".circle");
        this.middleX = this.circle.offsetLeft + this.circle.offsetWidth / 2;
        this.middleY = this.circle.offsetTop + this.circle.offsetHeight / 2;
      }
      ani(className, delay) {
        this.move(className);
        setTimeout(()=>{
            setInterval(this.move.bind(this,className), 50);
        },delay)
        
      }

      move(className){
        if (this.num === 360) {
            this.num = 0;
          } else {
            this.num++;
          }

          var line1Y = Math.sin((this.num * Math.PI) / 180) * 150;
          var line2X = Math.cos((this.num * Math.PI) / 180) * 150;
          document.querySelector(className).style.cssText = `;left:${
            line2X + this.middleX
          }px;top:${line1Y + this.middleY}px`;
      }
    }

    [1,2,3,4,5].forEach(item=>{
       const Ani = new AniClass();
       Ani.ani('.rect'+item,4000*item);
   });
  })();
</script>

``````

