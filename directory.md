# 项目目录结构规范


## 简介

该文档主要的设计目标是项目开发的目录结构保持一致，使容易理解并方便构建与管理。

### 编撰

李玉北、erik、黄后锦、王杨、张立理、赵雷、陈新乐、刘恺华。

本文档由`商业运营体系前端技术组`审校发布。

### 要求

在本文档中，使用的关键字会以中文+括号包含的关键字英文表示：必须(MUST)。关键字"MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL"被定义在rfc2119中。


### 规范说明约定

以下规范文档中：

1. `项目`包含但不限于`业务项目`和`包项目`。
2. `${root}`表示`项目`的根目录。

<a name="restype"></a>

## 资源分类

`资源`分成两大类：

1. `源代码资源`：指开发者编写的源代码，包括`js`、`html`、`css`、`template`等。
2. `内容资源`：指希望做为内容提供给访问者的资源，包括`图片`、`字体`、`flash`、`pdf`等。


## 目录命名原则

1. 简洁。有习惯性缩写的单词 *必须(MUST)* 采用容易理解的缩写。如：源代码目录使用`src`，不使用`source`。下面是更多例子：
    1. `img`: 图片。 *不允许(MUST NOT)* 使用`image`、`images`、`imgs`等。
    2. `js`: javascript脚本。 *不允许(MUST NOT)* 使用`script`、`scripts`等。
    3. `css`: 样式表。 *不允许(MUST NOT)* 使用`style`、`styles`等。
    4. `swf`: flash。 *不允许(MUST NOT)* 使用`flash`等。
    5. `src`: 源文件目录。 *不允许(MUST NOT)* 使用`source`等。
    6. `dep`: 引入的第三方依赖包目录。 *不允许(MUST NOT)* 使用`lib`、`library`、`dependency`等。
2.  *不允许(MUST NOT)* 使用复数形式。如：`imgs`、`docs`是不被允许的。



## 目录划分

<a name="root"></a>
### ${root}目录结构划分

在${root}下，目录结构 *必须(MUST)* 按照`职能`进行划分， *不允许(MUST NOT)* 将`资源类型`或`业务逻辑`划分的目录直接置于${root}下。

