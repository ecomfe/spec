
# JavaScript编码规范 - ESNext补充篇（草案）




[1 前言](#user-content-1-%E5%89%8D%E8%A8%80)

[2 代码风格](#user-content-2-%E4%BB%A3%E7%A0%81%E9%A3%8E%E6%A0%BC)

　　[2.1 文件](#user-content-21-%E6%96%87%E4%BB%B6)

　　[2.2 结构](#user-content-22-%E7%BB%93%E6%9E%84)

　　　　[2.2.1 缩进](#user-content-221-%E7%BC%A9%E8%BF%9B)

　　　　[2.2.2 空格](#user-content-222-%E7%A9%BA%E6%A0%BC)

　　　　[2.2.3 语句](#user-content-223-%E8%AF%AD%E5%8F%A5)

[3 语言特性](#user-content-3-%E8%AF%AD%E8%A8%80%E7%89%B9%E6%80%A7)

　　[3.1 变量](#user-content-31-%E5%8F%98%E9%87%8F)

　　[3.2 解构](#user-content-32-%E8%A7%A3%E6%9E%84)

　　[3.3 模板字符串](#user-content-33-%E6%A8%A1%E6%9D%BF%E5%AD%97%E7%AC%A6%E4%B8%B2)

　　[3.4 函数](#user-content-34-%E5%87%BD%E6%95%B0)

　　[3.5 箭头函数](#user-content-35-%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0)

　　[3.6 对象](#user-content-36-%E5%AF%B9%E8%B1%A1)

　　[3.7 类](#user-content-37-%E7%B1%BB)

　　[3.8 模块](#user-content-38-%E6%A8%A1%E5%9D%97)

　　[3.9 集合](#user-content-39-%E9%9B%86%E5%90%88)

　　[3.10 装饰器](#user-content-310-%E8%A3%85%E9%A5%B0%E5%99%A8)

　　[3.11 异步](#user-content-311-%E5%BC%82%E6%AD%A5)

[4 环境](#user-content-4-%E7%8E%AF%E5%A2%83)





## 1 前言


随着ECMAScript的不断发展，越来越多更新的语言特性将被使用，给应用的开发带来方便。本文档的目标是使ECMAScript新特性的代码风格保持一致，并给予一些实践建议。

本文档仅包含新特性部分。基础部分请遵循[JavaScript Style Guide](javascript-style-guide.md)。

虽然本文档是针对ECMAScript设计的，但是在使用各种基于ECMAScript扩展的语言时(如JSX、TypeScript等)，适用的部分也应尽量遵循本文档的约定。






## 2 代码风格





### 2.1 文件


##### [建议] ESNext语法的`JavaScript`文件使用`.js`扩展名。

##### [强制] 当文件无法使用`.js`扩展名时，使用`.es`扩展名。

解释：

某些应用开发时，可能同时包含ES5和ESNext文件，运行环境仅支持ES5，ESNext文件需要经过预编译。部分场景下，编译工具的选择可能需要通过扩展名区分，需要重新定义ESNext文件的扩展名。此时，ESNext文件必须使用`.es`扩展名。

但是，更推荐使用其他条件作为是否需要编译的区分：

1. 基于文件内容。
2. 不同类型文件放在不同目录下。





### 2.2 结构


#### 2.2.1 缩进


##### [建议] 使用多行字符串时遵循缩进原则。

解释：

4空格为一个缩进，换行后添加一层缩进。将起始和结束的`` ` ``符号单独放一行，有助于生成HTML时的标签对齐。

示例：

```javascript
// good
function foo() {
    let bar = `Hello
        World`;
    console.log(bar);
}

// good
function foo() {
    let html = `
        <div>
            <p></p>
            <p></p>
        </div>
    `;
}
```


#### 2.2.2 空格


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


#### 2.2.3 语句


##### [强制] 类声明结束不允许添加分号。

解释：

与函数声明保持一致。


##### [强制] 类成员定义中，方法定义后不允许添加分号，成员属性定义后必须添加分号。

解释：

成员属性是当前Stage 0的标准，如果使用的话，则定义后加上分号。

示例：

```javascript
// good
class Foo {
    foo = 3;

    bar() {

    }
}

// bad
class Foo {
    foo = 3

    bar() {

    }
}
```

##### [强制] export语句后，不允许出现表示空语句的分号。

解释：

export关键字不影响后续语句类型。

示例：

```javascript
// good
export function foo() {
}

export default bar() {
}


// bad
export function foo() {
};

export default bar() {
};
```


##### [强制] 属性装饰器后，可以不加分号的场景，不允许加分号。

解释：

只有一种场景是必须加分号的：当属性key是computed property key时，其装饰器必须加分号，否则修饰key的`[]`会做为之前表达式的property accessor。

上面描述的场景，装饰器后需要加分号。其余场景下的属性装饰器后不允许加分号。

示例：

```javascript
// good
class Foo {
    @log('INFO')
    bar() {

    }

    @log('INFO');
    ['bar' + 2]() {

    }
}

// bad
class Foo {
    @log('INFO');
    bar() {

    }

    @log('INFO')
    ['bar' + 2]() {

    }
}
```


##### [强制] 箭头函数的参数只有一个，并且不包含解构时，参数部分的括号必须省略。

示例：

```javascript
// good
list.map(item => item * 2);

// bad
list.map((item) => item * 2);
```

##### [强制] 箭头函数的函数体只有一个非`Object Literal`的单行表达式语句，且作为返回值时，必须省略`{}`和`return`。

示例：

```javascript
// good
list.map(item => item * 2);

// bad
list.map(item => {
    return item * 2;
});
```

##### [强制] 箭头函数的函数体只有一个`Object Literal`，且作为返回值时，不得省略`{}`和`return`。

示例：

```javascript
// good
list.map(item => {
    return {
        name: item[0],
        email: item[1]
    };
});

// bad
list.map(item => ({
    name: item[0],
    email: item[1]
}));
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






## 3 语言特性





### 3.1 变量


#### [强制] 使用let和const定义变量，不使用var。

解释：

使用let和const定义时，变量作用域范围更明确。

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


#### [强制] 不要使用3层及以上的解构。

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

在这种场景下，使用结构将降低代码可读性。

示例：

```javascript
// good
let len = myString.length;

// bad
let {length: len} = myString;
```

#### [强制] 不节省中间变量时，不允许因为使用解构产生中间对象。

解释：

纯因为使用解构产生无用的中间对象是一种浪费。

示例：

```javascript
// good
let {first: firstName, last: lastName} = person;
let one = 1;
let two = 2;

// bad
// 下面的代码产生了数组对象[1, 2]，但是这个数组对象并没什么用
let [one, two] = [1, 2];
```

#### [强制] 使用剩余运算符时，剩余运算符之前的所有元素必需具名。

解释：

剩余运算符之前的元素省略名称可能带来较大的程序阅读障碍。如果仅仅为了取数组后几项，请使用slice方法。

示例：

```javascript
// good
let [one, two, ...anyOther] = myArray;
let other = myArray.slice(3);

// bad
let [,,, ...other] = myArray;
```



### 3.3 模板字符串


#### [建议] 当对行首空格敏感时，避免使用多行字符串。

解释：

由于会破坏整体的缩进，使代码整体上缺乏结构性，视觉上变丑。

示例：

```javascript
// bad
function foo() {
    // 缩进再多几层会更丑
    let bar = `Hello
World`;
    console.log(bar);
}
```


#### [强制] 字符串内变量替换时，不要使用2次及以上的函数调用。

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

解释：

添加默认值有助于引擎的优化，在未来strong mode下也会有更好的效果。

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


#### [强制] 不要使用arguments对象，应使用`...args`代替。

解释：

在未来strong mode下`arguments`将被禁用。

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



#### [强制] 不得在顶层作用域下使用箭头函数。

解释：

顶层作用域包括全局代码、模块代码等，这个作用域下`this`对象为`undefined`或者`Global`对象，因此函数不应该绑定`this`。


#### [强制] 一个函数被设计为需要call和apply的时候，不能是箭头函数。

解释：

箭头函数会强制绑定当前环境下的`this`。



### 3.6 对象


#### [强制] 定义对象时，如果所有键均指向同名变量，则所有键都使用缩写；如果有一个键无法指向同名变量，则所有键都不使用缩写。

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

#### [强制] 定义方法时使用`MethodDefinition`语法，不使用`PropertyName:FunctionExpression`语法。

解释：

`MethodDefinition`语法更清晰简洁。

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

#### [建议] 使用Object.keys进行对象遍历。

解释：

不建议使用`for .. in`进行对象的遍历，以避免遗漏`hasOwnProperty`产生的错误。

示例：

```javascript
// good
for (let key of Object.keys(foo)) {
    let value = foo[key];
}
```

#### [建议] 定义对象的方法不应使用箭头函数。

解释：

箭头函数将`this`绑定到当前环境，在obj.method()调用时容易导致不期待的`this`。除非明确需要绑定`this`，否则不应使用箭头函数。

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
    bar: (x, y) => x + y
};
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



#### [强制] 使用class关键字定义一个类。

解释：

直接使用`class`定义类更清晰。不要再使用`function`和`prototype`形式的定义。

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

#### [强制] 使用super访问父类成员，而非父类的prototype

解释：

使用`super`和`super.foo`可以快速访问父类成员，而不必硬编码父类模块而导致修改和维护的不便，同时更节省代码。

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



#### [强制] export与内容定义放在一起。

解释：

何处声明要导出的东西，就在何处使用`export`关键字，不做声明后再统一导出。

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

export foo;
export bar;
```

#### [建议] 相互之间无关联的内容使用命名导出。

解释：

举个例子，工具对象中的各个方法，相互之间并没有强关联，通常外部会选择几个使用，则应该使用命名导出。

简而言之，当一个模块只扮演命名空间的作用时，使用命名导出。


#### [建议] 导出时使用`const`定义。

解释：

ES6 Module导出的是引用而非值，为防止模块内后续的修改影响外部引用的值，尽量导出`const`定义的常量。

```javascript
// foo.js
let x = 3;
export default x;

// 不小心写了这样的代码
document.addEventListener('click', () => x += 1, false);

// bar.js
import x from 'foo';
console.log(x); // 3
// 用户点了一下
console.log(x); // 4
```

#### [强制] 所有import语句写在模块开始处。

解释：

`import`会被默认提升至模块最前面，同时具备依赖声明的作用。

浏览器如能尽早读取相关的`import`内容则有可能更早地请求相关的依赖模块，有一定的性能提升作用，在HTTP/2之后可能会具有更明显的效果。

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

用数组展开代替`concat`方法，数组展开对`Iterable`有更好的兼容性。

示例：

```javascript
// good
let foo = [...foo, newValue];
let bar = [...bar, ...newValues];

// bad
let foo = foo.concat(newValue);
let bar = bar.concat(newValues);
```

#### [建议] 不要使用数组展开进行数组的复制操作。

解释：

使用数组展开语法进行复制，代码可读性较差。推荐使用`Array.from`方法进行复制操作。

示例：

```javascript
// good
let otherArr = Array.from(arr);

// bad
let otherArr = [...arr];
```

#### [建议] 尽可能使用`for .. of`进行遍历。

解释：

使用`for .. of`可以更好地接受任何的`Iterable`对象，如`Map#values`生成的迭代器，使得方法的通用性更强。

以下情况除外：

1. 遍历确实成为了性能瓶颈，需要使用原生`for`循环提升性能。
2. 需要遍历过程中的索引。


#### [强制] 当键值有可能不是字符串时，必须使用Map；当元素有可能不是字符串时，必须使用Set。

解释：

使用普通Object，对非字符串类型的key，需要自己实现序列化。并且运行过程中的对象变化难以通知Object。


#### [建议] 需要一个不可重复的集合时，应使用Set。

解释：

不要使用`{foo: true}`这样的普通Object。

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


#### [建议] 当需要遍历功能时，使用Map和Set。

解释：

Map和Set是可遍历对象，能够方便地使用`for...of`遍历。不要使用使用普通Object。

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

#### [建议] 程序运行过程中有添加或移除元素的操作时，使用Map和Set。

解释：

使用Map和Set，程序的可理解性更好；普通Object的语义更倾向于表达固定的结构。

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



### 3.10 装饰器



#### [建议] 不在直接声明的普通对象上使用装饰器。

解释：

一个普通对象应该是干净而不需要各种装饰器注入逻辑的。




### 3.11 异步


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

相比`callback`，使用`Promise`能够使复杂异步过程的代码更清晰。

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

1. 不允许使用非标准的`Promise` API，如`jQuery`的`Deferred`、`Q.js`的`defer`等。
2. 不允许使用非标准的`Promise`扩展API，如`bluebird`的`Promise.any`等。

使用标准的`Promise` API，当运行环境都支持时，可以把Promise Lib直接去掉。


#### [强制] 不允许直接扩展 `Promise` 对象的 `prototype`。

解释：

理由和 **不允许修改和扩展任何原生对象和宿主对象的原型** 是一样的。如果想要使用更方便，可以用utility函数的形式。


#### [强制] 不得为了编写的方便，将可以并行的IO过程串行化。

解释：

并行IO消耗时间约等于IO时间最大的那个过程，串行的话消耗时间将是所有过程的时间之和。

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

#### [建议] 使用 `async/await` 代替 `generator + co`。

解释：

使用语言自身的能力可以使代码更清晰，也无需引入`co`库。

示例：

```javascript
addReport(report, userId).then(
    function () {
        notice('Saved!');
    },
    function (message) {
        notice(message);
    }
);

// good
async function addReport(report, userId) {
    let user = await getUser(userId);
    let isValid = await validateUser(user);

    if (isValid) {
        let savePromise = saveReport(report, user);
        return savePromise();
    }

    return Promise.reject('Invalid');
}

// bad
function addReport(report, userId) {
    return co(function* () {
        let user = yield getUser(userId);
        let isValid = yield validateUser(user);

        if (isValid) {
            let savePromise = saveReport(report, user);
            return savePromise();
        }

        return Promise.reject('Invalid');
    });
}
```









## 4 环境



#### [建议] 持续跟进与关注运行环境对语言特性的支持程度。

解释：

[查看环境对语言特性的支持程度](https://kangax.github.io/compat-table/es6/)

ES标准的制定还在不断进行中，各种环境对语言特性的支持也日新月异。了解项目中用到了哪些ESNext的特性，了解项目的运行环境，并持续跟进这些特性在运行环境中的支持程度是很有必要的。这意味着：

1. 如果有任何一个运行环境（比如chrome）支持了项目里用到的所有特性，你可以在开发时抛弃预编译。
2. 如果所有环境都支持了某一特性（比如Promise），你可以抛弃相关的shim，或无需在预编译时进行转换。
3. 如果所有环境都支持了项目里用到的所有特性，你可以完全抛弃预编译。


#### [强制] 在运行环境中没有 `Promise` 时，将 `Promise` 的实现 `shim` 到 `global` 中。

解释：

当前运行环境下没有`Promise`时，可以引入`shim`的扩展。如果自己实现，需要实现在`global`下，并且与标准API保持一致。

这样，未来运行环境支持时，可以随时把`Promise`扩展直接扔掉，而应用代码无需任何修改。


#### [建议] 需要预编译时，使用 `babel` 做为预编译工具。

解释：

由于不同环境的（如浏览器）JavaScript引擎对ESNext特性支持程度的问题，ESNext代码可能需要进行预编译。此时，建议使用`babel`做为预编译工具。由于`babel`最新的6暂时还不稳定，建议暂时使用5。

不同的产品，对于浏览器支持的情况不同，使用 `babel` 的时候，需要设置的参数也有一些区别。下面在示例中给出一些建议的参数。

示例：

```shell
＃ 建议的参数
--loose all --modules amd --blacklist strict

＃ 如果需要使用 es7.classProperties、es7.decorators 等一些特性，需要额外的 --stage 0 参数
--loose all --modules amd --blacklist strict --stage 0
```

#### [建议] 生成的代码在浏览器环境运行时，应生成AMD模块化代码。

解释：

添加`--modules amd`参数即可。



#### [建议] 使用 `external-helpers` 减少生成文件的大小。

解释：

当babel在转换代码的过程中发现需要一些特性时，会在该文件头部生成对应的helper代码。默认情况下，对于每一个经由babel处理的文件，均会在文件头部生成对应需要的辅助函数，多份文件辅助函数存在重复，占用了不必要的代码体积。

因此推荐打开`externalHelpers: true`选项，使babel在转换后内容中不写入helper相关的代码，而是使用一个外部的`.js`统一提供所有的helper。对于[external-helpers](https://github.com/babel/babel.github.io/blob/5.0.0/docs/usage/external-helpers.md)的使用，可以有两种方式：

1. 默认方式：需要通过`<script>`自行引入`babel-polyfill.js` 和 `babel-external-helpers.js`。
2. 定制方式：自己实现[babel-runtime](https://github.com/babel/babel.github.io/blob/5.0.0/docs/usage/runtime.md)。

示例：

```shell
# 默认方式
--loose all --modules amd --external-helpers
# `babelHelpers`的代码可以通过执行`babel-external-helpers -t var`得到所有相关API的实现

# 定制方式
--loose all --modules amd --optional runtime
```



