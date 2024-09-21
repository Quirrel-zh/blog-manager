---
title: 【webpack】webpack快速入门
date: 2024-09-22 00.02.32
tags: 
    - webpack
    - web
categories:
    - web
    - webpack
toc: true
---

### 概念

本质上，**webpack** 是一个用于现代 JavaScript 应用程序的 *静态模块打包工具*。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个依赖图，然后将你项目中所需的每一个模块组合成一个或多个 *bundles*，它们均为静态资源，用于展示你的内容。

<!-- more -->

### 快速开始

{% message color:warning title:运行环境 %}
`webpack`是基于`node.js`运行的，请确保node.js最低版本为10.13
{% endmessage %}

#### 构建项目

首先创建一个文件夹，取任意名字。再进行初始化：
<div style="margin: 0 0 20px 0; padding: 10px 20px 20px 20px; border-radius: 6px; box-shadow: 0 0.5em 0.75em -0.125em rgba(10, 10, 10, 0.1), 0 0px 0 1px rgba(10, 10, 10, 0.02);">
	{% tabs size:medium %}
	<!-- tab id:1 title:npm active -->
	`npm init -y`
	<!-- endtab -->
	<!-- tab id:2 title:yarn -->
	`yarn init -y`
	<!-- endtab -->
	<!-- tab id:3 title:pnpm -->
	`pnpm init -y`
	<!-- endtab -->
	{% endtabs %}
</div>
<br>

> 后续我们使用npm进行示例



创建完成之后添加`webpack`依赖。

```powershell
npm add webpack webpack-cli --dev
```

使用`--dev`表示将webpack相关的依赖添加的开发者环境中。毕竟我们只有开发的时候会使用到webpack

#### 进行第一次打包

使用ide打开项目，并创建`src`目录，在目录下创建`index.js`文件。随便在里面写点东西

```javascript
console.log('Hello World!')
```

再去项目更目录创建一个`index.html`文件。将index.js引入此文件中

```html
<script src="./src/index.js"></script>
```

运行网页，在控制台中发现成功打印出了`Hello World!`

现在进行第一次进行webpack打包，使用命令行运行打包指令：

```
npx webpack
```

这样就完成了第一次打包，并且在项目目录中新加了一个`dist`目录，目录下有个`main.js`文件，其中的语句和我们在`index.js`中编写的一样。因为此时我们没有用到`import`导入任何的依赖。

这时候我们在html文件中的index.js的引用改为main.js，并更新页面。

```html
<script src="./dist/main.js"></script>
```

此时控制台依然可以成功打印出`Hello World!`

#### 添加依赖

此时我们试试在有依赖的情况下，打包后的代码是什么样的。
首先在src目录下新建一个`data.js`文件。里面导出一个函数来返回一串博客列表的数据：

```javascript
export function getBlogPosts() {
    return ["blog 1", "blog 2", "blog 3"];
}
```

然后在`index.js`中打印语句进行修改

```javascript
import { getBlogPosts } from "./data";

console.log(getBlogPosts());
```

此时再运行打包指令`npx webpack`后，查看main.js中的文件，发现此时语句并不和index.js中一致了

```javascript
// main.js
(() => {
	'use strict';
	console.log(['blog1', 'blog2', 'blog3']);
})();

```

说明webpack在打包时自动判断了代码的逻辑，并且简化了代码语句。并且此时再浏览器控制台中依然可以打印出博客列表的数据。

#### 配置文件

webpack最核心的一个部分就是他的配置文件，我们可以修改webpack 的各种配置比如`入口(entry)`、`输出(output)`、`loader`、`插件(plugin)`、`模式(mode)`、`浏览器兼容(browser compatibility)`以及`环境(environment)`。可以浏览webpack官方中文文档来获取详细信息。

> - [概念 | webpack 中文文档 (docschina.org)](https://webpack.docschina.org/concepts/)

##### 修改打包后的文件名

我们先试试将打包后的文件名修改一下：

1. 首先在项目根目录下创建一个webpack配置文件: `webpack.config.js`

2. 在文件中对webpack进行配置：

   ```javascript
   const path = require('path') // 引入path模块
   
   module.exports = {
   	mode: 'development', // 设置开发环境
   	entry: './src/index.js', // 入口文件
   	output: { // 输出
   		filename: 'dist.js', // 打包后的文件名
   		path: path.resolve(__dirname, 'dist'), // 打包后的目录
   	},
   }
   ```

3. 再运行打包指令

   `npx webpack`

此时我们发现在dist目录下新加了一个`dist.js`文件，这正是我们修改完配置后生成的，并且里面新增了很多代码，这是因为我们现在的环境改成了开发模式。我们再将html文件中引入的js文件修改为`dist.js`

```html
<script src="./dist/dist.js"></script>
```

刷新页面，控制台依旧打印出博客列表数组，这时候我们就可以将旧的`main.js`文件删除

##### 使用`loader`来导入`CSS`文件

首先我们先将`index.js`中的语句修改一下，改为在页面渲染一个列表，为后续添加样式做铺垫

```javascript
import { getBlogPosts } from './data';

const blogs = getBlogPosts();
const ul = document.createElement('ul');
blogs.forEach(blog => {
    const li = document.createElement('li');
    li.innerText = blog;
    ul.appendChild(li);
});
document.body.appendChild(ul);
```

先打包后刷新页面，看看是否渲染出了博客列表:

![](../images/webpack/img01.png)

然后我们在src目录中新建一个`style.css`文件，随便写点样式

{% codeblock "点击展开" lang:css >folded %}

*{
margin: 0;
padding: 0;
}

body {
	display: grid;
	place-items: center;
	height: 100vh;
}

ul {
	list-style: none;
}

li {
	padding: 12px;
}
img {
	max-width: 500px;
}

{% endcodeblock %}

我们再将css文件导入到`index.js`中。这样才能充分利用到webpack，如果在html文件中导入，那就和普通的导入没有什么区别了

```javascript
import { getBlogPosts } from './data';
// 导入css文件的路径
import './style.css';
```

如果我们现在直接进行webpack打包，会发现控制台报错

```
ERROR in ./src/style.css 1:0
Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
// 您可能没有合适的loader来加载这种文件类型
> * {
|       margin: 0;
|       padding: 0;
 @ ./src/index.js 2:0-21

webpack 5.94.0 compiled with 1 error in 53 ms
```

那是因为我们还没有使用loader来加载css。想要加载css文件我们需要安装两个loader：`style-loader`、`css-loader`

```
npm add --dev style-loader css-loader
// 注意，我们所有关于webpack相关的依赖都使用--dev来添加，因为只有在开发环境中才能用到
```

安装完成中在`webpack.config.js`中添加loader，在这里需要要配置使用什么样的文件拓展名来使用哪一个loader

```javascript
const path = require('path'); // 引入path模块

module.exports = {
	mode: 'development', // 设置开发环境
	entry: './src/index.js', // 入口文件
	output: {
		filename: 'dist.js', // 打包后的文件名
		path: path.resolve(__dirname, 'dist'), // 打包后的目录
	},
    module: { // 模块配置
        rules: [ // 规则
            {
                test: /\.css$/i, // 匹配css文件 使用正则表达式
                use: ['style-loader', 'css-loader'], // 使用的loader
            }
        ]
    }
};

```

然后再运行打包指令，刷新页面。发现网页样式已经改变：

![](../images/webpack/img02.png)

> 在使用SASS或者Less等css预处理文件，也可以通过对应的loader来配置

##### 如何加载图片
