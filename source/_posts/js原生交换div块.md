---
title: js原生交换div块
date: 2021-06-06 15:46:02
tags: js
---

### js原生实现div交换顺序动画参考代码 

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
            padding:0;
        }
      #ul{
          width:1000px;
          border:1px solid red;
          margin:0 auto;
          position: relative;
      }
      ul,li{
          list-style-type: none;
      }
       #ul>li{
           width:200px;
           height:200px;
           position: absolute;
           font-size:60px;
           text-align: center;
           line-height: 200px;
           color:#fff;
           cursor: pointer;
           user-select: none;
           transition: all .5s cubic-bezier(0.395, 1.650, 0.525, 0.745);
       }

    </style>
</head>
<body>
    <ul id="ul">
     
    </ul>
</body>
</html>

<script>
     function createLi(){
        var oUl = document.querySelector("#ul");
        for(let i = 1;i<21;i++){
            let tag = document.createElement("li");
            let text = document.createTextNode(i);
            tag.draggable=true;
            tag.appendChild(text);
            oUl.appendChild(tag);
        }
     }
     function computedLiPostion(){
        var oUl = document.querySelector("#ul");
        var oLi = oUl.getElementsByTagName("li");
        oUl.style.height = Math.ceil(oLi.length / 5) * oLi[0].offsetHeight+1 + 'px';
        var left =0,top=0;
        for(var i=0,length = oLi.length;i<length;i++){
            const bg = `rgb(${parseInt(Math.random() * 256)},${parseInt(Math.random() * 256)},${parseInt(Math.random() * 256)})`;
            oLi[i].style.cssText= `;left:${left}px;top:${top}px;background-color:${bg}`;
              left=left+oLi[0].offsetWidth;
              if(left >=1000){
                  left=0;
                  top+=oLi[0].offsetHeight;
              }
              
        }
     }
    function getStyle(element,attr){
          if(element.currentStyle){
              return element.currentStyle[attr]
          }else{
              console.log(window.getComputedStyle(element));
              return window.getComputedStyle(element)[attr]
          }
    }
     function changePosition(){
        var oUl = document.querySelector("#ul");
        var oLi = oUl.getElementsByTagName("li");
        for(var i=0,length=oLi.length;i<length;i++){
            oLi[i].index= i;
            var startIndex=0,endIndex=0;
            oLi[i].ondragstart=function(e){
                 startIndex = this.index;
            }
            oLi[i].ondragend=function(e){
                 e.preventDefault();
            }
            oLi[i].ondragover=function(e){
                e.preventDefault();
            }
            oLi[i].ondrop=function(e){
                 e.preventDefault();
                 endIndex = this.index;
                 for(let j=0,length=oLi.length;j<length;j++){
                     oLi[j].style.zIndex = 1;
                 }
                 let fromX = getStyle(oLi[startIndex],'left');
                 let fromY  = getStyle(oLi[startIndex],'top');
                 let toX = getStyle(oLi[endIndex],'left');
                 let toY = getStyle(oLi[endIndex],'top');
                 oLi[startIndex].style.cssText+=`;z-index:100;left:${toX};top:${toY}`;
                 oLi[endIndex].style.cssText+=`;z-index:100;left:${fromX};top:${fromY}`;
            }
        }
     }
     createLi();
     computedLiPostion();
     changePosition();
</script>
``````



> 上面代码有一个隐藏的问题，当快速拖动时，会出现块与块交换时出现错位的的情况，考虑是使用transition过渡的时候不能取到最终值，经过尝试，放弃直接获取style的方法来交换位置，改用在元素实例上添加自定义属性

### 参考代码如下，解决快速拖动时的错位问题

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
            padding:0;
        }
      #ul{
          width:1000px;
          border:1px solid red;
          margin:0 auto;
          position: relative;
      }
      ul,li{
          list-style-type: none;
      }
       #ul>li{
           width:200px;
           height:200px;
           position: absolute;
           font-size:60px;
           text-align: center;
           line-height: 200px;
           color:#fff;
           cursor: pointer;
           user-select: none;
           transition: all .5s cubic-bezier(0.395, 1.650, 0.525, 0.745);
       }

    </style>
</head>
<body>
    <ul id="ul">
     
    </ul>
</body>
</html>

<script>
     function createLi(){
        var oUl = document.querySelector("#ul");
        for(let i = 1;i<21;i++){
            let tag = document.createElement("li");
            let text = document.createTextNode(i);
            tag.draggable=true;
            tag.appendChild(text);
            oUl.appendChild(tag);
        }
     }
     function computedLiPostion(){
        var oUl = document.querySelector("#ul");
        var oLi = oUl.getElementsByTagName("li");
        oUl.style.height = Math.ceil(oLi.length / 5) * oLi[0].offsetHeight+1 + 'px';
        var left =0,top=0;
        for(var i=0,length = oLi.length;i<length;i++){
            const bg = `rgb(${parseInt(Math.random() * 256)},${parseInt(Math.random() * 256)},${parseInt(Math.random() * 256)})`;
            oLi[i].style.cssText= `;left:${left}px;top:${top}px;background-color:${bg}`;
              left=left+oLi[0].offsetWidth;
              if(left >=1000){
                  left=0;
                  top+=oLi[0].offsetHeight;
              }
              
        }
     }
    function getStyle(element,attr){
          if(element.currentStyle){
              return element.currentStyle[attr]
          }else{
 
              return window.getComputedStyle(element)[attr]
          }
    }
     function changePosition(){
        var oUl = document.querySelector("#ul");
        var oLi = oUl.getElementsByTagName("li");
        for(var i=0,length=oLi.length;i<length;i++){
            oLi[i].index= i;
            oLi[i].x = getStyle(oLi[i],'left');
            oLi[i].y = getStyle(oLi[i],'top');
            var startIndex=0,endIndex=0;
            oLi[i].ondragstart=function(e){
                 startIndex = this.index;
            }
            oLi[i].ondragend=function(e){
                 e.preventDefault();
            }
            oLi[i].ondragover=function(e){
                e.preventDefault();
            }
            oLi[i].ondrop=function(e){
                 e.preventDefault();
                 endIndex = this.index;
                 for(let j=0,length=oLi.length;j<length;j++){
                     oLi[j].style.zIndex = 1;
                 }
                 let fromX =oLi[startIndex].x;
                 let fromY  = oLi[startIndex].y
                 let toX =oLi[endIndex].x;
                 let toY = oLi[endIndex].y;
                 oLi[startIndex].x = toX;
                 oLi[startIndex].y = toY;
                 oLi[endIndex].x = fromX;
                 oLi[endIndex].y = fromY;
                 oLi[startIndex].style.cssText+=`;z-index:100;left:${toX};top:${toY}`;
                 oLi[endIndex].style.cssText+=`;z-index:100;left:${fromX};top:${fromY}`;
            }
        }
     }
     createLi();
     computedLiPostion();
     changePosition();
</script>
``````

