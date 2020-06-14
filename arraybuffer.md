## ArrayBuffer

浏览器提供了ArrayBuffer api，让你可以申请一片连续的内存空间。如：

```javascript
const buf = new ArrayBuffer(1024);
```

ArrayBuffer申请的最小单位为字节，上面等于申请了一片1KB大小的内存，且这片内存空间需要通过`TypedArray`视图或者`DataView`视图来操作。



## TypedArray

`Int8Array`、`Uint8Array`、`Int16Array`...

TypedArray只提供了9种固定的视图格式，如果需要从任意字节开始解读ArrayBuffer，必须使用DataView视图。

与普通数组相比，TypedArray数组可以直接操作二进制数据，不需要数据类型转换，因此效率更高。



## DataView

DataView视图可设定`字节序`，可以从ArrayBuffer的`任意字节`开始读取和写入。

**字节序**

DataView可设定`字节序`，8位单字节的视图不存在字节序问题，如`Int8Array`。

一般计算机本地采用`小端字节序`，即相对重要的字节排在后面的内存地址。

而网络传输一般采用`大端字节序`，即相对重要的字节排在前面的内存地址，符合人的阅读习惯。

如：

给`Int16Array`首字段赋值`0x0001`，在小端字节序的计算机中，被存储为`0x0100`。当你使用`Int8Array`视图读取这段内存，会读出`0x01`和`0x00`。