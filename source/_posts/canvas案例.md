---
title: canvas案例
date: 2022-04-08 09:14:52
tags: canvas
---

### 星空移动

``````html
<!--
 * @Description: file content
 * @Author: yuhongshao
 * @Date: 2022-04-07 18:55:03
 * @email: yuhongshao@tencent.com
 * @LastEditTime: 2022-04-07 20:30:07
-->
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        body{
            height: 100vh;
        }
    </style>
</head>

<body>
    <canvas id="canvas"></canvas>
</body>

</html>
<script>
    const canvas = document.querySelector("#canvas");
    const ctx = canvas.getContext('2d');
    const windowWidth = document.documentElement.clientWidth;
    const windowHeight = document.documentElement.clientHeight;
    canvas.width = windowWidth;
    canvas.height = windowHeight;
    ctx.fillStyle = 'black';
    ctx.fillRect(0, 0, windowWidth, windowHeight);

    function createPositon(min, max) {
        return Math.floor(Math.random() * (max - min)) + min;
    }

    class newDotInfo {
        constructor() {
            this.centerX = windowWidth / 2;
            this.centerY = windowHeight / 2;
            this.x = createPositon(0, windowWidth);
            this.y = createPositon(0,windowHeight);
            this.width = createPositon(2, 8);
            this.height = createPositon(2, 8);
            this.speed = Math.ceil(Math.random() * 2);
            this.xDirection = Math.random();
            this.yDirection = Math.random();

        }
    
        move() {

            if (this.xDirection > 0.5) {
                this.x += this.speed;

            }else if (this.xDirection <= 0.5) {
                this.x -= this.speed;

            }
         
            if (this.yDirection > 0.5) {
                this.y += this.speed;

            }else if (this.yDirection <= 0.5) {
                this.y -= this.speed;

            }

            ctx.fillStyle = 'white';
            ctx.fillRect(this.x, this.y, this.width, this.height);
        }
    }


    const arr = [];
 

    function createDot() {
      /*   ctx.clearRect(0, 0, windowWidth, windowHeight);*/
        ctx.fillStyle = 'black';
        ctx.fillRect(0, 0, windowWidth, windowHeight); 
        if(arr.length<100){
            for (let j = 0; j < 5; j++) {
            arr.push(new newDotInfo());
            
           }
        }
        console.log(arr.length);
        for (let i = 0; i < arr.length; i++) {
            arr[i].move();
            if(arr[i].x>windowWidth || arr[i].x<0 || arr[i].y<0 || arr[i].y>windowHeight){
                  arr.splice(i,1);
            }
        }
       /*  window.requestAnimationFrame(createDot); */
       window.requestAnimationFrame(createDot);
     
       
    }
    createDot();
  /*   setInterval(() => {
        createDot();
      }, 60); */
</script>
``````



### 碰撞运动

``````html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        body {
            height: 100vh;
            background-color: #ccc;
        }
    </style>
</head>

<body>
    <canvas id="canvas"></canvas>
</body>

</html>
<script>
    const canvas = document.querySelector("#canvas");
    const ctx = canvas.getContext('2d');

    canvas.width = "500";
    canvas.height = "500";
    let mx = 2,
        my = 5,
        rx = 50,
        ry=50;
    ctx.fillStyle = "white";
    ctx.fillRect(0, 0, 500, 500);
    ctx.beginPath();
    ctx.fillStyle = "red";
    ctx.arc(rx, ry, 50, 0, 2 * Math.PI);
    ctx.fill();
  

    function move() {
        ctx.fillStyle = "white";
        ctx.fillRect(0, 0, 500, 500);
        ctx.beginPath();
        ctx.fillStyle = "red";
        
        if(rx<50 || rx>450){
             mx*=-1;
        }
        if(ry<50 || ry>450){
            my*=-1;
        }
        rx+=mx;
        ry+=my;
        ctx.arc(rx, ry, 50, 0, 2 * Math.PI);
        ctx.fill();
        window.requestAnimationFrame(move);
    }
    window.requestAnimationFrame(move);
</script>
``````

