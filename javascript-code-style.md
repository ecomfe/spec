# Javascript编码规范 (1.1)


## 简介

本文档主要的设计目标是Javascript开发风格保持一致，使容易被理解和被维护。


### 编撰

张立理、erik、顾轶灵、李玉北、王海洋、王杨、周莲洁。

本文档由`商业前端技术组`审校发布。


### 要求

在本文档中，使用的关键字会以中文+括号包含的关键字英文表示： *必须(MUST)* 。关键字"MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL"被定义在rfc2119中。


## 空格

在特定的位置加上空格有助于代码的可读性，以下位置 *必须(MUST)* 加上空格：

- 除括号外，所有运算符的前后，如
- 用作代码块起始的左大括号`{`前，包括`if`、`else`、`try`、`finally`这些关键字之后，以及**函数定义的参数列表**之后
- 以下关键字之后：`for`、`switch`、`while`、`function`
- 对象初始化（`{ ... }`）的每个属性名的冒号`:`后
- 所有逗号`,`后
- 单行的对象初始化（`{ ... }`）左大括号`{`后和右大括号`}`前

注意：函数声明与具名函数表达式，**函数名与括号之间** *不允许(MUST NOT)* 包含空格，以期和函数调用保持一致。以下是一个函数的正确声明方式：

```javascript
function foo(x, y, z) {
    // FunctionBody
}

var foo = function (x, y, z) {
    // FunctionBody
};

var foo = function foo(x, y, z) {
    // FunctionBody
};
```

## 对齐和缩进

*必须(MUST)* 采用**4个空格**为一次缩进， *不得(MUST NOT)* 采用TAB作为缩进。在以下情况下 *必须(MUST)* 缩进：

- 一个代码块与其父代码块相比 *必须(MUST)* 多一次缩进
- 未结束的语句在换行后 *必须(MUST)* 多一次缩进
- `switch`下的`case`和`default` *必须(MUST)* 缩进

## 换行

在以下位置*必须*换行：

- 每个独立语句结束后
- 运算符处换行时，运算符 *必须(MUST)* 在新行的行首
- 单行长度超过限制时

对于因为单行长度超过限制时产生的换行，参考**行长度**中的策略进行分隔。

不同行为或逻辑的语句集，中间可以通过一个以上的换行做视觉上的区分，更易阅读。

```javascript
// 仅为按逻辑换行的示例，不代表setStyle的最优实现
function setStyle(element, property, value) {
    if (element == null) {
        return;
    }

    element.style[property] = value;
}
```

### 换行风格

对于`if...else...`、`try...catch...finially`、`do...while`等语句，我们 *推荐(RECOMMENDED)* 使用 **在}号后添加一个换行** 的风格，使代码层次结构更清晰，阅读性更好。

```javascript
if (condition) {
    // some statements;
}
else {
    // some statements;
}

try {
    // some statements;
}
catch (ex) {
    // some statements;
}

do {
    // some statements;
}
while (condition);
```

