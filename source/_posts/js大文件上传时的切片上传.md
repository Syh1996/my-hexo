---
title: js大文件上传时的切片上传
date: 2021-07-13 10:57:59
tags: js
---

### 1.大致流程

分为以下几步：

- 1.前端接收BGM并进行`切片`
- 2.将每份`切片`都进行`上传`
- 3.后端接收到所有`切片`，创建一个`文件夹`存储这些`切片`
- 4.后端将此`文件夹`里的所有切片合并为完整的BGM文件
- 5.删除`文件夹`，因为`切片`不是我们最终想要的，可`删除`
- 6.当服务器已存在某一个文件时，再上传需要实现`“秒传”`

![截屏2021-07-07 下午11.03.04.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1fe4c0b629543348a1bb1d7c422f521~tplv-k3u1fbpfcp-watermark.image)

### 2.前端实现切片

简单来说就是，咱们上传文件时，选中文件后，浏览器会把这个文件转成一个`Blob对象`，而这个对象的原型上上有一个`slice`方法，这个方法是大文件能够切片的原理，可以利用这个方法来给打文件切片

```js
<input type="file" @change="handleFileChange" />
<el-button @click="handleUpload"> 上传 </el-button>

data() {
    return {
        fileObj: {
            file: null
        }
    };
  },
  methods: {
      handleFileChange(e) {
          const [file] = e.target.files
          if (!file) return
          this.fileObj.file = file
      },
      handleUpload () {
          const fileObj = this.fileObj
          if (!fileObj.file) return
          const chunkList = this.createChunk(fileObj.file)
          console.log(chunkList) // 看看chunkList长什么样子
      },
      createChunk(file, size = 5 * 1024 * 1024) {
          const chunkList = []
          let cur = 0
          while(cur < file.size) {
              // 使用slice方法切片
              chunkList.push({ file: file.slice(cur, cur  size) })
              cur = size
          }
          return chunkList
      }
复制代码
```

> 例子我就用我最近很喜欢听得一首歌`嘉宾-张远`，他的大小是`32M`

![截屏2021-07-08 下午8.06.22.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33f448b2858c4cbd80c28f1466a01f45~tplv-k3u1fbpfcp-watermark.image)

> 点击上传，看看`chunkList`长什么样子吧：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d15dacd3c576489e8a6c165bc5b8635d~tplv-k3u1fbpfcp-watermark.image)

> 证明我们切片成功了！！！分成了7个切片

### 3.上传切片并展示进度条

我们先封装一个请求方法，使用的是`axios`：

```js
import axios from "axios";

axiosRequest({
      url,
      method = "post",
      data,
      headers = {},
      onUploadProgress = (e) => e, // 进度回调
    }) {
      return new Promise((resolve, reject) => {
        axios[method](url, data, {
          headers,
          onUploadProgress, // 传入监听进度回调
        })
          .then((res) => {
            resolve(res);
          })
          .catch((err) => {
            reject(err);
          });
      });
    }
复制代码
```

接着上一步，我们获得了所有`切片`，接下来要把这些切片保存起来，并逐一去上传

```js
handleUpload() {
      const fileObj = this.fileObj;
      if (!fileObj.file) return;
      const chunkList = this.createChunk(fileObj.file);
      this.fileObj.chunkList = chunkList.map(({ file }, index) => ({
        file,
        size: file.size,
        percent: 0,
        chunkName: `${fileObj.file.name}-${index}`,
        fileName: fileObj.file.name,
        index,
      }));
      this.uploadChunks(); // 执行上传切片的操作
    },
复制代码
```

`uploadChunks`就是执行上传所有切片的函数

```js
 async uploadChunks() {
      const requestList = this.fileObj.chunkList
        .map(({ file, fileName, index, chunkName }) => {
          const formData = new FormData();
          formData.append("file", file);
          formData.append("fileName", fileName);
          formData.append("chunkName", chunkName);
          return { formData, index };
        })
        .map(({ formData, index }) =>
          this.axiosRequest({
            url: "http://localhost:3000/upload",
            data: formData,
            onUploadProgress: this.createProgressHandler(
              this.fileObj.chunkList[index]
            ), // 传入监听上传进度回调
          })
        );
      await Promise.all(requestList); // 使用Promise.all进行请求
    },
 createProgressHandler(item) {
      return (e) => {
         // 设置每一个切片的进度百分比
        item.percent = parseInt(String((e.loaded / e.total) * 100));
      };
    },
复制代码
```

我不知道他们后端`Java`是怎么做的，我这里使用`Nodejs`模拟一下

