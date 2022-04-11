---
title: node那点事
date: 2021-03-12 21:41:41
tags: node
---

### 关于node的几项问题

* nodejs作为运行在服务器，虽然是使用js作为开发语言，但是无法获取Dom,Bom

* nodejs的变量作用于当前文件，例如A.js定义的全局变量无法在B.js文件中直接使用，如果需要调B.js的变量方法，需要使用module.exports导出,或者exports.变量名=值 

  

### http模块创建服务器

createServer() 创建一个服务器

end(string),end只能发送字符串类型，如果是对象数组可使用JSON.stringify转换为字符串

````javascript
const http = require("http");
const server = http.createServer();
server.on("request",(resquest,response)=>{
      response.setHeader("Content-Type","text/plain;charset=utf-8");
      response.end('这是一个测试')
});
server.listen(3000);
````

#### 上述代码可简写

``````javascript
const http = require("http");
const server = http.createServer((resquest,response)=>{
    .....
}).listen(3000,err=>{
    if(err){
        console.log("启动服务器出错");
        return;
    }
    console.log("启动服务器成功")
})
``````



#### 使用write写入数据

除了使用end发送数据外，也可以使用write写入

``````javascript
const http = require("http");
const server = http.createServer();
server.on("request",(resquest,response)=>{
      response.setHeader("Content-Type","text/plain;charset=utf-8");
      response.write('这是一个测试'); 
      response.end()
});
server.listen(3000);
``````

#### 使用writeHead写入响应头部

writeHead与setHeader效果相同，都是设置响应头部，writeHead(状态码,{....});

``````javascript
const http = require("http");
const server = http.createServer();
server.on("request",(resquest,response)=>{
      response.writeHead(200,{
          'Content-Type':'text/plain; charset=utf-8'
      });
      response.write('这是一个测试'); 
      response.end()
});
server.listen(3000);
``````



### fs文件操作模块

几个常用文件操作的方法

* fs.writeFile(file, data[, options], callback)  当 `file` 是文件名时，则异步地写入数据到文件（如果文件已存在，则覆盖文件）。 `data` 可以是字符串或 buffer。
* fs.writeFileSync(file, data[, options])    同上
* fs.rmdir(path[, options], callback)  删除目录recursive <boolean> 如果为 true，则执行递归的目录删除。 在递归模式中，错误不会被报告（如果 path 不存在），并且操作会被重试（当失败时）。 默认值: false。
* fs.rmdirSync(path[, options])  同上
* fs.stat(path[, options], callback)  统计文件/目录信息，包括创建时间...  
* fs.rename(oldPath, newPath, callback)  异步地把 oldPath 文件重命名为 newPath 提供的路径名。 如果 newPath 已存在，则覆盖它。 除了可能的异常，完成回调没有其他参数。
* fs.renameSync(oldPath, newPath) 	同上
* fs.existsSync(path) 如果路径存在，则返回 true，否则返回 false。
* fs.mkdir(path[, options], callback)  异步地创建目录。
* fs.mkdirSync(path[, options])  同上
* fs.readFile(path[, options], callback)  异步地读取文件的全部内容。
* fs.readFileSync(path[, options])   同步同上
* fs.appendFileSync(path,data)   追加数据到某个文件，某该文件不存在，则创建

#### 读取某个位置的所有文件/目录

```````javascript
const fs = require('fs');
const files = fs.readdirSync("../");
console.log(files);
```````

#### 读取某个文件的数据

``````javascript
const fs = require('fs');
const files = fs.readFileSync("./node.js");
console.log(files);
//输出 <Buffer 63 6f 6e 73 74 20 68 74 74 70 20 3d 20 72 65 71 75 69 72 65 28 22 68 74 74 70 22 29 3b 0d 0a 63 6f 6e 73 74 20 73 65 72 76 65 72 20 3d 20 68 74 74 70 ... 240 more bytes>  二进制的数据
//使用tostring()将二进制转为我们能认识的数据 
console.log(files.toString())
/* const http = require("http");
const server = http.createServer();
server.on("request",(resquest,response)=>{
      response.writeHead(200,{
          'Content-Type':'text/plain; charset=utf-8'
      });

      response.end('<h1>是一个测试</h1>')
});
server.listen(3000);
*/
``````

### 使用模板引擎 art-template

相关链接: [链接](http://aui.github.io/art-template/zh-cn/index.html)



### 处理静态资源 

1. 将css/js/image/video文件放到public公共文件夹中

2. 请求响应中匹配/public/开头，并读取相关文件返回,参考以下代码

   ```````javascript
   const http = require("http");
   const fs = require("fs");
   const server = http.createSever((request,response)=>{
         let url = request.url;
         if(/^\/public\//.test(url)){
              const file = fs.readFileSync('.'+url);
              response.end(file);
         }else if(url === '/'){
             const file = fs.readFileSync('./page/1.html');
             response.setHeader("Content-Type","text/html;charset=utf-8");
             response.end(file);
         }
   }).listen(3000,err=>{
       if(!err){
           console.log("服务器启动成功");
       }
   })
   ```````

   ``````html
   <img src="/public/1.jpg"/>
   <link href="/public/css/1.css"/>
   <script src="/public/js/1.js"/>
   <video src="/public/video/1.mp4"/>
   ``````




### 接收GET参数

``````javascript
const http = require("http");
const url = require("url"); //关键代码  使用url.parse解析url的参数
const server = http.createSever();
server.on("request",(request,response)=>{
    let urlData = url.parse(request.url,true); //url.parse(url,false/true),第二个参数是将get的参数query转为一个对象
});
server.listen(3000);
``````



### 接收post参数

``````javascript
const http = require('http');
const queryString = require("queryString");
http.createSever((request,response)=>{
    let post = ''; //接收的数据 
    request.on("data",chunk=>{
        post+=chunk;
    });
    request.on("end",()=>{
        let opt = queryString.parse(post); //opt = {name:'',age:''}
    })
}).listen(3000);
``````



### url模块

url.parse(url,[true/false]);   //将url格式化，用于获取参数，第二个参数为是否将query转为一个对象



### queryString模块

queryString.parse(url，分割符，切割符);  解析字符串为一个对象

```````javascript
queryString.parse('name=shao.yuhong&age=24');
/*
   {name:'shao.yuhong',age:24}
*/
```````

需注意

```````javascript
queryString.parse('?name=shao.yuhong&age=24');
/*
   {?name:'shao.yuhong',age:24}
*/
```````

针对其它切割字符串的情况

``````javascript
queryString.parse('name/shao.yuhong&age/4','&','/');
/*
  {name:'shao.yuhong',age:4} 
*/
``````



### express基于nodejs的开发框架

#### 安装 

``````
 npm install express --save
``````

#### 代码

``````javascript
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
``````

#### express获取 GET参数

``````javascript
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
  let opt = req.query; //获取 GET的参数
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
``````



#### 获取POST参数

获取POST参数，需要安装body-parser，body-parser会将post的参数封装到req.body中

``````javascript
 npm install body-parser
``````

使用方法

``````javascript
var bodyParser = require('body-parser')
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))

// parse application/json
app.use(bodyParser.json())
app.use(function (req, res) {
   req.body; //post参数
})
``````



#### 将css/image/js设置为公共资源返回

``````javascript
app.use('/public',express.static('路径'))
``````



### 常用的几个插件

* art-template  模板引擎

* cheerIo      将访问的dom文档转化为实例

  