---
title: 了解下angular第一弹
date: 2021-02-21 12:42:32
tags: angular
---



> *ngFor="let item of list; let index = index;let odd = odd;trackBy: trackByItems "

```
let index = index; //获取当前下标项
let odd = odd; //判断是否为奇偶数，返回true/false;  o=>false 1=>true
trackBy: trackByItems; //官方解释 如果将 NgFor 与大型列表一起使用，则对某个条目的较小更改（例如删除或添加一项）就会触发一系列 DOM 操作。 例如，重新查询服务器可能会重置包含所有新条目对象的列表，即使先前已显示这些条目也是如此。在这种情况下，Angular 只能看到由新的对象引用组成的新列表，它别无选择，只能用所有新的 DOM 元素替换旧的 DOM 元素。
你可以使用 trackBy 来让它更加高效。向该组件添加一个方法，该方法返回 NgFor 应该跟踪的值。这个例子中，该值是英雄的 id。如果 id 已经被渲染，Angular 就会跟踪它，而不会重新向服务器查询相同的 id。
```



> *[ngIf](https://angular.cn/api/common/NgIf)="nullCustomer"

```
 很可惜没有ngelse,判断时只能一个个*ngIf
```



> [ngSwitch]

```
<div [ngSwitch]="变量">
  <app-stout-item    *ngSwitchCase="'stout'"    [item]="currentItem"></app-stout-item>
  <app-device-item   *ngSwitchCase="'slim'"     [item]="currentItem"></app-device-item>
  <app-lost-item     *ngSwitchCase="'vintage'"  [item]="currentItem"></app-lost-item>
  <app-best-item     *ngSwitchCase="'bright'"   [item]="currentItem"></app-best-item>
<!-- . . . -->
  <app-unknown-item  *ngSwitchDefault           [item]="currentItem"></app-unknown-item>
</div>

//疑问？如果有一个cj = 81; 判断<60时，展示不合格；>=60 && < 80时，展示良好；>=80时，展示优秀，[ngSwitch]好像没法判断 ，等验证...
```



> 杂项

```
[属性] = ’变量 ‘    //绑定属性
(事件名/click) = "处理函数()"  //绑定事件,测试发现 处理函数必须加括号调用，否则该事件不起作用
(事件名/click) = "处理函数($event)" //绑定事件时如果需要获取原生的事件参数使用$event接收
```



> 使用angular提供的api进行数据双向绑定

```
<input type='text' [(ngModel)] = '变量'/>
//使用该API需要提前在app.module.ts中引入 import { FormsModule  }  from 'angular/forms'
//并在imports中使用FormsModule
```



> 父组件向子组件传值

```
//父组件
<父组件 [属性]="值"></父组件>
<父组件 [属性]="this"></父组件> //父组件将事件实例 this传给子组件，子组件可能通过this.属性.数据|方法（）

//子组件 
//children.component.ts
@Input() count:number = 300; //接收使用input,count是属性名:数据类型 = 设置默认值 
@Input("count") count2:number = 300;   //为count取别名为count2,此时页面中访问的数据应是count
```



> 子组件向父组件通信

```
//子组件
1.引入Output,EventEmitter。import { Component, OnInit,Input,EventEmitter,Output } from '@angular/core';
2.注册一个自定义事件。 @Output() 自定义事件名= new EventEmitter();
3.子组件某个事件触发函数中提交一个自定义事件。 this.toParentHandleCount.emit(val); //val需要额外携带的参数 

//父组件
<父组件 (自定义事件名)="处理函数($event)"></父组件>
注意,$event是接收的子组件携带的参数。
【重要】问：$event可以写为其它名字吗？？？答:不可以，亲测,写为其它名字不能收到子组件会的额外参数
```



> 获取dom

```
//方法1
<input type="text" placeholder="请输入相关信息" #input/>
<button (click)="submit1(input)">通过传值获取input </button>

submit1(ele):void{     
         //ele便是元素
     console.log(ele.value)
  }

//方法2 通过viewChild
 引入ViewChild，ElementRef是类型
 @ViewChild("input") input:ElementRef;  //将input元素赋值给input，数据类型为elementRef
 this.input.nativeElement //nativeElement便是原生dom
```



> 常用的三个生命周期

```
ngOnInit()  在 Angular 第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件。 欲知详情，参阅本文档中的初始化组件或指令。在第一轮 ngOnChanges() 完成之后调用，只调用一次。

ngAfterContentInit()当 Angular 把外部内容投影进组件视图或指令所在的视图之后调用。欲知详情和范例，参阅本文档中的响应内容中的变更。第一次 ngDoCheck() 之后调用，只调用一次。

ngOnDestroy() 每当 Angular 每次销毁指令/组件之前调用并清扫。 在这儿反订阅可观察对象和分离事件处理器，以防内存泄漏。 欲知详情，参阅本文档中的在实例销毁时进行清理。在 Angular 销毁指令或组件之前立即调用。
```



> 服务

```
//创建服务
ng g service 路径 / 服务名

//使用
//首先需要在app.module.ts中注册服务
import { LxService } from './service/lx.service';
providers: [LxService],

//在需要使用的组件中引入服务
//引入后在constructor(public store:LxService){ //为变量赋值 }
```



> Rxjs

```
//参考链接:https://www.angular.cn/guide/observables-in-angular
import {Observable} from 'rxjs'
  new Observable<string>(observer => {
    setInterval(() => observer.next(new Date().toString()), 1000); //支持每1秒钟返回数据，且对应组件相应接收
  });

 获取
 this.navStart.subscribe(evt => console.log('Navigation Started!'));
  
报错
observer.error(错误信息)

 取消获取
const subscription = observable.subscribe(() => {
  // observer handles notifications
});
subscription.unsubscribe();
[注意]取消获取亲测，服务中依然在执行中，只是在获取输出时不对页面赋值,即service执行中，component组件中获取时不执行

使用其它方法
import { debounceTime, distinctUntilChanged, filter, map, switchMap } from 'rxjs/operators';
    const subscription =store.getData().pipe(filter((v) => {
      console.log(v);
      if(v>0.5){
        return true;
      }else{
        return false
      }
    })).subscribe(data=>{
      console.log(data);
      this.data = data.toString();
    });
```



> 请求数据

```
service.js

import { Injectable } from '@angular/core';
import axios from 'axios';
import { Observable } from 'rxjs';
@Injectable({
  providedIn: 'root'
})
export class RequestService {


  constructor() { }
  get(url) {
    return new Observable(observer => {
      let result = null;
      axios.get(url).then(res => {
        observer.next(res);
      });
    });
  }
}


//about.js
constructor(public route:Router,public request:RequestService) {
    
   }


  ngOnInit(): void {
    console.log(this.request);
    this.request.get("https://www.shaoyuhong.cn/grzx/findTypeList.php").subscribe((res:any)=>{
       let { code,list}  = res.data;
       if(code ===200){
          this.newsList = list.map(item=>{
            item.id = item.type_id;
            item.name = item.type_name;
            return item;
          })
       }
    });
  }
```



> 路由

```
1.动态路由
app.routing.module.js   {path:'details/:id',component:DetailsComponent},
<a [routerLink]="['/home/details',item.id]" routerLinkActive="router-link-active" >
 接收
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
@Component({
  selector: 'app-details',
  templateUrl: './details.component.html',
  styleUrls: ['./details.component.less']
})
export class DetailsComponent implements OnInit {
  routerInfo:any = null;
  constructor(public info:ActivatedRoute) { 
  
  }


  ngOnInit(): void {
    this.info.params.subscribe(data=>{
      this.routerInfo = data;
    })
  }


}
[相关链接参考]: https://www.angular.cn/guide/router#accessing-query-parameters-and-fragments

2.Get方式传参
 <a [routerLink]="['/home/details']" [queryParams] = "{id:item.id,age:22,school:'重庆大学'}" routerLinkActive="router-link-active" >
 接收
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
@Component({
  selector: 'app-details',
  templateUrl: './details.component.html',
  styleUrls: ['./details.component.less']
})
export class DetailsComponent implements OnInit {
  routerInfo:any = null;
  constructor(public info:ActivatedRoute) { 
  
  }


  ngOnInit(): void {
    this.info.queryParams.subscribe(data=>{
      this.routerInfo = data;
    })
  }


}

//总结：动态路由使用this.router.params.subscribe监听。普通路由get传参使用this.router.queryParams.subscribe(data=>{})监听
```



> js跳转路由

```
  methodTo(){
      //引入:NavigationExtras from '@anuglar/router';
      let queryParams:NavigationExtras={
          queryParams:{
            name:'ddd'
          }
      }
      this.route.navigate(['/home/details',动态路由参数],queryParams);
  }
```