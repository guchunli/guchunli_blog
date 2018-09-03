---
title: ReactNative基础知识学习笔记
date: 2017-07-06 11:30:28
categories: 笔记
tags: [ReactNative,基础]
toc: true
---

## JSX
* Browser.js:作用是将 JSX 语法转为 JavaScript 语法
* JSX 中不能使用 if else 语句，但可以使用 conditional (三元运算) 表达式来替代
* 注释需要写在花括号中:`{/*注释...*/}`
<!--more-->
* JSX 允许在模板中插入数组，数组会自动展开所有成员
* 要渲染 HTML 标签，只需在 JSX 里使用小写字母的标签名,要渲染 React 组件，只需创建一个大写字母开头的本地变量
* class->className  for->htmlFor
* 使用{}的情况：表达式，变量，对象，字典
```
style={styles.mainStyle}
var str = 'hello'
<Text>{str}</Text>
```
* `{{}}`:第一重大括号表示这是 JavaScript 语法，第二重大括号表示样式对象

```
<View style={{flex:1}}></View>
```

* 使用()的情况：返回组件
```
render(){
    return (
        <View>
        </View>
    )
}
```

## 判断平台进行不同操作
```
const instructions = Platform.select({
    ios: 'Press Cmd+R to reload,\n' + 'Cmd+D or shake for dev menu',
    android:
    'Double tap R on your keyboard to reload,\n' +
    'Shake or press menu button for dev menu',
});
```

## CSS样式
* 使用驼峰命名法
* style属性可以是一个普通的 JavaScript 对象
* 建议使用StyleSheet.create来集中定义组件的样式
* 后声明的属性会覆盖先声明的同名属性

### 创建一个样式表
```
const styles = StyleSheet.create({
container: {
    borderRadius: 4,
    borderWidth: 0.5,
    borderColor: '#d6d7da',
    },
    title: {
    fontSize: 19,
    fontWeight: 'bold',
    },
    activeTitle: {
    color: 'red',
    },
});
```

### 使用一个样式表
```
<View style={styles.container}>
    <Text style={[styles.title, this.props.isActive && styles.activeTitle]} />
</View>
```

* margin：第一个组件比较特殊，参照父组件，与父控件之间的间距。其他组件间距，相对于上一个组件
* padding：设置子控件与当前控件的位置，想设置自己的子控件相对自己的位置的时候使用
* 注意marginRight和width冲突，如果设置了width，marginRight无效
position
* absolute:绝对定位，相对父组件
> 使用：当想把一个已经设置了宽度的控件快速的放在左下角，右下角的时候
* relative: 相对定位，相对自己的原始位置
> 使用：当想相对自己做出一些改变的时候，采用相对定位,比如相对自己，往下移动一点

## FlexBox布局
1.flexDirection：row | row-reverse | column | column-reverse
决定了子元素在主轴方向的排列方式（此样式设置在父元素上），默认值是column
```
<View style={{flex: 1, flexDirection: 'column'}}>
    <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
    <View style={{width: 50, height: 50, backgroundColor: 'skyblue'}} />
    <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
</View>
```

2.justifyContent（no flex:1）：flex-start | flex-end | center | space-between | space-around | space-evenly
决定了子元素在主轴方向的对齐方式（此样式设置在父元素上），默认值是flex-start
```
<View style={{
flex: 1,
flexDirection: 'column',
justifyContent: 'space-between',
}}>
    <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
    <View style={{width: 50, height: 50, backgroundColor: 'skyblue'}} />
    <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
</View>
```

3.alignItems（nowrap,align-self）：flex-start | flex-end | center | stretch
决定了子元素在次轴方向的排列方式（此样式设置在父元素上），默认值为 stretch，子组件在侧轴方向被拉伸到与容器相同的高度或宽度
`次轴：与主轴垂直的轴，若主轴方向为row，则次轴方向为column`
`要使stretch选项生效的话，子元素在次轴方向上不能有固定的尺寸`
```
<View style={{
flex: 1,
flexDirection: 'column',
justifyContent: 'center',
alignItems: 'stretch',
}}>
    <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
    <View style={{height: 50, backgroundColor: 'skyblue'}} />
    <View style={{height: 100, backgroundColor: 'steelblue'}} />
</View>
```
4.alignSelf：auto | flex-start | flex-end | center | stretch
决定了元素在父元素的次轴方向的排列方式（此样式设置在子元素上），其值会覆盖父元素的alignItems的值。默认值为 auto，继承它的父容器的alignItems属性。如果没有父容器则为 "stretch"。

