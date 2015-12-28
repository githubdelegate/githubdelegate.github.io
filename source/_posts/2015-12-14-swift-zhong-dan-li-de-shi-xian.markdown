---
layout: post
title: "swift 中单例的实现"
date: 2015-12-14 18:54:44 +0800
comments: true
categories: swift
---

Swift单例
==============

Use the **class constant** approach if you are using Swift 1.2 or above and the **nested struct** approach if you need to support earlier versions._

An exploration of the Singleton pattern in Swift. All approaches below support lazy initialization and thread safety.

Issues and pull requests welcome.

### Approach A: Class constant

```
class SingletonA {
    
    static let sharedInstance = SingletonA()
    
    init() {
        println("AAA");
    }
    
}
```

This approach supports lazy initialization because Swift lazily initializes class constants (and variables), and is thread safe by the definition of `let`.

Class constants were introduced in Swift 1.2. If you need to support an earlier version of Swift, use the nested struct approach below or a global constant.

### Approach B: Nested struct

```
class SingletonB {
    
    class var sharedInstance: SingletonB {
        struct Static {
            static let instance: SingletonB = SingletonB()
        }
        return Static.instance
    }
    
}
```

在类变量里嵌套一个 `Static` 结构体。
`Static` 封装了一个静态的常量，通过 static 定义意味着这个属性只存在一个，注意 Swift 中 static 的变量是延时加载的，意味着 Instance 直到需要的时候才会被创建。同时再注意一下，因为它是一个常量，所以一旦创建之后不会再创建第二次。这些就是单例模式的核心所在：一旦初始化完成，当前类存在一个实例对象，初始化方法就不会再被调用。


Here we are using the static constant of a nested struct as a class constant. This is a workaround for the lack of static class constants in Swift 1.1 and earlier, and still works as a workaround for the lack of static constants and variables in functions.

### Approach C: dispatch_once

The traditional Objective-C approach ported to Swift.

```
class SingletonC {
    
    class var sharedInstance: SingletonC {
        struct Static {
            static var onceToken: dispatch_once_t = 0
            static var instance: SingletonC? = nil
        }
        dispatch_once(&Static.onceToken) {
            Static.instance = SingletonC()
        }
        return Static.instance!
    }
}
```

I'm fairly certain there's no advantage over the nested struct approach but I'm including it anyway as I find the differences in syntax interesting.