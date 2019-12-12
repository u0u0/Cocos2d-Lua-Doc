# Roadmap

随着Apple Metal的发布，OpenGL ES 在将来的某一天有被抛弃的可能性。于此同时Google 也有对应的下一代图像驱动 Vulken。Cocos2d-x 3.0 的架构是与OpenGL深绑定的实现方式，不大刀很难去适配新的图形驱动。Cocos2d-x 4.0 在本月发布了第一个Release版本，对渲染框架做了大改动，有一个中间层来屏蔽各平台差异，目前已经支持OpenGL 和 Metal，而Vulken的支持在将来也不是困难。

Cocos2d-x 4.0 去掉了JS系统，保留Lua和CPP开发。Cocos官方在版本上终于进行了区分，creator与Cocos2d-x 4.0定位上做了划分。针对native的游戏开发，Cocos2d-x 4.0高可定制性，依然是一个值得关注的版本，社区引擎将持续跟进Cocos2d-x 4.0的发展。

## 4.0 版本

由于 Cocos2d-x 4.0 渲染框架的变化，不可能完全兼容3.7版本，独立维护是必然的选择。但社区版尽量保留3.7中积累的优点，为将来3.7升级4.0提供移植指导方案。

* 原则上与Cocos2d-x 4.0保持高度一致，提高易用性和稳定性。
* Player不再保留，部分功能转移到项目exe内来实现。
* 保留3.7的音频引擎。
* 保留framework大部分功能。
* FairyGUI支持。
* etc...

## 3.7 后续维护

Quick-Cocos2dx-Community 3.7.x 进行打大幅度的裁剪和改进，感谢开发者的测试与反馈，3.7系列已成熟稳定。

后续将着重于BUG的修正，Tiledmap、spine等工具运行时的更新与维护。

**3.7系列会继续做稳定性修正和平台升级跟进**。