5.aligncontent（wrap）：stretch | flex-start | flex-end | center | space-between | space-around
6.flexWrap：wrap | no-wrap
决定子控件在父视图内是否允许多行排列，显示不全时是否自动换行。默认为nowrap
7.flex:子控件在主轴中占据几等分，任意数字，所有子控件flex相加，自己flex占总共多少，就有多少宽度.

## props,state
* props：一般用于自定义组件，大多数组件在创建时就可以使用各种参数来进行定制，用于定制的这些参数就称为props（属性）。
    * 在父组件中指定，而且一经指定，在整个组件的生命周期中都不再改变。通过this.props.name访问。
    * this.props.children表示组件的所有子节点

* state：以后想修改某个属性，就修改界面，就需要用state。
    * constructor中声明(ES6)，在setState中修改数据

> this.props 表示那些一旦定义，就不再改变的特性，而 this.state 是会随着用户互动而产生变化的特性。
> 通过state 来更新和修改数据，而子组件只能通过 props 来传递数据

定义state:
```
this.state = {
num:1,
};
```

修改state:
```
this.setState({
num : number
})
```

或者
```
this.setState({name: e.target.value}, function(){
console.log(this.state.name);
})
```

* 一切界面变化都是状态state变化
* state的修改必须通过setState()方法
    * this.state.likes = 100; // 这样的直接赋值修改无效！
    * setState 是一个 merge 合并操作，只修改指定属性，不影响其他属性
    * setState 是异步操作，修改不会马上生效

## 父子组件传值
### 父传子
* props:this.props.name
* ref:this.refs.son.receiveMsg("msg")
### 子传父
* 方法回调
父组件：定义一个处理接收到值的方法，把这个方法传递给子组件，并且绑定this
子组件：通过this.props拿到这个方法调用
### 无关联组件间传值
* 通知:组件1传值给组件2
组件1：
```
<Text onPress={()=>{
DeviceEventEmitter.emit('NotificationName',123);
}}></Text>
```

组件2：
```
componentDidMount() {

    this.age = 10;
    this.age = 20;

    console.log(this.age);

    // 定义属性,但是对这个属性的修改不会触发render
    this.lister = DeviceEventEmitter.addListener('NotificationName',(value)=>{
        this.setState({
            value:value
        });
    })
}

componentWillUnmount() {
    this.lister.remove();
}
```

## 组件生命周期
```
export default class RNDemo extends Component {
    render() {
        return (
        <View />
        );
    }
}
AppRegistry.registerComponent('React', () => RNDemo);
```

### 实例化
0.defaultProps:给props设置默认值
1.constructor:初始化state（只调用一次）
2.componentWillMount:即将加载组件调用，render前（只调用一次）
3.render:渲染组件
4.componentDidMount:组件加载完成调用，render后（只调用一次）
* 除了 render ，其他方法只调用一次
### 运行
`props改变`
5.componentWillReceiveProps:props改变调用
`state改变`
6.shouldComponentUpdate:props/state改变调用，可控制是否刷新界面
7.componentWillUpdate:组件即将更新调用（调用this.setState会循环）
8.render
9.componentDidUpdate:组件更新完成（调用this.setState会循环）
* 绝对不要在componentWillUpdate，componentDidUpdate中调用this.setState方法，否则将导致无限循环调用，在componentWillReceiveProps，shouldComponentUpdate可以
### 销毁
10.componentWillUnmount:组件即将销毁，可移除观察者，清空数据等

## propTypes
* 可以实现类型检查，当传入错误的属性值，会报警告，但是不会报错
* 用PropTypes定义属性，外界使用的时候，会有提示。
* 必须要用static修饰，否则无效
    * static：用来定义类方法或者类属性，定义类的方法和属性，生成的对象就自动有这样的属性了。
