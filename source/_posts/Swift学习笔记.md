---
title: Swift学习笔记
date: 2017-07-14 17:13:04
categories: 学习
tags: [Swift,待补充]
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

* 数值型字面量
```
let a = 11
let b = 0b11
let c = 0o11
let d = 0x11
let e = 2e2
let f = 2e-2
let g = 0xFp2
let h = 0xFp-2
let i = 1_000_000
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

## 打印日志
```
print("item1","item2","item3", separator: ",,,", terminator: "...")
//item1,,,item2,,,item3...
```

## 类型转换
值永远不会被隐式转换为其他类型，如果要对不同类型的数据进行计算，必须要显式的转换
* 与字面量的加减无关
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
```
let (color,price,_) = ("red",80,true)
print(color)
let pen = ("red",80,true)
let (color,price,_) = pen
print(color)
print(pen.0)
let some = (color:"red",price:80)
print(some.color)
```

## 可选类型
可以是任何类型值的缺失
显式类型转换结果是可选类型
```
var s: String = "hello"
//s = nil //错误
var s1: String? = "hello"
s1 = nil
```

## nil
nil是一个确定的值，表示值缺失，任何类型的可选状态都可以被设置为nil
不可用于非可选的常量和变量
可选常量或变量不赋值，自动设值为nil
* 在OC中nil是指针，在swift中nil是可选类型。

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
* 需要改变，用`if var`
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
* 一般以下情况使用：
    * 下标越界
    * 传递给函数的参数不符合类型
    * 解析可选类型：一个可选值当前为 nil ，但随后的代码就需要非空的值才能成功执行。
    
```
let age = 17
assert(age>=18,"未成年人")
//Assertion failed: 未成年人
```

## fatalError
* 断言只会在 Debug 环境中有效，而在 Release 编译中所以变得断言都将被禁用。所以我们会考虑以产生致命错误(fatalError)的方式来种植程序。
```
required init?(coder aDecoder: NSCoder) {
fatalError("init(coder:) has not been implemented")
}
```

# 基本运算符

```
let a = 3
let b = 3.3
let c = Double(a)*b //或 let c = a*Int(b)
```
## 不可自增自减

## 取余
```
10 % 6  //4
10 % -6 //4
-10 % 6 //-4
```

## 空值合并(空合)运算符??
可对可选类型a进行空判断，如果包含值就解封，否则返回默认值b，此默认值b也为可选类型
```
let a = Int("aaa")
let b = 2
let c = a ?? b  //等同于 let c = a != nil ? a! : b
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

## 字符
str.characters
```
let c = "a"

let chars:[Character] = ["a","b","c"]
let str = String[chars]
for char in are{
print(char)
}
```
## 字符串可变性
变量可变，常量不可变

## 连接字符串和字符
* +：只能连接字符串，不能连接字符
* append
```
var str1 = "12"
let str2 = "34"
let char1:Character = "5"
str1 += str2
//str1 += char1     //+：只能连接字符串，不能连接字符
str1.append(char1)
str1.append(str2)
//str1.append(contentsOf: char1)    //contentsOf：只能连接字符串，不能连接字符
str1.append(contentsOf: str2)
```

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
* removeFirst(2),removeLast()
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

## swift4新增
* 多行字符串字面量
```
let str1 = """
12\n34
56
789
"""
print(str1)
```

* 去掉 characters
```
//swift3
let values = "one,two,three..."
print(values.characters.count)
var i = values.characters.startIndex

while let comma = values.characters[i...<values.characters.endIndex].index(of: ",") {
if values.characters[i..<comma] == "two" {
print("found it!")
}
i = values.characters.index(after: comma)
}

//swift4
let values = "one,two,three..."
print(values.count)
var i = values.startIndex

while let comma = values[i...<values.endIndex].index(of: ",") {
if values[i..<comma] == "two" {
print("found it!")
}
i = values.index(after: comma)
}
```

* String 当做 Collection 来用
```
let abc: String = "abc"
print(String(abc.reversed()))   // cba
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
let a:Array<Int>
```

* 创建空数组
```
var arr2 = [Int]()
//var arr3 = []  //错误
var a2:[String] = Array<String>()
```

* 给数组赋空值
```
arr1 = []
arr = [Int](repeatElement(3, count: 10))
arr = Array(repeatElement(2, count: 10))
var ints = [Int](repeating: "2", count: 10)
var ints = Array(repeating: "2", count: 10)
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
    
```
var a = 1
print(a.hashValue)
```

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
* 存储在集合中的key必须是可哈希化的。
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

* `dic["akey"] = nil`，`dic.removeValue(forKey: "akey")`：移除键值对
* dic.keys,dic.values
```
var keyArr = [String](dict.keys)
var valueArr = Array(dict.values)
```

* 遍历字典时候需要明确指明数组中的数据类型
for dict in dictArray  as! [[String:String]]{}

## 其他
### 生成器（Generator）
* 生成器允许遍历所有元素
```
protocol GeneratorType {
typealias Element
mutating func next() -> Element?
}

