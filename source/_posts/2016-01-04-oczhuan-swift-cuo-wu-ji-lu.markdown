---
layout: post
title: "oc转swift 错误记录"
date: 2016-01-04 14:34:43 +0800
comments: true
categories: "swift"
---

## 1.option enum -> OptionSetType
 
```
rightView.autoresizingMask = UIViewAutoresizingFlexibleLeftMargin |UIViewAutoresizingFlexibleTopMargin;
  
```
转
```
self.rightView?.autoresizingMask = [.FlexibleTopMargin,.FlexibleLeftMargin]
```


* MIN -> min


* 3.swift 闭包中的循环引用
> [Swift闭包二：循环引用](http://southpeak.github.io/blog/2014/06/27/ios-swift-closures-2/)

* 4.swift中和NSRange相关接口使用
	
```
let levels = "ABCDE"

let nsRange = NSMakeRange(1, 4)
// 编译错误
// 'NSRange' is not convertible to 'Range<String.Index>'
levels.stringByReplacingCharactersInRange(nsRange, withString: "AAAA")

let indexPositionOne = levels.startIndex.successor()
let swiftRange = indexPositionOne..<advance(indexPositionOne, 4)
levels.stringByReplacingCharactersInRange(swiftRange, withString: "AAAA")
// 输出：
// AAAAA
```
上面的方法太麻烦啦。
```
let nsRange = NSMakeRange(1, 4)
(levels as NSString).stringByReplacingCharactersInRange(
    nsRange, withString: "AAAA")
```


* 5 中文转拼音

```
+ (NSString *)pinyinString:(NSString *)string
{

    NSMutableString *mutableString = [NSMutableString stringWithString:string];
    CFStringTransform((CFMutableStringRef)mutableString, NULL, kCFStringTransformToLatin, false);
    CFStringTransform((CFMutableStringRef)mutableString, NULL, kCFStringTransformStripDiacritics, false);
    NSLog(@"%@",mutableString);
    return mutableString;
}


```