* 只能用于React框架的自定义组件，默认JS是没有的，因为它是React框架中的。
### 类型检查：当传入错误的属性值，会报警告，但是不会报错
导入：`import React, { Component,PropTypes } from 'react';`
* 定义属性
```
static propTypes = {
name:PropTypes.string,
age:PropTypes.number
}
```
```
propTypes: {
// 可以声明 prop 为指定的 JS 基本数据类型，默认情况，这些数据是可选的
optionalArray: React.PropTypes.array,
optionalBool: React.PropTypes.bool,
optionalFunc: React.PropTypes.func,
optionalNumber: React.PropTypes.number,
optionalObject: React.PropTypes.object,
optionalString: React.PropTypes.string,

// 可以被渲染的对象 numbers, strings, elements 或 array
optionalNode: React.PropTypes.node,

//  React 元素
optionalElement: React.PropTypes.element,

// 用 JS 的 instanceof 操作符声明 prop 为类的实例。
optionalMessage: React.PropTypes.instanceOf(Message),

// 用 enum 来限制 prop 只接受指定的值。
optionalEnum: React.PropTypes.oneOf(['News', 'Photos']),

// 可以是多个对象类型中的一个
optionalUnion: React.PropTypes.oneOfType([
React.PropTypes.string,
React.PropTypes.number,
React.PropTypes.instanceOf(Message)
]),

// 指定类型组成的数组
optionalArrayOf: React.PropTypes.arrayOf(React.PropTypes.number),

// 指定类型的属性构成的对象
optionalObjectOf: React.PropTypes.objectOf(React.PropTypes.number),

// 特定 shape 参数的对象
optionalObjectWithShape: React.PropTypes.shape({
color: React.PropTypes.string,
fontSize: React.PropTypes.number
}),

// 任意类型加上 `isRequired` 来使 prop 不可空。
requiredFunc: React.PropTypes.func.isRequired,

// 不可空的任意类型
requiredAny: React.PropTypes.any.isRequired,

// 自定义验证器。如果验证失败需要返回一个 Error 对象。不要直接使用 `console.warn` 或抛异常，因为这样 `oneOfType` 会失效。
customProp: function(props, propName, componentName) {
if (!/matchme/.test(props[propName])) {
return new Error('Validation failed!');
}
}
},
```

### 设置初始化值
```
static defaultProps = {
name:'xiaoming',
age:20
}
```

## 基本组件
### View
###TouchableOpacity：默认点击区域是所有子控件的区域,因为默认一个组件的尺寸由子控件决定
* activeOpacity:0-1 1-不透明
* 点击事件：onPress|onLongPress|onPressIn|onPressOut
* disabled:true|false
```
<TouchableOpacity activeOpacity={0.7}>
    <View style={styles.childSytle}>
    </View>
</TouchableOpacity>
```

### Text
* numberOfLines
* selectable:true|false 是否允许长按选择文本
* suppressHighlighting:true|false 是否允许按下时有灰色阴影
* onPress:文字点击事件  onPress={()=>{}}
* color/fontSize/fontWeight/lineHeight/textAlign
### Button
* 不可设置样式，一般用text自定义
* color/disabled/onPress/title
### TextInput
* 默认无边框
* autoFocus/blurOnSubmit/editable/maxLength/multiline/placeholder
* keyboardType/returnKeyType/clearButtonMode
* clearTextOnFocus/enablesReturnKeyAutomatically
* clear()
* 事件：onBlur/onFocus/onChange/onChangeText/onEndEditing/onKeyPress/onSubmitEditing
### Image
iOS中的2x,3x图片同样适用，如果存在a@2x.png与a@3x.png，写a.png即可。
同时兼容iOS和安卓平台的图片，如果存在a.ios.png与a.android.png，同样写a.png即可。
* source/defaultSource
#### 本地图片存放路径
1.放在与index.ios.js同目录下的Img文件夹
```
<Image source={require('./Img/chaolan.jpeg')} style={styles.imageSytle}/>
//Xcode的asset类目中，或是放置在Android的drawable目录
<Image source={{uri:'wukong'}} style={styles.imageSytle}/>
<Image source={{uri:'http://img01.youxiaoshuo.com/portal/201703/21/083647y43dl1j14s8s3g99.jpg', cache: 'only-if-cached'}} style={styles.imageSytle}/>
```

2.iOS：可以存放到iOS项目中，打开iOS项目，存入到Images.xcassets文件中
Android：可以存放到安卓项目中,必须放入drawable-xxhdpi文件中

