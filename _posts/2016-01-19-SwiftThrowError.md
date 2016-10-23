---
layout: post
title: "Swift异常处理"
date: 2016-01-19 19:50:11.000000000 +09:00
tags: Swift实用
---

- Swift中如果在调用一个方法时,看到该方法最后一个throws,表示这个方法可能会抛出异常(错误).
- 如果有抛出异常必须对异常进行处理
- 对异常处理有三种处理
  - do-catch
  - 转换成可选值
  - 使错误传递失效

范例中对json数据进行解析并转为模型,其中函数JSONObjectWithData会产生异常抛出
```swift
 override func viewDidLoad() {
        super.viewDidLoad()
        
        //获取路径
        guard let path = NSBundle.mainBundle().pathForResource("ABC", ofType: "json") else{     
            return
        }
        
        //将json数据转换成NSData
        guard let jsonData = NSData(contentsOfFile: path) else{
            return
        }
        
        //将json解析成数组
        guard let array = NSJSONSerialization.JSONObjectWithData(jsonData, options: .MutableContainers) else {
            return
        }
             
    }
```
程序写到这会看到Xcode报错

![Snip20160119_1.png](http://upload-images.jianshu.io/upload_images/744318-10fa5c06b3d40e0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 方式一:do-catch 处理

```swift 
//将json解析成数组
        do {
            let array = try NSJSONSerialization.JSONObjectWithData(jsonData, options: .MutableContainers)   
            print("如果出现异常,包括该行在内后续代码均不运行")  
            //如果不出现异常,后续转模型代码需写在这里
        }catch{
            // 一旦发现异常,就会直接执行catch中代理,并且catch有一个error变量.error中记录当前的异常
            print(error)
        }
        
```

- 方式二:转换成可选值

```Swift
//将json解析成数组
        //让系统自己处理异常,如果有异常直接给我返回nil.如果没有异常,则给我返回一个可选类型
        guard let array = try? NSJSONSerialization.JSONObjectWithData(jsonData, options: .MutableContainers) else{
            return
        }
```

- 方式三:使错误传递失效

```swift
//将json解析成数组
        // 不建议实用,类似于强制解包
        // 如果确定该函数在运行时不会抛出异常,可以使用这种写法
        // 如果抛出异常,程序崩溃
        let array = try! NSJSONSerialization.JSONObjectWithData(jsonData, options: .MutableContainers)
```

*以上三种方法,第三种不建议使用.第二种方法相对第一种更为简单明朗.*

