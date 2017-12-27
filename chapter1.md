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
**4、模型中有个数组属性，数组里面又要装着其它模型**
核心代码 `mj_objectWithKeyValues`和`mj_objectClassInArray`:

```object-c
@interface Ad : NSObject
    @property (copy, nonatomic) NSString *image;
    @property (copy, nonatomic) NSString *url;
@end
```
```object-c
@interface StatusResult : NSObject
/** 存放着一堆的微博数据（里面都是Status模型） */
@property (strong, nonatomic) NSMutableArray *statuses;
/** 存放着一堆的广告数据（里面都是Ad模型） */
@property (strong, nonatomic) NSArray *ads;
@property (strong, nonatomic) NSNumber *totalNumber;
@end
```
```object-c
#import "StatusResult.h"
#import "MJExtension.h"
@implementation StatusResult
/* 数组中存储模型数据，需要说明数组中存储的模型数据类型 */
+(NSDictionary *)mj_objectClassInArray
{
    return @{
             @"statuses" : @"Status",
             @"ads" : @"Ad"
             };
}
@end
#####在VC里实现以下来解析数据
NSDictionry dict_m8a = @{
    @"statuses" : 
        @[
            @{
                @"text" : @"Nice weather!",
                @"user" : @{
                    @"name" : @"Rose",
                    @"icon" : @"nami.png"
                }
            },
            @{
                @"text" : @"Go camping tomorrow!",
                @"user" : @{
                    @"name" : @"Jack",
                    @"icon" : @"lufy.png"
                }
            }
        ],
    @"ads": 
        @[
            @{
                @"image" : @"ad01.png",
                @"url" : @"http://www.ad01.com"
            },
            @{
                @"image" : @"ad02.png",
                @"url" : @"http://www.ad02.com"
            }
        ],
        @"totalNumber" : @"2014"
    };
//【重点，核心】》》数组中存储模型数据，需要说明数组中存储的模型数据类型 
[StatusResult mj_setupObjectClassInArray:^NSDictionary *{
    return @{
        @"statuses" : @"Status",
        // @"statuses" : [Status class],
        @"ads" : @"Ad"
        // @"ads" : [Ad class]
    };
}];
// Equals: StatusResult.m implements + mj_objectClassInArray method.

//以上方法在VC里写，如果多个地方解析该model，就要写多次，最好在model的.m文件写！
//字典转模型，支持模型的数组属性里面又装着模型
StatusResult *result = [StatusResult mj_objectWithKeyValues:dict_m8a];
//打印博主信息
for (Status *status in result.statuses) {
    NSString *text = status.text;
    NSString *name = status.user.name;
    NSString *icon = status.user.icon;
    NSLog(@"mj---text=%@, name=%@, icon=%@", text, name, icon);
}
//打印广告
for (Ad *ad in result.ads) {
    NSLog(@"mj---image=%@, url=%@", ad.image, ad.url);
}
//打印结果
//2016-07-04 13:47:58.994 PPDemos[3353:113055] mj---text=Nice weather!, name=Rose, icon=nami.png
//2016-07-04 13:47:58.995 PPDemos[3353:113055] mj---text=Go camping tomorrow!, name=Jack, icon=lufy.png
//2016-07-04 13:47:58.995 PPDemos[3353:113055] mj---image=ad01.png, url=http://www.ad01.com
//2016-07-04 13:47:58.995 PPDemos[3353:113055] mj---image=ad02.png, url=http://www.ad02.com
```
**5、模型中的属性名和字典中的key不相同(或者需要多级映射)**
核心代码： `mj_objectWithKeyValues:`和`mj_replacedKeyFromPropertyName`：

- 多级映射，用点语法设置
```object-c
@interface Bag : NSObject
    @property (copy, nonatomic) NSString *name;
    @property (assign, nonatomic) double price;
@end
```
```object-c
import <Foundation/Foundation.h>
@class Bag;
@interface Student : NSObject
    @property (copy, nonatomic) NSString *ID;
    @property (copy, nonatomic) NSString *desc;
    @property (copy, nonatomic) NSString *nowName;
    @property (copy, nonatomic) NSString *oldName;
    @property (copy, nonatomic) NSString *nameChangedTime;
    @property (strong, nonatomic) Bag *bag;
@end
```
```object-c
@implementation Student
+(NSDictionary *)mj_replacedKeyFromPropertyName
{
// 实现这个方法的目的：告诉MJExtension框架模型中的属性名对应着字典的哪个key
    return @{
        @"ID" : @"id",
        @"desc" : @"desciption",
        @"oldName" : @"name.oldName",
        @"nowName" : @"name.newName",
        @"nameChangedTime" : @"name.info[1].nameChangedTime",
        @"bag" : @"other.bag"
    };
}
@end
```
```object-c
NSDictionry *dict_nokey = @{
    @"id" : @"20",
    @"desciption" : @"kids",
    @"name" : @{
        @"newName" : @"lufy",
        @"oldName" : @"kitty",
        @"info" : 
            @[
                @"test-data",
                @{
                    @"nameChangedTime" : @"2013-08"
                }
            ]
        },
    @"other" : @{
        @"bag" : 
            @{
                @"name" : @"a red bag",
                @"price" : @100.7
            }
    }
};
```
```object-c
//
// // How to map
// [Student mj_setupReplacedKeyFromPropertyName:^NSDictionary *{
    // return @{
    // @"ID" : @"id",
    // @"desc" : @"desciption",
    // @"oldName" : @"name.oldName",
    // @"nowName" : @"name.newName",
    // @"nameChangedTime" : @"name.info[1].nameChangedTime",
    // @"bag" : @"other.bag"
    // };
// }];
// // Equals: Student.m implements +mj_replacedKeyFromPropertyName method.
//字典转模型，支持多级映射
Student *stu = [Student mj_objectWithKeyValues:dict_nokey];
//打印
NSLog(@"ID=%@, desc=%@, oldName=%@, nowName=%@, nameChangedTime=%@",
stu.ID, stu.desc, stu.oldName, stu.nowName, stu.nameChangedTime);
NSLog(@"bagName=%@, bagPrice=%f", stu.bag.name, stu.bag.price);
//2016-07-04 14:20:28.082 PPDemos[3602:126004] ID=20, desc=kids, oldName=kitty, nowName=lufy, nameChangedTime=2013-08
//2016-07-04 14:20:28.082 PPDemos[3602:126004] bagName=a red bag, bagPrice=100.700000
```


































