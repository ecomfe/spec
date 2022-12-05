# TypeScript编码规范

## 1 前言

随着 TypeScript 的不断发展，越来越多的开发者认可并使用 TypeScript 开发应用。本文档的目标是使 TypeScript 新特性的代码风格保持一致，并给予一些实践建议。
本文档基本遵循 JavaScript Style Guide，并增设与 TypeScript 相关的规则。

## 2 规则

### [强制] 将重载函数、方法的声明放置在一起

解释：

当一个函数或方法有多个类型的重载时，将它们放置在一起有助于代码阅读时很好地判断一个函数全部的重载方式。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/adjacent-overload-signatures": "error"
}
```

示例：

```ts
// good
interface Foo {
    foo(s: string): void;
    foo(n: number): void;
    foo(sn: string | number): void;
    bar(): void;
}

export function bar(): void;
export function foo(s: string): void;
export function foo(n: number): void;
export function foo(sn: string | number): void;

// bad
interface Foo {
    foo(s: string): void;
    foo(n: number): void;
    bar(): void;
    foo(sn: string | number): void;
}

export function foo(s: string): void;
export function foo(n: number): void;
export function bar(): void;
export function foo(sn: string | number): void;
```

### 【强制】 使用规范的数组类型定义形式

解释：

数组在TypeScript中有 T[] 或者 Array<T> 两种功能一致的定义形式，使用统一的一种定义形式可以帮助代码库更加易于阅读理解。

- T 为简单类型（原始类型或其引用），使用 T[] 定义数组。
- T为联合类型、交叉类型、对象、函数时，使用 Array<T> 定义数组。

- 只读类型统一使用 Array<T> 定义数组。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/array-type": ["error", {"default": "array-simple", "readonly": "generic"}]
}
```

示例：

```ts
// good
const a: Array<string | number> = ['a', 'b'];
const b: Array<{ prop: string }> = [{ prop: 'a' }];
const c: Array<() => void> = [() => {}];
const d: MyType[] = ['a', 'b'];
const e: string[] = ['a', 'b'];

const f: ReadonlyArray<string> = ['a', 'b'];
const g: ReadonlyArray<{ prop: string }> = [{ prop: 'default' }];

// bad
const a: (string | number)[] = ['a', 'b'];
const b: { prop: string }[] = [{ prop: 'a' }];
const c: (() => void)[] = [() => {}];
const d: Array<MyType> = ['a', 'b'];
const e: Array<string> = ['a', 'b'];

const f: readonly string[] = ['a', 'b'];
const g: readonly { prop: string }[] = [{ prop: 'default' }];
```

### 【强制】禁止 await 非 thenable 的值

解释：

thenable 指带有 then 方法的对象， 比如 Promise。有助于避免一些书写失误之类的错误。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/await-thenable": "error"
}
```

示例：

```ts
// good
await Promise.resolve('value');

const createValue = async () => 'value';
await createValue();

// bad
await 'value';

const createValue = () => 'value';
await createValue();
```

### 【强制】禁止通过命令注释强制解除ts的类型检查

解释：

ts中可以使用@ts-<directive>的注释指令，用于指定单行或单个文件的 ts 类型检查规则。规定的命令注释使用规则如下：

```ts
// @ts-expect-error // 下一行的代码类型错误为预期内的，无需报错（如果使用了但实际没错，反而报错）。允许使用但需要进行不少于3个字的说明。
// @ts-ignore // 忽略下一行的类型检查错误，不允许使用。
// @ts-nocheck // 整个文件关闭ts类型检查，不允许使用。
// @ts-check // 整个文件开启ts类型检查，允许使用。
```

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/ban-ts-comment": [
        "error",
        {
            "ts-expect-eror": "allow-with-description",
            "ts-ignore": true,
            "ts-nocheck": true,
            "ts-check": false,
            "minimumDescriptionLength": 3
        }
    ]
}
```

示例：

```ts
// good
if (false) {
  // Compiler warns about unreachable code error
  console.log('hello');
}

// bad
if (false) {
  // @ts-ignore: Unreachable code error
  console.log('hello');
}
if (false) {
  /*
  @ts-ignore: Unreachable code error
  */
  console.log('hello');
}
```

### 【强制】禁止通过命令注释强制解除tslint的检查

解释：

不允许使用// tslint:<rule-flag>  或 /* tslint:<rule-flag> */的注释指令，解除单行或整个文件的 tslint 规范检查。

- / tslint:<rule-flag> —— 代表注释下的一行，该规则生效。
-  /* tslint:<rule-flag> */ —— 代表整个文件，该规则生效 。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/ban-tslint-comment": "error"
}
```

示例：

```ts
// good

// This is a comment that just happens to mention tslint
/* This is a multiline comment that just happens to mention tslint */
someCode(); // This is a comment that just happens to mention tslint

// bad

/* tslint:disable */
/* tslint:enable */
/* tslint:disable:rule1 rule2 rule3... */
/* tslint:enable:rule1 rule2 rule3... */
// tslint:disable-next-line
someCode(); // tslint:disable-line
// tslint:disable-next-line:rule1 rule2 rule3...
```

### 【强制】禁用部分内置类型别名定义类型

解释：

不允许使用String、Object、Boolean、Number、Symbol、BigInt、Function等内置类型别名进行类型定义，这是不安全的。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/ban-types": "error"
}
```

示例：

```ts
// good
const str: string = 'foo';
const bool: boolean = true;
const num: number = 1;
const symb: symbol = Symbol('foo');
const bigInt: bigint = 1n;

const func: () => number = () => 1;

const lowerObj: object = {};
const capitalObj: { a: string } = { a: 'string' };

const curly1: number = 1;
const curly2: Record<'a', string> = { a: 'string' };

// bad

// use lower-case primitives for consistency
const str: String = 'foo';
const bool: Boolean = true;
const num: Number = 1;


 => 1;

// use safer object types
const lowerObj: Object = {};
const capitalObj: Object = { a: 'string' };

const curly1: {} = 1;
const curly2: {} = { a: 'string' };
```

### 【强制】 把类的泛型参数写在类的构造器处

解释：

ts中类的泛型参数有两种写法 『类型注解』或者 『构造函数注解』。请统一使用『构造函数注解』的写法， 有助于提升代码可读性。

```ts
const map: Map<string, number> = new Map(); // 类型注解写法
const map = new Map<string, number>(); // 构造函数注解写法
```

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/consistent-generic-constructors": ["error", "constructor"]
}
```

示例：

```ts
// good
const map = new Map<string, number>();
const map: Map<string, number> = new MyMap();
const set = new Set<string>();
const set = new Set();
const set: Set<string> = new Set<string>();

// bad
const map: Map<string, number> = new Map();
const set: Set<string> = new Set();
```

### [推荐] 使用一致的风格定义任意键值的对象

解释：

TS支持两种方式来定义任意键值的对象：

1. 使用下标签名，如

```ts
interface Foo {
    [key: string]: unknown;
}
type Foo = {
    [key: string]: unknown;
};
```

1. 使用内置类型Record，如

```ts
type Foo = Record<string, unknown>;
```

这两种方式的作用是一样的，建议使用一致的风格。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    // 使用Record
    "@typescript-eslint/consistent-indexed-object-style": ["warn", "record"]
}
```

示例：

```ts
// good
type Foo = Record<string, unknown>;

// bad
interface Foo {
    [key: string]: unknown;
}
type Foo = {
    [key: string]: unknown;
};
```

### [推荐] 使用一致的方式进行类型断言

解释：
TS提供两种类型断言的方式：

1. 尖括号，如 <Type>value
2. as，如 value as Type

建议使用一致的方式进行类型断言，以提高代码的可读性。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    // 变量作为参数使用时，可用as断言
    "@typescript-eslint/consistent-type-assertions": [
        "warn",
        {"assertionStyle": "as", "objectLiteralTypeAssertions": "allow-as-parameter"}
    ]
}
```

示例：

```ts
// good
const x: T = { ... }; // const x: T = {...} 总是优于 const x = {...} as T
const y = { ... } as any; // 定义变量时，可以 as any
const z = { ... } as unknown; // 定义变量时，可以 as unknown
foo({ ... } as T);
new Clazz({ ... } as T);
function foo() { throw { bar: 5 } as Foo }
const foo = <Foo props={{ ... } as Bar}/>;

// bad
const x = { ... } as T;

function foo() {
    return { ... } as T;
}
```

### [推荐] 使用一致的方式进行类型定义

解释：

TS允许使用type和interface两种方式来定义一个对象的类型，如

```ts
// type
type T1 = {
    a: string;
    b: number;
};

// interface
interface T2 {
    a: string;
    b: number;
}
```

这两种方式相近，但经常被混用。建议使用一致的方式进行类型定义，以提高代码的可读性。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    // 使用interface
    "@typescript-eslint/consistent-type-definitions": ["warn", "interface"]
}
```

示例：

