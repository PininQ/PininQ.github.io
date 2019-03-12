---
layout: post
title: Webpack4.x 生产模式配置教程「转」
categories: Webpack
description: Webpack4.x 生产模式配置教程「转」
keywords: Webpack
---

[维基百科](https://zh.wikipedia.org/zh-cn/Webpack) 关于 `Webpack` 的介绍：

`Webpack` 是一个开源的前端打包工具。`Webpack` 提供了前端开发缺乏的模块化开发方式，将各种静态资源视为 `模块`，并从它生成优化过的代码。

`Webpack` 可以从 `终端`、或是更改 `webpack.config.js` 来设置各项功能。

要使用 `Webpack` 前须先安装 `Node.js`。`Webpack` 其中一个特性是使用加载器来将资源转化成 `模块`。开发者可以自定义加载器的顺序、格式来因应项目的需求。

![Webpack4-01](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/webpack/webpack-4-quickstart/Webpack4-01.png)

从图中可以看出，`Webpack` 可以将多种静态资源 js、css、less 转换成一个静态文件，减少了页面的请求。

## 一、基础配置

创建一个空项目


```
mkdir webpack-4-quickstart
```
切换到该目录


```
cd webpack-4-quickstart
```
初始化 `package.json`（添加 `-y` 就不需要一直回车了，表示 `yes`）

```
npm init -y
```
目录会生成一个 `package.json` 文件

![Webpack4-02](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/webpack/webpack-4-quickstart/Webpack4-02.png)


安装 `Webpack4`（其中的 `i` 是简化的 `install`,`--save-dev` 可以简化成 `-D`）

```
npm i webpack --save-dev
```
![Webpack4-03](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/webpack/webpack-4-quickstart/Webpack4-03.png)

`Webpack4.x` 需要额外安装 `webpack-cli`

```
npm i webpack-cli --save-dev
```
![Webpack4-04](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/webpack/webpack-4-quickstart/Webpack4-04.png)


打开 `package.json` 添加构建脚本

```
"build": "webpack"
```

保存，然后运行

```
npm run build
```
报错如下
```
ERROR in Entry module not found: Error: Can't resolve'./src'in'H:\webCode\webpack\webpack-4-quickstart'
```
![Webpack4-05](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/webpack/webpack-4-quickstart/Webpack4-05.png)

webpack 4 需要在./src 目录下找一个入口文件！

简要来说：webpack 需要这个入口文件来开始 js 代码的打包。
在以前的版本里 webpack 的入口文件需要在配置文件 webpack.config.js 里指定。

但是现在不用指定了，它会默认选择./src/index.js 这个文件。

测试这个新特性很容易，创建一个./src/index.js:


```
console.log("Hello Webpack");
```
重新构建

```
npm run build
```

![Webpack4-06](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/webpack/webpack-4-quickstart/Webpack4-06.png)

在 `~/webpack-4-quickstart/dist/main.js` 得到你打包后的文件。

在 `webpack 4` 中 **不需要指定入口和出口文件**。

所以这就是第一个新特性：他会把./src/index.js 默认为入口文件，把打包后的文件放在./dist/main.js。

## 二、生产和开发模式

在 webpack 中拥有两份配置文件是常事。

一个典型的项目应该有：

- 一个 **开发用的配置文件**，用来定义 `webpack` 的 `dev server` 和其他东西
- 一个 **生产环境用的配置文件**，用来定义 `UglifyJSPlugin`，`sourcemap` 和其他东西。

在 `webpack4` 中你能够不写一行配置。

`webpack4` 介绍了生产和开发模式。

实际上如果你关注过 `npm run build` 的输出信息你会看到这个警告：

![Webpack4-07](https://raw.githubusercontent.com/PininQ/MarkdownPhotos/master/webpack/webpack-4-quickstart/Webpack4-07.png)

> The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'productoduction' to enable defaults for each environment.

打开 `package.json` 文件添加如下脚本


```json
"scripts": {
  "dev": "webpack --mode development",
  "build": "webpack --mode production"
}
```

现在运行：

```
npm run dev
```
查看 `./dis/main.js` 文件。发现 `main.js` 文件没有被压缩！

再这样运行：

```
npm run build
```

再查看 `./dis/main.js` 文件。发现 `main.js` 是一个压缩后的文件！

是这样的，生产模式开启了一系列额外的优化。包括 minification, scope hoisting, tree-shaking 等。

另一边开发模式为速度做了优化，除了提供一个没有压缩的包以外没有做额外的事。

所以这是第二个新特性：生产和开发模式。

在 `webpack4` 你不需要一行配置，只需要一个 `--mode` 选项。

## 三、覆盖默认的入口 / 出口文件

`webpack 4` 默认的入口 / 出口文件：

- 入口文件。默认是 `./src/index.js`
- 出口文件。默认是 `./dist/main.js`


我们可以在 `package.json` 里配置，覆盖默认的入口或者出口配置。

配置示例
```json
"scripts": {
  "dev": "webpack --mode development src/js/index.js --output a/main.js",
  "build": "webpack --mode production src/js/index.js --output a/main.js"
}
```
> PS：新建 a 目录

## 四、用 Babel 转换 ES6 的 JS 代码

现代 `JavaScript` 大多是用 `ES6` 写的。

但是不是所有浏览器都知道怎么处理 `ES6`。我们需要做一些转换。


这个转换的步骤叫做 `transpiling`。`transpiling` 是指把 `ES6` 转换成浏览器能够识别的代码。

`webpack` 本身并不知道如何去转换，但是它有 `loaders`。把它们想象成转换器。

`babel-loader` 是 `webpack` 的一个 `loader`，可以将 `ES6` 代码转换成 `ES5` 的代码。
为了使用这个 `loader` 我们需要去安装一系列的依赖。特别是：

- babel-core
- babel-loader
- babel-preset-env （for compiling Javascript ES6 code down to ES5）

安装 `loader` 和依赖：

```
npm i babel-core babel-loader babel-preset-env --save-dev
```

下一步，在项目目录下新建一个 `.babelrc` 文件，用来配置 `Babel`。

内容如下：
```
{
    "presets": [
        "env"
    ]
}
```

在这里我们有两个途径去配置 `babel-loader`：

- 用 `webpack` 的配置文件
- 在 `npm` 脚本里使用 `--module-bind`

`webpack 4` 的零配置适用于：

- 入口文件。默认是 `./src/index.js`
- 出口文件。默认是 `./dist/main.js`
- 生产和开发模式（无需创建两套配置文件）

所以，对于 `loaders` 我们仍然需要使用配置文件。

仍必须配置 `webpack.config.js` 文件。

### 1. 通过配置文件使用 babel-loader

在项目目录下新建一个 `webpack.config.js` 文件，然后配置 `loader`：


```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
```

下一步打开 `./src/index.js`，然后编写一些 `ES6` 代码：


```javascript
const arr = [1,2,3];
const iAmJavaScriptES6 = () => console.log(...arr);
window.iAmJavaScriptES6 = iAmJavaScriptES6;
```
最后打包：

```
npm run build
```

可以在 `./dist/main.js` 查看转换后的代码。

### 2. 不通过配置文件使用 babel-loader

还有一种使用 `webpack loaders` 的方法。

`--module-bind` 选项让你从命令行指定 `loaders`。

在 `package.json` 中配置：


```
"scripts": {
    "dev": "webpack --mode development --module-bind js=babel-loader",
    "build": "webpack --mode production --module-bind js=babel-loader"
}
```
然后你就可以开始构建了。

不过这种方式不是很方便，通过配置文件使用 `babel-loader` 相对而言要便捷很多。

## 五、在 webpack 4 中配置 React

安装 `React` 依赖包：

```
npm i react react-dom --save-dev
```
添加 `babel-preset-react`：


```
npm i babel-preset-react --save-dev
```
在 `.babelrc` 里配置 `preset`：

```
{
  "presets": ["env", "react"]
}

```

配置 `babel-loader` 也去加载 `.jsx` 文件。

`webpack.config.js` 这样配置：

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
```

测试是否搭好，你可以在 `./src/App.js` 里创建一个 `React` 组件。



```js
import React from "react";
import ReactDOM from "react-dom";
const App = ()=>{
    return (
        <div>
            <p>React here!</p>
            <p>Qin Bin!</p>
        </div>
    );
};
export default App;
ReactDOM.render(<App />, document.getElementById("app"));
```

然后在 `./src/idnex.js` 中引入：


```js
import App from "./App";
```

重新构建

```
npm run build
```

## 六、HTML 插件

`webpack` 需要两个额外的组件去处理 `HTML`：

- html-webpack-plugin
- html-loader

然后更新 ``webpack.config.js`` 的配置


```js
const HtmlWebPackPlugin = require("html-webpack-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: {minimize: true}
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    })
  ]
};
```

测试配置是否成功，在 `./src/index.html` 新建一个 `HTML` 文件：


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>webpack 4 quickstart</title>
</head>
<body>
    <div id="app">
    </div>
</body>
</html>
```

