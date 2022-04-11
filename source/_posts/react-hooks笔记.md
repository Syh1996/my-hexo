---
title: react_hooks笔记
date: 2021-08-30 10:23:51
tags: react
---

#### useState

``````jsx
function AboutHook() {
      const [count, setCount] = useState(0);
      const [show, setShow] = useState(true)
      return (<div>
            <h2>this is abouthook.js</h2>
            <button onClick={() => setShow(!show)}>显示/隐藏子组件</button>
            <p>
                  <button onClick={() => setCount(count - 1)}>-</button>
                  <span>{count}</span>
                  <button onClick={() => setCount(count + 1)}>+</button>
            </p>
      </div>);
}
``````



#### createContext()  

* 使用公共文件将实例导出  **export default const context = createContext()**
* 将需要暴露的数据通过**<C.provider value={值}></C.provider>**
* 子孙组件里面中使用useContext实例,const C = useContext(导出的实例)。C里面包含传入的数据

#### useContext()

针对function函数式组件的接收值 ，类式组件采用 static contextType = ThemeContext;  tihs.context中就是provider传入的值

#### useEffect

* 一个function中可以存在多个useEffect

* ``````js
  useEffect(()=>{
          console.log('props.count更新时触发 ');
       
      },[props.count]); //componentDidMount + count变化时的componentDidUpdate
  ``````

* ``````js
      useEffect(()=>{
          return ()=>{
              console.log('销毁了')
          }
      },[]) //componentDidMount +componentUnDidMount
  ``````

* ``````js
  useEffect(()=>{
      ....
      return ()=>{
          console.log('销毁了')
      }
  },[count]) //componentDidMount +componentDidUpdate + componentUnDidMount
  首次+count更新,更新的同时会触发销毁
  ``````

  #### useReducer实现reducer状态管理

``````js
function Test() {
    const defaultState = {
        color: 'red'
    }
    const [store, dispatch] = useReducer((state, action) => {
        if (action.type === 'changeColor') {
            return { ...state, color: action.payload.color }
        }
        return state;
    },defaultState);
    return (<div>
        <createContext2.Provider value={ {store:store,dispatch} }>
            <BtnGroup></BtnGroup>
            <Message></Message>
        </createContext2.Provider>
    </div>)
}
``````



#### useMemo

useMemo 主要用于性能优化，在hooks中数据更新会使页面重新渲染，如果 页面有调用 的函数，那么相关函数将重新计算并调用 ，useMeno主要用于解决多于调用 的问题

``````js
 const sum =useMemo(()=>{
        var sum =0;
        for(var i=0;i<1000;i++){
             sum+=i;
        }
        console.log("sum触发")
        return sum;
    },[age]) 
//调用时{sum} 不再是函数 
``````





#### useRef

useRef可以用于存储变量或者 dom元素

``````react
import {useRef} from 'react';
function Fn(){
      const ele = useRef;
      useEffect(()=>{
            ele.current = Math.random();
      });
    return (<div>
          <input  type='text' ref={ele}></input> 
        </div>);
}
``````



