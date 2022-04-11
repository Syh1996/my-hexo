---
title: redux-saga代码参考
date: 2021-02-21 12:53:06
tags: react
---



#### saga.js

```javascript
import { call, put,takeEvery, select,takeLatest } from 'redux-saga/effects';
import axios from 'axios';
const handleAddCountFn = function *(){
    const page = yield select(state=>state.payload.count);
    const num = yield select(state=>state.count);
    const result = yield call(axios.get,"http://www.shaoyuhong.cn/lx104.php",{params:{page:page+num}});
    yield put({
        type:'addCount',
        payload:{
             num:page
        }
    });
    yield put({
        type:'setList',
        payload:{
            list:result.data
        }
    })
};
```



```javascript
const handleSubCountFn = function *(){
  const page = yield select(state=>state.payload.count);
  const num  = yield select(state=>state.count);
  const result = yield call(axios.get,"http://www.shaoyuhong.cn/lx104.php",{params:{page:num - page}});
  yield put({
      type:'subCount',
      payload:{
          num:page
      }
  });
  yield put({
      type:'setList',
      payload:{
          list:result.data
      }
  })
};

function *saga(){
   yield takeEvery('requestAddCount',handleAddCountFn);
   yield takeLatest('requestSubCount',handleSubCountFn)
}
export default saga;
```



#### app.js提交请求

```javascript
@connect(
  state=>{
    return {
       count:state.count,
       list:state.list
    }
  },
  dispatch=>{
    return {
        addCount(){
          dispatch({type:'requestAddCount',payload:{count:parseInt(Math.random() * 100)}})
        },
        subCount(){
          dispatch({type:'requestSubCount',payload:{ count:100 }})
        }
    }
  }
)
```



####  reducer.js 参考

```javascript
const defaultState = {
      count:100,
      list:['test']
}
export default function(state = Object.assign({},defaultState),action = {type:'',payload:{}}){
     let { type,payload} = action;
     if(type === 'addCount'){
         let obj = Object.assign({},state);
         obj.count +=action.payload.num;
         return obj;
     }
     if(type ==='subCount'){
         let obj = Object.assign({},state);
         obj.count -=action.payload.num;
         return obj;
     }
     if(type === 'setList'){
         let obj = Object.assign({},state);
         obj.list = action.payload.list;
         return obj;
     }
    return Object.assign({},state,action);
}
```



//注: app.js dispatch提交的事件名可以和reducer.js action.type名不同，dispatch提交的事件将在saga.js的takeEvery中被监听

使用saga.js需要在reducer中默认返回Object.assign({},state,action);