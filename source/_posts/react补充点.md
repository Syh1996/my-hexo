---
title: react补充点
date: 2022-02-23 22:15:24
tags: react

---

## 概述

如果您熟悉 JavaScript 生态系统、React 和 React Router，这可以作为 React Router v6 的快速概览，其中包含大量代码和最少的解释。

- 有关 React Router 的完整介绍，请参阅[教程](https://reactrouter.com/docs/en/v6/getting-started/tutorial)
- 有关每个 API 的大量文档，请参阅[API 参考](https://reactrouter.com/docs/en/v6/api)
- 有关概念的更深入理解，请参阅[主要概念](https://reactrouter.com/docs/en/v6/getting-started/concepts)

## 安装

```
npm install react-router-dom@6
```

## 配置路由

```js
import { render } from "react-dom";
import {
  BrowserRouter,
  Routes,
  Route,
} from "react-router-dom";
// import your route components too

render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<App />}>
        <Route index element={<Home />} />
        <Route path="teams" element={<Teams />}>
          <Route path=":teamId" element={<Team />} />
          <Route path="new" element={<NewTeamForm />} />
          <Route index element={<LeagueStandings />} />
        </Route>
      </Route>
    </Routes>
  </BrowserRouter>,
  document.getElementById("root")
);
```

在以前版本的 React Router 中，当多个路由匹配一个不明确的 URL 时，您必须以某种方式对路由进行排序，以便获得正确的渲染。V6 更智能，并且会选择最具体的匹配，因此您不必再担心了。例如，URL`/teams/new`匹配这两个路由：

```html
<Route path="teams/:teamId" element={<Team />} />
<Route path="teams/new" element={<NewTeamForm />} />
```

但是`teams/new`是比 更具体的匹配`/teams/:teamId`，所以`<NewTeamForm />`会渲染。

## 导航

用于`Link`让用户更改 URL 或`useNavigate`自己进行（例如在表单提交之后）：

**请注意，react17版本中，定义变量use开头，如果不是hooks或者是高阶组件，将会报错 **

```js
import { Link } from "react-router-dom";

function Home() {
  return (
    <div>
      <h1>Home</h1>
      <nav>
        <Link to="/">Home</Link> |{" "}
        <Link to="about">About</Link>
      </nav>
    </div>
  );
}
import { useNavigate } from "react-router-dom";

function Invoices() {
  let navigate = useNavigate();
  return (
    <div>
      <p  onClick={()=> navigate(`/invoices/${newInvoice.id}`)}
      ></p>
    </div>
  );
}
```

**`navLink`取消了v5中的activeClassName**

**`<navLink end></navLink>`  end添加到父级导航，作用是当子导航 被匹配时，父级导航 将不再高亮**

`<Link replace><NavLink replace>` 替换方式





### 多级路由默认展示页

path="about/*"   ?   

如果path="about"   react-router-dom只会走一级路由，不会进入二级路由匹配

如果path="about/"   同理，react-router-dom不会进入二级路由的匹配

path="about/*"后    /about 会重定向到/about/tab1

``````html
 <Routes>
        <Route path="/" element={<App></App>}>
            <Route index element={<Message></Message>}></Route>
            <Route path="about/*"  element={<About></About>}>
                  <Route path="tab1" element={<AboutView1></AboutView1>}></Route>
                  <Route path="tab2" element={<AboutView2></AboutView2>}></Route>
                  <Route path="tab3" element={<AboutView3></AboutView3>}></Route>
                  <Route path="*" element={<Navigate to="tab1"></Navigate>}></Route>
            </Route>
        </Route>
        <Route path="/404" element={<Error></Error>}></Route>
        <Route path="*" element={<Navigate to="/404"></Navigate>}></Route>
       
      </Routes>
``````



### 路由配置表

配置表router.js

* **子路由重定向404效果参考path:‘about/'**
* **使用路由配置表时，不能再添加`<Routes>组件`**
* **使用`useRoutes` 时，需要要在`<BrowserRouter>`内，否则报错**

``````js
const routers = [
            {
               path:'message',
               element:<Message/>
            },
            {
                path:'about/*',
                element:<About/>,
                children:[
                    {
                        path:'tab1',
                        element:<AboutView1/>
                    },
                    {
                        path:'tab2',
                        element:<AboutView2/>
                    },
                    {
                        path:'tab3',
                        element:<AboutView3/>
                    },
                    {
                        path:'*',
                        element:<Navigate to="tab1"></Navigate>
                    }
                ]
            },
            {
                path:'404',
                element:<Error/>
            },
            {
                path:'*',
                element:<Navigate to="message"></Navigate>
            }
]
export default routers;
``````

index.js入口文件

``````jsx
ReactDOM.render(
  <React.StrictMode>
    <BrowserRouter>
      <App></App>
    </BrowserRouter>
  </React.StrictMode>,
  document.getElementById('root')
);
``````



App.js

``````jsx
function App() {
  const element = useRoutes(RoutersList);
  return (
    <div className="app">
        <h2>App.js入口文件</h2>
        <hr/>
           <main>
                <nav></nav>
                <section>
                  {element} 
                </section>
         </main>
    </div>
  );
}
``````

## 读取 URL 参数

* **useParams() 用于读取 /about/:id的参数**

  ```js
  import { Routes, Route, useParams } from "react-router-dom";
  
  function App() {
    return (
      <Routes>
        <Route
          path="invoices/:invoiceId"
          element={<Invoice />}
        />
      </Routes>
    );
  }
  
  function Invoice() {
    let params = useParams();
    return <h1>Invoice {params.invoiceId}</h1>;
  }
  ```

  

* **useSearchParams（）用于读取?name=xxx&age=22  URL的参数**    

  ``````jsx
  <Link to="../tab3?name=shao.yuhong&age=22">跳转到tab3.jsx search方式</Link> 
  
  <!--
     * 读取参数 
     * 请注意 useSearchParams是一个hooks函数
  -->
  import { useParams,useSearchParams,useLocation } from 'react-router-dom';
  const [searchParams,setSearchPamras] = useSearchParams();
  const name = searchParams.get('name'),age = searchParams.get('age')
  
  <!--也可以实现设置他当前url参数   ?加不加都无所谓-->
  setSearchPamras.set('name=xiaoliu&age=10000');
  setSearchPamras.set('?name=xiaoliu&age=10000'); 
  ``````

* **useLocation（） 用于读取原V5中的state,或者search（?name=xxx&age=22)**     ***经过测试-》state传参页面刷新参数不会丢失***

  ``````jsx
   <Link to="../tab3" state={{name:'shao.yuhong',age:25}}>跳转到tab3.jsx state方式</Link>
   const search = useLocation();
  
  {/* 
     结果如下:
     {pathname: '/about/tab3', search: '', hash: '', state: {…}, key: '9flq8e3f'}
  	hash: ""
  	key: "9flq8e3f"
  	pathname: "/about/tab3"
  	search: ""
  	state: {name: 'shao.yuhong', age: 25}
  	[[Prototype]]: Object
  */}
  ``````

  

### 传参跳转

除了使用`Link` `NavLink` 传递参数，也可能使用编程式传参

``````jsx
<Link to="../tab3" state={{name:'shao.yuhong',age:25}}>跳转到tab3.jsx state方式</Link>
<Link to="../tab3?name=shao.yuhong&age=22">跳转到tab3.jsx search方式</Link>
<p onClick={
             ()=>navigateTo('../tab3?school=重庆师范大学',{state:{name:'shaoyuhong',age:22}})
               }>P标签事件跳转</p>
<p onClick={
             ()=>navigateTo('../tab3?school=重庆师范大学',{replace:true,state:{name:'shaoyuhong',age:22}})
               }>P标签事件跳转</p>
``````

**navigate提供的配置项**

``````js
navigate(url,{
  replace:false/true,
  state:{},
})
``````

除此之外

``````
navigate(1);//前进
navigate(-1);//后退
``````





### 嵌套路由

这是 React Router 最强大的功能之一，因此您不必弄乱复杂的布局代码。你的绝大多数布局都与 URL 的段耦合，React Router 完全接受了这一点。

路由可以相互嵌套，它们的路径也会嵌套（子继承父）。

```js
function App() {
  return (
    <Routes>
      <Route path="invoices" element={<Invoices />}>
        <Route path=":invoiceId" element={<Invoice />} />
        <Route path="sent" element={<SentInvoices />} />
      </Route>
    </Routes>
  );
}
```

此路由配置定义了三个路由路径：

- `"/invoices"`
- `"/invoices/sent"`
- `"/invoices/:invoiceId"`

**嵌套路由时,对应的父组件需要使用`<Outlet/>`标识需要展示的子组件的位置 **

``````html
 <BrowserRouter>
      <Routes>
        <Route path="/" element={<App></App>}>
            <Route path="about" element={<About></About>}></Route>
        </Route>
        <Route path="/404" element={<Error></Error>}></Route>
        <Route path="*" element={<Navigate to="/404"></Navigate>}></Route>
       
      </Routes>
    </BrowserRouter>

<!-- App.js -->
import { Outlet } from 'react-router-dom';

function App() {
  return (
    <div className="App">
        <h2>App.js入口文件</h2>
        <hr/>
        <Outlet/>
    </div>
  );
}
``````



## 索引路线

索引路由可以被认为是“默认子路由”。当父路由有多个子路由，但 URL 就在父路由的路径上时，您可能希望将某些内容渲染到插座中。

**子路由的默认展示 无需path路由， 通过设置index实现； `<Route index element={<Activity />} />`**

考虑这个例子：

```js
function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route path="invoices" element={<Invoices />} />
        <Route path="activity" element={<Activity />} />
      </Route>
    </Routes>
  );
}

function Layout() {
  return (
    <div>
      <GlobalNav />
      <main>
        <Outlet />
      </main>
    </div>
  );
}
```

此页面在“/invoices”和“/activity”中看起来很棒，但在“/”中它只是一个空白页面，`<main>`因为那里没有要渲染的子路由。为此，我们可以添加一个索引路由：

```js
function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Activity />} />
        <Route path="invoices" element={<Invoices />} />
        <Route path="activity" element={<Activity />} />
      </Route>
    </Routes>
  );
}
```

现在在“/”处，`<Activity>`元素将呈现在插座内。

您可以在路由层次结构的任何级别拥有一个索引路由，当父级匹配但其他子级不匹配时将呈现该索引路由。

```js
function App() {
  return (
    <Routes>
      <Route index element={<Home />} />
      <Route path="dashboard" element={<Dashboard />}>
        <Route index element={<DashboardHome />} />
        <Route
          path="invoices"
          element={<DashboardInvoices />}
        />
      </Route>
    </Routes>
  );
}
```

## 相对链接

相对`<Link to>`值（不以 a 开头`/`）是相对于渲染它们的路径的路径。下面的两个链接将链接到`/dashboard/invoices`并且`/dashboard/team`因为它们在`<Dashboard>`. 当您更改父 URL 或重新排列组件时，这非常好，因为您的所有链接都会自动更新。

```js
import {
  Routes,
  Route,
  Link,
  Outlet,
} from "react-router-dom";

function Home() {
  return <h1>Home</h1>;
}

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <nav>
        <Link to="invoices">Invoices</Link>{" "}
        <Link to="team">Team</Link>
      </nav>
      <hr />
      <Outlet />
    </div>
  );
}

function Invoices() {
  return <h1>Invoices</h1>;
}

function Team() {
  return <h1>Team</h1>;
}

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="dashboard" element={<Dashboard />}>
        <Route path="invoices" element={<Invoices />} />
        <Route path="team" element={<Team />} />
      </Route>
    </Routes>
  );
}
```

## “未找到”路线

当没有其他路由与 URL 匹配时，您可以使用`path="*"`. 此路由将匹配任何 URL，但具有最弱的优先级，因此路由器仅在没有其他路由匹配时才会选择它。

```js
function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="dashboard" element={<Dashboard />} />
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}
```

## 多组路由

尽管您应该只`<Router>`在应用程序中拥有一个，但您可以在[``](https://reactrouter.com/docs/en/v6/api#routes)任何需要它们的地方拥有尽可能多的。每个`<Routes>`元素独立于其他元素运行，并选择一个子路由进行渲染。

```js
function App() {
  return (
    <div>
      <Sidebar>
        <Routes>
          <Route path="/" element={<MainNav />} />
          <Route
            path="dashboard"
            element={<DashboardNav />}
          />
        </Routes>
      </Sidebar>

      <MainContent>
        <Routes>
          <Route path="/" element={<Home />}>
            <Route path="about" element={<About />} />
            <Route path="support" element={<Support />} />
          </Route>
          <Route path="dashboard" element={<Dashboard />}>
            <Route path="invoices" element={<Invoices />} />
            <Route path="team" element={<Team />} />
          </Route>
          <Route path="*" element={<NotFound />} />
        </Routes>
      </MainContent>
    </div>
  );
}
```

## 后裔`<Routes>`

你可以在任何你需要的地方渲染[一个``元素](https://reactrouter.com/docs/en/v6/api#routes)，包括在另一个组件树的深处`<Routes>`。这些将与任何其他的一样工作`<Routes>`，除了它们将自动构建在渲染它们的路径的路径上。如果这样做，请*确保在父路由路径的末尾放置一个 **。否则，当父路由的路径长于父路由的路径时，父路由将不会匹配 URL，并且您的后代`<Routes>`将永远不会出现。

```js
function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="dashboard/*" element={<Dashboard />} />
    </Routes>
  );
}

function Dashboard() {
  return (
    <div>
      <p>Look, more routes!</p>
      <Routes>
        <Route path="/" element={<DashboardGraphs />} />
        <Route path="invoices" element={<InvoiceList />} />
      </Routes>
    </div>
  );
}
```

仅此而已！我们没有在这里介绍所有 API，但这些绝对是您将使用的最常用的 API。如果您想了解更多信息，请继续学习[我们的教程](https://reactrouter.com/docs/en/v6/getting-started/tutorial)或浏览[完整的 API 参考](https://reactrouter.com/docs/en/v6/api)。



## 其它杂项

### useInRouterContext

useInRouterContext()  只要是在`<browseRouter>`中，该函数返回true



### useNavigationType

NavigationType = "POP" | "PUSH" | "REPLACE";    通过useNavigationType（）返回当前的导航类型或用户如何来到当前页面

**POP代表页面第一次刷新或者第一次进入时，返回POP**



### useResolvedPath

将字符串解析为URL

``````js
import { Link,useNavigate,useNavigationType,useResolvedPath } from 'react-router-dom';
useResolvedPath('?keyword=test#h1')
``````

result：*{pathname: '/about/tab2', search: '?keyword=test', hash: '#h1'}*

​		hash**: "#h1"

​		pathname**: "/about/tab2"

​	   search**: "?keyword=test"

​	   [[Prototype]]: Object

---



## react父传子组件

组件通过{...info}将整个对象结构传给子组件

``````js
const info = {
   name:'shaoyuhong',
   age:22,
   school:'重庆师范大学'
}
 <About {...info}></About>
 <About name={info.name} age={info.age} school={info.school}></About>
``````



### 设置props的类型约束

``````js
About.propTypes = {
     age:PropTypes.number.isRequired
}
About.defaultProps = {
     money:'22K'
}
``````

将类型约束挂载到实例类上是一种方式，另外一种方式是将约束放到类的静态方式中，参考下

``````js
class About extends Component{
   static propTypes = {
        age:PropTypes.number.isRequired
   }
   static defaultProps = {
        money:'22K'
   }
    constructor(props){
        super(props);
        console.log(props);
    }
    render(){
        return (<div className='about'>
            <hr/>
             <h2>this is about.js</h2>
             <h3>接收的参数:{JSON.stringify(this.props)}</h3>
        </div>)
    }
}
``````

参考react网址：[https://react.docschina.org/docs/typechecking-with-proptypes.html](https://react.docschina.org/docs/typechecking-with-proptypes.html)



## ref的三种方式

* 字符串的形式 ***该方式不建议使用，会在将来版本中或废弃***

  ` <button className='btn' ref="submitBtn" onClick={this.log}>提交</button>`

  取当前dom时

  **this.refs.submitBtn**

* 回调函数的方式

  ` <button className='btn' ref={dom=>this.btn = dom} onClick={this.log}>提交</button>`

  取当前dom时

  **this.btn**

* 使用react.createRef() 创建ref

  import {createRef} from 'react';

   constructor(props){

  ​    super(props);

  ​    this.submit = createRef();

    }

  ` <button className='btn' ref={this.submit } onClick={this.log}>提交</button>`

  取当前dom时

  **this.submit.current**

### 设置对象时，当属性名是变量时

``````js
 const keys = 'name';
 this.setState({
       [keys]:'xxx'
       })
``````



## 高阶函数定义

传入的参数是一个函数 or  函数内部的表达式返回了一个函数



## 函数柯里化

通过函数调用返回函数继续处理的方式，实现多次传入参数最终统一处理

``````js
   function a(num1){
            return function(num2){
                 return function(num3){
                       return num1+num2+num3;
                 }
            }
       }
       console.log(a(1)(2)(3));
``````



## SPA

spa：single page application  单页面应用

* 整个页面只有一个完整的html页面
* 页面切换不会刷新，通过H5中的pushState和replaceState实现对URL修改
* 数据通过ajax获取



## this.setState

* this.setState({[变量] : 值 })

* this.setState((prevState,props)=>{

   	   return {

      ​     count:prevState.count + 1  

     }

  });



## 路由懒加载

打包是个非常棒的技术，但随着你的应用增长，你的代码包也将随之增长。尤其是在整合了体积巨大的第三方库的情况下。你需要关注你代码包中所包含的代码，以避免因体积过大而导致加载时间过长。

为了避免搞出大体积的代码包，在前期就思考该问题并对代码包进行分割是个不错的选择。 代码分割是由诸如 [Webpack](https://webpack.docschina.org/guides/code-splitting/)，[Rollup](https://rollupjs.org/guide/en/#code-splitting) 和 Browserify（[factor-bundle](https://github.com/browserify/factor-bundle)）这类打包器支持的一项技术，能够创建多个包并在运行时动态加载。

对你的应用进行代码分割能够帮助你“懒加载”当前用户所需要的内容，能够显著地提高你的应用性能。尽管并没有减少应用整体的代码体积，但你可以避免加载用户永远不需要的代码，并在初始加载的时候减少所需加载的代码量。

#### React.lazy

`React.lazy` 函数能让你像渲染常规组件一样处理动态引入（的组件）。

**使用之前：**

```js
import OtherComponent from './OtherComponent';
```

**使用之后：**

```js
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

一定是配合` <Suspense fallback={<div>Loading...</div>}> {element}  </Suspense>`使用，在网页加载太慢时，将优先展示fallback中的组件 

#### 在v6路由中如何 使用？

router.js

``````js
import { BrowserRouter, HashRouter, Routes, Route,Navigate } from 'react-router-dom';
const Error = lazy(()=>import('@/views/404'));
const About = lazy(()=>import('@/views/about'));
const Message = lazy(()=>import('@/views/message'));
const AboutView1 = lazy(()=>import('@/views/about_child1'));
const AboutView2 = lazy(()=>import('@/views/about_child2'));
const AboutView3 = lazy(()=>import('@/views/about_child3'));
const CanvasTest = lazy(()=>import('@/views/canvas'));


const routers = [
            {
               path:'message',
               element:<Message/>
            },
            {
                path:'ctx',
                element:<CanvasTest/>
             },
            {
                path:'about/*',
                element:<About/>,
                children:[
                    {
                        path:'tab1',
                        element:<AboutView1/>
                    },
                    {
                        path:'tab2',
                        element:<AboutView2/>
                    },
                    {
                        path:'tab3',
                        element:<AboutView3/>
                    },
                    {
                        path:'*',
                        element:<Navigate to="tab1"></Navigate>
                    }
                ]
            },
            {
                path:'404',
                element:<Error/>
            },
            {
                path:'*',
                element:<Navigate to="message"></Navigate>
            }
]
export default routers;
``````



app.js

``````jsx
import React, { Suspense } from 'react';
function App() {
  const element = useRoutes(RoutersList); //使用路由配置表

  return (
    <div className="app">
      <main>
        <nav></nav>
        <section>
          <Suspense fallback={<div>Loading...</div>}>
            {element}
          </Suspense>
        </section>
      </main>
    </div>
  );
}

``````



[参考react官方:https://zh-hans.reactjs.org/docs/code-splitting.html](https://zh-hans.reactjs.org/docs/code-splitting.html)



## WithRouter

当某个组件没有在路由配置中，如`<Footer>` `<Header>`类型组件，在公共的`layout`中，这类组件并不在路由配置中，此时这两个组件没有**this.props.history**,使用withRouter(Footer)可以拥有路由实例方法和属性

针对V5 react-react-dom，v6可以直接使用useNavigate()



## 针对react class类式组件的父孙间传值

* 需要一个全局公共变量、可以是模块化导出的变量const ThemeContext = React.createContext('');

* 父组件使用<ThemeContext.provider value={值}></ThemeContext.provider>向下暴露值 

  ``````jsx
      <moduleC.Provider value={fullName}>
              <D></D>
          </moduleC.Provider>
  ``````

* 子组件接收 **只针对类式组件  static contextType = ThemeContext**   结果将在**this.context中**

  ``````jsx
  import E from './e';
  import { moduleC } from '@/comm';
  import React from 'react';
  class D extends React.Component {
      static contextType = moduleC;
      constructor(props) {
          super(props);
      }
      render() {
          console.log(this.context);
          return (<div className="d">
              <h2>这是D组件</h2>
              <h3>接收到的值:{this.context}</h3>
              <E></E>
          </div>)
      }
  
  }
  export default D;
  ``````



## 函数式组件中的每一次update更新，都将触发整个函数执行

useState中的值第一次执行后会被暂存，update更新时，将使用上一次暂存的值，而不是再重新执行useState(默认值)中的值 



## puleComponent

`React.PureComponent` 与 [`React.Component`](https://zh-hans.reactjs.org/docs/react-api.html#reactcomponent) 很相似。两者的区别在于 [`React.Component`](https://zh-hans.reactjs.org/docs/react-api.html#reactcomponent) 并未实现 [`shouldComponentUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#shouldcomponentupdate)，而 `React.PureComponent` 中以浅层对比 prop 和 state 的方式来实现了该函数。

如果赋予 React 组件相同的 props 和 state，`render()` 函数会渲染相同的内容，那么在某些情况下使用 `React.PureComponent` 可提高性能。



## shouldComponentUpdate

shouldComponentUpdate(nextProps,nextState)

* 返回true,子组件更新

* 返回false,子组件不更新

  **通过对比当前this.props.xxx的值与下一次nextProps.xxx的值,true更新，false不更新**

``````jsx
<!--Parent-->
<Child math={this.state.math}></Child>

<!-- Child -->
import React, { Component,PureComponent } from 'react'
class Child extends Component{
     constructor(props){
          super(props);
     }
     shouldComponentUpdate(nextProps, nextState){
          return nextProps.math !== this.props.math;
     }
     render(){
          console.log('我是child.js render方法');
         return (<section className="child">
               <h1>Child.js</h1>
               <h3>{this.props.math}</h3>
         </section>);
     }
}
export default Child;
``````

#### 如果是更为复杂的对象时怎么办？

**考虑使用puleComponent**

## react中的插曹高级玩法

``````jsx
<!--App.jsx-->
<div>
  <Child math={this.state.math}>
        <Sun></Sun>
   </Child>
</div>
``````

***有一个需求，我希望不动结构的情况下，sun传入一个chlid组件中的state数据***

如何能解决这个问题？

```````jsx
<!--App.jsx-->
<div>
    <Child math={this.state.math} renderFn = {(slotName)=><Sun slotName={slotName}></Sun>}>
    </Child>
</div> 
```````

``````jsx
<!--Child.jsx-->
import React, { Component,PureComponent } from 'react'
class Child extends Component{
     constructor(props){
          super(props);
     }
     state = {
          slotName:'我是chlid.js中的slot数据'
     }
     render(){
        
         return (<section className="child">
               <h1>Child.js</h1>
               <h3>{this.props.math}</h3>
               {this.props.renderFn(this.state.slotName)}
         </section>);
     }
}
export default Child;
``````

**在App.jsx中传入一个自定义函数，函数返回Child组件**

**这样，Child.jsx中通过this.props.renderFn()调用就可以把标签放到指定的位置 **

**this.props.renderFn(child.jsx中的state)传入后，App.jsx自定义函数接收参数，并往Sun组件传入**

***OK啦***

## 高阶组件问题

``````js
// 此函数接收一个组件...
function withSubscription(WrappedComponent, selectData) {
  // ...并返回另一个组件...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ...负责订阅相关的操作...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
``````

[react官方文档 高阶组件：https://zh-hans.reactjs.org/docs/higher-order-components.html](https://zh-hans.reactjs.org/docs/higher-order-components.html)

与vue中的mixins非常相同，可以集成公共方法，数据
