# Quick-Cocos2dx-Community 在发布时加密资源文件

> 适用于 Quick-Cocos2dx-Community 3.7.x 和 Cocos2d-Lua-Community 4.x

在3.7以上版本中，game32.zip将视为资源，使用同一套加密方式。

## 示例

```
$/xxx/Quick-Cocos2dx-Community/quick/bin/EncodeRes.py -p /Users/u0u0/Desktop/testProject -s ASig -k /Users/u0u0/Desktop/key.png
```

新的加密脚本以文件作为key，支持二进制的key。事实上xxtea加密的key固定位16个字节128bit，也就是只取文件的前16字节作为key来加密。

例中，加密 res 资源文件下的二进制数据（参考py脚本中的过滤），原未加密的文件会备份到res_bk文件夹。

在AppDelegate.cpp中加入解码信息。

```
FileUtils::getInstance()->setFileDataDecoder(decoder);
```

3.7使用外部解码函数嵌入到引擎的file移植层的方式来提供自定义解码方案，也就是你可以用自己的加密方案和解密方案来替换引擎的默认方案，灵活性更高。

AppDelegate中提供默认的解码函数。实现如下：

```
static void decoder(Data &data)
{
    unsigned char sign[] = "ASig";
    unsigned char key[16] = {
        0x89, 0x50, 0x4E, 0x47,
        0x0D, 0x0A, 0x1A, 0x0A,
        0x00, 0x00, 0x00, 0x0D,
        0x49, 0x48, 0x44, 0x52
    };

    // decrypt XXTEA
    if (!data.isNull()) {
        bool isEncoder = false;
        unsigned char *buf = data.getBytes();
        ssize_t size = data.getSize();
        ssize_t len = strlen((char *)sign);
        if (size <= len) {
            return;
        }

        for (int i = 0; i < len; ++i) {
            isEncoder = buf[i] == sign[i];
            if (!isEncoder) {
                break;
            }
        }

        if (isEncoder) {
            xxtea_long newLen = 0;
            unsigned char* buffer = xxtea_decrypt(buf + len,
                                                  (xxtea_long)(size - len),
                                                  (unsigned char*)key,
                                                  (xxtea_long)sizeof(key),
                                                  &newLen);
            data.clear();
            data.fastSet(buffer, newLen);
        }
    }
}
```