

# Front-End Interview



## CSS

* 如何清除**由于代码换行**导致两元素中间的间距？

* **border-box**、**content-box**有什么区别？

* **position**有哪几个值，分别相对于什么定位？

* **BFC**有哪些特性，可以解决什么问题？

* **clear**的作用？

  

## JavaScript

* **\_\_proto\_\_**、**prototype**、**constructor**的关系?

* **eventloop**：输出正确的log日志。

  ```javascript
  // 普通
  console.log("start");
  
  Promise.resolve().then(function() {
    console.log("p1");
  });
  
  setTimeout(function() {
    console.log("t1");
  }, 0);
  
  Promise.resolve()
    .then(function() {
      console.log("p2");
    })
    .then(function() {
      console.log("p3");
  });
  
  console.log("end");
  ```
  
  ```html
  // 进阶
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <meta http-equiv="X-UA-Compatible" content="ie=edge" />
      <title>Document</title>
    </head>
    <body>
      <div class="parent">
        <div class="child">click me</div>
      </div>
  
      <script>
        const parent = document.querySelector(".parent");
        const child = document.querySelector(".child");
  
        parent.addEventListener("click", function parentClick() {
          console.log("parent:c");
          setTimeout(function() {
            console.log("parent:t");
          }, 0);
          Promise.resolve()
            .then(function() {
              console.log("parent:p1");
            })
            .then(function() {
              console.log("parent:p2");
            });
        });
  
        child.addEventListener("click", function childClick() {
          console.log("child:c");
          setTimeout(function() {
            console.log("child:t");
          }, 0);
          Promise.resolve()
            .then(function() {
              console.log("child:p1");
            })
            .then(function() {
              console.log("child:p2");
            });
        });
      </script>
    </body>
  </html>
  ```
  



## 浏览器

* 简述一下什么是同源策略，同源策略可以规避什么安全问题？
* 跨域有哪些解决方案？
* 跨域请求如何带上cookie？



## HTTP

* 简述协商缓存和强缓存机制。
* **cache-control**设置no-store、no-cache的作用。



## Node.js

* 如何遍历目录下的所有文件？



## Webpack

* 如何构建一个多页应用。
* webpack4 **splitChunks** 相关概念： chunks、cacheGroups



## Vue

* 下面的修改方式是否响应式？

  ```javascript
  data: {
  	arr:[{num:1}]
  }
  
  this.data.arr[0].num = 2;
  this.data.arr[0] = {num:2};
  ```

  

## DataStructure

* **数组和链表**在内存上的区别。
* **数组**的插入操作时间复杂度是多少？如何优化成O(1)。
* **二叉树**的深度遍历和广度遍历。

