# Cocos2d-Lua-Community 概述

## 总览

Cocos2d-Lua-Community 衍生自Cocos2d-x 4.0, 增强Lua接口，改进易用性。Lua的 "frameworks" 兼容 Quick-Cocos2dx-Community v3.7.x.

## 特性

* 绿色，无需安装，无需环境变量.
* OpenAL音频输出，为Lua设计.
* 集成更多实用Lua库: protobuf,lsqlite3等.
* TMX 运行时重构, 支持 Tiled Map Editor 1.2+.
* Spine 运行时支持 3.8.
* FairyGUI支持.

## 开发环境需求

* Mac OS X 10.9+, Xcode 10+
* Windows 7+, Visual Studio 2019
* Python 3.5+
* Android: NDK r20+, Android Studio 3.4+
* Cmake 3.16+ (Android Studio's cmake 插件 3.6+)

## 运行环境需求

* iOS 9.0+ for iPhone / iPad games
* Android 3.0.0+ for Android
* OS X v10.9+ for Mac games
* Windows 7+ for Win games, [Microsoft Visual C++ Redistributable for Visual Studio 2019 x86](https://aka.ms/vs/16/release/VC_redist.x86.exe), [Microsoft Visual C++ Redistributable for Visual Studio 2019 x64](https://aka.ms/vs/16/release/VC_redist.x64.exe)

## Lua Game Runner

> LuaGameRunner 的二进制只包含在网盘发布的 Cocos2d-Lua-Community.zip 中, GitHub 只含有源代码.

> 引擎下载链接: https://pan.baidu.com/s/162CmRTXFOSEChF6D5IJQzQ 提取码: 89jt 

LuaGameRunner用来替代Player，设计简单有效.

* 默认运行，启动引擎的测试用例。
* 传参运行, 可启动指定项目。
* 目前支持的快捷键:
	* "F5": 重新启动当前项目.

### 在 Mac 上运行

```
$cd Cocos2d-Lua-Community
$open ./tools/runner/bin/LuaGameRunner.app
```
### 在 Win 上运行

```
>cd Cocos2d-Lua-Community
>tools/runner/bin/win32/LuaGameRunner.exe
```

### 命令行参数(配合代码编辑器插件使用)

* --width (int), 游戏窗口宽。
* --height (int), 游戏窗口高。
* --scale (int), 窗口缩放因子, 内部使用 值/100 得到最终的缩放因子.
* --log, 输出Log到文件。
* --gamedir, 启动游戏的根路径。
* --writedir, 指定游戏的可写路径。

示例:

```
//windows
>LuaGameRunner.exe --width 1136 --height 640 --scale 75 --log --gamedir /newprj --writedir /newprj/output
//mac
$LuaGameRunner.app/Contents/MacOS/LuaGameRunner --width 1136 --height 640 --scale 75 --log --gamedir /newprj --writedir /newprj/output
```

## 新建项目

> CreateProject.py 用法与 3.7.x 相同，但使用python 3.x语法。

需要安装 Python 3.5+.

创建示例：

```
$cd Cocos2d-Lua-Community
$./tools/CreateProject.py -p www.www.newgame -o /ParentPath
```

> 自动创建 `/ParentPath/newgame` 作为项目根路径.

获取帮助：

```
$CreateProject.py -h
```