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
3. [在NPM上发布](#publishing-on-npm)
4. [模块装载](#module-loaders)
5. [添加到插件列表](#adding-to-the-plugins-list)

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

Everyone's tastes are different, but it's important to be consistent with whatever conventions you choose for your plugin.

For a good starting point, check out [Airbnb JavaScript Guide](https://github.com/airbnb/javascript).
Leaflet follows pretty much the same conventions
except for using smart tabs (hard tabs for indentation, spaces for alignment)
and putting a space after the `function` keyword.

### 插件API

Never expose global variables in your plugin.<br>
If you have a new class, put it directly in the `L` namespace (`L.MyPlugin`).<br>
If you inherit one of the existing classes, make it a sub-property (`L.TileLayer.Banana`).<br>
Every class should have a factory function in camelCase, e.g. (`L.tileLayer.banana`).<br>
If you want to add new methods to existing Leaflet classes, you can do it like this: `L.Marker.include({myPlugin: …})`.

Function, method, property and factory names should be in `camelCase`.<br>
Class names should be in `CapitalizedCamelCase`.

If you have a lot of arguments in your function, consider accepting an options object instead
(putting default values where possible so that users don't need to specify all of them):

```js
// bad
marker.myPlugin('bla', 'foo', null, {}, 5, 0);

 // good
marker.myPlugin('bla', {
	optionOne: 'foo',
	optionThree: 5
});
```

And most importantly, keep it simple. Leaflet is all about *simplicity*.

## Publishing on NPM

NPM (Node Packaged Modules) is a package manager and code repository for JavaScript. Publishing your module on NPM allows other developers to quickly find and install your plugin as well as any other plugins it depends on.

NPM has an excellent [developers guide](https://www.npmjs.org/doc/misc/npm-developers.html) to help you through the process.

When you publish your plugin you should add a dependency on `leaflet` to your `package.json` file. This will automatically install Leaflet when your package is installed.

Here is an example of a `package.json` file for a Leaflet plugin.

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

If possible, do not commit your minified files (e.g. `dist`) to a repo; this can
lead to confussion when trying to debug the wrong file. Instead, use `npm` to
trigger a build/minification just before publishing your package with a
[`prepublish` script](https://docs.npmjs.com/misc/scripts#common-uses), for example:

```json
{
  "name": "my-leaflet-plugin",
  ...
  "scripts": {
    "prepublish": "grunt build"
  }
}
```

You can then use the [`.gitignore`](https://help.github.com/articles/ignoring-files/)
file to make sure the minified files are not versioned, and an
[empty `.npmignore`](https://docs.npmjs.com/misc/developers#keeping-files-out-of-your-package)
to ensure that they are published to NPM.

## Module Loaders

Module loaders such as [RequireJS](http://requirejs.org/) and [Browserify](http://browserify.org/) implement module systems like AMD (Asynchronous Module Definition) and CommonJS to allow developers to modularize and load their code.

You can add support for AMD/CommonJS loaders to your Leaflet plugin by following this pattern based on the [Universal Module  Definition](https://github.com/umdjs/umd/blob/master/templates/returnExportsGlobal.js)

```js
(function (factory, window) {

    // define an AMD module that relies on 'leaflet'
    if (typeof define === 'function' && define.amd) {
        define(['leaflet'], factory);

    // define a Common JS module that relies on 'leaflet'
    } else if (typeof exports === 'object') {
        module.exports = factory(require('leaflet'));
    }

    // attach your plugin to the global 'L' variable
    if (typeof window !== 'undefined' && window.L) {
        window.L.YourPlugin = factory(L);
    }
}(function (L) {
    var MyLeafletPlugin = {};
    // implement your plugin

    // return your plugin when you are done
    return MyLeafletPlugin;
}, window));
```

Now your plugin is available as an AMD and CommonJS module and can be used in module loaders like Browserify and RequireJS.


## Adding to the plugins list

Once your plugin is published, it is a good idea to add it to the [Leaflet plugins list](http://leafletjs.com/plugins.html). To do so:

* [Fork](https://help.github.com/articles/fork-a-repo/) the Leaflet repo.
* In the `docs/plugins.md` file, find the section your plugin should go in, and add a table row with information and links about your plugin.
* Commit the code to your fork.
* [Open a pull request](https://help.github.com/articles/creating-a-pull-request/) from your fork to Leaflet's original repo.

Once the pull request is done, a Leaflet maintainer will have a quick look at your
plugin and, if everything looks right, your plugin will appear in the list shortly thereafter.
