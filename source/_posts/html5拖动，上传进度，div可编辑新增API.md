---
title: html5拖动，上传进度，div可编辑新增API
date: 2021-02-21 12:59:14
tags: javascript
---



上传进度：

   ev.loaded //已上传的大小

   ev.total //总大小



//一定要放在xhr.send之前，之后无作用

```javascript
    xhr.upload.onprogress = function(ev){
        console.log('进度：'+(100*ev.loaded/ev.total).toFixed(2)+'%');
    }
```



```
contenteditable="true"属性让div+css可以相当于Input     //<div>....标签均可修改内容 不限标签
输入框 <input type="text" x-webkit-speech />  //语音输入
```



```javascript
//桌面提醒
function showNotification() {
               // window.Notification.permission = "granted";
               
                if(window.Notification) {
                    if(window.Notification.permission == "granted") {
                        var notification = new Notification('你有一条新信息', {
                            body: "你好我是王小婷",

                            icon: "https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1422784010,1847471088&fm=27&gp=0.jpg"
                        });
                        setTimeout(function() { notification.close(); }, 50000);
                    } else {
                        window.Notification.requestPermission();
                    }
                } else alert('你的浏览器不支持此消息提示功能，请使用chrome内核的浏览器！');
            };
```