重新构想项目：

```
npm run build
```

查看 `./dist` 目录。可以看到运行的结果。

没有必要再你的 HTML 文件中引入你的 JavaScript：它会自动地注入进去。

在浏览器打开 `./dist/index.html`：可以看到 React 组件运行起来了！

如你所见在处理 HTML 上没有什么变化。

webpack 4 仍然是一个主要目标是 `js` 的模块打包工具。

但有个将 HTML 作为模块的方法（HTML 作为入口）。


## 七、提取 CSS 到文件中

webpack 不知道怎么去提取 CSS 到文件中。

在之前的 webpack 版本中使用的是 `extract-text-webpack-plugin` 来工作。

不幸的是这个插件在 webpack 4 表现并不好。

Michael Ciniawsky 说：

> 维护 extract-text-webpack-plugin 是一个很大的负担，而且这不是第一次因为这个问题使得升级 webpack 的主要版本变得困难。

`mini-css-extract-plugin` 是来解决这些问题的。

> 提示：你需要把 webpack 升级到 4.2.0.0，不然这个插件无法运行！

```
npm i mini-css-extract-plugin css-loader --save-dev
```

然后建立一个 `./src/main.css` 文件用来测试


```
body{
    line-height: 2px;
    background: #ccc;
}
p{
    color: green;
}
```

配置 plugin 和 loader：



```
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: {minimize: true}
          }
        ]
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ]
};
```

最后在 `./src/index.js` 入口文件中引入

```
//
// PATH OF THIS FILE:
//
import style from "./main.css";
```
构建：


```
npm run build
```

## 八、webpack dev server

在你改变代码后运行 npm run dev？这不是个理想的做法。花几分钟去配置下 webpack 的开发服务。一旦配置了 webpack dev server

它会在浏览器中加载你的 app。

只要你改变了文件，它会自动地刷新浏览器的页面。

安装下面的包来搭建 webpack dev server：


```
npm i webpack-dev-server --save-dev
```
然后打开 `package.json` 调整脚本：


```
"scripts": {
  "start": "webpack-dev-server --mode development --open",
  "build": "webpack --mode production"
}
```
保存关闭。

现在运行：


```
npm run start
```
你就能看到 webpack dev server 在浏览器中加载你的应用了。

webpack dev server 非常适合用来开发。（而且它能使得的 React Dev Tools 在浏览器中正常的工作）


## 九、转载出处

[【翻译】Webpack 4 教程：从 0 配置到生产模式
](https://juejin.im/post/5af934806fb9a07ab458bced)
