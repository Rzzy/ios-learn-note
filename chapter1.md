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
核心代码 `mj_objectWithKeyValues`:
```object-c
//复杂的字典[模型中有个数组属性，数组里面又要装着其他模型的字典]
    NSDictionary *dict_m8m = @{
                  @"text" : @"Agree!Nice weather!",
                  @"user" : @{
                          @"name" : @"Jack",
                          @"icon" : @"lufy.png"
                          },
                  @"retweetedStatus" : @{
                          @"text" : @"Nice weather!",
                          @"user" : @{
                                  @"name" : @"Rose",
                                  @"icon" : @"nami.png"
                                  }
                          }
                  };
```
```object-c
#import <Foundation/Foundation.h>
@class User;
@class Status;
//Status模型
@interface Status : NSObject
@property (copy, nonatomic) NSString *text;
@property (strong, nonatomic) User *user;/* 其他模型类型 */
@property (strong, nonatomic) Status *retweetedStatus;/* 自我模型类型 */
@end
//
```
```object-c
//字典转模型，模型里面含有模型
    Status *status = [Status mj_objectWithKeyValues:dict_m8m];
    NSString *text = status.text;
    NSString *name = status.user.name;
    NSString *icon = status.user.icon;
    NSLog(@"mj-----text=%@, name=%@, icon=%@", text, name, icon);
    NSString *text2 = status.retweetedStatus.text;
    NSString *name2 = status.retweetedStatus.user.name;
    NSString *icon2 = status.retweetedStatus.user.icon;
    NSLog(@"mj-----text2=%@, name2=%@, icon2=%@", text2, name2, icon2);
   //
    //打印结果
    //2016-07-04 11:45:39.675 PPDemos[2781:87089] mj-----text=Agree!Nice weather!, name=Jack, icon=lufy.png
    //2016-07-04 11:45:39.675 PPDemos[2781:87089] mj-----text2=Nice weather!, name2=Rose, icon2=nami.png
```








































