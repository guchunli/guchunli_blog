---
title: ReactiveNative学习笔记
date: 2017-05-21 11:39:45
categories: 笔记
tags: [iOS,ReactiveNative]
---

# 安装
必须安装：Xcode，Homebrew，node,npm
npm其实是Node.js的包管理工具,已经在Node.js安装的时候顺带装好了。
可选：React Native的命令行工具,IDE(Atom/Nuclide/Webstorm/Sublime),watchman,Flow
```
//brew
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew -v
//node
brew install node
//React Native的命令行工具
npm install -g react-native-cli
//watchman
brew install watchman
//flow
brew install flow
```

# 创建项目
```
$ react-native init AwesomeProject
```

运行项目:使用命令行或Xcode
```
react-native run-ios
react-native run-android
````

运行项目时如果遇到`<React/RCTBundleURLProvider.h>” file not found`问题，可按如下方法试着解决：
* 把项目里面的 node_modules 文件夹删除掉
* npm install
* react-native upgrade
* clean,run


# 基础
## JavaScript
(1){}表示定义一个对象
(2)var,let,function,弱类型,typeof(),isNaN()
数据类型：Boolean,null,undefined,Number,String,Symbol,Object
false:false,undefined,NaN,null,"",0
显式类型转换：parseInt()
隐式类型转换：==,-,*,/
数组：var a = ["1","2","3"]
     var a = new Array("1","2","3")
     var a = Array("1","2","3")
字典：{}
> JS:基于原型(所有对象均为实例,通过构造器函数来定义和创建一组对象) 
> OC：基于类
(3)Promises(ES6)
JS本身是单线程的语言，它要实现异步都是通过回调函数来实现的，JS中引入了Promises来处理异步和延迟操作。
    promise有以下几种状态：
    * pending，最初的状态
    * fulfilled，执行成功
    * rejected，执行出错
    * settled，执行结束了，失败(rejected)或者成功(fulfilled)
(4)箭头函数(ES6)
```
(s) => s.length 等同于 function(s){ return s.length }
```

普通函数.bind(this) 来把内部函数中的this绑定到了外部函数去。
箭头函数里的this还是原来的this，不需要额外绑定。
(5)模板字符串(ES6)
```
var html = `公司名：${name}   
简介：${desc}`;

等同于

