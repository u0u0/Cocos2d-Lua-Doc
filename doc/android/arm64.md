# 发布 Android arm64-v8a

> Supported from Quick-Cocos2dx-Community 3.7.6

## 升级说明

经过一番折腾，NDK r10d 是支持 arm64的，所以我们的 Android 开发环境暂时不用升级。NDK r10d 还可以再战 3 年。。。

老项目升级需要下列步骤：

1. 拷贝所有 arm64-v8a 文件夹到对应的目录。
2. `build_native.py` 经过升级改造，加入了 `-a` 参数指定编译不同的 ABI，默认编译 `armeabi-v7a`。把升级后的 `build_native.py` 拷贝到项目下面覆盖原来的文件，同时把改进过的 `Application.mk` 也拷贝到项目下面覆盖原来的文件。
3. `AppDelegate.cpp` 也经过了升级改造，请对比覆盖。
4. 升级 curl 的 Android include 文件，加入 64 位的头文件。

参考 cimmit [add support for Android arm64-v8a.](https://github.com/u0u0/Quick-Cocos2dx-Community/commit/a2dd46e10978aa9327eb9570bd9b12b5ca3b9458)

## Luajit bytecode 64

现在 arm64-v8a 也是运行在 64 模式下了，对应的 bytecode 也是需要 64 位的。为了方便 windows 下发布对用的 bytecode，引擎工具目录对应添加了 windows 的 64 位 luajit，并改进了相应脚本。

AppDelegate.cpp 现在自动识别 iOS 和 Android 的位数来加载 game32.zip 或 game64.zip。

## 编译 so

```
$build_native.py -a arm64-v8a
```

## 编译 apk

项目文件 `proj.android/app/build.gradle` ndk 配置加入 arm64-v8a， 如下：

```
ndk {
	abiFilters "arm64-v8a"
	abiFilters "armeabi-v7a"
}
```