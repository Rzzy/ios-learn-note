转载：https://www.jianshu.com/p/475b28160c89

**1、简单的字典 --> 模型:**
核心代码 `mj_objectWithKeyValues`
```object-c
typedef enum {
    SexMale,
    SexFemale
} Sex;
@interface User : NSObject
@property (copy, nonatomic) NSString *name;/* 姓名 */
@property (copy, nonatomic) NSString *icon;/* 头像 */
@property (assign, nonatomic) unsigned int age;/* 年龄 */
@property (copy, nonatomic) NSString *height;/* 身高 */
@property (strong, nonatomic) NSNumber *money;/* 资产 */
@property (assign, nonatomic) Sex sex;/* 性别 */
@property (assign, nonatomic, getter=isGay) BOOL gay;/* 是否是同性恋 */
@end
   //简单的字典
    NSDictionary *dict_user = @{
                           @"name" : @"Jack",
                           @"icon" : @"lufy.png",
                           @"age" : @20,
                           @"height" : @"1.55",
                           @"money" : @100.9,
                           @"sex" : @(SexFemale),/* 枚举需要使用NSNumber包装 */
                           @"gay" : @YES
                           };
    User *user = [User mj_objectWithKeyValues:dict_user];
    NSLog(@"MJ---%@----%@---%u---%@---%@---%u----%d",user.name,user.icon,user.age,user.height,user.money,user.sex,user.gay);
  //打印结果
  //2016-07-04 11:06:59.746 PPDemos[2432:73824] MJ---Jack----lufy.png---20---1.55---100.9---1----1

```

**2、JSON字符串 --> 模型**
核心代码：` mj_objectWithKeyValues`:
```object-c
// 定义一个JSON字符串
    NSString *jsonStr = @"{\"name\":\"Jack\", \"icon\":\"lufy.png\", \"age\":20}";
    User *user = [User mj_objectWithKeyValues:jsonStr];
    NSLog(@"MJ---%@----%@---%u",user.name,user.icon,user.age);
    //打印结果
    //2016-07-04 11:16:04.655 PPDemos[2563:78561] MJ---Jack----lufy.png---20
```
 **3、复杂的字典 --> 模型 (模型里面包含了模型)**











































