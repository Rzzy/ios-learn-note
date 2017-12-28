### UI基础控件之UIView详解

#### `UIView` 简介

**什么是**`UIView`  
`UIView`是窗口上的一块区域，是iOS中所有控件的基类，我们在app中所有能看见的都是直接或间接继承与`UIView`的.我们把`UIView`叫做视图.

`UIView`**的作用**

* 负责内部区域的内容渲染。
* 负责内部区域的触摸事件。
* 管理本身的所有子视图。
* 处理基本的动画。

#### `UIView`创建与使用

##### 创建`UIView`

```object-c
//通过frame创建View
UIView *view = [[UIView alloc] initWithFrame:CGRectMake(100, 100, 200, 200)];

//添加到父视图中
[self.window addSubview:view];
```

视图是一块区域，所以创建UIView的同时需要设置他的位置和大小，frame是一个包含位置和大小的结构体；

##### `UIView`的基本属性

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

> 注意：这里特别说下frame， 他是UIView一个非常重要的属性，决定了UIView的大小和位置；frame中设置的位置是以UIView的父视图坐标系为基准，需要特别注意的是不可以修改frame中的某个成员变量，只能整体修改frame

#### 子视图管理

##### NSArray \*subviews

* 管理所有的子视图\(控件\)
* 数组元素的顺序决定着子控件的显示层级顺序（下标越大的，越显示在上面）
* 所有子视图的管理都是对subviews数组的管理

##### 添加视图

```object-c
UIView *view2 = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];

view2.backgroundColor = [UIColor greenColor];
view2.tag = 200;

//将view2添加到view上
[view addSubview:view2];
```

通过上述代码添加后，view2是view的子视图，view是view2的父视图;  
所有视图之间有层次级别关系，越是在后添加的视图，越显示在上面，前面添加的在下面

##### 插入视图

```object-c
//将一个yView插入到view的子视图中，序号为0的位置
//序号越小越靠近下面，序号越大越靠近上面
//序号的范围[0,子视图的个数)
[view insertSubview:yView atIndex:0];

//将oView插入到view的子视图中view2的下面
[view insertSubview:oView belowSubview:view2];

//将人View插入到view的子视图中， yView的上面
[view insertSubview:rView aboveSubview:yView]
```

##### 删除视图

`UIView`中子视图管理和数组对元素的管理不一样，他不能通过父视图去删除子视图，只能子视图自己将自己从父视图中删除.

```object-c
//view2将自己从父视图中移除
[view2 removeFromSuperview];
```

##### 获取视图

视图没有名字，但他有标识，所以要想获取视图中的指定子视图，我们可以通过子视图的标识来获取,方法如下：

```object-c
//获取view下子视图标识为200的视图
UIView *subView = [view viewWithTag:200];
```

##### 更改视图的显示层级

```object-c
//通过父视图，将某个子视图在最上面显示
[view bringSubviewToFront:yView];

//通过父视图， 将某个子视图在最下显示
[view sendSubviewToBack:view2];
```



