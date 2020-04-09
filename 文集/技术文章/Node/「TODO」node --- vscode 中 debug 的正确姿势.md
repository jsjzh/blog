为什么文章这么短？就这么点内容？

博主才疏学浅，光写了这么写，就快被榨干了，我真的一滴都不剩啦（指肚子里的墨水）。

## NODE DEBUG

`.vscode/launch.json` 中的具体配置，关于在 node 中 debug 可以看我的一篇 blog「TODO」

### `javascript`

vscode 中调试和执行普通 js 文件方式，直接执行编译后的 js 代码

```json
{
    "type": "node",
    "request": "launch",
    "name": "TEST-Debug",
    "program": "${workspaceFolder}/test/index.js"
},
```

### `typescript`

#### `ts-node`

推荐使用 `ts-node` 直接执行和调试 ts 代码

```json
{
  "type": "node",
  "request": "launch",
  "name": "ts-debug",
  "cwd": "${workspaceFolder}/packages/fund-ttjj",
  "runtimeArgs": ["-r", "ts-node/register"],
  "args": [
    "${workspaceFolder}/packages/fund-ttjj/core/index.ts",
    "create",
    "demo-name"
  ],
  "env": {
    "TS_NODE_FILES": "true",
    "TS_NODE_PROJECT": "${workspaceFolder}/packages/fund-ttjj/tsconfig.dev.json"
  }
}
```

#### sourcemap

vscode 中调试配置： ts -> js 后，根据 sourcemap 调试 js
tsconfig.dev.js 需配置: sourcemap & incremental（增量编译）
tsc 编译时带上 --listEmittedFiles 可看到当前增量编译的文件名
ps: 最好使用两个版本的 tsconfig.js 因为 debugger 需要 sourcemap，而 npm 正式发包时，并不需要

```json
{
  "type": "node",
  "request": "launch",
  "name": "JS-Debug",
  "program": "${workspaceFolder}/src/index.ts",
  "preLaunchTask": "npm: dev",
  "sourceMaps": true,
  "outFiles": ["${workspaceFolder}/lib/**/*.js"]
}
```