```

### 序列（Sequence）
* 一种可以对其元素进行连续，迭代访问的类型。
```
protocol SequenceType {
    typealias Generator: GeneratorType
    func generate() -> Generator
}
```

### 集合(Collection)

# 控制流
## For-In 循环
* index 可直接使用，无序声明，不需要的时候可用`_`忽略
* 省略下标
    * `_` 能够匹配任意类型
    * `_` 表示忽略对应位置的值
```
for index in 1...5 {}
```

## while/repeat-while

## switch
* switch支持任意类型的数据比较 
* switch不会隐式贯穿，默认有break,不用加
* 每一个 case 分支都必须包含至少一条语句,没有可用break；必须有default语句,要保证处理所有可能的情况，不然编译器直接报错
* 每一个 `case` 中定义的变量仅在当前 `case` 中有效
* 复合匹配，可以用逗号隔开匹配多个值
* 区间匹配：`case 1...10:`
* 元组匹配：`case (_,0):`
* 值绑定：`case (let x,0):`
* where添加额外条件：`case let(x,y) where x==y:`

## 控制转移语句
* continue
* break：可用于switch语句或循环体中
* label
```
var i = 1
myLabel: while i<100{
    switch i {
        case 10:
            print("case-10")
            break myLabel
        case 20:
            print("case-20")
        default:
            print("default")
    }
    i+=1
}
```

* fallthrough：贯穿到下一个case中的代码
```
let i = 3
switch i {
    case 1:
        print("1")
    case 2:
        print("2")
    case 3:
        print("3")
        fallthrough
    case 4:
        print("4")
        fallthrough
    default:
        print("default")
}
```

* return
* throw
* guard:提前退出，后面必须有一个else语句，else中必须包含控制转移语句
* label标签语句
```
where labName{
    if(){
        break labName
    }else if(){
        continue labName
    }else{
        ///
    }
}
```

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
* swift中MARK的使用
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
func swapTwoInts(a: inout Int, b: inout Int) {
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

## 嵌套函数
```
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
func stepForward(input: Int) -> Int { return input + 1 }
func stepBackward(input: Int) -> Int { return input - 1 }
return backward ? stepBackward : stepForward
}
var currentValue = 4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
while currentValue != 0 {
print("\(currentValue)... ")
currentValue = moveNearerToZero(currentValue)
}
print("zero!")
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

```
func add(num: Int)->(()->()){
    var sum = 0
    func addNum(){
        sum+=num
        print(sum)
    }
    return addNum
}

var a = add(num: 10)
a() //10
a() //20

var b = add(num: 8)
b() //8
b() //16
a() //30
```

## 函数和闭包都是引用类型

## 逃逸闭包 @escaping
* 逃逸闭包：当一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行
* 如何逃逸：将闭包保存在一个函数外部定义的变量中，因为闭包需要在函数返回之后被调用
> 将一个闭包标记为 @escaping 意味着必须在闭包中显式地引用 self，非逃逸闭包可以隐式引用self

## 自动闭包 @autoclosure
> 自动闭包是一种自动创建的用来把作为实际参数传递给函数的表达式打包的闭包。它不接受任何实际参数，并且当它被调用时，它会返回内部打包的表达式的值。这个语法的好处在于通过写普通表达式代替显式闭包而使你省略包围函数形式参数的括号。

# 枚举enum
## 枚举语法
```
enum Direction {
    case north
    case south
    case east
    case west
}
var d = Direction.west
d = .west

enum Direction{ case East,West,South,North }
```

## 使用 Switch 语句匹配枚举值，如果变量是枚举值，可省略枚举名，还可以在case中加上元组变量
```
enum Direction{ case East,West,South,North }
var d = Direction.North

switch d{
case Direction.East:
print("east")
case .West:
print("wast")
case .South:
print("south")
case .North:
print("north")
//default可省略，非枚举必须加default
//default:
//print("other")
}
```

```
enum Direction{ case East,West,South,North,Other }
var d = Direction.North

switch d{
case Direction.East:
print("east")
case .West:
print("wast")
case .South:
print("south")
case .North:
print("north")
//default不可省略
default:
print("other")
}
```

## 原始值 rawValue
当使用整数作为原始值时，如果第一个枚举成员没有设置原始值，其原始值将为0,隐式赋值的值依次递增1
```
//赋初始值，必须有类型说明
enum Numbers:Int{case A = 1, B, C}
print(Numbers.A.rawValue)

enum Direction:String{
    case East = "east"
    case West = "west"
    case South = "south"
    case North = "north"
}
print(Direction.West.rawValue)
```

## 关联值：枚举成员可以是不同数据类型
```
enum State{
case Status1(Int,String)
case Status2(String,String)
}
var s = State.Status1(404,"page not found")
```

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

* 结构体类型的成员逐一构造器，类实例没有默认构造函数，类中的属性必须初始化或设为可选（属性加！或？，或者写init方法）
```
class Dog {

var name: String!
var age :Int!
init(){
name = "hua hua"
age = 2
}

func show(){
print("\(name!):\(age!)")   //解析
}
}
var d1 = Dog()
d1.show()
d1.name = "xiao hei"
d1.age = 3
d1.show()

print(d1 === d2)    //是否是同一对象:true
var d3 = Dog()
print(d1 === d3)    //是否是同一对象:false
```

* 结构体和枚举是值类型，函数和闭包是引用类型
> 值类型：被赋予给一个变量、常量或者被传递给一个函数的时候，其值会被拷贝。
> 引用类型：在被赋予到一个变量、常量或者被传递到一个函数时，其值不会被拷贝。因此，引用的是已存在的实例本身而不是其拷贝。实际是同一个。
> 结构体和枚举类型都是值类型。这意味着它们的实例，以及实例中所包含的任何值类型属性，在代码中传递的时候都会被复制。两者相互独立，是不同的。
> 在 Swift 中，Integer、floating-point、Boolean、string、array、dictionary 都是值类型，并且在底层都是以结构体的形式所实现。

