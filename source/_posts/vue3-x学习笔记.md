---
title: vue3.x学习笔记
date: 2021-02-21 11:45:25
tags: vue
---

> vue2.x文档https://cn.vuejs.org/v2/guide/components-props.html

> vue3.x文档https://www.vue3js.cn/docs/zh/api/application-config.html#errorhandler

> vite是一个Web开发构建工具，由于其本机ES模块导入方法，因此可以闪电般快速地提供代码。通过在终端中运行以下命令，可以使用Vite快速设置Vue项目。1.npm init vite-app <project-name> 2.npm install

> vue create 项目名

### 注意点 (vue3)

* setup中的this指向是undefind
* beforeCreated中的this指向是proxy  ，vue2中的this是vue实例



### setup坑

* 使用provide与inject时，provide如果需要响应子组件，需要将rective/ref整体数据传，不能是对象里面的数据单独传（不响应),见例子

  `parent.vue`

  ``````js
  <div class="hello">
          <h2><button @click="changeMsg">修改</button>{{msg}}</h2>
          <headerbar></headerbar>
          <contentbar></contentbar>
          <footerbar></footerbar>
      </div>
      
    setup(){
          
          const data = reactive({
              msg:'这是我要传递的信息'
          });
          const methods = {
                changeMsg:function(){
                   data.msg = '我被 修改了'+Math.random()
                }
          }
          provide('data',data);  //子组件会实时更新，响应
          provide('msg',data.msg); //子组件只会收到1次，更新 时不将响应
          provide('changeMsgFn',methods.changeMsg);
          return {
              ...toRefs(data),
              ...methods
          }
      },
  ``````

  `son.vue`

  ``````js
   setup(props) {
      const parentData = inject("data");
      const changeMsgFn = inject("changeMsgFn");
    /*   const data = reactive({
          parentData:inject('data'),
          msg:inject("msg"),
          changeMsg:inject("changeMsgFn")
      });
      console.log(data); */
      return {
         parentData,
         changeMsgFn
      };
    },
  ``````

  ***为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide 值时使用 [ref](https://v3.cn.vuejs.org/guide/reactivity-fundamentals.html#创建独立的响应式值作为-refs) 或 [reactive](https://v3.cn.vuejs.org/guide/reactivity-fundamentals.html#声明响应式状态)。***

  [原文参考:https://v3.cn.vuejs.org/guide/composition-api-provide-inject.html#%E4%BD%BF%E7%94%A8-inject](https://v3.cn.vuejs.org/guide/composition-api-provide-inject.html#%E4%BD%BF%E7%94%A8-inject)

  

> **setup****用法1，使用ref代替vue2中的data+methods**

```javascript
<ol>
    <li v-for="(item,index) in list" 
        :key="index" 
        :class="[activeIndex ===index?'active':'']"
        @click="setIndex(index)">{{item}}</li>
  </ol>

  setup(){
    const list = ref(["test1",'xiaoming','xiaohong','lichen']);
    const activeIndex = ref(-1);
    const setIndex = index=>{
       activeIndex.value = index;
    }
    return {
      list,
      activeIndex,
      setIndex
    }
  }

//使用ref创建 的数据是一个对象对他进行设置时，需要.value

//vue3使用ref创建 的响应数据，也可如vue2中在template直接设置变量值实现响应
 <button @click="msg+='+添加了1'">添加</button>
    setup(){
       let msg = ref("how are you");
       const addMsg = ()=>{
           msg.value +="+添加了"
       }
       return {
           msg,
           addMsg
       }
    }

//vue3使用reactive创建 的响应数据，也可如上，直接在template设置变量的值 实现响应
未使用toRefs: <button @click="data.msg+='|OK'">添加</button>
使用toRefs:<button @click="msg+='|OK'">添加</button>
 
```

```javascript
setup(){
  let count=0;
  let fn = ()=>{
   count++;  
  }
  return {
     count,
     fn,
   }
}
//此处页面将展示一次后，count改变将不会触发render视图更新
```



```javascript
   setup(){
     const obj = {
           xing:'',
           ming:''
     }
     const  user = ref(obj);
     const name = computed(()=>{
         return user.value.xing+"_"+user.value.ming
     });
     return {
         user,
         name
     }
   }
//ref也可以创建响应对象数据，此时用法上几乎与reactive一致，在setup获取栽个数据时，依然使用user.value.xxx.
//ref里面存放的数据是一个对象时，内部将经过reactive处理为proxy对象
```



> **reactive用法**，使用reactive代替ref

```js
 <ol>
    <li v-for="(item,index) in liData.list" 
            :key="index"
            :class="[liData.activeIndex ===index?'active':'']"
           @click="liData.setIndex(index)">{{item}}</li>
  </ol>

  setup(){
       const liData = reactive({
      list:["test1",'xiaoming','xiaohong','lichen'],
      activeIndex:-1,
      setIndex:index=>{
        liData.activeIndex = index;
      }
    });
    return {
      liData
    };
     }
//注意： 使用reactive时，数据方法将在一个对象统一管理，设置访问一个值时需要带顶层liData
//通过测试,liData.activeIndex = index,将liData更换为this,代码报错,此处 不能使用this
```



> 上面代码遍历时每次都需要写liData?使用**toRefs**

官方说法：由于`props`是反应性的，您不能使用ES6解构，因为它会删除道具的反应性。



```javascript
    const liData = reactive({
      list:["test1",'xiaoming','xiaohong','lichen'],
      activeIndex:-1,
      setIndex:index=>{
        liData.activeIndex = index;
      }
    });
    const liData1 = toRefs(liData); //此处变量名不可写toRefs中的变量重名，否则会报错，亲测
    return {
      ...liData1
    };
```



> ref与reactive的区别

ref用于创建简单的响应式数据，reactive用于创建复杂的响应式数据



### vue2与vue3的生命周期

```javascript
选件API	钩在里面 setup
  beforeCreate	    setup
  created	        setup
  beforeMount	    onBeforeMount
  mounted	        onMounted
  beforeUpdate	    onBeforeUpdate
  updated	        onUpdated
  beforeUnmount   	onBeforeUnmount
  unmounted	        onUnmounted
  errorCaptured	    onErrorCaptured
  renderTracked	    onRenderTracked
  renderTriggered	onRenderTriggered

 执行顺序vue3的生命周期在vue2之前
 setup>beforeCreate>created>onBeforeMound>beforeMount>onMounted>mounted......
```



### computed

```javascript
       const num1 = ref(0);
       const num2 = ref(0);
       const sum = computed({
           get:()=>Number(num1.value)+Number(num2.value),
           set:value=>value
       });
//get && set
```



```javascript
       const num1 = ref(0);
       const num2 = ref(0);
       const sum = computed(()=>Number(num1.value)+Number(num2.value));
//简单get时
```



### **watch**

```javascript
// watching a getter
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)

// directly watching a ref
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
```



```javascript
const firstName = ref('');
const lastName= ref('');
const state = reactive({ count:1 });
watch([firstName, lastName,()=>state.count], (newValues, prevValues) => {
  console.log(newValues, prevValues);
})

firstName.value = "John"; // logs: ["John",""] ["", ""]
lastName.value = "Smith"; // logs: ["John", "Smith"] ["John", ""]
```



假定结构如下

``````js
const state = reactive({ count:1,info:{name:'xxx'} });
``````

1. watch(state,(newVal,oldVal)=>{})  **`正常情况  异常行为监听一个object对象时newVal === oldVal`**

2. watch(()=>state.count,(newVal,oldVal)=>{ })    **`单独监听某个值的情况`**

3. watch([()=>state.count,()=>state.info.name],(newVal,oldVal)=>{  });   **`此时newVal= [item1(state.count的值),item2（state.info.name)的值]`**

4. watch(()=>state.info,(newVal,oldVal)=>{  },{deep:true})   **`监听state中的某个对象时，需要手动开启deep:true深度监听，否则无法监听到state.info.name的变化时的回调`**

5. ``````js
   const state = ref({ count:1,info:{name:'xxx'} });
   ``````

   **`假定有如上的特殊情况`**   监听方法为  `watch(()=>state.value.count)` 需要使用.value

   

//注意使用reactive时，需要在监听的对象上构造一次，如使用ref创建 的数据，可直接监听

相关链接: https://v3.vuejs.org/guide/reactivity-computed-watchers.html#watch



### watchEffect

```javascript
const state = reactive({
            num1:0,
            num2:0,
            sum:''
        });    
watchEffect(()=>{
            state.sum = Number(state.num1) +Number(state.num2);
        });
```

watchEffetc添加了第三个配置项immediate:true,deep:true

**`watchEffect比watch更加智能，类似于computed的方式，watchEffect根据里面的依赖值变化而触发回调`**

``````js
   const info = reactive({
       xing:'',
       ming:'',
       name:''
    })   
watchEffect(()=>{
      if(info.ming){
        info.name = info.xing +'-'  + info.ming;
      }else if(info.xing){
         info.name = info.xing
      }else{
        info.name = '';
      }
     
    });
``````



### toRef

toRef可以将reactive某个字段单独提出来与原数据建立共同的映射，即month = person.info.money类似,通过修改toRef的值，可以直接修改reactive

``````js
 const person = reactive({
         name:'xiaoMi',
         info:{
            money:1
         }
    });
    const money = toRef(person.info,'money');
    const addMoney = ()=>{
       /*  person.info.money+=1.5; */
       money.value+=1.5;
      
    }
``````



### toRefs

* toRefs可以将reactive定义的数据解析出来，去掉第一级

  ``````js
  const person = reactive({
           name:'xiaoMi',
           info:{
              money:1
           }
      });
  return {
       ...toRefs(person)
  }
  
  /**
     <template>中可以省略person的重复写
     <template>{{name}}</template>
  /**
  ``````

* toRefs将一个Reactive中的某个Object对象单独提出，与原有的数据关联  即user = person

  ``````js
  const person = reactive({
           name:'xiaoMi',
           info:{
              money:1
           }
      })
  const user = toRefs(person);
   const editName = ()=>{
         user.name.value +='!'
      }
  ``````



### readonly

接受一个对象 (响应式或纯对象) 或 ref 并返回原始对象的只读代理。只读代理是深层的：任何被访问的嵌套 property 也是只读的。



### shallowReactive

创建一个响应式代理，它跟踪其自身 property 的响应性，但不执行嵌套对象的深层响应式转换 (暴露原始值)



### markRaw

``````js
   const person = reactive({
         name:'xiaoMi',
         info:markRaw({
            money:1
         })
    });
``````

使用markRaw标记的数据将不会成为响应式，主要了为优化，对不需要响应式的数据减少proxy的递归成响应式

> 标记一个对象，使其永远不会转换为 proxy。返回对象本身。



### vue3.x自定义Hooks

```javascript
//useGetMousePosition.js

import { reactive, onMounted, onBeforeUnmount,ref } from 'vue';
export default function getMousePosition(){
/*   const state = reactive({
      x:0,
      y:0
  }); */
  const x = ref(0);
  const y = ref(0);
  const getPosition = ()=>{
      window.addEventListener("mousemove",e=>{
          x.value = e.pageX;
          y.value = e.pageY;
      });
  }
  onMounted(()=>{
       getPosition();
  });
  onBeforeUnmount(()=>{
     
  });
   return {
       x,
       y
   }
}
```



```javascript
//Test.vue
//引入useGetMousePosition方法
export default {
  name: "test6",
  setup() {
    const {x,y} = useGetMousePosition();
    return {
        x,y
    }
  },
};
```



### vue3中的两种子传父

```javascript
//方法1
 <父组件 :方法名="回调函数">   回调函数中修改父组件的值，子组件接收父组件传输的方法，这种方式与react相同

//方法2
  vue3中的不再是this.$emit,测试中this已经不再是指向vue实例，而是undefind,而emit在vue3中将其放在setup(props,context);第二个参数中
  调用方式context.emit(事件名,参数 ); 
  <父组件  @事件名="方法"/>

//需要注意的是
//vue2中对接收props并没有硬性规定，对指定接收的类型可写可不写
//但是在vue3中必须在props:{}中申请需要接收的参数，否则setup(props,context)中的props将是空的，找不到相关方法与变量
```





### 自定义hooks

当有公共的逻辑时，将状态data与方法提出封闭在一个js文件中的函数里,导出data数据

``````js
import { onBeforeUnmount, onMounted, onUnmounted, reactive  } from 'vue';
export default function(){
      const point = reactive({
          x:0,
          y:0
      });
      function getPoints(e){
           point.x = e.pageX;
           point.y = e.pageY;
      }
      onMounted(()=>{
         window.addEventListener('click',getPoints)
      });
      onBeforeUnmount(()=>{
          window.removeEventListener('click',getPoints)
      });
      return point;
}
``````



需要使用的地方 如test.vue

``````js
import usePoint from '../hooks/index.js';
setup(){
    const point = usePoint();
    return {
         point
    }
}
``````



### 其它变更

* 不再支持.native

* 不再支持keycode的事件修饰符  如@click.13='事件名'

* 全局注册组件放到了createApp中完成，详见： [https://v3.cn.vuejs.org/guide/component-registration.html#%E5%85%A8%E5%B1%80%E6%B3%A8%E5%86%8C](https://v3.cn.vuejs.org/guide/component-registration.html#%E5%85%A8%E5%B1%80%E6%B3%A8%E5%86%8C)

* 不再支持过滤器 vue.$filter语法 

* 新增`<teleport>`将组件挂载到某个dom

  ```js
   <teleport to="body">
        <div v-if="modalOpen" class="modal">
          <div>
            I'm a teleported modal! 
            (My parent is "body")
            <button @click="modalOpen = false">
              Close
            </button>
          </div>
        </div>
      </teleport>
  ```

  

* 404 Not found 路由由`path:*`更改   详见：[https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#%E5%93%8D%E5%BA%94%E8%B7%AF%E7%94%B1%E5%8F%82%E6%95%B0%E7%9A%84%E5%8F%98%E5%8C%96](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#%E5%93%8D%E5%BA%94%E8%B7%AF%E7%94%B1%E5%8F%82%E6%95%B0%E7%9A%84%E5%8F%98%E5%8C%96)

  ``````js
  const routes = [
    // 将匹配所有内容并将其放在 `$route.params.pathMatch` 下
    { path: '/:pathMatch(.*)*', name: 'NotFound', component: NotFound },
    // 将匹配以 `/user-` 开头的所有内容，并将其放在 `$route.params.afterUser` 下
    { path: '/user-:afterUser(.*)', component: UserGeneric },
  ]
  ``````

  

* app.config.globalProperties.foo = 'bar  定义的数据/方法可以在Vue.2x(mounted/created)中找到全局定义的方法

  vue3获取全局数据/方法: **`重点：官网好像不推荐使用getCurrentInstance`**

  > `getCurrentInstance` 只暴露给高阶使用场景，典型的比如在库中。强烈反对在应用的代码中使用 `getCurrentInstance`。请**不要**把它当作在组合式 API 中获取 `this` 的替代方案来使用。
  >
  > 详见： [https://v3.cn.vuejs.org/api/composition-api.html#getcurrentinstance](https://v3.cn.vuejs.org/api/composition-api.html#getcurrentinstance)

  ```````js
  //main.js
  app.config.globalProperties.$vueName  = 'Vue3全局挂载名称'
  
  //xxx.vue
  import {getCurrentInstance } from "vue";
  let {proxy}  = getCurrentInstance()
  console.log(proxy.$vueName);
  ```````

  

* Vue2.x中的beforeCreate的`this`指向的vue实例，到了Vue3中,setup/onMounted...生命周期的this指向的**`undefined`**

* vue2.x中的生命周期**包含beforeCreate**可以获取vue3.x中的setup实例方法、数据，使用this.xxx

  ``````js
  setup() {
     const count = ref(0);
     return {
         count
     }
  }
  created(){
     console.log(this.count);//获取为0
  }
  ``````

  

### vue2与 vue3版本的双向数据绑定原理

#### vue2

``````javascript
Object.defineProperty(obj,KEY,{
   get(){
       return obj[key]
   },
   set(value){
       obj[key] = value;
       document.xxx.innerText = value;//实现更新视图
   }
})
``````

**vue2使用的object.defineProperty存在的问题**

1. 新增、删除一个属性时，视图不会更新

   ``````js
   <template>
        <section>
               <h1>list.vue</h1>
               <h3 v-show="personInfo.name">用户名:{{ personInfo.name }}</h3>
               <h3 v-show="personInfo.age">年龄:{{ personInfo.age }}</h3>
               <h3 v-show="personInfo.school">学校:{{ personInfo.school }}</h3>
               <button @click="addSchool">添加一个学校</button>
        </section>
   </template>
   <script>
   import { defineComponent } from 'vue'
   
   export default defineComponent({
       setup() {
           
       },
       data(){
           return {
                personInfo:{
                     name:'shao.yuhong',
                     age:22
                }
           }
       },
       methods:{
           addSchool(){
                this.personInfo.addSchool = '重庆师范大学';
                console.log(this.personInfo);
           }
       }
   })
   </script>
   ``````

   > 解决方案
   >
   > vue2.x提供了两种方法
   >
   > * this.$set(this.personInfo,'school',值)
   >
   > * Vue.set(this.personInfo,'school',值)
   > * this.$delete(this.personInfo,'school',值)
   > * Vue.delete(this.personInfo,'school',值)

   

   



#### vue3

vue3使用proxy代理，弥补了vue2上述问题，但是出现了使用watch监听一个Array/object时，newVal与oldVal相同的问题

proxy中`get`  `set`  `deleteProerty`

使用es6中的Reflect读取，设置、删除某个值，使用Reflect而不是直接返回是因为

> 好处是，通过Reflect返回值就会知道成功还是失败。
>
> 对于vue3而言，底层使用reflect会比较健壮，不容易出现有个错误就导致程序阻塞。如果使用object：
>
> 虽然可以通过try catch，但是底层源码如果各种try catch就会显得不优雅。
>
> 如果使用reflect，当出现错误的时候，进行逻辑判断从而代码继续往下走

![https://gitee.com/xian_hong_xiao/assets/raw/master/img/proxy.png](https://gitee.com/xian_hong_xiao/assets/raw/master/img/proxy.png)



### vue.use()与Install

vue.use时,会找到对应的node_modules下的模块，这个模块存在一个文件名为index.js，并调用index.js中的install函数，在install函数中整合了所有组件，并导出