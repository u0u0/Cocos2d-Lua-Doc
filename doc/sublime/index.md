# quick-community-dev 插件安装

此插件为**Sublime Text**插件(建议安装sublime 3.x)，且基于**QuickXDev**进行修改，从而为Quick社区版**Quick-Cocos2dx-Community**提供支持。

> vim 开发插件请移步到：[https://github.com/u0u0/vim-quick-community](https://github.com/u0u0/vim-quick-community)

在此，特别感谢**QuickXDev**开发者为众多quick使用者在开发过程中所带来的便利。本文所述插件将站在巨人的肩膀上进行修改与维护，再次感谢**QuickXDev**开发者。

下文将介绍如何为Sublime Text本地手动安装此插件。

## 安装插件

打开 **Sublime Text** 中依次选择 **Preferences** ==> **Browse Packages...** ，此时将进入到Sublime Text的 **Packages** 目录下。

方式一：在上述目录下，调用 **git clone [https://github.com/u0u0/quick-comminuty-dev.git](https://github.com/iTyran/quick-comminuty-dev.git)** 命令将仓库克隆到目录下。

方式二：然后[点击此处下载插件](https://github.com/u0u0/quick-comminuty-dev/archive/master.zip) 并解压到上述路径下，并将文件夹更名为 **quick-comminuty-dev**。

在Sublime Text 中依次选择 **Preferences** ==> **Package Settings** ==> **quick-community-dev** ==> **Setting-User**，此时将自动创建一个文件，在文件中输入如下内容，并保存为 **quick-comminuty-dev.sublime-settings**：

```
{
    // must set the path
    "quick_cocos2dx_root": "此处填入引擎所在路径",
    // lua template attributes
    "date_format": "%Y-%m-%d %H:%M:%S",
    // i.e. peter or peter (peter@gmail.com)
    "author": "此处填入你的名字",
    // compile_scripts encrypt key,no encrypt when empty
    "compile_scripts_key": ""
}
```

## 测试

使用Sublime Text打开新建的quick项目，并在**src**文件夹上右键选择**Run With Player**，项目运行成功。