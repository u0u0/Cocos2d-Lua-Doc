# Quick-Cocos2dx-Community 在发布时加密资源文件

在 3.6 中，新增加`Quick-Cocos2dx-Community/quick/bin/encrypt_res/encrypt_res`，用于加密图片资源。
下面展示如何在发布游戏的时候使用encrypt_res加密资源。

示例环境：

* 引擎版本：Quick-Cocos2dx-Community 3.6
* 演示平台：Mac with Xcode on iOS

encrypt_res使用过程如下：

1. 打开Terminal，切换到项目目录下。

    $cd /Users/u0u0/Documents/project/quick_project/test

    > 注：/Users/u0u0/Documents/project/quick_project/test 为我用 player 建立的项目根路径。

2. 运行Quick提供的 encrypt_res.sh 工具。

    $/Users/u0u0/Documents/quick-comminuty/quick/bin/encrypt_res.sh -i res -o resnew -es XXTEA -ek test

    encrypt_res.sh 可以在 Quick 安装路径下找到，需要输入全路径。参数详情如下：

    * -h 帮助
    * -i 原资源目录名
    * -o 目标目录名
    * -p 包前缀
    * -ek 加密密钥
    * -es 加密签名
    * -q 生成过程不输出信息

    上例中，加密 res 资源文件下的所有图片，并输出到 resnew 目录，加密密钥：test，加密签名：XXTEA。

    > 注：未加密的文件会直接拷贝到resnew。

3. res文件夹更名为res_unEncrypt，resnew文件夹更名为res。

4. 在AppDelegate.cpp中加入解码信息。

    打开 /Users/u0u0/Documents/project/quick_project/test/frameworks/runtime-src/Classes/AppDelegate.cpp，在启动 Lua main入口前加入下面的代码。

    ```
    FileUtils::getInstance()->setResourceEncryptKeyAndSign("test", "XXTEA");
    ```

5. 同未加密之前一样编译项目进行测试。