* 通过URI加载网络图片，必须设置图片尺寸，否则不显示
* 通过URI加载网络图片，iOS端可以添加缓存策略(default/reload/force-cache/only-if-cached)
* blurRadius
* resizeMode:cover|contain|stretch|repeat|center
* 事件：onLoad/onLoadStart/onLoadEnd/onProgress/onError
### ScrollView
* horizontal/showsHorizontalScrollIndicator/showsVerticalScrollIndicator/alwaysBounceHorizontal/alwaysBounceVertical
* automaticallyAdjustContentInsets
* bounces/bouncesZoom/contentInset
* scrollEventThrottle/stickyHeaderIndices(与horizontal={true}冲突)
* 事件：onScrollBeginDrag/End/onMomentumScrollBegin/End/onScrollAnimationEnd/onScroll/scrollEventThrottle

## 自定义类
1.自定义类
```
export default class Person{}

```

2.继承extends
```
class HomeView extends Component
```

3.使用：var p = new Person();

## ScrollView
* 不能通过scrollView获取,因为在RN中,滚动的时候,不会给scrollView组件的contentOffset属性赋值,只能通过nativeEvent事件获取
```
_onScroll(e) {
// console.log('滚动的时候调用');
//  var scrollView = this.refs.scrollView;
//  console.log(scrollView.props.contentOffset);

var nativeEvent = e.nativeEvent;
console.log(nativeEvent.contentOffset);
}
```

## ListView
0.ListViewDataSource
```
构造函数可以接受下列四种参数（都是可选）：

getRowData(dataBlob, sectionID, rowID);
getSectionHeaderData(dataBlob, sectionID);
rowHasChanged(prevRowData, nextRowData);
sectionHeaderHasChanged(prevSectionData, nextSectionData);
```
1.创建数据源，给数据源设置数据
使用state保存数据源
### 不分组使用：cloneWithRows
```
constructor(props) {
    super(props);
    var data = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});

    this.state = {
        // 给数据源设置数据,创建新的数据,不过保存了之前的行改变方法属性
        dataSource: data.cloneWithRows(['row 1', 'row 2']),
    };
}
```

###  分组使用：cloneWithRowsAndSections
```
constructor(props) {
    super(props);
    var data = new ListView.DataSource({
        rowHasChanged:(r1,r2)=>r1 !== r2,
        sectionHeaderHasChanged:(s1,s2)=>s1 !== s2
    });
    var sectionData = {};
    this.state = {
        dataSource : data.cloneWithRowsAndSections(sectionData)
    };
}
```

2.实现数据源方法
```
<ListView dataSource={this.state.dataSource}
renderRow={this._renderRow.bind(this)}
renderHeader={this._renderHeader.bind(this)}
renderFooter={this._renderFooter.bind(this)}
renderSeparator={this._renderSeparator.bind(this)}
//renderSectionHeader={this._renderSectionHeader.bind(this)}
/>

_renderRow(rowData, sectionID, rowID, highlightRow) {
return (
<View>
<Text>{rowData}</Text>
</View>
);
}

_renderHeader() {
return (
<View>
<Text>头部视图</Text>
</View>
)
}

_renderFooter() {
return (
<View>
<Text>尾部视图</Text>
</View>
)
}

_renderSeparator(sectionID, rowID, adjacentRowHighlighted)  {
console.log(sectionID,rowID,adjacentRowHighlighted);
return (
<View style={{height:1,backgroundColor:'black'}}></View>
)
}

//如果是组视图，设置组数据
_renderSectionHeader(sectionData, sectionID) {
return (
<View>
<Text>{sectionID}</Text>
</View>
)
}
```

## FlatList
## SectionList
默认情况下每行都需要提供一个不重复的key属性。你也可以提供一个keyExtractor函数来生成key。
把这个属性添加到 <SectionList/> 里面
```
keyExtractor = {this._extraUniqueKey}   

_extraUniqueKey(item ,index){
return "index"+index+item;
}  
```

## 导航Navigator
如果找不到Navigator，安装Navigator所在的库：
```
npm install react-native-deprecated-custom-components --save
//yarn add react-native-deprecated-custom-components
```

项目导入：
```
import {Navigator} from 'react-native-deprecated-custom-components'
```

