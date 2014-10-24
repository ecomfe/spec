# Less 编码规范 (1.1)

## 简介

该文档主要的设计目标是提高 Less 文档的团队一致性与可维护性。

Less 代码的基本规范和原则与 [CSS 编码规范](https://github.com/ecomfe/spec/blob/master/css-style-guide.md) 保持一致。

### 编撰

erik、顾轶灵、黄后锦、李玉北、赵雷。

本文档由`商业运营体系前端技术组`审校发布。

### 要求

在本文档中，使用的关键字会以中文+括号包含的关键字英文表示：必须（MUST）。关键字"MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL"被定义在rfc2119中。

***

## 编码

使用UTF-8编码。*不得*（MUST NOT）包含BOM信息。

***

## 代码组织

代码*必须*（MUST）按如下形式按顺序组织：

1. `@import`
2. 变量声明
3. 样式声明

```less
// ✓
@import "est/all.less";

@default-text-color: #333;

.page {
    width: 960px;
    margin: 0 auto;
}
```

***

## `@import` 语句

`@import` 语句引用的文件*必须*（MUST）写在一对引号内，`.less` 后缀*不得*（MUST NOT）省略（与引入 CSS 文件时的路径格式一致）。引号使用 `'` 和 `"` 均可，但在同一项目内*必须*（MUST）统一。

```less
// ✗
@import 'est/all';
@import "my/mixins.less";

// ✓
@import "est/all.less";
@import "my/mixins.less";
```

***

## 空格

### 属性、变量

选择器和 `{` 之间*必须*（MUST）保留一个空格。

属性名后的冒号（`:`）与属性值之间*必须*（MUST）保留一个空格，冒号前*不得*（MUST NOT）保留空格。

定义变量时冒号（`:`）与变量值之间*必须*（MUST）保留一个空格，冒号前*不得*（MUST NOT）保留空格。

在用逗号（`,`）分隔的列表（Less 函数参数列表、以 `,` 分隔的属性值等）中，逗号后*必须*（MUST）保留一个空格，逗号前*不得*（MUST NOT）保留空格。

```less
// ✗
.box{
    @w:50px;
    @h :30px;
    width:@w;
    height :@h;
    color: rgba(255,255,255,.3);
    transition: width 1s,height 3s;
}

// ✓
.box {
    @w: 50px;
    @h: 30px;
    width: @w;
    height: @h;
    transition: width 1s, height 3s;
}
```

### 运算

`+` / `-` / `*` / `/` 四个运算符两侧*必须*（MUST）保留一个空格。`+` / `-` 两侧的操作数*必须*（MUST）有相同的单位，如果其中一个是变量，另一个数值*必须*（MUST）书写单位。

```less
// ✗
@a: 200px;
@b: (@a+100)*2;

// ✓
@a: 200px;
@b: (@a + 100px) * 2;
```

### 混入（Mixin）

Mixin 和后面的空格之间*不得*（MUST NOT）包含空格。在给 mixin 传递参数时，在参数分隔符（`,` / `;`）后*必须*（MUST）保留一个空格：

```less
// ✗
.box {
    .size(30px,20px);
    .clearfix ();
}

// ✓
.box {
    .size(30px, 20px);
    .clearfix();
}
```

***

## 选择器

当多个选择器共享一个声明块时，每个选择器声明*必须*（MUST）独占一行。

```less
// ✗
h1, h2, h3 {
    font-weight: 700;
}

// ✓
h1,
h2,
h3 {
    font-weight: 700;
}
```

Class 命名不得以样式信息进行描述，如 `.float-right`、`text-red` 等。

***

## 省略与缩写

### 缩写

多个属性定义可以使用缩写时， *尽量*（SHOULD）使用缩写。缩写更清晰字节数更少。常见缩写有 `margin`、`border`、`padding`、`font`、`list-style` 等。在书写时*必须*（MUST）考量缩写展开后是否有不需要覆盖的属性内容被修改，从而带来副作用。

### 数值

对于处于 `(0, 1)` 范围内的数值，小数点前的 `0` *可以*（MAY）省略，同一项目中*必须*（MUST）保持一致。

```less
// ✗
transition-duration: 0.5s, .7s;

// ✓
transition-duration: .5s, .7s;
```

### 0 值

当属性值为 0 时，*必须*（MUST）省略可省的单位（长度单位如 `px`、`em`，不包括时间、角度等如 `s`、`deg`）。

```less
// ✗
margin-top: 0px;

// ✓
margin-top: 0;
```

### 颜色

颜色定义*必须*（MUST）使用 `#rrggbb` 格式定义，并在可能时*尽量*（SHOULD）缩写为 `#rgb` 形式，且避免直接使用颜色名称与 `rgb()` 表达式。

```less
// ✗
border-color: red;
color: rgb(254, 254, 254);

// ✓
border-color: #f00;
color: #fefefe;
```

### 私有属性前缀

同一属性有不同私有前缀的，*尽量*（SHOULD）按前缀长度降序书写，标准形式*必须*（MUST）写在最后。且这一组属性以第一条的位置为准，*尽量*（SHOULD）按冒号的位置对齐。

```less
// ✓
.box {
    -webkit-transform: rotate(30deg);
       -moz-transform: rotate(30deg);
        -ms-transform: rotate(30deg);
         -o-transform: rotate(30deg);
            transform: rotate(30deg);
}
```

### 其他

*可以*（MAY）在无其他更好解决办法时使用 CSS hack，并且*尽量*（SHOULD）使用简单的属性名 hack 如 `_zoom`、`*margin`。

*可以*（MAY）但谨慎使用 IE 滤镜。需要注意的是，IE 滤镜中图片的 URL 是以页面路径作为相对目录，而不是 CSS 文件路径。

***

## 嵌套和缩进

*必须*（MUST）采用 4 个空格为一次缩进， *不得*（MUST NOT）采用 TAB 作为缩进。

嵌套的声明块前*必须*（MUST）增加一次缩进，有多个声明块共享命名空间时*尽量*（SHOULD）嵌套书写，避免选择器的重复。

但是需注意的是，*尽量*（SHOULD）仅在必须区分上下文时才引入嵌套关系（在嵌套书写前先考虑如果不能嵌套，会如何书写选择器）。

```less
// ✗
.main .title {
  font-weight: 700;
}

.main .content {
  line-height: 1.5;
}

.main {
.warning {
  font-weight: 700;
}
    
  .comment-form {
    #comment:invalid {
      color: red;
    }
  }
}

// ✓
.main {
    .title {
        font-weight: 700;
    }

    .content {
        line-height: 1.5;
    }
    
    .warning {
        font-weight: 700;
    }
}

#comment:invalid {
    color: red;
}
```

***

## 变量

Less 的变量值总是以同一作用域下最后一个同名变量为准，务必注意后面的设定会覆盖所有之前的设定。

变量命名*必须*（MUST）采用 `@foo-bar` 形式，*不得*（MUST NOT）使用 `@fooBar` 形式。

```less
// ✗
@sidebarWidth: 200px;
@width:800px;

// ✓
@sidebar-width: 200px;
@width: 800px;
```

***

## 继承

使用继承时，如果在声明块内书写 `:extend` 语句，*必须*（MUST）写在开头：

```less
// ✗
.sub {
    color: red;
    &:extend(.mod all);
}

// ✓
.sub {
    &:extend(.mod all);
    color: red;
}
```

***

## 混入（Mixin）

在定义 mixin 时，如果 mixin 名称不是一个需要使用的 className，*必须*（MUST）加上括号，否则即使不被调用也会输出到 CSS 中。

```less
// ✗
.big-text {
    font-size: 2em;
}

h3 {
    .big-text;
}

// ✓
.big-text() {
    font-size: 2em;
}

h3 {
    .big-text();
}
```

如果混入的是本身不输出内容的 mixin，*必须*（MUST）在 mixin 后添加括号（即使不传参数），以区分这是否是一个 className（修改以后是否会影响 HTML）。

```less
// ✗
.box {
    .clearfix;
    .size (20px);
}

// ✓
.box {
    .clearfix();
    .size(20px);
}
```

Mixin 的参数分隔符使用 `,` 和 `;` 均可，但在同一项目中*必须*（MUST）保持统一。

***

## 命名空间

变量和 mixin 在命名时*必须*（MUST）遵循如下原则：

* 一个项目只能引入一个无命名前缀的基础样式库（如 est）
* 业务代码和其他被引入的样式代码中，变量和 mixin 必须有项目或库的前缀

***

## 字符串

在进行字符串转义时，使用 `~""` 表达式与 `e()` 函数均可，但在同一项目中*必须*（MUST）保持一致。

字符串两侧的引号*必须*（MUST）使用 `"`。

## JS 表达式

*可以*（MAY）使用 JS 表达式（<code>~\`\`</code>）生成属性值或变量，其中包含的字符串两侧的引号*尽量*（SHOULD）使用单引号（`'`）。

***

## 注释

单行注释*尽量*（SHOULD）使用 `//` 方式。

```less
// Hide everything
* {
    display: none;
}
```
