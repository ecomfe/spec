
# JavaScript 编码规范 - ESNext 补充篇

**注：本文在公示完成后将合并至 JavaScript 编码规范。**

## 1 前言

随着 ECMAScript 的不断发展，越来越多更新的语言特性将被使用，给应用的开发带来方便。本文档的目标是使 ECMAScript 新特性的代码风格保持一致，并给予一些实践建议。

本文档仅包含新特性部分。基础部分请遵循 [JavaScript Style Guide](javascript-style-guide.md)。

由于 ECMAScript 依然在快速的不断发展，本文档也将可能随时保持更新。更新内容主要涉及对新增的语言特性的格式规范化、实践指导，引擎与编译器环境变化的使用指导。

虽然本文档是针对 ECMAScript 设计的，但是在使用各种基于 ECMAScript 扩展的语言时(如 JSX、TypeScript 等)，适用的部分也应尽量遵循本文档的约定。

## 2 代码风格

### 2.1 文件

##### [建议] ESNext 语法的 JavaScript 文件使用 `.js` 扩展名。

解释：

所谓更新的 ES 标准，本身也是 ES 的一部分，保持 `.js` 扩展名即可。

对于特定情况，使用 ES Module 的文件，可以有选择地使用 `.mjs` 后缀，对于 CommonJS 的文件，可以使用 `.cjs` 后缀。

### 2.2 结构

#### 2.2.1 空格

##### [强制] 使用 `generator` 时，`*` 前面不允许有空格，`*` 后面必须有一个空格。

示例：

```javascript
// good
function* caller() {
    yield 'a';
    yield* callee();
    yield 'd';
}

// bad
function * caller() {
    yield 'a';
    yield *callee();
    yield 'd';
}
```

#### 2.2.2 语句


##### [强制] 类声明结束不允许添加分号。

解释：

与函数声明保持一致。

##### [强制] `export` 语句后，不允许出现表示空语句的分号。

解释：

`export` 关键字不影响后续语句类型，如后缀是一个函数定义，则它依然是函数定义而不需要结尾的分号，类定义同理。

示例：

```javascript
// good
export function foo() {
}

export default function bar() {
}


// bad
export function foo() {
};

export default function bar() {
};
```

##### [强制] 箭头函数的参数只有一个，并且不包含解构时，参数部分的括号必须省略。

示例：

```javascript
// good
list.map(item => item * 2);

// good
let fetchName = async id => {
    let user = await request(`users/${id}`);
    return user.fullName;
};

// bad
list.map((item) => item * 2);

// bad
let fetchName = async (id) => {
    let user = await request(`users/${id}`);
    return user.fullName;
};
```

##### [建议] 箭头函数的函数体只有一个单行表达式语句，且作为返回值时，省略 `{}` 和 `return`。

如果单个表达式过长，可以使用 `()` 进行包裹。

示例：

```javascript
// good
list.map(item => item * 2);

let foo = () => (
    condition
        ? returnValueA()
        : returnValueB()
);

// bad
list.map(item => {
    return item * 2;
});
```

##### [建议] 箭头函数的函数体只有一个 `Object Literal`，且作为返回值时，不使用 `return` ，使用 `()` 包裹返回的对象。

示例：

```javascript
// good
list.map(item => ({name: item[0], email: item[1]}));

// bad
list.map(item => {
    return {name: item[0], email: item[1]};
});
```

##### [强制] 解构多个变量时，如果超过行长度限制，每个解构的变量必须单独一行。

解释：

太多的变量解构会让一行的代码非常长，极有可能超过单行长度控制，使代码可读性下降。

示例：

```javascript
// good
let {
    name: personName,
    email: personEmail,
    sex: personSex,
    age: personAge
} = person;

// bad
let {name: personName, email: personEmail,
    sex: personSex, age: personAge
} = person;
```

##### [建议] 对于对象初始化、数组初始化、`import` 及 `export` 语句，当其多个子元素占用多行时，最后一个元素后必须保留逗号。

解释：

ES Next允许以上语法最后保留逗号，多行时保留逗号有助于快速添加或删除子元素。

本条规则不适用于函数调用和声明的参数，函数最后一个参数不得包含逗号。

示例：

```javascript
// good
const array = [
    foo,
    bar,
];

const array = [foo, bar]; // 单行的最后没有逗号

const object = {
    foo: bar,
    x: y,
};

const object = {foo: bar}; // 单行的最后没有逗号

import {
    foo,
    bar,
} from 'module';

import {foo, bar} from 'module'; // 单行的最后没有逗号

export {
    foo,
    bar,
};

export {foo, bar};  // 单行的最后没有逗号

// bad
const array = [foo, bar,];

const object = {
    foo: bar,
    x: y
};
```

## 3 语言特性

### 3.1 变量


