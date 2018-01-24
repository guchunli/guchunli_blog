---
title: Swift3.0学习笔记
date: 2017-07-14 17:13:04
categories: 学习
tags: [Swift3.0,待补充]
toc: true
---

在线swift编辑器：IBM Swift Sandbox

# 基础知识
## 常量和变量
let，var分别声明常量，变量
<!--more-->
`代码中有不需要改变的值，使用 let 关键字将它声明为常量`
> 使用 let 修饰 `view` 并且赋值，表示该常量的内存地址不允许修改，但是可以修改其内部的属性

## 字面量
* 字面量是指像特定的数字，字符串或者是布尔值这样能够直接了当地指出自己的类型并对变量进行赋值的值。
```
let aNumber = 3
let astring = "Hello"
let aBool = true

let anArray = [1,2,3]
let aDictionary = ["key1": "value1", "key2": "value2"]
```

## 类型标注
很少需要标注，swift会进行类型推断，根据右边的代码，推导出变量的准确类型
> Option + Click 可以查看变量的类型
```
let a:Float = 3.0
```

## 注释
多行注释可嵌套

## 分号
swift语句，最后分号可写可不写

## 类型转换
值永远不会被隐式转换为其他类型，如果要对不同类型的数据进行计算，必须要显式的转换
```
let a = "abc"
let b = 9
let ab = a + String(b)
```

## 类型别名
```
typealias Example = Int
```

## 元组
* 元组可通过变量名或下标访问
* 可在定义元组的时候给元素命名
* 不需要访问的元素可用`_`代替
* 元组可作为函数返回值返回多个值


## 可选类型
可以是任何类型值的缺失
显示类型转换结果是可选类型

## nil
nil是一个确定的值，表示值缺失，任何类型的可选状态都可以被设置为nil
不可用于非可选的常量和变量
可选常量或变量不赋值，自动设值为nil

## if 语句以及强制解析
if语句中的条件必须是一个布尔表达式
当一个可选常量或变量确定包含值时，可用`!`强制解析
> `()` 可以省略，但是 `{}` 不能省略
* 当 if 的嵌套层次很深，可用where
```
if let u = url where u.host == "www.baidu.com" {}
```

解包：
(1)!强行解包,如果 变量 为空，运行时会崩溃
(2)判断是否为空
```
if str != nil {
print(str!)
}
```

(3)`if let`可选绑定

## 可选绑定
可以用在if或while语句中
可以判断多个可选项是否为空，用`,`隔开
* 一旦进入if分支，变量就不再是可选项
> `if let` 不能与使用 `&`、`|` 等条件判断,可以使用 `where` 子句
```
if let constantName = someOptional {
//constantName
}
```

## 隐式解析可选类型
可选类型被第一次赋值之后就可以确定之后一直有值，这种类型的可选状态被定义为隐式解析可选类型，可使用!直接声明，之后不需要使用!取值。
隐式解析可选类型就是可以自动解析的可选类型。
仍然可以可选绑定。

## 错误处理
抛出错误：
```
func canThrowAnError() throws {
// 这个函数有可能抛出错误
}
```

捕获错误：
```
do {
try canThrowAnError()
// 没有错误消息抛出
} catch {
// 有一个错误消息抛出
}
```

## 断言
当前面表达式的结果为false时显示后面的消息，应用终止。后面的断言消息也可省略。
release配置时，断言被禁用。
```
assert()
```

## fatalError
* 断言只会在 Debug 环境中有效，而在 Release 编译中所以变得断言都将被禁用。所以我们会考虑以产生致命错误(fatalError)的方式来种植程序。
```
required init?(coder aDecoder: NSCoder) {
fatalError("init(coder:) has not been implemented")
}
```

# 基本运算符
## 空合运算符??
可对可选类型a进行空判断，如果包含值就解封，否则返回默认值b，此默认值b也为可选类型
```
let 
let c = a ?? b
```

## 区间运算符
* 闭区间：... 
* 半开区间：..<

# 字符与字符串
## 初始化空字符串
```
let s1 = ""
let s2 = String()
```

## 字符串是值类型

## 字符
str.characters
```
let chars:[Character] = ["a","b","c"]
let str = String[chars]
```
## 字符串可变性
变量可变，常量不可变

## 连接字符串和字符
* +：只能连接字符串
* append

