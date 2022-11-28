
# JavaScript编码规范

## 1 前言

JavaScript 在百度一直有着广泛的应用，特别是在浏览器端的行为管理。本文档的目标是使 JavaScript 代码风格保持一致，容易被理解和被维护。

虽然本文档是针对 JavaScript 设计的，但是在使用各种 JavaScript 的预编译语言时(如 TypeScript 等)时，适用的部分也应尽量遵循本文档的约定。

## 2 代码风格

### 2.1 文件

##### [建议] 在文件结尾处，保留一个空行。

解释：

文件结尾保留空行有利于各类工具对文件进行处理，如命令行的 `cat` 命令等。

你可以使用 [EditorConfig](https://editorconfig.org/) 中的 `insert_final_newline = true` 配置自动化处理本条规则。

##### [建议] 使用LF作为换行符。

解释：

全局使用统一的换行符有助于自动化工具对文本进行处理，绝大部分社区工具依赖于换行符为 `LF` 进行工作。

你可以使用以下方式设置换行符：

- 使用 [EditorConfig](https://editorconfig.org/) 的 `end_of_line = lf` 配置。
- 使用 `git config --global core.autocrlf true` 确保代码提交与下载保持一致性。

### 2.2 结构

#### 2.2.1 缩进

##### [强制] 使用 `4` 个空格做为一个缩进层级，不允许使用 `2` 个空格 或 `tab` 字符。

##### [强制] `switch` 下的 `case` 和 `default` 必须增加一个缩进层级。

示例：

```javascript
// good
switch (variable) {
    case '1':
        // do...
        break;

    case '2':
        // do...
        break;

    default:
        // do...
}

// bad
switch (variable) {
case '1':
    // do...
    break;

case '2':
    // do...
    break;

default:
    // do...
}
```

##### [建议]当 `case` 下有局部变量声明时，在 `case` 层级增加大括号。

示例：

```javascript
// good
switch (variable) {
    case '1': {
        let result = `Hello ${variable}`;
        doSomeWork(result);
        break;
    }
    // ...
}

// bad
switch (variable) {
    case '1':
        let result = `Hello ${variable}`;
        doSomeWork(result);
        break;
    // ...
}
```

#### 2.2.2 空格

##### [强制] 二元运算符两侧必须有一个空格，一元运算符与操作对象之间不允许有空格。

示例：

```javascript
let a = !arr.length;
a++;
a = b + c;
```

##### [强制] 用作代码块起始的左花括号 `{` 前必须有一个空格。

示例：

```javascript
// good
if (condition) {
}

while (condition) {
}

function funcName() {
}

// bad
if (condition){
}

while (condition){
}

function funcName(){
}
```

##### [强制] `if / else / for / while / function / switch / do / try / catch / finally` 关键字后，必须有一个空格。

示例：

```javascript
// good
if (condition) {
}

while (condition) {
}

(function () {
})();

// bad
if(condition) {
}

while(condition) {
}

(function() {
})();
```

##### [强制] 在对象创建时，属性中的 `:` 之后必须有空格，`:` 之前不允许有空格。

示例：

```javascript
// good
let obj = {
    a: 1,
    b: 2,
    c: 3
};

// bad
let obj = {
    a : 1,
    b:2,
    c :3
};
```

##### [强制] 函数声明、具名函数表达式、函数调用中，函数名和 `(` 之间不允许有空格。

示例：

```javascript
// good
function funcName() {
}

let funcName = function funcName() {
};

funcName();

// bad
function funcName () {
}

let funcName = function funcName () {
};

funcName ();
```

##### [强制] `,` 和 `;` 前不允许有空格。如果不位于行尾，`,` 和 `;` 后必须跟一个空格。

示例：

```javascript
// good
callFunc(a, b);

// bad
callFunc(a , b) ;
```

##### [强制] 在函数调用、函数声明、括号表达式、属性访问、`if / for / while / switch / catch` 等语句中，`()` 和 `[]` 内紧贴括号部分不允许有空格。

示例：

```javascript
// good

callFunc(param1, param2, param3);

save(this.list[this.indexes[i]]);

needIncream && (variable += increament);

if (num > list.length) {
}

while (len--) {
}


// bad

callFunc( param1, param2, param3 );

save( this.list[ this.indexes[ i ] ] );

needIncreament && ( variable += increament );

if ( num > list.length ) {
}

while ( len-- ) {
}
```

##### [强制] 单行声明的数组与对象，如果包含元素，`{}` 和 `[]` 内紧贴括号部分不允许包含空格。

解释：

声明包含元素的数组与对象，只有当内部元素的形式较为简单时，才允许写在一行。元素复杂的情况，还是应该换行书写。


示例：

```javascript
// good
let arr1 = [];
let arr2 = [1, 2, 3];
let obj1 = {};
let obj2 = {name: 'obj'};
let obj3 = {
    name: 'obj',
    age: 20,
    sex: 1
};

// bad
let arr1 = [ ];
let arr2 = [ 1, 2, 3 ];
let obj1 = { };
let obj2 = { name: 'obj' };
let obj3 = {name: 'obj', age: 20, sex: 1};
```

##### [强制] 行尾不得有多余的空格。

解释：

除模板字符串外，一行代码的尾部不得有多余的空格。

跨行的模板字符串中允许有行尾空格，但出于格式化工具的行为一致性的考虑，仍不建设这样操作，如需要空格，建议在模板字符串中用变量占位声明。

```javascript
let message = `
Hello, ${'    '}
Goodbye.
`;
```

#### 2.2.3 换行


##### [强制] 每个独立语句结束后必须换行。

##### [强制] 每行不得超过 **120** 个字符。

解释：

超长的不可分割的代码允许例外，比如复杂的正则表达式。长字符串不在例外之列。

对于长字符串，如果具备可分割性，且字符串需要被开发者阅读以了解其内容，则建议对字符串进行分隔，确保每行字符数小于120。

##### [强制] 运算符处换行时，运算符必须在新行的行首。

示例：

```javascript
// good
if (user.isAuthenticated()
    && user.isInRole('admin')
    && user.hasAuthority('add-admin')
    || user.hasAuthority('delete-admin')
) {
    // Code
}

let result = number1 + number2 + number3
    + number4 + number5;


// bad
if (user.isAuthenticated() &&
    user.isInRole('admin') &&
    user.hasAuthority('add-admin') ||
    user.hasAuthority('delete-admin')) {
    // Code
}

let result = number1 + number2 + number3 +
    number4 + number5;
```

##### [强制] 在函数声明、函数表达式、函数调用、对象创建、数组创建、`for` 语句等场景中，不允许在 `,` 或 `;` 前换行。

示例：

```javascript
// good
let obj = {
    a: 1,
    b: 2,
    c: 3
};

foo(
    aVeryVeryLongArgument,
    anotherVeryLongArgument,
    callback
);


// bad
let obj = {
    a: 1
    , b: 2
    , c: 3
};

foo(
    aVeryVeryLongArgument
    , anotherVeryLongArgument
    , callback
);
```

##### [建议] 不同行为或逻辑的语句集，使用空行隔开，更易阅读。

示例：

```javascript
// 仅为按逻辑换行的示例，不代表setStyle的最优实现
function setStyle(element, property, value) {
    if (element == null) {
        return;
    }

    element.style[property] = value;
}
```

##### [建议] 在语句的行长度超过 `120` 时，根据逻辑条件合理缩进。

示例：

```javascript
// 较复杂的逻辑条件组合，将每个条件独立一行，逻辑运算符放置在行首进行分隔，或将部分逻辑按逻辑组合进行分隔。
// 建议最终将右括号 ) 与左大括号 { 放在独立一行，保证与 `if` 内语句块能容易视觉辨识。
if (user.isAuthenticated()
    && user.isInRole('admin')
    && user.hasAuthority('add-admin')
    || user.hasAuthority('delete-admin')
) {
    // Code
}

// 按一定长度截断字符串，并使用 + 运算符进行连接。
// 分隔字符串尽量按语义进行，如不要在一个完整的名词中间断开。
// 特别的，对于 HTML 片段的拼接，通过缩进，保持和 HTML 相同的结构。
let html = '' // 此处用一个空字符串，以便整个 HTML 片段都在新行严格对齐
    + '<article>'
    +     '<h1>Title here</h1>'
    +     '<p>This is a paragraph</p>'
    +     '<footer>Complete</footer>'
    + '</article>';

// 也可使用数组来进行拼接，相对 `+` 更容易调整缩进。
let html = [
    '<article>',
        '<h1>Title here</h1>',
        '<p>This is a paragraph</p>',
        '<footer>Complete</footer>',
    '</article>',
];
html = html.join('');

// 你也可以用 `dedent` 这个库和模板字符串来实现
let html = dedent`
    <article>
        <h1>Title here</h1>
        <p>This is a paragraph</p>
        <footer>Complete</footer>
    </article>
`;

// 当参数过多时，将每个参数独立写在一行上，并将结束的右括号 ) 独立一行。
// 所有参数必须增加一个缩进。
foo(
    aVeryVeryLongArgument,
    anotherVeryLongArgument,
    callback
);

// 也可以按逻辑对参数进行组合。
// 最经典的是 baidu.format 函数，调用时将参数分为“模板”和“数据”两块
baidu.format(
    dateFormatTemplate,
    year, month, date, hour, minute, second
);

// 当函数调用时，如果有一个或以上参数跨越多行，应当每一个参数独立一行。
// 这通常出现在匿名函数或者对象初始化等作为参数时，如 `setTimeout` 函数等。
setTimeout(
    function () {
        alert('hello');
    },
    200
);

order.data.read(
    'id=' + me.model.id,
    function (data) {
        me.attchToModel(data.result);
        callback();
    },
    300
);

// 链式调用较长时采用缩进进行调整。
$('#items')
    .find('.selected')
    .highlight()
    .end();

// 三元运算符由3部分组成，因此其换行应当根据每个部分的长度不同，形成不同的情况。
let result = thisIsAVeryVeryLongCondition
    ? resultA : resultB;

let result = condition
    ? thisIsAVeryVeryLongResult
    : resultB;

// 数组和对象初始化的混用，严格按照每个对象的 `{` 和结束 `}` 在独立一行的风格书写。
let array = [
    {
        // ...
    },
    {
        // ...
    }
];
```

#### 2.2.4 语句


##### [强制] 不得省略语句结束的分号。

对于函数声明（Function Declaration）、类声明（Class Declaration），它们不属于语句（Statement），因此不需要以分号结束。

对于 `export default` 语句，参考其后部分的类型，如果是函数、类声明，同样不需要分号。

示例：

```javascript
// good
function foo() {
    // ...
}

class Foo {
    // ...
}

let foo = function () {
    // ...
}; // 这里需要有分号

export default function foo() {
    // ...
} // 这里不需要分号

// bad
function foo() {
    // ...
};

class Foo {
    // ...
};

let foo = function () {
    // ...
}

export default function foo() {
    // ...
};
```

##### [强制] 在 `if / else / for / do / while` 语句中，即使只有一行，也不得省略块 `{...}`。

示例：

```javascript
// good
if (condition) {
    callFunc();
}

// bad
if (condition) callFunc();
if (condition)
    callFunc();
```

##### [强制] 函数定义结束不允许添加分号。

示例：

```javascript
// good
function funcName() {
}

// bad
function funcName() {
};

// 如果是函数表达式，分号是不允许省略的。
let funcName = function () {
};
```

##### [强制] `IIFE` 必须在函数表达式外添加 `(`，非 `IIFE` 不得在函数表达式外添加 `(`。

解释：

IIFE = Immediately-Invoked Function Expression.

额外的 ( 能够让代码在阅读的一开始就能判断函数是否立即被调用，进而明白接下来代码的用途。而不是一直拖到底部才恍然大悟。


示例：

```javascript
// good
let task = (function () {
   // Code
   return result;
})();

let func = function () {
};


// bad
let task = function () {
    // Code
    return result;
}();

let func = (function () {
});
```





### 2.3 命名


##### [强制] **变量**使用 `camelCase` 命名法。

示例：

```javascript
let loadingModules = {};
```

##### [强制] **常量**使用 `const` 关键字声明。

解释：

此处常量特指**引用不会改变、内容也不会改变**的不变量。

示例：

```javascript
const LOCAL_IP = '127.0.0.1';
```

##### [强制] **常量**使用 `CONST_CASE` 命名法。

示例：

```javascript
const HTML_ENTITY = {};
```

##### [强制] **函数**使用 `camelCase` 命名法。

示例：

```javascript
function stringFormat(source) {
}
```

##### [强制] 函数的**参数**使用 `camelCase` 命名法。

示例：

```javascript
function hear(theBells) {
}
```


##### [强制] **类**使用 `PascalCase` 命名法。

示例：

```javascript
class TextNode {
}
```

##### [强制] 类的**方法**和**属性**使用 `camelCase` 命名法

示例：

```javascript
class TextNode {
    value = '';
    engine = null;

    constructor(value, engine) {
        this.value = value;
        this.engine = engine;
    }

    clone() {
        return this;
    }
}
```

##### [强制] **枚举变量**使用 `PascalCase`，**枚举的属性**使用 `CONST_CASE` 命名法。

此条指 JavaScript 下的枚举模块，TypeScript 中的 `enum` 关键字参考 TypeScript 规范相关条目。

示例：

```javascript
const Permission = {
    READ: 1,
    READ_WRITE: 2,
};
```

##### [建议] 由多个单词组成的缩写词，在命名中，根据当前命名法和出现的位置，所有字母的大小写与首字母的大小写保持一致。

如果对缩写词有自己的命名方案，需要在项目内保持一致。

示例：

```javascript
function XMLParser() {
}

function insertHTML(element, html) {
}

let httpRequest = new HTTPRequest();
```

##### [强制] **类名**使用 `名词`。

示例：

```javascript
class Engine {
}
```

##### [建议] **函数名**、**方法名**使用 `动宾短语`。

示例：

```javascript
function getStyle(element) {
}

class Engine {
    formatText(text) {
        // ...
    }
}
```

##### [建议] `boolean` 类型的变量使用 `is` 或 `has` 开头。

示例：

```javascript
let isReady = false;
let hasMoreCommands = false;
```

##### [建议] `Promise` 对象用 `动宾短语的进行时` 表达。

示例：

```javascript
let loadingData = ajax.get('url');
loadingData.then(callback);
```

### 2.4 注释


#### 2.4.1 单行注释

##### [强制] 单行的注释以 `//` 起始，后跟一个空格，独占一行的情况下缩进与下一行被注释说明的代码一致。

示例：

```javascript
function processUserInput(input) {
    // 对字符串进行转义后才能正常解析
    let escaped = escapeString(input);
    outputToUser(escaped); // 用户看到的就是转义后的，没问题
}
```

#### 2.4.2 多行注释

##### [建议] 避免使用 `/*...*/` 这样的多行注释。有多行注释内容时，使用多个单行注释。

示例：

```javascript
// good
// 这是一段复杂的逻辑：
// 1. ...
// 2. ...
function work() {
    // ...
}

// bad
/*
 * 这是一段复杂的逻辑：
 * 1. ...
 * 2. ...
 */
function work() {
    // ...
}
```

#### 2.4.3 待办注释

##### [建议] 待办类注释结构包含类型关键字、说明、负责人。

格式为 `// 类型: 信息 @待办人`，注意类型后有一个分号，待办人前用 `@` 标识。

```javascript
// TODO: 这里还缺一个分支没处理 @zhangsan
```

##### [建议] 使用统一、固定的待办类关键字。

建议使用如下关键字：

- TODO：有功能待实现。此时需要对将要实现的功能进行简单说明。
- NOTE：提示阅读者着重阅读。
- WARN：警告此处代码非常重要。
- HACK：为修正某些问题而写的不太好或者使用了某些诡异手段的代码。此时需要对思路或诡异手段进行描述。
- FIXME：该处代码运行没问题，但可能由于时间赶或者其他原因，需要修正。此时需要对如何修正进行简单说明。
- DEPRECATED：该部分代码已经过期作废。

你可以使用如 [Better Comments 插件](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments)等工具高亮待办类的注释，以取得最佳的代码阅读体验。

#### 2.4.4 文档化注释

##### [强制] 为了便于代码阅读和自文档化，以下内容必须包含以 `/**...*/` 形式的块注释中。

解释：

1. 文件
3. 类
4. 函数或方法
5. 类属性
6. 事件
7. 全局变量
8. 常量

##### [强制] 文档注释前必须空一行。

```javascript
function foo() {
}

/**
 * 这里是下一个函数的注释，前面要空一行
 */
function bar() {
}
```

##### [建议] 自文档化的文档说明 what，而不是 how。

#### 2.4.5 类型定义

**类型定义类的注释均不涉及 TypeScript 编写的代码。**

##### [强制] 类型定义都是以 `{` 开始, 以 `}` 结束。

解释：

常用类型如：{string}, {number}, {boolean}, {Object}, {Function}, {RegExp}, {Array}, {Date}。

类型不仅局限于内置的类型，也可以是自定义的类型。比如定义了一个类 Developer，就可以使用它来定义一个参数和返回值的类型。


##### [强制] 对于基本类型 {string}, {number}, {boolean}，首字母必须小写。

| 类型定义 | 语法示例 | 解释 |
| ------- | ------- | --- |
|String|{string}|--|
|Number|{number}|--|
|Boolean|{boolean}|--|
|Object|{Object}|--|
|Function|{Function}|--|
|RegExp|{RegExp}|--|
|Array|{Array}|--|
|Date|{Date}|--|
|单一类型集合|{Array.&lt;string&gt;}|string 类型的数组|
|多类型|{(number｜boolean)}|可能是 number 类型, 也可能是 boolean 类型|
|允许为null|{?number}|可能是 number, 也可能是 null|
|不允许为null|{!Object}|Object 类型, 但不是 null|
|Function类型|{function(number, boolean)}|函数, 形参类型|
|Function带返回值|{function(number, boolean):string}|函数, 形参, 返回值类型|
|Promise|Promise.&lt;resolveType, rejectType&gt;|Promise，成功返回的数据类型，失败返回的错误类型|
|参数可选|@param {string=} name|可选参数, =为类型后缀|
|可变参数|@param {...number} args|变长参数,  ...为类型前缀|
|任意类型|{*}|任意类型|
|可选任意类型|@param {*=} name|可选参数，类型不限|
|可变任意类型|@param {...*} args|变长参数，类型不限|

#### 2.4.6 类注释

##### [建议] 使用 `@class` 标记类或构造函数。

解释：

对于使用对象 `constructor` 属性来定义的构造函数，可以使用 `@constructor` 来标记。

示例：

```javascript
/**
 * 描述
 *
 * @class
 */
class Developer {
}
```

##### [建议] 使用 `@extends` 标记类的继承信息。

示例：

```javascript
/**
 * 描述
 *
 * @class
 * @extends Developer
 */
class Fronteer extends Developer {
    constructor() {
        super();
    }
}
```

##### [强制] 类的属性或方法等成员信息不是 `public` 的，应使用 `@protected` 或 `@private` 标识可访问性。

解释：

生成的文档中将有可访问性的标记，避免用户直接使用非 `public` 的属性或方法。

示例：

```javascript
/**
 * 类描述
 *
 * @class
 * @extends Developer
 */
class Fronteer {
    /**
     * @type {string}
     * @private
     */
    value = 'T12';

    /**
     * 方法描述
     *
     * @private
     * @return {string} 返回值描述
     */
    getLevel() {
        return this.value;
    };
};

```

#### 2.4.7 函数/方法注释

##### [建议] 函数/方法注释包含函数说明，有参数和返回值时必须使用注释标识。

解释：

当 `return` 关键字仅作退出函数/方法使用时，无须对返回值作注释标识。


##### [建议] 参数和返回值注释包含类型信息，且不允许省略参数的说明。

##### [建议] 对 `Object` 类型的对象中各项的描述， 使用 `@param` 标识。

示例：

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

##### [建议] 重写父类方法时， 应当添加 `@override` 标识。如果重写的形参个数、类型、顺序和返回值类型均未发生变化，可省略 `@param`、`@return`，仅用 `@override` 标识，否则仍应作完整注释。

解释：

简而言之，当子类重写的方法能直接套用父类的方法注释时可省略对参数与返回值的注释。

#### 2.4.8 事件注释

##### [建议] 必须使用 `@event` 标识事件，事件参数的标识与方法描述的参数标识相同。

示例：

```javascript
/**
 * 值变更时触发
 *
 * @event Select#change
 * @param {Object} e e描述
 * @param {string} e.before before描述
 * @param {string} e.after after描述
 */
this.fire(
    'change',
    {
        before: 'foo',
        after: 'bar'
    }
);
```

##### [建议] 在会广播事件的函数前使用 `@fires` 标识广播的事件，在广播事件代码前使用 `@event` 标识事件。

##### [建议] 对于事件对象的注释，使用 `@param` 标识，生成文档时可读性更好。

示例：

```javascript
/**
 * 点击处理
 *
 * @fires Select#change
 * @private
 */
class Select {
    clickHandler() {
        /**
         * 值变更时触发
         *
         * @event Select#change
         * @param {Object} e e描述
         * @param {string} e.before before描述
         * @param {string} e.after after描述
         */
        this.fire(
            'change',
            {
                before: 'foo',
                after: 'bar'
            }
        );
    }
};
```

#### 2.4.9 常量注释

##### [建议] 常量使用 `@const` 标记，并包含说明和类型信息。

示例：

```javascript
/**
 * 常量说明
 *
 * @const
 * @type {string}
 */
const REQUEST_URL = 'myurl.do';
```

#### 2.4.10 复杂类型注释


##### [建议] 对于类型未定义的复杂结构的注释，可以使用 `@typedef` 标识来定义。

示例：

```javascript
// `namespaceA~` 可以换成其它 namepaths 前缀，目的是为了生成文档中能显示 `@typedef` 定义的类型和链接。
/**
 * 服务器
 *
 * @typedef {Object} namespaceA~Server
 * @property {string} host 主机
 * @property {number} port 端口
 */

/**
 * 服务器列表
 *
 * @type {Array.<namespaceA~Server>}
 */
let servers = [
    {
        host: '1.2.3.4',
        port: 8080
    },
    {
        host: '1.2.3.5',
        port: 8081
    }
];
```

#### 2.4.11 细节注释


对于内部实现、不容易理解的逻辑说明、摘要信息等，我们可能需要编写细节注释。

#### [建议] 细节注释遵循单行注释的格式。说明必须换行时，每行是一个单行注释的起始。

示例：

```javascript
function foo(p1, p2, opt_p3) {
    // 这里对具体内部逻辑进行说明
    // 说明太长需要换行
    for (...) {
        ....
    }
}
```

## 3 语言特性

### 3.1 变量

##### [强制] 变量均使用 `let` 或 `const` 定义，不得使用 `var` 定义变量。

解释：

`var` 定义的变量作用域过大，有更大的使用风险。

```javascript
//good
let name = 'MyName';
const ip = '127.0.0.1';

// bad
let name = 'MyName';
```

##### [强制] 变量、函数在使用前必须先定义。

解释：

不得直接向未定义的变量赋值来污染全局环境。


示例：

```javascript
// good
let name = 'MyName';

// bad
name = 'MyName';
```

原则上不建议使用全局变量，对于已有的全局变量或第三方框架引入的全局变量，需要根据检查工具的语法标识。

示例：

```javascript
/* globals jQuery */
let element = jQuery('#element-id');
```

##### [强制] 每个 `let` 或 `const` 只能声明一个变量。

解释：

一个 `let` 或 `const` 声明多个变量，容易导致较长的行长度，并且在修改时容易造成逗号和分号的混淆。


示例：

```javascript
// good
let hangModules = [];
let missModules = [];
let visited = {};

// bad
let hangModules = [],
    missModules = [],
    visited = {};
```

##### [强制] 变量必须 `即用即声明`，不得在函数或其它形式的代码块起始位置统一声明所有变量。

解释：

变量声明与使用的距离越远，出现的跨度越大，代码的阅读与维护成本越高。虽然JavaScript的变量是函数作用域，还是应该根据编程中的意图，缩小变量出现的距离空间。

示例：

```javascript
// good
function kv2List(source) {
    let list = [];

    for (let key in source) {
        if (source.hasOwnProperty(key)) {
            let item = {
                k: key,
                v: source[key]
            };

            list.push(item);
        }
    }

    return list;
}

// bad
function kv2List(source) {
    let list = [];
    let key;
    let item;

    for (key in source) {
        if (source.hasOwnProperty(key)) {
            item = {
                k: key,
                v: source[key]
            };

            list.push(item);
        }
    }

    return list;
}
```

### 3.2 条件

##### [强制] 在 Equality Expression 中使用类型严格的 `===`。仅当判断 `null` 或 `undefined` 时，允许使用 `== null`。

解释：

使用 `===` 可以避免等于判断中隐式的类型转换。


示例：

```javascript
// good
if (age === 30) {
    // ......
}

// bad
if (age == 30) {
    // ......
}
```

##### [建议] 按执行频率排列分支的顺序。

解释：

按执行频率排列分支的顺序好处是：

1. 阅读的人容易找到最常见的情况，增加可读性。
2. 提高执行效率。

##### [建议] 在逻辑实现中，考虑尽早返回。

解释：

将较短的逻辑块放在分支中，并使用 `return` 语句尽早使函数返回，可以有效减少 `if / else if` 的分支数量和代码的缩进量，使得代码整体结构更好、更易阅读。

```javascript
// good
function foo(value) {
    if (value < 0) {
        workWithNegativeValue(value);
        return; // 尽早返回
    }

    // 后续不用写 `else`
    let workingValue = Math.min(value, 1024);
    workWithPositiveValue(workingValue);
}

// bad
function foo(value) {
    if (value < 0) {
        workWithNegativeValue(value);
    }
    else {
        // 多了一层缩进，代码显得更复杂
        let workingValue = Math.min(value, 1024);
        workWithPositiveValue(workingValue);
    }
}
```

##### [建议] 对于相同变量或表达式的多值条件，用 `switch` 代替 `if`。

示例：

```javascript
// good
switch (typeof variable) {
    case 'object':
        // ......
        break;
    case 'number':
    case 'boolean':
    case 'string':
        // ......
        break;
}

// bad
let type = typeof variable;
if (type === 'object') {
    // ......
}
else if (type === 'number' || type === 'boolean' || type === 'string') {
    // ......
}
```

##### [建议] 如果函数或全局中的 `else` 块后没有任何语句，可以删除 `else`。

解释：

当一个 `else` 分支前的其它分支均通过 `return` 或 `throw` 等方式中断了函数时，该段逻辑并不需要 `else` 分支包裹，可以通过删除 `else` 部分来减少缩进，提升代码可读性。

示例：

```javascript
// good
function getName() {
    if (name) {
        return name;
    }

    return 'unnamed';
}

// bad
function getName() {
    if (name) {
        return name;
    }
    else {
        return 'unnamed';
    }
}
```

### 3.3 循环


##### [建议] 如无必要，不要在循环体中包含函数表达式，事先将函数提取到循环体外。

解释：

循环体中的函数表达式，运行过程中会生成循环次数个函数对象。

仅在依赖循环中的闭包变量，如 `i` 或 `length` 时，需要在循环体中声明函数。


示例：

```javascript
// good
function clicker() {
    // ......
}

for (let i = 0; i < elements.length; i++) {
    let element = elements[i];
    addListener(element, 'click', clicker);
}


// bad
for (let i = 0; i < elements.length; i++) {
    let element = elements[i];
    addListener(element, 'click', function () {});
}
```

##### [建议] 对循环内多次使用的高访问成本的不变值，在循环外用变量缓存。

解释：

在 DOM 等环境下，部分属性的访问有很高的成本，如 `offsetWidth` 会引起重排，此时需要将该值放在循环体外缓存，避免多次读取产生大量的性能损失。

示例：

```javascript
// good
let width = wrap.offsetWidth + 'px';
for (let i = 0; i < elements.length; i++) {
    let element = elements[i];
    element.style.width = width;
    // ......
}


// bad
for (let i = 0; i < elements.length; i++) {
    let element = elements[i];
    element.style.width = wrap.offsetWidth + 'px';
    // ......
}
```

### 3.4 类型

#### 3.4.1 类型检测

##### [建议] 类型检测优先使用 `typeof`。对象类型检测使用 `instanceof`。`null` 或 `undefined` 的检测使用 `== null`。

示例：

```javascript
// string
typeof variable === 'string'

// number
typeof variable === 'number'

// boolean
typeof variable === 'boolean'

// Function
typeof variable === 'function'

// Object
typeof variable === 'object'

// RegExp
variable instanceof RegExp

// Array
variable instanceof Array

// null
variable === null

// null or undefined
variable == null

// undefined
typeof variable === 'undefined'
```

#### 3.4.2 类型转换

##### [建议] 转换成 `string` 时，使用 `String(value)` 、 `toString()` 或模板字符串。

示例：

```javascript
// good
String(number);
number.toString();
`${number}`;

// bad
number + '';
```

##### [建议] 转换成 `number` 时，通常使用 `+`。

示例：

```javascript
// good
+str;

// bad
Number(str);
```

##### [建议] `string` 转换成 `number`，要转换的字符串结尾包含非数字并期望忽略时，使用 `parseInt`。

示例：

```javascript
let width = '200px';
parseInt(width, 10);
```

##### [强制] 使用 `parseInt` 时，必须指定进制。

示例：

```javascript
// good
parseInt(str, 10);

// bad
parseInt(str);
```

##### [建议] 转换成 `boolean` 时，使用 `!!`。

示例：

```javascript
let num = 3.14;
!!num;
```

##### [建议] `number` 去除小数点，使用 `Math.floor` / `Math.round` / `Math.ceil` / `Math.trunc`，不使用 `parseInt`。

示例：

```javascript
// good
let num = 3.14;
Math.ceil(num);

// bad
let num = 3.14;
parseInt(num, 10);
```

### 3.5 字符串

##### [强制] 字符串开头和结束使用单引号 `'`。

解释：

1. 输入单引号不需要按住 `shift`，方便输入。
2. 实际使用中，字符串经常用来拼接 HTML。为方便 HTML 中包含双引号而不需要转义写法。

在字符串本身包含单引号（`'`）时，则可以使用模板字符串代替。

示例：

```javascript
let str = '我是一个字符串';
let html = '<div class="cls">拼接HTML可以省去双引号转义</div>';
let code = `console.log('Hello World')`;
```

##### [建议] 在合适场景下，使用模板字符串、 `数组` 或 `+` 拼接字符串。

解释：

1. 使用 `+` 拼接字符串，如果拼接的全部是 StringLiteral，压缩工具可以对其进行自动合并的优化。所以，静态字符串建议使用 `+` 拼接。
2. 对于存在变量的、有多行的，可以使用模板字符串。
3. 对于每一小块显著有语义性和关联性，并通过统一的字符连接的，可以使用数组。

示例：

```javascript
// 使用数组拼接字符串
let elements = [
    // 推荐换行开始并缩进开始第一个字符串, 对齐代码, 方便阅读.
    '<ul>',
        '<li>第一项</li>',
        '<li>第二项</li>',
    '</ul>'
];
let html = elements.join('');

// 配合 `dedent` 使用模板字符串
let html = dedent`
    <ul>
        <li>第一项</li>
        <li>第二项</li>
    </ul>
`;

// 使用 `+` 拼接字符串
let html = '' // 建议第一个为空字符串, 第二个换行开始并缩进开始, 对齐代码, 方便阅读
    + '<ul>',
    +    '<li>第一项</li>',
    +    '<li>第二项</li>',
    + '</ul>';
```

##### [强制] 使用字符串拼接的方式生成HTML，需要根据语境进行合理的转义。

解释：

在 JavaScript 中拼接，并且最终将输出到页面中的字符串，需要进行合理转义，以防止安全漏洞。下面的示例代码为场景说明，不能直接运行。

示例：

```javascript
// HTML 转义
let str = '<p>' + htmlEncode(content) + '</p>';

// HTML 转义
let str = '<input type="text" value="' + htmlEncode(value) + '">';

// URL 转义
let str = '<a href="/?key=' + htmlEncode(urlEncode(value)) + '">link</a>';

// JavaScript字符串 转义 + HTML 转义
let str = '<button onclick="check(\'' + htmlEncode(strLiteral(name)) + '\')">提交</button>';
```

### 3.6 对象


##### [强制] 使用对象字面量 `{}` 创建新 `Object`。

在特殊情况下，可以使用 `Object.create(null)` 创建一个特殊的没有原型的对象。

示例：

```javascript
// good
let obj = {};

// bad
let obj = new Object();
```

##### [建议] 对象创建时，如果一个对象的所有 `属性` 均可以不添加引号，建议所有 `属性` 不添加引号。

示例：

```javascript
let info = {
    name: 'someone',
    age: 28
};
```

##### [强制] 不允许修改和扩展任何原生对象和宿主对象的原型。

示例：

```javascript
// 以下行为绝对禁止
String.prototype.trim = function () {
};
```

##### [建议] 属性访问时，尽量使用 `.`。

解释：

属性名符合 Identifier 的要求，就可以通过 `.` 来访问，否则就只能通过 `[expr]` 方式访问。

通常在 JavaScript 中声明的对象，属性命名是使用 Camel 命名法，用 `.` 来访问更清晰简洁。部分特殊的属性（比如来自后端的 JSON ），可能采用不寻常的命名方式，可以通过 `[expr]` 方式访问。


示例：

```javascript
info.age;
info['more-info'];
```

##### [建议] `for in` 遍历对象时, 使用 `hasOwnProperty` 过滤掉原型中的属性。

示例：

```javascript
let newInfo = {};
for (let key in info) {
    if (info.hasOwnProperty(key)) {
        newInfo[key] = info[key];
    }
}
```

### 3.7 数组


##### [强制] 使用数组字面量 `[]` 创建新数组，除非想要创建的是指定长度的数组。

示例：

```javascript
// good
let arr = [];

// bad
let arr = new Array();
```

##### [强制] 遍历数组不使用 `for in`。

解释：

数组对象可能存在数字以外的属性, 这种情况下 `for in` 不会得到正确结果。

示例：

```javascript
let arr = ['a', 'b', 'c'];

// 这里仅作演示, 实际中应使用 Object 类型
arr.other = 'other things';

// 正确的遍历方式
for (let i = 0, len = arr.length; i < len; i++) {
    console.log(i);
}

// 错误的遍历方式
for (let i in arr) {
    console.log(i);
}
```

##### [建议] 不因为性能的原因自己实现数组排序功能，尽量使用数组的 `sort` 方法。

解释：

自己实现的常规排序算法，在性能上并不优于数组默认的，当数据特点鲜明，适合使用桶排时，才考虑使用自定义的排序实现。

### 3.8 函数

#### 3.8.1 函数长度

##### [建议] 一个函数的长度控制在 **50** 行以内。

解释：

将过多的逻辑单元混在一个大函数中，易导致难以维护。一个清晰易懂的函数应该完成单一的逻辑单元。复杂的操作应进一步抽取，通过函数的调用来体现流程。

特定算法等不可分割的逻辑允许例外。


示例：

```javascript
function syncViewStateOnUserAction() {
    if (x.checked) {
        y.checked = true;
        z.value = '';
    }
    else {
        y.checked = false;
    }

    if (a.value) {
        warning.innerText = '';
        submitButton.disabled = false;
    }
    else {
        warning.innerText = 'Please enter it';
        submitButton.disabled = true;
    }
}

// 直接阅读该函数会难以明确其主线逻辑，因此下方是一种更合理的表达方式：

function syncViewStateOnUserAction() {
    syncXStateToView();
    checkAAvailability();
}

function syncXStateToView() {
    y.checked = x.checked;

    if (x.checked) {
        z.value = '';
    }
}

function checkAAvailability() {
    if (a.value) {
        clearWarnignForA();
    }
    else {
        displayWarningForAMissing();
    }
}
```

##### [建议] 一个函数的缩进层级小于 **6** 层。

解释：

过多的层级代表着函数复杂度太大，需要合理拆解函数。

包含JSX的部分可以有限地超过 6 层，但依然要有一个合理的限制，不应有过深的嵌套。

#### 3.8.2 参数设计


##### [建议] 一个函数的参数控制在 **6**** 个以内。

解释：

除去不定长参数以外，函数具备不同逻辑意义的参数建议控制在 **6** 个以内，过多参数会导致维护难度增大。

##### [建议] 通过 `options` 参数传递非数据输入型参数。

解释：

有些函数的参数并不是作为算法的输入，而是对算法的某些分支条件判断之用，此类参数建议通过一个 `options` 参数传递。

如下函数：

```javascript
/**
 * 移除某个元素
 *
 * @param {Node} element 需要移除的元素
 * @param {boolean} removeEventListeners 是否同时将所有注册在元素上的事件移除
 */
function removeElement(element, removeEventListeners) {
    element.parent.removeChild(element);

    if (removeEventListeners) {
        element.clearEventListeners();
    }
}
```

可以转换为下面的签名：

```javascript
/**
 * 移除某个元素
 *
 * @param {Node} element 需要移除的元素
 * @param {Object} options 相关的逻辑配置
 * @param {boolean} options.removeEventListeners 是否同时将所有注册在元素上的事件移除
 */
function removeElement(element, options) {
    element.parent.removeChild(element);

    if (options.removeEventListeners) {
        element.clearEventListeners();
    }
}
```

这种模式有几个显著的优势：

- `boolean` 型的配置项具备名称，从调用的代码上更易理解其表达的逻辑意义。
- 当配置项有增长时，无需无休止地增加参数个数，不会出现 `removeElement(element, true, false, false, 3)` 这样难以理解的调用代码。
- 当部分配置参数可选时，多个参数的形式非常难处理重载逻辑，而使用一个 options 对象只需判断属性是否存在，实现得以简化。

#### 3.8.3 空函数

##### [建议] 空函数不使用 `new Function()` 的形式。

示例：

```javascript
let emptyFunction = function () {};
```

##### [建议] 对于性能有高要求的场合，建议存在一个空函数的常量，供多处使用共享。

示例：

```javascript
const EMPTY_FUNCTION = function () {};

function dispose() {
    source.setProgressHandler(EMPTY_FUNCTION);
    source.setFinishHandler(EMPTY_FUNCTION);
}
```

### 3.9 面向对象

##### [强制] 自定义事件的事件名就当与对应框架相符。

解释：

当使用某个框架时，API 设计中的自定义事件名也就当符合该框架官方事件的命名，一些参考：

- DOM：使用全小写，如`dragstart`。
- NodeJS：使用 `camelCase` 的形式，如 `unhandledRejection` 。
- VSCode：使用 `camelCase` 的形式，如 `onDebugResolve` 。

##### [建议] 自定义事件只能有一个 `event` 参数。如果事件需要传递较多信息，应仔细设计事件对象。

解释：

一个事件对象的好处有：

1. 顺序无关，避免事件监听者需要记忆参数顺序。
2. 每个事件信息都可以根据需要提供或者不提供，更自由。
3. 扩展方便，未来添加事件信息时，无需考虑会破坏监听器参数形式而无法向后兼容。

### 3.10 动态特性

#### 3.10.1 eval

##### [强制] 避免使用直接 `eval` 函数。

解释：

直接 `eval`，指的是以函数方式调用 `eval` 的调用方法。直接 `eval` 调用执行代码的作用域为本地作用域，应当避免。

如果有特殊情况需要使用直接 `eval`，需在代码中用详细的注释说明为何必须使用直接 `eval`，不能使用其它动态执行代码的方式，同时需要其他资深工程师进行 Code Review。

##### [建议] 尽量避免使用 `eval` 函数。


#### 3.10.2 动态执行代码

##### [建议] 使用 `new Function` 执行动态代码。

解释：

通过 `new Function` 生成的函数作用域是全局使用域，不会影响当当前的本地作用域。如果有动态代码执行的需求，建议使用 `new Function`。


示例：

```javascript
let handler = new Function('x', 'y', 'return x + y;');
let result = handler($('#x').val(), $('#y').val());
```

#### 3.10.3 with

##### [强制] 避免使用 `with` 关键字。

解释：

使用 `with` 可能会增加代码的复杂度，不利于阅读和管理；也会对性能有影响。大多数使用 `with` 的场景都能使用其他方式较好的替代。所以，尽量不要使用 `with`。

#### 3.10.4 delete

##### [建议] 减少 `delete` 的使用。

解释：

如果没有特别的需求，减少或避免使用 `delete`。`delete` 的使用会破坏部分 JavaScript 引擎的性能优化。

#### 3.10.5 对象属性

##### [建议] 避免修改外部传入的对象。

解释：

JavaScript 因其脚本语言的动态特性，当一个对象未被 seal 或 freeze 时，可以任意添加、删除、修改属性值。

但是随意地对 非自身控制的对象 进行修改，很容易造成代码在不可预知的情况下出现问题。因此，设计良好的组件、函数应该避免对外部传入的对象的修改。

下面代码的 **selectNode** 方法修改了由外部传入的 **dataSource** 对象。如果 **datasource** 用在其它场合（如另一个 Tree 实例）下，会造成状态的混乱。

```javascript
class Tree {
    constructor(dataSource) {
        this.dataSource = dataSource;
    }

    selectNode(id) {
        // 从dataSource中找出节点对象
        let node = this.findNode(id);
        if (node) {
            node.selected = true;
            this.flushView();
        }
    }
}
```

对于此类场景，需要使用额外的对象来维护，使用由自身控制，不与外部产生任何交互的 **selectedNodeIndex** 对象来维护节点的选中状态，不对 **dataSource** 作任何修改。

```javascript
class Tree {
    constructor(dataSource) {
        this.dataSource = dataSource;
        this.selectedNodeIndex = {};
    }

    selectNode(id) {
        // 从dataSource中找出节点对象
        let node = this.findNode(id);

        if (node) {
            this.selectedNodeIndex[id] = true;
            this.flushView();
        }
    }
}
```

除此之外，也可以通过 `deepClone` 等手段将自身维护的对象与外部传入的分离，保证不会相互影响。

## 4 浏览器环境

### 4.1 DOM

#### 4.1.1 元素获取

##### [建议] 对于单个元素，尽可能使用 `document.getElementById` 获取，避免使用`document.all`。

##### [建议] 对于多个元素的集合，尽可能使用 `context.getElementsByTagName` 获取。其中 `context` 可以为 `document` 或其他元素。指定 `tagName` 参数为 `*` 可以获得所有子元素。

##### [建议] 操作一个活性的元素集合时，尽量缓存集合长度。如需多次操作同一集合，则应将集合转为数组。

解释：

原生获取元素集合的结果并不直接引用 DOM 元素，而是对索引进行读取，所以 DOM 结构的改变会实时反映到结果中。

你可以使用 `querySelectorAll` 来获得静态的元素集合，也可以避免此类问题。

示例：

```html
<div></div>
<span></span>

<script>
let elements = document.getElementsByTagName('*');

// 显示为 DIV
alert(elements[0].tagName);

let div = elements[0];
let p = document.createElement('p');
docpment.body.insertBefore(p, div);

// 显示为 P
alert(elements[0].tagName);
</script>
```

##### [建议] 获取元素的直接子元素时使用 `children`。避免使用`childNodes`，除非预期是需要包含文本、注释和属性类型的节点。

#### 4.1.2 样式获取

##### [建议] 获取元素实际样式信息时，应使用 `getComputedStyle` 或 `currentStyle`。

解释：

通过 style 只能获得内联定义或通过 JavaScript 直接设置的样式。通过 CSS class 设置的元素样式无法直接通过 style 获取。

#### 4.1.3 样式设置


##### [建议] 尽可能通过为元素添加预定义的 className 来改变元素样式，避免直接操作 style 设置。

##### [强制] 通过 style 对象设置元素样式时，对于带单位非 0 值的属性，不允许省略单位。

解释：

除了 IE，标准浏览器会忽略不规范的属性值，导致兼容性问题。

#### 4.1.4 DOM 操作

##### [建议] 操作 `DOM` 时，尽量减少页面 `reflow`。

解释：

页面 reflow 是非常耗时的行为，非常容易导致性能瓶颈。下面一些场景会触发浏览器的reflow：

- DOM元素的添加、修改（内容）、删除。
- 应用新的样式或者修改任何影响元素布局的属性。
- Resize浏览器窗口、滚动页面。
- 读取元素的某些属性（offsetLeft、offsetTop、offsetHeight、offsetWidth、scrollTop/Left/Width/Height、clientTop/Left/Width/Height、getComputedStyle()、currentStyle(in IE)) 。

##### [建议] 尽量减少 `DOM` 操作。

解释：

DOM 操作也是非常耗时的一种操作，减少 DOM 操作有助于提高性能。举一个简单的例子，构建一个列表。我们可以用两种方式：

1. 在循环体中 createElement 并 append 到父元素中。
2. 在循环体中拼接 HTML 字符串，循环结束后写父元素的 innerHTML。

第一种方法看起来比较标准，但是每次循环都会对 DOM 进行操作，性能极低。在这里推荐使用第二种方法。

#### 4.2.5 DOM 事件


##### [建议] 优先使用 `addEventListener / attachEvent` 绑定事件，避免直接在 HTML 属性中或 DOM 的 `expando` 属性绑定事件处理。

解释：

expando 属性绑定事件容易导致互相覆盖。

##### [建议] 在没有事件自动管理的框架支持下，应持有监听器函数的引用，在适当时候（元素释放、页面卸载等）移除添加的监听器。