也 *可以(MAY)* 使用符合[Code Conventions for the Java Programming Language](http://www.oracle.com/technetwork/java/codeconv-138413.html)的换行风格。

```javascript
if (condition) {
    // some statements;
} else {
    // some statements;
}

try {
    // some statements;
} catch (ex) {
    // some statements;
}

do {
    // some statements;
} while (condition);
```

无论使用哪种换行风格，在同一个项目下 *必须(MUST)* 保证风格一致。

## 行长度

每一行代码严格以**120**字符为最大长度，即一行包括前后的空格， *不得(MUST NOT)* 超过**120**个字符。

由于必定存在一些特殊的原因，导致一条语句的长度超过120， *应当(SHOULD)* 按以下原则进行切分：

### 字符串过长截断

按一定长度截断字符串，并使用`+`运算符进行连接。分隔字符串尽量按语义进行，如不要在一个完整的名词中间断开。

特别的，对于HTML片段的拼接，通过缩进，保持和HTML相同的结构：

```javascript
var html = '' // 此处用一个空字符串，以便整个HTML片段都在新行严格对齐
    + '<article>'
    +     '<h1>Title here</h1>'
    +     '<p>This is a paragraph</p>'
    +     '<footer>Complete</footer>'
    + '</article>';
```

也可使用数组来进行拼接，相对`+`运算更容易调整缩进：

```javascript
var html = [
    '<article>',
        '<h1>Title here</h1>',
        '<p>This is a paragraph</p>',
        '<footer>Complete</footer>',
    '</article>';
];
html = html.join(''); // 注意需要join
```

### 函数调用时参数过多

当参数过多，在一行书写函数调用语句会超过120个字符的限制时， *应当(SHOULD)* 将每个参数独立写在一行上，并将结束的右括号`)`独立一行，所有参数 *必须(MUST)* **增加一个缩进**，以控制每行的长度，如：

```javascript
// 注意每一个参数的缩进
foo(
    aVeryVeryLongArgument,
    anotherVeryLongArgument,
    callback
);
```

当参数较多，一行一个书写会导致过长时， *应当(SHOULD)* 按逻辑对参数进行组合，最经典的是`baidu.format`函数，如：

```javascript
// 将参数分为“模板”和“数据”两块
baidu.format(
    dateFormatTemplate,
    year, month, date, hour, minute, second
);


// 将参数分为“模板”、“日期”和“时间”
baidu.format(
    dateFormatTemplate,
    year, month, date, 
    hour, minute, second
);
```

### 三元运算符过长

三元运算符由3部分组成，因此其换行 *应当(SHOULD)* 根据每个部分的长度不同，形成3种不同的情况：

```javascript
// 无需换行
var result = condition ? resultA : resultB;

// 条件超长的情况
var result = thisIsAVeryVeryLongCondition
    ? resultA : resultB;

// 结果分支超长的情况
var result = condition
    ? thisIsAVeryVeryLongResult
    : resultB;
var result = condition
    ? resultA 
    : thisIsAVeryVeryLongResult;
```

*不得(MUST NOT)* 出现以下情况：

```javascript
// 最后一个结果很长，但不建议合并条件和第一分支
// 不要这么干
var result = condition ? resultA
    : thisIsAVeryVeryLongResult;
```

这种方法会导致语义上的分裂，即“条件和分支”在一行，“另一分支”在一行，没有按逻辑进行组织。


### 过长的逻辑条件组合

当因为较复杂的逻辑条件组合导致120个字符无法满足需求时， *应当(SHOULD)* 将每个条件独立一行，逻辑运算符**放置在行首**进行分隔，或将部分逻辑按逻辑组合进行分隔。最终将右括号`)`与左大括号`{`放在独立一行，保证与if内语句块能容易视觉辨识。如：

```javascript
// 注意逻辑运算符前的缩进
if (user.isAuthenticated()
    && user.isInRole('admin')
    && user.hasAuthority('add-admin')
    || user.hasAuthority('delete-admin')
) {
    // Code
}
```


### 过长的JSON和数组

如果对象属性较多导致每个属性一行占用空间过大， *可以(SHOULD)* 按语义或逻辑进行分组的组织，如：

```javascript
// 英文-数字的映射
var mapping = {
    one: 1, two: 2, three: 3, four: 4, five: 5,
    six: 6, seven: 7, eight: 8, nine: 9, ten: 10,
    eleven: 11, twelve: 12, thirteen: 13, fourteen: 14, fifteen: 15,
    sixteen: 16, seventeen: 17, eighteen: 18, nineteen: 19, twenty: 20
};
```

通过5个一组的分组，将每一行控制在合理的范围内，并且按逻辑进行了切分。

对于项目较多的数组，也 *可以(SHOULD)* 采用相同的方法，如：

```javascript
// 数字-英文的映射
var mapping = [
    'one', 'two', 'three', 'four', 'five',
    'six', 'seven', 'eight', 'nine', 'ten',
    'eleven', 'twelve', 'thirteen', 'fourteen', 'fifteen',
    'sixteen', 'seventeen', 'eighteen', 'nineteen', 'twenty'
];
```

### 单行和跨行参数混用

当函数调用时，传递的参数**大于或等于2个**，且**有一个或以上参数跨越多行**时， *应当(SHOULD)* 每一个参数独立一行，这通常出现在匿名函数或者对象初始化等作为参数时，如`setTimeout`函数等：

```javascript
setTimeout(
    function() {
        alert('hello');
    },
    200
);

order.data.read(
    'id=' + me.model.id, 
    function(data) {
        me.attchToModel(data.result);
        callback();
    }, 
    300
);
```

如果认为每个参数单独一行占用过多的空间，则 *应当(SHOULD)* 将跨域多行的参数独立出来为一个变量：

```javascript
function attachDataToModel() {
    me.attchToModel(data.result);
    callback();
}
order.data.read('id=' + me.model.id, attachDataToModel, 300);
```

### 数组和对象初始化的混用

*必须(MUST)* 严格按照每个对象的起始`{`和结束`}`在独立一行的风格书写，如：

```javascript
var array = [
    {
        // ...
    },
    {
        // ...
    }
];
```

## 命名

命名的方法通常有以下几类：

### 命名法说明

- camel命名法，形如`thisIsAnApple`
- pascal命名法，形如`ThisIsAnApple`
- 下划线命名法，形如`this_is_an_apple`
- 中划线命名法，形如`this-is-an-apple`

根据不同类型的内容， *必须(MUST)* 严格采用如下的命名法：

### 变量名

*必须(MUST)* 使用camel命名法

### 参数名

*必须(MUST)* 使用camel命名法

### 函数名

*必须(MUST)* 使用camel命名法

### 方法/属性

*必须(MUST)* 使用camel命名法

### 私有（保护）成员

*必须(MUST)* 以下划线`_`开头

### 常量名

*必须(MUST)* 使用全部大写的下划线命名法，如`IS_DEBUG_ENABLED`

### 类名

*必须(MUST)* 使用pascal命名法

### 枚举名

*必须(MUST)* 使用pascal命名法

### 枚举的属性

*必须(MUST)* 使用全部大写的下划线命名法

### 命名空间

*必须(MUST)* 使用camel命名法

### 语义

命名同时还需要关注语义，如：

- 变量名 *应当(SHOULD)* 使用名词
- **boolean**类型的 *应当(SHOULD)* 使用**is**、**has**等起头，表示其类型
- 函数名 *应当(SHOULD)* 用动宾短语
- 类名 *应当(SHOULD)* 用名词

## 语法

### 变量声明

一个`var`关键字 *必须(MUST)* 只**声明一个变量**。

变量 *必须(MUST)* 即用即声明， *不得(MUST NOT)* 在函数（或其它形式的代码块）起始位置统一声明所有变量。

### 字符串

字符串的起始和结束 *必须(MUST)* 使用单引号`'`。

### 对象、数组和正则

对象、数组和正则 *必须(MUST)* 优先使用其初始化器（`{...}`、`[...]`、`/.../`）声明，非必要 *不允许(MUST NOT)* 使用`new Object|Array|RegExp`。

如果一个对象的所有属性名均不是保留字，则该对象声明时，所有属性名 *不得(MUST NOT)* 添加引号，这包括属性名是数字的情况。

如果一个对象的属性名有一个或多个是保留字，则该对象声明时，**所有**属性名 *必须(MUST)* 添加引号。

### 内置原型

*不得(MUST NOT)* 修改内置对象的原型

### 继承

*建议(MAY)* 通过`baidu.inherit`或相似机制实现继承。

如自己实现继承， *必须(MUST)* 对constructor进行修正。

### 异常

允许使用异常，但在创建`Error`对象时， *必须(MUST)* 明确地传递`message`参数。


## 注释

良好的注释有利于代码阅读和自文档化，以下内容 *必须(MUST)* 包含以/\*\*开头和*/结尾的块注释，便于自文档化：

1. 文件
2. namespace
3. 类
4. 函数或方法
5. 类属性
6. 事件
7. 全局变量
8. 常量

自文档化的文档 *必须(MUST)* 说明what，而不是how。





### 类型定义

所有的类型定义都是以`{`开始, 以`}`结束，例如: {string}, {number}, {boolean}, {Object}, {Function}, {RegExp}, {Array}, {Date}。不仅仅局限于内置的类型，也可以是自定义的类型。比如定义了一个类`Person`，就可以使用它来定义一个参数和返回值的类型。

注意：对于基本类型{string}, {number}, {boolean}，首字母 *必须(MUST)* 小写。

<table>
    <thead>
        <tr>
            <td bgcolor="eeeeee">类型定义</th>
            <td bgcolor="eeeeee">语法示例</th>
            <td bgcolor="eeeeee">解释</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>原生类型</td>
            <td>String => {string}<br>Number => {number}<br>Boolean => {boolean}<br>Object => {Object}<br>Function => {Function}<br>RegExp => {RegExp}<br>Array => {Array}<br>Date => {Date}<br>...</td>
            <td>--</td>
        </tr>
        <tr>
            <td>单一类型集合</td>
            <td>{Array.&lt;string&gt;}</td>
            <td>string类型的数组</td>
        </tr>
        <tr>
            <td>多类型</td>
            <td>{(number｜boolean)}</td>
            <td>可能是number类型, 也可能是boolean类型</td>
        </tr>
        <tr>
            <td>允许为null</td>
            <td>{?number}</td>
            <td>可能是number, 也可能是null</td>
        </tr>
        <tr>
            <td>不允许为null</td>
            <td>{!Object}</td>
            <td>Object类型, 但不是null</td>
        </tr>
        <tr>
            <td>Function类型</td>
            <td>{function(number, boolean)}</td>
            <td>函数, 形参类型</td>
        </tr>
        <tr>
            <td>Function带返回值</td>
            <td>{function(number, boolean):string}</td>
            <td>函数, 形参, 返回值类型</td>
        </tr>
        <tr>
            <td>参数可选</td>
            <td>@param {string=} opt_name</td>
            <td>可选参数, =为类型后缀, opt_为形参前缀</td>
        </tr>
        <tr>
            <td>可变参数</td>
            <td>@param {...number} var_args</td>
            <td>变长参数,  ...为类型前缀, var_args为形参名</td>
        </tr>
        <tr>
            <td>任意类型</td>
            <td>{*}</td>
            <td>任意类型
                <br>@param {...*} var_args
                <br>@param {*=} opt_name
            </td>
        </tr>
    </tbody>
</table>


### 文件注释

文件 *必须(MUST)* 包含文件注释，文件注释 *必须(MUST)* 包含文件说明和开发者信息。文件说明用@file标识，开发者信息用@author标识。注释 *必须(MUST)* 以`/**`开始, 以`*/`结束。

推荐采用如下的文件注释（可以在自己的编辑器中配置模板文件）：

```javascript
/**
 * @file Describe the file
 * @author leeight(liyubei@baidu.com)
 */

// 这里是文件的内容
```

### 命名空间注释

命名空间 *必须(MUST)* 使用@namespace标识。

```javascript
/**
 * @namespace
 */
var baidu = {};
```

### 类注释

*必须(MUST)* 使用@constructor在类的构造函数上做标记。当类说明和构造函数说明需要区分时， *可以(SHOULD)* 使用@class进行类说明。

```javascript
/**
 * 描述
 *
 * @constructor
 */
function Person() {
    // constructor body
}
```

*必须(MUST)* 使用@extends标记类的继承信息。

```javascript
/**
 * 描述
 *
 * @constructor
 * @extends Person
 */
function Baiduer() {
    Person.call(this);
    // constructor body
}
baidu.inherits(Baiduer, Person);
```

使用包装方式扩展类成员时， *必须(MUST)* 通过@lends进行重新指向。

```javascript
/**
 * 类描述
 *
 * @constructor
 * @extends Person
 */
function Baiduer() {
    Person.call(this);
    // constructor body
}

baidu.extend(
    Baiduer.prototype, 
    /** @lends Baiduer.prototype */{
        _getLevel: function() {
            // TODO
        }
    }
);
```



类的属性或方法等成员信息 *必须(MUST)* 使用@public/@protected/@private中的任意一个，指明可访问性。

```javascript
/**
 * 类描述
 *
 * @constructor
 * @extends Person
 */
function Baiduer() {
    Person.call(this);

    /**
     * 属性描述
     * 
     * @type {string}
     * @private
     */
    this._level = 'T11';

    // constructor body
}
baidu.inherits(Baiduer, Person);

/**
 * 方法描述
 * 
 * @private
 * @return {string} 返回值描述
 */
Baiduer.prototype._getLevel = function() {
};
```


### 函数/方法注释

函数/方法注释 *必须(MUST)* 包含函数说明、参数和返回值。

参数和返回值注释 *必须(MUST)* 包含类型信息和说明。

如果形参是可选参数， *可以(SHALL)* 使用`opt_`为前缀。

```javascript
/**
 * 函数描述
 *
 * @param {string} p1 参数1的说明
 * @param {string} p2 参数2的说明，比较长
 * 那就换行了.
 * @param {number=} opt_p3 参数3的说明（可选）
 * @return {Object} 返回值描述
 */
function foo(p1, p2, opt_p3) {
    var p3 = opt_p3 || 10;
    return {
        p1 : p1,
        p2 : p2,
        p3 : p3
    };
}
```

对Object中各项的描述， *必须(MUST)* 使用@param， *不得(MUST NOT)* 使用@config。@config只能描述参数的直接1层属性。

```javascript
/**
 * 函数描述
 *
 * @param {Object} option 参数描述
 * @param {string} option.url option项描述
 * @param {string=} option.method option项描述，可选参数
 */
function foo(option) {
    // TODO
}
```

重写父类方法时， *应当(SHOULD)* 添加@override标识。

### 事件注释

*必须(MUST)* 用@event标识事件，事件参数的标识与方法描述的参数标识一样。

```javascript
/**
 * 值变更时触发
 *
 * @event
 * @param {Object} e e描述
 * @param {string} e.before before描述
 * @param {string} e.after after描述
 */
onchange: function (e) {
}
```

如果不带占位函数的事件， *可以(SHOULD)* 在fire时或构造函数中进行事件标识。此时 *必须(MUST)* 明确指定事件所属。

```javascript
Select.prototype.clickHandler = function () {
    /**
     * 值变更时触发
     *
     * @event Select#change
     * @param {Object} e e描述
     * @param {string} e.before before描述
     * @param {string} e.after after描述
     */
    this.fire('change', {...});
};
```

### 全局变量注释

全局变量 *必须(MUST)* 包含注释。全局变量注释 *必须(MUST)* 包含说明和类型信息。

```javascript
/**
 * 全局变量说明
 * 
 * @type {number}
 */
var currentStep = 1;
```

### 常量注释

常量 *必须(MUST)* 包含注释,且 *必须(MUST)* 使用@const标记，并包含说明和类型信息。

```javascript
/**
 * 常量说明
 *  
 * @const
 * @type {string}
 */
var REQUEST_URL = 'myurl.do';
```

### 细节注释

对于内部实现、不容易理解的逻辑说明、摘要信息等，我们可能需要编写细节注释。

细节注释 *必须(MUST)* 使用单行注释的//形式，并且在//后 *必须(MUST)* 跟一个空格。说明必须换行时，每行是一个单行注释的起始。

```javascript
function foo(p1, p2, opt_p3) {
    // 这里对具体内部逻辑进行说明
    // 说明太长需要换行
    for (...) {
        ....
    }
}
```

有时我们会使用一些特殊标记进行说明。特殊标记 *必须(MUST)* 使用单行注释的形式。下面列举了一些常用标记：

1. TODO: 有功能待实现。此时需要对将要实现的功能进行简单说明。
2. FIXME: 该处代码运行没问题，但可能由于时间赶或者其他原因，需要修正。此时需要对如何修正进行简单说明。
3. HACK: 为修正某些问题而写的不太好或者使用了某些诡异手段的代码。此时需要对思路或诡异手段进行描述。
4. XXX: 该处存在陷阱。此时需要对陷阱进行描述。


## 其它

#### `eval`和`with`

当代码中使用`eval`或`with`时，该代码必须由一个同级别工程师和一个高级别工程师进行Review。

## 参考

1. <https://developers.google.com/closure/compiler/docs/js-for-compiler#types>
2. <http://usejsdoc.org/>
3. <http://www.oracle.com/technetwork/java/codeconv-138413.html>
4. <http://www.gnu.org/prep/standards/standards.html>
