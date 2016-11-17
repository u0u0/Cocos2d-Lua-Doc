# luaj 模块

## Lua 接口

`luaj.callStaticMethod(className, funcName, args, sig)`

调用 Java 接口。

| 参数名 | 类型 |说明|
|----|---|----|
| className | string | Java 类名 |
| funcName | string | 成员函数名 |
| args | table | 参数[可选] |
| sig | string | 函数签名[可选] |

返回值：

* 当成功时，第1个值为 true，第2个值是 Java 方法的返回值(如果有)。
* 当失败时，第1个值为 false，第2个值是错误代码。

| 错误代码 | 描述 |
|-----|------|
| -1 | 不持的参数类型或返回值类型 |
| -2 | 无效的签名 |
| -3 | 找不到指定的方法 |
| -4 | Java 方法执行时抛出了异常 |
| -5 | Java 虚拟机出错 |

## Java 接口

包名：

```
import org.cocos2dx.lib.Cocos2dxLuaJavaBridge;
```

-----------------------------

`public static native int callLuaFunctionWithString(int luaFunctionId, String value);`

调用 Lua 接口。

| 参数名 | 类型 |说明|
|----|---|----|
| luaFunctionId | int | 从Lua传递过来的函数ID |
| value | string | 字符串参数 |

-----------------------------

`public static native int callLuaGlobalFunctionWithString(String luaFunctionName, String value);`

调用 Lua 接口。

| 参数名 | 类型 |说明|
|----|---|----|
| luaFunctionName | string | Lua全局函数名 |
| value | string | 字符串参数 |

-----------------------------

`public static native int retainLuaFunction(int luaFunctionId);`

luaFunctionId 对应的 Lua 函数的引用计数加1。

| 参数名 | 类型 |说明|
|----|---|----|
| luaFunctionId | int | 从Lua传递过来的函数ID |

-----------------------------

`public static native int releaseLuaFunction(int luaFunctionId);`

luaFunctionId 对应的 Lua 函数的引用计数减1。

| 参数名 | 类型 |说明|
|----|---|----|
| luaFunctionId | int | 从Lua传递过来的函数ID |