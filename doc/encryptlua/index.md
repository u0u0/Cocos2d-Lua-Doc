# Quick-Cocos2dx-Community 在发布时加密lua源文件

Cocos2d-Lua(Quick-cocos2d-x) 集成有 Player 模拟器，可以快速测试 Lua 游戏代码。
当游戏开发完成，需要打包发布的时候，我们不希望 Lua 源代码以明文的方式暴露在 apk 或 ipa 包中，否则你的辛苦劳动成果将被轻易剽窃。Quick框架已为我们提供了这方面的支持。
本文记录ipa打包加密Lua全过程。

环境：

* 引擎版本：Quick-Cocos2dx-Community 3.6 以上
* 演示平台：Mac with Xcode on iOS


1. 打开Terminal，切换到项目目录下。

    $cd /Users/u0u0/Documents/project/quick_project/test

    > 注：/Users/u0u0/Documents/project/quick_project/test 为我用 player 建立的项目根路径。

2. 运行Quick提供的 compile_scripts.sh 加密工具。

    $/Users/u0u0/Documents/quick-comminuty/quick/bin/compile_scripts.sh -i src -o res/game.zip -e xxtea_zip -ek aaa -es Xt -b 64

    compile_scripts.sh 可以在 Quick 安装路径下找到，需要输入全路径。参数详情如下：

    * -h 帮助
    * -i 源码目录
    * -o 输出文件或目录
    * -p 包前缀
    * -m 编译模式：zip（zip压缩格式）、files（离散文件）
    * -e 加密模式：xxtea_zip（对zip包进行 xxtea 加密）、xxtea_chunk（对.luac 进行加密）
    * -ek 加密密钥
    * -es 加密签名
    * -ex 编译后的文件的后缀名
    * -c 从一个文件加载参数列表
    * -q 生成过程不输出信息
    * -b 32(默认值)bytecode 或 64 位 bytecode

    上例中，加密 lua 源文件所在的 src 目录，输出到<strong>res/game.zip</strong>,加密模式为zip包xxtea，加密密钥：aaa，加密签名：Xt。

3. 打开 iOS 工程文件，路径在 /Users/u0u0/Documents/project/quick_project/test/frameworks/runtime-src/proj.ios_mac/test.xcodeproj。
去掉src文件夹的引用，入下图所示：

	![remove ref](./removeref.png)

    > 注：不是删除src，仅去引用，这样源代码就不会被打包进ipa了。

4. 修改启动项

    打开 /Users/u0u0/Documents/project/quick_project/test/frameworks/runtime-src/Classes/AppDelegate.cpp，修改 Lua 启动入口。

    在 `LuaStack* stack = engine->getLuaStack();` 之后加入：

        stack->setXXTEAKeyAndSign("aaa", "Xt");//与game.zip的生成参数对应
        // load framework
        stack->loadChunksFromZIP("res/game.zip");
        stack->executeString("require 'main'");

    并屏蔽掉原程序入口：

        #else
            //engine->executeScriptFile(ConfigParser::getInstance()->getEntryFile().c_str());
        #endif

5. 运行Xcode测试，程序正常启动。如果看到如下log信息，说明已使用加密的game.zip包

    ```
    lua_loadChunksFromZIP() - load zip file: /private/var/mobile/Containers/Bundle/Application/A2985A39-3588-48AD-8219-2476A12B956E/test iOS.app/res/game.zip*
    lua_loadChunksFromZIP() - loaded chunks count: 125
    ```