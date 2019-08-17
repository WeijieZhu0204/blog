# 跨端融合开发



#### 主流框架和方案

- React Native
- Weex
- Hippy
- Flutter（孕育中）
- Taro（偏向于翻译转换的角色，适配不同的跨端框架）
- Hybrid开发（由于webview性能问题已经逐渐淘汰）（PhoneGap、AppCan）



#### 基本架构

按照层级抽象如下：

1. 业务代码
2. Javascript Framework
3. Javascript Runtime
4. Javascript Core
5. IOS、Android



- 业务代码：

  即我们写的React Native、Weex、Hippy业务逻辑代码。业务代码一般会经过webpack loader的处理。

- Javascript Framework：

  Framework层主要针对Weex和Hippy框架，Weex支持Vue、Rax，Hippy支持React、Vue。实际上框架需要针对这些不同的前端UI框架做特殊处理，如：创建一个元素的时候，在web环境调用`document.createElement`，而在客户端环境则调用下层Runtime提供的API接口。

- Javascript Runtime：

  Runtime主要对外暴露了一些代理接口，如节点的增删改查、网络请求、Native层功能等。

- Javascript Core：

  提供Javascript的执行环境，如IOS环境中使用JavaScriptCore，Android环境中使用Chrome V8、QQ浏览器X5内核。

- IOS、Android

  最下层就是客户端Native层，提供各种原生的能力和UI界面的渲染。Native层通过JsBridge方案与Javascript通信。



#### 主流框架对比

| 框架         | 优点                                                         | 缺点                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| React Native | 1.社区成熟，轮子多                                           | 1. 存在政策与协议风险<br />2.打包JSBundle较大，没有分包机制<br />3.部分组件如List组件性能较差<br />4.部分事件发送频繁导致性能损失、例如列表滚动事件、手势事件等<br />5.不支持web端 |
| Weex         | 1.三端统一<br />2.支持拆包，轻量化                           | 1.第三方组件和轮子较少，业务场景偏向于营销内容               |
| Hippy        | 1.高性能可复用滚动列表<br />2.Hibby Buffer二进制传输协议，编解码性能提升<br /> |                                                              |

