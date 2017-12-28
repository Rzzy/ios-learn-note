####UIView 的使用

```object-c
//    UIWindow *aWindow = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] ;
//    self.window = aWindow ;
//    [aWindow release] ;

//  效果等同上述代码
//    当应用程序加载完成后，创建一个跟屏幕尺寸一样大的window对象
    self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] autorelease];



// Override point for customization after application launch.
//    修改window的背景颜色为指定颜色
    self.window.backgroundColor = [UIColor purpleColor];

//    makeKeyAndVisible方法可以让window对象成为主窗口对象，并且显示在屏幕上。
    [self.window makeKeyAndVisible];

    UIView *aView = [[UIView alloc] initWithFrame:CGRectMake(30, 30, 100, 100)] ;
    [self.window addSubview:aView] ;
    [aView release] ;
    //UIView对象的背景颜色默认是透明的clearColor
    aView.backgroundColor = [UIColor cyanColor] ;



    UIView *yellowView = [[UIView alloc] initWithFrame:CGRectMake(25, 25, 100, 100)] ;
    [aView addSubview:yellowView] ;
    [yellowView release] ;
    yellowView.backgroundColor = [UIColor yellowColor] ;

    UIView *blueView = [[UIView alloc] initWithFrame:CGRectMake(25, 25, 100, 100)] ;
    [yellowView addSubview:blueView] ;
    [blueView release] ;
    blueView.backgroundColor = [UIColor blueColor] ;


    NSLog( @"%@", NSStringFromCGRect(self.window.bounds) ) ;
    // iPhone 6 Plus 的分辨率 1920 * 1080 （414 * 736）
    //frame 中的大小其实是屏幕上物理像素点的个数，视图对象根据给定的物理像素点的个数以及起始位置、颜色信息来确定视图的范围和要显示的颜色。

    UIView *anView = [[UIView alloc] initWithFrame:CGRectMake(50, 50, 300, 300)];

    anView.backgroundColor = [UIColor redColor] ;
    [self.window addSubview:anView] ;
    [anView release] ;
    //视图创建并添加在父视图上时，打印 center 属性的值；
    NSLog( @"%@", NSStringFromCGPoint(anView.center) ) ;
    //通过修改视图的 center 属性来改变视图的起始点
//  anView.center = CGPointMake(200, 200) ;
    anView.center = self.window.center ;
    //frame 属性是指该视图在其父视图产生的坐标系中的起始点和大小；
//    anView.frame = CGRectMake(60, 80, 100, 120) ;
    //影响视图的 center 属性的原因有，视图的起始点发生改变和视图的大小发生改变；
    NSLog( @"%@", NSStringFromCGPoint(anView.center) ) ;


    UIView *bView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)] ;
    bView.backgroundColor = [UIColor orangeColor] ;
    bView.center = anView.center ;
    [anView addSubview:bView] ;
    [bView release] ;
//   
    anView.bounds = CGRectMake(20, -20, 300, 300) ;

    NSLog( @"%@", NSStringFromCGRect(anView.frame) ) ;
    //与 frame 不同的是 bounds 描述的是一个视图在自身坐标系中的起始点和大小，起始点默认与坐标系原点重合，为 （0，0），而 frame 描述的是视图在其父视图中的起始点和大小。
    NSLog( @"%@", NSStringFromCGRect(bView.bounds) ) ;
    //bounds 是可一个被修改的，修改 bounds 的起始点会导致当前视图产生的坐标系原点发生改变。
    //视图显示的位置是依赖于自身的 frame 的，修改 bounds 的起始点不会影响到 frame 的起始点，所以看到的视图并没有发生位移。
    //当我们把一个视图的 bounds 起始点改成（+，+）时，自身坐标系原点将向左上角移动对应大小。
    //当我们把一个视图的 bounds 起始点改成（-，-）时，自身坐标系原点将向右下角移动对应大小。
    //当我们把一个视图的 bounds 起始点改成（-, +）时，自身坐标系原点将向右移动对应大小。
    //当我们把一个视图的 bounds 起始点改成（+, -）时，自身坐标系原点将向左移动对应大小。


    UIView *greenView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 120, 120)] ;
    greenView.backgroundColor = [UIColor greenColor] ;
    //一个视图的子视图是按照添加时间以下标为序的，我们可以通过下面的方法改变视图层级。
//    [anView insertSubview:greenView atIndex:0] ;
    //将一个子视图插入到已经存在的子视图的上方。
//    [anView insertSubview:greenView aboveSubview:bView] ;
    //将一个子视图插入到已经存在的子视图的下方。
    [anView insertSubview:greenView belowSubview:bView] ;
    [greenView release] ;

    //管理视图层级
    //将一个视图移动到视图层级的最上层。
    [anView bringSubviewToFront:greenView] ;
    //将一个视图移动到视图层级的最下层。
    [anView sendSubviewToBack:greenView] ;
    //一个子视图调用 removeFromSuperview 方法，把自己从父视图中移除。
    [greenView removeFromSuperview] ;

    //一旦一个视图被添加显示以后，它的 superview 属性就可以获取到它的父视图。
    NSLog( @"%@", [anView superview] ) ;
    NSLog( @"%@", [bView superview] ) ;
    //视图是通过 subviews 数组来管理其子视图们的。
    NSLog( @"%@", [anView subviews] ) ;

    // hidden 是一个视图的显隐属性，默认为NO ，表示不隐藏，如果一个视图的 hidden 属性设置为 YES，其子视图也将被隐藏。
//    anView.hidden = YES ;
    // alpha 是一个视图的不透明度，范围在 0~1，默认为1，表示完全不透明。
    anView.alpha = 0.8 ;

    anView.tag = 100 ;

    UIView *getView = [self.window viewWithTag:100] ;
    NSLog( @"%@", getView ) ;
```