---
title: Webpack 记录
date: 2019-08-07 13:18:12
tags: [webpack]
category: 前端

---



# 基本概念

- [https://webpack.js.org/concepts/](https://webpack.js.org/concepts/)

## Entry

打包开始的入口文件, webpack 开始打包从这个文件开始检索文件，绘制依赖图表

> 可以设置多个入口 [entry-points](https://webpack.js.org/concepts/entry-points/)

`webpack.config.js`

```js
module.exports = {
    ectry: './src/index.js'
}
```

```js
module.exports = {
  entry: {
    app: './src/app.js',
    adminApp: './src/adminApp.js'
  }
};
```

## Output

Output 属性配置打包生成的 bundles 放置位置和文件名(默认: `./dist/main.js`)

`webpack.config.js`

```js
const path = require('path');

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-bundle.js'
    }
};
```

> __dirname 总是返回被执行的 js 所在文件夹的绝对路径
> __filename: 总是返回被执行的 js 的绝对路径


- [https://webpack.js.org/concepts/output/](https://webpack.js.org/concepts/output/)
- [https://webpack.js.org/configuration/output/](https://webpack.js.org/configuration/output/)

使用 hash 命名输出的文件:

```js
module.exports = {
  //...
  output: {
    path: '/home/proj/cdn/assets/[hash]',
    publicPath: 'https://cdn.example.com/assets/[hash]/'
  }
};
```

### publicPath

>publicPath 以 / 结尾

静态资源最终访问路径 = output.publicPath + 资源loader或插件等配置路径

## Loaders

webpack 原生只支持 JavaScript, JSON 文件，Loaders 帮助 webpack 处理其他类型的文件。

```js
module.exports = {
    //...
    module: {
        rules: [
            { test: /\.txt$/, use: 'raw-loader' }
        ]
    }
}
```

- `test`: 匹配文件类型
- `use`: 使用那种 loader 解析文件，可以设置多个 loader

## plugins

plugins 处理整个 bundle 文件的优化，asset 管理，环境变量 注入

`webpack.config.js`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin'); // installed  via npm
const webpack = require('webpack'); //to access build-in plugins

module.exports = {
    //...
    plugins: [
        new HtmlWebpackPlugin({ template: './src/index.html' })
    ]
}
```

- `html-webpack-plugin` 插件，帮助向 html 文件注入生成的 bundle.js 文件

## Mode

设置打包模式

- development
- production
- none

每个模式，背后包含了一些列的配置: [https://webpack.js.org/configuration/mode/](https://webpack.js.org/configuration/mode/)

根据 mode 设置不同的打包配置:

```js
var config = {
  entry: './app.js'
  //...
};

module.exports = (env, argv) => {

  if (argv.mode === 'development') {
    config.devtool = 'source-map';
  }

  if (argv.mode === 'production') {
    //...
  }

  return config;
};
```

