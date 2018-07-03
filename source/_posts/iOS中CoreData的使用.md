---
title: iOS中CoreData的使用
date: 2018-07-03 15:23:45
categories:
tags:
toc:
---

## 创建工程时选择 use Core Data
<!--more-->
### 写数据
```
- (void)writeData{

    //暂存器
    AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
    NSManagedObjectContext *managedContext = appDelegate.persistentContainer.viewContext;

    NSEntityDescription *entity = [NSEntityDescription entityForName:@"Student" inManagedObjectContext:managedContext];

    NSManagedObject *student = [[NSManagedObject alloc]initWithEntity:entity insertIntoManagedObjectContext:managedContext];

    NSString *name = @"zhangsan";
    [student setValue:name forKey:@"name"];

    NSError *error = nil;
    if (![managedContext save:&error]) {
        NSLog(@"Could not save (%@), (%@)",error,error.userInfo);
    } else {
        NSLog(@"student 存储成功");
    }
}

```

### 读数据
```
- (void)readData{

    //暂存器
    AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
    NSManagedObjectContext *managedContext = appDelegate.persistentContainer.viewContext;

    NSFetchRequest *fetchRequest = [[NSFetchRequest alloc]initWithEntityName:@"Student"];
    NSError *error = nil;
    NSArray *fetchedResults = [managedContext executeFetchRequest:fetchRequest error:&error];
    if (!fetchedResults) {
        NSLog(@"Could not fetch (%@), (%@)",error,error.userInfo);
    }else{
        for (NSManagedObject *each in fetchedResults) {
            NSLog(@"%@",[each valueForKey:@"name"]);
        }
    }
}
```

## 创建工程之后关联core data
### 新建一个Data Model对象，添加实体（必须大写），属性，类型等
注意：
* Codegen：Manual/None
* Language
### 创建实体
Editor->Create NSManagedObject Subclass
### 生成上下文 关联数据库
NSManagedObjectContext 管理对象，上下文，持久性存储模型对象，处理数据与应用的交互
NSManagedObjectModel 被管理的数据模型，数据结构
NSPersistentStoreCoordinator 添加数据库，设置数据存储的名字，位置，存储方式
NSManagedObject 被管理的数据记录
NSFetchRequest 数据请求
NSEntityDescription 表格实体结构

```
//创建数据库
- (void)createSqlite{

    //1、创建模型对象
    //获取模型路径
    NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"Model" withExtension:@"momd"];
    //根据模型文件创建模型对象
    NSManagedObjectModel *model = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];

    //2、创建持久化存储助理：数据库
    //利用模型对象创建助理对象
    NSPersistentStoreCoordinator *store = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:model];

    //数据库的名称和路径
    NSString *docStr = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject];
    NSString *sqlPath = [docStr stringByAppendingPathComponent:@"coreData.sqlite"];
    NSLog(@"数据库 path = %@", sqlPath);
    NSURL *sqlUrl = [NSURL fileURLWithPath:sqlPath];

    NSError *error = nil;
    //设置数据库相关信息 添加一个持久化存储库并设置类型和路径，NSSQLiteStoreType：SQLite作为存储库
    [store addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:sqlUrl options:nil error:&error];

    if (error) {
        NSLog(@"添加数据库失败:%@",error);
    } else {
        NSLog(@"添加数据库成功");
    }

    //3、创建上下文 保存信息 对数据库进行操作
    NSManagedObjectContext *context = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSMainQueueConcurrencyType];

    //关联持久化助理
    context.persistentStoreCoordinator = store;
    _context = context;

}
```

### 增删改查排
写入数据
```
- (void)wirteData{
    // 1.根据Entity名称和NSManagedObjectContext获取一个新的继承于NSManagedObject的子类Student
    Student * student = [NSEntityDescription  insertNewObjectForEntityForName:@"Student"  inManagedObjectContext:_context];

    //2.根据表Student中的键值，给NSManagedObject对象赋值
    student.name = [NSString stringWithFormat:@"Mr-%d",arc4random()%100];
    student.age = arc4random()%20;
    student.sex = arc4random()%2 == 0 ?  @"美女" : @"帅哥" ;
    student.height = arc4random()%180;
    student.number = arc4random()%100

    //   3.保存插入的数据
    NSError *error = nil;
    if ([_context save:&error]) {
    [self alertViewWithMessage:@"数据插入到数据库成功"];
    }else{
    [self alertViewWithMessage:[NSString stringWithFormat:@"数据插入到数据库失败, %@",error]];
    }
}
```

