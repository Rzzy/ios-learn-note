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
```object-c
typedef NS_ENUM(NSInteger, UITableViewCellEditingStyle) {
  UITableViewCellEditingStyleNone, // 无
  UITableViewCellEditingStyleDelete, // “-”号效果
  UITableViewCellEditingStyleInsert // “+”号效果
};
```
#### 其它枚举

```object-c
// 源码来源于UIKit->UITableView.h

typedef NS_ENUM(NSInteger, UITableViewStyle) {
    UITableViewStylePlain,          // regular table view
    UITableViewStyleGrouped         // preferences style table view
};

typedef NS_ENUM(NSInteger, UITableViewScrollPosition) {
    UITableViewScrollPositionNone,
    UITableViewScrollPositionTop,    
    UITableViewScrollPositionMiddle,   
    UITableViewScrollPositionBottom
};                // scroll so row of interest is completely visible at top/center/bottom of view

typedef NS_ENUM(NSInteger, UITableViewRowAnimation) {
    UITableViewRowAnimationFade,
    UITableViewRowAnimationRight,           // slide in from right (or out to right)
    UITableViewRowAnimationLeft,
    UITableViewRowAnimationTop,
    UITableViewRowAnimationBottom,
    UITableViewRowAnimationNone,            // available in iOS 3.0
    UITableViewRowAnimationMiddle,          // available in iOS 3.2.  attempts to keep cell centered in the space it will/did occupy
    UITableViewRowAnimationAutomatic = 100  // available in iOS 5.0.  chooses an appropriate animation style for you
};

// Including this constant string in the array of strings returned by sectionIndexTitlesForTableView: will cause a magnifying glass icon to be displayed at that location in the index.
// This should generally only be used as the first title in the index.
UIKIT_EXTERN NSString *const UITableViewIndexSearch NS_AVAILABLE_IOS(3_0) __TVOS_PROHIBITED;

// Returning this value from tableView:heightForHeaderInSection: or tableView:heightForFooterInSection: results in a height that fits the value returned from
// tableView:titleForHeaderInSection: or tableView:titleForFooterInSection: if the title is not nil.
UIKIT_EXTERN const CGFloat UITableViewAutomaticDimension NS_AVAILABLE_IOS(5_0);

typedef NS_ENUM(NSInteger, UITableViewRowActionStyle) {
    UITableViewRowActionStyleDefault = 0,
    UITableViewRowActionStyleDestructive = UITableViewRowActionStyleDefault,
    UITableViewRowActionStyleNormal
} NS_ENUM_AVAILABLE_IOS(8_0) __TVOS_PROHIBITED;
```

#### DateSource——数据源
主要为UITableView提供数据供UITableViewCell显示时调用。当指定UITableViewCell支持编辑操作(insert、delete、move、reordering等)时，需在操作时同时操作数据源（DataSource），否则将可能导致crash。

```object-c
Configuring a Table View
- tableView:cellForRowAtIndexPath:
 Required
- numberOfSectionsInTableView:
- tableView:numberOfRowsInSection:
 Required
- sectionIndexTitlesForTableView:
- tableView:sectionForSectionIndexTitle:atIndex:
- tableView:titleForHeaderInSection:
- tableView:titleForFooterInSection:
Inserting or Deleting Table Rows
- tableView:commitEditingStyle:forRowAtIndexPath:
- tableView:canEditRowAtIndexPath:
Reordering Table Rows
- tableView:canMoveRowAtIndexPath:
- tableView:moveRowAtIndexPath:toIndexPath:

```

#### Delegate——代理
主要提供一些可选的方法，用来控制UITableViewCell的选择、指定section的头和尾的显示以及协助完成cell的删除和排序等功能。

```object-c
Configuring Rows for the Table View
- tableView:heightForRowAtIndexPath:
- tableView:estimatedHeightForRowAtIndexPath:
- tableView:indentationLevelForRowAtIndexPath:
- tableView:willDisplayCell:forRowAtIndexPath:
Managing Accessory Views
- tableView:editActionsForRowAtIndexPath:
- tableView:accessoryTypeForRowWithIndexPath:
 (iOS 3.0)
- tableView:accessoryButtonTappedForRowWithIndexPath:
Managing Selections
- tableView:willSelectRowAtIndexPath:
- tableView:didSelectRowAtIndexPath:
- tableView:willDeselectRowAtIndexPath:
- tableView:didDeselectRowAtIndexPath:
Modifying the Header and Footer of Sections
- tableView:viewForHeaderInSection:
- tableView:viewForFooterInSection:
- tableView:heightForHeaderInSection:
- tableView:estimatedHeightForHeaderInSection:
- tableView:heightForFooterInSection:
- tableView:estimatedHeightForFooterInSection:
- tableView:willDisplayHeaderView:forSection:
- tableView:willDisplayFooterView:forSection:
Editing Table Rows
- tableView:willBeginEditingRowAtIndexPath:
- tableView:didEndEditingRowAtIndexPath:
- tableView:editingStyleForRowAtIndexPath:
- tableView:titleForDeleteConfirmationButtonForRowAtIndexPath:
- tableView:shouldIndentWhileEditingRowAtIndexPath:
Reordering Table Rows
- tableView:targetIndexPathForMoveFromRowAtIndexPath:toProposedIndexPath:
Tracking the Removal of Views
- tableView:didEndDisplayingCell:forRowAtIndexPath:
- tableView:didEndDisplayingHeaderView:forSection:
- tableView:didEndDisplayingFooterView:forSection:
Copying and Pasting Row Content
- tableView:shouldShowMenuForRowAtIndexPath:
- tableView:canPerformAction:forRowAtIndexPath:withSender:
- tableView:performAction:forRowAtIndexPath:withSender:
Managing Table View Highlighting
- tableView:shouldHighlightRowAtIndexPath:
- tableView:didHighlightRowAtIndexPath:
- tableView:didUnhighlightRowAtIndexPath:
Managing Table View Focus
- tableView:canFocusRowAtIndexPath:
- tableView:shouldUpdateFocusInContext:
- tableView:didUpdateFocusInContext:withAnimationCoordinator:
- indexPathForPreferredFocusedViewInTableView:

UIKIT_EXTERN NSString *const UITableViewSelectionDidChangeNotification;
```
#### `UITableViewCell`
`UITableViewCell`的所有`subView`都应该加载`contentView`上面，以免在编辑时出现UI错误。

#### Edit
通过从UITableViewCell中派生一个类，可以更深度的定制一个cell，可以指定cell在进入edit模式的时候如何相应等等。最简单的实现方式就是将所有要绘制的内容放到一个定制的subView中，并且重载该subView的drawRect方法直接把要显示的内容绘制出来(这样可以避免subView过多导致的性能瓶颈)，最后再将该subView添加到cell派生类中的contentView中即可。但是这样定制的cell需要注意在数据改变的时候，通过手动调用该subView的setNeedDisplay方法来刷新界面，这个例子可以在苹果的帮助文档中的TableViewSuite工程中找到，这儿就不举例了。


http://www.devzhang.com/14464613593730.html
https://www.jianshu.com/p/aa9721e4484d








































