---
title: js第二弹
date: 2021-12-21 10:23:18
tags: js

---

### textContent和innerText区别

``````html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <style>
        button{
            border:1px solid red;
        }
    </style>
    <div class="contain">
        北京上海广州<span>深圳厦门</span>陕西西安
        <p>台湾香港澳门</p>
    </div>
    <button onclick="myFunction()">我是按钮</button>
    <script>
        function myFunction(){
            console.log(event.type);
        }
        let container = document.querySelector("body");
        console.log("textContent的内容是:",container.textContent);
        console.log("innerText的内容是:",container.innerText);
        console.log("innerHTML的内容是:",container.innerHTML);
    </script>
</body>
</html>
``````



* **通过textContent属性可以获取指定节点的文本，以及该指定节点所包含后代节点中文本内容，也包括`<script>`和`<style>`元素中的内容（这里的不是文本而是CSS样式代码和JavaScript代码）**

  ![图片](https://img-blog.csdnimg.cn/20190108212407282.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MjA3OTQ4,size_16,color_FFFFFF,t_70)

* **通过textContent属性可以获取指定节点的文本，以及该指定节点所包含后代节点中文本内容，也包括`<script>和<style>`元素中的内容（这里的不是文本而是CSS样式代码和JavaScript代码）**

  ![](https://img-blog.csdnimg.cn/20190108212450804.png)

* **通过textContent属性可以获取指定节点的文本，以及该指定节点所包含后代节点中文本内容，也包括`<script>和<style>`元素中的内容（这里的不是文本而是CSS样式代码和JavaScript代码）**

![](https://img-blog.csdnimg.cn/20190109105114269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MjA3OTQ4,size_16,color_FFFFFF,t_70)

### $attrs与$listeners

包含了父作用域中不作为 prop 被识别 (且获取) 的 attribute 绑定 (`class` 和 `style` 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (`class` 和 `style` 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件——在创建高级别的组件时非常有用。

***好理解一点就是  在子组件使用this.$attrs可以接收父组件传到子组件的自定义属性，但是this.$attrs不接收class style html自带的两个属性，并且如果子组件某个属性已经使用了props:['type']接收了某个自定义属性，此时this.$attrs将不再接收被props接收的部分***

**使用v-bind可以快速绑定一个对象**    v-bind="{name:'xxx',age:22}"

``````vue
//App.vue
<template>
  <div id="app">
    <!--  <router-view/> -->
    <h3>this is app.vue</h3>
    <!-- 快速绑定数据的简写 -->
    <Hello v-bind="personInfo" class="className" ></Hello>
      
    <!--  等价于 -->
    <Hello :name="personInfo.name" :age="personInfo.age" :school="personInfo.school" class="className" ></Hello>
      
  </div>
</template>

<script>
import Hello from "./components/hello";
export default {
  name: "App",
  data() {
    return {
       personInfo:{
           name:'shao.yuhong',
           age:22,
           school:'重庆师范大学'
       }
    };
  },
  components: {
    Hello,
  },
};
``````

``````vue
//Hello.vue
 <template>
    <div class="box">
          <hr/>
          <h3>hello.vue</h3>
      	  <!-- 如果需要将参数传到孙组件，可以采用v-bind=$attrs的方式 -->
          <HelloSon v-bind="$attrs"/>
    </div>
</template>

<script>
import HelloSon from './helloSon.vue';
export default {
  name:'hello',
  components:{
      HelloSon
  },
  props:{
       name:{
           type:String,
           default:'xx'
       }
  },
  created(){
       console.log(this.$attrs);  //{age:22,school:'重庆师范大学'}  由于name已经被props接收，所以this.$attrs不能获取name
  }
}
``````



$listeners包含了父作用域中的 (不含 `.native` 修饰器的) `v-on` 事件监听器。它可以通过 `v-on="$listeners"` 传入内部组件——在创建更高层次的组件时非常有用。

***好理解一点就是  在子组件使用this.$listeners可以接收父组件传到子组件的自定义方法，但是this.$listeners不接收js原生native自带的事件，如click.native  focus.native...***

**使用v-on可以快速绑定多个自定义事件** 如v-on="$listeners"

使用场景：A组件引入B组件，B组件引入C组件；现在C组件需要调A组件的方法，或者是给A组件传参

* C组件this.$emit('事件',参数)=>B组件接收后向A组件继续this.$emit()=>A组件接收处理
* B组件使用v-on="$listeners" ，C组件直接调A组件数据

``````vue
<!--App.vue -->
<template>
  <div id="app">
    <!--  <router-view/> -->
    <h3>this is app.vue</h3>
    <Hello  class="className"  v-bind="personInfo" @appLog="appLog" @appBgColorRandom="appBgColorRandom"></Hello>
  </div>
</template>

<script>
import Hello from "./components/hello";
export default {
  name: "App",
  data() {
    return {
       personInfo:{
           name:'shao.yuhong',
           age:22,
           school:'重庆师范大学'
       }
    };
  },
  methods:{
    appLog(){
      console.log('appLog')
    },
    appBgColorRandom(){
       const oApp = document.querySelector("#app");
       const colors = `rgb(${~~(Math.random() * 255)},${~~(Math.random() * 255)},${~~(Math.random() * 255)},0.8)`;
       oApp.style.backgroundColor=colors;
    }
  },
  components: {
    Hello,
  },
};
</script>
``````



``````vue
<!--hello-->
<template>
    <div class="box">
          <hr/>
          <h3>hello.vue</h3>
          <h4>使用$attrs接收的参数{{$attrs}}</h4>
          <!--将A组件的自定义事件通过 v-on传入$listeners-->
          <HelloSon v-bind="$attrs" v-on="$listeners"/>
    </div>
</template>

<script>
import HelloSon from './helloSon.vue';
export default {
  name:'hello',
  components:{
      HelloSon
  },
  props:{
       name:{
           type:String,
           default:'xx'
       }
  },
  created(){
       console.log(this.$attrs);
  }
}
</script>

``````



``````vue
<!--helloSon.vue-->
<template>
  <div class="box">
    <hr />
    <h3>hello_son.vue</h3>
    <button @click="log">打印log</button>
    <button @click="changeBgColor">更改app的背景颜色</button>
  </div>
</template>

<script>
export default {
  name: "hello",
  created() {
    console.log(this.$attrs);
  },
  methods: {
    log() {
      this.$emit("appLog");//此时，C组件=》A组件  直接调
    },
    changeBgColor() {
      this.$emit("appBgColorRandom"); //此时，C组件=》A组件  直接调
    },
  },
};
</script>
``````





### $props

这个比较好理解，即使用prop接收的参数，如果prop里面没有接收，该自定义参数将不会进入$props



### $parent

使用[$parent](https://cn.vuejs.org/v2/api/#vm-parent) ，**子组件可以获取父组件**的data  methods等数据 也可以直接调父组件的方法  参数如下   ***不能获取爷组件的哈***

``````
$attrs: Object
$children: [VueComponent]
$createElement: ƒ (a, b, c, d)
$el: div#app
$listeners: Object
$options: {parent: Vue, _parentVnode: VNode, propsData: undefined, _parentListeners: undefined, _renderChildren: undefined, …}
$parent: Vue {_uid: 0, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: Vue, …}
$refs: {}
$root: Vue {_uid: 0, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: Vue, …}
$scopedSlots: {$stable: true, $key: undefined, $hasNormal: false}
$slots: {}
$vnode: VNode {tag: 'vue-component-4-App', data: {…}, children: undefined, text: undefined, elm: div#app, …}
appBgColorRandom: ƒ ()
appLog: ƒ ()
personInfo: (...)
test: ƒ ()
_c: ƒ (a, b, c, d)
_data: {__ob__: Observer}
_directInactive: false
_events: {}
_hasHookEvent: false
_inactive: null
_isBeingDestroyed: false
_isDestroyed: false
_isMounted: true
_isVue: true
``````



### $data

Vue 实例观察的数据对象。Vue 实例代理了对其 data 对象 property 的访问。

**简单点说，就是通过this.$data你能得到当时data{}数据里面的响应数据，this.$data包含props的响应数据**



### $root

当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。



### [$children](https://cn.vuejs.org/v2/api/#vm-children)

当前实例的直接子组件。**需要注意 `$children` 并不保证顺序，也不是响应式的。**如果你发现自己正在尝试使用 `$children` 来进行数据绑定，考虑使用一个数组配合 `v-for` 来生成子组件，并且使用 Array 作为真正的来源。

> 父组件使用this.$children时，由于加载的顺序，只能在mounted生命周期中this.$children才能获取成功

``````
(2) [VueComponent, VueComponent]
0: VueComponent {_uid: 2, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: VueComponent, …}
1: VueComponent
	$attrs: (...)
	$children: []
	$createElement: ƒ (a, b, c, d)
	$el: div.hello
	$listeners: (...)
	$options: {parent: VueComponent, _parentVnode: VNode, propsData: undefined, _parentListeners: undefined, _renderChildren: 		undefined, …}
	$parent: VueComponent {_uid: 1, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: VueComponent, …}
	$refs: {}
	$root: Vue {_uid: 0, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: Vue, …}
	$scopedSlots: {$stable: true, $key: undefined, $hasNormal: false}
	$slots: {}
	$vnode: VNode {tag: 'vue-component-3-HelloWorld', data: {…}, children: undefined, text: undefined, elm: div.hello, …}
	msg: (...)
	_c: ƒ (a, b, c, d)
	_data: {__ob__: Observer}
	_directInactive: false
	_events: {}
	_hasHookEvent: false
	_inactive: null
	_isBeingDestroyed: false
	_isDestroyed: false
	_isMounted: true
	_isVue: true
	_renderProxy: Proxy {_uid: 4, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: VueComponent, …}
	_routerRoot: Vue {_uid: 0, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: Vue, …}
	_self: VueComponent {_uid: 4, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: VueComponent, …}
	_staticTrees: (2) [VNode, VNode]
``````



### [v-pre](https://cn.vuejs.org/v2/api/#v-pre)

- 跳过这个元素和它的子元素的编译过程。可以用来显示原始 Mustache 标签。跳过大量没有指令的节点会加快编译。

- **示例**：

  ```vue
  <span v-pre>{{ this will not be compiled }}</span>  <!--页面输出{{ this will not be compiled }} --> 
  ```



### flex的问题

主轴不一定就是水平的，主轴取决于弹性布局的方向flex-direction，

row横向）布局时主轴就是水平的那条，同时交叉轴就是换行flex-wrap时产生交叉的那一条

交叉轴为flex-wrap换行时，如果对齐为flex-start，则是flex-start对齐的那条线

如果布局中没有存在换行，就不存在交叉轴

![图片](https://gitee.com/xian_hong_xiao/assets/raw/master/img/11.png)

* **flex：0 0 340px**   = flex-grow:0, flex-shirk:0;**flex-basis**:340px

* align-self:flex-start;  可以对单个纵向对齐设置，参照下图

  ![](https://gitee.com/xian_hong_xiao/assets/raw/master/img/12.png)

* 同理  justify-self也可以对单个左右对齐单独设置

* ** *`flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`***



### XSS

Cross-Site Scripting（跨站脚本攻击）简称 XSS，是一种代码注入攻击。攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。利用这些恶意脚本，攻击者可获取用户的敏感信息如 Cookie、SessionID 等

XSS 的本质是：恶意代码未经过滤，与网站正常的代码混在一起；浏览器无法分辨哪些脚本是可信的，导致恶意脚本被执行。

| **类型**   | **存储区\***            | **插入点\***    |
| ---------- | ----------------------- | --------------- |
| 存储型 XSS | 后端数据库              | HTML            |
| 反射型 XSS | URL                     | HTML            |
| DOM 型 XSS | 后端数据库/前端存储/URL | 前端 JavaScript |

存储型和反射型 XSS 都是在服务端取出恶意代码后，插入到响应 HTML 里的，攻击者刻意编写的“数据”被内嵌到“代码”中，被浏览器所执行。

预防这两种漏洞，有两种常见做法：

- 改成纯前端渲染，把代码和数据分隔开。
- 对 HTML 做充分转义。

#### 纯前端渲染的过程：

1. 浏览器先加载一个静态 HTML，此 HTML 中不包含任何跟业务相关的数据。
2. 然后浏览器执行 HTML 中的 JavaScript。
3. JavaScript 通过 Ajax 加载业务数据，调用 DOM API 更新到页面上。

在纯前端渲染中，我们会明确的告诉浏览器：下面要设置的内容是文本（`.innerText`），还是属性（`.setAttribute`），还是样式（`.style`）等等。浏览器不会被轻易的被欺骗，执行预期外的代码了。



#### 转义 HTML

如果拼接 HTML 是必要的，就需要采用合适的转义库，对 HTML 模板各处插入点进行充分的转义。

在使用 `.innerHTML`、`.outerHTML`、`document.write()` 时要特别小心，不要把不可信的数据作为 HTML 插到页面上

如果用 Vue/React 技术栈，并且不使用 `v-html`/`dangerouslySetInnerHTML` 功能，就在前端 render 阶段避免 `innerHTML`、`outerHTML` 的 XSS 隐患。

对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 XSS 发生，但可以增加 XSS 攻击的难度。

***HTTP-only Cookie***: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。

尽量不要使用 `onLoad="onload('{{data}}')"`、`onClick="go('{{action}}')"` 这种拼接内联事件的写法。在 JavaScript 中通过 `.addEventlistener()` 事件绑定会更安全。



### XSRF

CSRF（Cross-site request forgery跨站**请求**伪造

* 可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有token或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求
* 检查 Referer 来源
* 在http请求头中添加自定义头部



### UDP与TCP区别

* udp是非连接的协议，发送数据前不需要进行TCP三次连接握手，TCP需要进行三次握手连接 
* TCP传输数据可靠，UDP只能尽最大努力交付，但不能保证数据能成功送达
* 由于UDP传输数据前不需要建立连接，所以UDP速度上比TCP更快
*  TCP保证数据顺序，UDP不保证 



### 全双工半双工单工

* 【单工】发送方=》接收方，发送方只能发，接收方只能收，如广播、收音机
* 【半双工】同一时间，发的时候不能收，收的时候不能发，如对讲机
* 【全双工】发送的时候可以收，接收的时候也可以发，如电话 TCP采用的就是这个



### TCP三次握手

![](https://gitee.com/xian_hong_xiao/assets/raw/master/img/tcp3.png)

三次握手：
第一次握手：客户端发送syn包(syn=x)到服务器，并进入SYN_SEND状态，等待服务器确认；
第二次握手：服务器收到syn包，必须确认客户的SYN（ack=x+1），同时自己也发送一个SYN包（syn=y），即SYN+ACK包，此时服务器进入SYN_RECV状态；
第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=y+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。
握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP 连接都将被一直保持下去。
————————————————
版权声明：本文为CSDN博主「ronety」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ronety/article/details/89140053

### TCP四次挥手

![](https://gitee.com/xian_hong_xiao/assets/raw/master/img/tcp4.png)

四次挥手
与建立连接的“三次握手”类似，断开一个TCP连接则需要“四次握手”。
第一次挥手：主动关闭方发送一个FIN，用来关闭主动方到被动关闭方的数据传送，也就是主动关闭方告诉被动关闭方：我已经不 会再给你发数据了(当然，在fin包之前发送出去的数据，如果没有收到对应的ack确认报文，主动关闭方依然会重发这些数据)，但是，此时主动关闭方还可 以接受数据。
第二次挥手：被动关闭方收到FIN包后，发送一个ACK给对方，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号）。
第三次挥手：被动关闭方发送一个FIN，用来关闭被动关闭方到主动关闭方的数据传送，也就是告诉主动关闭方，我的数据也发送完了，不会再给你发数据了。
第四次挥手：主动关闭方收到FIN后，发送一个ACK给被动关闭方，确认序号为收到序号+1，至此，完成四次挥手。
————————————————
版权声明：本文为CSDN博主「ronety」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ronety/article/details/89140053

***【问题1】为什么连接的时候是三次握手，关闭的时候却是四次握手？***
`答：因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，"你发的FIN报文我收到了"。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手`



### 原型与原型链

#### 原型

原型定义了公共的属性和方法，通过原型创建的实例对象，将共享拥有原型上的属性和方法

B是通过Log类创建的，所以B共享拥有了Log类的属性和方法，这个时候Log就是B类的原型, 此时B类的隐式原型===Log的显式原型，即`B.__proto__===Log.protype`

原型链主要解决的是继承问题

在Javascript中如果访问一个对象本身不存在的属性或是方法，就首先在它的原型对象上去寻找，如果原型对象上也不存在，就继续在原型对象的原型对象上去寻找，直到找到为止。那么原型对象有尽头么？所有对象的原型尽头是**Object.prototype**,那么**Object.prototype**这个对象的**__proto__**指向啥呢？答案是`null`。我们日常开发中用到的绝大多数对象的**__proto__**基本不会直接指向**Object.prototype**,基本都是指向另一个对象。比如所有的函数的**__proto__**都会指向**Function.prototype**,所有数组的**__proto__**都会指向**Array.prototype**。

---



简单点来说就是 **如果访问一个对象本身不存在的属性或是方法，就首先在它的原型对象上去寻找，如果原型对象上也不存在，就继续在原型对象的原型对象上去寻找，直到找到为止**

每一个实例对象的创建（除NULL外），都会有一个隐式对象__proto__，每一个函数的创建都会有一个prototype,__proto__指向的原型上的prototype,并继承原型上的属性和方法

---



``````js
    class A {
      constructor(name = 'shao.yuhong',age = 24) {
        (this.name =name), (this.age = age);
      }
      render(){
           console.log('this is render,name='+this.name+',age='+this.age)
      }
    }
    class Log  extends A {
      constructor(name, age) {
        super(name, age);
      }
      log() {
        console.log(this.name + "/" + this.age);
      }
    }

    const B = new Log('liming',30);
    console.log(B.__proto__ === Log.prototype); //true
    B.log();
    B.render();
``````

**B类的数据打印参考**

``````js
Log {name: 'liming', age: 30}
age: 30
name: "liming"
[[Prototype]]: A
	constructor: ƒ Log(name, age)
	log: ƒ log()
	[[Prototype]]: Object
		constructor: ƒ A()
		render: ƒ render()
		[[Prototype]]: Object
``````





### 事件循环机制

event loop分为**宏任务** 和 **微任务**

宏任务：setInterval  、setTimeout 、I/O 、 UI  render

微任务：Object.observe   mutationObserve  promise

事件处理时，会先执行整体代码、遇见微任务会将它加入到微任务队列、遇见宏任务会将宏任务加入宏任务队列。

等整体代码执行完成后，再搜索微任务、最后执行宏任务

需要注意的是  这里面的promise指的是**.then**里面的是微任务方法,promise里面的正常执行

`````js
setTimeout(()=>{
    console.log('d');
},0);
console.log('a');
new Promise(resolve=>{
      console.log('b');
      resolve();
}).then(res=>{
    console.log('c');
});

`````

结果:abcd



### 数据加密方式

Md5  base64  AES  DES  sha1

### vue render函数与component的对比 

参考链接：[URL](https://cn.vuejs.org/v2/guide/render-function.html)

vue中使用render函数创建模板有两个方式，使用`createElement`或者`JSX`语法

* createElement语法   **该语法不适合复杂情况，不作讨论**

  ``````js
  props: ['items'],
  render: function (createElement) {
    if (this.items.length) {
      return createElement('ul', this.items.map(function (item) {
        return createElement('li', item.name)
      }))
    } else {
      return createElement('p', 'No items found.')
    }
  }
  ``````

* jsx语法 **使用react的jsx语法，更简单**     完全与react语法相似

  ``````js
  export default {
      props:['arr'],
      data(){
          return {
              count:100
          }
      },
      methods:{
          countHanle(val,ev){
              console.log(ev);
              this.count+=val;
          }
      },
      render: function (h) {
       
             return (<div>
                   <h1>内容</h1>
                   <ul>
                        {
                            this.arr.map((item)=>{
                               return <li key={item}>{item}</li>
                            })
                        }
                       <li></li>
                   </ul>
                   <hr/>
                   <div>
                        <button class={['button','el-button'].join(' ')} onClick={e=>this.countHanle(-1,e)}>-</button>
                        <span>{this.count}</span>
                        <button  onClick={e=>this.countHanle(1,e)}>+</button>
                   </div>
             </div>);
        }
  }
  ``````



以上render使用时

``````js
import List from './components/list';
<List></List>
``````



### slot

* 使用slot插入的样式，子组件样式高于父组件

* 使用slot插入，默认使用的是父组件里面的值

  ``````js
    <template #h3>
                <h2>这是h1的v-slot:h3 slot---name：{{dataName}}</h2>
     </template>
     data(){
      return {
         dataName:'name is app.vue'
      }
    }
  //dataName ='name is app.vue'
  ``````

* 使用slot插入,如果需要`<template>{{name}}</template`slot里面的值不需要**父组件**而是使用**子组件**的话，需要两个走

  1. 给`<template #h1="作业域名称"></template>` , 注意`#h1`===`v-slot="h1"`,一种简写

  2. 作用域有了后，需要在`<slot name="h1" :data="子组件数据"></slot>`

     ``````html
        <!--父组件 -->
        <template #h2="shao">
                   <h2>这是h1的v-slot:h2 slot---name：{{shao.dataName}}</h2>
        </template>
     
        <!--子组件-->
        <slot name="h2" :dataName="dataName"></slot>
     ``````

参考链接：[https://blog.csdn.net/weixin_45432736/article/details/108552978](https://blog.csdn.net/weixin_45432736/article/details/108552978)

### 普通函数、箭头函数、构造函数的区别

#### 箭头函数

箭头函数是普通函数的简写，可以更优雅的定义一个函数，和普通函数相比，有以下几点差异：

1、函数体内的 this 对象，就是定义时所在的对象，而不是使用时所在的对象。

2、不可以使用 arguments 对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

3、不可以使用 yield 命令，因此箭头函数不能用作 Generator 函数。

4、不可以使用 new 命令，因为：

- 没有自己的 this，无法调用 call，apply。
- 没有 prototype 属性 ，而 new 命令在执行时需要将构造函数的 prototype 赋值给新的对象的 __proto__

#### 普通函数

1. this指向的调用的实例

2. 有arguments

3. 使用普通函数定义时,如果函数没有返回值，打印该函数会是undefind

   `````js
   function fn(){ }
   console.log(fn);//undefind
   `````

   

#### 构造函数

1. 使用普通函数定义时,如果函数没有返回值，打印该函数会是实例
2. this指针指向的是当前构造函数实例

````js
function fn(){}
const fnMethod = new Fn();
console.log(fnMethod); //[object Object]
````



### localstorage sessionStorge cookie session

* localStorage生命周期是永久，这意味着除非用户显示在浏览器提供的UI上清除localStorage信息，否则这些信息将永远存在。sessionStorage生命周期为当前窗口或标签页，一旦窗口或标签页被永久关闭了，那么所有通过sessionStorage存储的数据也就被清空了。
* 在浏览器中，如果A标签页打开了当前项目，localStorage能在B标签页中获取已经存储的数据。而在sessionStorage中，只在当前窗口或者使用js `a`标签触发的跳转页面能获取已经存储的数据
* localStorage和sessionStorage的存储数据大小一般都是：5MB，不同浏览器可能存在一些差异
* localStorage和sessionStorage都保存在客户端，不与服务器进行交互通信。
* cookie的大小被限制在4KB
* 缓存解析上的不同:`localStorage  sessionStorage`直接由浏览器解析。`cookie session`的解析需要通过http发送给服务端，由服务端解析后返回给客户端
* cookie session默认生命周期都是在当前页面被关闭后，与`localStorage  sessionStorage`不同的是，cookie session可以设置过期时间，而`localStorage  sessionStorage`是不能直接设置的，但可以配合存入过期时间，页面进入入口 文件时，判断当前系统时间与过期时间，从而决定是否需要清除`localStorage  sessionStorage`
* 安全性：session的安全性大于cookie。原因是session存储在服务器、而cookie是存储在客户端。在早期前后端未分离时，后端通过session存储登陆状态

### BFC块级上下文

BFC(Block formatting context)直译为"块级格式化上下文",它是一个独立的渲染区域，内部渲染不影响外面

#### 形成BFC的条件

- 1、float:left
- 2、position的值为fixed; absolute;
- 3、display的值是inline-block、table-cell、flex、table-caption或者inline-flex
- 4、overflow的值是hidden/auto/scoll

使用BFC可以直接margin重叠问题

``````html
<html>
<style>
   p{
    margin: 20px 0;
    background-color: red;
    overflow: hidden;
    }
   </style>
<body>

<p>1</p>
<p>2</p>
<div>
<p>3</p>
</div>

<p>请仅仅把标题标签用于标题文本。不要仅仅为了产生粗体文本而使用它们。请使用其它标签或 CSS 代替。</p>

</body>
</html>

``````

##### 效果图

![](https://gitee.com/xian_hong_xiao/assets/raw/master/img/13.png)

上面代码运行后，明显可以发现`p`的margin-top:20px,margin-bottom:20px; `p`与`p`之前应该是40,而实际上只有20，发生了margin重叠; 

现在用BFC的思想，在div上加上`overflow:hidden`后，重叠的问题就解决了,效果如下

![](https://gitee.com/xian_hong_xiao/assets/raw/master/img/14.png)



#### == (~|~) 垂直margin为什么会重叠？

***找到了一个很好的解释：margin的定义不是让元素移动xxpx，而是这个元素的旁边必须有xxxpx的的空白。***

### vue react jquery对比

* `jquery`的思想是操作dom节点实现视图上的更新，这与`vue` `react`是最大的区别,而`vue` `react`思想是**数据驱动视图更新** 并且使用vNode虚拟Dom映射到真实DOM，通过diff算法实现视图上更新
* `jquery`采用大量操作dom的方式，性能上没有vue/react好
* `jquery`主要面向的项目是普通html单个页面，且兼容性好，在考虑IE8的浏览器兼容时，`react` `vue`没有他的优势，因为vue/react最低浏览器要求ie9以上.而vue/react面向的是一个应用型
* 使用vue/react最大的缺点是不利于seo，搜索引擎不能抓取相关数据，原因是页面中的数据是后续加载的。而jquey的单个页面不存在这个问题
* 最后vue /react采用了Vnode虚拟DOM，需要在内存中生成dom树的备份节点，所以会消耗部分内存
* vue通过diff算法，局部更新页面。而react的更新将是整个页面，包含子孙组件。所以react提供了shouldComponentUpdate(nextProps,nextState)方法，决定是否需要更新子组件
* vue在扩展上使用minxs混入、react采用的是HOC高阶组件的方式



### HTTP状态码

| 502  | 网关或者代理服务器错误                            |
| ---- | ------------------------------------------------- |
| 500  | 内部服务器错误                                    |
| 404  | 资源不存在                                        |
| 403  | 服务器收到客户的请求，但是拒绝执行                |
| 302  | 资源临时移动，302浏览器下次会依然尝试着请求该资源 |
| 301  | 资源永久移动，301浏览器下次不会再请求该资源 |
| 304  | 资源使用缓存资源 |
| 200  | 请求成功 |



### HTTP缓存机制

缓存机制分为`强缓存`和`协商缓存`

强缓存：不需要通过服务端判断，直接使用本地资源

协商缓存：由服务器判断，资源使用服务器资源还是本地资源

| **响应头**    | (常用)值                                                     | **说明**             |
| ------------- | ------------------------------------------------------------ | -------------------- |
| Cache-Control | **no-cache**, no-store, must-revalidate, **max-age**, public, private | 强制缓存             |
| Last-Modified | 请求的资源最近更新时间                                       | 协商缓存             |
| ETag          | string                                                       | 协商缓存、资源版本号 |

协商缓存时，首次会通过http响应资源时返回资源的同时，设置更新时间或者资源版本号，下次请求该资源时，http请求状况会添加`If-Modified-Since` | `If-None-Match`头部信息，服务器收到该头部信息后，通过比对，判断是否使用缓存资源还是服务器资源

| 请求头            | **值**                          |
| ----------------- | ------------------------------- |
| If-None-Match     | 缓存响应头中的 ETag 值          |
| If-Modified-Since | 缓存响应头中的 Last-Modified 值 |

[参考网站](https://blog.csdn.net/guduyibeizi/article/details/81814577)



### for...in 和 for...of

- forEach和map,针对await不生效；使用break或continue会报错；使用return 无效；

- for循环、for...in,for...of，支持await，for和for...of中可以使用break和continue；for...in会忽略continue和break

  ***for...in循环：主要为遍历对象而设计，不适用于遍历数组。***

  遍历数组缺点：

  - 数组的键名是数字；
  - 不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键；
  - 某些情况下，for...in循环会以任意顺序遍历键名。

   ***for ...of：***

  - es6 中添加的循环遍历语法；
  - ***支持遍历数组，类数组对象（DOM NodeList），字符串，Map 对象，Set 对象，Generator 对象；***
  - ***不支持遍历普通对象***；
  - 遍历后输出的结果为数组元素的值；
  - 可搭配实例方法 entries()，同时输出数组的内容和索引；