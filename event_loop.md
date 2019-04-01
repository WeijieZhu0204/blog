# Event Loop

## outline

* 在 JavaScript Engine 中（以 V8 为例），只是实现了 ECMAScript 标准，而并不关心什么 Event Loop。也就是说 **Event Loop 是属于 JavaScript Runtime** 的，是由宿主环境提供的（比如浏览器）。所以千万不要搞错了，这也是前面介绍 JavaScript Engine 和 Runtime 的原因。