## Tabbar
### TabBarIOS
* 只可用于iOS平台
* badge可设置未读消息数
```
<TabBarIOS style={{flex:1,alignItems:'flex-end'}}>
<TabBarIOS.Item 
title='消息'
icon={require('./Img/home.png')}
badge={3}
onPress={()=>{
this.setState({
selectIndex:0
})
}}
selected={0==this.state.selectIndex}
>
<View>
<Text>首页</Text>
</View>
</TabBarIOS.Item>
</TabBarIOS>
```

### TabNavigator
* 可用于iOS和安卓
1.安装
```
npm install react-native-tab-navigator --save
```

2.导入
```
import TabNavigator from 'react-native-tab-navigator';
```

3.使用
* TabBarIOS: `icon={require('./Img/home.png')}`
* TabNavigator: renderIcon是传入一个函数，这个函数返回一个Image组件,如果是通过url加载，一定要记得设置尺寸，否则不显示
```
renderIcon={() => <Image source={require('./Img/indexicon_hone_after@3x.png')} style={styles.iconStyle}/>}
```

```
<TabNavigator}>
<TabNavigator.Item title='消息'
renderIcon={() => <Image source={require('./Img/indexicon_hone_after@3x.png')} style={styles.iconStyle}/>}
// renderBadge={3}
onPress={()=>{
this.setState({
selectIndex:0
})
}}
selected={0==this.state.selectIndex}
>
<View style={{flex:1,justifyContent:'center',alignItems:'center'}}>
<Text>首页</Text>
</View>
</TabNavigator.Item>
</TabNavigator>
```

## 主流架构搭建
react navigation

npm install react-navigation --save
import {TabNavigator} from 'react-navigation';

## 网络请求
* fetch: 发送请求
* then: 回调函数
* response: 请求到的json数据
* catch: 捕获异常
发送GET请求：
```
fetch('http://192.168.1.143:8080') // 发送GET请求
.then((response)=>response.json()) // 请求成功，把请求数据转换为 JSON
.then((json)=>{                    // 获取JSON数据做事情
console.log(json)
})
.catch((error)=>{                // 请求失败或者处理JSON数据失败，调用
console.log(error)
})
```

发送POST请求有三种方式：
* application/x-www-form-urlencoded: 参数是普通的参数拼接
* application/json: 参数是json格式
* application/form-data: 文件上传

### request模块

## 本地存储 AsyncStorage
* 异步的
* 只能存储字符串
* 把数据保存到沙盒中的Documents中,并生成manifest.json文件
* save/read/delete
```
AsyncStorage.setItem('object',JSON.stringify(object),(error)=>{});
AsyncStorage.getItem('object',(error,result)=>{});
AsyncStorage.removeItem('object',(error)=>{});
```

## 引导页
1.第一次进入界面，写个属性，记录下第一次加载。
2.每次启动，获取之前是否保存过第一次加载的属性，如果加载过，就显示主页，没加载过，就显示引导页

## 模块导出
* 自定义组件：继承自Component，必须使用class定义类
* 自定义类：可以不使用class定义

### 自定义组件导出
1.ES6/5
* ES6:一般使用默认组件，支持import导出
* ES5:一般使用非默认组件，支持require导出
2.默认组件/非默认组件
* 默认组件
```
//定义
export default class XXX extends Component
//外部使用
import XXX from '../Custom/XXX'
```

* 非默认组件
非默认组件：需要加入{}
```
//定义
export class XXX extends Component
//外部使用
import {XXX} from '../Custom/XXX'
```

### 自定义类
1.没有对象属性的自定义类
* 定义：class
* 导出：export default
2.有对象属性的自定义类
* 定义：function
* 导出：module.exports(也可以export default)

3.class/function
* class定义类：在类中声明任何属性和方法，都会自动生成对象的属性和方法
```
export default class XXX {}
```

* function定义类：要想定义属性和方法，属性和方法前面必须添加this
```
function XXX(data) {
this.data = data;
this.func = function () {}
}
```

4.导出自定义类的两种方式
* module.exports
```
function XXX(data) {}
module.exports = XXX;
```

* export default
```
export default function XXX(sectionID,rowData) {}
```
5.外部引用
```
import XXX from '../Custom/XXX'
```

### 自定义类的继承--call方法
call方法
1.交换方法调用
```
func1.call(func2);  //调用func1
```

2.交换方法调用者
```
c1.log.call(c2);    //c2调用c1的log方法
```

