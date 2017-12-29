### UITableView详解

##### 概述
`UITableView`堪称`UIKit`里面最复杂的一个控件了，同时也是最常用的控件，使用起来不算难，但是要用好并不容易。当使用的时候我们必须要考虑到后台数据的设计，`tableViewCell`的设计和重用以及`tableView`的效率等问题。

`UITableView`只能有一列数据(cell)，且只支持纵向滑动，当创建好的`tablView`第一次显示的时候，我们需要调用其`reloadData`方法，强制刷新一次，从而使`tableView`的数据更新到最新状态。

UITableView使用时常遇到的问题大致如下：
+ 顶层设计
    + TableView的类型选择
    + Section的设置，合适分组，如何分组等问题
    + Cell的使用，是否自定义？是否使用xib？等等
    + Cell填充问题，一般应遵循重用、解耦原则。
+ 重用问题
    + UITableViewCell重用
    + UITableViewHeaderFooterView重用
+ 协议方法
    + Delegate与DataSource代理方法的含义、实现  
+ UI问题
    + 分割线
    + Cell自定义
    + 默认选中某个Cell

####`NSIndexPath`