var tpl = '公司名：' + name + '\n'+
'简介：'+ desc;
```

## React基础
### 组件（component）
是React类的基类，进行视图展示。
* 组件的属性(prop)：组件之前通过标签的属性来传递数据，由父组件传递给子组件(单向的属性传递)
* 组件的状态(state)
    + 每次调用setState时都会更新组件的状态，触发render函数进行渲染更新界面（render方法是被异步调用的，这可以保证同步的多个setState方法只会触发一次render）
    + 组件自己不可以自己修改props（即：props可认为是只读的），只可由其他组件调用它时在外部修改
* createClass：创建一个component
* render()：返回的的 JSX 模板需要一个根元素包裹起来
返回一个根的视图，用来渲染实际的Component可视部分
* getInitialState()：只能在用React.createClass创建的时候使用
在Component被加载之前调用一次，这个方法的返回值会被设置为this.state
* getDefaultProps()：在Class 创建的时候，调用一次，这个方法在调用的时候，任何实例还没有被创建
返回的任何Object 对象，在各个实例中是共享的

* 生命周期
![组件生命周期](http://o8cfktdb3.bkt.clouddn.com/componentLifeCycle.png)

(1)创建：getDefaultProps(ES6:defaultProps)->
(2)实例化：getInitialState(ES6:constructor)->componentWillMount->render->componentDidMount->
(3)运行更新阶段：
    + 运行中->props改变->componentWillReceiveProps->
    + 运行中->state改变->
shouldComponentUpdate->
    + false->运行中
    + true->componentWillUpdate->render->componentDidUpdate->运行中
(4)销毁阶段：Unmount(卸载)->componentWillUnmount

|            生命周期           |            调用次数            | 能否使用 setSate()            |
|------------------------------|------------------------------|------------------------------
|defaultProps / getDefaultProps|         1(全局调用一次)       |否|
|constructor / getInitialState | 1            |否|
|componentWillMount            | 1            |是|
|render                        | >=1          |否|
|componentDidMount             | 1            |是|
|componentWillReceiveProps     | >=0          |是|
|shouldComponentUpdate         | >=0          |否|
|componentWillUpdate           | >=0          |否|
|componentDidUpdate            | >=0          |否|
|componentWillUnmount          | 1            |否|

(1)getDefaultProps(ES6:defaultProps):初始化一些默认的属性，一个控件可以利用this.props获取并初始化它的属性
    + ES5:getDefaultProps:function(){},propTypes:{},
    + ES6:static defaultProps={};static propTypes={};

(2)constructor(props) / getInitialState():状态初始化
    + ES5:getInitialState: function(){}
    + ES6:constructor(props){}
(3)componentWillMount():准备加载组件，业务初始化操作，也可以设置组件状态
(4)render():渲染界面，返回JSX或其它组件来构成DOM，只能返回一个顶级元素
(5)componentDidMount():组件加载成功并被成功渲染，一般会做一些网络请求等加载数据的操作

(6)componentWillReceiveProps():组件接收到新的props会触发，可以调用setState()来完成对state的修改，nextProps 是即将被设置的属性，旧的属性还是可以通过 this.props 来获取
(7)shouldComponentUpdate():返回布尔值（决定是否需要更新组件）
(8)componentWillUpdate():shouldComponentUpdate返回true或者调用forceUpdate之后触发此方法，会把 nextProps 和 nextState 分别设置到 this.props 和 this.state 中，开始准备更新组件。不能使用 this.setState 来修改状态。
(9)render()：再确定需要更新组件时，调用render，根据diff算法，渲染界面，生成需要更新的虚拟DOM数据。
(10)componentDidUpdate():可以在这个方法中做DOM操作，首次render之后调用componentDidMount，其它render结束之后都是调用componentDidUpdate。
(11)componentWillUnmount():组件从DOM中移除，可以做一些组件相关的清理工作，例如取消计时器、网络请求。
```
注意：绝对不要在componentWillUpdate和componentDidUpdate中调用this.setState方法，否则将导致无限循环调用。
```

组件更新的四种方式：
(1)首次Initial Render，即首次加载组件
(2)调用this.setState
(3)父组件发生更新
(4)调用this.forceUpdate强制更新

## statics
定义Components可以调用的静态方法


# RN与原生项目
启动开发服务器
```
react-native start
```

## 原生和React Native之间的通信方式
主要包括三部分：
* 属性
* 原生模块
* 原生UI组件封装

### 属性
(1)原生组件传递属性到React Native（原生->rn）
原生给JS传数据，主要依靠属性。
initialProperties必须是NSDictionary的一个实例。这一字典参数会在内部被转化为一个可供JS组件调用的JSON对象。
(2)从原生组件更新属性到React Native（原生->rn）
RCTRootView同样提供了一个可读写的属性appProperties。在appProperties设置之后，React Native应用将会根据新的属性重新渲染。当然，只有在新属性和之前的属性有区别时更新才会被触发。
你可以随时更新属性，但是更新必须在主线程中进行，读取则可以在任何线程中进行。
更新属性时并不能做到只更新一部分属性。我们建议你自己封装一个函数来构造属性。
注意：目前，最顶层的RN组件（即registerComponent方法中调用的那个）的componentWillReceiveProps和componentWillUpdateProps方法在属性更新后不会触发。但是，你可以通过componentWillMount访问新的属性值。
(3)从React Native传递属性到原生组件（rn->原生）


参考链接：[React Native开发](http://www.lcode.org/【react-native开发】react-native-for-android环境配置以及第一个实例/)
[React Native 简介与入门](http://www.jianshu.com/p/5b185df2d11a)

