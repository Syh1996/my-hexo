---
title: 你不知道的css
date:  2021-05-17 10:51:00
tags: css
---

### flex布局实现一左一右块

``````html
 <style>
       *{
           margin:0;
           padding: 0;
       }
       ul,li{
           list-style: none;
       }
       ul{
           border:1px solid red;
           box-sizing: border-box;
           height: 200px;
           display: flex;
           align-items: center;
       }
       ul>li{
           width: 30px;
           height: 30px;
           background-color: blue;
           color: #fff;
           text-align: center;
           
       }
       ul>li:not(:last-child){
           margin-right: 20px;
       }
   </style>
<ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
    </ul>
``````

　在做一个列表的时候，单个Flex容器内有三个内联的靠右对齐的按钮，效果如图：

![img](https://img2020.cnblogs.com/blog/1480163/202004/1480163-20200420192141968-1161796065.png)

　　而我想让红色按钮靠左，而另外两个蓝色按钮保持靠右

　　**方法A：**

 　为红色按钮单独加上：flex: 1 

　　此效果的原理是：利用flex:1，动态填充宽度

　　**方法B：**

　　这个时候我们可以为红色按钮单独加上：margin-right : auto；

　　效果如图：

 ![img](https://img2020.cnblogs.com/blog/1480163/202004/1480163-20200420192532014-1239259732.png)

　　此效果的原理是：

　　margin-right 不设置的话默认是0，父容器width 定宽之后，margin-right取值为 **auto** ，

　　则自动占据了剩余的全部宽度 ------《css权威指南》

#### css选择前几个元素或者后几个元素

操作前4个元素

``````css
<style type="text/css">
			#nth-test div:nth-child(-n + 4) {
				background-color: red;
			}
		</style>
``````



操作元素3之后的元素

``````css
#nth-test div:nth-child(n + 3) {
	background-color: red;
}
``````



**text-decoration: line-through wavy red; **   //三个参数，*** 1：下划线、删除线； 2：wavy代表波浪线; 3:线条颜色***



#### 给文字加边框

通常，`border` 属性是为 HTML 元素加上边框的不二选择，但这个边框是基于盒子模型，即呈现在元素的四周，无法对盒子内的文字生效。

假设我想让文字带有 1px 的黑色边框，首选 [text-shadow](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FCSS%2Ftext-shadow) ，它能设置多个文字阴影，从视觉上达到边框效果。

HTML 代码如下：

```html
<div class="text">I Like CSS World</div>
复制代码
```

CSS 代码很简单，将 `text-shadow` 的 blur 值设为 `0`，分别向**右下、左上、右上、左下方向偏移 1px**.

```css
.text {
  color: white;
  font-size: 4em;
  text-shadow: 1px 1px 0px black, -1px -1px 0px black, 1px -1px 0px black, -1px
      1px 0px black;
}

```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d5c5826a8794843bf44c41d8df431d2~tplv-k3u1fbpfcp-zoom-1.image)

[CodePen 在线预览地址](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2Fb2d1%2Fpen%2FGRmqqMa)

`text-shadow` 兼容所有现代浏览器，但仔细观察，会发现边缘处有锯齿状，毕竟是模拟实现，效果差强人意。

第二种方法选用 [-webkit-text-stroke](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FCSS%2F-webkit-text-stroke) 属性，字面意思就是为文字加上边框。

它是 `-webkit-text-stroke-width` 和 `-webkit-text-stroke-text` 两个属性的缩写形式。

```css
.text {
  color: white;
  font-size: 4em;
  -webkit-text-stroke: 1px black;
}
复制代码
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/406b8ca74a734c1eb6744aa41575b14f~tplv-k3u1fbpfcp-zoom-1.image)

[CodePen 在线预览地址](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2Fb2d1%2Fpen%2FzYwBBLR)

**`-webkit-text-stroke` 使用简单，效果出色。**

但相信你也注意到了 "webkit"，一般带有前缀的属性兼容性较差，又或非 CSS 官方认定的属性（浏览器厂商自己实现），最好不要在生产环境使用它。

