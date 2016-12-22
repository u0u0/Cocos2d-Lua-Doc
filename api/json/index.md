# json 模块

## 接口

`json.encode(var)`

将 table 数据编码为 JSON 字符串。

| 参数名 | 类型 |说明|
|----|---|----|
| var | table | 待转换的table |

返回值：string

-----------------------------

`json.decode(text)`

将 JSON 字符串解码为 table 对象。

| 参数名 | 类型 |说明|
|----|---|----|
| text | string | Json 字符串 |

返回值：table