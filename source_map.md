# Source Map在浏览器内是如何运作的？

### source map的原理

建议先通过这篇文章了解一下source map的原理。[source map 的原理探究](https://github.com/wayou/wayou.github.io/issues/9)



### 如何生成source map文件

我们在本地新建如下的工作目录：

```
├── dist
│   ├── index.min.js
│   └── index.min.js.map
├── index.html
└── src
    └── index.js
```



```html
// index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Source Map</title>
  </head>
  <body>
    <script src="dist/index.min.js"></script>
  </body>
</html>

```



```javascript
// src/index.js

function say(what) {
  console.log(what);
}

var what = "hello";
say(what);
```



执行uglify-js的cli命令，生成source map：

```bash
> npm i uglify-js@3 -g

> uglifyjs src/index.js -o dist/index.min.js -cm --source-map "root='/',url='index.min.js.map'"
```
src/index.js：源文件

-o： 输入的目标地址

-c： 启用压缩 -m 启用混淆

--source-map ： 输出source-map 

> 其中 root 表示源文件的根路径
>
> url 表示将在 index.min.js 文件中加入source map的url地址



此时将输出两个文件：

```javascript
// dist/index.min.js 压缩后的代码

function say(a){console.log(a)}var what="hello";say(what);
//# sourceMappingURL=index.min.js.map
```



```json
// dist/index.min.js.map source map文件

{
  "version": 3,
  "sources": ["src/index.js"],
  "names": ["say", "what", "console", "log"],
  "mappings": "AAAA,SAASA,IAAIC,GACXC,QAAQC,IAAIF,GAGd,IAAIA,KAAO,QACXD,IAAIC",
  "sourceRoot": "/"
}
```

- sources：源文件的路径。
- names：记录完整的连续字符名。
- mappings：根据 Base64 VLQ 编码记录源文件内容和压缩文件内容的映射关系。
- sourceRoot：源代码文件的根路径。



### 浏览器如何加载source map

在当前工作目录启动一个静态服务：

```bash
> npm i anywhere -g

> anywhere
Running at http://localhost:8000/
Also running at https://localhost:8001/
```



通过浏览器调试工具的Sources面板可以看到如下目录：

```
├── dist
│   ├── index.min.js
├── src
│   ├── index.js
└── index.html
```



**浏览器解析过程**：

- 解析 index.html
- 加载 http://localhost:8000/dist/index.min.js
- 加载 http://localhost:8000/dist/index.min.js.map。sourceMappingURL=index.min.js.map 是相对路径，相对于当前index.min.js。
- 由于 names 字段只记录了连续的字符名， mappings 字段只记录了源文件内容和压缩文件内容的映射关系，只靠source map文件是无法还原源代码内容的。此时浏览器会根据 sources 和 sourceRoot 字段尝试加载源代码文件 ：http://localhost:8000/src/index.js 。（如果不设置sourceRoot为根路径 "/"，则相对于index.min.js路径加载源文件 ：http://localhost:8000/dist/src/index.js ，显然是错误的。）
- 此时浏览器可以根据 mappings 去映射正确的源文件位置了。



### includeSources

通过设置 includeSources 参数，源文件内容将以 sourcesContent 字段添加进 source map 文件当中。浏览器不需要额外加载源文件内容，但是 source map 的体积也将变得更加庞大。



```bash
> uglifyjs src/index.js -o dist/index.min.js -cm --source-map "root='/',url='index.min.js.map',includeSources"
```



```json
// dist/index.min.js.map source map文件

{
  "version": 3,
  "sources": ["src/index.js"],
  "names": ["say", "what", "console", "log"],
  "mappings": "AAAA,SAASA,IAAIC,GACXC,QAAQC,IAAIF,GAGd,IAAIA,KAAO,QACXD,IAAIC",
  "sourceRoot": "/",
  "sourcesContent": [
    "function say(what) {\n  console.log(what);\n}\n\nvar what = \"hello\";\nsay(what);\n"
  ]
}
```



done.