```js
const http = require("http");
const path = require("path");
const fse = require("fs-extra");
const multiparty = require("multiparty");

const server = http.createServer();
const UPLOAD_DIR = path.resolve(__dirname, ".", `qiepian`); // 切片存储目录

server.on("request", async (req, res) => {
    res.setHeader("Access-Control-Allow-Origin", "*");
    res.setHeader("Access-Control-Allow-Headers", "*");
    if (req.method === "OPTIONS") {
        res.status = 200;
        res.end();
        return;
    }
    console.log(req.url)

    if (req.url === '/upload') {
        const multipart = new multiparty.Form();

        multipart.parse(req, async (err, fields, files) => {
            if (err) {
                console.log('errrrr', err)
                return;
            }
            const [file] = files.file;
            const [fileName] = fields.fileName;
            const [chunkName] = fields.chunkName;
            // 保存切片的文件夹的路径，比如  张远-嘉宾.flac-chunks
            const chunkDir = path.resolve(UPLOAD_DIR, `${fileName}-chunks`);
            // // 切片目录不存在，创建切片目录
            if (!fse.existsSync(chunkDir)) {
                await fse.mkdirs(chunkDir);
            }
            // 把切片移动到切片文件夹
            await fse.move(file.path, `${chunkDir}/${chunkName}`);
            res.end(
                JSON.stringify({
                    code: 0,
                    message: "切片上传成功"
                }));
        });
    }
})

server.listen(3000, () => console.log("正在监听 3000 端口"));
复制代码
```

接下来就是页面上进度条的显示了，其实很简单，我们想要展示`总进度条`，和`各个切片的进度条`，各个切片的进度条我们都有了，我们只需要算出`总进度`就行，怎么算呢？这么算：`各个切片百分比 * 各个切片的大小 / 文件总大小`

```js
 <div style="width: 300px">
      总进度：
      <el-progress :percentage="totalPercent"></el-progress>
      切片进度：
      <div v-for="item in fileObj.chunkList" :key="item">
        <span>{{ item.chunkName }}：</span>
        <el-progress :percentage="item.percent"></el-progress>
      </div>
</div>

 computed: {
    totalPercent() {
      const fileObj = this.fileObj;
      if (fileObj.chunkList.length === 0) return 0;
      const loaded = fileObj.chunkList
        .map(({ size, percent }) => size * percent)
        .reduce((pre, next) => pre  next);
      return parseInt((loaded / fileObj.file.size).toFixed(2));
    },
  },
复制代码
```

我们再次上传音乐，查看效果：

![截屏2021-07-08 下午10.33.51.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b43ea7e186d4f04b1cce52763c052ec~tplv-k3u1fbpfcp-watermark.image)

后端也成功保存了

![截屏2021-07-08 下午10.34.28.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84c9bae205f44033849255abb16615ed~tplv-k3u1fbpfcp-watermark.image)

### 4.合并切片为BGM

好了，咱们已经保存好所有切片，接下来就要开始`合并切片`了，我们会发一个`/merge`请求，叫后端合并这些切片，前端代码添加合并的方法：

```js
async uploadChunks() {
      const requestList = this.fileObj.chunkList
        .map(({ file, fileName, index, chunkName }) => {
          const formData = new FormData();
          formData.append("file", file);
          formData.append("fileName", fileName);
          formData.append("chunkName", chunkName);
          return { formData, index };
        })
        .map(({ formData, index }) =>
          this.axiosRequest({
            url: "http://localhost:3000/upload",
            data: formData,
            onUploadProgress: this.createProgressHandler(
              this.fileObj.chunkList[index]
            ),
          })
        );
      await Promise.all(requestList); // 使用Promise.all进行请求

      this.mergeChunks()
    },
 mergeChunks(size = 5 * 1024 * 1024) {
       this.axiosRequest({
         url: "http://localhost:3000/merge",
         headers: {
           "content-type": "application/json",
         },
         data: JSON.stringify({ 
          size,
           fileName: this.fileObj.file.name
         }),
       });
     }
复制代码
```

后端增加`/merge`接口：