* 恒等运算符（===/!==）：判定两个常量或者变量是否引用同一个类实例

```
//结构体
struct Point {
var x: Int
var y: Int
}
var p1 = Point(x: 10, y: 20)
var p2 = p1
print("\(p1.x):\(p1.y)")
print("\(p2.x):\(p2.y)")
p1.x = 100
p1.y = 200
print("\(p1.x):\(p1.y)")
print("\(p2.x):\(p2.y)")

//枚举
enum Direction{case East,West,North,South}
var direction1 = Direction.East
var direction2 = direction1
print("\(direction1)")
print("\(direction2)")
direction1 = .West
print("\(direction1)")
print("\(direction2)")

//类
class Dog {

var name: String!
var age: Int!
init(){

name = "hua hua"
age = 2
}
func show(){
print("\(name):\(age)")
}
}

var d1 = Dog()
var d2 = d1
d1.show()
d2.show()
d1.name = "xiao hei"
d1.age = 3
d1.show()
d2.show()
```

## 类和结构体的选择
大部分使用类，以下情形考虑构建结构体：
* 目的是用来封装少量相关简单数据值。
* 实例在被赋值或传递时，封装的数据将会被拷贝而不是被引用。
* 储存的值类型属性，也应该被拷贝，而不是被引用。
* 不需要去继承另一个既有类型的属性或者行为。

# 属性
`存储属性只能用于类和结构体，计算属性可以用于类、结构体和枚举。`
## 存储属性
* 定义：存储在特定类或结构体实例里的一个常量或变量，保存单个类型的变量。
* `所有类的存储属性——包括从它的父类继承的所有属性——都必须在初始化期间分配初始值。`
* 常量结构体的存储属性：如果创建了一个结构体的实例并将其赋值给一个常量，则无法修改该实例的任何属性，即使有属性被声明为变量也不行(由于结构体（struct）属于值类型。`当值类型的实例被声明为常量的时候，它的所有属性也就成了常量`。属于引用类型的类（class）则不一样，`把一个引用类型的实例赋给一个常量后，仍然可以修改该实例的变量属性`。)
* 延迟存储属性lazy
    * 定义：在第一次被访问的时候创建
    * 一般用于：
        - 延迟对象的创建
        - 当属性的值依赖于其他未知类
    * 必须将延迟存储属性声明成变量（使用 var 关键字），而常量属性在构造过程完成之前必须要有初始值，因此无法声明成延迟属性

```
//1)类中的属性必须初始化或设为可选
class Dog {
var name: String!
var age: Int!
}


//2）常量结构体的存储属性
struct Point {
var x: Int
var y: Int
}

let p1 = Point(x: 1, y: 2)
//结构体常量对象不可以进行修改
//p1.x = 100
//p1.y = 200

3）延迟存储属性 lazy：调用时才进行构造
class A {
init(){
print("A init")
}

func f1(){
print("f1...")
}
}

class B {
lazy var a: A = A()
init(){
print("B init")
}
}

var b = B()
//调用时A才进行构造
print(b.a.f1())
```

## 计算属性
* 定义：计算属性不直接存储值，而是提供一个 getter 和一个可选的 setter，来间接获取和设置其他属性或变量的值。
* 新值的参数名默认为newValue
* 只读计算属性的声明可以去掉get关键字和花括号，直接return
> 必须使用var定义计算属性，包括只读计算属性

```
struct Rect {
    var w: Double
    var h: Double

    var circle: Double{
        get{
            print("get...")
            return (w+h)*2
        }
        set{
            //默认newValue，oldValue
            //newValue就是circle
            w = newValue/2
            h = newValue/2
            print("newValue=\(newValue)")
        }
    }
}

var r = Rect(w: 30, h: 20)
print(r.circle)
r.circle = 200
print(r.circle)
```

## 属性观察器
* 属性被设置值的时候会调用属性观察器（即使新值与当前值相同）
* 可添加观察器的情况：
    - 定义的存储属性（除延迟存储属性之外）
    - 从父类继承的存储属性或计算属性（通过重写属性的方式），不需要为无法重载的计算属性添加属性观察器，因为可以通过setter直接监控和响应值的变化
* willSet：传入新属性值，默认为newValue，常量参数
* didSet：传入旧属性值，默认为oldValue
* willSet和didSet在属性初始化过程中不会被调用
* 继承：
    - 父类属性在子类的构造器中赋值，会先调用父类观察器，再调用子类观察器。
    - 在父类初始化方法调用之前，子类给属性赋值时，不会调用观察器方法。
    
```
class Animal {
    var age: Int = 1{

        //可以不加newValue:willSet(newValue)
        willSet{
            print("newValue:\(newValue)")
            //保护属性
            if newValue <= 0 {
                print("非法年龄！")
            }
        }
        //必须加newValue
        didSet(newValue){
            print("newValue:\(newValue)")
            print("didSet")
        }
    }
}
var a = Animal()
print(a.age)    //1
a.age = -1
print(a.age)    //-1
```

## 全局变量和局部变量
* 都属于存储型变量，跟存储属性类似。
* 都可以定义计算型变量和为存储型变量定义观察器，与计算属性类似。
* 全局变量或常量都是延迟计算的，与延迟存储属性相似，不需要声明lazy。局部变量或常量从不延迟计算。

