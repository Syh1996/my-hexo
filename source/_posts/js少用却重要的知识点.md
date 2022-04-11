---
title: js少用却重要的知识点
date: 2021-06-06 15:21:20
tags: js

---

#### primise问题

```````js
 const promise = new Promise((resolve,reject)=>{
               setTimeout(()=>{
                     resolve({msg:'我是promise异步数据'});
               },2000);
        })
```````

promise 2秒后将得到resolve的数据,且promise会返回resolve的数据



#### window.addEventLister问题

window.addEventLister不能阻止冒泡

#### 异常捕获问题

try...catch不能去捕获异步代码，如promise/setTimeout

``````js
 try{
        setTimeout(()=>{coslso.log('a');},2000)  
       }catch(e){
           console.log(e);
       }
``````

如需解决promise监听异常问题，可使用window.addEventListener('unhandledrejection'异常监听

``````js
try {
       /*  setTimeout(() => { coslso.log('a'); }, 2000) */
       Promise.reject('错误')
    } catch (e) {
        console.log('进入catch')
        console.log(e);
    }
    window.addEventListener('unhandledrejection', function (e) {
        console.log('进入unhandledrejection');
        console.log(e);
    })

``````

js代码捕获

``````
通过window.addEventListener来实现，网络异常可以在事件捕获的阶段捕捉到，addEventListener第三个参数设置为true
2⃣️ 代码必须放在文档载入之前
3⃣️ 通过e.srcElement来判断网络错误事件发生的元素
``````

``````js
        window.addEventListener("error",function(e){
           console.log(e.target.tagName);
            if(e.target.tagName && e.target.tagName.toUpperCase() ==='IMG'){
                console.log('给img设置默认');
                e.srcElement.src="https://syh1996.github.io/images/logo.jpg"
            }
        },true)
``````

上述代码将，捕获元素资源和代码报错，例如图片资源为404未找到时，将触发错误捕获，将图片地址重置为https://syh1996.github.io/images/logo.jpg

如https://syh1996.github.io/images/logo.jpg继续报错，将继续被window.error捕获，直到图片能加载成功

#### 原生获取样式

``````js
    function getStyle(element,attr){
          if(element.currentStyle){
              return element.currentStyle[attr]
          }else{
              console.log(window.getComputedStyle(element));
              return window.getComputedStyle(element)[attr]
          }
    }
``````

----



#### 关于transition animation动画与js获取的问题

一个div#id{position:absolute;left:0;top：0；transition:all 10s}，问，一个js  document.querySelector("#id").style.left= ‘1500px'后，开一个定时器，每秒获取#id的style.left，10秒触发10次，这10次会输出什么

``````css
<style>
      *{
          margin:0;
          padding: 0;
      }
      #id{
          width: 100px;
          height: 100px;
          background: red;
          position: absolute;
          left: 0;
          top: 100px;
          transition: all 20s;
      }
</style>
``````

``````html
<body>
    <button id="btn1">运动</button>
    <div id="id"></div>
</body>
</html>
<script>
      var oBtn = document.querySelector("#btn1");
      var oDiv = document.querySelector("#id");

      function getStyle(element,attr){
          if(element.getCurrentStyle){
              return element.getCurrentStyle[attr]
          }else{
               return window.getComputedStyle(element)[attr]
          }
      }
      oBtn.onclick=function(){
          oDiv.style.left="1000px";
          setInterval(()=>{
                console.log(getStyle(oDiv,'left')); //1px,2px,3px....
                console.log(oDiv.style.left); //1000px,1000px,100px
          },500);
      }
</script>
``````

