---
title: vue cli 中兼容高级语法
date: '2025-03-22T14:00:00.000+00:00'
lang: zh
duration: 1min
type: note
---

> 起因：在 vue cli 中引入处理 markdown 文档的第三方依赖包，发现 vue cli 无法处理 es6 高级语法，例如可选链语法等。

通过在项目根目录配置 `babel-loader`，以处理高级语法，但最后仍无法正确编译。原因是 `babel` 默认不处理 `node_modules` 下的内容。

最后通过大佬得知，在 `vue cli` 中可通过 `transpileDependencies` 来处理。

### transpileDependencies
- Type: `boolean | Array<string | RegExp>`
- Default: `false`

默认情况下 `babel-loader` 会忽略所有 `node_modules` 中的文件。你可以启用本选项，以避免构建后的代码中出现未转译的第三方依赖。

不过，对所有的依赖都进行转译可能会降低构建速度。如果对构建性能有所顾虑，你可以只转译部分特定的依赖：给本选项传一个数组，列出需要转译的第三方包包名或正则表达式即可。