```ts
// good
type T = string; // 非对象类型可用 type
type Foo = string | {}; // 混合类型可用 type

interface T {
    x: number;
}

// bad
type T = { x: number };
```

### [强制] 明确类成员的可访问性

解释：

TS允许使用public、protected和private来修饰类成员的可访问性，默认值为public。

使用访问修饰符可以明确哪些属性和方法是类私有的，哪些是公共可访问的，有利于提高代码的可读性。

可选项：

```ts
// explicit 需标明；no-public 不需要public；off 关闭检测
type AccessibilityLevel = "explicit" | "no-public" | "off";

interface Options {
    accessibility?: AccessibilityLevel; // 通用配置
    overrides?: { // 规则重载，特定类型的成员可使用以下配置覆盖accessibility
        accessors?: AccessibilityLevel; // 访问器 get/set
        constructors?: AccessibilityLevel; // 构造器
        methods?: AccessibilityLevel; // 方法
        properties?: AccessibilityLevel; // 属性
        parameterProperties?: AccessibilityLevel; // 参数属性
    };
    ignoredMethodNames?: string[]; // 指定忽略检测的方法
}

// 默认需标明所有类成员的可访问性
const defaultOptions: Options = [{ accessibility: "explicit" }];
```

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    // 禁用public（默认为public，不需要额外去写）
    "@typescript-eslint/explicit-member-accessibility": ["error", {"accessibility": "no-public"}]
}
```

示例：

```ts
// good
class Animal {
    constructor(protected breed, name) {
        // Parameter property and constructor
        this.name = name;
    }
    private animalName: string; // Property
    get name(): string {
        // get accessor
        return this.animalName;
    }
    private set name(value: string) {
        // set accessor
        this.animalName = value;
    }
    protected walk() {
        // method
    }
}

// bad
class Animal {
    public constructor(public breed, name) {
        // Parameter property and constructor
        this.animalName = name;
    }
    public animalName: string; // Property
    public get name(): string {
        // get accessor
        return this.animalName;
    }
    public set name(value: string) {
        // set accessor
        this.animalName = value;
    }
    public walk() {
        // method
    }
}
```

### [强制] 使用一致的风格分隔对象类型定义的成员

解释：

官方：使用Linter检测不如使用Formatter自动格式化。

TS提供三种方式来分隔interface或type定义的对象类型的成员，如

```ts
interface Foo {
    // 分号(semi)，默认值，推荐使用
    name: string;
    // 逗号(comma)，JSON风格
    name: string,
    // 换行(none)
    name: string
}
```

应使用一致的分隔符，以提高代码的可读性。

可选项：

```ts
interface Options {
    multiline?: { // 多行
        delimiter?: "none" | "semi" | "comma"; // 分隔符，换行｜分号｜逗号
        requireLast?: boolean; // 最后的成员后面是否需要分隔符
    };
    singleline?: { // 单行
        delimiter?: "semi" | "comma"; // 分隔符，分号｜逗号
        requireLast?: boolean;
    };
    overrides?: {
        interface?: { // 针对interface定义的对象类型
            multiline?: {
                delimiter?: "none" | "semi" | "comma";
                requireLast?: boolean;
            };
            singleline?: {
                delimiter?: "semi" | "comma";
                requireLast?: boolean;
            };
        };
        typeLiteral?: { // 针对type定义的对象类型
            multiline?: {
                delimiter?: "none" | "semi" | "comma";
                requireLast?: boolean;
            };
            singleline?: {
                delimiter?: "semi" | "comma";
                requireLast?: boolean;
            };
        };
    };
    /**
     * multilineDetection: 多行的定义方式
     * brackets: 默认值，在interface和type定义中出现任意换行，即为多行
     * last-member: 若最后一个成员和最后的括号在同一行，为单行
     */
    multilineDetection?: "brackets" | "last-member"; //
}

// 默认值
const defaultOptions: Options = [
    {
        multiline: { delimiter: "semi", requireLast: true },
        singleline: { delimiter: "semi", requireLast: false },
        multilineDetection: "brackets",
    },
];
```

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    // 多行，分号分隔，最后添加；单行，逗号分隔，最后省略
    "@typescript-eslint/member-delimiter-style": ["error", {"multiline": "semi", "singleline": "comma"}]
}
```

示例：

```ts
// good
// 多行，分号分隔，最后添加
interface Foo {
    name: string;
    greet(): string;
}
type Bar = {
    name: string;
    greet(): string;
}

// 单行，逗号分隔，最后省略
interface Foo { name: string }
type Bar = { name: string }
type FooBar = { name: string, greet(): string }

// bad
// 多行，用错或缺少分隔符
interface Foo {
    name: string
    greet(): string
}
interface Bar {
    name: string,
    greet(): string,
}
interface Baz {
    name: string;
    greet(): string
}

// 单行，用错或多余分隔符
type FooBar = { name: string; greet(): string }
type FooBar = { name: string, greet(): string, }
```

### [推荐] 使用一致的对象成员顺序

解释：

在使用class、interface和type定义一个对象的结构时，将属性、方法和构造器以一致的顺序进行排列可以使代码更容易阅读、检索和编辑。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    // [..., "signature", ..., "field", ..., "constructor", ..., "method", ...]
    "@typescript-eslint/member-ordering": "warn"
}
```

示例：

```ts
// good
class Foo {
    [Z: string]: any; // -> signature
    private C: string; // -> field
    public D: string; // -> field
    protected static E: string; // -> field

    constructor() {} // -> constructor

    public static A(): void {} // -> method
    public B(): void {} // -> method
}
interface Foo {
    [Z: string]: any; // -> signature

    B: string; // -> field

    new (); // -> constructor

    A(): void; // -> method
}
type Foo = {
    // no signature

    B: string; // -> field

    // no constructor

    A(): void; // -> method
};

// bad
class Foo {
    public static A(): void {} // -> method
    public B(): void {} // -> method

    constructor() {} // -> constructor

    [Z: string]: any; // -> signature

    private C: string; // -> field
    public D: string; // -> field
    protected static E: string; // -> field
}
interface Foo {
    A(): void; // -> method

    new (); // -> constructor

    B: string; // -> field

    [Z: string]: any; // -> signature
}
type Foo = {
    A(): void; // -> method

    B: string; // -> field

    // no constructor
    // no signature
};
```

### [强制] 命名时必须匹配命名规范

解释：

命名类型时匹配规范的拼写类型，有利于代码类型进行整理规范。不同类型可以进行一目了然的判断。

camelCase：驼峰拼写法。使用大写字符分割单词，单词之间不允许下划线，允许连续大写（例：myID)

PascalCase：大驼峰。与camelCase类似，但第一个字符必须大写。（例：MyId)

UPPER_CASE：字符必须大写，并且使用下划线分割单词。（例：MY_ID)

注：均不允许以下划线开头或者结尾。

默认拼写格式：camelCase

特例如下：

variableLike( function, parameter, variable)： 使用camelCase或者UPPER_CASE

typelike( class, enum, interface, typeAlias, typeParameter)：使用PascalCase

enumMember：使用PascalCase或者UPPER_CASE

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/naming-convention": [
        "error",
        [
            {
                "selector": "default",
                "format": ["camelCase"],
                "leadingUnderscore": "forbid",
                "trailingUnderscore": "forbid"
            },
            {
                "selector": "variableLike",
                "format": ["camelCase", "UPPER_CASE"],
                "leadingUnderscore": "forbid",
                "trailingUnderscore": "forbid"
            },
            {
                "selector": "memberLike",
                "format": ["camelCase"],
                "leadingUnderscore": "forbid",
                "trailingUnderscore": "forbid"
            },
            {
                "selector": "property",
                "format": ["camelCase"],
                "leadingUnderscore": "forbid",
                "trailingUnderscore": "forbid"
            },
            {
                "selector": "method",
                "format": ["camelCase"],
                "leadingUnderscore": "forbid",
                "trailingUnderscore": "forbid"
            },
            {
                "selector": "typeLike",
                "format": ["PascalCase"],
                "leadingUnderscore": "forbid",
                "trailingUnderscore": "forbid"
            },
            {
                "selector": "enumMember",
                "format": ["PascalCase", "UPPER_CASE"],
                "leadingUnderscore": "forbid",
                "trailingUnderscore": "forbid"
            }
        ]
    ]
}
```

示例：

```ts
// good
let modalVisible: boolean = false;
const PERSON_NAMES: string[] = ['张三', '李四'];

function doSomething<T>(variable: T) {
  // do something
  return variable;
}

interface OptionItem {
  label: string;
  value: string;
}

enum FOUR_DIRECTION{ // or FourDirection
  Up,
  Down,
  Left,
  Right,
}

// bad
let modalvisible: boolean = false;
const PERSONNAMES: string[] = ['张三', '李四'];

function dosomthing<t>(Variable: t) {
  // do something
  return variable;
}

interface optionItem {
  Label: string;
  Value: string;
}

enum fourDirection {
  up,
  down,
  left,
  right,
}
```

