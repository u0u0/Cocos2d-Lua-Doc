# audio 模块

> 以下 API 从 Quick-Cocos2dx-Community 3.7.0 可用。

## 接口

`audio.loadFile(path, callback)`

预加载音频文件。

| 参数名 | 类型 |说明|
|----|---|----|
| path | string | ogg音频文件相对于res的路径 |
| callback | function | 原型 func(path, isLoadedSuccess) |

返回值：无

-----------------------------

`audio.unloadFile(path)`

释放预加载的音频对象。

| 参数名 | 类型 |说明|
|----|---|----|
| path | string | ogg音频文件相对于res的路径 |

返回值：无

-----------------------------

`audio.unloadAllFile()`

释放所有预加载的音频对象。

参数：无

返回值：无

-----------------------------

`audio.playBGM(path, isLoop)`

播放背景音乐。

| 参数名 | 类型 |说明|
|----|---|----|
| path | string | ogg音频文件相对于res的路径 |
| isLoop | boolean | [可选] 是否循环播放，默认为true |

返回值：无

-----------------------------

`audio.stopBGM()`

停止背景音乐。

参数：无

返回值：无

-----------------------------

`audio.setBGMVolume(vol)`

设置背景音乐的音量。

| 参数名 | 类型 |说明|
|----|---|----|
| vol | number | 取值范围0～1 |

返回值：无

-----------------------------

`audio.playEffect(path, isLoop)`

播放音效。

| 参数名 | 类型 |说明|
|----|---|----|
| path | string | ogg音频文件相对于res的路径 |
| isLoop | boolean | [可选] 是否循环播放，默认为false |

返回值：音源对象，可调用对象方法。

-----------------------------

`audio.setEffectVolume(vol)`

设置音效的音量。

| 参数名 | 类型 |说明|
|----|---|----|
| vol | number | 取值范围0～1 |

返回值：无。

-----------------------------

`audio.stopEffect()`

停止音效。

参数：无。

返回值：无。

-----------------------------

`audio.stopAll()`

停止背景音乐和所有音效。

参数：无。

返回值：无。

-----------------------------

`audio.pauseAll()`

暂停背景音乐和所有音效。

参数：无。

返回值：无。

-----------------------------

`audio.resumeAll()`

恢复背景音乐和所有音效。

参数：无。

返回值：无。