## 类型属性
```
struct Structname {
    static var storedTypeProperty = " "
    static var computedTypeProperty: Int {
    // 这里返回一个 Int 值
    }
}

enum Enumname {
    static var storedTypeProperty = " "
    static var computedTypeProperty: Int {
    // 这里返回一个 Int 值
    }
}

class Classname {
    class var computedTypeProperty: Int {
    // 这里返回一个 Int 值
    }
}
```

* 存储型类型属性可以是变量或常量，计算型类型属性跟实例的计算型属性一样只能定义成变量属性（计算型属性只能声明为变量）。
* 在为类定义`计算型类型属性`时，可以改用关键字 `class` 来支持子类对父类的实现进行重写。

# 方法
* 类、结构体、枚举都可以定义实例方法，也可以定义类型方法。
* self
    - 在一个方法中使用一个已知的属性或者方法名称，可省略self。
    - 实例方法的某个参数名称与实例的某个属性名称相同的时候，参数名称享有优先权，可以使用self属性来区分参数名称和属性名称。
* 变异mutating
    - 结构体和枚举是值类型，值类型属性不能在实例方法中被修改，在实例方法中修改值类型：mutating，修改后的值会一直保留
    - 赋给隐含属性self一个全新的实例，新实例在方法结束后将替换原来的实例

```
struct Area {
    var length = 1
    var breadth = 1

    func area() -> Int {
        return length * breadth
    }

    mutating func scaleBy(res: Int) {
        self.length *= res
        self.breadth *= res
        print(length)
        print(breadth)

        //只有在变异方法中才可以给self赋值
        //self = Area(length:self.length*res, breadth:self.breadth*res)
    }
}

//若实例对象为let,不可修改
var val = Area(length: 3, breadth: 5)
val.scaleBy(res: 13)
print("\(val.length)---\(val.breadth)")
```

* 类型方法：类型方法的方法体中，self指向这个类型本身，而不是类型的某个实例
    - 静态方法只能访问静态方法和属性
    - 实例方法可以访问实例方法和属性，通过类型调用静态方法和属性
    - 用class修饰的方法可以被子类覆盖（重写）
    - 在方法func关键字之前加上关键字`static`来指定类型方法，类还可以用关键字`class`来允许子类重写父类的方法实现

```
class MyClass {
    var v1: Int!
    var s1: String!
    static var ss1: String!
    class func f1(){
        print("static f1")

        //***静态方法只能访问静态方法和属性
        ss1 = "hello"
        //        v1 = 1
        //        f2()
    }
    func f2(){
        print("f2")

        //***实例方法可以访问实例方法和属性，通过类型调用静态方法和属性
        MyClass.ss1 = "world"
        MyClass.f1()
    }
    //用class修饰的方法可以被子类覆盖（重写）
    class func f3() {
        print("class f3")
    }
}

class MySubClass: MyClass {
    //override class：重写   f2不可重写
    override class func f3(){
        print("override f3...")
    }
}

MyClass.ss1 = "static ss1"
print(MyClass.ss1)
MyClass.f1()

MyClass.f3()
MySubClass.f3()
```

# 下标
可以定义在类，结构体，枚举中，是访问对象、集合、序列的快捷方式
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

* 下标脚本类似于实例方法((Int)->Int)和计算型属性（set,get）的混合
* 可以读写或只读
* newValue的类型和下标的返回类型相同，默认newValue
```
struct Matrix {
    let rows: Int,columns: Int
    var grid: [Double]
    init(rows: Int,columns: Int){
        self.rows = rows
        self.columns = columns
        grid = Array(repeatElement(5.0, count: rows*columns))
    }
    func indexIsValidForRow(row: Int,column: Int)->Bool{
        return row >= 0 && column >= 0 && row < rows && column < columns
    }
    subscript(row: Int,column: Int)->Double{
        get{
            print("get...\((row*columns)+column)")
            assert(indexIsValidForRow(row: row, column: column), "Index out of range")
            return grid[(row*columns)+column]
        }
        set {
            print("set...\((row*columns)+column)")
            assert(indexIsValidForRow(row: row, column: column), "Index out of range")
            grid[(row*columns)+column] = newValue
        }
    }
}
var matr = Matrix(rows: 6, columns: 6)
print(matr.grid)
print(matr[5,5])
```

# 继承
## 基类
* 如果你不为你定义的类指定一个超类的话，这个类就自动成为基类。

## 重写override
* 可以重写继承来的实例方法、类方法、实例属性、类型属性，自定义getter和setter或添加属性观察器（可重写计算属性，方法，存储属性不可重写，可重写属性观察器）
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
* 存储属性在构造器中赋值时，它们的值是被直接设置的，不会触发任何属性观察器。
* 存储属性在构造器中赋值流程
    * 创建初始值
    * 在属性定义中指定默认属性值
    * 初始化实例，并调用init()方法
    
* 可选属性类型：当存储属性声明为可选时，将自动初始化为空 nil
* 修改常量属性：`在init里可以对let的实例常量进行赋值`，可以在构造过程中的任意时间点给常量属性指定一个值，但是它的常量属性只能在定义它的类的构造过程中修改；不能在子类中修改
* 外部参数名：Swift 会为每个构造器的参数自动生成一个跟内部名字相同的外部名，如果不想提供外部名称可以使用下划线(_)来显式描述它的外部名
* 默认构造器：如果结构体或类的所有属性都有默认值，且它是没有父类的基类，同时没有自定义的构造器，swift会自动提供默认构造器，默认构造器将简单的创建一个所有属性值都设置为默认值的实例
* 逐一成员构造器：结构体自动获得一个逐一成员构造器
* 构造器代理规则
    * 值类型：不支持继承，可以使用self.init在自定义的构造器中引用其它的属于相同值类型的构造器
    * 类类型：支持集成，类有责任保证其所有继承的存储型属性在构造时也能正确的初始化

