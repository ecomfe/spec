## 1 文件组织

### 1.1 命名

#### [强制] 同一目录下不得拥有同名的`.js` 、 `.jsx` 、 `.ts` 、 `.tsx` 文件。

#### [强制] 同一项目下组件文件名保持一致的规则。

以组件名为 `FooBar` 为例，常见的方式有：

- `foo-bar.tsx`
- `FooBar.tsx`
- `fooBar.tsx`

解释：

在使用模块导入时，往往不添加后缀，如果存在同名但不同后缀的文件，构建工具将无法决定哪一个是需要引入的模块。

### 1.2 模块化

#### [建议] 实现单个组件的文件以`export default`的形式暴露一个组件。

解释：

允许一个文件中存在多个不同的组件，但仅允许通过`export default`暴露一个组件，其它组件均定义为内部组件。

## 2 命名规则

### 2.1 组件

#### [强制] 组件名为 `PascalCase`。

包括函数组件，名称均为 `PascalCase`。

示例：

```jsx
function FooBar() {
    return <div />;
}

class FooBar {
    render() {
        return <div />;
    }
}
```

#### [强制] 组件名称与文件名称保持相同含义。

同时组件名称应当能体现出组件的功能，以便通过观察文件名即确定使用哪一个组件。

### 2.2 属性

#### [强制] 使用`onXxx`形式作为`props`中用于回调的属性名称。

解释：

使用统一的命名规则用以区分`props`中回调和非回调部分的属性，在JSX上可以清晰地看到一个组件向上和向下的逻辑交互。

对于不用于回调的函数类型的属性，使用动词作为属性名称。

示例：

```js
// onClick作为回调以on开头，renderText非回调函数则使用动词
function Label({onClick, renderText}) {
    return <span onClick={onClick}>{renderText()}</span>;
}
```

#### [建议] 作为组件方法的事件处理函数以具备业务含义的词作为名称，不使用`onXxx`形式命名。

示例：

```js
// good
function Form({onSubmit}) {
    let [name, setName] = useState('');
    let [age, setAge] = useState('');
    let collectAndSubmitData = () => {
        let data = {name, age};
        onSubmit(data);
    }
    let syncName = e => setName(e.target.value);
    let syncAge = e => setAge(e.target.value);

    return (
        <div>
            <label>姓名：<input type="text" onChange={syncName} /></label>
            <label>年龄：<input type="number" onChange={syncAge} /></label>
            <button type="button" onClick={collectAndSubmit}>提交</button>
        </div>
    );
}
```

### 2.3 高阶组件

#### [强制] 高阶组件使用camelCase命名。

解释：

高阶组件事实上并非一个组件，而是一个“生成组件类型”的函数，因此遵守JavaScript函数命名的规范，使用camelCase命名。

#### [建议] 使用`withXxx`或`xxxable`形式的词作为高阶组件的名称。

解释：

高阶组件是为组件添加行为和功能的函数，因此使用如上形式的词有助于对其功能进行理解。

## 3 组件声明

### 3.1 组件

#### [强制] 当需要类组件时，使用 ES Class 声明组件，禁止使用 `React.createClass` 。

解释：

[React v15.5.0](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html)已经弃用了`React.createClass`函数。

示例：

```js
// good
class Message extends PureComponent {
    render() {
        return <span>{this.state.message}</span>;
    }
}

// bad
let Message = React.createClass({
    render() {
        return <span>{this.state.message}</span>;
    }
});
```

#### [强制] 尽量使用无状态函数组件。

解释：

函数组件在React中有着特殊的地位，在将来也有可能得到更多的内部优化。

示例：

```js
// good
function NextNumber({value}) {
    return <span>{value + 1}</span>;
}

// bad
class NextNumber {
    render() {
        return <span>{this.props.value + 1}</span>
    }
}
```

#### [建议] 无需显式引入 `React` 对象。

解释：

新版 `React` 使用 `react/jsx-runtime` 模块进行 JSX 元素处理，且由工具（如 Babel 等）负责引入，不需要显式引入。

对于其它功能，统一使用 Named Import 引入。

示例：

```js
import {useMemo, useCallback, memo} from 'react';
```

#### [建议] 使用 `function` 关键字声明函数组件。

解释：

`function` 声明函数组件在类型推导上更加灵活，支持挂载子组件。且在与 `export default` 一起使用时，相比箭头函数依然可以保持函数名称。

### 3.2 属性

#### [强制] 对于有默认值的可选属性，使用参数默认值声明对应的值，不使用 `defaultProps` 。

解释：

声明初始值有助于对组件的初始状态的理解，且使用参数的默认值声明有更好的性能，在 TypeScript 类型推导上也更准确。