### [建议] toString方法只在字符串化时提供有用信息的对象上调用

直接调用对象的toString方法，返回的往往不是一个期待的有用值。而是一个无用值 " [object Object] "。

所以要避免未定义toString未返回有用值时调用此方法。

解释：

对象的toString默认方法返回 " [object Object] "。

若未定义toString方法，当使用 + 或者 `${}`进行转化时将调用toString方法。将得到一个无用值。

注：函数本身还有可用toString方法。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-base-to-string": "warn"
}
```

示例：

```ts
// good

// 原类型的toSring方法可用
'Text:' + true; // Boolean

`Value: ${123}`;  // Number

`Arrays: ${[1, 2, 3]}`; // Array

(() => { }).toString(); // Function

// 在对象/类内定义一个可用的toString方法
const objectToString = {
  toString: () => 'Hello, world!',
};

`Object: ${objectToString}`;

class ObjectToString {
  toString() {
    return 'Hello, world!';
  }
}
`Class: ${new ObjectToString()}`;

// bad
//直接调用
'' + {};
{}.toString()

// 未定义toString方法调用
class ObjectNoToString { }
new ObjectNoToString() + '';
```

### [强制] 使用 === 判断时，不使用非空断言或者使用括号包裹

解释：

当同时使用非空断言 ! 与 === 进行运算时，容易和 !== 进行混淆。

适当忽略单变量非必要非空断言，以及组合值使用括号包裹进行区分。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-confusing-non-null-assertion": "error"
}
```

示例：

```ts
// good
interface Foo {
  bar?: string;
  num?: number;
}

const foo: Foo = {};

const isEqualsBar = foo.bar == 'hello';
const isEqualsNum = (1 + foo.num!) == 2;

// bad
const isEqualsBar = foo.bar! == 'hello';
const isEqualsNum = 1 + foo.num! == 2;
```

### [强制] 要求void类型的表达式出现在语句位置

解释：

返回非必要值会造成函数有返回值的错觉。导致代码逻辑混乱。可以适当使用逻辑运算符进行避免。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-confusing-void-expression": "error"
}
```

示例：

```ts
// good
alert('Hello, world!');

promise.then(value => {
  window.postMessage(value);
});

function doSomething() {
  if (!somethingToDo) {
    console.error('Nothing to do!');
    return;
  }

  console.log('Doing a thing...');
}

// 使用逻辑函数优化
cond && console.log('true');
cond || console.error('false');
cond ? console.log('true') : console.error('false');

// bad
// 忘记某些函数无返回值
const response = alert('Are you sure?');

// promise链中return非响应值
promise.then(value => window.postMessage(value));

// 给予函数返回值的错觉
function doSomething() {
  if (!somethingToDo) {
    return console.error('Nothing to do!');
  }

  console.log('Doing a thing...');
}
```

### [强制] 禁止重复的enum成员值

解释：

在一个枚举类型中，通常期望成员中具有唯一的值。重复的值会导致难以追踪的错误。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-duplicate-enum-values": "error"
}
```

示例：

```ts
// good
enum E {
  A = 0,
  B = 1,
}

enum E {
  A = 'A',
  B = 'B',
}

// bad
enum E {
  A = 0,
  B = 0,
}

enum E {
  A = 'A',
  B = 'A',
}
```

### [建议] 禁止对计算键表达式使用delete运算符

解释：

错误的常量可能获取错误的数据结构。但使用变量/计算值可能会偶尔导致边缘性错误，比如键名为“hasOwnProperty”。

如果要存储对象的集合，可以考虑使用Map或Set。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-dynamic-delete": "warn"
}
```

示例：

```ts
// good
const container: { [i: string]: number } = {
  /* ... */
};

delete container.aaa;
delete container[7];
delete container['-Infinity'];

// bad
delete container['aaa'];
delete container['Infinity'];

const name = 'name';
delete container[name];
delete container[name.toUpperCase()];
```

### [推荐] 不允许使用any类型 @佘采华

解释：
TypeScript 中的 any 类型是类型系统的一个危险的“escape hatch”。使用 any 会禁用许多类型检查规则，通常最好仅作为最后的手段或在制作原型代码时使用。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-explicit-any": "warn"
}
```

示例：

```ts
// good
const age: number = 17;
const ages: number[] = [17];
function greet(): string {}
function greet(): string[] {}

// bad
const age: any = 'seventeen';
const ages: any[] = ['seventeen'];
function greet(): any {}
function greet(): any[] {}
```

### [强制] 禁止额外的非空断言 @佘采华

解释：

【！】 TypeScript 中的非空断言运算符用于断言值的类型不包括 null 或 undefined。对单个值多次使用该运算符没有任何作用。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-extra-non-null-assertion": "error"
}
```

示例：

```ts
// good
const foo: { bar: number } | null = null;
const bar = foo!.bar;

function foo(bar: number | undefined) {
  const bar: number = bar!;
}
function foo(bar?: { n: number }) {
  return bar?.n;
}

// bad
const foo: { bar: number } | null = null;
const bar = foo!!!.bar;

function foo(bar: number | undefined) {
  const bar: number = bar!!!;
}

function foo(bar?: { n: number }) {
  return bar!?.n;
}
```

### [推荐] 不允许将类用作名称空间  @佘采华

解释：

此规则报告类何时没有非静态成员，例如专门用作静态名称空间的类。

来自 OOP 范式的用户可能会将他们的实用函数包装在一个额外的类中，而不是将它们放在 ECMAScript 模块的顶层。在 JavaScript 和 TypeScript 项目中通常不需要这样做。

- - 包装类在没有添加任何结构改进的情况下为代码增加了额外的认知复杂性

1. 1. 1. 无论放在它们上面的是什么，都已经在模块中组织好了，例如有作用的函数。
      2. 作为替代方案，您可以 import * as ... 模块以将所有这些都放在一个对象中导出。

- - 当你开始键入属性名称时，IDE 无法为静态类或命名空间导入的属性提供好的建议。
  - 当它们都在类中时，静态分析未使用变量等的代码会更加困难(see: [无效代码 (无效类型) in TypeScript](https://effectivetypescript.com/2020/10/20/tsprune))

XXX

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-extraneous-class": "warn"
}
```

示例：

```ts
// good
export const version = 42;

export function isProduction() {
  return process.env.NODE_ENV === 'production';
}

function logHelloWorld() {
  console.log('Hello, world!');
}

// bad
class StaticConstants {
  static readonly version = 42;

  static isProduction() {
    return process.env.NODE_ENV === 'production';
  }
}

class HelloWorldLogger {
  constructor() {
    console.log('Hello, world!');
  }
}
```

### 【强制】要求适当处理类似 Promise 的语句 @佘采华

解释：

“漂浮”Promise 是在没有设置任何代码来处理它可能抛出的任何错误的情况下创建的。漂浮 Promise 会导致几个问题，例如操作顺序不正确、忽略 Promise  reject等。

- - 当 Promise 创建但未正确处理时，此规则会报告。处理 Promise 值语句的有效方法包括:
    - await Promise
    - return Promise
    - .then() 两个参数处理
    - .catch()一个参数处理

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-floating-promises": "warn"
}
```

示例：

```ts
// good
const promise = new Promise((resolve, reject) => resolve('value'));
await promise;

async function returnsPromise() {
  return 'value';
}
returnsPromise().then(
  () => {},
  () => {},
);

Promise.reject('value').catch(() => {});

Promise.reject('value').finally(() => {});

// bad
const promise = new Promise((resolve, reject) => resolve('value'));
promise;

async function returnsPromise() {
  return 'value';
}
returnsPromise().then(() => {});

Promise.reject('value').catch();

Promise.reject('value').finally();
```

### 【强制】禁止使用 for-in 循环遍历数组。 @佘采华

解释：

for-in 循环 (for (var i in o)) 遍历对象的属性。虽然对数组类型使用 for-in 循环是合法的，但并不常见。 for-in 将迭代数组的索引作为字符串，省略数组中存在的“漏洞”。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-for-in-array": "error"
}
```

示例：

```ts
// good
declare const array: string[];

for (const value of array) {
  console.log(value);
}

for (let i = 0; i < array.length; i += 1) {
  console.log(i, array[i]);
}

array.forEach((value, i) => {
  console.log(i, value);
})

for (const [i, value] of array.entries()) {
  console.log(i, value);
}

// bad
declare const array: string[];

for (const i in array) {
  console.log(array[i]);
}

for (const i in array) {
  console.log(i, array[i]);
}
```

### 【强制】不允许对初始化为数字、字符串或布尔值的变量或参数进行显式类型声明。 @佘采华

解释：

