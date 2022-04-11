---
title: iframe的通信
date: 2021-05-25 15:14:31
tags: html

---

``````html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <input type="text" id="text">
    <button id="btn">发送</button>
    <ul id="ul"></ul>
    <hr/>
    <iframe src="./2.html" width="1000" height="400"></iframe>
</body>
</html>
<script>
       var oText = document.querySelector("#text");
       var oBtn = document.querySelector("#btn");
       oBtn.onclick=function(){
            if(oText.value){
                  var oIframe = document.querySelector("iframe");
                  oIframe.contentWindow.postMessage(oText.value);
            }
       }
       window.addEventListener("message",({data})=>{
              var oLi = document.createElement("li");
              var oText = document.createTextNode(data);
              oLi.appendChild(oText);
              document.querySelector("#ul").appendChild(oLi);  
       })
</script>
``````

``````html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h4>收到的消息是</h4>
    <ul id="ul">
      
    </ul>
    <input type="text" id="text">
    <button id="btn">给父级发送消息</button>
</body>
</html>
<script>
    window.addEventListener("message",function({data}){
        var oUl = document.querySelector("#ul");
        var oLi = document.createElement("li");
        var oText = document.createTextNode(data);
        oLi.appendChild(oText);
        oUl.appendChild(oLi);

    })

   var oBtn = document.querySelector("#btn");
   var oText = document.querySelector("#text");
   oBtn.onclick=function(){
         var value = oText.value;
         if(value){
             parent.postMessage(value);
         }
        }
</script>
``````



#### iframe多层嵌套

window.frames[0].frame[0].frame[0].postMessage('值'); //window指向当前层，该代码可以由A层直接到D层

相同,parent.postMessage();可以由子向父传值 

parent.parent.parent.postMesage(); 可以向父级之上的父级传值 

top.postMessage()可以向顶层传值 