---
title: Swift项目中的注意点
date: 2017-08-03 10:16:56
categories: 学习
tags: [Swift项目]
toc: true
---

## swift与OC的区别
* 注意: Swift开发中定义变量时候先用let(常量), 只有真正需要修改时才用var
* 由于编译器可以通过赋值的类型自动推导出数据的真实类型, 所以在Swift开发中, 能不写数据类型就不写数据类型
* Swift 对类型校验更加严格，不同类型的变量不允许直接计算
* Swift语法允许我们直接修改一个对象的结构体属性的成员
* 一般情况下只有需要区分两个变量, 或者在闭包中访问外界属性才需要加上self，可以提醒程序员主动思考当前self会不会形成循环引用
```
titleLabel?.frame.origin.x = 0
```

* 一般在本类中的扩展中实现代理方法
```
extension QRCodeViewController: UINavigationControllerDelegate, UIImagePickerControllerDelegate
```

# day 1
## 创建项目准备工作
### 删除模板文件

* ViewController.swift
* Main.storyboard
* LaunchScreen.xib

### 创建项目结构

#### 主目录 `Classes`

#### 二级目录

| 目录名 | 说明 |
| -- | -- |
| Main | 主要 |
| Home | 首页 |
| Message | 消息 |
| Discover | 发现 |
| Profile | 我 |
| Tools | 工具类 |

### 创建项目文件

#### Main

BaseTabbar/BaseNavigation/BaseViewController

#### 功能模块

| 目录 | Controller |
| -- | -- |
| Home | HomeTableViewController.swift |
| Message | MessageTableViewController.swift |
| Discover | DiscoverTableViewController.swift |
| Profile | ProfileTableViewController.swift |

### 设置程序入口
* 修改 `AppDelegate` 中的 `didFinishLaunchingWithOptions` 函数，设置启动控制器

### iOS Images Extractor
可以获取APP中的所有图片

## 架构/设计

* 单一职责原则
* 这是经常被违背的原则。一个类只能干一个事情，一个方法最好也只干一件事情。比较常见的违背是`一个类既干UI的事情，又干逻辑的事情`，这个在低质量的客户端代码里很常见
* 行为是否统一，例如：
* 缓存是否统一
* 错误处理是否统一
* 错误提示是否统一
* 弹出框是否统一
* ……
* 代码污染
* 代码有没有对其他模块强耦合
* 重复代码
* 开闭原则
* 面向接口编程
* 健壮性
* 是否考虑线程安全
* 数据访问是否一致性
* 边界处理是否完整
* 逻辑是否健壮
* 是否有内存泄漏
* 有没有循环依赖
* 有没有野指针
* ……
* 错误处理
* 改动是不是对代码的提升
* 新的改动是打补丁，让代码质量继续恶化，还是对代码质量做了修复
* 效率/性能
* 关键算法的时间复杂度多少？有没有可能有潜在的性能瓶颈
* 客户端程序对频繁消息和较大数据等耗时操作是否处理得当

## 代码风格

* 可读性
* 衡量可读性的可以有很好实践的标准，就是 Reviewer 能否非常容易的理解这个代码。如果不是，那意味着代码的可读性要进行改进
* 命名对可读性非常重要
* 英语用词尽量准确一点，必要时可以查字典
* 函数长度/类长度
* 函数太长的不好阅读
* 类太长了，检查是否违反的 `单一职责` 原则
* 恰到好处的注释
* 参数不要太多，一般不要超过 3 个

## log
* QorumLogs
```
QorumLogs.enabled = true // 启用QorumLogs
QorumLogs.test() // 测试QorumLogs

// 设置需要显示的等级
QorumLogs.minimumLogLevelShown = 3

// 测试的时候，可以限定输出文件
QorumLogs.onlyShowThisFile(BaseTabbarController.self)

QL1("mylog") // debug
QL2("mylog")  // info
QL3("mylog") // warning
QL4("mylog") // error
QLPlusLine() // 打印加号作为分割线
QLShortLine() // 打印等号作为分割线
```

