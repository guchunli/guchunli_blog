---
title: redux学习笔记
copyright: true
date: 2018-10-30 19:18:22
categories: 笔记
tags: [ReactNative]
toc: true
---

在Javascript中，不可变操作有三种不同的实现方式：
1.使用Immutable.js创建不可变的数据结构。
2.使用JavaScript库(如Underscore和Lodash)来执行不可变的操作。
3.使用ES6方法执行不可变操作，`b = Object.assign([],a)`，也可以使用操作符(...)执行不可变操作。
<!--more-->

## 组成部分
1.store：在Redux中，所有的数据（比如state）被保存在一个被称为store的容器中 → 在一个应用程序中只能有一个。store本质上是一个状态树，保存了所有对象的状态。任何UI组件都可以直接从store访问特定对象的状态。
2.action：要通过本地或远程组件更改状态，需要分发一个action。分发在这里意味着将可执行信息发送到store。当一个store接收到一个action，它将把这个action代理给相关的reducer。
3.reducer：reducer是一个纯函数，它可以查看之前的状态，执行一个action并且返回一个新的状态。
4.type - 一个简单的字符串常量，例如ADD, UPDATE, DELETE等。
5.payload - 用于更新状态的数据。

Redux工具调试：time-travelling tool

## 安装React
react-redux帮助你完成数据订阅
redux-thunk可以放你实现异步action
redux-logger是redux的日志中间件。
```
npm install --save redux react-redux redux-thunk
npm install --save-dev redux-logger
```

## 注意八个问题
1.
第一种更新本地状态的方式：this.setState()
通过this.setState()方法来更新状态.状态对象(state object)的更新过程是一次浅合并.因此你可以只更新本地状态中特定的某一部分状态,而其余的状态都不会受到影响.一旦状态更新完,组件就会重新渲染.
this.setState()方法是异步更新本地状态的.
问题：当你用this.setState()更新本地状态时,而本地状态又在异步执行更新完成之前改变了,这时你就操作了一个旧的状态.

第二种更新React本地状态的方式.
this.setState()函数可以接受一个函数作为参数而非对象.而这个回调函数的调用会传入在当下this.setState()异步执行后的本地状态作为参数.这个回调执行的时候就能获取到当前最新的,可信赖的本地状态.所以当你需要根据之前的本地状态来更新时,就可以使用传入函数给this.setState()而非对象.
this.setState(previousState => ({ counter: previousState.counter + 1 }));

另外,这也适用于依赖props的更新.在异步执行更新之前,从父组件获取到的props也有可能被改变过.所以传入this.setState()的回调会被注入第二个参数props.
this.setState((prevState, props) => ...);

使用回调函数时的另外一个好处是能单独对状态更新进行测试.简单地把this.setState(fn)中的回调函数提取出来并导出(export)即可.这个回调函数应该是一个纯函数,你可以根据输入进行简单的输出测试.

2.React的State和Props
Props随着组件树往下传递,而State则由组件自己维护,此外通过往上层组件冒泡的函数可以改变组件中的State,而更新过后的State又以Props的形式往下传递.
组件可以管理很多State,这些State可以作为Props往下传递给子组件并且Props中可以传递函数给予子组件修改父组件的State.

3.提取React的State
往上提取：当更多的组件需要用到特定的State时,可以往上提取State,直到需要访问该State的组件的公共组件.此外,本地状态管理依然保持着可维护性,因为一个组件根据自身需求管理尽可能多的状态。
往下提取：如果组件或其子组件不需要该State的话,则可以往下提取State放置在需要的地方.就不会出现传递Props需要遍历整个组件树的情况了.

4.React高阶组件(HOC)
高阶组件是React中一种高级设计模式.你可以使用它来抽象功能,并将其作为其他多个组件的可选功能重用.高阶组件接受一个组件和其他可选配置作为参数并返回一个增强版本的组件.它建立在Javascript的高阶函数(返回函数的函数)的原则之上.

5.React上下文(Context)

6.React Stateful组件(带状态的组件)
React中有两种声明组件的方式: ES6类组件和函数(不带状态)组件.
（1）类组件：React类组件是可以保持State和能出发声明周期函数的.这些组件能访问this.state和调用this.setState()方法.这就说明了ES类组件是能带状态的组件.而如果他们不需要保持本地State的话,也可以是无状态组件.通常无状态的类组件也会需要使用声明周期函数.
（2）函数组件：一个不带状态的函数组件仅仅是一个接收Props并返回JSX的函数.其中不保持任何的State也不会触发任何React生命周期函数.顾名思义就是无状态的.

结论：`只有ES6类组件是可以带状态的,但是他们也可以是无状态的.而函数组件则是无状态的.`
还可以使用高阶组件来添加状态到React组件上.当使用高阶组件时,你可以选择传递任意局部状态到React组件中去.

7.容器组件与展示组件
组件分为两类:容器组件和展示组件.
容器组件负责描述组件是如何工作的,展示组件负责组件内容的展示.容器组件一般是一个类组件,因为容器组件是需要管理本地状态的.
展示组件是一个无状态函数组件,因为一般只用于展示Props和调用从父组件传递过来的函数.

8.MobX & Redux 

参考资料：
[Redux入门教程（快速上手）](https://segmentfault.com/a/1190000011474522)
[在使用Redux前你需要知道关于React的8件事](https://segmentfault.com/a/1190000013725571)
[在React中使用Redux](https://www.jianshu.com/p/06f5285e2620)
