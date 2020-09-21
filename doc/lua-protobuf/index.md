# lua-protobuf 用法说明

> 适用于 Cocos2d-Lua-Community 4.0.1 及以上版本。

在3.7中集成了protoc-gen-lua和sproto以实现Lua中支持protobuf，但是这两个库都已停止维护，并且不支持pb3的格式。而开源社区一款新的库[lua-protobuf](https://github.com/starwing/lua-protobuf)支持pb3数据格式，并且维护更新频繁，在4.0.1中集成以更好的支持protobuf。

## proto3 协议文件示例

addressbook.proto 内容如下：


```
// [START declaration]
syntax = "proto3";
package tutorial;

// [END declaration]

// [START java_declaration]
option java_package = "com.example.tutorial";
option java_outer_classname = "AddressBookProtos";
// [END java_declaration]

// [START csharp_declaration]
option csharp_namespace = "Google.Protobuf.Examples.AddressBook";
// [END csharp_declaration]

// [START messages]
message Person {
  string name = 1;
  int32 id = 2;  // Unique ID number for this person.
  string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    string number = 1;
    PhoneType type = 2;
  }

  repeated PhoneNumber phones = 4;
}

// Our address book file is just one of these.
message AddressBook {
  repeated Person people = 1;
}
// [END messages]
```

## 生成xxx.pb文件

下载[Google protocol-buffers](https://github.com/golang/protobuf) 3.x最新发布版本，使用其中的命令行工具转化.proto为.pb。把生成的xxx.pb放到res资源目录下。

3.11.4下载地址参考：

* https://github.com/protocolbuffers/protobuf/releases/download/v3.11.4/protoc-3.11.4-win32.zip
* https://github.com/protocolbuffers/protobuf/releases/download/v3.11.4/protoc-3.11.4-win64.zip
* https://github.com/protocolbuffers/protobuf/releases/download/v3.11.4/protoc-3.11.4-linux-x86_64.zip
* https://github.com/protocolbuffers/protobuf/releases/download/v3.11.4/protoc-3.11.4-osx-x86_64.zip

转换命令行：

```
protoc -o addressbook.pb ./addressbook.proto  --proto_path ./
```

## Lua测试代码

参考引擎中的测试案例文件`Cocos2d-Lua-Community/tests/src/app/views/Test_LuaProtobuf.lua`。

```
local pb = require "luapb" -- different name from pbc

function TestCase:ctor()
	self.super.ctor(self)

	self:setNodeEventEnabled(true)
	-- tips
	local label = display.newTTFLabel({
		text = "Encode & Decode addressbook.pb",
		size = 25,
		color = cc.c3b(255, 255, 255),
	})
	label:align(display.CENTER, display.cx, display.cy + 200)
	self:addChild(label)

	-- test code
	assert(pb.loadfile(cc.FileUtils:getInstance():fullPathForFilename("addressbook.pb")))

	local person = {
		name = "Alice",
		id = 12345,
		phones = {
			{ number = "1301234567" },
			{ number = "87654321", type = "WORK" },
		}
	}

	-- 序列化成二进制数据
	local data = assert(pb.encode("tutorial.Person", person))
	print("pb.encode data length:" .. #data)
	-- 从二进制数据解析出实际消息
	local msg = assert(pb.decode("tutorial.Person", data))
	-- 打印消息内容（使用了serpent开源库）
	dump(msg, "pb.decode")
end
```