## 类的继承和构造过程
* 类里面的所有存储型属性——包括所有继承自父类的属性——都必须在构造过程中设置初始值，可以通过指定构造器或便利构造器实现。
### 指定构造器
* 初始化类中提供的所有属性，并根据父类链往上调用父类的构造器来实现父类的初始化，每一个类都必须拥有至少一个指定构造器
```
init(parameters) {
statements
}
```

### 便利构造器convenience
* 便利构造器可以调用同一个类中的指定构造器，并为其参数提供默认值，必要是创建
```
convenience init(parameters) {
statements
}
```

### 构造器的继承和重载：当重写一个父类指定构造器时，子类不会默认继承父类的构造器，需要写override，`override convenience`

## 可失败构造器（init?,init!）
* 非可失败构造器（init），可失败构造器（init?），隐式解包可失败构造器（init!）
* 可失败构造器：可失败构造器会创建一个类型为自身的可选类型的对象，在init关键字后面添加问号(init?)，通过`return nil`来表明可失败构造器在何种情况下应该失败。
* 可失败构造器的参数名和参数类型，`不能与其它非可失败构造器的参数名，及其参数类型相同`。
* 变量初始化失败可能的原因有：
    * 传入无效的参数值
    * 缺少某种所需的外部资源
    * 没有满足特定条件

```
struct Animal {
    let species: String
    init?(species: String) {
    if species.isEmpty { return nil }
        self.species = species
    }
}
```

* 枚举类型的可失败构造器
```
enum TemperatureUnit {
    // 开尔文，摄氏，华氏
    case Kelvin, Celsius, Fahrenheit
    init?(symbol: Character) {
        switch symbol {
            case "K":
            self = .Kelvin
            case "C":
            self = .Celsius
            case "F":
            self = .Fahrenheit
            default:
            return nil
        }
    }
}
```

