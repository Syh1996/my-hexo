---
title: svg那些事
date: 2021-02-21 12:29:34
tags: svg

---

####  animate

下面的例子将圆的cx属性作为动画。为了实现这种效果，我们添加了一个<animate>元素到<circle>元素的内部。<animate> 比较重要的属性如下：

- attributeName 
  需要动画的属性名称
- from 
  属性的初始值
- to 
  终止值
- dur 
  动画的时间

如果你想要让该元素的更多属性具有动画效果，只要添加更多的<animate> 元素到该元素内部即可。

```html
<svg xmlns="http://www.w3.org/2000/svg" width="300px" height="100px">
    <rect x="0" y="0" width="300" height="100" stroke="black" stroke-width="1" />
    <circle cx="0" cy="50" r="15" fill="blue" stroke="black" stroke-width="1">
        <animate attributeName="cx" from="0" to="100" dur="5s" repeatCount="indefinite" />
    </circle>
</svg>123456
```

![这里写图片描述](https://gitee.com/xian_hong_xiao/assets/raw/master/img/20161209112154855.gif)

#### animateTransform

<animateTransform>元素可以执行变换属性的动画。这个新的元素是必要的，因为我们不能用一个简单的数值的属性就像x来制作这种动画。旋转属性就像：rotation(theta, x, y)，theta是一个角度，x和y是绝对坐标。在下面这个例子中我们绕着旋转中心旋转一定的角度。

```
<svg xmlns="http://www.w3.org/2000/svg" width="300px" height="200px">
    <rect x="0" y="0" width="200" height="200" fill="yellow" stroke="red" stroke-width="1" />
    <rect x="20" y="50" width="15" height="34" fill="blue" stroke="black" stroke-width="1" transform="rotation">
        <!-- Rotate from 0 to 360 degrees, and move from 60 to 100 in the x direction. -->
        <!-- Keep doing this until the drawing no longer exists. -->
        <animateTransform attributeType="XML" attributeName="transform" begin="0s" dur="5s" type="rotate" from="20 60 60" to="360 100 60" repeatCount="indefinite" />
    </rect>
</svg>12345678
```

![这里写图片描述](https://gitee.com/xian_hong_xiao/assets/raw/master/img/20161222085404804.gif)

#### animateMotion

<animateMotion>元素让你可以实现一个路径动画，并且根据路径进行旋转。路径使用和<path>相同的方式进行定义。你可以设置属性来定义对象是否根据路径的正切角度来旋转。

#### Example 1: Linear motion

在这个例子中，一个蓝色的圆在黑盒的左右边缘之间来回的反弹，无限地重复着同样的动作。该动画是由<animateMotion>元素控制的。在这种情况下我们建立了一个路径，由MoveTo命令来创建动画的起始点，然后Horizontal-line命令来将圆向右移动300像素到右边，接着使用Z命令，关闭路径，建立一个环回路径。通过设置repeatCount属性为indefinite，我们可以指定只要SVG图片存在的话，动画是否永久循环。

```
<svg xmlns="http://www.w3.org/2000/svg" width="300px" height="100px">
    <rect x="0" y="0" width="300" height="100" fill="yellow" stroke-width="1" stroke="red" />
    <circle cx="0" cy="50" r="15" fill="blue" stroke="black" stroke-width="1">
        <animateMotion path="M 0 0 H 300 Z" dur="3s" repeatCount="indefinite" />
    </circle>
</svg>123456
```

![这里写图片描述](https://gitee.com/xian_hong_xiao/assets/raw/master/img/20161209110812518.gif)

#### Example 2: Curved motion

和上面差不多的例子，只不过现在是沿着曲线和路径方向运动。

```
<svg width="300px" height="100px">
    <rect x="0" y="0" width="300" height="100" fill="yellow" stroke-width="1" stroke="red" />
    <rect x="0" y="0" width="20" height="20" fill="blue" stroke="black" stroke-width="1">
        <animateMotion path="M 250,80 H 50 Q 30,80 30,50 Q 30,20 50,20 H 250 Q 280,20,280,50 Q 280,80,250,80Z" dur="3s" repeatCount="indefinite" rotate="auto">
    </rect>
</svg>123456
```

![这里写图片描述](https://gitee.com/xian_hong_xiao/assets/raw/master/img/20161209110427592.gif)

<animateMotion>使元素沿着动作路径移动，路径也可以 **path元素的id的路径** 组成

``````html
<symbol>
   <path d="xxxxxxx" id="path1"></path>
</symbol>
<rect x="0" y="0" width="100" height="100" fill="red">
   <animateMotion dur="8s">
       <mpath xlink:href="#path1"/>
    </animateMotion>
</rect>
``````

> 请注意三者之间的区别
>
> animate 主要用于控制属性的变换,例如cx,cy ,r
>
> animateTransform 主要用于控制位移/倾斜/放大,此时attrbuteName="transform"  transform必须是这个名字，其它名字不起作用
>
> animateMotion  主要用于控制路径运动



> 额外参数说明
>
> repeatCount="indefinite"   动画重复,可以是数字N,当是N时，将执行N次



> 注意事项
>
> svg动画animate/animateTransform/animateMotion可以同时存在多个,例下



``````html
<symbol>
   <path d="xxxxxxx" id="path1"></path>
</symbol>
<rect x="0" y="0" width="100" height="100" fill="red">
   <animateMotion dur="8s">
       <mpath xlink:href="#path1"/>
    </animateMotion>
    <animate dur="8s" attributeName="x" from="0" to="100"></animate>
</rect>
``````





#### 线性动画

**第一：概念解释**

**1. stroke**意思是：画短线于，在...上划线

**2. stroke-dasharray**:用于创建虚线，之所以后面跟的是array的，是因为值其实是数组。请看下面解释

```
stroke-dasharray = '10'
stroke-dasharray = '10, 5'
stroke-dasharray = '20, 10, 5'
```

stroke-dasharray为一个参数时： 其实是表示虚线长度和每段虚线之间的间距

　　如：stroke-dasharray = '10' 表示：虚线长10，间距10，然后重复 虚线长10，间距10

两个参数或者多个参数时：一个表示长度，一个表示间距
　　如：stroke-dasharray = '10, 5' 表示：虚线长10，间距5，然后重复 虚线长10，间距5
　　如：stroke-dasharray = '20, 10, 5' 表示：虚线长20，间距10，虚线长5，接着是间距20，虚线10，间距5，之后开始如此循环

 

**3. stroke-dashoffset： offset：偏移的意思。**
这个属性是相对于起始点的偏移，正数偏移x值的时候，相当于往左移动了x个长度单位，负数偏移x的时候，相当于往右移动了x个长度单位。
需要注意的是，不管偏移的方向是哪边，要记得dasharray 是循环的，也就是 虚线-间隔-虚线-间隔。
这个属性要搭配stroke-dashoffset才能看得出来效果，非虚线的话，是无法看出偏移的。


概念有点抽象，来看一个MDN的例子，图中红线段是偏移的距离

![img](https://gitee.com/xian_hong_xiao/assets/raw/master/img/1312697-20190618114434344-1494379511.png)

 

上图效果分别是：
1.没有虚线
2.stroke-dasharray="3 1" ，虚线没有设置偏移，也就是stroke-dashoffset值为0
3.stroke-dashoffset="3"，偏移正数，虚线整体左移了3个单位，图中3后面的红线段，就是起始线段，线段之后是1个单位的间隔，我们可见区域从这个间隔开始，然后循环 3-1,3-1的虚线-间隔-虚线-间隔
4.stroke-dashoffset="-3"，偏移负数，虚线整体右移动了3个单位，由于dasharray 是循环的，前面偏移的位置会有dasharray 填充上
5.stroke-dashoffset="1"，偏移正数，虚线整体左移了1个单位，最终呈现出来的效果跟 线段4 一样


**圆形环绕一圈效果**

鼠标hover的时候，外层线段绕自身一圈。这个动画的实现原理，跟上面的是一毛一样的
设置stroke-dasharray虚线长度等于当前圆的周长，间隔大于或者等于圆的周长

![img](https://gitee.com/xian_hong_xiao/assets/raw/master/img/1312697-20190618115412705-1014505699.gif)



第一步：先画出一个圆圈：代码如下，圆的半径设置为50

```
<svg  width="200" height="200" viewBox="0 0 200 200">
   <circle id="circle" cx="100" cy="80" r="50"  fill="gray" stroke-width="5" stroke="green" />
</svg>
```

![img](https://gitee.com/xian_hong_xiao/assets/raw/master/img/1312697-20190618115639809-1926849090.png)


第二步：设置圆的stroke-dasharray和stroke-dashoffset，为圆的周长

```
#circle{
     transition: all 2s;
     stroke-dasharray:314,314;
     stroke-dashoffset:314;
 }
```

![img](https://gitee.com/xian_hong_xiao/assets/raw/master/img/1312697-20190618115936577-653191996.png)

 

第三步：hover的时候，设置stroke-dashoffset为0，注意圆的stroke起始位置为右侧中间

```
svg:hover #circle{
    stroke-dashoffset:0;
}
```

圆形环绕的效果就做好了~



### SVG复用



#### <defs>与<symbol>的相同点

- <defs>元素用于预定义一个元素使其能够在SVG图像中重复使用。
- <symbol>元素用于定义可重复使用的符号。
- 嵌入在<defs>或<symbol>元素中的图形是不会被直接显示的，除非你使用<use>元素来引用它。



#### <defs>与<symbol>的不同点

- xlink定义了一套标准的在 XML 文档中创建超级链接的方法，可以用它来引用<symbol>元素或<defs>内定义的元素和组。
- 一个<symbol>元素可以有preserveAspectRatio和viewBox属性。而<g>元素不能拥有这些属性。

因此相比于在<defs>元素中使用<g>的方式来复用图形，使用<symbol>元素也许是一个更好的选择。



<use x="" y="" transform=""> 复用时x,y是偏移值,transform可以是位置，倾斜，旋转

``````javascript
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="300" height="400">
    <defs>
        <g id="ShapeGroup">
            <rect x="50" y="50" width="100" height="100" fill="#69C" stroke="red" stroke-width="2"/>
            <circle cx="100" cy="100" r="40" stroke="#00f" fill="none" stroke-width="5"/>
        </g>
    </defs>
    <use xlink:href="#ShapeGroup" transform="translate(-10,0) scale(0.5)"/>
    <use xlink:href="#ShapeGroup" transform="translate(10,10) scale(1)"/>
    <use xlink:href="#ShapeGroup" transform="translate(50,60) scale(1.5)"/>
</svg>
``````



#### 参考实例

``````html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
         svg{
             width:100vw;
             height:100vh;


         }
       #use1{
           stroke: #fdd663;
           stroke-dasharray: 10 10;
           stroke-dashoffset: 0;
           animation: ani1 15s infinite  linear;
       }
       #use2{
           stroke: #ee675c;
           stroke-dasharray: 10 10;
           stroke-dashoffset: 0;
           animation: ani2 15s infinite linear;
       }
       #use3{
           stroke: #81c995;
           stroke-dasharray: 10 10;
           stroke-dashoffset: 0;
           animation: ani3 15s infinite linear;
        
       }
       @keyframes ani1{
           0%,100%{
            stroke-dasharray: 50 80;
           stroke-dashoffset: 500;
           }
         
          50%{
            stroke-dasharray: 50 80;
           stroke-dashoffset: 0;
           }
       }


       @keyframes ani2{
           0%,100%{
            stroke-dasharray: 100 120;
           stroke-dashoffset: 500;
           }
          
           60%{
            stroke-dasharray: 100 120;
           stroke-dashoffset: 0;
           }
       }


       @keyframes ani3{
           0%,100%{
            stroke-dasharray: 150 160;
           stroke-dashoffset: 500;
           }
         
           70%{
            stroke-dasharray: 150 160;
           stroke-dashoffset: 0;
           }
           
       }
    </style>
</head>
<body>
    <svg xmlns="http://www.w3.org/2000/svg">
        <symbol id="back">
          <path 
           d="m641.78091,790.55116c33.66658,-0.42299 -32.06272,-1.96304 -43.72177,-2.8119c-97.42147,-2.36241 -178.2728,-99.22185 -167.75035,-195.34463c2.13073,-96.18695 94.77423,-180.68256 191.20004,-171.00335c25.09965,-10.66217 60.72977,24.52486 76.31358,7.71823c40.02758,-70.61028 78.81009,-141.9582 120.45054,-211.62919c35.46917,-5.06262 77.72556,-8.97584 112.22972,0.62002c15.95178,25.12759 -13.05085,31.96845 -31.14866,28.08932c-20.19947,0 -40.39894,0 -60.59845,0c-14.62302,26.67862 -29.62846,53.14464 -44.57028,79.64524c84.86946,0 169.73902,0 254.60848,0c1.88991,-9.02791 23.32755,-28.4795 4.32205,-26.36513c-34.52901,-4.35192 -12.92977,-44.25838 12.64019,-32.90578c35.89235,1.31889 73.05347,-3.53818 107.86866,3.65736c18.5978,37.3249 -35.1766,29.3175 -58.22777,29.71312c-17.06225,-5.56892 -20.59815,11.48777 -27.35368,23.15896c-27.39004,28.05648 -10.36541,57.62647 8.04336,84.977c6.75234,12.44232 11.56268,29.71084 27.99389,18.08497c33.50719,-7.36288 70.03627,-7.62992 103.37777,0.97937c69.67034,20.69872 125.53164,85.29464 130.20844,158.46779c4.22013,44.94577 -5.38832,91.27423 -33.36473,127.53277c-32.44851,46.47673 -85.10832,71.92571 -140.50727,77.08815m-43.43384,0.45368c-50.50555,-12.96067 -97.07043,-43.1132 -124.06679,-88.63504c-17.66691,-22.75363 -19.96364,-57.98298 -29.35393,-80.80756c-24.7664,-0.32404 -49.53283,-0.64811 -74.29929,-0.97213c-50.16146,-80.69484 -94.37449,-165.08841 -142.7612,-246.90817c-9.43189,28.01069 -63.052,66.11946 -33.44545,91.90377c74.30256,62.34548 84.19744,184.12325 20.64528,257.53107c-29.12447,32.64847 -69.52634,55.1472 -111.05801,67.86512m3.19001,-44.39426c80.14518,-17.45284 131.35959,-109.86251 103.63829,-186.93172c-8.56705,-25.1009 -32.13926,-65.19724 -52.95897,-65.60816c-22.19314,38.3176 -42.15846,78.00715 -66.48161,115.04078c-53.41137,9.39497 -4.47703,-55.63874 6.82448,-77.73755c12.11678,-26.96262 56.11764,-70.57906 0.68807,-69.93326c-38.91343,-3.87236 -80.26776,-0.12761 -111.7472,25.70082c-67.55668,44.70576 -80.34302,146.95748 -29.18278,208.59638c34.63405,44.90491 94.44332,63.99612 149.21969,50.87273l0.00003,-0.00003zm560.74527,-0.01111c68.4067,-16.07246 117.63305,-86.37989 108.77861,-156.15879c-4.68298,-77.54732 -82.0197,-140.26335 -158.90589,-129.84036c-57.61424,-5.45274 -2.4553,47.43981 5.77997,73.15358c7.83463,27.30336 50.99384,60.83818 26.89819,87.46467c-54.04397,2.26405 -109.41302,-1.5899 -163.30859,3.85947c9.68005,71.46638 77.31359,130.58443 150.29498,125.11564c10.24195,-0.08353 20.53325,-0.94999 30.46277,-3.59421l-0.00003,0zm-220.78109,-159.71467c6.50943,-55.91724 38.20528,-106.78138 84.43086,-138.60756c2.73105,-13.71648 -25.25894,-76.89265 -35.50779,-32.84775c-31.63458,57.50046 -63.43641,114.92385 -94.10164,172.9456c14.69042,0.35532 31.15827,2.92043 45.17857,-1.49029zm165.12836,-2.77332c-18.23577,-34.28188 -36.87647,-68.35803 -55.93684,-102.1883c-38.23572,20.16323 -63.38996,62.83399 -69.33606,105.42466c41.74855,-0.88994 84.19744,2.18142 125.38156,-2.22409l-0.1085,-1.0123l0,0l-0.00016,0.00003zm-178.84621,-123.65431c17.98509,-33.19417 36.31151,-66.21344 53.74109,-99.70241c-75.78914,-1.69903 -151.75025,-2.0712 -227.5138,0.3591c38.91549,66.88235 74.07351,136.35541 116.62381,200.88702c20.57455,-31.64719 38.04868,-68.08253 57.14889,-101.54368l0,-0.00003z" 
           stroke-width="2" fill="transparent" />
        </symbol>
        <use href="#back" id="use1"></use>
        <use href="#back" id="use2"></use>
        <use href="#back" id="use3"></use>
    </svg>
</body>
</html>
``````

![图片](https://gitee.com/xian_hong_xiao/assets/raw/master/img/2.PNG)



### 异常问题

* svg作为背景图片时使用background-size:100% 100%;不起作用？

​           打开svg图片，然后在svg标签上面加上属性：preserveAspectRatio="none meet";