在值上，需要区分 `undefined` 与 `null` ，对于可选的属性，其默认值可能是 `null` ，此时依然需要通过参数默认值声明。

示例：

```jsx
// good
function Foo({value = 0}) {
    return <span>{value}</span>;
}

// bad
function Foo({value}) {
    return <span>{value}</span>;
}

Foo.defaultProps = {
    value: 1,
};
```

### 3.3 生命周期

#### [强制] 禁止使用 `componentWillMount` 。

解释：

使用 `constructor` 代替。

#### [强制] 禁止使用 `componentWillReceiveProps` 。

解释：

对于类组件，使用 `getDerivedStateFromProps` 代替。

#### [强制] 依照规定顺序编排组件中的方法和属性。

按照以下顺序编排组件中的方法和属性：

1. `static displayName`
2. `static propTypes`
3. `state defaultProps`
4. 其它静态的属性
5. `state`
6. 其它实例属性
7. 用于事件处理并且以属性的方式（`onClick = e => {...}`）声明的方法
8. `constructor`
9. `componentDidMount`
10. `shouldComponentUpdate`
11. `static getDerivedStateFromProps`
12. `componentDidUpdate`
13. `componentWillUnmount`
14. 事件处理方法
15. 其它方法
16. `render`

### 3.4 高阶组件

#### [建议] 高阶组件返回新的组件类型时，添加 `displayName` 属性。

同时在 `displayName` 上声明高阶组件的存在。

```js
// good
let asPureComponent = Component => {
    let componentName = Component.displayName || Component.name || 'UnknownComponent';
    return class extends PureComponent {
        static displayName = `asPure(${componentName})`

        render() {
            return <Component {..this.props} />;
        }
    };
};
```

## 4 组件实现

### 4.1 更新机制

#### [强制] 禁止为继承自 `PureComponent` 的组件编写 `shouldComponentUpdate` 实现。

