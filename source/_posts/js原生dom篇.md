---
title: js原生dom篇
date: 2021-02-25 21:29:54
tags: javascript
---

[*element*.attributes](https://www.runoob.com/jsref/prop-node-attributes.html) 返回一个元素的属性集合，{onclick:{},type:'xxx'}

[*element*.childNodes](https://www.runoob.com/jsref/prop-node-childnodes.html)返回元素的一个子节点的数组

```html
<body>
	
<p id="demo">单击“按钮”获取有关身体元素的子节点的信息</p>
<button onclick="myFunction()">点我</button>
<script>
function myFunction(){
	var txt="";
	var c=document.body.childNodes;
	console.log(c);
}
</script>
<p><strong>注意:</strong> 空格内元素看作是文本，文本是节点。</p>
</body>

/*
   输出结果
   [p#demo,text,button,text,script,p]
*/
```



* childNodes 属性返回所有的节点，**包括文本节点、注释节点**；

* children 属性只返回元素节点；

[*element*.classList](https://www.runoob.com/jsref/prop-element-classList.html) 返回元素的类名，作为 DOMTokenList 对象。['one','two','one two'] 最后一个结果是合成 的className

[*element*.cloneNode()](https://www.runoob.com/jsref/met-node-clonenode.html) 克隆某个元素,当需要克隆整个ul>li时，需要遍历数据，一个个克隆

```html
<body>
    <ul id="ul">
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
    </ul>
    <ul id="ul2">


    </ul>
</body>


</html>
<script>
    var copyLi = document.querySelector("#ul").children;
    for (let i = 0, length = copyLi.length; i < length; i++) {
        var oli = copyLi[i].cloneNode(true);
        document.querySelector("#ul2").appendChild(oli);
    }
</script>
```

[*element*.contentEditable](https://www.runoob.com/jsref/prop-html-contenteditable.html) 设置或返回元素的内容是否可编辑

[*element*.firstChild](https://www.runoob.com/jsref/prop-node-firstchild.html) 返回元素的第一个子节点

[*element*.lastChild](https://www.runoob.com/jsref/prop-node-lastchild.html) 返回的最后一个子节点

[*element*.getAttribute()](https://www.runoob.com/jsref/met-element-getattribute.html) 返回指定元素的属性值

[*element*.hasAttribute()](https://www.runoob.com/jsref/met-element-hasattribute.html) 如果元素中存在指定的属性返回 true，否则返回false。

```html
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>


<p id="demo">点击按钮查看按钮元件具有一个onclick属性。</p>
<button onclick="myFunction()" class="one">点我</button>
<script>
function myFunction(){
	var btn=document.getElementsByTagName("BUTTON")[0];
	var x=document.getElementById("demo");
	x.innerHTML=btn.hasAttribute("class");
};
</script>
<p>IE 8 及更早版本不支持 hasAttribute 方法。</p>


</body>
</html>
```

[*element*.hasAttributes()](https://www.runoob.com/jsref/met-node-hasattributes.html) 如果元素有任何属性返回true，否则返回false。

[*element*.hasFocus()](https://www.runoob.com/jsref/met-document-hasfocus.html) 返回布尔值，检测文档或元素是否获取焦点

[*element*.insertBefore()](https://www.runoob.com/jsref/met-node-insertbefore.html) 现有的子元素之前插入一个新的子元素

```
父元素.insertBefore(需要插入的元素,XX元素之前)
```



[*element*.isContentEditable](https://www.runoob.com/jsref/prop-html-iscontenteditable.html) 如果元素内容可编辑返回 true，否则返回false

[*element*.isEqualNode()](https://www.runoob.com/jsref/met-node-isequalnode.html) 检查两个元素是否相等

```html
<body>
	
<button onclick="myFunction('myList1','myList2')">比较列表1和列表2</button>
<button onclick="myFunction('myList1','myList3')">比较列表1和列表3</button>
<p id="demo">单击按钮比较两个列表中的<em>第一项</em></p>
列表 1:
<ul id="myList1"><li>Water</li><li>Milk</li></ul>
列表 2:
<ul id="myList2"><li>Coffee</li><li>Tea</li></ul>
列表 3:
<ul id="myList3"><li>Water</li><li>Fire</li></ul>
<script>
function myFunction(x,y){
	var item1=document.getElementById(x).firstChild;
	var item2=document.getElementById(y).firstChild;
	var x=document.getElementById("demo");
	x.innerHTML=item1.isEqualNode(item2);
}
</script>
<p><strong>注意:</strong> Internet Explorer 8及之前版本不支持isEqualNode 方法。</p>
</body>
```

[*element*.nextElementSibling](https://www.runoob.com/jsref/prop-element-nextelementsibling.html) 返回指定元素之后的下一个兄弟元素（相同节点树层中的下一个元素节点）。

```html
<ul>
  <li id="item1">咖啡 (第一项)</li>
  <li id="item2">西瓜 (第二项)</li>
</ul>
<p id="demo"></p>
<script>
function myFunction() {
    var x = document.getElementById("item1").nextElementSibling.innerHTML; 
    document.getElementById("demo").innerHTML = x;
}
</script>
```

[*element*.previousElementSibling](https://www.runoob.com/jsref/prop-element-previouselementsibling.html) 返回指定元素的前一个兄弟元素（相同节点树层中的前一个元素节点）

[*element*.removeAttribute()](https://www.runoob.com/jsref/met-element-removeattribute.html) 从元素中删除指定的属性

[*element*.replaceChild()](https://www.runoob.com/jsref/met-node-replacechild.html) 替换一个子元素。父元素.replaceChild(新元素,旧元素)

```html
<ul id="myList"><li>Coffee</li><li>Tea</li><li><span>Milk</span></li></ul>
<p id="demo">单击按钮替换列表中的第一项。</p>
<button onclick="myFunction()">点我</button>
<script>
function myFunction(){
	var textnode=document.createTextNode("Water");
	var node = document.createElement('li');
	node.appendChild(textnode);
	var item=document.getElementById("myList").children[0];


	document.getElementById("myList").replaceChild(node,item);
}
</script>
```



### js实现拖拽交换位置效果参考

```css
<style>
    *{
        margin:0;
        padding: 0;
    }
    .box{
        width:1200px;
        background-color: #eee;
        margin:0 auto;
    }
    .box:after{
        content:'';
        display: block;
        clear: both;
    }
    .box>div{
        height: 200px;
        width: 300px;
        float: left;
      cursor: pointer;
      user-select: none;
      transition: all .5s;
      position:relative
    }
    .box>div.active:after{
        content: '插入之前';
        display: block;
        width: 50%;
        height: 100%;
        background-color: rgba(0,0,0,.3);
        position: absolute;
        left: 0;
        top: 0;
    }
    .red{
        background-color: red;
    }
    .blue{
        background-color: blue;
    }
    .dark{
        background-color: darkcyan;
    }
    .saddle{
        background-color:saddlebrown
    }


</style>
```



```html
<body>
   <div class="box">
       <div draggable="true" class="red">1</div>
       <div draggable="true" class="blue">2</div>
       <div draggable="true" class="dark">3</div>
       <div draggable="true" class="saddle">4</div>
    </div>
</body>
</html>
<script>
    
      (function(){
           var current = null,index=0;
           var oBox = document.querySelector(".box");
           var oDiv = oBox.getElementsByTagName("div");
           for(let i=0,length = oDiv.length;i<length;i++){
               oDiv[i].ondragstart = function(){
                   current = this;
                   index = i;
               }
              oDiv[i].ondragover = function(){
                 event.preventDefault();
                 if(!this.classList.value.includes('active') && this!==current){
                    this.className +=" active";
                 }
                
              }
              oDiv[i].ondragleave = function(){
                  this.className = this.classList[0];
              }
              oDiv[i].ondrop = function(){
                if(this && current){
                    oBox.insertBefore(current,this);
                    this.className=this.classList[0];
                }
        
              }
           }
      })();
</script>
```