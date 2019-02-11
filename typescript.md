# TypeScript

## Module Resolution

### Classic

相对路径模块：在文件 `/root/src/A.ts` 内引入 `import { b } from "./moduleB"`，只会在相对路径上寻找。

1. `/root/src/moduleB.ts`
2. `/root/src/moduleB.d.ts`



非相对路径模块：在文件 `/root/src/A.ts` 内引入 `import { b } from "moduleB"`，会从当前目录一直往上寻找。

1. `/root/src/moduleB.ts`
2. `/root/src/moduleB.d.ts`
3. `/root/moduleB.ts`
4. `/root/moduleB.d.ts`
5. `/moduleB.ts`
6. `/moduleB.d.ts`



### Node

相对路径模块：在文件 `/root/src/A.js` 内引入 `import { b } from "./moduleB"`，只会在相对路径上寻找。

1. `/root/src/moduleB.js`
2. `/root/src/moduleB/package.json`(if it specifies a `"main"` property)
3. `/root/src/moduleB/index.js`



非相对路径模块：在文件 `/root/src/A.js` 内引入 `import { b } from "moduleB"`，会从当前目录一直往上寻找node_modules。

1. `/root/src/node_modules/moduleB.js`
2. `/root/src/node_modules/moduleB/package.json` (if it specifies a `"main"` property)
3. `/root/src/node_modules/moduleB/index.js` 
4. ...lookup upward
5. `/node_modules/moduleB.js`
6. `/node_modules/moduleB/package.json` (if it specifies a `"main"` property)
7. `/node_modules/moduleB/index.js`



## Declaration

### lib.d.ts

This file is automatically included in the compilation process of TypeScript.

### 外部模块

如果想使用JavaScript编写的模块，可以尝试安装开源的声明文件：如npm install @types/{模块名称} 。

如果不存在@types声明，需要自行声明一个外部模块。

```javascript
// node_modules/mod/index.js
exports.test = function(name) {
  console.log(name);
};

// lib.d.ts
declare module "mod" {
  export function test(name: string): void;
}
// or shorthand declaration
declare module "mod"

```

当引入非相对路径模块的时候，编译引擎会自动寻找外部模块声明。

```typescript
import mod from "./mod"; //相对路径模块
import mod from "mod"; //非相对路径模块
```



## tsconfig.json

- baseUrl 

  设置`baseUrl`来告诉编译器到哪里去查找模块，此路径只针对非相对路径模块起作用，如 `import A from 'moduleA'`。

- paths

  必须同时设置baseUrl，path路径相对于baseUrl。

  > 如果你遇到 `Cannot find module 'xxxx'`的错误，需要注意baseUrl和paths仅仅是提供给TS编译器解析模块，分析类型信息时使用，实际预期并不会像webpack一样重写模块加载地址。
  > 参考以下issuse：[https://github.com/Microsoft/TypeScript/issues/10866#issuecomment-246789510](https://github.com/Microsoft/TypeScript/issues/10866#issuecomment-246789510)

* exclude

  exclude可以过滤include引入的路径，但是不能过滤files引入的文件。

* lib

  引入typescript内置的模块声明。譬如编译输出的目标代码为target：es5，但是代码中使用了es2015的Promise，这个时候需要引入内置的模块声明 "lib": ["es2015.promise"]。

  > 注意该声明只是为了typescirpt编译检查通过，实际上并不会在代码中注入Promise的polyfill，如果运行时环境没有Promise将会报错。

* module

  决定使用什么模块系统输出代码。

* 