```js
// 接收请求的参数
const resolvePost = req =>
    new Promise(res => {
        let chunk = ''
        req.on('data', data => {
            chunk = data
        })
        req.on('end', () => {
            res(JSON.parse(chunk))
        })

    })
const pipeStream = (path, writeStream) => {
    console.log('path', path)
    return new Promise(resolve => {
        const readStream = fse.createReadStream(path);
        readStream.on("end", () => {
            fse.unlinkSync(path);
            resolve();
        });
        readStream.pipe(writeStream);
    });
}

// 合并切片
const mergeFileChunk = async (filePath, fileName, size) => {
    // filePath：你将切片合并到哪里，的路径
    const chunkDir = path.resolve(UPLOAD_DIR, `${fileName}-chunks`);
    let chunkPaths = null
    // 获取切片文件夹里所有切片，返回一个数组
    chunkPaths = await fse.readdir(chunkDir);
    // 根据切片下标进行排序
    // 否则直接读取目录的获得的顺序可能会错乱
    chunkPaths.sort((a, b) => a.split("-")[1] - b.split("-")[1]);
    const arr = chunkPaths.map((chunkPath, index) => {
        return pipeStream(
            path.resolve(chunkDir, chunkPath),
            // 指定位置创建可写流
            fse.createWriteStream(filePath, {
                start: index * size,
                end: (index  1) * size
            })
        )
    })
    await Promise.all(arr)
};
if (req.url === '/merge') {
        const data = await resolvePost(req);
        const { fileName, size } = data;
        const filePath = path.resolve(UPLOAD_DIR, fileName);
        await mergeFileChunk(filePath, fileName, size);
        res.end(
            JSON.stringify({
                code: 0,
                message: "文件合并成功"
            })
        );
    }
复制代码
```

现在我们重新上传音乐，发现切片上传成功了，也合并成功了：

![截屏2021-07-09 下午1.44.29.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af48b4bef5b54f3f953dd80cdee934be~tplv-k3u1fbpfcp-watermark.image)

### 5.删除切片

上一步我们已经完成了`切片合并`这个功能了，那之前那些存在后端的切片就没用了，不然会浪费服务器的内存，所以我们在确保合并成功后，可以将他们`删除`

```js
// 合并切片
const mergeFileChunk = async (filePath, fileName, size) => {
    // filePath：你将切片合并到哪里，的路径
    const chunkDir = path.resolve(UPLOAD_DIR, `${fileName}-chunks`);
    let chunkPaths = null
    // 获取切片文件夹里所有切片，返回一个数组
    chunkPaths = await fse.readdir(chunkDir);
    // 根据切片下标进行排序
    // 否则直接读取目录的获得的顺序可能会错乱
    chunkPaths.sort((a, b) => a.split("-")[1] - b.split("-")[1]);
    const arr = chunkPaths.map((chunkPath, index) => {
        return pipeStream(
            path.resolve(chunkDir, chunkPath),
            // 指定位置创建可写流
            fse.createWriteStream(filePath, {
                start: index * size,
                end: (index  1) * size
            })
        )
    })
    await Promise.all(arr)
    fse.rmdirSync(chunkDir); // 合并后删除保存切片的目录
};
复制代码
```

我们再次上传，再看看，那个储存此音乐的切片文件夹被我们`删了`：

![截屏2021-07-09 下午1.46.59.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7528f7e2e78a42278bdf7a7103c57fbd~tplv-k3u1fbpfcp-watermark.image)

### 6.秒传功能

所谓的`秒传功能`，其实没那么高大上，通俗点说就是，当你上传一个文件时，后端会判断服务器上有无这个文件，有的话就不执行上传，并返回给你`“上传成功”`，想要执行此功能，后端需要重新写一个接口`/verify`

```js
if (req.url === "/verify") {
        const data = await resolvePost(req);
        const { fileName } = data;
        const filePath = path.resolve(UPLOAD_DIR, fileName);
        console.log(filePath)
        if (fse.existsSync(filePath)) {
            res.end(
                JSON.stringify({
                    shouldUpload: false
                })
            );
        } else {
            res.end(
                JSON.stringify({
                    shouldUpload: true
                })
            );
        }
复制代码
```

前端在`上传文件`步骤也要做拦截：

```js
async handleUpload() {
      const fileObj = this.fileObj;
      if (!fileObj.file) return;
      const { shouldUpload } = await this.verifyUpload(
         fileObj.file.name,
       );
       if (!shouldUpload) {
         alert("秒传：上传成功");
         return;
       }
      const chunkList = this.createChunk(fileObj.file);
      this.fileObj.chunkList = chunkList.map(({ file }, index) => ({
        file,
        size: file.size,
        percent: 0,
        chunkName: `${fileObj.file.name}-${index}`,
        fileName: fileObj.file.name,
        index,
      }));
      this.uploadChunks();
    },
 async verifyUpload (fileName) {
       const { data } = await this.axiosRequest({
         url: "http://localhost:3000/verify",
         headers: {
           "content-type": "application/json",
         },
         data: JSON.stringify({
           fileName,
         }),
       });
       return data
     }
复制代码
```

现在我们重新上传音乐，因为服务器上已经存在了`张远-嘉宾`这首歌了，所以，直接alert出`秒传：上传成功`

