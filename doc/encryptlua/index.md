# Quick-Cocos2dx-Community 在发布时打包Lua代码

> Update to Quick-Cocos2dx-Community 3.7

Lua代码打包有两个好处：

1. 方便用 cc.LuaLoadChunksFromZIP 做热更新。
2. 充分利用luajit的bytecode保护的你源代码。

> **注意：3.7 新的脚本只打包，不进行xxtea加密。**

## 示例

```
$/xxxx/Quick-Cocos2dx-Community/quick/bin/PackageScripts.py -p /Users/u0u0/Desktop/testProject -o game -b 32
```

上例中，打包 lua 源文件所在的 src 目录，输出到<strong>res/game32.zip</strong>。

> 注：-b 参数可选64，仅在mac中有效，它编译iOS使用的64位bytecode.

1. 打开 iOS 工程文件，路径在 /Users/u0u0/Documents/project/quick_project/test/frameworks/runtime-src/proj.ios_mac/test.xcodeproj。
去掉src文件夹的引用，入下图所示：

	![remove ref](./removeref.png)

    > 注：不是删除src，仅去引用，这样源代码就不会被打包进ipa了。

2. 修改启动项

    打开 /Users/u0u0/Documents/project/quick_project/test/frameworks/runtime-src/Classes/AppDelegate.cpp，修改 Lua 启动入口。

把`#if 0`改为`#if 1`，这样引擎切换启动入口为game32.zip中的lua代码。

3. 运行Xcode测试，程序正常启动。如果看到如下log信息，说明已使用game.zip包进行加载

    ```
    lua_loadChunksFromZIP() - load zip file: /private/var/mobile/Containers/Bundle/Application/A2985A39-3588-48AD-8219-2476A12B956E/test iOS.app/res/game.zip*
    lua_loadChunksFromZIP() - loaded chunks count: 125
    ```

## Android 平台的差异

大部分过程和iOS相同，不同点在第3条，Android修改资源的引用分下面三个步骤：

1. 运行 build_native.py -r 脚本，自动拷贝 res 到assert目录。
	
	> 注：-r 模式只会拷贝 res。

2. 用 Android Studio打包apk。