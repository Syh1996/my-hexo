---
title: vue发布插件到npm源
date: 2021-06-30 16:27:01
tags: vue npm
---

### 注册npm

[注册npm https://www.npmjs.com](https://www.npmjs.com)



### vue配置

使用vue init webpack-simple 项目名，初始化项目

提示：不要用vue init webpack npm-practice 初始化项目，因为我们就开发个组件，不需要那么多配置，配置多了修改起来也麻烦，webpack-simple足够了

#### 修改目录

在src目录下新建packages文件夹，建立组件vue文件与配置js文件

* Btngroup.vue文件

  ```````js
  <template>
    <div class="btn-group">
      <button @click="sub">-</button>
      <span>{{ num }}</span>
      <button @click="add">+</button>
    <!--   <shaobtnGroup/> -->
    </div>
  </template>
  
  <script>
                       
  export default {
    name:'btngroup',
    data() {
      return {
        num: 1,
      };
    },
    components:{
    },
    methods: {
      add() {
        this.num += 100;
      },
      sub() {
        this.num -= 100;
      },
    },
  };
  </script>
  
  <style scoped>
  .btn-group button {
    display: inline-block;
    width: 120px;
    height: 30px;
    line-height: 30px;
    font-size: 20px;
  }
  .btn-group span {
    margin: 0 10px;
    line-height: 30px;
    font-size: 20px;
  }
  </style>
  ```````

* index.js配置文件

  ``````js
  import BtnGroup from './index.vue'
   
  //这样就可以使用Vue.use进行全局安装了。
  BtnGroup.install = function(Vue){
     Vue.component(BtnGroup.name, BtnGroup)
  }
  export default BtnGroup
  ``````



#### 修改package.json文件配置

main为指向打包后的文件js； name用于发布npm后，使用时import name from [来源的文件地址便是package.json打包时的文件名]； version打包发布时的版本号，需要注意npm发布时不允许重复提交同一个版本号，所以每次修改都需要修改一次版本号； private:false，private是true的时候不能发布到npm，需设置成false

``````js
"main": "dist/build.js",
"name": "vue1",
"version": "1.0.0",
"private": false,
``````



#### 最后修改webpack.config.js

``````js
const env = process.env.NODE_ENV;
module.exports = {
  entry: env==='development'?'./src/main.js':'./src/packages/btngroup/index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist/',
    filename: 'build.js',
    libraryTarget:'umd'
  }, 
  }
``````

entry:判断当前环境，调试时使用正常的main.js，当打包发布时需要将入口文件指向**组件配置的文件js**

libraryTarget:**非常重要** ,不知道有什么吊用，不写发布后无法正常使用。webpack官方未发现有这个参数



### 发布NPM

* 使用npm run build打包项目
*  首次运行npm login登录npm,会提示输入个人信息，Email是发布成功后，自动给这个邮箱发送一个邮件，通知发布成功，输入完，登录成功
* 运行 npm publish,发布成功。 
*  自动给这个邮箱发送一个邮件，通知发布成功 



###  使用

发布成功后，在npm网站上package中可以看到自己的发布的npm包

![vue发布插件到npm](https://gitee.com/xian_hong_xiao/assets/raw/master/img/npm.PNG)





通过install安装该插件，在main.js引入并使用插件

***import btngroup from 'shao-btn-group';*** //shao-btn-group来源于打包时的package.json的name字段

***Vue.use(btngroup)*** //使用

最后在需要使用的页面中使用组件

< btngroup />



### 异常处理

* npm publish时，可能出现报错，大概意思是没有权限提交。造成的原因是当前插件的名字(package.json中的name值 )与现有平台的插件名字出现重复，当前无法提交。需要修改package.json中 name值，直到没有重复的名字即可