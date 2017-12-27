####iOS中事件的产生和传递
转载：https://www.jianshu.com/p/585760924a92

**iOS中的事件可以分为3大类型**：
> 按照时间顺序，事件的生命周期是这样的：
事件的产生和传递（事件如何从父控件传递到子控件并寻找到最合适的view、寻找最合适的view的底层实现、拦截事件的处理）->找到最合适的view后事件的处理（touches方法的重写，也就是事件的响应）

![事件分类](/assets/1932148-1c4e39f1487a6d54.png)

*响应者对象*
+ 在iOS中不是任何对象都能处理事件，只有继承了UIResponder的对象才能接收并处理事件。我们称之为“响应者对象”
+ UIApplication、UIViewController、UIView都继承自UIResponder，因此它们都是响应者对象，都能够接收并处理事件

###`UIResponder`
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
###`UIView`的触摸事件处理
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
>提示：touches中存放的都是UITouch对象

###`UITouch`
+ 当用户用一根手指触摸屏幕时，会创建一个与手指相关联的UITouch对象
+ 一根手指对应一个UITouch对象

***UITouch的作用***

1. 保存着跟手指相关的信息，比如触摸的位置、时间、阶段

2. 当手指移动时，系统会更新同一个UITouch对象，使之能够一直保存该手指的触摸位置。

3. 当手指离开屏幕时，系统会销毁相应的UITouch对象

> 提示：iPhone开发中，要避免使用双击事件！'


***UITouch的属性***
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
***UITouch的方法***
```object-c
-(CGPoint)locationInView:(UIView *)view;
// 返回值表示触摸在view上的位置
// 这里返回的位置是针对view的坐标系的（以view的左上角为原点(0, 0)）
// 调用时传入的view参数为nil的话，返回的是触摸点在UIWindow的位置

-(CGPoint)previousLocationInView:(UIView *)view;
// 该方法记录了前一个触摸点的位置

```
















