# 如何从 Quick 3.3 或早期版本升级到 Quick-Cocos2dx-Community 最新版本

## 3.6.x 系列升级方式

Quick-Cocos2dx-Community 3.6 系列 Lua接口保存了完全的兼容性。
升级只需以下简单步骤。

1. 使用 Quick 3.6 新建一个相同包名的项目。
2. 使用 project_old/res 替换文件夹 project_new/res
3. 使用 project_old/src/app 替换文件夹 project_new/src/app
4. 使用 project_old/src/config.lua 替换文件 project_new/src/config.lua
5. 在新项目中测试并开发。

## 升级到3.7

在 Quick-Cocos2dx-Community 3.7 中，引擎做了去冗余大裁剪，同时重写了音频引擎。旧的项目能否升级到3.7取决于你使用到的 api 范围。

除了完成3.6升级所列出的步骤，你还需要做如下代码修改。

1. 所有Quick UI 与 2.x 的 UI统统需要替换为ccui。
2. 音频播放代码切换到全新的播放接口。
3. 由于quick framework进行了大裁剪，需要检查对quick framework的依赖情况，替换为其它实现方式。
4. filter改为shader实现。
5. AssetsManagerEx热更新替换为自研的纯lua方案。
6. 反复测试。

## 升级到4.0

1. 升级到3.7.x最新，可保证大部分api在4.0兼容。
2. OpenGL相关的直接调用全部改为backend。
3. 自定义shader加载代码修改，shader需要兼容性测试。