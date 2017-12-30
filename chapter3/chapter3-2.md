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

#### `Edit`
通过从UITableViewCell中派生一个类，可以更深度的定制一个cell，可以指定cell在进入edit模式的时候如何相应等等。最简单的实现方式就是将所有要绘制的内容放到一个定制的subView中，并且重载该subView的drawRect方法直接把要显示的内容绘制出来(这样可以避免subView过多导致的性能瓶颈)，最后再将该subView添加到cell派生类中的contentView中即可。但是这样定制的cell需要注意在数据改变的时候，通过手动调用该subView的setNeedDisplay方法来刷新界面，这个例子可以在苹果的帮助文档中的TableViewSuite工程中找到，这儿就不举例了。

观看这两种定制cell的方法，我们会发现subView都是添加在cell的contentView上面的，而不是直接加到cell上面，这样写也是有原因的。下面我们看一下cell在正常状态下和编辑状态下的构成图：

>用户可自定义Cell的editingAccessoryView属性，以实现编辑状态下的自定义效果。

![](/assets/2012062819321126.png)

进入编辑状态下cell的构成图如下：
![](/assets/2012062819324942.png)

通过观察上面两幅图片我们可以看出来，当cell在进入编辑状态的时候，contentView会自动的缩放来给Editing control腾出位置。这也就是说如果我们把subView添加到contentView上，如果设置autoresizingMask为更具父view自动缩放的话，cell默认的机制会帮我们处理进入编辑状态的情况。而且在tableView是Grouped样式的时候，会为cell设置一个背景色，如果我们直接添加在cell上面的话，就需要自己考虑到这个背景色的显示问题，如果添加到contentView上，则可以通过view的叠加帮助我们完成该任务。综上，subView最好还是添加到cell的contentView中。

#### `ReOrdering`
为了使`UITableVeiew`进入`edit`模式以后，如果该`cell`支持`reordering`的话，`reordering`控件就会临时的把`accessaryView`覆盖掉。为了显示`reordering`控件，我们必须将`cell`的`showsReorderControl`属性设置成`YES`，同时实现`dataSource`中的`tableView:moveRowAtIndexPath:toIndexPath:`方法。我们还可以同时通过实现`dataSource`中的 `tableView:canMoveRowAtIndexPath:`返回`NO`，来禁用某一些`cell`的`reordering`功能。

下面看苹果官方的一个reordering流程图：
![](/assets/2012062819375133.png)

上图中当`tableView`进入到`edit`模式的时候，`tableView`会去对当前可见的`cell`逐个调用`dataSource`的`tableView:canMoveRowAtIndexPath:`方法(此处官方给出的流程图有点儿问题)，决定当前`cell`是否显示`reoedering`控件，当开始进入拖动`cell`进行拖动的时候，每滑动过一个`cell`的时候，会去掉用`delegate`的`tableView:targetIndexPathForMoveFromRowAtIndexPath:toProposedIndexPath:`方法，去判断当前划过的`cell`位置是否可以被替换，如果不行则给出建议的位置。当用户放手时本次`reordering`操作结束，调用`dataSource`中的`tableView:moveRowAtIndexPath:toIndexPath:`方法更新`tableView`对应的数据。

此处给个我写demo中的更新数据的小例子：

`EditTableViewController.h` 文件

```object-c
#import <UIKit/UIKit.h>

@interface EditTableViewController : UITableViewController

@end
```

`EditTableViewController.m` 文件