参考[React的相关Issue](https://github.com/facebook/react/issues/9239)，在 React 的实现中， `PureComponent` 并不直接实现 `shouldComponentUpdate` ，而是添加一个 `isReactPureComponent` 的标记，由 `CompositeComponent` 通过识别这个标记实现相关的逻辑。因此在 `PureComponent` 上自定义 `shouldComponentUpdate` 并无法享受 `super.shouldComponentUpdate` 的逻辑复用，也会使得这个继承关系失去意义。

#### 【强制】不在组件中声明另一个组件。

解释：

在 React 的更新判断中，当一个元素（`Element`）的类型或 `key` 变化时，元素会被销毁并重建，所有状态丢失，且引起更大的 DOM 变化，进而影响性能。

在一个组件的渲染逻辑中创建另一个组件的类型，相当于每一次渲染都会有一个新的组件类型，即每次渲染都会引起销毁、重建逻辑，产生不必要的性能损耗和不预期的状态变化。

因此，对于所谓的“子组件”，我们应当把它提到源代码的顶层，多个组件之间用 `props` 通信。

示例：

```jsx
// good
function Bar({name}) {
    // ...
}

function Foo({name}) {
    return <Bar name={name} />;
}

// bad
function Foo({name}) {
    function Bar() {
        // ...
    }

    return <Bar />;
}
```

#### 【强制】为useMemo、useCallback、useEffect等有依赖的hook提供全部的依赖项。

解释：

React 的 `useCallback`、`useMemo` 和 `useEffect` hook 有第2个参数表示依赖数组，当依赖数组中元素变化时，hook 对应的函数会被重新执行。这是符合 React 的响应式概念的，应当遵守。

你可以使用 [eslint-plugin-react-hooks](https://github.com/facebook/react/tree/main/packages/eslint-plugin-react-hooks) 中的 `react-hooks/exhaustive-deps` 规则进行检查，也可以自动修复依赖数组的内容。

如果你需要在特殊情况下需要省略部分依赖，你可以使用 `useRef` 保存该依赖的值。

#### 【强制】不得在 `setState` 的参数值中使用 `state` 对象。

解释：

由于 `setState` 的异步特性，实际在更新状态发生时，可能 `state` 对象已经是过期的，因此需要使用回调的形式调用 `setState` 。

示例：

```js
// good
let [counter, setCounter] = useState(0);
let increment = () => setCounter(c => c + 1);

// bad
let [counter, setCounter] = useState(0);
let increment = () => setCounter(counter + 1);
```

#### 【建议】不要在 `didMount` 和 `didUpdate` 中使用 `setState` 。

解释：

对于类组件， `didMount` 和 `didUpdate` 是发生在状态变化后的，此时再触发状态变更会引起多余的渲染，甚至可能引起死循环的无限渲染。

通常类似情况，就当将多次的 `setState` 合并成一个，或考虑状态的粒度是否正确。

在极少数情况下，如需要 DOM 元素来获取状态值，可以忽视本规则。

#### 【强制】不直接修改 `state` 和 `props`。

解释：

React 的组件是由不可变的状态和属性驱动的，因此对 `state` 和 `props` 的直接修改并不会触发渲染和更新视图，会导致状态与界面的不一致。

在任何需要修改状态的场合，就当使用 `setState` 来更新，对于 `props` 则就当保持不可变的前提下，通过回调由父元素修改后传递新值到子组件。

### 4.2. 渲染逻辑

#### 【强制】不要在条件、循环语句中调用hook。

解释：

Hooks 的本质是一个用数组保存的中间状态，它严格依赖各个 hook 调用的顺序和类型，在多次渲染中不能有任何变化。

在条件语句或循环语句中使用 hook ，会让组件保存的 hook 数组的数量和顺序产生变化，进而引起渲染的问题。

#### 【强制】不使用字符串类型的 `ref` 属性。

解释：

在较新版本的 React 中，字符串类型的 `ref` 属性已经被废弃。

对于类组件，使用 `createRef` 函数创建 `ref` 对象。对于函数组件，使用 `useRef` 创建。

### 【建议】不使用 `dangerslySetInnerHTML` 。

解释：

直接使用 `dangerslySetInnerHTML` 相当于使用 `innerHTML` ，存在XSS风险。如必须使用的场景，谨慎评估后使用注释说明原因。

#### 【建议】不使用 `findDOMNode` 。

解释：

在较新版本的 React 中 `findDOMNode` 已经被废弃，就当使用 `ref` 来实现相应功能。

## 5 JSX

### 5.1 标签

#### [强制] 没有子节点的组件使用自闭合语法。

解释：

JSX与HTML不同，所有元素均可以自闭合。

示例：

```js
// good
<Foo />
<div />

// bad
<Foo></Foo>
<div></div>
```

#### [强制] 保持起始和结束标签在同一层缩进。

解释：

对于标签前面有其它语句（如`return`的情况，使用括号进行换行和缩进）。

对于直接`return`的函数组件，可以直接使用括号而省去大括号和`return`关键字：

```js
function Message() {
    return (
        <div>
            <span>Hello World</span>
        </div>
    );
}
```

示例：

```js
// good
class Message {
    render() {
        return (
            <div>
                <span>Hello World</span>
            </div>;
        );
    }
}

// bad
class Message {
    render() {
        return <div>
            <span>Hello World</span>
        </div>;
    }
}
```

#### 【强制】无子元素的DOM组件必须使用自闭合的形式书写。

示例：

```jsx
// good
function Foo() {
    return <div />;
}

// bad
function Foo() {
    return <div></div>;
}

#### [强制] 自闭合标签的`/>`前添加一个空格。

示例：

```js
// bad
<Foo bar="bar"/>
<Foo bar="bar"  />

// good
<Foo bar="bar" />
```

### 5.2 属性

#### [强制] 对于多属性需要换行，从第一个属性开始，每个属性一行。

示例：

```js
// 没有子节点
<SomeComponent
    longProp={longProp}
    anotherLongProp={anotherLongProp}
/>

// 有子节点
<SomeComponent
    longProp={longProp}
    anotherLongProp={anotherLongProp}
>
    <SomeChild />
    <SomeChild />
</SomeComponent>
```

#### [强制] 以字符串字面量作为值的属性使用双引号（`"`），在其它类型表达式中的字符串使用单引号（`'`）。

示例：

```js
// good
<Foo bar="bar" />
<Foo style={{width: '20px'}} />

// bad
<Foo bar='bar' />
<Foo style={{width: "20px"}} />
```

#### [强制] 对于值为`true`的属性，省去值部分。

示例：

```js
// good
<Foo visible />

// bad
<Foo visible={true} />
```

#### [强制] 值为`true`的属性放在其它属性前面。

解释：

将`true`类的值放在前面使得组件的声明更接近自然语言，提高可读性。

示例：

```js
// good
<Foo visible requireValidation data={data} />

// bad
<Foo data={data} visible requireValidation />
```

#### [强制] 对于需要使用`key`的场合，提供一个唯一标识作为`key`属性的值，禁止使用可能会变化的属性（如索引）。

解释：

`key`属性是React在进行列表更新时的重要属性，如该属性会发生变化，渲染的性能和**正确性**都无法得到保证。

示例：

```js
// good
{list.map(item => <Foo key={item.id} {...item} />)}

// bad
{list.map((item, index) => <Foo key={index} {...item} />)}
```

#### [建议] 对于 `PureComponent` ，避免在JSX的属性值中直接使用对象和函数表达式。

解释：

`PureComponent` 使用对 `props` 和 `state` 进行比较来决定是否需要渲染，而在JSX的属性值中使用对象、函数表达式会造成每一次的对象引用不同，从而 `shallowEqual` 会返回 `false` ，导致不必要的渲染。

示例：

```js
// good
class WarnButton extends PureComponent {
    alertMessage = () => {
        alert(this.props.message);
    };

    render() {
        return <button type="button" onClick={this.alertMessage}>提示</button>
    }
}

// bad
class WarnButton extends PureComponent {
    alertMessage(message) {
        alert(message);
    }

    render() {
        return <button type="button" onClick={() => this.alertMessage(this.props.message)}>提示</button>
    }
}
```

#### 5.2.1 层级

#### [建议] 将JSX的层级控制在5层以内。

解释：

JSX提供了基于组件的便携的复用形式，因此可以通过将结构中的一部分封装为一个函数组件来很好地拆分大型复杂的结构。层次过深的结构会带来过多缩进、可读性下降等缺点。如同控制函数内代码行数和分支层级一样，对JSX的层级进行控制可以有效提升代码的可维护性。

示例：

```js
// good
function Header({title, subtitle}) {
    return (
        <header>
            <h3>{title}</h3>
            <span>{subtitle}</span>
        </header>
    );
}

function Content({content}) {
    return <section>{content}</section>;
}

function Footer({author, postTime}) {
    return (
        <footer>
            <span>{author}</span>@<time>{postTime}</time>
        </footer>
    );
}

function Itemitem() {
    return (
        <div>
            <Header {...item} />
            <Content {...item} />
            <Footer {...item} />
        </div>
    )
}

function List({items}) {
    return (
        <ul>
            {items.map(Item)}
        </ul>
    );
}

// bad
function List({items}) {
    return (
        <ul>
            {
                items.map(item => (
                    <li>
                        <header>
                            <h3>{item.title}</h3>
                            <span>{item.subtitle}</span>
                        </header>
                        <section>{item.content}</section>
                        <footer>
                            <span>{item.author}</span>@<time>{item.postTime}</time>
                        </footer>
                    </li>
                ))
            }
        </ul>
    );
}
```

## 补充部分

以下为本次待新增的，暂不写具体解释，有异议的直接评论。

### 【强制】跨多行的自闭合组件元素，闭合符独立一行且与起始符对齐。

示例：

```jsx
//good
<input
    type="date"
    value={value}
    onChange={syncValue}
/>

//bad
<input
    type="date"
    value={value}
    onChange={syncValue} />

<input
    type="date"
    value={value}
    onChange={syncValue}
    />
```

### 【强制】字符串类型的属性不能使用 `{}` 包裹。

``jsx
// good
<img src="./logo.png" />

// bad
<img src={'./logo.png'} />
```

### 【强制】属性的=两侧不能有空格。

示例：

```jsx
// good
<input type="date" value={value} />

// bad
<input type = "date" value = {value} />
```

### 【强制】属性中的大括号两侧不能有空格。

示例：

```jsx
// good
<Counter value={value + 1} />

// bad
<Counter value={ value + 1 } />
```

### 【强制】如果属性值跨多行，属性的起始 `{ 后必须换行，结束 `}` 前必须换行。

示例：

```jsx
// good
<IconLabel
    title={
        <>
            这是一个有
            <span>{linesCount}行</span>
            组成的内容
        </>
    }
/>

// bad
<IconLabel
    title={<>
        这是一个有
        <span>{linesCount}行</span>
        组成的内容
    </>}
/>
```

### 【强制】不得包含无用的Fragment元素。

解释：

当一个 `Fragment` 不用于指定 `key` 属性，且不用于将多个元素聚为一组时，它是可以被删除的。

示例：

```jsx
// good
<>
  <Foo />
  <Bar />
</>
<> {foo}</>
<SomeComponent>
  <>
    <div />
    <div />
  </>
</SomeComponent>
<Fragment key={item.id}>{item.value}</Fragment>

// bad
<>{foo}</>
<><Foo /></>
<p><>foo</></p>
<Fragment>foo</Fragment>
<section>
  <>
    <div />
    <div />
  </>
</section>
```

### 【建议】回调类属性放在组件属性声明的最后。

解释：

从组件的声明上，通过合适地排列属性，可以清晰地将它分为控制展示的“状态”和控制交互的“回调”，两者区分开来能够使表达更清晰。

同时，配合“回调属性均以 `onXxx` 形式命名”的规则，当每个属性一行时，对齐也会更一致。

```jsx
// good
<Form
    name={name}
    age={age}
    onNameChange={syncName}
    onAgeChange={synceAge}
/>

// bad
<Form
    name={name}
    onNameChange={syncName}
    age={age}
    onAgeChange={synceAge}
/>
```