## 插入和删除
以下方法也可使用在Array,Dictionary,Set中。
* str.characters.indices所有索引
* 在某个索引值之前插入字符：`insert(_:at:)`，不能越界
* 插入字符串：`insert(contentsOf:at:)`
```
var hello = "hello";
hello.insert(contentsOf:" world!".characters, at: hello(before: welcome.endIndex))
```

* 删除字符：`remove(at:)`，越界返回nil
* 删除字符串`removeSubrange(_:)`
```
var hello = "hello world!"
let range = hello.index(hello.endIndex, offsetBy: -7)..<hello.endIndex
hello.removeSubrange(range)
```

## 计算字符数量
str.characters.count

## 字符串索引
* `startIndex`,`endIndex`,`index(before:)`,`index(after:)`,`index(_:offsetBy:)`,也可以使用在Array,Dictionary,Set中
* str.characters.indices所有索引

## 比较字符串
* 字符串相等：==
* 前缀相等：`hasPrefix(_:)`
* 后缀相等：`hasSuffix(_:)`

## 插值
值转换成字符串：\()
> 如果变量是可选项，拼接的结果中会有 `Optional`，`??` 操作符用于检测可选项是否为 `nil`， 如果不是 `nil`，使用当前值，如果是 `nil`，使用后面的值替代

## 格式化字符串
* 在实际开发中，如果需要指定字符串格式，可以使用 `String(format:...)` 的方式
```swift
let h = 8
let m = 23
let s = 9
//后面的参数需要放在一个数组中
let timeString = String(format: "%02d:%02d:%02d", arguments: [h, m, s])
```

```
let name = "Tom"
let date = NSDate()
let string = NSString(format: "Hello %@. Date: %@", name, date)
```

# 集合类型
## 集合的可变性
变量可变，常量不可变
## 数组
* 数组的类型
    * 如果初始化时，所有内容类型一致，择数组中保存的是该类型的内容
    * 如果初始化时，所有内容类型不一致，择数组中保存的是 `NSObject`
* 数字可以直接添加到集合，不需要再转换成 `NSNumber`
* 如果将结构体对象添加到集合，仍然需要转换成 `NSValue`
```
//var array2 = ["zhangsan", 18]  //会报错:Heterogeneous collection literal could only be inferred to '[Any]'; add explicit type annotation if this is intentional
let array2 = ["zhangsan", 18] as [Any]
array2.append(100)
array2.append(NSValue(CGPoint: CGPoint(x: 10, y: 10)))
```

最后一个元素后面允许有个逗号
* 定义数组
```
var arr1:[Int]
```

* 创建空数组
```
var arr1 = [Int]()
//var arr1 = []  错误
```

* 给数组赋空值
```
arr1 = []
arr = [Int](repeatElement(3, count: 10))
arr = Array(repeatElement(2, count: 10))
```

* 判断是否是空数组：isEmpty
* `不可以用下标访问的形式为数组添加新项`

* 遍历数组
可以使用元组返回索引和元素
```
for (index, value) in arr.enumerated() {}
```

* 必须是相同类型的数组才能够合并

## 集合
* 存储在集合中的数据必须是可哈希化的。
可哈希化必须满足三个条件：
    * a == a(自反性)
    * a == b意味着b == a(对称性)
    * a == b && b == c意味着a == c(传递性)

* 创建空集合
```
var set1 = Set<String>()
```

* 集合转数组
```
let arr = set1.sorted()
```

* 集合的交差并补
```
let x:Set = [1,2,3]
let y:Set = [3,4,5]
x.intersection(y)
x.subtract(y)
x.union(y)
x.symmetricDifference(y)
```

* 集合相等：有完全相同的元素
```
let set1:Set = [1,2,3]
let set2:Set = [3,2,1]
set1 == set2
```

* 子集（可以相等），严格子集
```
let set1:Set = [1,2,3,4]
let set2:Set = [3,4]
set2.isSuperset(of: set1)
set2.isStrictSuperset(of: set1)
```

* 父集（可以相等），严格父集
```
set1.isSubset(of: set2)
set1.isStrictSubset(of: set2)
```

* 无交集
```
let set1:Set = [1,2]
let set2:Set = [3,4]
set1.isDisjoint(with: set2)
```

## 字典
* 创建
```
var dict = ["a":"b"]
var dict1 : Dictionary<String,String>
var dict2 : [String:String]
```

* `updateValue(_:forKey:)`:设置或更新值，但是返回更新值之前的原值
```
let oldValue = dic.updateValue("lisi", forKey: "name")
```

