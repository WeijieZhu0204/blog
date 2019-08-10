# 前端性能优化









## Outline

- [使用flexbox布局模型，性能优于普通的css布局模型。](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing?hl=zh-cn#flexbox)
- [避免强制同步布局](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing?hl=en#flexbox)



## 渲染性能

* 浏览器的渲染流程

  JavaScript->Style->Layout->Paint->Composite

* layout属性

  改变了元素的几何属性，会重新触发Layout布局，如width、height、margin等。

* paint only 属性

  改变了元素的paint only属性，则浏览器会跳过Layout布局，执行Paint过程，如backgroundColor、color等。

* 查看[css trigger](https://csstriggers.com/)可以了解不同的css属性如何触发浏览器渲染流程。

* Render Tree

* 优化JS执行

  - requestanimationframe相对于setInterval的优点在于，保证每次JS执行的时机都在帧开头。

* 样式优化

  - 降低选择器的复杂性，如nth-of-type需要比普通class更多的计算
  - 





## Referer

- [页面生命周期：DOMContentLoaded, load, beforeunload, unload 解析](https://github.com/fi3ework/blog/issues/3)
- 





* 关键渲染路径

  