```object-c
#import "EditTableViewController.h"

@interface EditTableViewController ()
@property (nonatomic, strong) NSMutableArray *dataSource;
@end

@implementation EditTableViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // Uncomment the following line to preserve selection between presentations.
    // self.clearsSelectionOnViewWillAppear = NO;
    
    // Uncomment the following line to display an Edit button in the navigation bar for this view controller.
     self.navigationItem.rightBarButtonItem = self.editButtonItem;
    self.title = @"编辑TableView";
    [self prepareItems];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

#pragma mark - Private Functions
- (void)prepareItems{
    self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc] initWithTitle:@"关闭" style:UIBarButtonItemStylePlain target:self action:@selector(dismissSelf)];
}

- (void)dismissSelf{
    [self.navigationController dismissViewControllerAnimated:YES completion:nil];
}

- (id)deleteObjectWithNSIndexPath:(NSIndexPath *)indexPath{
    NSMutableArray *array = self.dataSource[indexPath.section];
    id obj = [array objectAtIndex:indexPath.row];
    [array removeObjectAtIndex:indexPath.row];
    return obj;
}

- (void)insertObject:(NSString *)object inDataSourceAtNSIndexPath:(NSIndexPath *)indexPath{
    NSMutableArray *array = self.dataSource[indexPath.section];
    [array insertObject:object atIndex:indexPath.row];
}

#pragma mark - Table view data source

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return self.dataSource.count;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return ((NSMutableArray*)self.dataSource[section]).count;
}


- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"zxw"];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:@"zxw"];
    }
    if (indexPath.section < self.dataSource.count) {
        NSArray *array = self.dataSource[indexPath.section];
        if (indexPath.row < array.count) {
            cell.textLabel.text = self.dataSource[indexPath.section][indexPath.row];
        }else{
            NSLog(@"row越界%ld",(long)indexPath.row);
        }
    }else{
        NSLog(@"section越界:%ld",(long)indexPath.section);
    }
    
    return cell;
}

- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section{
    return 40.0;
}

- (CGFloat)tableView:(UITableView *)tableView heightForFooterInSection:(NSInteger)section{
    return 35.0;
}

- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section{
    return @"开始";
}

- (NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section{
    return @"结束";
}

// 某个Cell是否可以编辑
- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath {
    return YES;
}

// 返回Cell的编辑状态
- (UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath{
    return indexPath.row % 3;
}

// Override to support editing the table view.
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath {
    if (editingStyle == UITableViewCellEditingStyleDelete) {
        // 1. 删除数据
        // 2. 删除Cell
        [self deleteObjectWithNSIndexPath:indexPath];
        [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
    } else if (editingStyle == UITableViewCellEditingStyleInsert) {
        // 1. 插入数据
        // 2. 插入Cell
        [self insertObject:@"插入的Cell" inDataSourceAtNSIndexPath:indexPath];
        [tableView insertRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationLeft];
    }
}

// Override to support rearranging the table view.
// 移动Cell时调用该方法
- (void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)fromIndexPath toIndexPath:(NSIndexPath *)toIndexPath {
    /**
     if(section不变){
        if(row发生变化){
            直接交换元素
        }
     }else{
        // 1 取出fromIndexPath位置的数据
        // 2 删除fromeIndexPath的数据
        // 3 将存起来的数据插入到toIndexPath的位置
     }
     */
    if (fromIndexPath.section == toIndexPath.section) {
        if (fromIndexPath.row != toIndexPath.row) {
            NSMutableArray *array = self.dataSource[fromIndexPath.section];
            [array exchangeObjectAtIndex:fromIndexPath.row withObjectAtIndex:toIndexPath.row];
        }
    }else{
        NSString *str = [self deleteObjectWithNSIndexPath:fromIndexPath];
        [self insertObject:str inDataSourceAtNSIndexPath:toIndexPath];
    }
}



// Override to support conditional rearranging of the table view.
// 某个Cell是否可以移动
- (BOOL)tableView:(UITableView *)tableView canMoveRowAtIndexPath:(NSIndexPath *)indexPath {
    // Return NO if you do not want the item to be re-orderable.
    return indexPath.row % 2;
}


/*
#pragma mark - Navigation

// In a storyboard-based application, you will often want to do a little preparation before navigation
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    // Get the new view controller using [segue destinationViewController].
    // Pass the selected object to the new view controller.
}
*/

#pragma mark - Getter
- (NSMutableArray *)dataSource{
    if (!_dataSource) {
        _dataSource = @[].mutableCopy;
        for (NSInteger i = 0; i < 5; i++) {
            NSMutableArray *array = @[].mutableCopy;
            for (NSInteger j = 0; j < 5; j++) {
                [array addObject:[NSString stringWithFormat:@"第%ld组，Row:%ld",i ,(long)j]];
            }
            [_dataSource addObject:array];
        }
    }
    return _dataSource;
}
```

上面代码中首先拿到源`cell`所处的`section`，然后从该`section`对应的数据中移除，然后拿到目标`section`的数据，然后将源`cell`的数据添加到目标`section`中，并更新回数据模型，如果我们没有正确更新数据模型的话，显示的内容将会出现异常。




http://www.devzhang.com/14464613593730.html
https://www.jianshu.com/p/aa9721e4484d








































