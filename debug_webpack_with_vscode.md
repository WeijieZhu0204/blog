# 如何断点调试webpack的构建流程



最近在阅读element-ui源码，其中有一段基于webpack和markdown-it的构建流程晦涩难懂，于是想在vscode里面断点调试一下。



（断点调试图）



### 首先我们得调整一下npm script：

```javascript
// package.json

"dev": "npm run bootstrap && npm run build:file && cross-env NODE_ENV=development webpack-dev-server --config build/webpack.demo.js & node build/bin/template.js",

```

因为我们需要传入一些node运行参数，需要将其中的webpack-dev-server换一种方式执行

```javascript
// package.json

"dev": "npm run bootstrap && npm run build:file && cross-env NODE_ENV=development node --inspect-brk=9229 ./node_modules/webpack-dev-server/bin/webpack-dev-server.js --config build/webpack.demo.js & node build/bin/template.js",

```

`--inspect-brk=9229` 的意思是使用9229端口启动调试代理模式，并且在调试程序attach进来之前，先停止执行代码。



### 启动vscode的自动attach模式

在vscode中执行快捷键`cmd + shift + p`，调出命令输入窗口，输入`auto attach`，此时窗口左下角会显示**自动附加：开**。



### 断点

先在预定位置打好断点，执行`npm run dev`

```bash
➜  element git:(dev) ✗ npm run dev

> @weijiezhu/element-ui@2.4.16 dev /Users/joneszhu/dev/github/element
> npm run bootstrap && npm run build:file && cross-env NODE_ENV=development node --inspect-brk=9229 ./node_modules/webpack-dev-server/bin/webpack-dev-server.js --config build/webpack.demo.js & node build/bin/template.js

> Debugger listening on ws://127.0.0.1:9229/531559c7-163c-4cec-931c-721b2e03368d
For help, see: https://nodejs.org/en/docs/inspector
Debugger attached.
```

此时，调试程序已经attach到9229端口了，webpack程序开始执行，并且停留在断点位置。



### Referer

- [nodejs debugging](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [vscode nodejs-debugging](https://code.visualstudio.com/docs/nodejs/nodejs-debugging)



done.