常用的目录有`src`、`doc`、`dep`、`test`等。详细请参考[一级目录详细说明](#level1)

    ${root}/
        src/
        test/
        doc/
        dep/
        ...

### 业务项目目录结构划分

`业务项目`的${root}目录结构划分遵循[${root}目录结构划分](#root)。


#### 项目代号

业务项目 *可以(SHOULD)* 为项目起一个代号名称。代号名称 *必须(MUST)* 为一个单词，不宜过长。例：北斗的项目代号为`triones`，哥伦布的项目代号为`clb`，百度锦囊的项目代号为`jn`。项目代号有利于区分不同项目，为未来项目之间的重用留下扩展的后路。

在项目开发时，通常会使用如下[加载器配置](module.md#config)，将项目代号指向`src`。

```javascript
{
    baseUrl: '${docroot}',
    paths: {
        'triones': 'src'
    }
}
```


#### 根据业务逻辑划分src目录结构

`业务项目`的`src`目录内，绝大多数情况 *应当(SHOULD)* 根据`业务逻辑`划分目录结构。划分出的子目录（比如[例子](#bizdirexample)中的`biz1`）我们称为`业务目录`。

`src`下 *必须(MUST)* 只包含`业务目录`与`common`目录。`业务公共资源` *必须(MUST)* 命名为`common`。`common`目录做为`业务公共资源`的目录，也视如`业务目录`。

    ${root}/
        src/
            common/
            biz1/
                subbiz1/
                subbiz2/
            biz2/

较小规模的`业务项目`（如投放端），`src`目录允许视如`业务目录`，直接按照[业务目录划分原则](#bizdirprinciple)划分目录结构。

    ${root}/
        src/
            foo.js


<a name="bizdirprinciple"></a>
#### 业务目录划分原则

1. `JS资源` *不允许(MUST NOT)* 按`资源类型`划分目录， *必须(MUST)* 按`业务逻辑`划分目录。`JS资源`应直接置于`业务目录`下。即：`业务目录`下不允许出现`js`目录。
2. 除`JS资源`外的`源文件资源`，当资源数量较多时，为方便管理， *允许(SHOULD)* 按`资源类型`划分目录。即：`业务目录`下允许出现`css`、`tpl`目录。
3. `内容资源` *允许(SHOULD)* 按`资源类型`划分目录。即：`业务目录`下允许出现`img`、`swf`、`font`目录。
4. `业务目录`中，如果文件太多不好管理，需要划分子目录时，也 *必须(MUST)* 继续遵守根据`业务逻辑`划分的原则，划分子业务。如：下面例子中的`subbiz1`。


通常，对于一个`业务目录`， *鼓励(SHOULD)* 将业务相关的`源文件资源`都直接置于`业务目录`下。

    biz1/
        img/
            add_button.png
        add.js
        add.tpl.html
        add.css

`业务目录`下`源文件资源`数量较多时，我们第一直觉应该是：是否业务划分不够细？是否应该划分子业务，建立子业务目录？

    biz2/
        subbiz1/
            list.js
            list.tpl.html
            list.css
        subbiz2/

遇到确实是一个业务整体，无法划分子业务时， *允许(MAY)* 将非`JS资源`按`资源类型`划分目录进行管理。

    biz1/
        css/
            add.css
            edit.css
            remove.css
            img/
                add_button.png
        tpl/
            add.html
            edit.html
            remove.html
        add.js
        edit.js
        remove.js


`源文件资源`和`内容资源`请参考[资源分类](#restype)章节，常用`资源目录`请参考[资源目录](#resdir)章节，常用`业务目录`请参考[业务目录](#bizdir)章节。


<a name="bizdirexample"></a>
#### 业务项目目录划分示例

    ${root}/
        src/
            common/
                img/
                    sprites.png
                    logo.png
                conf.js
                layout.css
            biz1/
                img/
                    add_button.png
                add.js
                add.tpl.html
                add.less
            biz2/
                subbiz1/
                    list.js
                    list.tpl.html
                    list.css
                subbiz2/
        dep/
            er/
                src/
                test/
            esui/
                src/
                test/
        test/
        doc/
        index.html
        main.html
        ......

<a name="packagedir"></a>
### 包项目目录结构划分

`包项目`的${root}目录结构划分遵循[${root}目录结构划分](#root)。

<a name="packagesrc"></a>
#### 包项目src目录结构划分

`包`是实现某个独立功能，有复用价值的代码集。按照通常的理解，一个`包项目`不应该特别复杂。

所以，`包`可视如一个不太复杂的`业务`，其`src`下的划分原则与`业务项目`的[业务目录划分原则](#bizdirprinciple)保持一致。

    ${root}/
        src/
            css/
                img/
                    sprites.png
                table.css
                button.css
                select.css
            main.js
            Control.js
            InputControl.js
            Button.js
            Table.js
            Select.js
        test/
        doc/
        package.json
        ...




## 常用目录

<a name="level1"></a>

### 一级目录

直接置于`${root}`下的目录称作`一级目录`。一级目录 *必须(MUST)* 具有某种`职能`属性。

除了下面列举的一些常见目录之外，`${root}`下面也可以放置一些跟项目发布相关的文件，例如`build.sh`，`build.xml`，`Makefile`，`Gruntfile`等等.

#### src

`src`目录用于存放开发时源文件，发布时 *必须(MUST)* 被删除。


#### dep

`dep`目录用于存放`项目`引入依赖的第三方包。该目录下的内容通过平台工具管理，项目开发人员 *不允许(MUST NOT)* 更改`dep`目录下第三方包的任何内容。

当项目需要修改引入的第三方代码时，第三方包应将源码直接置于`${root}/src`目录下，规则见该目录下的规定。

更多关于`包`的内容请参考 [包结构规范](package.md)


#### tool

`tool`目录用于存放开发时或构建阶段使用的工具。该目录在发布时 *必须(MUST)* 被删除。


#### test

`test`目录用于存放测试用例以及开发阶段的模拟数据。该目录在发布时 *必须(MUST)* 被删除。


#### doc

`doc`目录用于存放项目文档。项目文档可能是开发者维护的文档，也可能是通过工具生成的文档。


#### entry

`entry`目录用于存放项目的`页面入口文件`，通常是上线后可被直接访问的静态页面。

`RIA项目`通常会包含较少的`页面入口文件`，常见的是`main.html`，这些文件 *可以(SHOULD)* 直接放在`${root}`目录下。

    ${root}/
        src/
            common/
                conf.js
            card/
            gold/
            message/
        index.html
        main.html
        ......


`多页面项目`通常`页面入口文件`较多， *可以(SHOULD)* 统一放在`entry`目录中，按`业务逻辑`命名。

    ${root}/
        src/
            common/
                conf.js
            card/
            gold/
            message/
        entry/
            card.html
            gold.html
            message.html
            ......


项目在发布的时候，构建工具可以`页面入口文件`为入口进行分析和编译。


`RIA项目`经过构建工具编译后，目录结构可能如下：

    output/
        asset/
            js/
            css/
            tpl/
            img/
        index.html
        main.html

`多页面项目`经过构建工具编译后，目录结构可能如下：

    output/
        card/
            asset/
                js/
                css/
                img/
            index.html
        gold/
            asset/
                js/
                css/
                img/
            index.html

#### asset

`asset`目录用于存放用于`线上访问`的静态资源。

通常构建工具会对`src`目录和`dep`目录下的资源进行分析、合并与压缩等，生成到`asset`目录下。所以该目录尽量避免手工管理。下面是一个构建工具生成后的`asset`目录示例：

    ${root}/
        asset/
            js/
                loader.js
                build.js
            css/
                common.css
                img/
            tpl/
                build.tpl.html
            img/
            ...

<a name="resdir"></a>


### 资源目录

按`资源`类型命名的目录称作`资源目录`。`资源目录` *不允许(MUST NOT)* 直接置于${root}下。


#### js

`js`目录可用于存放`js`资源文件（包含可编译成`js`的`coffeescript`等语言）。`js`文件后缀名 *必须(MUST)* 为.js，`coffeescript文件`后缀名 *必须(MUST)* 为.coffee。

`js`目录内 *必须(MUST)* 存放`js`资源文件，但`js`资源文件不一定（MAY NOT）存放于`js`目录下：

1. 对于`src`目录，`js`资源文件 *不允许(MUST NOT)* 存放于`js`目录下。
2. 对于`asset`目录，`js`资源文件 *可以(SHOULD)* 存放于`js`目录下，视构建行为决定。
3. 对于其他`一级目录`内，`js`资源文件 *可以(SHOULD)* 不存放于`js`目录下。

#### css

`css`目录可用于存放`css资源文件`（包含`less`，`sass`等动态样式表语言）。`css`文件后缀名 *必须(MUST)* 为.css，`less`文件后缀名 *必须(MUST)* 为`.less`。

`css`目录内 *必须(MUST)* 存放`css`资源文件，但`css`资源文件不一定（MAY NOT）存放于`css`目录下：

1. 对于`src`目录，`css`资源文件 *可以(SHOULD)* 存放于`业务目录`下，也 *可以(SHOULD)* 存放于`css`目录下。
2. 对于`asset`目录，`css`资源文件 *可以(SHOULD)* 存放于`css`目录下，视构建行为决定。
3. 对于其他`一级目录`内，`css`资源文件 *可以(SHOULD)* 不存放于`css`目录下。

关于css引用图片的位置说明，请参考[img](#imgdir)章节。


<a name="imgdir"></a>

#### img

`img`目录可用于存放`图片资源文件`。包括`页面直接引用`的图片与`css引用`图片。常见的图片资源有`gif/jpg/png/svg/bmp`等。

对于`css`引用的图片， *必须(MUST)* 放在`./img`目录下，`.`代表当前`css`资源所在的目录。

对于`页面直接引用`的图片：

1. 被多页面引用的图片 *应该(SHOULD)* 放在`${root}/src/common/img`目录下。
2. 单一页面引用的图片 *应该(SHOULD)* 放在`./img`目录下，`.`代表当前页面所在的目录。


#### tpl

`tpl`目录可用于存放`template`资源文件。`template`资源文件后缀名 *可以(SHOULD)* 为`.html`或`.tpl`。

通常，对于`RIA`系统，`template`资源文件采用`.html`后缀使其能够被`xhr`加载。


#### font

`font`目录可用于存放字体资源文件。常见的字体资源有`tff/woff/svg`等。


#### swf

`swf`目录可用于存放`flash`资源文件。`flash`资源文件 *不允许(MUST NOT)* 置于`img`目录中。


<a name="bizdir"></a>

### 业务目录

<a name="commondir"></a>

#### common

`common`目录为业务公共目录，用于存放业务项目的业务公共文件。所以，根据`业务逻辑`划分目录结构时，业务逻辑命名 *不允许(MUST NOT)* 为`common`。


## FAQ

### 为啥biz下面没资源类型目录了？

如果在`biz`下继续划分`资源目录`，代码的结构可能就是这样子了：

    ${root}/
        src/
            biz1/
                js/
                    list.js

当我们需要使用`list.js`的时候，必须写如下的代码：`require("../biz1/js/list")`，但是从逻辑上说，更合理的写法应该是`require("../biz1/list")`。因此我们不推荐在`biz`下面对源代码资源划分目录。

