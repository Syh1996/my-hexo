---
title: js设计模式
date: 2021-06-23 17:35:17
tags: js
---



### 单例设计模式

一个class类，new 多次，始终只有一个最终生成的实例，即new class() ===new class() ===new class();

单例设计模式，实现过程为，一个闭包，用一个变量记录是否已经 new实例过，如果是返回记录的实例。否则new一个实例并赋值给闭包变量返回。**参考以下弹窗设计**

``````html
<!DOCTYPE html>	
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }
      .mask {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        width: 300px;
        height: 200px;
        margin: auto;
        display: none;
      }
      .mask .title {
        background-color: darkcyan;
        color: #fff;
        height: 30px;
        line-height: 30px;
        padding: 0 10px;
        box-sizing: border-box;
      }
      .mask .content {
        background-color: #ccc;
        color: 0.333;
        padding: 20px;
        box-sizing: border-box;
        height: 140px;
      }
      .mask .btns {
        height: 30px;
        background-color: darkgrey;
        text-align: right;
        line-height: 30px;
        padding: 0 10px;
        box-sizing: border-box;
      }
    </style>
  </head>
  <body>
    <button id="btn" onclick="openMask()">打开弹窗</button>
  </body>
</html>
<script>
  const singeFunction = (function () {
    class Mask {
      constructor() {
        this.el = null; //弹窗类
        this.el = document.createElement("div");
        this.el.className = "mask";
        document.body.appendChild(this.el);
        this.bindEvent();
        this.onSubmit = null;
        this.onCancel = null;
      }
      create(opt) {
        this.onSubmit = opt.submit;
        this.onCancel = opt.cancel;
        this.el.innerHTML = ` 
            <div class="title">${opt.title || "默认标题"}</div>
            <div class="content">${opt.msg || "默认内容"}</div>
            <div class="btns">
                <button class="save">保存</button>
                <button class="cancel">取消</button>
            </div>`;
        this.el.style.display = "block";
      }

      //绑定确定、返回按钮事件
      bindEvent() {
        let that = this;
        this.el.addEventListener("click", function (e) {
          let tag = e.target;
          if (tag.className === "cancel") {
            that.el.style.display = "none";
            that.onCancel();
          } else if (tag.className === "save") {
            that.el.style.display = "none";
            that.onSubmit();
          }
        });
      }
    }
    var instance = null;
    return function () {
      if (instance) {
        return instance;
      }
      instance = new Mask();
      return instance;
    };
  })();

  function openMask() {
    var fn = singeFunction();
    fn.create({
      title: "标题" + Math.random(),
      content: "内容" + Math.random(),
      submit: function () {
        alert("你点击了确定");
      },
      cancel: function () {
        alert("你点击了取消");
      },
    });
  }
</script>

``````

