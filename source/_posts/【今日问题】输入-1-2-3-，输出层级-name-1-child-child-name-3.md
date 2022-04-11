---
layout: '【今日问题】输入[1,2,3]，输出层级{name:1,child:name:2'
title: '【今日问题】输入[1,2,3]，输出层级{name:1,child:child:{name:3}'
date: 2021-02-21 12:39:00
tags: 算法
---



> 使用reduce实现

``````javascript
<script>
var arr = [1,2,3];
var obj = arr.slice().reverse().reduce((opt,item)=>{
    if(opt){
      return {name:item,child:opt}
    }else{
        return { name:item}
    }
},null);
console.log(obj); 

</script>
``````



> 使用递归

```````javascript
function formatter(arr){
    if(arr.length){
        let current = arr.shift();
        if(arr.length ===0){
            return {
                name:current
            }
        }
        return {
             name:current,
             child:formatter(arr)
        }
    }
    return {};
}
console.log(formatter(arr.concat()));
```````



#### 感谢余大佬提点

