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