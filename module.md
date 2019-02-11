# About Module



## Modules

### ES6Module

- With top-level import or export declarations in  a file.
- Typescript supports "export =" to simulate the CommonJS workflow, but "import module = require('module')" must be used to import this module.



### Notes

- Essential difference between ES6Module and CommonJS.

  1. CommonJS导出的对象遵循基本类型和引用类型的原则，但是ES6Module导出的是“完全引用”。

  ```javascript
  // CommonJS
  //moduleA.js
  let number = 1;
  
  const myModule = {
    name: "berofeChange"
  };
  
  let obj = {
    name: "oldObject"
  };
  
  // change after 1 second
  setTimeout(() => {
    number = 2;
    myModule.name = "afterChange";
    obj = { name: "newObjet" };
  }, 1000);
  
  module.exports = {
    number,
    myModule,
    obj
  };
  
  
  // index.js
  const { number, myModule, obj } = require("./moduleA.js");
  
  // echo value after changed
  setTimeout(() => {
    console.log(number); // echo 1;
    console.log(myModule.name); // echo afterChange
    console.log(obj); // echo { name: 'oldObject' }
  }, 1500);
  
  ```

  ```javascript
  // ES6Module
  //moduleA.ts
  export let number = 1;
  
  export const module = {
    name: "berofeChange"
  };
  
  export let obj = {
    name: "oldObject"
  };
  
  // change after 1 second
  setTimeout(() => {
    number = 2;
    module.name = "afterChange";
    obj = { name: "newObjet" };
  }, 1000);
  
  
  // index.ts
  import { number, module, obj } from "./moduleA";
  
  // echo value after changed
  setTimeout(() => {
    console.log(number); // echo 2;
    console.log(module.name); // echo afterChange
    console.log(obj); // echo { name: 'newObjet' }
  }, 1500);
  ```
