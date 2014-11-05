# 包结构规范 (1.1)

## 简介

该文档主要的设计目标是商业体系`前端`资源分包进行约定规范，使开发资源容易被共享和复用。

### 编撰

李玉北、erik、黄后锦、王杨、张立理、赵雷。

本文档由`商业运营体系前端技术组`审校发布。

### 要求

在本文档中，使用的关键字会以中文+括号包含的关键字英文表示：必须(MUST)。关键字"MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL"被定义在rfc2119中。

### 规范说明约定

以下规范文档中，以`${root}`表示包的根目录。

### 包开发说明

#### 包定义

`包`是实现某个独立功能，有复用价值的代码集。在具体的实现过程中， *必须(MUST)* 按照[模块和加载器规范](module.text)来开发和管理模块。

#### 模块定义

`包`中的模块定义时 *必须(MUST)* 采用匿名id`define( factory )`进行定义， *不允许(MUST NOT)* 使用`define( moduleId, factory )`。

#### 依赖管理

`包`中的模块对其他模块的依赖分成两种：`内部模块依赖`和`外部包依赖`。下面是关于模块依赖的管理说明：

1. 对`内部模块依赖`的情况， *必须(MUST)* 保证内部模块id与路径的对应关系。require依赖引用 *必须(MUST)* 使用`relative id`， *不允许(MUST NOT)* 使用`top-level id`。
2. 对`外部包依赖`的情况，require依赖引用 *必须(MUST)* 使用`top-level id`。

开发时，我们通常会做一些测试用例或示例，此时需要通过AMD Loader将当前包粘合到页面环境，并使其可运行。这时我们需要遵守一些规则：

1. 对`内部模块依赖`，AMD Loader配置 *推荐(RECOMMENDED)* 通过`packages`将`location`配置到`${root}`下的`src`目录， *不允许(MUST NOT)* 通过`paths`进行路径映射。
2. 对`外部包依赖`，请参照[项目目录结构规范](directory.md)将相关依赖包导入，并且 *必须(MUST)* 通过`packages`项配置AMD Loader。

```javascript
// 示例：ER package的test配置
require.config({
    packages: [
        {
            name: 'er',
            location: '../src',
            main: 'main'
        },
        {
            name: 'mini-event',
            location: '../dep/mini-event/1.0.0/src',
            main: 'main'
        },
        {
            name: 'etpl',
            location: '../dep/etpl/2.0.2/src',
            main: 'main'
        }
    ]
});
```

### 资源

*允许(SHALL)* 包含如下类型的资源：

脚本, 样式以及样式相关图片, 直接引用图片, html, 模板, 文档, 测试套件。

## 包描述文件

包描述文件 *必须(MUST)* 置于`${root}`下，命名为package.json， *必须(MUST)* 是一个UTF-8编码的严格JSON格式的文本文件。

### 必选字段

+ `name`: 包名。 *必须(MUST)* 为由camel命名法产生的字母组成的字符串。
+ `version`: 版本号。版本号 *必须(MUST)* 为字符串，需要符合[SemVer](http://semver.org/)的格式约定。
+ `maintainers`: 维护者列表。该字段 *必须(MUST)* 是一个数组，数组中每项 *必须(MUST)* 包含维护者的名称字段"name"与电子邮件字段"email"。

### 可选字段

+ `main`: 模块名，用来说明当前`包`的入口文件。如果包名为`foo`，那么执行`require("foo")`的时候，返回的内容就是当前模块`exports`的内容。
+ `description`: 描述信息。 *必须(MUST)* 为字符串。
+ `dependencies`: 依赖声明。该字段 *必须(MUST)* 是一个`JSON Object`，其中`key`为依赖的包名，`value`为版本号，支持如下的格式：
    + `version`
    + `>version`
    + `>=version`
    + `<version`
    + `<=version`
    + `*`: 任意版本
+ `devDependencies`: 类似`dependencies`的角色，但不是当前`包`必须的，只是在开发和调试的时候一些依赖的内容。
+ `contributors`: 贡献者列表。该字段 *必须(MUST)* 是一个数组，数组中每项 *必须(MUST)* 包含维护者的名称字段`name`与电子邮件字段`email`。
+ `homepage`: 该字段 *必须(MUST)* 为URL格式的字符串。

### 示例

 ```JavaScript
    {
        "name": "zrender",
        "version": "0.0.1",
        "maintainers": [
            {"name": "foo", "email": "foo@baidu.com"},
            {"name": "bar", "email": "bar@baidu.com", "url": "http://www.baidu.com/bar"}
        ],
        "dependencies": {
            "foo": "0.0.1",
            "bar": "*"
        },
        "devDependencies": {
            "uglify-js": "*"
        }
    }
```


## 包目录结构

### 示例

    ${root}/
        package.json
        README.md
        src/
            css/[?]
            img/
            main.js
        dep/
            foo/
            bar/
            jquery/
            tangram/
            mustache/
            ...
        test/
        doc/

### package.json

`package.json` *必须(MUST)* 直接放在包顶层目录`${root}`中。

### src

按照通常的理解，一个`包项目`不应该特别复杂。下面是一个简单的例子，详细的目录划分方法和原则请参考 [项目目录结构规范](directory.md) 。

    ${root}/
        src/
            main.js
            Control.js
            Button.js
            css/
                button.css
        package.json
        README.md

### dep

用来存放`dependencies package`的代码， *不允许(MUST NOT)* 放置任何其它的内容。


### 参考资料

1. <https://npmjs.org/doc/json.html>
2. <http://twitter.github.com/bower/>
3. <http://yeoman.io/>
4. <http://semver.org/>