删除数据
```
- (void)deleteData{

//创建删除请求
NSFetchRequest *deleRequest = [NSFetchRequest fetchRequestWithEntityName:@"Student"];

//删除条件
NSPredicate *pre = [NSPredicate predicateWithFormat:@"age < %d", 10];
deleRequest.predicate = pre;

//返回需要删除的对象数组
NSArray *deleArray = [_context executeFetchRequest:deleRequest error:nil];

//从数据库中删除
for (Student *stu in deleArray) {
[_context deleteObject:stu];
}

NSError *error = nil;
//保存--记住保存
if ([_context save:&error]) {
[self alertViewWithMessage:@"删除 age < 10 的数据"];
}else{
NSLog(@"删除数据失败, %@", error);
}
}
```

更改数据
```
//更新，修改
- (void)updateData{

//创建查询请求
NSFetchRequest *request = [NSFetchRequest fetchRequestWithEntityName:@"Student"];
NSPredicate *pre = [NSPredicate predicateWithFormat:@"sex = %@", @"帅哥"];
request.predicate = pre;

//发送请求
NSArray *resArray = [_context executeFetchRequest:request error:nil];

//修改
for (Student *stu in resArray) {
stu.name = @"且行且珍惜_iOS";
}

//保存
NSError *error = nil;
if ([_context save:&error]) {
[self alertViewWithMessage:@"更新所有帅哥的的名字为“且行且珍惜_iOS”"];
}else{
NSLog(@"更新数据失败, %@", error);
}  
}
```

读取数据
```
//读取查询
- (void)readData{

/* 谓词的条件指令
1.比较运算符 > 、< 、== 、>= 、<= 、!=
例：@"number >= 99"

2.范围运算符：IN 、BETWEEN
例：@"number BETWEEN {1,5}"
@"address IN {'shanghai','nanjing'}"

3.字符串本身:SELF
例：@"SELF == 'APPLE'"

4.字符串相关：BEGINSWITH、ENDSWITH、CONTAINS
例：  @"name CONTAIN[cd] 'ang'"  //包含某个字符串
@"name BEGINSWITH[c] 'sh'"    //以某个字符串开头
@"name ENDSWITH[d] 'ang'"    //以某个字符串结束

5.通配符：LIKE
例：@"name LIKE[cd] '*er*'"   //*代表通配符,Like也接受[cd].
@"name LIKE[cd] '???er*'"

*注*: 星号 "*" : 代表0个或多个字符
问号 "?" : 代表一个字符

6.正则表达式：MATCHES
例：NSString *regex = @"^A.+e$"; //以A开头，e结尾
@"name MATCHES %@",regex

注:[c]*不区分大小写 , [d]不区分发音符号即没有重音符号, [cd]既不区分大小写，也不区分发音符号。

7. 合计操作
ANY，SOME：指定下列表达式中的任意元素。比如，ANY children.age < 18。
ALL：指定下列表达式中的所有元素。比如，ALL children.age < 18。
NONE：指定下列表达式中没有的元素。比如，NONE children.age < 18。它在逻辑上等于NOT (ANY ...)。
IN：等于SQL的IN操作，左边的表达必须出现在右边指定的集合中。比如，name IN { 'Ben', 'Melissa', 'Nick' }。

提示:
1. 谓词中的匹配指令关键字通常使用大写字母
2. 谓词中可以使用格式字符串
3. 如果通过对象的key
path指定匹配条件，需要使用%K

*/

//创建查询请求
NSFetchRequest *request = [NSFetchRequest fetchRequestWithEntityName:@"Student"];
//查询条件
NSPredicate *pre = [NSPredicate predicateWithFormat:@"sex = %@", @"美女"];
request.predicate = pre;

// 从第几页开始显示
// 通过这个属性实现分页
//request.fetchOffset = 0;
// 每页显示多少条数据
//request.fetchLimit = 6;

//发送查询请求
NSArray *resArray = [_context executeFetchRequest:request error:nil];

[self alertViewWithMessage:@"查询所有的美女"];  
}
```

排序
```
//排序
- (void)sort{
//创建排序请求
NSFetchRequest *request = [NSFetchRequest fetchRequestWithEntityName:@"Student"];
//实例化排序对象
NSSortDescriptor *ageSort = [NSSortDescriptor sortDescriptorWithKey:@"age"ascending:YES];
NSSortDescriptor *numberSort = [NSSortDescriptor sortDescriptorWithKey:@"number"ascending:YES];
request.sortDescriptors = @[ageSort,numberSort];
//发送请求
NSError *error = nil;
NSArray *resArray = [_context executeFetchRequest:request error:&error];
if (error == nil) {
[self alertViewWithMessage:@"按照age和number排序"];
}else{
NSLog(@"排序失败, %@", error);
}
}
```

### CoreData调试:
打开Product，选择Edit Scheme.
选择Arguments，在下面的ArgumentsPassed On Launch中添加下面两个选项，如图：
(1)-com.apple.CoreData.SQLDebug
(2)1