* `dic["akey"] = nil`:移除键值对
* dic.keys,dic.values
```
var keyArr = [String](dict.keys)
var valueArr = Array(dict.values)
```

* 遍历字典时候需要明确指明数组中的数据类型
for dict in dictArray  as! [[String:String]]{}

# 控制流
## For-In 循环
* index 可直接使用，无序声明，不需要的时候可用`_`忽略
* 省略下标
    * `_` 能够匹配任意类型
    * `_` 表示忽略对应位置的值
```
for index in 1...5 {}
```

## while repeat-while

## switch
* switch支持任意类型的数据比较 
* switch不会隐式贯穿，匹配到后会退出switch语句，所以不用在每个子句结尾写break
* 每一个 case 分支都必须包含至少一条语句,没有可用break
* 必须有default语句,要保证处理所有可能的情况，不然编译器直接报错
* 每一个 `case` 中定义的变量仅在当前 `case` 中有效
* 复合匹配，可以用逗号隔开匹配多个值
* 区间匹配
* 元组匹配
* 值匹配
* where添加额外条件

## 控制转移语句
* continue
* break
可用于switch语句或循环体中
* fallthrough：贯穿到下一个case中的代码
* return
* throw

* guard:提前退出，后面必须有一个else语句，else中必须包含控制转移语句

## 检测API可用性
*是必须的，用于指定在所有其它平台中，如果版本号高于你的设备指定的最低版本，if语句的代码块将会运行。
```
if #available(iOS 10, macOS 10.12, *) {
// 在 iOS 使用 iOS 10 的 API, 在 macOS 使用 macOS 10.12 的 API
} else {
// 使用先前版本的 iOS 和 macOS 的 API
}
```

# 函数
* MARK
```
// MARK: - mark something
```

## 函数定义与调用
```
func hello(name:String) -> String{
    let str = "Hello,"+name+"!";
    return str;
}
hello(name:"Anna");
```

## 指定参数标签
```
func someFunction(argumentLabel parameterName: Int) {}
```

## 忽略参数标签
如果一个参数有一个标签，那么在调用的额是很好必须使用标签来标记这个参数，如果不希望为某个参数添加标签，可以使用`_`来代替一个明确的参数标签。
```
func hello(name:String) -> String{}
//调用 hello(name:"Anna");
func hello(_ name:String) -> String{}
//调用 hello("Anna");
```

## 默认参数值
```
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
// 如果你在调用时候不传第二个参数，parameterWithDefault 会值为 12 传入到函数体中。
}
someFunction(parameterWithoutDefault: 4) // parameterWithDefault = 12
```

## 可变参数
```
func getMiddle(_ numbers: Double...) -> Double{}
```

## 输入输出参数
> 函数参数默认是常量。试图在函数体中更改参数值将会导致编译错误
* 输入输出参数：可以在函数中修改的参数，并且这些修改在参数调用的时候仍然存在
> 只能传递变量给输入输出参数，不能传入常量或者字面量，并且在传入的时候在参数名前添加`&`。
```
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
let temporaryA = a
a = b
b = temporaryA
}
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
```

## 函数类型
* 可作为参数类型或返回值类型
```
func calculate(x: Int,y: Int,method: (Int,Int)->Int) -> Int{
return method(x,y)
}

func add(x: Int,y: Int)->Int{
return x+y
}
func multiply(x: Int,y: Int)->Int{
return x*y
}
calculate(x: 3, y: 4, method: add)
calculate(x: 3, y: 4, method: multiply)
```

# 闭包
闭包的三种形式：
* 全局函数是一个有名字但不会捕获任何值的闭包
* 嵌套函数是一个有名字并可以捕获其封闭函数域内值的闭包
* 闭包表达式是一个利用轻量级语法所写的可以捕获其上下文中变量或常量值的匿名闭包
## 闭包表达式
```
{ (parameters) -> returnType in
statements
}
```

排序闭包：
```
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )
```

> 排序闭包函数类型需为`(String, String) -> Bool`
* 所有的参数和返回值类型都可以被正确推断，则返回箭头（->）和围绕在参数周围的括号也可以被省略
```
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```