TypeScript 能够从参数、属性和变量的默认值或初始值推断出它们的类型。无需在初始化为布尔值、数字或字符串的那些构造之一上使用显式 **[: ]**类型注释。这样做会给代码增加不必要的冗长 - 使其更难阅读 - 在某些情况下可以防止 TypeScript 推断更具体的文字类型（eg: 10）而不是更通用的原始类型（eg: number）。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-inferrable-types": "error"
}
```

示例：

```ts
// good
const a = 10n;
const a = BigInt(10);
const a = !0;
const a = Boolean(null);
const a = true;
const a = null;
const a = 10;
const a = Infinity;
const a = NaN;
const a = Number('1');
const a = /a/;
const a = new RegExp('a');
const a = `str`;
const a = String(1);
const a = Symbol('a');
const a = undefined;
const a = void someValue;

class Foo {
  prop = 5;
}

function fn(a = 5, b = true) {}

// bad
const a: bigint = 10n;
const a: bigint = BigInt(10);
const a: boolean = !0;
const a: boolean = Boolean(null);
const a: boolean = true;
const a: null = null;
const a: number = 10;
const a: number = Infinity;
const a: number = NaN;
const a: number = Number('1');
const a: RegExp = /a/;
const a: RegExp = new RegExp('a');
const a: string = `str`;
const a: string = String(1);
const a: symbol = Symbol('a');
const a: undefined = undefined;
const a: undefined = void someValue;

class Foo {
  prop: number = 5;
}

function fn(a: number = 5, b: boolean = true) {}
```

### [强制] 不允许泛型或返回类型之外的void类型。

解释：

void 指的是一个要忽略的函数返回。试图在返回类型或泛型类型参数之外使用void类型通常是程序员犯错的迹象，即使使用正确，void也会误导其他开发人员。

void类型意味着不能与任何其他类型混合，除了 never 类型，因为后者接受所有类型。如果你认为需要这样做，那么你可能需要使用 undefined 类型。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-invalid-void-type": "error"
}
```

示例：

```ts
// good
type NoOp = () => void;

function noop(): void {}

let trulyUndefined = void 0;

async function promiseMeSomething(): Promise<void> {}

type stillVoid = void | never;

// bad
type PossibleValues = string | number | void;
type MorePossibleValues = string | ((number & any) | (string | void));

function logSomething(thing: void) {}
function printArg<T = void>(arg: T) {}

logAndReturn<void>(undefined);

interface Interface {
  lambda: () => void;
  prop: void;
}

class MyClass {
  private readonly propName: void;
}
```

### [强制] 不允许使用 void 运算符，除非用于丢弃值。

解释：

void 指的是一个要忽略的函数返回。void 运算符是传达程序员丢弃值这一意图的有效工具。

该规则有助于捕获 API 更改，之前在调用站点丢弃了一个值，但被调用者已更改，因此不再返回值。 当与 no-unused-expressions 结合使用时，它还可以通过确保一致性来帮助代码的读者，如：类似 void foo(); 的语句， 总是丢弃一个返回值；类似 foo() 的语句， 永远不会丢弃返回值。 该规则报告参数已为 void 或undefined 类型的任何 void 运算符。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-meaningless-void-operator": "error"
}
```

示例：

```ts
// good
(() => {})();

function foo() {}
foo(); // nothing to discard

function bar(x: number) {
  void x; // discarding a number
  return 2;
}
void bar(); // discarding a number

// bad
void (() => {})();

function foo() {}
void foo();
```

### [强制] 强制执行 new 和构造函数的有效定义。

解释：

JavaScript 类可以定义一个构造方法，该方法在新创建类实例时运行。 TypeScript 允许描述静态类对象的接口定义 new() 方法（尽管这在现实世界的代码中很少使用）。 刚接触 JavaScript 类或 TypeScript 接口的开发人员有时可能会混淆何时使用构造函数或 new。

当一个类定义了一个名为 new 的方法或一个接口定义了一个名为 constructor 的方法时，该规则会报告。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-misused-new": "error"
}
```

示例：

```ts
// good
declare class C {
  constructor();
}

interface I {
  new (): C;
}

// bad
declare class C {
  new(): C;
}

interface I {
  new (): I;
  constructor(): void;
}
```

### [强制] 不允许在不适合使用 Promises 的地方使用它们。

解释：

该规则禁止在 TypeScript 编译器允许但不适合的地方使用 Promise 来进行逻辑运算，例如 if 语句。 这些情况通常是由于缺少 await 关键字或只是误解了处理/等待异步函数的使用方式而引起的。

no-misused-promises 仅检测向不正确的逻辑位置提供 Promise 的代码。 请参阅 no-floating-promises 以检测未处理的 Promise 语句。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-misused-promises": "error"
}
```

示例：

```ts
// good
const promise = Promise.resolve('value');

// Always `await` the Promise in a conditional
if (await promise) {
  // Do something
}

const val = (await promise) ? 123 : 456;

while (await promise) {
  // Do something
}


// bad
const promise = Promise.resolve('value');

if (promise) {
  // Do something
}

const val = promise ? 123 : 456;

while (promise) {
  // Do something
}
```

### [强制] 禁止命名空间 namespace

解释：

TypeScript 曾经允许一种称为“自定义模块”（ module Example {} ）的代码组织形式，后来被重命名为“命名空间”（namespace Example）。命名空间是组织TypeScript 代码的过时方法。现在首选ES2015模块语法（ import/export）。

此规则不会报告使用 TypeScript 模块声明来描述外部 API（declare module 'foo' {}）。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-namespace": "error"
}
```

示例：

```ts
// good
declare module 'foo' {}

// anything inside a d.ts file

// bad
module foo {}
namespace foo {}

declare module foo {}
declare namespace foo {}
```

### [强制] 不允许在空合并运算符的左操作数中使用非空断言

解释：

?? 运算符允许在处理 null 或 undefined 时提供默认值。 在零合并运算符的左侧使用 ! 运算符是多余的，并且可能标志着程序员错误或混淆了这两个运算符。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-non-null-asserted-nullish-coalescing": "error"
}
```

示例：

```ts
// good
foo ?? bar;
foo ?? bar!;
foo!.bazz ?? bar;
foo!.bazz ?? bar!;
foo() ?? bar;

// This is considered correct code because there's no way for the user to satisfy it.
let x: string;
x! ?? '';

// bad
foo! ?? bar;
foo.bazz! ?? bar;
foo!.bazz! ?? bar;
foo()! ?? bar;

let x!: string;
x! ?? '';

let x: string;
x = foo();
x! ?? '';
```

### [推荐] 不允许非空断言与可选链同时使用

解释：

对可选链提供的可能为空的值进行非空断言很可能是错误的。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "typescript-eslint/no-non-null-asserted-optional-chain": "warn"
}
```

示例：

```ts
// good
foo?.bar;
foo?.bar();

// bad
foo?.bar!;
foo?.bar()!;
```

### [推荐] 不使用非空断言

解释：

使用断言来绕过编译的非空判断，通常意味着代码本身的类型安全可能存在隐患。通常更好的做法是重构代码逻辑，使Typescript能够理解什么时候值可能是空的。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-non-null-assertion": "warn"
}
```

示例：

```ts
interface Example {
  property?: string;
}
declare const foo: Example;

// good
const includesBaz = foo.property?.includes('baz') ?? false;

// bad
const includesBaz = foo.property!.includes('baz');
```

### [强制] 不允许冗余的类型成员

解释：

在联合类型( | )或者交叉类型( & )中，一些类型成员可以覆盖其他类型成员，或者被其他类型成员所覆盖。在以下规则中，部分的类型成员在联合类型/交叉类型中会不起作用。

联合类型中：

- - any 和 unknown 覆盖其他所有联合类型成员
  - never 会在联合类型中不起作用，除非是在返回值类型中
  - 基本数据类型会覆盖其任何一个字面量类型，例如string会覆盖''

交叉类型中：

- - any 和 never 覆盖其他所有交叉类型成员
  - unknown 在交叉类型中不起作用
  - 字面量类型覆盖所有基本数据类型
  - 字面量类型例如""覆盖其对应的任何基本数据类型，例如string

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-redundant-type-constituents": "error"
}
```

示例：

```ts
// good
type UnionAny = any;
type UnionUnknown = unknown;
type UnionNever = never;

type UnionBooleanLiteral = boolean;
type UnionNumberLiteral = number;
type UnionStringLiteral = string;

type IntersectionAny = any;
type IntersectionUnknown = string;
type IntersectionNever = string;

type IntersectionBooleanLiteral = false;
type IntersectionNumberLiteral = 1;
type IntersectionStringLiteral = 'foo';

// bad
type UnionAny = any | 'foo';
type UnionUnknown = unknown | 'foo';
type UnionNever = never | 'foo';

type UnionBooleanLiteral = boolean | false;
type UnionNumberLiteral = number | 1;
type UnionStringLiteral = string | 'foo';

type IntersectionAny = any & 'foo';
type IntersectionUnknown = string & unknown;
type IntersectionNever = string | never;

type IntersectionBooleanLiteral = boolean & false;
type IntersectionNumberLiteral = number & 1;
type IntersectionStringLiteral = string & 'foo';
```

