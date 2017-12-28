UI基础控件之UIView详解

转载：https://www.jianshu.com/p/1f28240babd0

####`UIView` 简介

**什么是`UIView`**
`UIView`是窗口上的一块区域，是iOS中所有控件的基类，我们在app中所有能看见的都是直接或间接继承与`UIView`的.我们把`UIView`叫做视图.

**`UIView`的作用**
+ 负责内部区域的内容渲染。
+ 负责内部区域的触摸事件。
+ 管理本身的所有子视图。
+ 处理基本的动画。

####`UIView`创建与使用

#####创建`UIView`
```object-c
//通过frame创建View
UIView *view = [[UIView alloc] initWithFrame:CGRectMake(100, 100, 200, 200)];
    
//添加到父视图中
[self.window addSubview:view];
```
视图是一块区域，所以创建UIView的同时需要设置他的位置和大小，frame是一个包含位置和大小的结构体；

#####`UIView`的基本属性
```object-c
    //设置视图的背景颜色
    view.backgroundColor = [UIColor redColor];
    
    //修改视图的大小或者位置
    view.frame = CGRectMake(200, 100, 200, 200);
    
    //设置视图的透明度[0.0, 1.0]
    view.alpha = 0.5;
    
    //设置是否隐藏
    view.hidden = NO;
    
    //设置视图的标签
    view.tag = 100;
    
    //中心点
    view.center = self.window.center;
```
>注意：这里特别说下frame， 他是UIView一个非常重要的属性，决定了UIView的大小和位置；frame中设置的位置是以UIView的父视图坐标系为基准，需要特别注意的是不可以修改frame中的某个成员变量，只能整体修改frame

##### 子视图管理
#####NSArray *subviews



























