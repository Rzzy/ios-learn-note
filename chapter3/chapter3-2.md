### UITableView详解

##### 概述

`UITableView`堪称`UIKit`里面最复杂的一个控件了，同时也是最常用的控件，使用起来不算难，但是要用好并不容易。当使用的时候我们必须要考虑到后台数据的设计，`tableViewCell`的设计和重用以及`tableView`的效率等问题。

`UITableView`只能有一列数据\(cell\)，且只支持纵向滑动，当创建好的`tablView`第一次显示的时候，我们需要调用其`reloadData`方法，强制刷新一次，从而使`tableView`的数据更新到最新状态。

UITableView使用时常遇到的问题大致如下：

* 顶层设计
  * TableView的类型选择
  * Section的设置，合适分组，如何分组等问题
  * Cell的使用，是否自定义？是否使用xib？等等
  * Cell填充问题，一般应遵循重用、解耦原则。
* 重用问题
  * UITableViewCell重用
  * UITableViewHeaderFooterView重用
* 协议方法
  * Delegate与DataSource代理方法的含义、实现  
* UI问题
  * 分割线
  * Cell自定义
  * 默认选中某个Cell

#### `NSIndexPath`

提到UITableView，就必须的说一说NSIndexPath。UITableView声明了一个NSIndexPath的类别，主要用来标识当前cell的在tableView中的位置，该类别有section和row两个属性，前者标识当前cell处于第几个section中，后者代表在该section中的第几行。

```object-c
// 源码来源于UIKit->UITableView.h

// This category provides convenience methods to make it easier to use an NSIndexPath to represent a section and row
@interface NSIndexPath (UITableView)

+(instancetype)indexPathForRow:(NSInteger)row inSection:(NSInteger)section;
@property (nonatomic, readonly) NSInteger section;
@property (nonatomic, readonly) NSInteger row;

@end
```

#### `UITableViewRowAction`

```object-c
// 源码来源于UIKit->UITableView.h

NS_CLASS_AVAILABLE_IOS(8_0) __TVOS_PROHIBITED 

@interface UITableViewRowAction : NSObject <NSCopying>

+ (instancetype)rowActionWithStyle:(UITableViewRowActionStyle)style title:(nullable NSString *)title handler:(void (^)(UITableViewRowAction *action, NSIndexPath *indexPath))handler;

@property (nonatomic, readonly) UITableViewRowActionStyle style;
@property (nonatomic, copy, nullable) NSString *title;
@property (nonatomic, copy, nullable) UIColor *backgroundColor; // default background color is dependent on style
@property (nonatomic, copy, nullable) UIVisualEffect* backgroundEffect;

@end

#ifndef SDK_HIDE_TIDE
NS_CLASS_AVAILABLE_IOS(9_0) 

@interface UITableViewFocusUpdateContext : UIFocusUpdateContext

@property (nonatomic, strong, readonly, nullable) NSIndexPath *previouslyFocusedIndexPath;
@property (nonatomic, strong, readonly, nullable) NSIndexPath *nextFocusedIndexPath;

@end
#endif
```

#### 一些枚举值

`UITableViewCellAccessoryType`

```object-c
typedef NS_ENUM(NSInteger, UITableViewCellAccessoryType) {
    UITableViewCellAccessoryNone,                   // 不显示任何图标
    UITableViewCellAccessoryDisclosureIndicator,    // 跳转指示图标
    UITableViewCellAccessoryDetailDisclosureButton, // 内容详情图标和跳转指示图标
    UITableViewCellAccessoryCheckmark,              // 勾选图标
    UITableViewCellAccessoryDetailButton NS_ENUM_AVAILABLE_IOS(7_0) // 内容详情图标
};
```

`UITableViewCellAccessoryType`根据其`Index`对应的效果图见下图。  
![](/assets/QQ20151103-2@2x.png)

用户还可以通过UITableViewCell的accessoryView（UIView）自定义一个UIView视图。  
`UITableViewCellStyle`

```object-c
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
    UITableViewCellStyleDefault,    // 左侧显示textLabel（不显示detailTextLabel），imageView可选（显示在最左边）
    UITableViewCellStyleValue1,     // 左侧显示textLabel、右侧显示detailTextLabel（默认蓝色），imageView可选（显示在最左边）
    UITableViewCellStyleValue2,     // 左侧依次显示textLabel(默认蓝色)和detailTextLabel，imageView可选（显示在最左边）
    UITableViewCellStyleSubtitle    // 左上方显示textLabel，左下方显示detailTextLabel（默认灰色）,imageView可选（显示在最左边）
};
```
`UITableViewCellStyle`索引对应下图由上往下的次序的效果。
![](/assets/QQ20151103-3@2x.png)

**`UITableViewCellEditingStyle`**
