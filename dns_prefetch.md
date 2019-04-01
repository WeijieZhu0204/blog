# DNS Prefetch 原理解析



DNS prefetch即DNS预解析，通过向DNS服务器提前获取域名的实际指向，可以有效减少浏览器在发送请求时的DNS解析时间。

DNS解析是一个多级处理的过程，通过向不同层级的DNS服务器查询，最终得到域名指向。当然不同层级都可实现各自的缓存，我们电脑本地就有一层DNS缓存，一般缓存有50-200个域名解析。在这里不深入解释DNS解析的过程。



## 如何设置DNS Prefetch

1、浏览器自动处理

浏览器在加载一个html页面的时候，会自动获取当前页面所有a标签href属性中的域名，并且执行DNS预解析。a标签以外的标签不做处理。

需要注意的是，当浏览器遇到https页面，处于安全性考虑，不会自动进行DNS预解析。需要手动加上一个meta开关。

```html
<meta http-equiv="x-dns-prefetch-control" content="on">	
```



2、手动配置

在页面顶部加入如下标签即可。

```html
<link rel="dns-prefetch" href="//host_name_to_prefetch.com">
```



## Chromium实现

DNS解析可能耗费大量的时间，在某些系统上(如Windows)甚至不支持异步的DNS解析。为了防止DNS预解析阻塞页面渲染影响用户体验，Chromium内核没有使用浏览器的网络堆栈实现DNS prefetch。而是启动了8个异步的worker线程专门负责DNS预解析。**因此DNS预解析与首屏加载没有直接关系，既不会加速首屏渲染，也不会阻塞首屏渲染**



## 应用场景

参考上面的结论，我们知道声明在html中的JavaScript、CSS、图片等静态资源的加载，并不会直接受益于DNS prefetch。

那么我们什么时候会用上DNS预解析呢？

1、在整个站点的入口声明DNS预解析。

通过在站点入口声明了后续其他子页面和子站点可能会访问的域名，浏览器提前进行了DNS预解析。这样用户在访问其他子站点的时候将会间接受益于入口的预解析，降低了DNS lookup所消耗的时间。

2、根据实际情况配置当前页面的DNS预解析。

声明后续操作可能会用到的域名，如发送的异步请求、动态加载图片等等。

3、并不是DNS prefetch设置的越多越好，虽然是异步线程，实际上还是占用设备的带宽，造成资源竞争。



## Referer

[The Chromium Projects - DNS Prefetching](https://www.chromium.org/developers/design-documents/dns-prefetching)



Done.