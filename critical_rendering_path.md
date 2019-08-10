# 关键渲染路径



关键渲染路径：即从接收HTML、CSS和JavaScript字节到转变成渲染的像素的过程。



## 构建对象模型

浏览器渲染页面需要先构建DOM和CSSOM树。因此我们需要尽快将HTML和CSS提供给浏览器。



### 文档对象模型(DOM)

> Referer
>
> * [12.2 Parsing HTML documents](<https://html.spec.whatwg.org/multipage/parsing.html#parsing>)

官方的DOM解析模型图：

<img src="https://raw.githubusercontent.com/WeijieZhu0204/Blog/master/assets/images/parsing-model-overview.svg?sanitize=true" width="400px">

1、网络加载：下载HTML文件，得到HTML字节流。

2、字节流解码：把字节流转换成对应编码的字符码点，如UTF-8编码的字符。

3、输入流预处理：预处理换行符和document.write插入等。

4、令牌化：通过一组状态机把预处理得到的字符串转换成标准的HTML标签。

5、构造DOM树：根据各种复杂的规则生成最终的DOM对象树。



### CSS对象模型(CSSOM)

生成CSSOM的流程与上面DOM相似，这里就不做赘述。

值得注意的是，在生成DOM树的过程中，如果发现有外部CSS依赖，浏览器会立即发出该资源的请求。



### 观察Performance

```html
//index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    <link rel="stylesheet" href="./style.css" />
  </head>
  <body>
    <div class="test">hello world</div>
  </body>
</html>
```

**渲染流程：**

1、Loading HTML

2、Parse HTML (生成DOM过程)

​	1、Request CSS

​	2、Recalculate Style

3、Parse Stylesheet (生成CSSOM过程)

4、Recalculate Style

5、Layout

6、Update Layer Tree

7、Paint

8、Composite Layers