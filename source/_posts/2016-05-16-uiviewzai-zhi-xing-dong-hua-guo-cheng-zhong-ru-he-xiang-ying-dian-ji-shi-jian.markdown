---
layout: post
title: "UIView在执行动画过程中如何响应点击事件"
date: 2016-05-16 20:32:50 +0800
comments: true
categories: ios 动画
---

# UIView在执行动画过程中如何响应事件

先看代码

```

- (void)viewDidLoad {
    [super viewDidLoad];

    UIButton *btn = [[UIButton alloc] initWithFrame:CGRectMake(100, 100, 100, 40)];
    self.btn = btn;
    [btn addTarget:self action:@selector(btnClick) forControlEvents:UIControlEventTouchUpInside];
    btn.backgroundColor = [UIColor greenColor];
    [self.view addSubview:btn];
    
}
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
    [UIView animateWithDuration:15 delay:0 options:UIViewAnimationCurveLinear | UIViewAnimationOptionAllowUserInteraction animations:^{
         self.btn.frame = CGRectMake(300, 100, 100, 40);
    } completion:^(BOOL finished) {
        
    }];
}
```
上面是一段很简单的动画代码，问题是在btn做动画，移动过程中，btn不能响应点击事件了。这是因为其实btn 接受事件的区域已经移动到300的位置，你点击300会发现触发了点击事件。实际的动画过程就是`.layer.presentationLayer`在做，就是你能看到的移动过程实际上是`.layer.presentationLayer`.所以要判断要想在动画过程中响应点击事件就需要判断手指点击的点是不是在`.layer.presentationLayer`上，使用UIView方法 `hitTest:withEvent:`方法，这个方法会返回一个View，就是在当前View的层级结构中找到一个最合适的View来处理事件。
	大概的过程就是先调用`pointInside:withEvent:`方法，判断触摸点是不是在当前View中，若返回NO,表示触摸点不在当前View上，`hitTest:withEvent:`方法返回nil；若返回YES，表示触摸点在当前View内，然后从subviews数组中`尾部向前遍历`,每个子View同样执行这两个方法，直到有子视图的hitTest:withEvent:方法返回非空对象或者全部子视图遍历完毕。▷ 若第一次有子视图的hitTest:withEvent:方法返回非空对象,则当前视图的hitTest:withEvent:方法就返回此对象，处理结束
▷ 若所有子视图的hitTest:withEvent:方法都返回nil，则当前视图的hitTest:withEvent:方法返回当前视图自身(self)
• 最终，这个触摸事件交给主窗口的hitTest:withEvent:方法返回的视图对象去处理。
	

现在考虑为什么使用`layer.presentationLayer`,这个可以看[动画解释](http://objccn.io/issue-12-1/),

```
    if ([self.layer.presentationLayer hitTest:touchedPoint]){
        CGPoint newPoint = [self.layer.presentationLayer convertPoint:touchedPoint fromLayer:self.superview.layer];
        NSLog(@"得到新的点=%@",NSStringFromCGPoint(newPoint));
        if (newPoint.x > 0 && newPoint.x < 35) {
            if (self.bulletViewHeadImageTappedBlock) {
                self.bulletViewHeadImageTappedBlock(self.infoDict);
            }
            return YES;
        }
    }

```
看这段代码，`hitTest:touchedPoint`有返回值表示点击点在self.layer.presentationLayer范围内，然后就是转换坐标系把点击点转换到`self.layer.presentationLayer`坐标系里面，就可以判断具体点击在哪个视图上了。


注意：

1. 使用这种方法要把View的userInteractionEnabled=NO.
2. 如果View中如果有Button，也要把Button的userInteractionEnabled = NO.


> 未完待续 

> http://www.superqq.com/blog/2015/04/23/iosyong-hu-dian-ji-shi-jian-chu-li/
> 

