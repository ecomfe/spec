# 模块和加载器规范

## 简介

该文档主要的设计目标是定义前端代码的模块规范，便于开发资源的共享和复用。该文档
在 [amdjs](https://github.com/amdjs/amdjs-api/wiki) 规范的基础上，进行了更细粒度的规范化。

### 编撰

李玉北、erik、黄后锦、王杨、张立理、赵雷、陈新乐、顾轶灵、林志峰、刘恺华。

本文档由`商业运营体系前端技术组`审校发布。

### 要求

在本文档中，使用的关键字会以中文+括号包含的关键字英文表示： 必须(MUST) 。关键字"MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL"被定义在rfc2119中。

## 模块定义

模块定义 *必须(MUST)* 采用如下的方式：

```javascript
define( factory );
```

推荐采用`define(factory)`的方式进行`模块定义`。使用匿名`moduleId`，从而保证开发中模块与路径相关联，有利于模块的管理与整体迁移。

SHOULD NOT使用如下的方式：

```javascript
define( moduleId, deps, factory );
```

### moduleId

`moduleId`的格式应该符合 [amdjs](https://github.com/amdjs/amdjs-api/wiki/AMD) 中的约束条件。

1. `moduleId`的类型应该是`string`，并且是由`/`分割的一些`term`来组成。例如：`this/is/a/moduleId`。
2. `term`应该符合`[a-zA-Z0-9_]+`这个规则。
3. `moduleId`不应该有`.js`后缀。
4. `moduleId`应该跟文件的路径保持一致。

`moduleId`在实际使用（如`require`）的时候，又可以分为如下几种类型：

1. `relative moduleId`：是以`./`或者`../`开头的`moduleId`。例如：`./foo`, `../../bar`。
2. `top-level moduleId`：除上面两种之外的`moduleId`。例如`foo`，`bar/a`，`bar/b`。

在模块定义的时候，`define`的第一个参数如果是`moduleId`， *必须(MUST)* 是`top-level moduleId`， *不允许(MUST NOT)* 是`relative moduleId`。

### factory

#### AMD风格与CommonJS风格

模块的`factory`有两种风格，`AMD推荐的风格`和`CommonJS的风格`。`AMD推荐的风格`通过返回一个对象做为模块对象，`CommonJS的风格`通过对`module.exports`或`exports的属性`赋值来达到暴露模块对象的目的。

*建议(SHOULD)* 使用`AMD推荐的风格`，其更符合Web应用的习惯，对模块的数据类型也便于管理。


```javascript
// AMD推荐的风格
define( function( require ) {
    return {
        method: function () {
            var foo = require("./foo/bar");
            // blabla...
        }
    };
});

// CommonJS的风格
define( function( require, exports, module ) {
    module.exports = {
        method: function () {
            var foo = require("./foo/bar");
            // blabla...
        }
    };
});
```

#### 参数

模块的`factory`默认有三个参数，分别是`require`, `exports`, `module`。

```javascript
define( function( require, exports, module ) {
    // blabla...
});
```

使用`AMD推荐风格`时，`exports`和`module`参数可以省略。

```javascript
define( function( require ) {
    // blabla...
});
```

开发者 *不允许(MUST NOT)* 修改`require`, `exports`, `module`参数的形参名称。下面就是错误的用法：

```javascript
define( function( req, exp, mod ) {
    // blablabla...
});
```

#### 类型

`factory`可以是任何类型，一般来说常见的就是三种类型`function`, `string`, `object`。当`factory`不是`function`时，将直接做为模块对象。

```javascript
// src/foo.js
define( "hello world. I'm {name}" );

// src/bar.js
define( {"name": "fe"} );
```

上面这两种写法等价于：

```javascript
// src/foo.js
define( function(require) {
    return "hello world. I'm {name}";
});

// src/bar.js
define( function(require) {
    return {"name": "fe"};
} );
```

#### require

`require`这个函数的参数是`moduleId`，通过调用`require`我们就可以引入其他的模块。`require`有两种形式：

```javascript
require( {string} moduleId );
require( {Array} moduleIdList, {Function} callback );
```

`require`存在`local require`和`global require`的区别。

在`factory`内部的`require`是`local require`，如果`require`参数中的`moduleId`的类型是`relative moduleId`，那么相对的是当前`模块id`。

在全局作用域下面调用的`require`是`global require`，`global require`不支持`relative moduleId`。

```javascript
// src/foo.js
define( function( require ) {
    var bar = require("./bar"); // local require
});

// src/main.js
// global require
require( ['foo', 'bar'], function ( foo, bar ) {     
    // blablalbla...
});
```

#### exports

`exports`是使用`CommonJS风格`定义模块时，用来公开当前模块对外提供的API的。另外也可以忽略`exports`参数，直接在`factory`里面返回自己想公开的API。例如下面三种写法功能是一样的：

```javascript
define( function( require, exports, module ) {
    exports.name = "foo";
});

define( function( require, exports, module ) {
    return { "name" : "foo" };
});

define( function( require, exports, module ) {
    module.exports.name = "foo";
});
```

`module`是当前模块的一些信息，一般不会用到。其中`module.exports === exports`。

### dependencies

模块和模块的依赖关系需要通过`require`函数调用来保证。

```javascript
// src/js/ui/Button.js
define( function( require, exports, module ) {
    require("css!../../css/ui/Button.css");
    require("tpl!../../tpl/ui/Button.tpl.html");

    var Control = require("ui/Control");
    
    /**
     * @constructor
     * @extends {Control}
     */
    function Button() {
        Control.call(this);

        var foo = require("./foo");
        foo.bar();
    }
    baidu.inherits(Button, Control);

    ...

    // exports = Button;
    // return Button;
});
```

具体实现的时候是通过正则表达式分析`factory`的函数体来识别出来的。因此为了保证识别的正确率，请尽量
避免在函数体内定义`require`变量或者`require`属性。例如不要这么做：

```javascript
var require = function(){};
var a = {require:function(){}};
a.require("./foo");
require("./bar");
```

<a name="config"></a>
## 模块加载器配置

`AMD Loader`应该支持如下的配置，更新配置的时候，写法如下：

```html
<script src="${amdloader.js}"></script>
<script>
require.config({
    ....
});
</script>
```

### baseUrl

类型应该是`string`。在`ID-to-path`的阶段，会以`baseUrl`作为根目录来计算。如果没有配置的话，就默认以当前页面所在的目录为`baseUrl`。
如果`baseUrl`的值是`relative`，那么相对的是当前页面，而不是`AMD Loader`所在的位置。

### paths

类型应该是`Object.<string, string>`。它维护的是`moduleId`前缀到路径的映射规则。这个对象中的`key`应该是`moduleId`的前缀，`value`如果是一个相对路径的话，那么相对的是`baseUrl`。当然也可以是绝对路径的话，例如：`/this/is/a/path`，`//www.google.com/this/is/a/path`。

```javascript
{
    baseUrl: '/fe/code/path',
    paths: {
        'ui': 'esui/v1.0/ui',
        'ui/Panel': 'esui/v1.2/ui/Panel',
        'tangram': 'third_party/tangram/v1.0',
        'themes': '//www.baidu.com/css/styles/blue'
    }
}
```

在`ID-to-path`的阶段，如果`模块`或者`资源`是以`ui`, `ui/Panel`, `tangram`开头的话，那么就会去配置指定的地方去加载。例如：

* `ui/Button` => `/fe/code/path/esui/v1.0/ui/Button.js`
* `ui/Panel` => `/fe/code/path/esui/v1.2/ui/Panel.js`
* `js!tangram` => `/fe/code/path/third_party/tangram/v1.0/tangram.js`
* `css!themes/base` => `//www.baidu.com/css/styles/blue/base.css`

另外，需要支持为插件指定不同的的`paths`，语法如下：

```javascript
{
    baseUrl: '/fe/code/path',
    paths: {
        'css!': '//www.baidu.com/css/styles/blue',
        'css!foo': 'bar',
        'js!': '//www.google.com/js/gcl',
        'js!foo': 'bar'
    }
}
```

## 模块加载器插件

该文档不限定使用何种`AMD Loader`，但是一个`AMD Loader`应该支持至少三种插件（css，js，tpl）才能满足我们的业务需求。

### 插件语法

    [Plugin Module ID]![resource ID]

`Plugin Module Id`是插件的`moduleId`，例如`css`，`js`，`tpl`等等。`!`是分割符。

`resource ID`是`资源Id`，可以是`top-level`或者`relative`。如果`resource ID`是`relative`，那么相对的是当前`模块的Id`，而不是当前`模块Url`。例如：

```javascript
// src/Button.js
define( function( require, exports, module ){
    require( "css!./css/Button.css" );
    require( "css!base.css" );
    require( "tpl!./tpl/Button.tpl.html" );
});
```

如果当前模块的路径是`${root}/src/ui/Button.js`，那么该模块依赖的`Button.css`和`Button.tpl.html`的路径就应该分别是`${root}/src/css/ui/Button.css`，`${root}/src/tpl/Button.tpl.html`；该模块依赖的`base.css`的路径应该是`${baseUrl}/base.css`。

### css插件

参考上面的示例。如果`resource ID`省略后缀名的话，默认是`.css`；如果有后缀名，以具体的后缀名为准。例如：`.less`。

### js插件

用来加载不符合该文档规范的js文件，例如`jquery`，`tangram`等等。例如：

```javascript
// src/js/ui/Button.js
define( function( require, exports, module ) {
    require( "js!jquery" );
    require( "js!./tangram" );
});
```

### tpl插件

如果项目需要前端模板，需要通过tpl插件加载。tpl插件由模板引擎提供方实现。插件的语法应该跟上述`js`，`css`插件的语法保持一致，例如：

```javascript
require( "tpl!./foo.tpl.html" );
```

## FAQ

### 为什么不能采用define(moduleId, deps, factory)来定义模块？

`define(moduleId, deps, factory)`这种写法，很容易出现很长的deps，影响代码的风格。

```javascript
define(
    "module/id", 
    [
        "module/a", 
        "module/b", 
        "module/c"
    ], 
    function ( require ) {
        // blabla...
    }
);
```

构建工具对代码进行处理和编译时，允许将代码编译成这种风格，明确硬依赖。

### 相对于模块的Id和相对于模块Url有什么区别？

还是看 [erik的解释吧](https://github.com/ecomfe/edp/issues/13#issuecomment-14383810)



