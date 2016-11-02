# 安装 Quick-Cocos2dx-community 的 vim 开发插件

支持的引擎版本：Cocos2d-Lua v3.3 以及 Quick-Cocos2dx-community 3.6 以后版本

## 安装

如果你使用 Pathogen 进行插件管理，安装过程如下：

```
cd ~/.vim/bundle
git clone https://github.com/u0u0/vim-quick-cocos2d-x
```

> 注意：插件需要vim的python扩展支持，MacVim自带python支持，并与Mac系统的python2.7 匹配；Window下需要安装Python官方的32位版本，因为Vim for windows的安装包是32位的，两者需要匹配才能正常工作。

## 设置

在 `.vimrc` 中加入:

```
let g:cocos2dx_diction_location = '~/.vim/bundle/vim-quick-cocos2d-x/key-dict'
```

## 用法

* 使用快捷键`<C-x><C-o>`或`<Tab>`来触发函数自动补全。
* 使用快捷键`<F5>`快速启动Player运行lua文件所在的游戏项目。