![图片](https://gitee.com/xian_hong_xiao/assets/raw/master/img/100.png)



 getComputedStyle是获取元素最终呈现的样式，style只能拿到行内style属性设置的属性，对于类似于的空标签，style拿不到属性，将返回空字符串。 

所以上述代码当是  元素.style.left时，会出现打印同一个数，为1000px，而getComputedStyle会打印最后呈现的结果当前位置





### 关于window.addEventListenner的第三个参数

``````js
  const bool = {
        once:true,
        capture:true,
        passive:true
  };
  document.body.addEventListener("click",function(e){
      console.log('body is click');
      e.preventDefault();
  },bool)

  document.querySelector("#box").addEventListener("click",function(){
      console.log('box is click')
  },bool)

  document.querySelector("#btn").addEventListener(
    "click",
    function () {
      console.log("btn is click");
    },
    bool
  );
``````

主要关注下第三个参数，可以设置为bool类型（useCapture）或者object类型(options)。

- options包括三个布尔值选项：
  - capture: 默认值为false（即 使用事件冒泡）**btn>box>body>window**. true是否使用事件捕获**window>body>box>btn**；
  - once: 默认值为false. 是否只调用一次，if true，会在调用后自动销毁listener
  - passive: if true, 意味着listener不能调用preventDefault方法，如果又确实调用了的话，浏览器只会console一个warning，而不会真的去执行preventDefault方法。**根据规范，默认值为false. 但是chrome, Firefox等浏览器为了保证滚动时的性能，在document-level nodes(Window, Document, Document.body)上针对touchstart和touchmove事件将passive默认值改为了true**， 保证了在页面滚动时不会因为自定义事件中调用了preventDefault而阻塞页面渲染。
- useCapture: 默认值为false（即 使用事件冒泡），true事件捕获。





### 关于vue methods写法

``````js
methods:{
    show: function(){
        console.log("显示内容")
    }
}

//vue防抖函数
show:(()=>{
     let timer1 = null;
     return function(fn){
          if(timer1){
               clearTimeout(timer1);
               timer1 = null;
          }
         timer1= setTimeout(()=>{
             fn();
         },1000)
     }
})()
``````



### 文件流转base64图片

``````js
初始化一个FileReader 对象
*/
const reader= new  FileReader()
/*
开始读取指定的Blob中的内容。一旦完成，result属性中将包含一个data: URL格式的字符串以表示所读取文件的内容。
*/
reader.readAsDataURL(file)
/*
在读取后，触发的事件，并获取读取文件的地址
*/
reader.onload = (e) => {
       img.src = this.result
}
``````



### base64转文件流

``````js
const img = new Image();
img.src = base64;
const fd = new formData();
fd.appendChild('file',fd);
``````



### .bind的用法 

.bind除了拥有修改this指针外,还能将额外的参数与原生的事件参数合并一起传入到形参

``````js
  var obj = {
      name:'obj name'
      }
  
  function fn(...arg){
    console.log(this); //obj
    console.log(arg);  //['shao.yuhong',mouseEvent]
  }
  document.querySelector("h1").onclick=fn.bind(obj,'shao.yuhong');
``````



### js模版字符串原生onclick传递对象引用数据

onclick时，原生js不能直接传递对象数据，如果直接传递obj,log函数中收到的是[object Object]字符串。

处理方法需要把obj通过JSON.stringify()将对象转为对象字符串。

因为obj存在单双引号，所以onclick=''遵循里面双引号，外面单引号。当里面单双引号都存在的情况。考虑使用转义字符 **\ '** 对他进行处理

``````js
<script>
  const obj = {
    name:"shao.yuhong",
    age:22,
    school:'重庆师范大学'
    }
    
  function log(item){
    console.log(item);
    }
  document.body.innerHTML= `<h1>This is heading 1</h1>
<h2 onclick=\"log(${JSON.stringify(obj)})\">This is heading 2</h2>
<h3>This is heading 3</h3>
<h4>This is heading 4</h4>
<h5>This is heading 5</h5>
<h6>This is heading 6</h6>`
</script>
``````





#### 后台返回文件流，前端下载、并展示接收进度

``````js
<script>
    var xhr = new XMLHttpRequest();
    xhr.open('GET', 'http://www.shaoyuhong.cn/download.php');
    xhr.responseType = "blob"; //很关键
    xhr.onprogress = function (event) {
        if (event.lengthComputable) {
            console.log(event.loaded / event.total * 100+"%");
         
        
        }
    };
    xhr.onload = function (oEvent) {

        if (xhr.readyState === 4 && xhr.status === 200) {
            // var name = xhr.getResponseHeader("Content-disposition");
            // var filename = name.substring(20,name.length);
            var blob = new Blob([xhr.response],{type:'image/jpeg'});
            var csvUrl = URL.createObjectURL(blob);
            var link = document.createElement('a');
            link.href = csvUrl;
            link.download = 'abababababba';
            link.click();
        }
    }
    xhr.send();
</script>
``````



###  window.open模拟授权效果

``````js
    const oBtn = document.querySelector("button");
    oBtn.onclick=function(){
         const windowView = window.open('./20.html','_blank','width=200;height=200');
       /*   windowView.document.write('<h1>我是一个授权操作</h1>'); */
     
         setTimeout(()=>{
             windowView.close();
         },5000);
    }
// window.open('http://www.baidu.com')  也能实现定时关闭
``````