### [强制] 不允许使用require引入模块

解释：

请使用ES6的import代替require。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-require-imports": "error"
}
```

示例：

```ts
// good
import * as lib1 from 'lib1';
import { lib2 } from 'lib2';
import * as lib3 from 'lib3';

// bad
const lib1 = require('lib1');
const { lib2 } = require('lib2');
import lib3 = require('lib3');
```

### [推荐] 不为this设置别名

解释：

将this赋值给一个变量而非直接使用箭头函数，如果不是在非ES6的旧项目中，就需要考虑是否未能合理地管理好作用域。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-this-alias": "warn"
}
```

示例：

```ts
// good
setTimeout(() => {
  this.doWork();
});

// bad
const self = this;

setTimeout(function () {
  self.doWork();
});
```

### [推荐] 不使用不必要的布尔字面量进行比较

解释：

当一个变量只可能是布尔类型时，直接使用变量的值本身或其一元否定进行判断更加简洁明了。而当变量的类型是包含布尔值的联合类型时，用布尔字面量进行对比才是有意义的。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unnecessary-boolean-literal-compare": "warn"
}
```

示例：

（注：尽管示例中均使用了严格相等===，但是实际上宽松相等==也是如此）

```ts
// good
declare const someCondition: boolean;
if (someCondition) {
}

declare const someObjectBoolean: boolean | Record<string, unknown>;
if (someObjectBoolean === true) {
}

declare const someStringBoolean: boolean | string;
if (someStringBoolean === true) {
}

// bad
declare const someCondition: boolean;
if (someCondition === true) {
}
```

### [推荐]避免不必要的条件判断

解释：

当一个表达式被用于条件判断时，如果已经能根据表达式的类型推断出表达式的计算结果为真或者为假，那么这个条件判断是不必要的。涉及这一规则的场景有：

- `&&`,`||`和`?:`（三元）运算符的参数
- `if`, `for`, `while`, 和`do-while`语句的条件
- 可选链表达式的基值

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unnecessary-condition": "warn"
}
```

示例：

```ts
// good
function head<T>(items: T[]) {
  // items有可能是个空数组，所以这里的判断是有必要的
  if (items.length) {
    return items[0].toUpperCase();
  }
}

function foo(arg: string) {
  // arg有可能是空字符串，所以这里的判断是有必要的
  if (arg) {
  }
}

// bad
function foo(arg: 'bar' | 'baz') {
  // arg 不会为null、undefined或者空字符串，所以这个if条件判断是不必要的
  if (arg) {
  }
}

function bar<T>(arg: string) {
  // arg 不会为null、undefined, 所以 ?. 操作符是不必要的
  return arg?.length;
}
```

### [推荐]避免不必要的限定符

解释：

在TypeScript中，enum和namespace的成员通常通过限定属性的方式进行访问，例如`Enum.member`。但是当在enum或namespace的内部访问其成员时，可以不加限定符。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unnecessary-qualifier": "warn"
}
```

示例：

```ts
// good
enum A {
  B,
  C = B,
}
namespace A {
  export type B = number;
  const x: B = 3;
}

// bad
enum A {
  B,
  // 在枚举A内部访问B，可以直接访问B，而不是通过A.B的方式
  C = A.B,
}
namespace A {
  export type B = number;
  // 在命名空间A内部访问B，可以直接访问B，而不是通过A.B的方式
  const x: A.B = 3;
}
```

### [推荐]避免不必要的类型参数

解释：

在TypeScript中定义函数、接口、类时可以为类型参数指定一个默认值，例如 `function f<T = number>(...) {...}`，在使用它的时候如果将要显式指定的类型参数的值，如`f<number>(...)`等于该类型参数的默认值，则无需显式指定。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unnecessary-type-arguments": "warn"
}
```

示例：

```ts
// good
class C<T = number> {}
new C();
// 类型参数默认值是number，此处可以显示指定为string
new C<string>();

class D extends C {}
// 类型参数默认值是number，此处可以显示指定为string
class D extends C<string> {}

interface I<T = number> {}
// 类型参数默认值是number，此处可以显示指定为string
class Impl implements I<string> {}

// bad
class C<T = number> {}
// 类型参数默认值是number，此处无需指定
new C<number>();

class D extends C<number> {}
// 类型参数默认值是number，此处无需指定
interface I<T = number> {}
class Impl implements I<number> {}
```

### [强制]禁止不必要的类型断言

解释：

在TypeScript中可以使用类型断言来告诉编译器当前表达式的类型，但是如果断言的结果与编译器预期的类型相同，则应该避免将断言留在代码中，这会影响可读性。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unnecessary-type-assertion": "error"
}
```

示例：

```ts
// good
const foo = <number>3;

const foo = 3 as number;

// 此处存疑，@see https://typescript-eslint.io/rules/no-unnecessary-type-assertion
// 是否应该作为bad case的示例
const foo = 'foo' as const;

function foo(x: number | undefined): number {
    // 此处非null和非undefined的类型断言是有必要的
    return x!;
}

// bad
const foo = 3;
const bar = foo!;

const foo = <3>3;

type Foo = 3;
const foo = <Foo>3;

type Foo = 3;
const foo = 3 as Foo;

function foo(x: number): number {
    // 此处非null和非undefined的类型断言是不必要的
    return x!;
}
```

### [强制]禁止不必要的类型约束

解释：

在TypeScript中泛型参数<T>可以被关键字`extends`约束，当没有提供`extends`关键字时，默认为`extends any`，因此手动的`extends any`是不必要的。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unnecessary-type-constraint": "error"
}
```

示例：

```ts
// good
interface Foo<T> {}

type Bar<T> = {};

class Baz<T> {
  qux<U> { }
}

const Quux = <T>() => {};

function Quuz<T>() {}

// bad
interface FooAny<T extends any> {}

type BarAny<T extends any> = {};

class BazAny<T extends any> {
  quxAny<U extends any>() {}
}

const QuuxAny = <T extends any>() => {};

function QuuzAny<T extends any>() {}
```

### [强制] 禁止在函数调用时使用any类型的参数

解释：

在TypeScript中使用any通常是一个危险的信号，any会导致跳过许多类型检查规则，因此在函数调用时使用any类型的参数会产生潜在的安全漏洞和错误。涉及这一规则的场景有：

- 函数调用时禁止使用any类型的参数
- 函数调用时禁止使用包含any类型元素的数组或元祖
- 禁止将any类型传递给明确指定类型的泛型参数

允许将any类型的参数传递给unknow类型，例如：

```js
declare function foo(arg1: unknown, arg2: Set<unkown>, arg3: unknown[]): void;
foo(1 as any, new Set<any>(), [] as any[]);
```

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unsafe-argument": "error"
}
```

示例：

```ts
// good
declare function foo(arg1: string, arg2: number, arg3: string): void;

foo('a', 1, 'b');

const tuple1 = ['a', 1, 'b'] as const;
foo(...tuple1);

declare function bar(arg1: string, arg2: number, ...rest: string[]): void;
const array: string[] = ['a'];
bar('a', 1, ...array);

declare function baz(arg1: Set<string>, arg2: Map<string, string>): void;
foo(new Set<string>(), new Map<string, string>());
// bad
declare function foo(arg1: string, arg2: number, arg3: string): void;

const anyTyped = 1 as any;

foo(...anyTyped);
foo(anyTyped, 1, 'a');

const anyArray: any[] = [];
foo(...anyArray);

const tuple1 = ['a', anyTyped, 'b'] as const;
foo(...tuple1);

const tuple2 = [1] as const;
foo('a', ...tuple, anyTyped);

declare function bar(arg1: string, arg2: number, ...rest: string[]): void;
const x = [1, 2] as [number, ...number[]];
foo('a', ...x, anyTyped);

declare function baz(arg1: Set<string>, arg2: Map<string, string>): void;
foo(new Set<any>(), new Map<any, string>());
```

### [强制]禁止使用不安全的赋值

解释：

在 `TypeScript`中 `any`是一个危险的事情，使用它会跳过很多类型检测，因此最好作为最后的招数，或者仅 demo 代码中使用。尽管你初衷是好的，但 `any` 类型很容易造成代码漏洞，另外将 `any` 赋值给变量之后会变得很难理解。

此规则不允许将 `any` 分配给变量，或者将 `any[]` 分配给数组。此规则还会针对泛型类型的参数进行检查，例如，如果将 `Set<any>` 分配给声明为 `Set<string>`的变量的时候，就会报错。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unsafe-assignment": "error"
}
```

示例：