* 何时触发：值类型的可失败构造器何时触发没有限制，类的可失败构造器只能在所有的类属性被初始化后和所有类之间的代理调用发生完后触发失败行为
* 覆盖一个可失败构造器：可以用子类的可失败构造器或非失败构造器覆盖基类的可失败构造器
* 但一个非失败构造器永远不能代理调用一个可失败构造器
* init!
```
struct StudRecord {
    let stname: String

    init!(stname: String) {
    if stname.isEmpty {return nil }
    self.stname = stname
    }
}

let stmark = StudRecord(stname: "Runoob")
if let name = stmark {
    print("指定了学生名")
}

let blankname = StudRecord(stname: "")
if blankname == nil {
    print("学生名为空")
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

# 析构过程
*
```
deinit {
// 执行析构过程
}
```

# 自动引用计数

> 引用计数仅仅应用于类的实例。结构体和枚举类型是值类型。

## 类实例之间的循环强引用

### 弱引用 weak

* 当 ARC 设置弱引用为nil时，属性观察不会被触发

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

### 无主引用 unowned

```
loadData{ [unowned self] in
print("\(self.view)")
}
```

### 循环引用总结
* `对于生命周期中会变为nil的实例使用弱引用。相反的，对于初始化赋值后再也不会被赋值为nil的实例，使用无主引用。` 当其他的实例有更短的生命周期时，使用弱引用(当其他实例析构在先)，当其他实例有相同的或者更长生命周期时，请使用无主引用
* swift
    * `[weak self]`：self是可选项，如果self已经被释放，则为nil
    * `[unowned self]`：self不是可选项，如果self已经被释放，则为野指针访问，使用无主引用，必须确保引用始终指向一个未销毁的实例
* Objc
    * `__weak typeof(self) weakSelf;`：如果self已经被释放，则为nil
    * `__unsafe_unretained typeof(self) weakSelf;`：如果self已经被释放，则为野指针访问

## 闭包引起的循环强引用

> 解决闭包引起的循环强引用:闭包捕获列表，在定义闭包时同时定义捕获列表作为闭包的一部分

* 当闭包和捕获的实例总是互相引用时并且总是`同时销毁`时，将闭包内的捕获定义为`无主引用`。
相反的，当捕获引用有时`可能会是nil`时，将闭包内的捕获定义为`弱引用`。
如果捕获的引用`绝对不会置为nil`，应该用`无主引用`，而不是弱引用。

```
lazy var someClosure: (Int, String) -> String = {
[unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
// 这里是闭包的函数体
}
```

# 可选链

> 可以应用于任意类型，并且能检查调用是否成功。
> `多次请求或调用可以被链接成一个链，如果任意一个节点为nil将导致整条链失效`

* 通过可选链访问属性，返回两个值：
    * 如果目标有值，调用成功，返回该值
    * 如果目标为nil，调用返回nil
* 通过可选链调用方法，返回两个值：
    * 如果调用成功，返回Void
    * 如果没有称，返回nil

* 可选链可代替强制解析：
    * ?：当可选为nil，输出预定的错误信息
    * ! ：当可选为nil，强制展开执行错误
    
* 连接多层可选链：如果试图通过可选链获得Int值，不论使用了多少层链接返回的总是Int?。
* 可以通过可选链调用返回为可空值的方法，并且可以继续对可选值进行链接。

# 错误处理
## 表示并抛出错误(throw)
```
//枚举构建一组相关的错误状态，枚举的关联值还可以提供错误状态的额外信息
enum VendingMachineError: Error {
case invalidSelection                    //选择无效
case insufficientFunds(coinsNeeded: Int) //金额不足
case outOfStock                          //缺货
}
//抛出一个错误
throw VendingMachineError. insufficientFunds(coinsNeeded: 5)
```

## throwing函数
* `throws`写在函数声明的参数列表后，`->`前。
> throwing函数可在其内部抛出错误，将错误传递到函数被调用时的作用域，非throwing函数内部抛出的错误只能在函数内部处理。

```
func canThrowErrors() throws -> String
```

## 处理错误
* 处理错误的四种方式：
    * 把函数抛出的错误传递给调用此函数的代码
    * do-catch语句
    * 将错误作为可选类型：`try?`
    * 断言此错误根本不会发生
    
### do-catch
```
do {
    try expression
    statements
} catch pattern 1 {
    statements
} catch pattern 2 where condition {
    statements
}
```

### 将错误转换成可选值 try?
* 如果在评估try?表达式时一个错误被抛出，那么表达式的值就是nil。
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

### 禁用错误传递 try!
* 会把调用包装在一个不会有错误抛出的运行时断言中，如果真的抛出，则会发生运行时错误。

## 指定清理操作 defer
* defer语句在即将离开当前代码块时（throw,return,break等）执行一系列语句。
将代码的执行延迟到当前作用域退出之前，延时执行的操作会按照被指定时的顺序的相反顺序执行，即第一条defer语句中的代码会在第二条defer语句中的代码被执行之后才执行，以此类推。
* `即使没有涉及到错误处理，也可以使用defer语句`

# 类型转换
## 检查值的类型：is
* 用类型检查操作符（is）来检查一个实例是否属于特定子类型。

## 向下转型：as
* 某类型的一个常量或变量可能在幕后实际上属于一个子类，用`as`向下转到它的子类型。
* 转换没有真的改变实例或它的值
* 类型转换的条件转换：as? as!
    - 可选形式as?：下转成一个可选值
    - 强制形式as!：向下转型+强制解包
    
```
for item in library{
    //（1）item不确定是什么类型，所以as?
    if let movie = item as? Movie{
        //（2）使用可选绑定类型进行可选解析
        print("\(movie.name)--\(movie.director)")
    }else if let song = item as? Song{
        print("\(song.name)--\(song.artist)")
    }
}
```

## 获取实例的类型名称
```
let someStr = "123"
print(type(of: someStr))    //String
```

## Any 和 AnyObject 的类型转换
* Any 可以表示任何类型，包括函数类型、可选类型。
* AnyObject 可以表示任何类型的实例。
> Any类型可以表示所有类型的值，包括可选类型。Swift 会在你用Any类型来表示一个可选值的时候，给你一个警告。如果你确实想使用Any类型来承载可选值，你可以使用as操作符显式转换为Any。
```
// 可以存储Any类型的数组
var arr1 = [Any]()
// [AnyObject] 类型的数组
var arr2: [AnyObject]
```

# 嵌套类型
* 类、枚举、结构体之间可相互嵌套

# 扩展 Extensions

```
extension SomeType {
// 为 SomeType 添加的新功能写到这里
}
extension SomeType: SomeProtocol, AnotherProctocol {
// 协议实现写到这里
}
```

* 与OC中的分类类似，但是swift中的扩展没有名字
* 扩展可以添加新的计算属性，但是不可以添加存储属性，也不可以向已有属性添加属观察器
* 可用于向已有的类、结构体、枚举或协议添加新功能，但是不能重写已有的功能。
* 通过为一个已有类型添加新功能，则`新功能对该类型的所有实例都有效`，即使它们是在扩展定义之前创建的
* 可以实现的功能：
    - 添加计算型属性和计算型类型属性
    - 定义实例方法和类型方法
    - 提供新的构造器
    - 定义下标
    - 定义和使用新的嵌套类型
    - 使一个已有类型符合某个协议
    
* swift4：extension中可以访问private属性

## 计算型属性
* 扩展可以添加新的计算属性，但是不可以添加`存储型属性`，也不可以为已有属性添加`属性观察器`。

## 构造器
* 扩展可以为类添加新的便利构造器，不可以添加新的指定构造器或析构器。指定构造器和析构器必须由原始类提供。

## 方法
通过扩展添加的实例方法可以修改该实例本身，结构体和枚举类型中修改self或其属性的方法必须将该实例方法标注为mutating

# 协议
类、枚举、结构体都可以实现协议

## 协议语法
定义协议：
```
protocol SomeProtocol {
// 协议的定义部分
}
```

遵循协议：
```
struct SomeStructure: FirstProtocol, AnotherProtocol {
// 结构体的定义部分
}
class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol {
// 类的内容
}
```

## 属性要求
* 协议可以要求遵循协议的类型提供的实例属性或类型属性的`名称和类型`，还可指定`读写性`，但不用指定是存储型属性或计算型属性。
> 用`var`声明变量属性，用`{set get}`声明属性是可读可写的，`{get}`表示只读属性。

* 类属性要求使用static前缀关键字
```
protocol AnotherProtocol {
    static var someTypeProperty: Int { get set }
}
```

## 方法要求
* 方法不需要大括号和方法体
* 不支持为协议中的方法的参数提供默认值

```
protocol classa {
var marks: Int { get set }
var result: Bool { get }

func attendance() -> String
func markssecured() -> String
}
```

## Mutating 方法要求
* 表示可以在该方法中修改它所属的实例及其实例属性的值
* 实现协议中的mutating方法时，若是类类型，则不用写`mutating`，对于结构体和枚举必须写。

```
protocol SomeProtocal {
    mutating func show()
    }
}
```

## 构造器要求
* 协议构造器在类中的实现：在遵循该协议的类中实现构造器，并指定其为类的指定构造器或便利构造器，这时必须为构造器实现标上`required`修饰符，可以确保遵循该协议的子类也能提供此构造器的显式实现或继承实现。
```
class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
    // 构造器实现
    }
}
```

* 如果一个子类重写了父类的指定构造器，并且该构造器满足了某个协议的要求，则构造器的实现需要标上`required`和`override`。
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

## 协议作为类型
* 作为函数、方法或构造器中的参数类型或返回值类型
* 作为常量、变量或属性的类型
* 作为数组、字典或其他容器中的元素类型

## 协议可以继承
```
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
// 协议定义
}
```

## 类专属协议
* 在协议继承列表添加`class`关键字，可以限制协议只能适配到类类型
* `该class关键字必须是第一个出现在协议的继承列表中，其后才是其他集继承协议`

```
protocol SomeClassOnlyProtocol: class, SomeInheritedProtocol {
// 协议定义
}
```

## 协议的合成
```
protocol Stname {
var name: String { get }
}

