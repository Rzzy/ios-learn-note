####史上最详细的iOS之事件的传递和响应机制-原理篇

转载：https://www.jianshu.com/p/2e074db792ba

**iOS中的事件可以分为3大类型!**：
> 按照时间顺序，事件的生命周期是这样的：
事件的产生和传递（事件如何从父控件传递到子控件并寻找到最合适的view、寻找最合适的view的底层实现、拦截事件的处理）->找到最合适的view后事件的处理（touches方法的重写，也就是事件的响应）

[](https://upload-images.jianshu.io/upload_images/1932148-1c4e39f1487a6d54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)