```ts
// good
const x = 1, y = 1;
const [x] = [1];
[x] = [1] as [number];

function foo(a = 1) {}
class Foo {
  constructor(private a = 1) {}
}
class Foo {
  private a = 1;
}

const x: Set<string> = new Set<string>();
const x: Map<string, string> = new Map<string, string>();
const x: Set<string[]> = new Set<string[]>();
const x: Set<Set<Set<string>>> = new Set<Set<Set<string>>>();

// bad
const x = 1 as any, y = 1 as any;
const [x] = 1 as any;
const [x] = [] as any[];
const [x] = [1 as any];
[x] = [1] as [any];

function foo(a = 1 as any) {}
class Foo {
  constructor(private a = 1 as any) {}
}
class Foo {
  private a = 1 as any;
}

const x: Set<string> = new Set<any>();
const x: Map<string, string> = new Map<string, any>();
const x: Set<string[]> = new Set<any[]>();
const x: Set<Set<Set<string>>> = new Set<Set<Set<any>>>();
```

以下这种场景，将 `any`赋值给 `unknown`是可以的。

```ts
const x: unknown = y as any;
const x: unknown[] = y as any[];
const x: Set<unknown> = y as Set<any>;
```

### [强制]禁止使用不安全的调用

解释：

在 `TypeScript`中 `any`是一个危险的事情，使用它会跳过很多类型检测，因此最好作为最后的招数，或者仅 demo 代码中使用。尽管你初衷是好的，但 `any` 类型很容易造成代码漏洞，另外将 `any` 赋值给变量之后会变得很难理解。

此规则不允许调用类型为 any 的方法。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unsafe-call": "error"
}
```

示例：

```ts
// good
declare const typedVar: () => void;
declare const typedNested: { prop: { a: () => void } };

typedVar();
typedNested.prop.a();

(() => {})();

new Map();

String.raw`foo`;

// bad
declare const anyVar: any;
declare const nestedAny: { prop: any };

anyVar();
anyVar.a.b();

nestedAny.prop();
nestedAny.prop['a']();

new anyVar();
new nestedAny.prop();

anyVar`foo`;
nestedAny.prop`foo`;
```

###

### [强制]禁止使用不安全的声明合并

解释：

在 `TypeScript`中，支持对相同的命名进行声明合并。但是类和接口之间的声明合并是不安全的。

因为 `TypeScript` 编译器不会检查属性是否已经初始化，就会导致这些代码运行时发生错误。

```ts
interface Foo {
  nums: number[];
}

class Foo {}

const foo = new Foo();

foo.nums.push(1); // Runtime Error: Cannot read properties of undefined.
```

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unsafe-declaration-merging": "error"
}
```

示例：

```ts
// good
interface Foo {}
class Bar implements Foo {}

namespace Baz {}
namespace Baz {}
enum Baz {}

namespace Qux {}
function Qux() {}

// bad
interface Foo {}

class Foo {}
```

###

### [强制]禁止访问不安全的成员

解释：

在 `TypeScript`中 `any`是一个危险的事情，使用它会跳过很多类型检测，因此最好作为最后的招数，或者仅 demo 代码中使用。尽管你初衷是好的，但 `any` 类型很容易造成代码漏洞，另外将 `any` 赋值给变量之后会变得很难理解。

此规则禁止访问 `any` 类型的成员。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unsafe-member-access": "error"
}
```

示例：

```ts
// good
declare const properlyTyped: { prop: { a: string } };

properlyTyped.prop.a;
properlyTyped.prop['a'];

const key = 'a';
properlyTyped.prop[key];

const arr = [1, 2, 3];
arr[1];
const idx = 1;
arr[idx];
arr[idx++];


// bad
declare const anyVar: any;
declare const nestedAny: { prop: any };

anyVar.a;
anyVar.a.b;
anyVar['a'];
anyVar['a']['b'];

nestedAny.prop.a;
nestedAny.prop['a'];

const key = 'a';
nestedAny.prop[key];

// Using an any to access a member is unsafe
const arr = [1, 2, 3];
arr[anyVar];
nestedAny[anyVar];
```

###

### [强制]禁止返回不安全的内容

解释：

在 `TypeScript`中 `any`是一个危险的事情，使用它会跳过很多类型检测，因此最好作为最后的招数，或者仅 demo 代码中使用。尽管你初衷是好的，但 `any` 类型很容易造成代码漏洞，另外将 `any` 赋值给变量之后会变得很难理解。

此规则禁止函数返回 `any`或者 `any[]`，另外也会针对泛型类型的参数进行检查，例如，如果函数定义返回是 `Set<string>`的时候，实际返回了 `Set<any>` 也会报错。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-unsafe-return": "error"
}
```

示例：

```ts
// good
function foo1() {
  return 1;
}
function foo2() {
  return Object.create(null) as Record<string, unknown>;
}

const foo3 = () => [];
const foo4 = () => ['a'];

function assignability1(): Set<string> {
  return new Set<string>(['foo']);
}
type TAssign = () => Set<string>;
const assignability2: TAssign = () => new Set(['foo']);


// bad
function foo1() {
  return 1 as any;
}
function foo2() {
  return Object.create(null);
}
const foo3 = () => {
  return 1 as any;
};
const foo4 = () => Object.create(null);

function foo5() {
  return [] as any[];
}
function foo6() {
  return [] as Array<any>;
}
function foo7() {
  return [] as readonly any[];
}
function foo8() {
  return [] as Readonly<any[]>;
}
const foo9 = () => {
  return [] as any[];
};
const foo10 = () => [] as any[];

const foo11 = (): string[] => [1, 2, 3] as any[];

// generic position examples
function assignability1(): Set<string> {
  return new Set<any>([1]);
}
type TAssign = () => Set<string>;
const assignability2: TAssign = () => new Set<any>([true]);
```

不过，定义 `unknown`实际返回 `any`是可以的，例如以下这种场景

```ts
function foo1(): unknown {
  return JSON.parse(singleObjString); // Return type for JSON.parse is any.
}

function foo2(): unknown[] {
  return [] as any[];
}
```

### [强制]禁止导出无用的空字符集

解释：

空的 `export {}` 语句有时在 `TypeScript` 代码中很有用，可以将本来是脚本文件的文件转换为模块文件。参考 [TypeScript Handbook Modules page](https://www.typescriptlang.org/docs/handbook/modules.html)。但是，如果文件中有任何其他顶级导入或导出语句，则 `export {}` 语句不会执行任何操作。此条规则禁止再 `ES Module`中导出无用的 `export {}`语句。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-useless-empty-export": "error"
}
```

示例：

```ts
// good
export const value = 'Hello, world!';

import 'some-other-module';

// bad
export const value = 'Hello, world!';
export {};

import 'some-other-module';
export {};
```

### [强制] 禁止在非模块导入语句中使用 `require`

解释：

禁止使用类似 `var foo = require("foo")`的语句。应当使用 ES6 样式的模块导入或 `import foo = require("foo")`的形式替代。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/no-var-requires": "error"
}
```

示例：

```ts
// good
import foo = require('foo');
require('foo');
import foo from 'foo';

// bad
var foo = require('foo');
const foo = require('foo');
let foo = require('foo');
```

### [推荐] 有明确类型定义时应使用非空断言

解释：

在 TypeScript 中断言一个不是 `null` 或 `undefined` 的值通常有两种方法：

- `!` : 非空断言
- `as` : 使用等效类型的传统类型断言

一般来说使用 `!` 非空断言更好。因为代码量更少，并且当类型变化时不会无法同步。当 `as` 关键字与 `!` 起到相同的作用时，将触发本条规则，并建议用 `!` 修复代码。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/non-nullable-type-assertion-style": "warn"
}
```

示例：

```ts
// good
const maybe = Math.random() > 0.5 ? '' : undefined;

const definitely = maybe!;
const alsoDefinitely = maybe!;

// bad
const maybe = Math.random() > 0.5 ? '' : undefined;

const definitely = maybe as string;
const alsoDefinitely = <string>maybe;
```

### [强制] 字面类型强制使用 `as const`

解释：

有两种常用的方法告诉 TypeScript 一个字面量应当被解释为字面类型本身（例如 `2`）而不是一般的通用类型（例如 `number`）：

- `as const` : 让 TypeScript 自动推断字面类型
- `as` 加字面类型: 为 TypeScript 显式指定字面类型

一般来说更加推荐 `as const`，因为无需再次输入一遍字面值。当 `as` 加字面类型可以被 `as const` 替代时，将触发本规则。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-as-const": "error"
}
```

示例：

```ts
// good
let foo = 'bar';
let foo = 'bar' as const;
let foo: 'bar' = 'bar' as const;
let bar = 'bar' as string;
let foo = <string>'bar';
let foo = {bar: 'baz'};

// bad
let bar: 2 = 2;
let foo = <'bar'>'bar';
let foo = {bar: 'baz' as 'baz'};
```

### [推荐] 每个枚举成员必须被显式初始化

解释：

在 TypeScript 中使用 `enum` 来组织语义化相关的常量是一个很实用的方法。`enum` 中没有指定具体值的枚举成员默认会被赋予一个自增的数字。