protocol Stage {
var age: Int { get }
}

struct Person: Stname, Stage {
var name: String
var age: Int
}

func show(celebrator: Stname & Stage) {
print("\(celebrator.name) is \(celebrator.age) years old")
}

let studname = Person(name: "Priya", age: 21)
print(show(celebrator: studname))   //Priya is 21 years old

```

## 检查协议一致性
* is：检查实例湿度遵循了某个协议
* as?：当实例遵循了协议时，返回该协议类型，否则返回nil
* as! ：强制向下转换类型，如果失败，会引起运行时错误

# 泛型
* 泛型使用占位类型名（在这里用字母 T 来表示）来代替实际类型名（例如 Int、String 或 Double）。
```
// 定义一个交换两个变量的函数
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}

//泛型的栈
struct Stack<Element> {
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}
var stackOfStrings = Stack<String>()
```

## 扩展泛型类型
```
extension Stack {
    //扩展了一个只读计算型属性
    var topItem: Element? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}
```

## 类型约束
```
//T 必须是 SomeClass 子类的类型约束,要求 U 必须符合 SomeProtocol 协议的类型约束
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
// 这里是泛型函数的函数体部分
}
```

## 关联类 associatedtype
```
// Container 协议
protocol Container {
    associatedtype ItemType
    // 添加一个新元素到容器里
    mutating func append(_ item: ItemType)
    // 获取容器中元素的数
    var count: Int { get }
    // 通过索引值类型为 Int 的下标检索到容器中的每一个元素
subscript(i: Int) -> ItemType { get }
}

// Stack 结构体遵从 Container 协议
struct Stack<Element>: Container {
// Stack<Element> 的原始实现部分
    var items = [Element]()
    mutating func push(_ item: Element) {
    items.append(item)
    }
    mutating func pop() -> Element {
    return items.removeLast()
    }
    // Container 协议的实现部分
    mutating func append(_ item: Element) {
    self.push(item)
    }
    var count: Int {
    return items.count
    }
    subscript(i: Int) -> Element {
    return items[i]
    }
}
```

## where语句
```
//用来检查两个Container实例是否包含相同顺序的相同元素
func allItemsMatch<C1: Container, C2: Container>
(_ someContainer: C1, _ anotherContainer: C2) -> Bool
where C1.ItemType == C2.ItemType, C1.ItemType: Equatable {

    // 检查两个容器含有相同数量的元素
    if someContainer.count != anotherContainer.count {
        return false
    }

    // 检查每一对元素是否相等
    for i in 0..<someContainer.count {
        if someContainer[i] != anotherContainer[i] {
            return false
        }
    }

    // 所有元素都匹配，返回 true
    return true
}
```

# 访问控制
* 可以给单个类型（类、结构体、枚举）设置访问级别，也可以给这些类型的属性、函数、初始化方法、基本类型、下标、协议等设置访问级别
* 访问控制基于模块与源文件
    * 模块：独立单元构建和发布的framework或application
    * 源文件：通常属于一个模块，源文件可以包含多个类和函数的定义

## 四种访问级别
* 四种访问级别，从高到低，默认为`internal`：
| 访问级别 | 权限  |
|:--------:|----------|
|   public    | 可以访问自己模块中源文件里的任何实体，别人也可以通过引入该模块来访问源文件里的所有实体   |
|   internal   | 可以访问自己模块中源文件里的任何实体，别人不能   |
|   fileprivate    | 文件内私有，只能在当前源文件中使用   |
|   private    | 只能在类中访问，离开了这个类或者结构体的作用域外面就无法访问   |

## 访问级别的原则
* `元组`的访问级别与元组中访问级别`最低的类型`一致（元组 = min（元组中元素））
```
internal class SomeInternalClass {}
private class SomePrivateClass {}
//该函数返回类型的访问级别是 private，必须使用 private 修饰符，明确的声明该函数
func someFunction() -> (SomeInternalClass, SomePrivateClass) {
// 函数实现
}
```

* 枚举中`成员`的访问级别`继承自该枚举`，不能为枚举中的成员单独声明不同的访问级别（枚举成员 = 枚举）
```
public enum Student {
    case Name(String)
    case Mark(Int,Int,Int)
}
```

* 子类的访问级别`不得高于父类`的访问级别。（子类 <= 父类）
```
public class SuperClass {
    fileprivate func show() {
    print("超类")
    }
}

