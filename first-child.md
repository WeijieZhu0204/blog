# 关于first-child的误解

为什么设置的first-child、last-child没有起作用？

下面我们延伸到nth-child、first-of-type、last-of-type、nth-of-type作统一探讨。



## first-child、last-child命中需要满足的条件

1. 被设置first-child的标签必须能找到父标签。

   从上面的渲染结果可以看出，被设置first-child的标签必须能找到父标签，并且非\<body\>标签才能生效。

2. 系父节点的第一个子节点。

   p:first-child是指什么？选择p标签的父节点下的第一个子节点，并且该节点为p标签的元素。

   很多人误以为是选择p标签的父节点下的第一个p标签节点。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>first child</title>
  <style>
    div,p{
      color:black;
    }
    div:first-child{
      color:red;
    }
    p:first-child{
      color:red;
    }
  </style>
</head>
<body>
  <div class="test1">
    test1:
    <p>p1</p>
    <p>p2</p>
    <p>p3</p>
  </div>
  <div class="test2">
    test2:
    <div>div1</div>
    <p>p4</p>
    <p>p5</p>
  </div>
</body>
</html>
```

```
test1:
p1 // red , hit p:first-child 
p2
p3

test2:
div1 // red , hit div:first-child
p4 // black , do not hit p:first-child 
p5
```



特殊情况：nth-child、first-of-type、last-of-type、nth-of-type等属性即使父节点是\<body\>也可以生效。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>first child</title>
  <style>
    div,p{
      color:black;
    }
    div:first-of-type{
      color:red;
    }
  </style>
</head>
<body>
  <div>div1</div>
  <div>div2</div>
</body>
</html>
```

```
div1 // red , hit div:first-of-type
div2
```



## first-of-type

实际上first-child在某些使用场景下的表现并不尽人意，譬如我们只是想找到首次出现的p标签，但是因为p标签的前面出现了一个div标签，就无法命中了，因此我们有了另外一个属性：first-of-type。

同类型的还有：last-of-type、nth-of-type等。

p:first-of-type 选择p标签的父节点下，首次出现的p标签节点。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>first child</title>
  <style>
    div,p{
      color:black;
    }
    p:first-of-type{
      color:red;
    }
  </style>
</head>
<body>
  <div>div1</div>
  <p>p1</p>
  <p>p2</p>
</body>
</html>
```

```
div1
p1 // red , hit p:first-of-type
p2
```



Done.

