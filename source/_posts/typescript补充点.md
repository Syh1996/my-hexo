---
title: typescript补充点
date: 2022-02-23 22:15:15
tags: ts
---

### declare 

``````js
declare let $: JQuery;
$()....
``````



### 泛型

``````js
function Fn<T(该名字可以随意)>(a:T,b:string):T{
   //.....
}
fn<number>(1,'1');
``````

需要多个泛型约束

``````js
function Fn<T,P>(a:T,b:string):T{
   //.....
}
fn<number,string>(1,'1');
``````



### 接口也可以使用泛型

``````js
interface Page{
      current:number;
      size:number;
}
interface Opt<R> {
      name:string;
      page:R
}
const a:Opt<Page> = {
    name:'shao.yuhong',
    page:{
        current:1,
        size:10
    }
}
``````