// 访问级别不能低于超类 internal > public
internal class SubClass: SuperClass  {
    override internal func show() {
    print("子类")
    }
}
```

* 常量、变量、属性`不能拥有比它们的类型更高`的访问级别，下标也不能拥有比索引类型或返回类型更高的访问级别（常量、变量、属性、下标 <= 它的类型）

* 常量、变量、属性、下标索引的getter和setter的访问级别`继承自他们所属成员`的访问级别，setter的访问级别可以低于对应的getter的访问级别，这样可以控制常量、变量、下标的读写权限（getter和setter = 常量、变量、属性、下标索引，getter >= setter）

* 构造器和默认构造器访问级别：
    * 自定义的初始化方法 <= 它所属类
    * 必要构造器 = 所属类
    * 函数中，初始化方法参数 >= 初始化方法
    * 默认构造器 = 所属类型

* 协议访问级别（请确保该协议只在声明的访问级别作用域中使用）：实现协议的必要函数 = 协议

* 扩展访问级别：扩展成员 = 原始类成员，单独成员所声明的访问级别可以覆盖扩展的默认访问级别。

* 泛型访问级别 = min（泛型类型、函数本身、泛型类型参数）
```
public struct TOS<T> {
    var items = [T]()
    private mutating func push(item: T) {
        items.append(item)
    }

    mutating func pop() -> T {
        return items.removeLast()
    }
}
```

* 类型别名 <= 原类型

# 高级运算符
## 位运算符
* 按位取反：~
* 按位与：&
* 按位或：|
* 按位异或：^
* 按位左移：<<(乘2)
* 按位右移：>>(除2)
* 有符号整数的移位运算：0代表证书，1代表负数，负数表示用二进制补码

## 溢出运算符
* 溢出加法：&+
* 溢出减法：&-
* 溢出乘法：&*

## 优先级和结合性
* 乘法与取余运算都是左结合

## 运算符函数

## 自定义运算符


# Swift UI部分

Q:编译错误：`The “Swift Language Version” (SWIFT_VERSION) build setting must be set to a supported value for targets which use Swift. This setting can be set in the build settings editor.`
A:解决：Build Settings->Swift Language Version 选择一个最新的swift版本

```
var myBtn: UIButton?
myBtn = UIButton(type:UIButtonType.system)
myBtn!.frame = CGRect(x: 100, y: 100, width: 100, height: 44)
myBtn!.backgroundColor = UIColor.red
myBtn!.setTitle("注册", for: UIControlState.normal)
myBtn!.addTarget(self, action: Selector(("register:")), for: UIControlEvents.touchUpInside)
self.view.addSubview(myBtn!)
```

```
var myBtn: UIButton!
myBtn = UIButton(type:UIButtonType.system)
myBtn!.frame = CGRect(x: 100, y: 100, width: 100, height: 44)
myBtn!.backgroundColor = UIColor.red
myBtn!.setTitle("注册", for: .normal)
myBtn!.addTarget(self, action: #selector(register(sender:)), for: .touchUpInside)
view.addSubview(myBtn!)

@objc func register(sender: UIButton){
print("register click")
}
```

# swift的常用内置函数总结
* abs
* assert
* countElements
* enumerate
* sort
* contains(sequence, element)
* dropFirst(sequence)
* dropLast(sequence)
* dump(object)：打印出某个对象object的所有信息
* equal(sequence1, sequence2)：判断两个序列是否相等
* filter(sequence, includeElementClosure)：对序列sequence中每个元素都执行includeElementClosure闭包，并将所有闭包结果为true的元素合成一个新序列sequence并返回。
* find(sequence, element)：返回序列sequence中某元素element的位置index。如果序列中不存在此元素，则返回nil。
* indices(sequence)：返回序列sequence中所有元素的位置（indices是index的复数）
* join(separator, sequence)：将序列sequence通过分隔符separator连成一个字符串，并返回此字符串。
* map(sequence, transformClosure)：对序列sequence中每个元素都执行includeElementClosure闭包，并将所有闭包的结果合成一个新序列sequence并返回。
* max(comparable1, comparable2, etc.)  min(comparable1, comparable2, etc.)
* maxElement(sequence)：返回序列sequence中的最大值。  minElements(sequence)：返回序列sequence中的最小值。
* reverse(sequence)：返回逆序的序列sequence。

# 常用有用知识点总结
* try-catch
* assert
* extension
* fatalError
* 柯里化：把接受多个参数的方法变换成接受第一个参数的方法，并且返回接受余下的参数并且返回结果的新方法。
* mutating


参考资料：[The Swift Programming Language中文版](http://wiki.jikexueyuan.com/project/swift)
[菜鸟教程-swift教程](http://www.runoob.com/swift/swift-tutorial.html)
[Swift的74个常用内置函数介绍](http://blog.csdn.net/banma2008/article/details/46360333)
[深入探究Swift数组背后的协议、方法、拓展](http://www.cocoachina.com/ios/20151218/14716.html)
[swift常用代码](http://www.cnblogs.com/Jepson1218/p/5277677.html)
[https://github.com/yagamis/swift2basic](https://github.com/yagamis/swift2basic)
[https://github.com/KeyJohn/Swift](https://github.com/KeyJohn/Swift)