#### [强制] 使用 `let` 和 `const` 定义变量，不使用 `var`。

解释：

使用 `let` 和 `const` 定义时，变量作用域范围更明确。

示例：

```javascript
// good
for (let i = 0; i < 10; i++) {

}

// bad
for (var i = 0; i < 10; i++) {

}
```

### 3.2 解构

#### [建议] 不要使用3层及以上的解构。

解释：

过多层次的解构会让代码变得难以阅读。

示例：

```javascript
// bad
let {documentElement: {firstElementChild: {nextSibling}}} = window;
```

#### [建议] 使用解构减少中间变量。

解释：

常见场景如变量值交换，可能产生中间变量。这种场景推荐使用解构。

示例：

```javascript
// good
[x, y] = [y, x];

// bad
let temp = x;
x = y;
y = temp;
```

#### [强制] 仅定义一个变量时不允许使用解构。

解释：

在这种场景下，使用解构将降低代码可读性。

示例：

```javascript
// good
let len = myString.length;

// bad
let {length: len} = myString;
```

#### [建议] 如果不节省编写时产生的中间变量，解构表达式 `=` 号右边不允许是 `ObjectLiteral` 和 `ArrayLiteral`。

解释：

在这种场景下，使用解构将降低代码可读性，通常也并无收益。如果2个变量有非常强的相关性，可以考虑使用 `ArrayLiteral` 进行声明，但通常我们并不会遇到此类场景。

示例：

```javascript
// good
let {first: firstName, last: lastName} = person;
let one = 1;
let two = 2;

// bad
let [one, two] = [1, 2];
```

#### [强制] 使用剩余运算符时，剩余运算符之前的所有元素必需具名。

解释：

剩余运算符之前的元素省略名称可能带来较大的程序阅读障碍。如果仅仅为了取数组后几项，请使用 `slice` 方法。

示例：

```javascript
// good
let [one, two, ...anyOther] = myArray;
let other = myArray.slice(3);

// bad
let [,,, ...other] = myArray;
```

### 3.3 模板字符串

#### [建议] 字符串内变量替换时，不要使用 `2` 次及以上的函数调用。

解释：

在变量替换符内有太多的函数调用等复杂语法会导致可读性下降。

示例：

```javascript
// good
let fullName = getFullName(getFirstName(), getLastName());
let s = `Hello ${fullName}`;

// bad
let s = `Hello ${getFullName(getFirstName(), getLastName())}`;
```

### 3.4 函数


#### [建议] 使用变量默认语法代替基于条件判断的默认值声明。

示例：

```javascript
// good
function foo(text = 'hello') {
}

// bad
function foo(text) {
    text = text || 'hello';
}
```


#### [强制] 不要使用 `arguments` 对象，应使用 `...args` 代替。

示例：

```javascript
// good
function foo(...args) {
    console.log(args.join(''));
}

// bad
function foo() {
    console.log([].join.call(arguments));
}
```

### 3.5 箭头函数

#### [强制] 一个函数被设计为需要 `call` 和 `apply` 的时候，不能是箭头函数。

解释：

箭头函数会强制绑定当前环境下的 `this`。

### 3.6 对象


#### [建议] 定义对象时，如果所有键均指向同名变量，则所有键都使用缩写；如果有一个键无法指向同名变量，则所有键都不使用缩写。

解释：

目的在于保持所有键值对声明的一致性。

```javascript
// good
let foo = {x, y, z};

let foo2 = {
    x: 1,
    y: 2,
    z: z
};


// bad
let foo = {
    x: x,
    y: y,
    z: z
};

let foo2 = {
    x: 1,
    y: 2,
    z
};
```

#### [强制] 定义方法时使用 `MethodDefinition` 语法，不使用 `PropertyName: FunctionExpression` 语法。

解释：

方法和函数是不同的东西，方法是与该对象相关的，通常会使用 `this` ，而函数则是更多是独立的逻辑，此时将对象视为一个类似命名空间的容器，本身函数与对象间没有强从属关系。

因此，当定义一个对象的方法时，应当明确使用 `MethodDefinition` 语法。

示例：

```javascript
// good
let foo = {
    bar(x, y) {
        return x + y;
    }
};

// bad
let foo = {
    bar: function (x, y) {
        return x + y;
    }
};
```

#### [建议] 使用 `Object.keys` 或 `Object.entries` 进行对象遍历。

解释：

不建议使用 `for .. in` 进行对象的遍历，以避免遗漏 `hasOwnProperty` 产生的错误。

示例：

```javascript
// good
for (let key of Object.keys(foo)) {
    let value = foo[key];
}

// good
for (let [key, value] of Object.entries(foo)) {
    // ...
}
```

#### [建议] 尽量使用计算属性键在一个完整的字面量中完整地定义一个对象，避免对象定义后直接增加对象属性。

解释：

