# 事件循环 (Event loops)

相信研究过event loops的大家，都看过以下两个相关介绍：

* [菲利普·罗伯茨：到底什么是Event Loop呢？ | 欧洲 JSConf 2014](https://www.youtube.com/watch?v=8aGhZQkoFbQ&list=LLhYeD_539GqLI8lmMIY011g&index=2&t=0s)
* [Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/) (感觉这个人的英文写的好绕···)



下面将会基于HTML标准探讨一下event loops。

[https://html.spec.whatwg.org/multipage/webappapis.html#event-loops](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)



### 定义

要协调事件、用户交互、脚本、渲染、网络等，用户代理必须使用这个章节中描述的事件循环。每个代理都有一个关联的事件循环。

> 用户代理可以理解为浏览器。



**每个event loop有一个或多个task queues**。task queue是一个有序的任务队列，任务队列负责以下工作：

- 派发事件：在特定的EventTarget对象中发送一个Event事件

- 解析HTML

- 回调：执行回调函数

- 使用资源：使用非阻塞的方式请求资源。

- 响应DOM操作：为了响应DOM操作而触发的任务，如插入一个元素到文档中。

  

每个task都来源于一个特定的task source。所有自于特定的task source和归属于一个特定event loop的task，每次都必须插入到同一个task queue中，但是不同task source的task可以允许被插入到不同的task queue中。简而言之，就是要保证同类型的任务会被放到同一个队列当中，这样才能确保任务被按顺序执行。

> 例如，一个用户代理可以有一个专门处理鼠标和键盘事件的task queue（用户交互任务源），另外一个task queue负责剩余的任务。这样代理程序为了优先保证用户响应，可以分配更多系统资源处理交互类型的task queue，并且保证相同task source来源的任务执行顺序。



**每个event loop只有一个microtask queue**。



## task && microtask

html标准中只说明了以下几种普通类型的task source：

* DOM操作类型
* 用户交互类型
* 网络类型
* 访问历史记录类型 (操作history api等)



那是不是可以理解为非以上几种的都归类到microtask，总结了网上的资料，发现以下几种microtask source比较可靠：

* Promise
* MutationObserver



### event loop处理模型

一个事件循环只要存在，就必须连续的执行以下步骤（简化）：

1、代理程序可能选择任何一个task queue。找到task queue的首个task。**如果这里没有任务可以执行，则跳到下面的microtasks步骤**。

2、执行上一步的task

3、从task queue中移除上一步的task

4、执行microtasks的检查。如果microtask checkpoint标记为false，则执行以下步骤：

​	1、设置microtask checkpoint标记为true。

​	2、当microtask queue不为空（**循环执行直到microtask queue为空**）：

​		1、找到microtask queue的首个microtask

​		2、执行上一步的microtask

​		3、从microtask queue中移除上一步的microtask

​	3、设置microtask checkpoint标记为false。

5、更新页面渲染

6、本轮event loop结束



## 实例

先来看一个常规的例子：

```javascript
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


// console

// start
// end
// p1
// p2
// p3
// t1
```

毫无疑问对吧。：）



来一个不一样的：

```html
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


// console

// child:c
// child:p1
// child:p2
// parent:c
// parent:p1
// parent:p2
// child:t
// parent:t
```

你会发现click回调会优先于所有settimeout回调执行，期间还夹杂着一些microtask被调用，典型的event loop循环模型。因此，根据上面的html标准可以推断出，**这里存在另外一个task queue，专门用来处理用户交互相关的task**，该队列的调度优先级要高于普通的task queue。



再来一个进阶：

```html
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
      Promise.resolve().then(function() {
        console.log("p1");
      });

      setTimeout(function t1() {
        console.log("t1");
      }, 0);

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
      // 通过代码同步触发点击事件
      child.click();

      setTimeout(function() {
        console.log("t2");
      }, 0);

      Promise.resolve().then(function() {
        console.log("p2");
      });
    </script>
  </body>
</html>

// console

// child:c
// parent:c
// p1
// child:p1
// parent:p1
// p2
// child:p2
// parent:p2
// t1
// child:t
// parent:t
// t2
```

上面这个留给大家思考，欢迎讨论。