# Leaflet插件创作指南

Leaflet的最值得注意的事情就是它的强大的插件生态系统。[Leaflet插件页面](http://leafletjs.com/plugins.html)列出了几十个很棒插件，并且每周都添加更多的插件。

本指南列出了一系列符合Leaflet本身质量标准的创作插件的最佳做法。

1. [介绍](#介绍)
	- [存放](#存放)
	- [命名](#命名)
	- [演示](#演示)
	- [Readme](#readme)
	- [许可](#许可)
2. [源码](#源码)
	- [文件结构](#文件结构)
	- [代码公约](#代码公约)
	- [插件API](#插件API)
3. [在NPM中发布](#在NPM中发布)
4. [模块加载](#模块加载)
5. [添加到插件列表](#添加到插件列表t)

## 介绍

### 存放

用来放置你的插件库的最佳位置是独立的[GitHub](http://github.com)项目。
如果你创建了一系列不同用途的插件，不要把它们放置在同一个GitHub项目里&mdash;
在单独的GitHub项目中使用小型、独立的插件通常更容易。

### 命名

大多数现有的插件（存放库）遵循命名规则像这样：`Leaflet.MyPluginName`。
你可以使用其他的命名格式（例如`leaflet-my-plugin-name`），只要确保你命名是包含`Leaflet`字眼就好，因为这样做会让别人显而易见明白这是一个Leaflet插件。

### 演示

在发布插件时最重要的一件事是包含展示插件功能的演示&mdash;这往往是人们最想看到的东西。

放置一个演示最简单的方法是使用[GitHub页面](http://pages.github.com/)。
比较好的[起始点](https://help.github.com/articles/creating-project-pages-manually) 是在你的库中创建一个`gh-pages` 分支并把`index.html` 页面放在这个分支下&mdash;放置后，它会以`http://<user>.github.io/<repo>`格式被发布。

### Readme

下一件事是你需要在这个库根目录里写一个[好的`README.md`](https://github.com/noffle/art-of-readme)（或链接到具有相似内容的网站）。
至少应包含以下条目：

- 插件名
- 一个简单、简明的描述，用于说明它的作用
- 配置条件
	- Leaflet 版本
	- 其他外部依赖（如果有的话）
	- 浏览器/设备的兼容性
- 演示链接
- 引入插件的说明指导
- 简单的使用代码示范
- API 接口参考（方法、选项、事件）

### 许可

每一个开源库都需要包含一个许可文件。
如果你不知道为你的代码选择什么开源许可证，
[MIT许可](http://opensource.org/licenses/MIT) 和 [BSD 2-Clause许可](http://opensource.org/licenses/BSD-2-Clause) 是很好的选择。
你可以将其放在回购中作为`LICENSE`文件，也可以从Readme链接到许可证。

## 源码

### 文件结构

保证文件结构清洁、简单，不要在一个地方堆积很多文件 &mdash;让访客在你的库中浏览轻松一点。

一个简单插件的准系统结构将如下所示：

```
my-plugin.js
README.md
```

一个更复杂的插件文件结构的例子：

```
/src        - JS 源代码文件
/dist       - 缩小插件JS文件，CSS文件，图像
/spec       - 测试文件
/lib        - 任何外部库/插件，如有必要
/examples   - 插件用法的HTML示例
README.md
LICENSE
package.json
```

### 代码公约

每个人的代码风格都不一样，但重要的是你要贯彻为插件选择的任何代码风格。

想要赢在起跑线，请浏览[Airbnb编写的JavaScript指南](https://github.com/airbnb/javascript).
除了使用智能缩进（硬tab用于缩进，空格用于对齐）并`function`关键字后面加一个空格外，Leaflet完全遵循相同的代码公约。 

### 插件API

永远不要在你的插件中暴露出全局变量。<br>
如果你建了一个新类，把它放在`L`命名空间中（`L.MyPlugin`）。<br>
如果你继承了已经存在的类，把它写作子属性（`L.TileLayer.Banana`）。<br>
每个类在camelCase中都应该有一个工厂函数，例如`L.titleLayer.banana`。<br>
如果你想在已经存在的类里添加一个新方法，你可以像这样做： `L.Marker.include({myPlugin: …})`.

函数，方法，属性和工厂名称应在`camelCase`中。<br>
类名应该在`CapitalizedCamelCase`中。

如果你的函数中有很多参数，请考虑接受一个选项对象（尽可能放置默认值，以便用户不需要指定所有这些值）：

```js
// 错误做法
marker.myPlugin('bla', 'foo', null, {}, 5, 0);

 // 正确做法
marker.myPlugin('bla', {
	optionOne: 'foo',
	optionThree: 5
});
```

最重要的是，保持简洁。Leaflet的一切都是简洁的。

## 在NPM中发布

NPM（Node Packaged Modules）是一个JavaScript包管理器和代码仓库。在NPM上发布你的模块允许其他开发人员快速找到并安装你的插件以及其所依赖的任何其他插件。

NPM 有一个优秀的[开发人员指南](https://www.npmjs.org/doc/misc/npm-developers.html)，帮助你完成整个过程。

发布插件时，你应该添加一个`Leaflet`依赖关系到你的`package.json`文件。这样做是为了当安装插件后后，将自动安装Leaflet。

这里有一个Leaflet插件的`package.json`文件例子：

```json
{
  "name": "my-leaflet-plugin",
  "version": "1.0.0",
  "description": "A simple leaflet plugin.",
  "main": "my-plugin.js",
  "author": "You",
  "license": "IST",
  "peerDependencies": {
    "leaflet": "^1.0.0"
  }
}
```

如果可能，请勿将你的缩小版文件（例如dist）提交到代码库中；这可能导致在尝试调试错误文件时的混乱。
相反，在使用[预发布脚本](https://docs.npmjs.com/misc/scripts#common-uses)发布插件包之前，使用npm触发构建/缩小，例如：

```json
{
  "name": "my-leaflet-plugin",
  ...
  "scripts": {
    "prepublish": "grunt build"
  }
}
```

然后，你可以使用[.gitignore](https://help.github.com/articles/ignoring-files/)文件来确保最小化的文件不被版本化，并且一个and an
[空的`.npmignore`](https://docs.npmjs.com/misc/developers#keeping-files-out-of-your-package)文件以确保它们被发布到NPM。

## 模块加载

如同[RequireJS](http://requirejs.org/) 和 [Browserify](http://browserify.org/) 这样的模块加载器运行模块系统，像AMD（Asynchronous Module Definition）和 CommonJS 一样允许开发者模块化和加载他们的源码。

你可以通过遵循[通用模块定义](https://github.com/umdjs/umd/blob/master/templates/returnExportsGlobal.js)的模式，为你的Leaflet插件添加对AMD / CommonJS加载程序的支持。

```js
(function (factory, window) {

    // 定义一个依赖于`Leaflet`的AMD模块
    if (typeof define === 'function' && define.amd) {
        define(['leaflet'], factory);

    
    } 
    
    // 定义一个依赖于`Leaflet`的Common JS模块
    else if (typeof exports === 'object') {
        module.exports = factory(require('leaflet'));
    }

    // 将你的插件附加到全局变量`L`上
    if (typeof window !== 'undefined' && window.L) {
        window.L.YourPlugin = factory(L);
    }
}(function (L) {
    var MyLeafletPlugin = {};
    // 运行你的插件

    // 完成后返回你的插件
    return MyLeafletPlugin;
}, window));
```

现在，你的插件可以作为AMD和CommonJS模块使用，可以在Browserify和RequireJS这样的模块加载器中使用。


## 添加到插件列表

一旦你的插件发布完成，把它加入[Leaflet插件列表](http://leafletjs.com/plugins.html)是一个好的想法。像这样做：

* [Fork](https://help.github.com/articles/fork-a-repo/) 这个Leaflet代码库。
* 在`docs/plugins.md`文件中，找到你的插件应该进入的部分，并添加一个表行说明你的插件的信息和链接。
* 提交代码到你的`fork`.
* 从你的fork[推送请求](https://help.github.com/articles/creating-a-pull-request/) 到Leaflet原始库。

一旦这个推送的请求被传递，Leaflet的维护者会迅速查看你的插件，并且一切看起来是正确的，你的插件此后不久就会出现在列表中。
