---
title: js常见算法数组去重
date: 2021-02-23 21:45:34
tags: javascript
---

1. ES6 new Set去重

```
[...new Set(arr)]
```

2. 利用object key不重复的特性去重

```javascript
let arr = [................]
let obj = {};
arr.forEach((item,index)=>{
    obj[item] = 任意值 
})

//最后通过obj key生成数组   Object.keys(obj)
```

3. 利用双重循环删除多余值

4. 利用includes() find() findIndex() filter()这类方法push到一个全新的数组

5. 利用相邻节点相同删除的方法
   * 对数据排序 
   * 循环数组，如下一项与记录的一项相同则删除，不同则重新记录当前项