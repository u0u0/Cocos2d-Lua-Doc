# 在 Lua 中使用 luaoc 直接调用 Objective-C 代码

同 luaj 一样，luaoc 可直接在 Lua 端调用 Objective-C 代码，并支持 Objective-C 回调 Lua 函数。

## 内部机制

Objective-C 可以在运行时修改对象（例如替换class）和类（例如增加、删除和替换ivar和方法），它具有移动动态语言的特性。

我们可以从 Lua 端以字符串的方式，把 Objective-C 的类名、函数名传递过来，然后用下面 Objective-C 提供的函数来实现动态调用 Objective-C 某个类的方法。

```
Class targetClass = NSClassFromString(className);
SEL methodSel = NSSelectorFromString(methodName);
NSMethodSignature *methodSig = [targetClass methodSignatureForSelector:(SEL)methodSel];
NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:methodSig];
[invocation setArgument:&dict atIndex:2];
[invocation invoke];
```

当然，LuaObjcBridge 内部还需要考虑参数的传递、返回值以及函数回调。

## luaoc 特性

* 从 Lua 调用 Objective-C Class Static Method。
* 支持`int/float/boolean/String/Lua function/Lua table`六种参数类型
* Lua function 作为参数传递给 Objective-C，可实现 Objective-C 端反向调用Lua函数。
* 从 Objective-C 调用 Lua 的全局函数，或者指定函数。

## [luaoc API reference](../../api/luaoc/index.md)

## 接口示例

`OCTest`是 Objective-C 端的一个单例类，定义有一个`LuaOCTest`函数供 Lua 端调用。

OCTest.h 与 OCTest.mm 的存放位置如下图：

![](./luaoc.png)

### OCTest.h 头文件实现

```
#import <Foundation/Foundation.h>

@interface OCTest : NSObject
{
}

@property (assign) int luaHander;
@property (readonly) NSString *name;

- (void)delay;
+ (NSDictionary *)LuaOCTest:(NSDictionary *)dict;

@end
```

### OCTest.mm 实现

```
#import "OCTest.h"
#include "platform/ios/CCLuaObjcBridge.h"

@interface OCTest ()
@end

@implementation OCTest

/* ========== begin of singleton ===========  */
__strong static OCTest *_singleton = nil;

+ (id)allocWithZone:(NSZone *)zone
{
    return [self sharedInstance];
}

+ (OCTest *)sharedInstance
{
    static dispatch_once_t pred = 0;
    dispatch_once(&pred, ^{
        _singleton = [[super allocWithZone:NULL] init];
    });
    return _singleton;
}

- (id)copyWithZone:(NSZone *)zone
{
    return self;
}

/* ========== end of singleton ===========  */

- (instancetype)init
{
    self = [super init];
    if (self) {
        _name = @"OCTest";
        _luaHander = -1;
    }
    return self;
}

- (void)delay
{
    // 1. lua 函数压栈
    cocos2d::LuaObjcBridge::pushLuaFunctionById(self.luaHander);

    // 2. 构建参数，压栈
    cocos2d::LuaValueDict item;
    item["str"] = cocos2d::LuaValue::stringValue("hello");
    item["int"] = cocos2d::LuaValue::intValue(1000);
    item["bool"] = cocos2d::LuaValue::booleanValue(TRUE);
    cocos2d::LuaObjcBridge::getStack()->pushLuaValueDict(item);
    // 3. 调用函数
    cocos2d::LuaObjcBridge::getStack()->executeFunction(1);

    // 4. 释放 func 引用计数
    cocos2d::LuaObjcBridge::releaseLuaFunctionById(self.luaHander);
}

+ (NSDictionary *)LuaOCTest:(NSDictionary *)dict
{
    if ([dict objectForKey:@"num"]) {
        // 测试 lua 传递过来的 数字
        NSLog(@"== get lua num:%d", [[dict objectForKey:@"num"] intValue]);
    }
    if ([dict objectForKey:@"str"]) {
        // 测试 lua 传递过来的 字符串
        NSLog(@"== get lua num:%@", [dict objectForKey:@"str"]);
    }
    if ([dict objectForKey:@"cb"]) {
        // 保存 handler，handler在传递过来的时候，已经被引用计数+1保护。
        [OCTest sharedInstance].luaHander = [[dict objectForKey:@"cb"] intValue];
    }

    // 测试延迟调用
    [[OCTest sharedInstance] performSelector:@selector(delay) withObject:nil afterDelay:2];

    // 反给lua的返回值。
    return [NSDictionary dictionaryWithObjectsAndKeys:
            [OCTest sharedInstance].name, @"name",
            [NSNumber numberWithInt:[OCTest sharedInstance].luaHander], @"luaHander",
            nil];
}

@end
```

OCTest 的实现有以下几个注意事项：

* OCTest.mm 以mm后缀结尾，目的是让它能调用引擎提供的`C++`接口。
* OCTest 提供了一个 Objective-C 单例类的实现参数，具体见备注。
* delay函数用来延迟调用 Lua 函数。
* LuaOCTest 是静态函数，提供给 Lua 端调用的。
* Lua 与 Objective-C 之间的参数传递都是通过 NSDictionary 进行，这是由于 Objective-C 无法获取函数的个数以及类型，统一以 NSDictionary 来进行参数的传递与返回值处理。

### Lua 调用代码

```
local args = {
	num = 20,
	str = "hello",
	cb = function (event)
		print("== cb")
		dump(event)
	end
}
local ok, rtn = luaoc.callStaticMethod("OCTest", "LuaOCTest", args)
if ok then
	print("== rtn")
	dump(rtn)
end
```

这里测试了 Lua 参数传递，返回值，以及回调函数。

运行结果：

```
2016-11-22 15:20:04.813928 luaoc Mac[1653:148212] == get lua num:20
2016-11-22 15:20:04.813953 luaoc Mac[1653:148212] == get lua num:hello
[LUA-print] == rtn
[LUA-print] dump from: [string "src/app/scenes/MainScene.lua"]:22: in function 'ctor'
[LUA-print] - "<var>" = {
[LUA-print] -     "luaHander" = 1
[LUA-print] -     "name"      = "OCTest"
[LUA-print] - }
[LUA-print] == cb
[LUA-print] dump from: [string "src/app/scenes/MainScene.lua"]:16: in function <[string "src/app/scenes/MainScene.lua"]:14>
[LUA-print] - "<var>" = {
[LUA-print] -     "bool" = true
[LUA-print] -     "int"  = 1000
[LUA-print] -     "str"  = "hello"
[LUA-print] - }
CCLuaBridge::releaseLuaFunctionById() - function id 1 released
```

## Lua function 的引用计数

由于 Lua 虚拟机具有自动垃圾回收机制，传入 Objective-C 端的 Lua function 的引用计数会在C层的 LuaObjcBridge 中自动增加1，以保护函数不被垃圾垃圾回收。

对应的引擎`C++`层的 LuaObjcBridge 提供了 `cocos2d::LuaObjcBridge::releaseLuaFunctionById(self.luaHander)` 函数用于减少 Lua function 的引用计数。