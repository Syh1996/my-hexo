---
title: 前端实现在线预览pdf、word、xls、ppt等文件
date: 2021-02-21 12:57:52
tags: html
---



### 1、前端实现pdf文件在线预览功能

方式一、pdf文件理论上可以在浏览器直接打开预览但是需要打开新页面。在仅仅是预览pdf文件且UI要求不高的情况下可以直接通过a标签href属性实现预览



###  2、word、xls、ppt文件在线预览功能

word、ppt、xls文件实现在线预览的方式比较简单可以直接通过调用微软的在线预览功能实现 (预览前提：资源必须是公共可访问的)

```html
<iframe src='https://view.officeapps.live.com/op/view.aspx?src=http://storage.xuetangx.com/public_assets/xuetangx/PDF/1.xls' width='100%' height='100%' frameborder='1'>
            </iframe>
```