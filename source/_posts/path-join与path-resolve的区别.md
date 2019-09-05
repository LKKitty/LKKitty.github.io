---
title: path.join与path.resolve的区别
date: 2019-09-05 11:12:33
tags:
  - nodejs
---
path 是nodejs中的一个路径模块。
<!-- more -->
# 使用
path 模块提供用于处理文件路径和目录路径的实用工具。 它可以使用以下方式访问：
```js
const path = require('path')
```
# path.resolve()
  path.resolve总是返回一个以相对于当前的工作目录（working directory）的绝对路径。
  “__dirname”是node.js中的一个全局变量，它指向当前执行脚本所在的目录。
  ```js
    path.resolve(__dirname, './b') // '/Users/username/Projects/webpack-demo/a/b'
  ```

# path.join()
path.join()对于以/开始的路径片段，path.join只是简单的将该路径片段进行拼接
```js
path.join('/a', '/b') // 'a/b'
```
# 参考资料
  [path.join 与 path.resolve 的区别](https://www.cnblogs.com/philipding/p/7773022.html)
  [Node.js中文网](http://nodejs.cn/api/path.html)