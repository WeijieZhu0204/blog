# 字符编码



## 字节和位

位（bit）和字节（byte）都是计算机的存储单位。位是最小的存储单位，每一位存储一个二进制数（0和1），一个字节由八位组成。

因此，一个字节可以表示成：0000 1101



## 进制

整数各种进制的字面量如下：

- 十进制数，没有前缀
- 二进制数，前缀是0b
- 八进制数，前缀是0o
- 十六进制数，前缀是0x



### 进制之间的转换

譬如一个十进制的数值：17 ，经过转换如下：

二进制：0b10001 $$(1 * 2^0 + 1 * 2^4 = 17)$$

八进制：0o21 $$(2 * 8^1 + 1 * 8^0 = 17)$$

十六进制： 0x11 $$(1 * 16^1 + 1 * 16^0 = 17)$$



### 进制和存储单位的关系

二进制的一位数值对应存储单位的1位（bit），也就是 0b10001 占用了5位（bit）存储空间。

八进制的一位数值对应存储单位的3位（bit），也就是 0o21 占用了6位（bit）存储空间。

十六进制的一位数值对应存储单位的4位（bit），也就是 0x11 占用了8位（bit），1字节（byte）存储空间。

任何进制都只是不同的表达方式，最终都会转换成二进制的形式存储在内存空间当中。



## Unicode

Unicode字符集定义了每一个字符和码点的映射。码点采用 `U+` 前缀后接4位十六进制数值表示。

如： Unicode字符 a 对应的码点是 U+0061。



### Unicode的几个关键字概念

* 字符（Character）：人类可认知的字符实体，如字符 a 。
* 码点（Code Point）：Unicode字符集每一个字符都有相应唯一的十六进制码点，如字符 a 对应的码点是 U+0061 。
* 基本平面（BMP）：Unicode码点处于 U+0000 - U+FFFF 之间的字符集合。
* 辅助平面（SMP）：Unicode码点处于 U+10000 - U+10FFFF 之间的字符集合。



### 什么是编码？

同一个Unicode字符对应的码点，经过不同的编码过程，会得出不同的编码结果，一般使用十六进制表示。

如：字符 Ω

Unicode码点：U+03A9

UTF-8编码：0xCEA9

UTF-16编码：0xFEFF03A9

UTF-32编码：0x0000FEFF000003A9

经过不同的编码方式，得出的编码结果是不一样的。UTF-8编码占用的存储空间最小，因此其被广泛使用。同理，当我们遇到一个二进制文本的时候，需要用正确的编码方式进行解读，否则就会出现乱码。



### UTF-8、UTF-16、UTF-32、UCS-2编码

我们无需去深究具体的编码过程，只需了解其主要区别。

1. UTF-8：使用一至四个字节变长的方式表示BMP、SMP平面字符，减少存储占用的空间。

2. UTF-16：使用两个字节来表示BMP平面字符，四个字节表示SMP平面字符，空间利用率介于前者和后者之间。

   如果是BMP平面，直接把码点转为对应的两字节十六进制形式即可，UCS-2编码也是如此。如：

   ```javascript
   // 字符 a 的码点是 U+0061
   ```

   



3. UTF-32：使用四个字节表示BMP、SMP平面字符，占用空间通常是其他编码的几倍。

4. UCS-2：使用两个字节来表示BMP平面字符，无法表示SMP平面字符。









## JavaScript & Unicode

由于JavaScript在诞生的时候还没有UTF-8、UTF-16编码，因此一开始使用的就是UCS-2编码，而这个编码只支持两个字节，因此不能用来表示SMP平面的字符。当遇到超过两字节的编码时，JavaScript会使用UTF-16进行解析，其实是对于SMP平面的一个增强。



### JavaScript中关于字符编码的坑

1. SMP平面字符（超过2字节）如何在JavaScript中表示。
2. 





### ES6新增的支持

1. 