* 自定义LOG
print(#function)  // 打印所在的方法
print(#line)     // 打印所在的行
print(#file)     // 打印所在文件的路径
```
/*
泛型
如果想在函数中使用泛型, 那么必须告诉系统者是一个泛型函数
func 函数名称<T>(形参列表) -> 返回值类型
{
}
message : T
T具体是什么类型由调用者来确定, 调用者传递的是什么类型, T就是什么类型
*/
func XXLog<T>(_ message: T, method: String = #function, line: Int = #line)
{
#if DEBUG
print("\(method)[\(line)]: \(message)")
#endif
}
```

## 异常处理机制
do catch的作用: 一旦方法抛出异常, 那么就会执行catch后面{}中的内容, 如果没有抛出异常, 那么catch后面{}中的内容不执行
try : 正常处理异常, 一旦有异常就执行catch
try!: 强制处理异常(忽略异常),也就是说告诉系统一定不会发生异常, 如果真的发生了异常, 那么程序会崩溃
try?: 忽略异常, 告诉系统可能有异常也可能没有异常, 如果发生异常返回值就是nil, 如果没有发生异常, 会将返回值包装为一个可选类型的值
开发中推举使用try? 和 try , 不推荐使用try!
```
//序列化JSON
guard let dictArray = try? NSJSONSerialization.JSONObjectWithData(jsonData!, options: NSJSONReadingOptions.MutableContainers) else{}
```

## 动态加载控制器
* 命名空间
作用: 避免重复
不用项目中的命名空间是不一样的, 默认情况下命名空间的名称就是当前项目的名称
正是因为Swift可以通过命名空间来解决重名的问题, 所以在做Swift开发时尽量使用cocoapods来集成三方框架, 这样可以有效的避免类名重复
正是因为Swift中有命名空间, 所以通过一个字符串来创建一个类和OC中也不太一样了, OC中可以直接通过类名创建一个类, 而Swift中如果想通过类名来创建一个类必须加上命名空间

* guard可以有效的解决可选绑定容易形成{}嵌套问题
```
guard 条件表达式 else {
//            需要执行的语句
//            只有条件为假才会执行{}中的内容
return
}
```

* 动态加载控制器
    + 在 swift 中，类名是包含命名空间的,类名的组成格式是 `namespace.类名`
    + 命名空间默认是项目名称，同一个命名空间全局共享
    + 从 `mainBundle` 的 `infoDictionary` 获取命名空间名称,namespace 对应的是 `CFBundleExecutable`
```
childControllerName = "HomeTableViewController"

// 1.动态获取命名空间
// 由于字典/数组中只能存储对象, 所以通过一个key从字典中获取值取出来是一个AnyObject类型, 并且如果key写错或者没有对应的值, 那么就取不到值, 所以返回值可能有值也可能没值, 所以最终的类型是AnyObject?
guard let name =  NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as? String else
{
NJLog("获取命名空间失败")
return
}

// 2.根据字符串获取Class
let cls: AnyClass? = NSClassFromString(name + "." + childControllerName)

// 3.告诉编译器真实类型是UITableViewController
// Swift中如果想通过一个Class来创建一个对象, 必须告诉系统这个Class的确切类型
guard let typeCls = cls as? UITableViewController.Type else
{
NJLog("cls不能当做UITableViewController")
return
}

// 4.实例化控制器
// 通过Class创建对象
let childController = typeCls.init()
NJLog(childController)
```

## as
* 如果某些方法返回的数据类型是 `AnyObject`/`AnyClass`，则需要在右侧使用 `as 类型` 表明类型，并且根据返回值`是否是可选项`，添加 `!` 或者 `?`，例如：
```swift
let ns = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
```

* 如果某些方法返回类型是 `AnyObject`/`AnyClass`，但是对象类型是动态生成的，也就是说，编码时同样无法确定改对象的准确类型，可以在左侧使用 `: AnyObject` 或者 `: AnyClass` 告诉编译器暂不处理
```swift
let cls: AnyClass = NSClassFromString(ns + "." + vcName)!
```

## storyboard refrence

## 懒加载
懒加载的代码是一个闭包，因此在代码内部需要使用 `self.`
```swift
lazy var centerBtn: UIButton = {
    () -> UIButton
    in

    let btn = UIButton()
    return btn
}()
```

## @objc
* 类
```
@objc class MyHelper:NSObject {  
// class code  
} 
```

使用@objc修饰后的类型，可以直接供 Objective-C 调用
使用@objc修饰的类，必须继承自NSObject。
* 方法
`@objc private func`
作用：在swift 中 如果一个按钮添加点击方法 如果定义为Private  或者 定义为 FilePrivate 那么会在Addtaget方法中找不到私有方法
但是又不想把方法暴露出来，避免外界访问 ，那么可以在私有方法前加 @objc 修饰 那么它就能找到那个方法了, 允许这个函数在“运行时”通过oc的消息机制调用

## private/fileprivate
* private
能在同一个类 或者 同一个文件（extension）中访问这个方法
* 不希望暴露的方法，应该使用 `private` 修饰符
* 按钮点击事件的调用是由 `运行循环` 监听并且以`消息机制`传递的，因此，按钮监听函数不能设置为 `private`

* fileprivate
只能在一个类中访问 不能在类扩展中访问

## 通知/代理/block
通知 : 层级结构较深
代理 : 父子 , 方法较多时候使用
block: 父子, 方法较少时使用(一般情况一个方法)
> 和OC不一样, Swift中如果简单的调用代理方法, 不用判断代理能否响应

## 协议
* 定义协议

```swift
/// 访客视图协议
protocol VisitorViewDelegate: NSObjectProtocol
{
/// 将要登录
func visitorViewLogin()
/// 将要注册
func visitorViewRegister()
}
```

> 定义协议时，需要继承自 `NSObjectProtocol`，否则无法设置代理的类型

* 定义代理

```swift
/// 定义代理 - 一定要用 weak
weak var delegate: VisitorViewDelegate?
```

## 构造器
如果构造方法前面没有convenience单词, 代表着是一个初始化构造方法(指定构造方法)
如果构造方法前面有convenience单词, 代表着是一个便利构造方法

指定构造方法和便利构造方法的区别
1.指定构造方法中必须对所有的属性进行初始化
2.便利构造方法中不用对所有的属性进行初始, "因为便利构造方法依赖于指定构造方法"
一般情况下如果想给系统的类提供一个快速创建的方法, 就自定义一个便利构造方法

## 
// 一般情况下设置全局性的属性, 最好放在AppDelegate中设置, 这样可以保证后续所有的操作都是设置之后的操作
```
// 设置全局外观
private func setupAppearance() {
    UINavigationBar.appearance().tintColor = UIColor.orange
    UITabBar.appearance().tintColor = UIColor.orange
}
```

## tabbarItem
tabBar 的 items 是在 `视图将要出现之前` 才被创建的
* viewDidLoad 方法调用是实例化控制器方法触发的
此时只是创建子控制器，而由于界面还没有显示，按照 iOS 开发的延迟创建原则，tabBar 中的 items 还没有被创建
* viewWillAppear 方法是由 `makeKeyAndVisible` 方法触发的
此时需要开始准备将控制器的子视图添加到界面上，因此 tabBar 中的 items 已经被创建

# day 2
## UIPresentationController
1.自定义转场modal出来的控制器不会移除原有的控制器
2.自定义转场modal出来的控制器的尺寸可以自己在containerViewWillLayoutSubviews方法中控制
```
override func containerViewWillLayoutSubviews()
{
// 设置弹出视图的尺寸
presentedView()?.frame = CGRect(x: 100, y: 45, width: 200, height: 200)
}
```

3.containerView 非常重要, 容器视图, 所有modal出来的视图都是添加到containerView上的
4.presentedView() 非常重要, 通过该方法能够拿到弹出的视图
