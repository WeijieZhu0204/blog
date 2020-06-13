## ArrayBuffer

浏览器提供了ArrayBuffer api，让你可以申请一片连续的内存空间。如：

```javascript
const buf = new ArrayBuffer(1024);
```

ArrayBuffer申请的最小单位为字节，上面等于申请了一片1KB大小的内存，且这片内存空间需要通过`TypedArray`对象或者`DataView`对象来操作。



## TypedArray





## DataView

