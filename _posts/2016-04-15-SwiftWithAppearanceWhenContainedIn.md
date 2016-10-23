---
layout: post
title: "Swift下的appearanceWhenContainedIn"
date: 2016-04-15 17:19:11.000000000 +08:00
tags: Swift实用
---

公司最近项目重构，用的Swift语言处理。利用代码处理tabBar时候，习惯性的用OC的appearanceWhenContainedIn：方法得到全局的tabBarItem修改属性。但是在swift中却没有这个方法。
Swift中另外提供了一个方法（如下）代替原有的，但是，这个方法适配iOS9.0以上，所以，悲剧了。

```swift
public static func appearanceWhenContainedInInstancesOfClasses(containerTypes: [AnyObject.Type]) -> Self
```

但是在OC下的appearanceWhenContainedIn：还是可以使用的。于是用OC写了一个分类进行桥接。废话不多说，上代码

```ObjC
//UIBarItem+UIAppearance_Swift.h
@interface UIBarItem (UIAppearance_Swift)

+ (instancetype)aw_appearanceWhenContainedIn:(Class<UIAppearanceContainer>)containerClass;

@end

```

```ObjC
//UIBarItem+UIAppearance_Swift.m

@implementation UIBarItem (UIAppearance_Swift)

+ (instancetype)aw_appearanceWhenContainedIn:(Class<UIAppearanceContainer>)containerClass{
    return [self appearanceWhenContainedIn:containerClass, nil];
}

@end

```

然后在桥接文件记得导入

```objc
#import "UIBarItem+UIAppearance_Swift.h"
```

最后就可以在项目中丝滑的使用了

```swift 
 override func loadView() {
        
        super.loadView()
        
        //获取app中的tabBarItem
        let item = UITabBarItem.wn_appearanceWhenContainedIn(RootTabBarController.self)
        
        ///选中
        var attrSel = [String : AnyObject]()
        attrSel[NSFontAttributeName] = UIFont.systemFontOfSize(12)
        attrSel[NSForegroundColorAttributeName] = UIColor(red: 251/255, green: 175/255, blue: 40/255, alpha: 1.0)
        item.setTitleTextAttributes(attrSel, forState: .Selected)
        
        ///默认
        var attrNor = [String : AnyObject]()
        attrNor[NSFontAttributeName] = UIFont.systemFontOfSize(12)
        attrNor[NSForegroundColorAttributeName] = UIColor.lightGrayColor()
        item.setTitleTextAttributes(attrNor, forState: .Normal)
    }

```

