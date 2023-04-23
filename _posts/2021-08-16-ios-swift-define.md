---
title: "Swift 常用宏定义"
layout: post
date: 2021-8-16 12:00
image: #/assets/images/markdown.jpg
headerImage: false
tag:
- iOS
- Swfit
category: blog
author: #Yuan Lee
description: #Markdown summary with different options
# jemoji: '<img class="emoji" title=":ramen:" alt=":ramen:" src="http://localhost:4000/assets/images/profile.jpg" height="20" width="20" align="absmiddle">'
---

## Summary

iOS 开发中使用 Swift 时的一些常用宏定义

---

#### 1.封装的日志输出功能
- 此处用到的 DEBUG 需要提前定义，Build Settings -> Swift Compiler - Custom Flags -> Other Swift Flags
{% highlight raw %}
/// 为 Swift 封装的日志输出功能（T表示不指定日志信息参数类型)
/// - Parameter message:    要打印的内容
/// - Parameter file:       当前所在文件
/// - Parameter methodName: 当前位置的方法名
/// - Parameter lineNumber: 当前位置所在行数
public func NSLog<T>(_ message: T, file : String = #file, methodName: String = #function, lineNumber : Int = #line) {
    #if DEBUG

    ///去掉 .swift
    ///let filePath = file as NSString
    ///let filePath_copy = filePath.lastPathComponent as NSString
    ///let fileName = filePath_copy.deletingPathExtension
    
    //获取文件名
    ///带后缀 .swift
    let fileName = (file as NSString).lastPathComponent
    // 创建一个日期格式器
    let dformatter = DateFormatter()
    // 为日期格式器设置格式字符串
    dformatter.dateFormat = "yyyy-MM-dd HH:mm:ss.SSSSSS"
    // 使用日期格式器格式化当前日期、时间
    let datestr = dformatter.string(from: Date())
    //打印日志内容
    print("\(datestr)\n    文件:\(fileName)[第\(lineNumber)行]\n    方法:\(methodName)\n    打印:\(message)")
    #else
    #endif
}
{% endhighlight %}

#### 2.为 Swift 封装的发送通知的快捷方法
{% highlight raw %}
/// 为 Swift 封装的发送通知的快捷方法
/// - Parameter name:      通知名称
/// - Parameter object:    通知对象
/// - Parameter userInfo:  通知内容
public func postNotification(name: String, object: Any? = nil, userInfo: [AnyHashable : Any]? = nil) {
    NotificationCenter.default.post(name: NSNotification.Name.init(rawValue: name), object: object, userInfo: userInfo)
}
{% endhighlight %}

#### 3.为 Swift 封装的添加通知监听的快捷方法
{% highlight raw %}
/// 为 Swift 封装的添加通知监听的快捷方法
/// - Parameter observer: 监听者
/// - Parameter selector: 收到通知时执行的方法
/// - Parameter name:     要监听的通知名称
/// - Parameter object:   发送通知的对象
public func addNotificationObserver(_ observer: Any, selector: Selector, name: String, object: Any? = nil) {
    NotificationCenter.default.addObserver(observer, selector: selector, name: NSNotification.Name.init(name), object: object)
}
{% endhighlight %}

#### 4.为 Swift 封装的添加通知监听并执行 ‘闭包’ 的快捷方法
{% highlight raw %}
/// 为 Swift 封装的添加通知监听并执行 ‘闭包’ 的快捷方法
/// - Parameter forName: 要监听的通知名称
/// - Parameter object:  发送通知的对象，如果为 nil，监听任何对象
/// - Parameter queue:   执行闭包的线程，如果为 nil，在主线程执行
/// - Parameter block:   收到通知时要执行的闭包
/// - Parameter note:    返回的 ’Notification‘ 通知对象
public func addNotificationObserver(forName: String, object: Any? = nil, queue: OperationQueue? = nil, using block: @escaping (_ note: Notification) -> Void) {
    NotificationCenter.default.addObserver(forName: NSNotification.Name.init(forName), object: object, queue: queue, using: block)
}
{% endhighlight %}

#### 5.为 Swift 封装的移除通知监听的快捷方法
{% highlight raw %}
/// 为 Swift 封装的移除通知监听的快捷方法
/// - Parameter observer: 监听者
/// - Parameter name:     通知名称
/// - Parameter object:   发送通知的对象
public func removeNotificationObserver(_ observer: Any, name: NSNotification.Name? = nil, object: Any? = nil) {
    NotificationCenter.default.removeObserver(observer, name: name, object: object)
}
{% endhighlight %}

#### 6.RGBA 方式表示颜色， Alpha 默认为 1.0
{% highlight raw %}
/// RGBA 方式表示颜色， Alpha 默认为 1.0
/// - Parameters:
///   - rgb:   十六进制数字，如 0xFFFFFF
///   - alpha: 透明度百分比，范围: 0 ~ 1.0，默认为 1.0
///     - 0 表示完全透明，1 表示完全不透明
/// - Returns: UIColor
public func RGBColor(rgb: Int, alpha: CGFloat = 1.0) -> UIColor {
    UIColor.init(red: ((CGFloat)((rgb & 0xFF0000) >> 16)) / 255.0, green: ((CGFloat)((rgb & 0xFF00) >> 8)) / 255.0, blue: ((CGFloat)(rgb & 0xFF)) / 255.0, alpha: alpha)
}
{% endhighlight %}

#### 7.在主线程延迟执行函数
{% highlight raw %}
/// 在主线程延迟执行函数
/// - Parameters:
///   - delta:   延迟时间，单位 - 秒
///   - execute: 要延迟执行的闭包
public func delay(_ delta: Double, _ execute: @escaping () -> Void) {
    let deadline = DispatchTime.now() + delta
    DispatchQueue.main.asyncAfter(deadline: deadline, execute: execute)
}
{% endhighlight %}