在一个完整的字面量中声明所有的键值，而不需要将代码分散开来，有助于提升代码可读性。

示例：

```javascript
// good
const MY_KEY = 'bar';
let foo = {
    [MY_KEY + 'Hash']: 123
};

// bad
const MY_KEY = 'bar';
let foo = {};
foo[MY_KEY + 'Hash'] = 123;
```

### 3.7 类

#### [强制] 使用 `class` 关键字定义一个类。

解释：

直接使用 `class` 定义类更清晰。不要再使用 `function` 和 `prototype` 形式的定义。

```javascript
// good
class TextNode {
    constructor(value, engine) {
        this.value = value;
        this.engine = engine;
    }

    clone() {
        return this;
    }
}

// bad
function TextNode(value, engine) {
    this.value = value;
    this.engine = engine;
}

TextNode.prototype.clone = function () {
    return this;
};
```

#### [强制] 使用 `super` 访问父类成员，而非父类的 `prototype`。

解释：

使用 `super` 和 `super.foo` 可以快速访问父类成员，而不必硬编码父类模块而导致修改和维护的不便，同时更节省代码。

```javascript
// good
class TextNode extends Node {
    constructor(value, engine) {
        super(value);
        this.engine = engine;
    }

    setNodeValue(value) {
        super.setNodeValue(value);
        this.textContent = value;
    }
}

// bad
class TextNode extends Node {
    constructor(value, engine) {
        Node.apply(this, arguments);
        this.engine = engine;
    }

    setNodeValue(value) {
        Node.prototype.setNodeValue.call(this, value);
        this.textContent = value;
    }
}
```

### 3.8 模块

#### [强制] `export` 与内容定义放在一起。

解释：

何处声明要导出的东西，就在何处使用 `export` 关键字，不在声明后再统一导出。

示例：

```javascript
// good
export function foo() {
}

export const bar = 3;

// bad
function foo() {
}

const bar = 3;

export {foo};
export {bar};
```

#### [建议] 相互之间无关联的内容使用命名导出。

解释：

举个例子，工具对象中的各个方法，相互之间并没有强关联，通常外部会选择几个使用，则应该使用命名导出。

简而言之，当一个模块只扮演命名空间的作用时，使用命名导出。

#### [强制] 所有 `import` 语句写在模块开始处。

示例：

```javascript
// good
import {bar} from './bar';

function foo() {
    bar.work();
}

// bad
function foo() {
    import {bar} from './bar';
    bar.work();
}
```

### 3.9 集合

#### [建议] 对数组进行连接操作时，使用数组展开语法。

解释：

用数组展开代替 `concat` 方法，数组展开对 `Iterable` 有更好的兼容性。

示例：

```javascript
// good
let foo = [...foo, newValue];
let bar = [...bar, ...newValues];

// bad
let foo = foo.concat(newValue);
let bar = bar.concat(newValues);
```

#### [建议] 使用数组展开语法进行数组的复制操作。

解释：

使用数组展开可以快速地进行数组复制，也便于插入、追加其它元素。

示例：

```javascript
// good
let otherArr = [...arr];

// bad
let otherArr = arr.slice();
let otherArr = Array.from(arr);
```

#### [建议] 尽可能使用 `for .. of` 进行遍历。

解释：

使用 `for .. of` 可以更好地接受任何的 `Iterable` 对象，如 `Map#values` 生成的迭代器，使得方法的通用性更强。

以下情况除外：

1. 遍历确实成为了性能瓶颈，需要使用原生 `for` 循环提升性能。
2. 需要遍历过程中的索引。

当然，如果可以使用 `filter` 或 `map` 等函数完成逻辑，不建议使用 `for .. of` 的遍历来实现。

#### [强制] 当键值有可能不是字符串时，必须使用 `Map`；当元素有可能不是字符串时，必须使用 `Set`。

解释：

使用普通 Object，对非字符串类型的 `key`，需要自己实现序列化。并且运行过程中的对象变化难以通知 Object。

同时，如果一个对象从语义上应当是一个 `Map` 或 `Set` 对象，建议直接使用这2个类型，而不是使用 `Obejct` 来模拟行为。

#### [建议] 需要一个不可重复的集合时，应使用 `Set`。

解释：

不要使用 `{foo: true}` 这样的普通 `Object`。

示例：

```javascript
// good
let members = new Set(['one', 'two', 'three']);

// bad
let members = {
    one: true,
    two: true,
    three: true
};
```

#### [建议] 当需要遍历功能时，使用 `Map` 和 `Set`。

解释：

`Map` 和 `Set` 是可遍历对象，能够方便地使用 `for...of` 遍历。不要使用使用普通 Object。

示例：

