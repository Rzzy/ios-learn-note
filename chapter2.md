#### iOS中事件的产生和传递

转载：[https://www.jianshu.com/p/585760924a92](https://www.jianshu.com/p/585760924a92)

**iOS中的事件可以分为3大类型**：

> 按照时间顺序，事件的生命周期是这样的：  
> 事件的产生和传递（事件如何从父控件传递到子控件并寻找到最合适的view、寻找最合适的view的底层实现、拦截事件的处理）-&gt;找到最合适的view后事件的处理（touches方法的重写，也就是事件的响应）

![事件分类](/assets/1932148-1c4e39f1487a6d54.png)

_响应者对象_

* 在iOS中不是任何对象都能处理事件，只有继承了UIResponder的对象才能接收并处理事件。我们称之为“响应者对象”
* UIApplication、UIViewController、UIView都继承自UIResponder，因此它们都是响应者对象，都能够接收并处理事件

### `UIResponder`

UIResponder内部提供了以下方法来处理事件

`触摸事件`:

```object-c
-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;

-(void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;

-(void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;

-(void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;
```

`加速计事件`

```oobject-c
-(void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event;

-(void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event;

-(void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event;
```

`远程控制事件`

```oobject-c
-(void)remoteControlReceivedWithEvent:(UIEvent *)event;
```

### `UIView`的触摸事件处理

UIView是UIResponder的子类，可以实现下列4个方法处理不同的触摸事件

```object-c
// 一根或者多根手指开始触摸view，系统会自动调用view的下面方法
-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event

// 一根或者多根手指在view上移动，系统会自动调用view的下面方法（随着手指的移动，会持续调用该方法）
-(void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event

// 一根或者多根手指离开view，系统会自动调用view的下面方法
-(void)touchesEnded:(NSSet *)touches withEvent:(UIEvent  *)event


// 触摸结束前，某个系统事件(例如电话呼入)会打断触摸过程，系统会自动调用view的下面方法
-(void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
```

> 提示：touches中存放的都是UITouch对象

### `UITouch`

* 当用户用一根手指触摸屏幕时，会创建一个与手指相关联的UITouch对象
* 一根手指对应一个UITouch对象

_**UITouch的作用**_

1. 保存着跟手指相关的信息，比如触摸的位置、时间、阶段

2. 当手指移动时，系统会更新同一个UITouch对象，使之能够一直保存该手指的触摸位置。

3. 当手指离开屏幕时，系统会销毁相应的UITouch对象

> 提示：iPhone开发中，要避免使用双击事件！'

_**UITouch的属性**_

```object-c
// 触摸产生时所处的窗口
@property(nonatomic,readonly,retain)UIWindow *window;

// 触摸产生时所处的视图
@property(nonatomic,readonly,retain)UIView *view;

// 短时间内点按屏幕的次数，可以根据tapCount判断单击、双击或更多的点击
@property(nonatomic,readonly)NSUInteger tapCount;

// 记录了触摸事件产生或变化时的时间，单位是秒
@property(nonatomic,readonly)NSTimeInterval timestamp;

// 当前触摸事件所处的状态
@property(nonatomic,readonly)UITouchPhase phase;
```

_**UITouch的方法**_

```object-c
-(CGPoint)locationInView:(UIView *)view;
// 返回值表示触摸在view上的位置
// 这里返回的位置是针对view的坐标系的（以view的左上角为原点(0, 0)）
// 调用时传入的view参数为nil的话，返回的是触摸点在UIWindow的位置

-(CGPoint)previousLocationInView:(UIView *)view;
// 该方法记录了前一个触摸点的位置
```

### `UIEvent`

* 每产生一个事件，就会产生一个UIEvent对象
* UIEvent：称为事件对象，记录事件产生的时刻和类型

**常见属性**

```object-c
// 事件类型
@property(nonatomic,readonly)UIEventType type;
@property(nonatomic,readonly)UIEventSubtype subtype;

// 事件产生的时间
@property(nonatomic,readonly)NSTimeInterval timestamp;
```

* UIEvent还提供了相应的方法可以获得在某个view上面的触摸对像（UITouch）

_**touches和event参数**_

* 一次完整的触摸过程，会经历3个状态：

```object-c
// 触摸开始：
-(void)touchesBegan:(NSSet *)touches withEvent:(UIEven *)event

// 触摸移动：
-(void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event

// 触摸结束：
-(void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event

// 触摸取消（可能会经历）：
-(void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
```

_**4个触摸事件处理方法中，都有NSSet **_**touches和UIEvent **_**event两个参数**_

* 一次完整的触摸过程中，只会产生一个事件对象，4个触摸方法都是同一个event参数

* 如果两根手指同时触摸一个view，那么view只会调用一次touchesBegan:withEvent:方法，touches参数中装着2个UITouch对象

* 如果这两根手指一前一后分开触摸同一个view，那么view会分别调用2次touchesBegan:withEvent:方法，并且每次调用时的touches参数中只包含一个UITouch对象

* 根据touches中UITouch的个数可以判断出是单点触摸还是多点触摸

##### Q：

默认触摸方法NSSet里面只能获得一个UITouch对象,为什么?

##### A：

UIView默认不支持多点触控。也就是说不支持多只手指同时触摸。

##### Q：

如何让视图接收多点触摸?

##### A：

需要设置它的multipleTouchEnabled属性为YES，默认状态下这个属性值为NO，即视图默认不接收多点触摸。。

##### Q：

如何判断用户当前是双击还是单击?

##### A：

根据UITouch的tapCount属性的值。tapCount表示短时间内轻击屏幕的次数。因此可以根据tapCount判断单击、双击或更多的轻击。

> 根据tapCount点击的次数来设置当前视图的背景色\(双击改变背景颜色\)  
> 轻击操作很容易引起歧义，比如当用户点了一次之后，并不知道用户是想单击还是只是双击的一部分，或者点了两次之后并不知道用户是想双击还是继续点击。为了解决这个问题，一般可以使用“延迟调用”函数,或手势识别器

1. 使用“延迟调用”函数

```object-c
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event {
    UITouch *touch = [touches anyObject];
    if(touch.tapCount != 2){ // 如果不是双击
        [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(setBackgroundColor:)  object:[UIColor orangeColor]];
    } else { // 延时1执行改变背景的方法
        [self performSelector:@selector(setBackgroundColor:) withObject:[UIColor orangeColor] afterDelay:1.0];
    }
}
```

1. 使用Gesture Recognizer
   使用Gesture Recognizer识别就会简单许多，只需添加两个手势识别器，分别检测单击和双击事件，设置必要的属性即可

```object-c
- (id)init {  
    if ((self = [super init])) {  
    self.userInteractionEnabled = YES;  
        UITapGestureRecognizer *singleTapGesture = [[UITapGestureRecognizer alloc]initWithTarget:self action:@selector(handleSingleTap:)];  
        singleTapGesture.numberOfTapsRequired = 1;  
        singleTapGesture.numberOfTouchesRequired  = 1;  
        [self addGestureRecognizer:singleTapGesture];  

        UITapGestureRecognizer *doubleTapGesture = [[UITapGestureRecognizer alloc]initWithTarget:self action:@selector(handleDoubleTap:)];  
        doubleTapGesture.numberOfTapsRequired = 2;  
        doubleTapGesture.numberOfTouchesRequired = 1;  
        [self addGestureRecognizer:doubleTapGesture];  

        [singleTapGesture requireGestureRecognizerToFail:doubleTapGesture];  
    }  
    return self;  
}  
-(void)handleSingleTap:(UIGestureRecognizer *)sender{  
    CGPoint touchPoint = [sender locationInView:self];  
    //...  
}  
-(void)handleDoubleTap:(UIGestureRecognizer *)sender{  
    CGPoint touchPoint = [sender locationInView:self];  
    //...  
}
```

唯一需要注意的是:  
`[singleTapGesture requireGestureRecognizerToFail:doubleTapGesture]`;  
这句话的意思时，只有当doubleTapGesture识别失败的时候\(即识别出这不是双击操作\)，singleTapGesture才能开始识别，同我们一开始讲的是同一个问题。

> 提示：iPhone开发中，要避免使用双击事件!

NSObject类的cancelPreviousPerformRequestWithTarget:selector:object方法取消指定对象的方法调用。

> Cancels perform requests previously registered with performSelector:withObject:afterDelay:.  
> All perform requests are canceled that have the same target as aTarget, argument as anArgument, and selector as aSelector.  
> \(如果是带参数，那取消时的参数也要一致，否则不能取消成功\)

_**细节**_  
检测tapCount可以放在touchesBegan也可以touchesEnded，不过一般后者更准确，因为touchesEnded可以保证所有的手指都已经离开屏幕，这样就不会把轻击动作和按下拖动等动作混淆。

_\*不管是一个手指还是多个手指，轻击操作都会使每个触摸对象的tapCount加1，因此可以直接调用touches的anyObject方法来获取任意一个触摸对象然后判断其tapCount的值即可。_

#### 事件的产生和传递

* **发生触摸事件后，系统会将该事件加入到一个由UIApplication管理的事件队列中, 为什么是队列而不是栈？因为队列的特定是先进先出，先产生的事件先处理才符合常理，所以把事件添加到队列。**

* **UIApplication会从事件队列中取出最前面的事件，并将事件分发下去以便处理，通常，先发送事件给应用程序的主窗口（keyWindow）**

##### _应用如何找到最合适的控件来处理事件_？

> 1. 首先判断主窗口（keyWindow）自己是否能接受触摸事件,不能,则传给UIApplication处理.,能,转2
> 2. 判断触摸点是否在自己身上
> 3. 子控件数组中从后往前遍历子控件，重复前面的两个步骤（所谓从后往前遍历子控件，就是首先查找子控件数组中最后一个元素，然后执行1、2步骤）
> 4. 如果没有符合条件的子控件，那么就认为自己最合适处理这个事件，也就是自己是最合适的view。

* **主窗口会在视图层次结构中找到一个最合适的视图来处理触摸事件，但是这仅仅是整个事件处理过程的第一步**
* **找到合适的视图控件后，就会调用视图控件的touches方法来作具体的事件处理**

> touchesBegan…  
> touchesMoved…  
> touchedEnded…  
> 注意: 如果父控件不能接受触摸事件，那么子控件就不可能接收到触摸事件

#### `UIView`不接收触摸事件的三种情况

1.不接收用户交互

```object-c
userInteractionEnabled = NO
```

2.隐藏

```object-c
hidden = YES
```

3.透明

```object-c
alpha = 0.0 ~ 0.01
```

> 提示：UIImageView的userInteractionEnabled默认就是NO，因此UIImageView以及它的子控件默认是不能接收触摸事件的

##### **事件传递示例**

![事件传递示例](/assets/1932148-7e8ce35dc4d4b2ba.png)  
_**触摸事件的传递是从父控件传递到子控件**_

--点击了绿色的view：  
UIApplication -&gt;UIWindow-&gt;白色 -&gt;绿色

--点击了蓝色的view：  
UIApplication-&gt;UIWindow-&gt;白色 -&gt;橙色 -&gt;蓝色

--点击了黄色的view：  
UIApplication-&gt;UIWindow-&gt;白色 -&gt;橙色 -&gt;蓝色 -&gt;黄色

_**触摸事件处理的详细过程**_

* 用户点击屏幕后产生的一个触摸事件，经过一系列的传递过程后，会找到最合适的视图控件来处理这个事件
* 找到最合适的视图控件后，就会调用控件的touches方法来作具体的事件处理

```object-c
touchesBegan…
touchesMoved…
touchedEnded…
```

* 这些touches方法的默认做法是将事件顺着响应者链条向上传递，将事件交给上一个响应者进行处理
  ##### **响应者链条**
* 响应者链条：是由多个响应者对象连接起来的链条
* 作用：能很清楚的看见每个响应者之间的联系，并且可以让一个事件多个对象处理。
* 响应者对象：能处理事件的对象

##### _**事件传递的完整过程**_

1. 先将事件对象由上往下传递\(由父控件传递给子控件\)，找到最合适的控件来处理这个事件。

2. 调用最合适控件的touches….方法

3. 如果调用了\[super touches….\];就会将事件顺着响应者链条往上传递，传递给上一个响应者

4. 接着就会调用上一个响应者的touches….方法

##### _**如何判断上一个响应者**_

1. 如果当前这个view是控制器的view,那么控制器就是上一个响应者
2. 如果当前这个view不是控制器的view
   * 当前这个view的父类不是自定义的view,那么父控件就是上一个响应者
   * 当前这个view的父类是自定义的view,那么父类就是上一个响应者

##### _**响应者链的事件传递过程**_

1. 如果view的控制器存在，就传递给控制器；如果控制器不存在，则将其传递给它的父视图
2. 在视图层次结构的最顶级视图，如果也不能处理收到的事件或消息，则其将事件或消息传递给window对象进行处理
3. 如果window对象也不处理，则其将事件或消息传递给UIApplication对象
4. 如果UIApplication也不能处理该事件或消息，则将其丢弃

#### `hitTest:withEvent:`方法和`pointInside:withEvent:`

```object-c
    1. hitTest调用时机:当一个事件传递给一个控件的时候，系统就会调用这个方法
    2. hitTest作用: 寻找到最合适处理事件的view。
    * （回顾下事件传递），UIApplication -> UIWindow
    *  UIWindow去寻找最合适的view? [UIWindow hitTest:withEvent:]里面做了什么事情？
    1> 判断窗口能不能处理事件? 如果不能，意味着窗口不是最合适的view，而且也不会去寻找比自己更合适的view,直接返回nil,通知UIApplication，没有最合适的view。
    2> 判断点在不在窗口
    3> 遍历自己的子控件，寻找有没有比自己更合适的view
    4> 如果子控件不接收事件，意味着子控件没有找到最合适的view,然后返回nil,告诉窗口没有找到更合适的view,窗口就知道没有比自己更合适的view,就自己处理事件。

     * 验证下hitTest方法返回nil，里面的子控件能处理事件吗？ 重写view的hitTest:withEvent:方法，
     * 验证这个方法是否真能找到最合适的view？
     * 如果点击屏幕任何一个地方，都由控制器的view来处理事件，怎么做? 直接返回白色的view,就不会继续去找白色view的子控件了。
```

```object-c
2> hitTest:withEvent:方法的处理流程如下:
    1、调用当前视图的pointInside:withEvent:方法判断触摸点是否在当前视图内
        若返回NO,则hitTest:withEvent:返回nil;
        若返回YES,则向当前视图的所有子视图(subviews)发送hitTest:withEvent:消息，所有
        子视图的遍历顺序是从top到bottom，即从subviews数组的末尾向前遍历,直到有子视图返
        回非空对象或者全部子视图遍历完毕。
    2、若第一次有子视图返回非空对象,则hitTest:withEvent:方法返回此对象，处理结束。
    3、如所有子视图都返回nil,则hitTest:withEvent:方法返回自身(self)。
```



