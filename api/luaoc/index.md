# luaoc 模块

## Lua 接口

`luaoc.callStaticMethod(className, methodName, args)`

调用 Objective-C 接口。

| 参数名 | 类型 |说明|
|----|---|----|
| className | string | Objective-C 类名 |
| methodName | string | Objective-C 类方法名 |
| args | table | 参数[可选] |

返回值有两个：

* 第一个返回值 bool 表示 callStaticMethod 是否调用成功
* 第二个返回值 table 是 methodName 的返回值。

## LuaObjcBridge 模块

头文件:

```
#include "platform/ios/CCLuaObjcBridge.h"
```

----------------------------

`LuaStack *LuaObjcBridge::getStack(void)`

获取 LuaStack。

参数：无，返回值：LuaStack。

----------------------------

`int LuaObjcBridge::pushLuaFunctionById(int functionId)`

把 Lua 函数压栈。

| 参数名 | 类型 |说明|
|----|---|----|
| functionId | int | Lua端传递过来的函数 ID |

返回值：int。

* 0，成功。
* -1，失败。

----------------------------

`int LuaObjcBridge::retainLuaFunctionById(int functionId)`

把 Lua 函数引用计数加1。

| 参数名 | 类型 |说明|
|----|---|----|
| functionId | int | Lua端传递过来的函数 ID |

返回值：int。

* 0，失败。
* 大于0，当前的引用计数。

----------------------------

`int LuaObjcBridge::releaseLuaFunctionById(int functionId)`

把 Lua 函数引用计数减1。

| 参数名 | 类型 |说明|
|----|---|----|
| functionId | int | Lua端传递过来的函数 ID |

返回值：int。