当一个项目中 `enum` 枚举成员的值很重要时，如果 `enum` 之后被修改，允许包含隐含值的枚举可能会造成缺陷。

本规则建议每个 `enum` 成员都显式指定初始化的值。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-enum-initializers": "warn"
}
```

示例：

```ts
// good
enum Status {
    Open = 'Open',
    Close = 'Close'
}

enum Direction {
    Up = 1,
    Down = 2
}

enum Color {
    Red = 'Red',
    Green = 'Green',
    Blue = 'Blue'
}

// bad
enum Status {
    Open = 1,
    Close
}

enum Direction {
    Up,
    Down
}

enum Color {
    Red,
    Green = 'Green'
    Blue = 'Blue'
}
```

### [推荐] 尽可能使用 `for-of` 循环而不是标准 `for` 循环

解释：

许多开发者默认用 `for (let i = 0; i < ...` 循环来遍历数组。然而，这些数组中很大一部分的循环遍历变量（例如 `i`）只是用来访问数组中每个元素。这种情况下，`for-of` 循环读写起来更加容易。

本规则建议当循环索引只是用来访问被遍历数组的时候，应当使用 for-of 循环。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-for-of": "warn"
}
```

示例：

```ts
// good
declare const array: string[];

for (const x of array) {
    console.log(x);
}

for (let i = 0; i < array.length; i++) {
    // 使用到了 i，因此可以不用 for-of
    console.log(i, array[i]);
}

// bad
declare const array: string[];

for (let i = 0; i < array.length; i++) {
    console.log(array[i]);
}
```

### [推荐] 使用函数类型而不是含有调用签名的接口

解释：

TypeScript 允许两种常见的方法来定义一个函数的类型：

- 函数类型: `() => string`
- 含有签名的对象: `{(): string}`

通常应当尽可能使用函数类型方式，因为这样更加简单明了。

本规则建议使用函数类型而不是含有调用签名的接口或对象类型。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-function-type": "error"
}
```

示例：

```ts
// good
type Example = () => string;

function foo(example: () => number): number {
    return bar();
}

// 返回函数本身，而不是 `this` 参数。
type ReturnsSelf = (arg: string) => ReturnsSelf;

function foo(bar: {(): string; baz: number}): string {
    return bar();
}

interface Foo {
    bar: string;
}
interface Bar extends Foo {
    (): void;
}

// 允许使用多个调用签名（重载）：
interface Overloaded {
    (data: string): number;
    (id: number): string;
}
// 等同于重载的接口
type Intersection = ((data: string) => number) & ((id: number) => string);

// bad
interface Example {
    (): string;
}

function foo(example: {(): number }): number {
    return example();
}

interface ReturnsSelf {
    // 返回函数本身，而不是 `this` 参数。
    (arg: string): this;
}
```

### [推荐]推荐使用`includes`

解释：

在ES2015规范发布之前，检测数组和字符串中是否存在某个值的标准方法是判断`Array#indexOf` 和 `String#indexOf`是否等于`-1`。
在ES2015规范之后，增加了`String#includes`（ES2015）和`Array#includes`(ES2016)来检测数组和字符串中是否存在某个值。

为了代码的可读性，本条规则推荐使用`.includes`方法替代之前的`indexOf`方法。此外，同样推荐使用`.includes`方法替代简单的正则表达式校验方法。

> 本条规则同样适用于同时拥有`indexOf`和`includes`方法且两个方法具有相同入参类型的自定义对象

检查判断String中是否存在在ES2015之前

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-includes": "warn"
}
```

示例：

```ts
// good
const str: string;
const array: any[];
const readonlyArray: ReadonlyArray<any>;
const typedArray: UInt8Array;
const maybe: string;
const userDefined: {
  indexOf(x: any): number;
  includes(x: any): boolean;
};

str.includes(value);
array.includes(value);
readonlyArray.includes(value);
typedArray.includes(value);
maybe?.includes('');
userDefined.includes(value);

str.includes('example');

// 如果自定义的 indexOf 和 includes 方法的入参类型不一致，则本条规则不适用
declare const mismatchExample: {
  indexOf(x: unknown, fromIndex?: number): number;
  includes(x: unknown): boolean;
};
mismatchExample.indexOf(value) >= 0;

// bad
const str: string;
const array: any[];
const readonlyArray: ReadonlyArray<any>;
const typedArray: UInt8Array;
const maybe: string;
const userDefined: {
  indexOf(x: any): number;
  includes(x: any): boolean;
};

str.indexOf(value) !== -1;
array.indexOf(value) !== -1;
readonlyArray.indexOf(value) === -1;
typedArray.indexOf(value) > -1;
maybe?.indexOf('') !== -1;
userDefined.indexOf(value) >= 0;

/example/.test(str);
```

### [强制]使用常量枚举类型

解释：

TypeScript允许枚举类型的值是有效的js表达式。但由于枚举内部是单独的作用域，且每个枚举类型是当前作用域中的一个变量，所以会产生一些违反直觉的结果。
例如：

```ts
const imOutside = 2;
const b = 2;
enum Foo {
  outer = imOutside,
  a = 1,
  b = a,
  c = b,
  // 此时c的值是什么
 // c == Foo.b == Foo.c == 1?
  // c == b == 2?
}
```

> 答案： `c` 等于 `1`

因此，为了防止出现这些问题，此规则要求使用常量作为枚举类型的值，但允许常量进行位运算。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-literal-enum-member": ["error", {"allowBitwiseExpressions": true}]
}
```

示例：

```ts
// good
enum Valid {
  A,
  B = 'TestStr', // 字符串类型
  C = 4, // 数字类型
  D = null,
  E = /some_regex/, // 正则表达式
  F = 0 << 2, // 常量位运算
}


// bad
const str = 'Test';
const num = 0;
enum Invalid {
  A = str, // 变量
  B = {}, // 对象
  C = `A template literal string`, // 模板字面量
  D = new Set(1, 2, 3), // 构造函数
  E = 2 + 2, // 表达式
  F = num << 1; // 非常量位运算
}
```

### [推荐] 使用空值合并运算符

解释：

空值合并运算符（??）是一个逻辑运算符，当左侧的操作数为 null 或者 undefined 时，返回其右侧操作数，否则返回左侧操作数。

逻辑或运算符（||）会在左侧操作数为假值时返回右侧操作数。

所以在处理假值相关的操作时，本条规则推荐使用空值合并运算符代替逻辑或运算符。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-nullish-coalescing": "warn"
}
```

示例：

```ts
// good
const foo: any = 'bar';
foo ?? 'a string';

const foo: string | undefined = 'bar';
foo ?? 'a string';

const foo: string | null = 'bar';
foo ?? 'a string';

declare const a: string | null;
declare const b: string | null;
declare const c: string | null;

if (a ?? b) {
}
while (a ?? b) {}
do {} while (a ?? b);
for (let i = 0; a ?? b; i += 1) {}
a ?? b ? true : false;

// 由于 ?? 运算符的没有算数优先级，所以需要使用括号隔离??运算符
a ?? (b && c);


// bad
```

### [推荐]优先使用可选链运算符 @常宇清

解释：

可选链表达式（`?.`）在访问值为`null`或者`undfined`的对象时，会返回`undfined`。并且相对于逻辑与运算符（`&&`）来说，可选运算符写法更加简洁。
本条规则推荐使用`?.`替代`&&`

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-optional-chain": "warn"
}
```

示例：

```ts
// good
foo?.a?.b?.c;
foo?.['a']?.b?.c;
foo?.a?.b?.method?.();

foo?.a?.b?.c?.d?.e;

!foo?.bar;
!foo?.[bar];
!foo?.bar?.baz?.();


// bad
foo && foo.a && foo.a.b && foo.a.b.c;
foo && foo['a'] && foo['a'].b && foo['a'].b.c;
foo && foo.a && foo.a.b && foo.a.b.method && foo.a.b.method();

// With empty objects
(((foo || {}).a || {}).b || {}).c;
(((foo || {})['a'] || {}).b || {}).c;

// With negated `or`s
!foo || !foo.bar;
!foo || !foo[bar];
!foo || !foo.bar || !foo.bar.baz || !foo.bar.baz();

// this rule also supports converting chained strict nullish checks:
foo &&
  foo.a != null &&
  foo.a.b !== null &&
  foo.a.b.c != undefined &&
  foo.a.b.c.d !== undefined &&
  foo.a.b.c.d.e;
```

### [推荐]优先使用readonly属性 @常宇清

解释：

如果一个成员变量被标记为`private`，则这个变量不应该在类定义之外访问，同时，如果这个私有成员变量只在构造函数中被修改,则他同时应当被标记为`readonly`。
本条规则推荐使用`readonly`标记只在构造函数中修改的私有成员变量。

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-readonly": "warn"
}
```

示例：

```ts
// good
class Container {
  // 公共成员变量有可能被外部修改
  public publicMember: boolean;

  // 受保护的成员变量有可能被子类修改
  protected protectedMember: number;