* 单表达式闭包隐式返回,可以省略 return 关键字，把语句的值作为结果返回
```
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

* 可以通过参数位置引用参数，通过 $0，$1来顺序调用闭包的参数
```
reversedNames = names.sorted(by: { $0 > $1 } )
```

* 运算符方法
```
reversedNames = names.sorted(by: >)
```

## 尾随闭包
* 当一个闭包作为最后一个参数传给函数的时候，闭包可以直接跟在括号后面，不用写出参数标签
* 如果闭包是函数的唯一参数，则当使用尾随闭包时，可以把()省略掉
```
reversedNames = names.sorted { $0 > $1 }
```

## 值捕获
* 闭包可以在其被定义的上下文中捕获常量或变量。即使定义这些常量和变量的原作用域已经不存在，闭包仍然可以在闭包函数体内引用和修改这些值。
* Swift 中，可以捕获值的闭包的最简单形式是嵌套函数
* 如果一个值不会被闭包改变，或者在闭包创建后不会改变，Swift 可能会改为捕获并保存一份对值的拷贝。
> 如果你将闭包赋值给一个类实例的属性，并且该闭包通过访问该实例或其成员而捕获了该实例，你将在闭包和该实例间创建一个循环强引用。Swift 使用捕获列表来打破这种循环强引用

## 函数和闭包都是引用类型

## 逃逸闭包 @escaping
* 逃逸闭包：当一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行
* 如何逃逸：将闭包保存在一个函数外部定义的变量中，因为闭包需要在函数返回之后被调用
> 将一个闭包标记为 @escaping 意味着必须在闭包中显式地引用 self，非逃逸闭包可以隐式引用self

## 自动闭包 @ autoclosure
* 自动闭包让你能够延迟求值，因为直到你调用这个闭包，代码段才会被执行

## 解除循环引用
1.与OC类似的方法
```
weak var weakSelf = self
loadData{
print("\(weakSelf?.view)")
}
```

2.swift推荐的方法
```
loadData{ [weak self] in
    print("\(self?.view)")
}
```

3.unowned
```
loadData{ [unowned self] in
    print("\(self.view)")
}
```

* 循环引用总结
    * swift
        * `[weak self]`：self是可选项，如果self已经被释放，则为nil
        * `[unowned self]`：self不是可选项，如果self已经被释放，则为野指针访问
    * Objc
        * `__weak typeof(self) weakSelf;`：如果self已经被释放，则为nil
        * `__unsafe_unretained typeof(self) weakSelf;`：如果self已经被释放，则为野指针访问

# 枚举
## 枚举语法
```
enum CompassPoint {
    case north
    case south
    case east
    case west
}
var directionToHead = CompassPoint.west
//var directionToHead = .west
```

## 使用 Switch 语句匹配枚举值，如果变量是枚举值，可省略枚举名，还可以在case中加上元组变量

## 原始值 rawValue
当使用整数作为原始值时，如果第一个枚举成员没有设置原始值，其原始值将为0,隐式赋值的值依次递增1

# 类和结构体
## 类和结构体对比
相同点：
* 定义属性用于存储值
* 定义方法用于提供功能
* 定义下标操作使得可以通过下标语法来访问实例所包含的值
* 定义构造器用于生成初始化值
* 通过扩展以增加默认实现的功能
* 实现协议以提供某种标准功能
类的不同点：
* 继承允许一个类继承另一个类的特征
* 类型转换允许在运行时检查和解释一个类实例的类型
* 析构器允许一个类实例释放任何其所被分配的资源
* 引用计数允许对一个类的多次引用

* 定义
```
class SomeClass {
// 在这里定义类
}
struct SomeStructure {
// 在这里定义结构体
}
```

* 结构体类型的成员逐一构造器，类实例没有

* 结构体和枚举是值类型
> 值类型：被赋予给一个变量、常量或者被传递给一个函数的时候，其值会被拷贝。
> 在 Swift 中，Integer、floating-point、Boolean、string、array、dictionary 都是值类型，并且在底层都是以结构体的形式所实现。
> 结构体和枚举类型都是值类型。这意味着它们的实例，以及实例中所包含的任何值类型属性，在代码中传递的时候都会被复制。两者相互独立，是不同的。


* 类是引用类型
> 引用类型在被赋予到一个变量、常量或者被传递到一个函数时，其值不会被拷贝。因此，引用的是已存在的实例本身而不是其拷贝。实际是同一个。

* 恒等运算符
===/!==：判定两个常量或者变量是否引用同一个类实例

## 类和结构体的选择
大部分使用类，以下情形考虑构建结构体：
* 目的是用来封装少量相关简单数据值。
* 实例在被赋值或传递时，封装的数据将会被拷贝而不是被引用。
* 储存的值类型属性，也应该被拷贝，而不是被引用。
* 不需要去继承另一个既有类型的属性或者行为。

# 属性
## 存储属性
* 定义：存储在特定类或结构体实例里的一个常量或变量，保存单个类型的变量。
* 常量结构体的存储属性：如果创建了一个结构体的实例并将其赋值给一个常量，则无法修改该实例的任何属性，即使有属性被声明为变量也不行(由于结构体（struct）属于值类型。当值类型的实例被声明为常量的时候，它的所有属性也就成了常量；属于引用类型的类（class）则不一样。把一个引用类型的实例赋给一个常量后，仍然可以修改该实例的变量属性。)
* 延迟存储属性
定义：在第一次被访问的时候创建
> 必须将延迟存储属性声明成变量（使用 var 关键字），而常量属性在构造过程完成之前必须要有初始值，因此无法声明成延迟属性

## 计算属性
* 定义：计算属性不直接存储值，而是提供一个 getter 和一个可选的 setter，来间接获取和设置其他属性或变量的值。
> 计算属性可以用于类、结构体和枚举，存储属性只能用于类和结构体。
* 新值的参数名默认为newValue
> 必须使用var定义计算属性，包括只读计算属性
* 只读计算属性的声明可以去掉get关键字和花括号，直接return

## 属性观察器
* 属性被设置值的时候会调用属性观察器（即使新值与当前值相同）
* 可添加观察器的情况：
    - 定义的存储属性（除延迟存储属性之外）
    - 从父类继承的存储属性或计算属性（通过重写属性的方式）
* willSet：传入新属性值，默认为newValue，常量参数
* didSet：传入旧属性值，默认为oldValue
* 继承：
    - 父类属性在子类的构造器中赋值，会先调用父类观察器，再调用子类观察器。
    - 在父类初始化方法调用之前，子类给属性赋值时，不会调用观察器方法。

## 全局变量和局部变量
* 都属于存储型变量，跟存储属性类似。
* 都可以定义计算型变量和为存储型变量定义观察器，与计算属性类似。
* 全局变量或常量都是延迟计算的，与延迟存储属性相似，不需要声明lazy。
* 局部变量或常量从不延迟计算。

## 类型属性
* 存储型类型属性可以是变量或常量，计算型类型属性跟实例的计算型属性一样只能定义成变量属性。
* 在为类定义计算型类型属性时，可以改用关键字 class 来支持子类对父类的实现进行重写。

# 方法
* 类、结构体、枚举都可以定义实例方法，也可以定义类型方法。
* self
    - 在一个方法中使用一个已知的属性或者方法名称，可省略self。
    - 实例方法的某个参数名称与实例的某个属性名称相同的时候，参数名称享有优先权，可以使用self属性来区分参数名称和属性名称。
* mutating
    - 不能在结构体类型的常量上调用可变方法，因为其属性不能被改变，即使属性是变量属性
    - 赋给隐含属性self一个全新的实例
* 类型方法：类型方法的方法体中，self指向这个类型本身，而不是类型的某个实例

# 下标
```
subscript(index: Int) -> Int {
    get {
    // 返回一个适当的 Int 类型的值
    }
    set(newValue) {
    // 执行适当的赋值操作
    }
}
```

* 可设定为读写或只读
* newValue的类型和下标的返回类型相同，默认newValue

# 继承
## 基类
* 如果你不为你定义的类指定一个超类的话，这个类就自动成为基类。

## 重写override
* 可以重写继承来的实例方法、类方法、实例属性、类型属性，自定义getter和setter或添加属性观察器
* 重写属性的getter或setter
    - 可以为任意继承来的属性（存储属性或计算属性）自定义getter或setter
    - 可以将继承来的只读属性重写为读写属性，但是不能将继承来的读写属性重写为只读属性。
    - 如果在重写属性中提供了 setter，那么一定要提供 getter，如果不修改可以调用super方法
* 重写属性观察器
    - 不可以为继承来的常量存储属性或只读计算属性添加属性观察器，因为这些属性的值是不可以修改的
    - 不可以同时重写setter和属性观察器，因为在setter中就可以观察到任何值的变化
* 防止重写 final
    - final var,final func,final class fun,final subscript
    - final class 声明的类不能被继承

# 构造过程
* 没有返回值
* 用于类、结构体或枚举
```
init() {
// 在此处执行构造过程
}
```

## 默认属性值
* 如果一个属性总是使用相同的初始值，为其设置默认值比每次在构造器中赋值要好。

## 存储属性的初始赋值
* 需要为存储属性在定义时设置默认值或者在构造器中赋值，不会触发属性观察器。

* 可以使用下划线(_)来显式描述它的外部名
* 可以在构造过程中的任意时间点给常量属性指定一个值
* 如果结构体或类的所有属性都有默认值，同时没有自定义的构造器，swift会自动提供默认构造器
* 结构体自动获得一个逐一成员构造器

## 类的继承和构造过程
* 类里面的所有存储型属性——包括所有继承自父类的属性——都必须在构造过程中设置初始值，可以通过指定构造器或便利构造器实现。
### 指定构造器
* 每一个类都必须拥有至少一个指定构造器
```
init(parameters) {
statements
}
```

### 便利构造器
* 便利构造器可以调用同一个类中的指定构造器，并为其参数提供默认值。
```
convenience init(parameters) {
statements
}
```

## 类的构造器代理规则
* 指定构造器必须调用其直接父类的指定构造器
* 便利构造器必须调用其同类定义的其他构造器
* 便利构造器必须最终导致一个指定构造器被调用
> 指定构造器向上代理，便利构造器横向代理

## 两段式构造过程
* 安全检查1
指定构造器必须保证它所在类引入的所有属性都必须先初始化完成，之后才能将其它构造任务向上代理给父类中的构造器。
* 安全检查2
指定构造器必须先向上代理调用父类构造器，然后再为继承的属性设置新值。
* 安全检查3
便利构造器必须先代理调用同一类中的其它构造器，然后再为任意属性赋新值。
* 安全检查4
构造器在第一阶段构造完成之前，不能调用任何实例方法，不能读取任何实例属性的值，不能引用self作为一个值。

## 构造器的继承和重写


可失败构造器
必要构造器
通过闭包或函数设置属性的默认值

# 析构过程
```
deinit {
// 执行析构过程
}
```

# 自动引用计数
> 引用计数仅仅应用于类的实例。结构体和枚举类型是值类型。
## 类实例之间的循环强引用

> 解决实例之间的循环强引用:弱引用（weak reference）和无主引用（unowned reference）
### 弱引用 weak
> 当其他的实例有更短的生命周期时，使用弱引用(当其他实例析构在先)
* 当 ARC 设置弱引用为nil时，属性观察不会被触发
### 无主引用 unowned
> 当其他实例有相同的或者更长生命周期时，请使用无主引用
* 使用无主引用，必须确保引用始终指向一个未销毁的实例
## 闭包引起的循环强引用
> 解决闭包引起的循环强引用:闭包捕获列表
```
lazy var someClosure: (Int, String) -> String = {
[unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
// 这里是闭包的函数体
}
```

# 可选链
> 可以应用于任意类型，并且能检查调用是否成功。

使用可选链式调用代替强制展开
为可选链式调用定义模型类
通过可选链式调用访问属性
通过可选链式调用调用方法
通过可选链式调用访问下标
连接多层可选链式调用
在方法的可选返回值上进行可选链式调用

处理变量的可选值时，可以在操作之前加?，如果？之前的值是nil,?后面的操作会被忽略，整个表达式返回nil，否则运行?后面的操作，这两种情况，整个表达式的值也是可选值

# 错误处理
处理错误的四种方式：
* 把函数抛出的错误传递给调用此函数的代码
* do-catch语句
* 将错误作为可选类型处理
* 断言此错误根本不会发生

* throw：抛出错误
* try(try?,try!)：识别错误
* do-catch：处理错误

## throwing函数
* `throws`写在函数声明的参数列表后,`->`前。
> throwing函数可在其内部抛出错误，将错误传递到函数被调用时的作用域，非throwing函数内部抛出的错误只能在函数内部处理。

## 将错误转换成可选值 try?
```
let y = try? someThrowingFunction()
等价于
let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

## 禁用错误传递 try!
会把调用包装在一个不会有错误抛出的运行时断言中，如果真的抛出，则会发生运行时错误。

## 指定清理操作
* defer语句在即将离开当前代码块时（throw,return,break等）执行一系列语句。
将代码的执行延迟到当前作用域退出之前，延时执行的操作会按照被指定时的顺序的相反顺序执行，即第一条defer语句中的代码会在第二条defer语句中的代码被执行之后才执行，以此类推。

# 类型转换
## 检查类型 is
* 用类型检查操作符（is）来检查一个实例是否属于特定子类型。
## 向下转型 as
* 某类型的一个常量或变量可能在幕后实际上属于一个子类，用`as`向下转到它的子类型。
* 转换没有真的改变实例或它的值
> 当不确定向下转型是否成功，使用`as?`，当确定一定会成功，使用`as!`
## Any 和 AnyObject 的类型转换
* Any 可以表示任何类型，包括函数类型。
* AnyObject 可以表示任何类型的实例。
> Any类型可以表示所有类型的值，包括可选类型。Swift 会在你用Any类型来表示一个可选值的时候，给你一个警告。如果你确实想使用Any类型来承载可选值，你可以使用as操作符显式转换为Any。

# 嵌套类型
* 类、枚举、结构体之间可相互嵌套

# 扩展 Extensions
* 与OC中的分类类似，但是swift中的扩展没有名字
* 可用于已有的类、结构体、枚举或协议
* 可以为一个类添加新功能，但是不能重写已有的功能
* 可以实现的功能：
    - 添加计算型属性和计算型类型属性
    - 定义实例方法和类型方法
    - 提供新的构造器
    - 定义下标
    - 定义和使用新的嵌套类型
    - 使一个已有类型符合某个协议
    
## 扩展语法
extension SomeType {
// 为 SomeType 添加的新功能写到这里
}
> 通过为一个已有类型添加新功能，则新功能对该类型的所有实例都有效，即使它们是在扩展定义之前创建的

## 计算型属性
* 扩展可以添加新的计算属性，但是不可以添加存储型属性，也不可以为已有属性添加属性观察器。

## 构造器
* 扩展可以为类添加新的便利构造器，不可以添加新的指定构造器或析构器。指定构造器和析构器必须由原始类提供。

## 方法
通过扩展添加的实例方法可以修改该实例本身，结构体和枚举类型中修改self或器属性的方法必须将该实例方法标注为mutating

# 协议
类、枚举、结构体都可以实现协议

## 协议语法
定义协议：
```
protocol SomeProtocol {
// 这里是协议的定义部分
}
```

遵循协议：
```
struct SomeStructure: FirstProtocol, AnotherProtocol {
// 这里是结构体的定义部分
}
```

## 属性要求
* 协议可以要求遵循协议的类型提供的实例属性或类型属性的`名称和类型`，还可指定`读写性`。
> 用`var`声明变量属性，用`{set get}`声明属性是可读可写的。

## 方法要求
* 方法不需要大括号和方法体
* 不支持为协议中的方法的参数提供默认值

## Mutating 方法要求
* 实现协议中的mutating方法时，若是类类型，则不用写`mutating`，对于结构体和枚举必须写。

## 构造器要求
* 必须为构造器实现标上`required`修饰符，可以确保所有子类也必须提供此构造器实现。
```
class SomeClass: SomeProtocol {
required init(someParameter: Int) {
// 这里是构造器的实现部分
}
}
```

* 如果一个子类重写了父类的指定构造器，并且该构造器满足了某个协议的要求，则构造器的实现需要标上`mutating`和`override`。
```
protocol SomeProtocol {
init()
}

class SomeSuperClass {
init() {
// 这里是构造器的实现部分
}
}

class SomeSubClass: SomeSuperClass, SomeProtocol {
// 因为遵循协议，需要加上 required
// 因为继承自父类，需要加上 override
required override init() {
// 这里是构造器的实现部分
}
}
```

## 可是白构造器要求
非可失败构造器（init）
可失败构造器（init?）
隐式解包可失败构造器（init!）

## 协议作为类型
* 作为函数、方法或构造器中的参数类型或返回值类型
* 作为常量、变量或属性的类型
* 作为数组、字典或其他容器中的元素类型

## 委托（代理）模式

通过扩展添加协议一致性
通过扩展遵循协议
协议类型的集合
协议的继承
类类型专属协议
协议合成
检查协议一致性
可选的协议要求
协议扩展

# 泛型

# 访问控制

# 高级运算符


# 常用代码
try-catch,assert,extension,fatalError,柯里化,mutating,


参考资料：[The Swift Programming Language中文版](http://wiki.jikexueyuan.com/project/swift)
[https://github.com/yagamis/swift2basic](https://github.com/yagamis/swift2basic)

