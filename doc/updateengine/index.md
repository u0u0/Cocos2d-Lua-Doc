# 如何从 Quick 3.3 或早期版本升级到 Quick-Cocos2dx-Community 最新版本

Quick-Cocos2dx-Community 3.6 系列 Lua接口保存了完全的兼容性。
升级只需以下简单步骤。

1. 使用 Quick 3.6 新建一个相同包名的项目。
2. 使用 project_old/res 替换文件夹 project_new/res
3. 使用 project_old/src/app 替换文件夹 project_new/src/app
4. 使用 project_old/src/config.lua 替换文件 project_new/src/config.lua
5. 在新项目中测试并开发。