![截屏2021-07-09 下午2.17.02.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5ccd82b75bdb49e3a19962d0ab6a8191~tplv-k3u1fbpfcp-watermark.image)

## 暂停续传

### 1.大致流程

暂停续传其实很简单，比如一个文件被切成`10片`，当你上传成功`5片`后，突然暂停，那么下次点击`续传`时，只需要过滤掉之前已经上传成功的那`5片`就行，怎么实现呢？其实很简单，只需要点击`续传`时，请求`/verity`接口，返回`切片文件夹里`现在已成功上传的切片列表，然后前端过滤后再把`还未上传的切片`的继续上传就行了，后端的`/verify`接口需要做一些修改

```js
if (req.url === "/verify") {
        // 返回已经上传切片名列表
        const createUploadedList = async fileName =>
             fse.existsSync(path.resolve(UPLOAD_DIR, fileName))
                 ? await fse.readdir(path.resolve(UPLOAD_DIR, fileName))
                 : [];
        const data = await resolvePost(req);
        const { fileName } = data;
        const filePath = path.resolve(UPLOAD_DIR, fileName);
        console.log(filePath)
        if (fse.existsSync(filePath)) {
            res.end(
                JSON.stringify({
                    shouldUpload: false
                })
            );
        } else {
            res.end(
                JSON.stringify({
                    shouldUpload: true,
                     uploadedList: await createUploadedList(`${fileName}-chunks`)
                })
            );
        }
    }
复制代码
```

### 2.暂停上传

前端增加一个`暂停按钮`和`pauseUpload`事件

```js
 <el-button @click="pauseUpload"> 暂停 </el-button>


 const CancelToken = axios.CancelToken;
 const source = CancelToken.source();

axiosRequest({
      url,
      method = "post",
      data,
      headers = {},
      onUploadProgress = (e) => e,
    }) {
      return new Promise((resolve, reject) => {
        axios[method](url, data, {
          headers,
          onUploadProgress,
           cancelToken: source.token
        })
          .then((res) => {
            resolve(res);
          })
          .catch((err) => {
            reject(err);
          });
      });
    },
 pauseUpload() {
       source.cancel("中断上传!");
       source = CancelToken.source(); // 重置source，确保能续传
     }
复制代码
```

### 3.续传

增加一个`续传`按钮，并增加一个`keepUpload`事件

```js
 <el-button @click="keepUpload"> 续传 </el-button>

 async keepUpload() {
       const { uploadedList } = await this.verifyUpload(
         this.fileObj.file.name
       );
       this.uploadChunks(uploadedList);
     }
复制代码
```

### 4.优化进度条

```js
续传`中，由于那些没有上传的切片会`从零开始`传，所以会导致`总进度条`出现`倒退现象`，所以我们要对`总进度条`做一下优化，确保他不会`倒退`，做法就是维护一个变量，这个变量只有在`总进度大于他`时他才会`更新成总进度
总进度：
 <el-progress :percentage="tempPercent"></el-progress>

 watch: {
       totalPercent (newVal) {
           if (newVal > this.tempPercent) this.tempPercent = newVal
       }
   },
```



作者：Sunshine_Lin链接：https://juejin.cn/post/6982877680068739085   来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### 个人使用vue实现代码

``````js
<template>
  <div id="app">
    <input type="file" @change="fileChange" />
    <button @click="upload">提交</button>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",
  data() {
    return {
      checkedFile: null,
      spliceFileList: [],
    };
  },
  components: {
    HelloWorld,
  },
  methods: {
     //记录文件
    fileChange(e) {
      this.checkedFile = e.target.files;
      console.log(this.checkedFile);
    },
      
      //切片文件并生成formData
    upload() {
      let size = 0;
      const splitSize = 10 * 1024;
      const fileList = [];
      while (size < this.checkedFile[0].size) {
        fileList.push(this.checkedFile[0].slice(size, size + splitSize));
        size += splitSize;
      }
      this.spliceFileList = fileList.map((file, index) => {
        const formData = new FormData();
        formData.append("fileName", this.checkedFile[0].name);
        formData.append("file", file);
        return {
          formData,
          index,
        };
      });
      this.sendFile();
    },
      
      //上传文件
    sendFile() {
      this.spliceFileList.forEach((file) => {
        let xhr = new XMLHttpRequest();
        xhr.open("post", "http://www.shaoyuhong.cn/lx104.php");
        xhr.send(file.formData);
        xhr.onreadystatechange = function () {
          if (this.readyState === 4 && this.status === 200) {
            console.log(this.responseText);
          }
        };
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
</style>

``````

