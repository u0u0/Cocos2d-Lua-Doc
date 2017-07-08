# 基于 OpenAL 的 audio engine

cocos2d-x 3.x 中的音频引擎有几个方面的问题：

1. SimpleAudioEngine每个平台独立实现，导致每个平台的表现都不一致。win32的实现是最糟糕的：性能低，不支持多音效同时播放。而其它平台也或多或少有写问题，比如android上对音效的长度有限制等。
2. 新的audio engine，实现过于复杂，android平台的代码量异常庞大。而且依旧默认使用SimpleAudioEngine作为播放引擎，一不小心开启2个音频播放器同时存在，还会有初始化冲突。
3. 需要兼顾三种语言，不能发挥lua的设计优势。

基于上述考虑以及本人之前的研究，在3.7中基于OpenAL编写新的声音引擎，并废弃所有旧接口。

## [Audio API reference](../../api/audio/index.md)

## 设计特点

1. 仅集成ogg解码器。原因：开源免费，同体积比mp3效果好。
2. 核心用cpp实现，管理代码用Lua实现。
3. 所有音频强制预加载才可播放，预加载不区分音乐或音效，预加载使用异步方式。
4. 每个音乐和音效都可以独立控制播放、暂停、音量等操作。

## 用法

预加载`audio.loadFile(path, callback)`，path为res下的相对路径，callback为异步加载后引擎通知Lua端的回调函数接口。callback参考如下：

```
function(fn, success)
	if not success then
		print("Fail to load audio:" .. fn)
		return
	end
end
```

参数fn为audio.loadFile的path，success为布尔变量，表面是否加载成功。
一旦音频文件预加载之后，可以作为背景音乐或音效进行播放。

播放BGM `audio.playBGM(path, isLoop)`，第二个参数默认为true。

播放Effect `audio.playEffect(path, isLoop)`，第二个参数默认为false，playEffect返回音源对象，可调用`pause()、resume()、stop()、setVolume(vol)`这几个方法。

> 注意：平台不同，支持的最大音源数不同。为了保障后续音效的正常播放，音源在audio内部有及时的回收机制。所以audio.playEffect返回的音源对象，不应长期持有；返回音源对象只是为了便于你进行setVolume来独立控制单个音效音量的目的。

## win32 已知问题

在某些windows声卡驱动下，如果不插入耳机，又没有其它外接扬声器，会导致OpenAL无法初始化。情况尴尬，遇到这种情况，插入耳机进行测试吧。