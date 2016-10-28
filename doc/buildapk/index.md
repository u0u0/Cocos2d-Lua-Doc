# Android 项目的编译与打包

从 Quick-Cocos2dx-Community 3.4 开始，proj.android 模版工程修改为Android Studio，放弃对ADT的支持，移除了AnySDK的集成。apk的生成分2个部分进行。

## Build Native

proj.android 下新引入 build_native.py 脚本，去掉了之前的`.sh, .bat`等与平台相关的编译脚本。你需要安装**Python 2.x**：最新的 Python 2.7 是 Mac 系统标配；如果你使用window开发，建议下载 Python 2.7 的 32位安装包，这里有利于与 vim 的 python 插件等结合使用。

在运行 build_native.py 脚本之前，需要先安装 Android NDK，目前引擎基于 NDK r10e(r10d也ok) 测试发布。

NDK的安装就是一个解压过程，之后你需要设置一下系统Path环境变量，让命令行能直接找到`ndk-build`命令。

### Mac NDK 环境变量设置

在`~/.bash_profile`文件中加入下面代码：

```
PATH=/usr/local/bin:$PATH:/Users/u0u0/bin/android-ndk-r10e
```

> 请理解shell的工作原理，并灵活修改path变量。

最后运行下面的命令刷新shell的path：

```
$source ~/.bash_profile
```

### Windows NDK 环境变量设置

右键"计算机"，选择弹出菜单中的"属性"，然后在"控制面板主页"中点击"高级系统设置"。如下图所示：

![system config](./sysconfig.png)

然后点击 "系统属性" => "高级" => "环境变量"，如下图所示：

![system config](./env.png)

在"系统变量"的 Path 变量的尾部添加以下字段：

```
D:\u0u0\android-ndk-r10e
```

### 运行 build_native.py 脚本编译 native 代码

环境变量只需第一次设置，再确保引擎的`setup.sh`或`setup.bat`正确运行后，你就可以直接运行 build_native.py 来编译 native 代码了。

build_native.py 有几个可选参数，如下：

```
NAME
    build_native --

SYNOPSIS
    build_native [-h] [-r] [-c]

    -h 输出帮助信息
    -r 编译release版本
    -c 清理编译环境
```

build_native.py 在编译成功后，会自动拷贝 res , src 到assert目录，每当这两个文件夹下的数据有改动的时候，你都应该再次运行 build_native.py 脚本。别担心，native代码不会再次编译，整个过程很快就会完成。

## Android Studio 打包

首先你需要正确安装 Android Studio。运行 Android Studio 在启动界面选择"Open an existing Android Studio project"，在弹出的文件夹选择器中选择项目的`proj.android`文件夹。

等待项目导入完成后，选择菜单 "build" => "generate signed apk"；选择module，下一步；选择你的签名文件，填入签名的密钥，选择应用昵称，输入密钥，下一步，选择导出目录，完成apk生成。