3.通过function自定义类没有继承功能，需要通过call方法自己实现
本质：交换方法调用者

## 项目中经常遇到的问题
### 文件导入路径问题（已废弃）
@providesModule放到第一行注释
```
/**
* @providesModule Common
*/
export default class Common {}
```

外部引用：
```
import Common from 'Common'
```

### 主头文件问题
1.创建主头文件
```
import Common from '../Common/GroupListView'
...

// 这句话的意思：把当前文件作为一个模块导出，模块里面有这些子组件
// 以后导入这个模块的时候，就能获取了这个模块里面的东西.
module.exports = {
GroupListView,
...
};
```

2.使用头文件
```
import Common from 'Common'
// 创建行模型
var item0 = new Common.GroupListView();
```

## 动画
### Animated
Animated封装了四个可以动画化的组件：View、Text、Image和ScrollView。
* 配置动画
* 组合动画
parallel
sequence
stagger
delay
* 联动多个动画值
* 插值：interpolate({inputRange:[0,1],outputRange:[0,100],})
* 跟踪动态值:leader,follower,ValueXY
* 输入事件：Animated.event
* 使用原生动画驱动：`useNativeDriver:true`

### LayoutAnimation
```
LayoutAnimation.spring();
this.setState({w: this.state.w + 15, h: this.state.h + 15})
```

## 定时器
* setTimeout,clearTimeout
* setInterval,clearInterval
* setImmediate,clearImmediate
* requestAnimationFrame,cancelAnimationFrame

requestAnimationFrame():用来执行在一段时间内控制视图动画的代码
setTimeout/setInterval/setImmediate:稍后执行代码，可能会延迟当前正在进行的动画
runAfterInteractions():稍后执行代码，不会延迟当前进行的动画
```
InteractionManager.runAfterInteractions(() => {
// ...需要长时间同步执行的任务...
});
```

> *** 注意：在unmount方法中清除定时器

## setNativeProps

## 调试
iOS模拟器上，`cmd+D`为摇晃手势
启动模拟器：
```
react-native run-ios --simulator "iPhone 7"
```

* Reload:刷新JS文件
* Debug JS Remotely：在Chrome中调试JS代码
会自动打开`http://localhost:8081/debugger-ui`，打开开发者工具可以查看console输出，以及打断点调试JS脚本。
* Enable Live Reload：自动刷新，实时加载，应用更新时需要刷新当前页面，可以看到明显的全局刷新效果。
* Enable Hot Reloading：热加载，基本上看不出刷新的效果，类似于局部刷新。
* Start Systrace
* Show Inspector
* Show Pert Monitor

必须重新编译应用才能生效：
* 增加了新的资源，如图片
* 更改了原生代码

* console.error()可触发红屏报错
* 屏蔽指定警告：ignoreWarnings(['Warning:']);

* 访问控制台日志：
```
react-native log-ios
react-native log-android
```

还可以通过Debug->Open System Log查看日志

* React Developer Tools
```
npm install -g react-devtools  //安装
react-devtools  //启动
```

* 真机调试
(1)退出所有终端打开的页面
(2)手机与电脑连到同一无线网下
(3)将AppDelegate中jsCodeLocation的URL地址中的`localhost`改成电脑的IP地址，然后打开`Debug JS Remotely`
(4)解决https问题
(5)更新node,npm版本到最新
(6)关闭防火墙

## 自动化测试
```
$ cd react-native
$ npm test
$ npm run flow
$ ./scripts/objc-test-ios.sh
$ ./scripts/test-manual-e2e.sh
```

## 性能
只在开发环境时打印日志
```
if (!__DEV__) {
    global.console = {
        info: () => {},
        log: () => {},
        warn: () => {},
        debug: () => {},
        error: () => {},
    };
}
```

## 发布应用
1.edit scheme->Run->release release版本会自动禁用开发者菜单，同事讲JS文件和静态图片打包压缩后放到包中
2.热更新：[pushy](https://github.com/reactnativecn/react-native-pushy/blob/master/docs/guide.md)


[袁峥讲ReactNative-系列](http://www.jianshu.com/p/504a26d094b2)
[React学习资源汇总](http://blog.xieliqun.com/2016/11/06/react-study/#more)
[React Native开发错误警告处理总结（已解决 ！持续更新](http://www.jianshu.com/p/98c8f2a970eb)
