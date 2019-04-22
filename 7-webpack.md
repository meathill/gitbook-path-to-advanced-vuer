组件库开发与使用
=============

组件开发是个很大的话题，多半得搞一次“达人课”才讲得完，这里我暂且只分享当时被卡了一阵子的编译。

好比说，我们有好几个产品，这些产品之间有一些公用的组件，在每个仓库里独立维护这些组件明显不合适，所以我们就想把它们取出来，作为一个独立的组件仓库，那么应该怎么做呢？

1. 建立仓库
--------------

这一步应该不用介绍了，创建仓库，`npm init` 创建 package.json，安装依赖，等等。

2. 配置 webpack
--------------

这一步主要用来配置打包选项。

首先是入口。实际上，Vue 单文件组件中的 `<template>` 部分都会被编译成 `render()` 函数，然后 `<style>` 部分则都会被编译成插入样式的脚本，最终生成的组件是一个纯的 JS 文件，所以入口就是一个暴露所有组件给外界的 JS。

接下来是输出模式，重点在这里。Webpack 支持各种各样的打包形式，作为库使用时，我们需要的是 commonjs2 或者 umd，所以一定要配置 `libraryTarget`。

然后我们关注依赖。通常，这个组件库，在这个时候，以内部使用为主，依赖多半是共用的。所以我们不需要把依赖都打包，可以使用 `externals` 配置将它们排除出去。

最后，如果你希望抽出 CSS、或者对 JS 进行压缩，也可以配置 terser-webpack-plugin、mini-css-extract-plugin 这些插件，基本上不会出错，我就不多说了。

```js
// webpack.config.js
const VueLoaderPlugin = require('vue-loader/lib/plugin');

module.exports = {
  // entry 默认为当前目录 ./src/index.js，可以不写
  output: {
    libraryTarget: 'commonjs2', // 因为不考虑单独使用，commonjs2 足够了
  },
  // module 配置就不写了，没啥可写的
  // 以 commonjs 形式加载这两个依赖，这两个依赖在产品仓库中都有用到，所以不需要打包
  externals: {
    'eventemitter3': 'commonjs eventemitter3',
    'lodash-es': 'commonjs lodash-es'
  },
  // vue-loader >= 15 之后，编译机制出现变化，一定要用这个插件
  plugins: [
    new VueLoaderPlugin(),
  ],
}

// src/index.js
import Component1 from './component-1.vue';
import Component2 from './component-2.vue';

export {
  Component1,
  Component2,
};
```

3. 编译，提交，安装
----------------

我们有两种部署方案：1. 直接通过 GitHub 部署；2. 通过 NPM 部署。

NPM 部署只需要 `npm publish` 即可，好处是不污染仓库，有比较完善的版本管理和依赖管理。坏处是 NPM 默认是公开的，想私有得花钱买企业版。

所以我暂时只用到 GitHub 部署。首先，编译，然后把编译后的文件也提交。然后，修改 package.json，把里面的入口文件，也就是 `main` 指向编译结果 `dist/index.js`。

接下来安装，`npm i owner/repo` 即可，比如我的 GitHub 用户名 meathill，我的仓库名 hello-world，那就是 `npm i meathill/hello-world`。也可以指定版本，在后面加 `#`：`npm i meathill/hello-world#a1b2c3d`。NPM 安装 GitHub 仓库无法指定版本，会报错“没有 package.json”，暂时不知道如何解决。

--------

好了，至此，我们可以独立的开发维护组件仓库，并且在其它产品中使用它了。


