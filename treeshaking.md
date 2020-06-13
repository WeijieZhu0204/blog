# Treeshaking



## 使treeshaking生效实验对照

环境：webpack：4.41.5

| 可变量                        | 值                     | treeshaking是否生效 | 备注                                                |
| ----------------------------- | ---------------------- | ------------------- | --------------------------------------------------- |
| webpack: mode参数             | development            | false               | production环境时默认开启treeshaking                 |
|                               | production             | true                |                                                     |
| typescript: module参数        | commonjs               | false               | 输出的模块必须是es6规范的模块                       |
|                               | es2015 \| ESNext       | true                |                                                     |
| package.json: sideEffects参数 | false \| 不填          | true                | sideEffects实测没什么效果                           |
|                               | true                   | true                |                                                     |
| export导出方式                | export default { *** } | false               | 通过export default导出复合对象无法被treeshaking优化 |
|                               | export ***             | true                |                                                     |

