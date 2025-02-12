---
date created: 2022-05-31 23:46
date updated: 2022-08-20 01:00
---

@babel/core 核心代码，包括 transform parse
babylon babel 解析器
babel-types 用于 AST 节点的 Lodash 式工具库，包括构造，验证以及变换 AST 节点方法
babel-traverse 遍历 AST，负责替换，移除和添加节点

@babel/core 生成语法树和遍历语法树
babel-core 调用 @babel/core
preset-env 负责转换语法树，转换时经常用到 babel-types（生产零件）

---

1. 读取配置对象，读取 shell 配置对象并合并参数
2. 创建 Compiler
   1. 保存 options
   2. 创建 this.hooks
   3. 初始化内部对象
3. 挂载所有插件
4. 执行 Compiler run 方法
5. 找到编译入口模块
6. 读取模块内容，找到与模块对应的所有 loaders
7. 调用所有 loader 转换文件内容
8. 创建入口模块对象
   1. 模块 ID：相对于根路径的相对路径
   2. 模块名称：entry1
   3. 依赖模块的绝对路径数组
9. loader 转换后的源码转换为 AST 语法树
10. 遍历语法树，找源码中的 import require
11. 找依赖的模块，找到放到当前模块的依赖数组中
12. 递归编译依赖模块
13. 把每个入口模块和依赖的模块编译成一个 chunk
14. 根据 chunk 生成 assets
    1. key 文件名 value 文件内容
15. 根据 assets 写入文件系统

---

1. webpack的环境变量都需要怎么设置，最优方案
2. 解析 css 都有哪些loader
3. css-loader 的 importLoaders
4. css 怎么分离文件
5. babel-loader polyfill的几种实现方式
6. source-map 以及最佳实现
7. 如何引入第三方库，五种方式
8. devServer mock以及其中间件形式
9. 有几种 hash
10. html css js 图片压缩方式
11. 如何实现css移动端适配
12. 资源模块是什么
13. 有哪些常用插件

---

1. 代码为什么要进行构建打包
   1. 体积更小：Tree-Shaking 压缩 合并
   2. 编译高级语法：TS ES6 模块化 Less
   3. 兼容性检查，语法错误：Polyfill Postcss ESlint
   4. 统一开发环境
   5. 统一构建流程和产出标准
   6. 集成构建规范
2. module chunk bundle 分别什么意思
   1. module 各个源码文件
   2. chunk 多模块合成的产物
   3. bundle 最终的输出文件
3. babel webpack 区别
   1. babel 只关心语法编译，不关心模块化
   2. webpack 打包构建工具
4. 如何产出 lib
   1. webpack.dll.js
   2. output.library
5. babel-polyfill babel-runtime 区别
   1. 是否污染全局
   2. 第三方 lib 要使用 babel-runtime
6. 如何实现懒加载
   1. import()
   2. Vue React 异步组件
   3. Vue-Router React-Router
7. Proxy 不能被 polyfill：没有内容可以完全模拟这个功能
8. 性能优化构建手段
   1. 优化 babel-loader
   2. IgnorePlugin
   3. noParse
   4. happyPack
   5. ParallelUglifyPlugin
   6. （开发）自动刷新 热更新 DllPlugin
9. 性能优化产出代码
   1. base64
   2. bundle + hash
   3. 懒加载
   4. 提取公共代码
   5. CDN 加速
   6. IgnorePlugin
   7. production（TreeShaking，Scope Hosting

---

### 性能优化

1. 缩小查找范围
2. noParse
3. IgnorePlugin
4. 区分环境变量
   1. 生产环境：分离CSS，压缩 HTML CSS JS 图片
   2. 开发环境：source-map，打印信息 live loader或 hot reload
5. 图片压缩和优化
6. 日志优化：friendly-errors-webpack-plugin
   1. stats 设置 verbose 全部输出
   2. echo $? 如果返回 0 成功，其他失败
7. 费时分析 speed-measure-webpack-plugin new一个实例并包裹整个 module.exports 即可
8. 拆包分析 webpack-bundle-analyzer
9. libraryTarget library
10. polyfill
11. purgecss-webpack-plugin 去除多余 css 代码
12. thread-loader happyPack 多进程打包 parallelUglifyPlugin 多进程压缩
13. DLLPlugin 动态链接库（开发
    1. DllPlugin 打包出 dll 文件，如 React ReactDOM
    2. DllReferencePlugin 引入并使用 dll 文件
14. TreeShaking：production 并且因为只有 esm 可以 TreeShaking，所以 babel-loader 配置  modules: false
15. splitChunk
    1. entry
       1. 如果入口 chunk 包含了重复模块，会重复打包
       2. 不能将核心应用进行动态拆分d
    2. 动态导入和懒加载
       1. 懒加载
       2. prefetch
       3. preload
    3. 提取公共代码
       1. 抽取第三方模块 vendor
       2. 抽取公共模块 common
       3. HtmlWebpackPlugin 中的 excludeChunks
16. scope hoisting
    1. 将所有的模块按引用顺序放到一个函数作用域
    2. import 转 require
    3. 创建的函数作用域减少，内存开销减小
    4. 大量作用域包裹代码导致体积增大
    5. CJS 不支持
    6. webpack.optimize.ModuleConcatenationPlugin 开发使用，生产默认打开
17. 缓存机制
    1. babel-loader 缓存 cacheDirectory
    2. cache-loader，直接放置到某个 loader 前就可以缓存
    3. hard-source-webpack-plugin 直接使用就可以，会把缓存放到 node_modules 的 .cache 中，大大加速第二次构建的速度
    4. oneOf 多个匹配到一个就退出
18. 热更新/自动刷新（开发
