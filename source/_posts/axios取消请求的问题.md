---
title: axios取消请求的问题
date: 2021-12-17 15:06:06
tags: axios		

---

针对如tab栏快速切换加载数据时，由于ajax是异步的，有时不能做加载动画防止快速点击，此时快速切换时，如A、B两栏快速切换，A栏切换请求接口；B栏切换请求接口；此时AB快速切换，接口跟不上，容易造成数据错乱，为了解决这种情况，就需要用到请求取消

需要注意的是axios提供的**cancelToken**仅仅只是对相同的同一个接口有效，可以取消。如果遇见多个接口需要防止这种取消之前的接口时，需要重新使用axios.CancelToken创建一个实例

效果如图

![图片](https://upload-images.jianshu.io/upload_images/5916524-fec8d000f96f27fa.png?imageMogr2/auto-orient/strip|imageView2/2/w/559/format/webp)

#### 封装一个简易的axios

```````js
/*
 * @Author: your name
 * @Date: 2021-12-16 13:59:33
 * @LastEditTime: 2021-12-17 08:53:19
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
 * @FilePath: \vue1\axios.js
 */
import axios from 'axios';

const getFn =  (url, data, that,CancelToken) => {
    const result = axios.get(url, {
        params: data,
        cancelToken: new CancelToken(function executor(c) {
            // executor 函数接收一个 cancel 函数作为参数
    
            if(that){
                that.cancel = c;
               
            }
            
          })
    });
    return result;
}
export default getFn;
```````



#### 调用

``````vue
<!--
 * @Author: your name
 * @Date: 2021-12-16 13:41:59
 * @LastEditTime: 2021-12-17 15:08:00
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE  
 * @FilePath: \vue1\src\App.vue
-->
<template>
  <div id="app">
    <h1>app.vue</h1>
    <hr />
    <div>
      <button
        @click="getData1(11)"
        :class="btnName === '个人笔记' ? 'active' : ''"
      >
        请求个人笔记
      </button>
      <button
        @click="getData1(12)"
        :class="btnName === '面试题' ? 'active' : ''"
      >
        请求面试题
      </button>
      <button @click="cancelFn">取消</button>
    </div>
    <ul v-if="list.length > 0">
      <li v-for="(item, index) in list" :key="index">
        【{{ btnName }}】--- {{ item.title }}
      </li>
    </ul>
    <div v-else>没有相关数据</div>
  </div>
</template>

<script>
import getFn from "../axios";
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      list: [],
      btnName: "个人笔记",
      cancel: "",
      CancelToken: "",
    };
  },
  created() {
    this.CancelToken = axios.CancelToken;
    this.source = this.CancelToken.source();
    this.getData1();
  },
  methods: {
    cancelFn() {
      const that = this;
      if (that.source) {
        that.source.cancel();
        this.source = this.CancelToken.source();
      }
    },
    async getData1(typeId = 11) {
      if (typeId === 11) {
        this.btnName = "个人笔记";
      } else {
        this.btnName = "面试题";
      }

      const that = this;
      if (this.cancel) {
        this.cancel();
      }

      getFn(
        "https://www.shaoyuhong.cn/grzx/findNoteList.php",
        { page: 1, type_id: typeId },
        that,
        that.CancelToken
      ).then((res) => {
        const { code, list } = res.data;
        if (code === 200) {
          this.list = list;
        }
      });
    },
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
button.active {
  background-color: red;
  color: #fff;
}
</style>

``````