```javascript
// good
let membersAge = new Map([
    ['one', 10],
    ['two', 20],
    ['three', 30]
]);
for (let [key, value] of map) {
}

// bad
let membersAge = {
    one: 10,
    two: 20,
    three: 30
};
for (let key in membersAge) {
    if (membersAge.hasOwnProperty(key)) {
        let value = membersAge[key];
    }
}
```

#### [建议] 程序运行过程中有添加或移除元素的操作时，使用 `Map` 和 `Set`。

解释：

使用 `Map` 和 `Set`，程序的可理解性更好；普通 Object 的语义更倾向于表达固定的结构。

示例：

```javascript
// good
let membersAge = new Map();
membersAge.set('one', 10);
membersAge.set('two', 20);
membersAge.set('three', 30);
membersAge.delete('one');

// bad
let membersAge = {};
membersAge.one = 10;
membersAge.two = 20;
membersAge.three = 30;
delete membersAge['one'];
```

### 3.10 异步


#### [强制] 回调函数的嵌套不得超过3层。

解释：

深层次的回调函数的嵌套会让代码变得难以阅读。

示例：

```javascript
// bad
getUser(userId, function (user) {
    validateUser(user, function (isValid) {
        if (isValid) {
            saveReport(report, user, function () {
                notice('Saved!');
            });
        }
    });
});
```

#### [建议] 使用 `Promise` 代替 `callback`。

解释：

相比 `callback`，使用 `Promise` 能够使复杂异步过程的代码更清晰。

示例：

```javascript
// good
let user;
getUser(userId)
    .then(function (userObj) {
        user = userObj;
        return validateUser(user);
    })
    .then(function (isValid) {
        if (isValid) {
            return saveReport(report, user);
        }

        return Promise.reject('Invalid!');
    })
    .then(
        function () {
            notice('Saved!');
        },
        function (message) {
            notice(message);
        }
    );
```

#### [强制] 使用标准的 `Promise` API。

解释：

1. 不允许使用非标准的 `Promise` API，如 `jQuery` 的 `Deferred`、`Q.js` 的 `defer` 等。
2. 不允许使用非标准的 `Promise` 扩展 API，如 `bluebird` 的 `Promise.any` 等。

使用标准的 `Promise` API，当运行环境都支持时，可以把 Promise Lib 直接去掉。

#### [强制] 不允许直接扩展 `Promise` 对象的 `prototype`。

解释：

理由和 **不允许修改和扩展任何原生对象和宿主对象的原型** 是一样的。如果想要使用更方便，可以用 utility 函数的形式。


#### [强制] 不得为了编写的方便，将可以并行的IO过程串行化。

解释：

并行 IO 消耗时间约等于 IO 时间最大的那个过程，串行的话消耗时间将是所有过程的时间之和。

示例：

```javascript
requestData().then(function (data) {
    renderTags(data.tags);
    renderArticles(data.articles);
});

// good
async function requestData() {
    const [tags, articles] = await Promise.all([
        requestTags(),
        requestArticles()
    ]);
    return {tags, articles};
}

// bad
async function requestData() {
    let tags = await requestTags();
    let articles = await requestArticles();

    return Promise.resolve({tags, articles});
}
```

## 4 环境

### 4.1 运行环境

#### [建议] 持续跟进与关注运行环境对语言特性的支持程度。

解释：

你可以通过 [MDN](https://developer.mozilla.org/zh-CN/) 或 [Can I use](https://caniuse.com/) 来查询各类特性在浏览器中的支持程度。

你可以通过 [Browserslist](https://browsersl.ist/) 来向各类工具提供你需要兼容的运行时，以便各类工具自动判断进行源码的转换。

ES 标准的制定还在不断进行中，各种环境对语言特性的支持也日新月异。了解项目中用到了哪些 ESNext 的特性，了解项目的运行环境，并持续跟进这些特性在运行环境中的支持程度是很有必要的。这意味着：

1. 如果有任何一个运行环境（比如 Chrome）支持了项目里用到的所有特性，你可以在开发时抛弃预编译。
2. 如果所有环境都支持了某一特性（比如 `Array.prototype.flatMap`），你可以抛弃相关的 shim，或无需在预编译时进行转换。
3. 如果所有环境都支持了项目里用到的所有特性，你可以完全抛弃预编译。

无论如何，在选择预编译工具时，你都需要清晰的知道你现阶段将在项目里使用哪些语言特性，然后了解预编译工具对语言特性的支持程度，做出选择。

#### [强制] 在运行环境中没有 `Promise` 时，将 `Promise` 的实现 shim 到 `global` 中。

解释：

当前运行环境下没有 `Promise` 时，可以引入 `shim` 的扩展。如果自己实现，需要实现在 `global` 下，并且与标准 API 保持一致。

这样，未来运行环境支持时，可以随时把 `Promise` 扩展直接扔掉，而应用代码无需任何修改。

当选择 shim 时，使用 `core-js` 作为实现。
