# 4.0 使用 backend 加载自定义 shader

4.0渲染底层抽象出了backend，以屏蔽OpenGL和Metal的差异，自定义shader的语法依然保持了OpenGL风格，引擎内部用了一个三方库把OpenGL shader转换成Metal shader来使用。


## 着色器示例

与3.7相比, 片段着色器中的部分参数名称有变化，以下是一个简单的示例 

### 顶点着色器

```
attribute vec4 a_position;
attribute vec2 a_texCoord;
attribute vec4 a_color;

uniform mat4 u_MVPMatrix;

#ifdef GL_ES
varying lowp vec4 v_fragmentColor;
varying mediump vec2 v_texCoord;
#else
varying vec4 v_fragmentColor;
varying vec2 v_texCoord;
#endif

void main()
{
    gl_Position = u_MVPMatrix * a_position;
    v_fragmentColor = a_color;
    v_texCoord = a_texCoord;
}
```

### 片段着色器

```
#ifdef GL_ES
precision lowp float;
#endif

varying vec4 v_fragmentColor;
varying vec2 v_texCoord;

uniform sampler2D u_texture;

// v_fragmentColor.rgb use for tint, v_fragmentColor.a use for mulit
void main()
{
	vec4 texColor = texture2D(u_texture, v_texCoord);
	if (texColor.a <= 0.0)
		discard;

	// Unpremult to get original color
	vec4 colorMask = vec4(v_fragmentColor.rbg / v_fragmentColor.a, 1.0);
	gl_FragColor = texColor * (1.0 - v_fragmentColor.a) + colorMask * v_fragmentColor.a;
}
```

## Lua加载代码

```
local vsh = require('app.shaders.' .. vshStr)
local frag = require('app.shaders.' .. fragStr)
local program = ccb.Device:getInstance():newProgram(vsh, frag)
local state = ccb.ProgramState:new(program)
sprite:setProgramState(state)
```

在4.0中**program是建议缓存的**，可自己lua写个缓存器，但state不需要，同一个program可以给不用的精灵保持不同的渲染状态。

## 设置ProgramState的参数

```
state:setUniformInt("u_intvalue", 0)
state:setUniformFloat("u_floatvalue", 0.1)
state:setUniformVec2("u_sizevalue", {x = 1, y = 1})
state:setUniformVec4("u_rect", { x = 1, y = 1, w = 1, z = 1 })
```

## 使用内部shader设置灰度

引擎内部Program已在c++中缓存，无需lua端缓存。通过下面的方法获取并使用。

```
local program = ccb.Program:getBuiltinProgram(cc.backendProgramType.GRAY_SCALE)
local state = ccb.ProgramState:new(program)
sprite:setProgramState(state)
```

## 还原为精灵默认shader

```
local program = ccb.Program:getBuiltinProgram(cc.backendProgramType.POSITION_TEXTURE_COLOR)
local state = ccb.ProgramState:new(program)
sprite:setProgramState(state)
```