  // 私有成员变量有可能被当前类的非构造函数修改
  private modifiedLater = 'unchanged';

  // 如果当前私有成员变量没有被当前类的非构造函数修改，则需要加上`readonly`
  private readonly neverModifiedMember = true;

  public mutate() {
    this.modifiedLater = 'mutated';
  }
}

// bad
class Container {
  // 这个成员变量应该标记为`readonly`
  private neverModifiedMember = true;
  private onlyModifiedInConstructor: number;

  public constructor(
    onlyModifiedInConstructor: number,
    // 私有形参同样应该标记为`readonly`
    private neverModifiedParameter: string,
  ) {
    this.onlyModifiedInConstructor = onlyModifiedInConstructor;
  }
}
```

### [推荐]当返回当前类型时 ，优先使用`this`

解释：

方法链是 OOP 语言中一种常见的设计模式，Typescript为这种模式提供了一个动态的`this`类型。当类方法显示声明返回值类型为当前类时，会导致当前类的子类调用该方法时返回的值是父类的类型，而不是子类的类型。例如：

```ts
class Animal {
  eat(): Animal {
    //   ~~~~~~
    // 为了避免下面这个错误，
    // 你可以移除这个声明
    // 或者使用`this`替代它
    console.log("I'm moving!");
    return this;
  }
}

class Cat extends Animal {
  meow(): Cat {
    console.log('Meow~');
    return this;
  }
}

const cat = new Cat();
cat.eat().meow();
//        ~~~~
// Error: Property 'meow' does not exist on type 'Animal'.
// 因为`eat`方法返回的是`Animal`类型，但`Animal`类型中不存在`meow`方法
```

本条规则推荐使用`this`类型替代显示声明当前类的类型

你可以使用以下 ESLint 规则进行自动化检测：

```json
{
    "@typescript-eslint/prefer-return-this-type": "warn"
}
```

示例：

```ts
// good
class Foo {
  f1(): this {
    return this;
  }
  f2() {
    return this;
  }
  f3 = (): this => {
    return this;
  };
  f4 = () => {
    return this;
  };
}

class Base {}
class Derived extends Base {
  f(): Base {
    return this;
  }
}
// bad
class Foo {
  f1(): Foo {
    return this;
  }
  f2 = (): Foo => {
    return this;
  };
  f3(): Foo | undefined {
    return Math.random() > 0.5 ? this : undefined;
  }
}
```

### [推荐]优先使用String#startsWith和 String#endsWith

解释：

JS中有多种方式判断字符串string是否由特定字符串开头或结尾，例如：foo.indexOf('bar') === 0。ES5中推荐使用String#startsWith和 String#endsWith来判断。统一使用这些方法来判断可提高代码可读性。

当代码中的方法完全可以使用String#startsWith或 String#endsWith 替代时则会命中此规则。此规则无其他配置项。

```json
{
    "@typescript-eslint/prefer-string-starts-ends-with": "warn"
}
```

示例：

```ts
// good
declare const foo: string;

// starts with
foo.startsWith('bar');

// ends with
foo.endsWith('bar');

// bad
declare const foo: string;

// starts with
foo.indexOf('bar') === 0;
foo.slice(0, 3) === 'bar';
foo.substring(0, 3) === 'bar';
foo.match(/^bar/) != null;
/^bar/.test(foo);

// ends with
foo[foo.length - 1] === 'b';
foo.charAt(foo.length - 1) === 'b';
foo.lastIndexOf('bar') === foo.length - 3;
```

### [强制]强制使用ts-expect-error替代ts-ignore

解释：

TypeScript可以通过在代码行开头添加@ts-ignore和@ts-expect-error标记豁免单行代码的TS报错信息。这两个标记的表现基本一致，主要区别在，@ts-expect-error如果在正确的代码行前标记会抛出type error，@ts-ignore则不会。

因此即便问题代码已修复，原有的@ts-ignore 易被遗漏，仍留在代码中。若出现新的问题又会被遗留的@ts-ignore 忽略，不能及时修复。推荐优先使用@ts-expect-error。

```json
{
    "@typescript-eslint/prefer-ts-expect-error": "error"
}
```

示例：

```ts
// good

// @ts-expect-error
const str: string = 1;

/**
 * Explaining comment
 *
 * @ts-expect-error */
const multiLine: number = 'value';

/** @ts-expect-error */
const block: string = 1;

const isOptionEnabled = (key: string): boolean => {
  // @ts-expect-error: if key isn't in globalOptions it'll be undefined which is false
  return !!globalOptions[key];
};

// bad

// @ts-ignore
const str: string = 1;

/**
 * Explaining comment
 *
 * @ts-ignore */
const multiLine: number = 'value';

/** @ts-ignore */
const block: string = 1;

const isOptionEnabled = (key: string): boolean => {
  // @ts-ignore: if key isn't in globalOptions it'll be undefined which is false
  return !!globalOptions[key];
};
```

### [强制] 限制加号运算符的操作值类型

解释：

TypeScript允许『 + 』运算符对任意类型的两个数值进行相加。但是，如果让原始数据类型不同的两个数值相加容易导致异常。

当『 + 』运算符操作的两个值为不同类型，或者操作值的类型不为bigint/number/string时，该规则会进行限制或提示。

```json
{
    "@typescript-eslint/restrict-plus-operands": [
        "error",
        {
            // 是否检查复合运算符，如 `+=`. 默认false
            "checkCompoundAssignments": false,
            // 是否允许使用 `any` 类型. 默认false
            "allowAny": false,
        }
    ]
}
```

示例：

```ts
// good

// 默认配置
var foo = parseInt('5.5', 10) + 10;
var foo = 1n + 1n;

/*eslint @typescript-eslint/restrict-plus-operands: ["error", { "checkCompoundAssignments": true }]*/
let foo: number = 0;
foo += 1;

//  { allowAny: true }
var fn = (a: any, b: string) => a + b;

// bad

// 默认配置
var foo = '5.5' + 5;
var foo = 1n + 1;

/*eslint @typescript-eslint/restrict-plus-operands: ["error", { "checkCompoundAssignments": true }]*/
let bar: string = '';
bar += 0;

//  { allowAny: true }
var fn = (a: any, b: {}) => a + b;
```

### [强制]强制模板字符串中使用string类型

解释：

在JavaScript有些场景下会默隐式调用对象的toString()方法将其转换为字符串，比如当使用+操作符拼接字符，或者在${}模板语法中。Object对象的toString()方法默认返回的`"[object Object]"`字符串，往往不符合预期。这条规则当模板字符串中的数值既不是基础数据类型也没有toString()方法时则生效。

```json
{
    "@typescript-eslint/restrict-template-expressions": [
        "error",
        {
            // 模板字符串中是否允许number类型，默认true
            "allowNumber": true,
            // 模板字符串中是否允许boolean类型，默认false
            "allowBoolean": true,
            // 模板字符串中是否允许any类型，默认false
            "allowAny": false,
            // 模板字符串中是否允许null，默认false
            "allowNullish": false,
            // 模板字符串中是否允许正则表达式，默认false
            "allowRegExp": true
        }
    ]
}
```

示例：

```ts
// good

const stringWithKindProp: string & { _kind?: 'MyString' } = 'foo';
const msg3 = `stringWithKindProp = ${stringWithKindProp}`;

// bad

const arg1 = [1, 2];
const msg1 = `arg1 = ${arg1}`;
```

### [推荐]switch-case所有场景需进行联合类型定义

解释：

TypeScript中的联合类型经常用于描述，switch-case语句中可能包含的case场景的类型的集合。但若联合类型变化了，往往容易忘记修改对应的case。

此规则当switch语句中缺少default语句，或者不指定default且未将case对应类型的所有场景列出时会提示报错。

```json
{
    "@typescript-eslint/switch-exhaustiveness-check": "warn"
}
```

示例：

```ts
type Day =
  | 'Monday'
  | 'Tuesday';

const day = 'Monday' as Day;
let result = 0;

// good

switch (day) {
  case 'Monday':
    result = 1;
    break;
  default:
    result = 42;
}

switch (day) {
  case 'Monday':
    result = 1;
    break;
  case 'Tuesday':
    result = 2;
    break;
}


// bad

switch (day) {
  case 'Monday':
    result = 1;
    break;
}
```

### [强制]类型声明前后空格需要保持一致

解释：

类型声明前后空格一致可提高代码可读性。TypeScript中最常见的类型声明空格样式规范是在冒号后添加一个空格，有其他特殊需求也可通过配置项定制。

```json
{
    "@typescript-eslint/type-annotation-spacing": [
        "error",
        {
            "before": false,
            "after": true,
            "overrides": {
                "arrow": {"before": true, "after": true}
            }
        }
    ]
}
```

示例：

```js
// good
let foo: string = "bar";
let foo: string = () => {};

// bad
let foo:string = "bar";
function foo() :string {}
let foo: string = ()=>{};
```
