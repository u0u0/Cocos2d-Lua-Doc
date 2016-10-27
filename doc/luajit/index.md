# Quick-Cocos2dx-Community 中 64 位 Luajit 使用说明

在 3.6 中，我们加入了 Luajit 2.1 以支持 64 位 bytecode，并替换掉了原生lua。
由于android并没有强制要求64位，Luajit在不用的平台使用不同的位数支持。情况如下：

* Android、Win32、Mac 继续使用32位的jit。
* iOS更新硬件来决定使用的jit位数，5s以上设备使用64位jit，以下设备使用32位jit。


事实上每个平台都需要独立的luajit库支撑，引擎提供的luajit包含了各个平台的库，游戏开发者可放心使用。

**在大部分时候，你不需要感知Luajit的存在，依然像往常一样正常使用 Player 来开发游戏。**

以下两个情况是你需要注意的：

1. 使用 Xcode 的 iOS 模拟器来测试代码。如果你选择iPhone 5s或以上的模拟器，那么你需要在 project 的 **other linker flags** 中增加以下参数：

	```
	-pagezero_size 10000 -image_base 100000000
	```

	如下图所示：

    ![otherlink.png](./luajitios.png)

    > 注意：iPhone 4s模拟器不需要设置。
    > 调试完成请去掉这个参数设置，否则AppStore上新检查将不能通过。

2. 游戏发布的时候，针对iOS你需要用 **[compile_scripts](http://www.tyrantek.com/archives/411/)** 分别生产 32 和 64 位的 bytecode包，并在游戏启动逻辑加入系统位数判断，以启动不同的包。

    > Quick comminuty 3.6中的compile_scripts已支持在Mac平台生成iOS上可运行的64位bytecode。

	新建的工程中的文件`runtime-src/Classes/AppDelegate.cpp`有示例代码，如下：

    ```
    #if 0
        // use luajit bytecode package
        stack->setXXTEAKeyAndSign("2dxLua", "XXTEA");

    #ifdef CC_TARGET_OS_IPHONE
        if (sizeof(long) == 4) {
            stack->loadChunksFromZIP("res/game.zip");
        } else {
            stack->loadChunksFromZIP("res/game64.zip");
        }
    #else // #ifdef CC_TARGET_OS_IPHONE
        stack->loadChunksFromZIP("res/game.zip");
    #endif
        stack->executeString("require 'main'");
    #else // #if 0
        // use discrete files
        engine->executeScriptFile("src/main.lua");
    #endif
    ```

    发布正式ipa或apk包的时候，请把`#if 0`修改为`#if 1`。

    > 注：3.6中修改了`stack->setXXTEAKeyAndSign`接口，因为两个长度参数完全多余。