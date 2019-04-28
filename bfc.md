# Block Formatting Context



## 块格式化上下文

为盒子创造了一个与外部隔离的容器，使其内部不受外部影响，同理也不会对外部盒子造成影响。



## BFC布局规则

* 垂直方向上，属于同一个BFC容器的两个盒子的margin会发生重叠。
* BFC容器不会与浮动盒子重叠。
* BFC容器里面的盒子不会影响到外部盒子的布局，反之亦然。
* 计算BFC容器高度的时候，浮动元素的高度也会计算其中。



## 如何触发BFC

* 根元素或包含根元素的元素
* 浮动元素：float不为none
* 绝对定位元素：position为absolute或者fixed
* 行内块元素：display inline-block
* overflow不为visible的元素
* 弹性元素、网格元素：display flex、grid
* 等等



## 应用场景

* 解决被浮动元素覆盖
* 解决由于浮动造成的高度塌陷
* 解决垂直方向的margin重叠和margin穿透

