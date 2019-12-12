# 自定义 Shader 的使用

Quick-Cocos2dx-Community 基于 OpenGL ES 开发，所以它的 shader 完全尊从 OpenGL ES 2.0 的规范。本篇主要讲解  Quick 中让精灵使用自定义 shader 进行渲染的用法，以及 Quick 在 shader 中的一些约定变量的含义。 shader 本身的运作以及语法规范请参考 OpenGL ES 2.0 的编程规范。

## 顶点着色器

test.vert 示例代码如下：

```
attribute vec4 a_position;
attribute vec2 a_texCoord;
attribute vec4 a_color;

#ifdef GL_ES
varying lowp vec4 v_fragmentColor;
varying mediump vec2 v_texCoord;
#else
varying vec4 v_fragmentColor;
varying vec2 v_texCoord;
#endif

void main()
{
    gl_Position = CC_PMatrix * a_position;
    v_fragmentColor = a_color;
    v_texCoord = a_texCoord;
}
```

输入参数：

* a_position, 顶点坐标。
* a_texCoord，纹理坐标。
* a_color，顶点颜色。

输入参数均是 Quick 中协定好的参数名，你不应该去修改它的命名，否则引擎不能正常渲染。

输出到片段着色器的参数：

* v_fragmentColor, 顶点颜色
* v_texCoord, 纹理坐标

输出参数的命名可以自行修改，但是需要与接下来的片段着色器代码相匹配。

`#ifdef GL_ES`是 GLSL 语言支持的宏定义，用来判断是否是ES平台，这里用来让shader代码自适应PC和手机平台。

> 注意：大部分情况下，自定义shader的编码工作集中在片段着色器，顶点着色器拷贝上面的代码即可。

## 片段着色器

test.frag 实例代码如下：

```
varying vec4 v_fragmentColor;
varying vec2 v_texCoord;

uniform vec2 v_mousePosition;

void main()
{
	if (v_texCoord.x>v_mousePosition.x-0.1 &&
			v_texCoord.x<v_mousePosition.x+0.1 &&
			v_texCoord.y>v_mousePosition.y-0.1 &&
			v_texCoord.y<v_mousePosition.y+0.1) {
		gl_FragColor = texture2D(CC_Texture0, v_texCoord);
	} else {
		gl_FragColor = vec4(1.0,0.0,0.0,.8);
	}
}
```

这个片段着色器实现了：输入一个坐标，坐标为中心的矩形区域使用原始纹理渲染，而超出范围的用红色渲染。一个类似聚光灯的效果，只不过这里是矩形。

注意以下几点：

* OpenGL 颜色取之为 0 ~ 1 之间的浮点数。
* 纹理坐标依然为 0 ~ 1 之间的浮点数。
* CC_Texture0 没有显示定义，然后在引擎里面会自动在你的 shader 代码头部附加一些变量定义，并在渲染的时候传递参数进来。CC_Texture0 代表第一个纹理，通常我们的精灵只有一个纹理。
* texture2D 为 GLSL 内建函数，取纹理坐标对应的颜色值。

其它自动附加到片段着色器的参数有：

* `uniform mat4 CC_PMatrix;` 透视矩阵
* `uniform mat4 CC_MVMatrix;` 模型视图矩阵
* `uniform mat4 CC_MVPMatrix;` 透视模型视图矩阵
* `uniform mat3 CC_NormalMatrix;` 法线矩阵
* `uniform vec4 CC_Time;`
	* CC_Time[0], 游戏启动以来的时间 / 10.0。
	* CC_Time[1], 游戏启动以来的时间。
	* CC_Time[2], 游戏启动以来的时间 * 2。
	* CC_Time[3], 游戏启动以来的时间 * 4。
* `uniform vec4 CC_SinTime;`
	* CC_SinTime[0], 游戏启动以来的时间 / 8.0。
	* CC_SinTime[1], 游戏启动以来的时间 / 4.0。
	* CC_SinTime[2], 游戏启动以来的时间 / 2.0。
	* CC_SinTime[3], sinf(游戏启动以来的时间)。
* `uniform vec4 CC_CosTime;`
	* CC_CosTime[0], 游戏启动以来的时间 / 8.0。
	* CC_CosTime[1], 游戏启动以来的时间 / 4.0。
	* CC_CosTime[2], 游戏启动以来的时间 / 2.0。
	* CC_CosTime[3], cosf(游戏启动以来的时间)。
* `uniform vec4 CC_Random01;`
	* CC_Random01[0], 0 ~ 1 之间的随机数。
	* CC_Random01[1], 0 ~ 1 之间的随机数。
	* CC_Random01[2], 0 ~ 1 之间的随机数。
	* CC_Random01[3], 0 ~ 1 之间的随机数。

## Lua中的加载代码

`MainScene:ctor()`实例代码如下：

```
local sprite = display.newSprite("2.png")
	:center()
	:addTo(self)

-- use shader cache of cocos2x cpp engine
local prog = cc.GLProgramCache:getInstance():getGLProgram("myShader")
if not prog then
	prog = cc.GLProgram:createWithFilenames("test.vert", "test.frag")
	cc.GLProgramCache:getInstance():addGLProgram(prog, "myShader")
end

local progStat = cc.GLProgramState:getOrCreateWithGLProgramName("myShader")
progStat:setUniformVec2("v_mousePosition", cc.p(0.5, 0.5))
-- 部分预乘的图片，需要gl.SRC_ALPHA,才能让片段着色器中修改gl_FragColor.a值生效
sprite:setBlendFunc(gl.SRC_ALPHA, gl.ONE_MINUS_SRC_ALPHA)
sprite:setGLProgramState(progStat)
```

片段着色器参数的传递，使用`progStat:setUniformVec2`来实现。不同的参数类型，有不同的函数对应。最后`sprite:setGLProgramState`让精灵使用我们自定义的着色器来渲染。

> 片段着色器的参数，需要以uniform定义，才能从CPU直接传递过来，而其它类型的参数是用顶点着色器传递过来的。

支持的 uniform 参数传递函数如下：

* setUniformTexture
* setUniformMat4
* setUniformFloat
* setUniformVec3
* setUniformInt
* setUniformVec4
* setUniformVec2