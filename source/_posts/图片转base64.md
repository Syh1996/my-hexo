---
title: 图片转base64
date: 2021-02-21 12:02:14
tags: js
---



``````javascript
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>

</body>

</html>
<script>
    function createCanvas() {
        var canvas = document.createElement("canvas");
        canvas.width = 100;
        canvas.height = 100;
        canvas.style.border = "1px solid red";
        document.body.appendChild(canvas);
    }

    function setCanvasBg() {
        var canvas = document.querySelector("canvas");
        var ctx = canvas.getContext("2d");
        ctx.drawImage(image, 0, 0, 100, 100);
        ctx.lineWidth = 1;
        ctx.lineCap = "round";
        ctx.strokeStyle = 'red';
        ctx.moveTo(20, 80);
        ctx.lineTo(80, 80);
        ctx.stroke();
        ctx.fillStyle = '#fff';
        ctx.font = "16px Georgia";
        ctx.textAlign = "center";
        ctx.fillText("shao", 50, 95);
    }

    function toBase64() {
        var canvas = document.querySelector("canvas");
        var base64 = canvas.toDataURL("image/jpeg");
        return base64;
    }
    var image = new Image();
    image.src = "./u=1610542142,718703573&fm=26&gp=0.jpg";
    image.setAttribute('crossOrigin', 'anonymous');
    image.onload = function () {
        createCanvas();
        setCanvasBg();
        document.body.style.backgroundImage = `url(${toBase64()})`;
        document.querySelector("canvas").remove();
    }
</script>
``````

