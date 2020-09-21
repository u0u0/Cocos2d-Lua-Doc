# LabelTTF与RichText的扩展

> 适用于3.7.1以上3.7.x版本

## LabelTTFEx.lua

可在`https://github.com/u0u0/Lua-utils/blob/master/src/app/utils/LabelTTFEx.lua`获取。

LabelTTFEx对引擎原声的lable控件进行了扩展，支持下划线、粗体和斜体。

> 注意：粗体只对外部TTF字体有效，系统字体无效。

## RichTextEx.lua

可在`https://github.com/u0u0/Lua-utils/blob/master/src/app/utils/RichTextEx.lua`获取。

RichTextEx 依赖于 htmlparser 和 LabelTTFEx，均可在上述目录获取到。

### 用法

```
local RichTextEx = require("app.utils.RichTextEx")
local clickDeal = function(id, content)
	print(id, content)
end

RichTextEx.new([==[
<t c="#f00" s="50" id="click">Hello World!</t><br><i s="0_DEMO/icon/baogao_2.png" id="iamgeaa"></i>
]==], clickDeal)
	:addTo(self)
	:center()
```

### 说明

类似html的标记语言，`<t></t>`标签表示文字，`<i></i>`标签表示图片，`<br>`表示换行。如果加入了id属性，自动添加触摸事件和下划线。

> RichText的换行、触摸事件分发和排版在3.7.1中有对应的引擎bug修正，旧版本引擎不适用。