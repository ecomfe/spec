# React规范

## 文件组织

- [强制]同一目录下不得拥有同名的`.js`和`.jsx`文件。

    在使用模块导入时，倾向于不添加后缀，如果存在同名但不同后缀的文件，构建工具将无法决定哪一个是需要引入的模块。

- [强制]组件文件使用一致的`.js`或 `.jsx`后缀。

    所有组件文件的后缀名从`.js`或`.jsx`中任选其一。

    不应在项目中出现部分组件为`.js`文件，部分为`.jsx`的情况。

- [强制]每一个文件以`export default`的形式暴露一个组件。

    允许一个文件中存在多个不同的组件，但仅允许通过`export default`暴露一个组件，其它组件均定义为内部组件。

- [强制]每个存放组件的目录使用一个`index.js`以命名导出的形式暴露所有组件。

    同目录内的组件相互引用使用`import Foo from './Foo';`进行。

    引用其它目录的组件使用`import {Foo} from '../component';`进行。

    建议使用[VSCode的export-index插件](https://marketplace.visualstudio.com/items?itemName=BrunoLM.export-index)等插件自动生成`index.js`的内容。

## 命名规则

- [强制]组件名为PascalCase。

    包括函数组件，名称均为PascalCase。

- [强制]组件名称与文件名称保持相同。

    同时组件名称应当能体现出组件的功能，以便通过观察文件名即确定使用哪一个组件。

- [强制]高阶组件使用camelCase命名。

    高阶组件事实上并非一个组件，而是一个“生成组件类型”的函数，因此遵守JavaScript函数命名的规范，使用camelCase命名。

- [强制]使用`onXxx`形式作为`props`中用于回调的属性名称。

    使用统一的命名规则用以区分`props`中回调和非回调部分的属性，在JSX上可以清晰地看到一个组件向上和向下的逻辑交互。

    对于不用于回调的函数类型的属性，使用动词作为属性名称。

    ```javascript
    // onClick作为回调以on开头，renderText非回调函数则使用动词
    let Label = ({onClick, renderText}) => <span onClick={onClick}>{renderText()}</span>;
    ```

- [建议]使用`withXxx`或`xxxable`形式的词作为高阶组件的名称。

    高阶组件是为组件添加行为和功能的函数，因此使用如上形式的词有助于对其功能进行理解。

- [建议]作为组件方法的事件处理函数以具备业务含义的词作为名称，不使用`onXxx`形式命名。

    ```javascript
    // Good
    class Form {
        @autobind
        collectAndSubmitData() {
            let data = {
                name: this.state.name,
                age: this.state.age
            };
            this.props.onSubmit(data);
        }

        @autobind
        syncName() {
            // ...
        }

        @autobind
        syncAge() {
            // ...
        }

        render() {
            return (
                <div>
                    <label>姓名：<input type="text" onChange={this.syncName} /></label>
                    <label>年龄：<input type="number" onChange={this.syncAge} /></label>
                    <button type="button" onClick={this.collectAndSubmit}>提交</button>
                </div>
            );
        }
    }
    ```

## 组件声明

- [强制]使用ES Class声明组件，禁止使用`React.createClass`。

    [React v15.5.0](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html)已经弃用了`React.createClass`函数。

    ```javascript
    // Bad
    let Message = React.createClass({
        render() {
            return <span>{this.state.message}</span>;
        }
    });

    // Good
    class Message extends PureComponent {
        render() {
            return <span>{this.state.message}</span>;
        }
    }
    ```

- [强制]不使用`state`的组件声明为函数组件。

    函数组件在React中有着特殊的地位，在将来也有可能得到更多的内部优化。

    ```javascript
    // Bad
    class NextNumber {
        render() {
            return <span>{this.props.value + 1}</span>
        }
    }

    // Good
    let NextNumber = ({value}) => <span>{value + 1}</span>;
    ```

- [强制]所有组件均需声明`propTypes`。

    `propsTypes`在提升组件健壮性的同时，也是一种类似组件的文档的存在，有助于代码的阅读和理解。

- [强制]对于所有非`isRequired`的属性，在`defaultProps`中声明对应的值。

    声明初始值有助于对组件初始状态的理解，也可以减少`propTypes`对类型进行校验产生的开销。

    对于初始没有值的属性，应当声明初始值为`null`而非`undefined`。

- [强制]如无必要，使用静态属性语法声明`propsTypes`、`contextTypes`、`defaultProps`和`state`。

    仅当初始`state`需要从`props`计算得到的时候，才将`state`的声明放在构造函数中，其它情况下均使用静态属性声明进行。

- [强制]依照规定顺序编排组件中的方法和属性。

    按照以下顺序编排组件中的方法和属性：

    1. `static displayName`
    2. `static propTypes`
    3. `static contextTypes`
    4. `state defaultProps`
    5. `static state`
    6. 其它静态的属性
    7. 用于事件处理并且以属性的方式（`onClick = e => {...}`）声明的方法
    8. 其它实例属性
    9. `constructor`
    10. `getChildContext`
    11. `componentWillMount`
    12. `componentDidMount`
    13. `shouldComponentUpdate`
    14. `componentWillUpdate`
    15. `componentDidUpdate`
    16. `componentWillUnmount`
    17. 事件处理方法
    18. 其它方法
    19. `render`

    其中`shouldComponentUpdate`和`render`是一个组件最容易被阅读的函数，因此放在最下方有助于快速定位。

- [建议]无需显式引入React对象。

    使用JSX隐式地依赖当前环境下有`React`这一对象，但在源码上并没有显式使用，这种情况下添加`import React from 'react';`会造成一个没有使用的变量存在。

    使用[babel-plugin-react-require](https://www.npmjs.com/package/babel-plugin-react-require)插件可以很好地解决这一问题，因此无需显式地编写`import React from 'react';`这一语句。

- [建议]使用箭头函数声明函数组件。

    箭头函数具备更简洁的语法（无需`function`关键字），且可以在仅有一个语句时省去`return`造成的额外缩进。

- [建议]高阶组件返回新的组件类型时，添加`displayName`属性。

    同时在`displayName`上声明高阶组件的存在。

    ```javascript
    // Good
    let asPureComponent = Component => {
        let componentName = Component.displayName || Component.name || 'UnknownComponent';
        return class extends PureComponent {
            static displayName = `asPure(${componentName})`

            render() {
                return <Component {...this.props} />;
            }
        };
    };
    ```

## 组件实现

- [强制]除顶层或路由级组件以外，所有组件均在概念上实现为纯组件（Pure Component）。

    本条规则并非要求组件继承自`PureComponent`，“概念上的纯组件”的意思为一个组件在`props`和`state`没有变化（shallowEqual）的情况下，渲染的结果应保持一致，即`shouldComponentUpdate`应当返回`false`。

    一个典型的非纯组件是使用了随机数或日期等函数：

    ```javascript
    let RandomNumber = () => <span>{Math.random()}</span>;
    let Clock = () => <span>{Date.time()}</span>;
    ```

    非纯组件具备向上的“传染性”，即一个包含非纯组件的组件也必须是非纯组件，依次沿组件树结构向上。由于非纯组件无法通过`shouldComponentUpdate`优化渲染性能且具备传染性，因此要避免在非顶层或路由组件中使用。

    如果需要在组件树的某个节点使用随机数、日期等非纯的数据，应当由顶层组件生成这个值并通过`props`传递下来。对于使用Redux等应用状态管理的系统，可以在应用状态中存放相关值（如Redux使用Action Creator生成这些值并通过Action和reducer更新到store中）。

- [强制]禁止为继承自`PureComponent`的组件编写`shouldComponentUpdate`实现。

    参考[React的相关Issue](https://github.com/facebook/react/issues/9239)，在React的实现中，`PureComponent`并不直接实现`shouldComponentUpdate`，而是添加一个`isReactPureComponent`的标记，由`CompositeComponent`通过识别这个标记实现相关的逻辑。因此在`PureComponent`上自定义`shouldComponentUpdate`并无法享受`super.shouldComponentUpdate`的逻辑复用，也会使得这个继承关系失去意义。

- [强制]为非继承自`PureComponent`的纯组件实现`shouldComponentUpdate`方法。

    `shouldComponentUpdate`方法在React的性能中扮演着至关重要的角色，纯组件必定能通过`props`和`state`的变化来决定是否进行渲染，因此如果组件为纯组件且不继承`shouldComponentUpdate`，则应当有自己的`shouldComponentUpdate`实现来减少不必要的渲染。

- [建议]为函数组件添加`PureComponent`能力。

    函数组件并非一定是纯组件，因此其`shouldComponentUpdate`的实现为`return true;`，这可能导致额外的无意义渲染，因此推荐使用高阶组件为其添加`shouldComponentUpdate`的相关逻辑。

    推荐使用[react-pure-stateless-component](https://www.npmjs.com/package/react-pure-stateless-component)库实现这一功能。

- [建议]使用`@autobind`进行事件处理方法与`this`的绑定。

    由于`PureComponent`使用[`shallowEqual`](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/shallowEqual.js)进行是否渲染的判断，如果在JSX中使用`bind`或箭头函数绑定`this`会造成子组件每次获取的函数都是一个新的引用，这破坏了`shouldComponentUpdate`的逻辑，引入了无意义的重复渲染，因此需要在`render`调用之前就将事件处理方法与`this`绑定，在每次`render`调用中获取同样的引用。

    当前比较流行的事前绑定`this`的方法有2种，其一使用类属性的语法：

    ```javascript
    class Foo {
        onClick = e => {
            // ...
        }
    };
    ```

    其二使用`@autobind`的装饰器：

    ```javascript
    class Foo {
        @autobind
        onClick(e) {
            // ...
        }
    }
    ```

    使用类属性语法虽然可以避免引入一个`autobind`的实现，但存在一定的缺陷：

    1. 对于新手不容易理解函数内的`this`的定义。
    2. 无法在函数上使用其它的装饰器（如`memoize`、`deprecated`或检验相关的逻辑等）。

    因此，推荐使用`@autobind`装饰器实现`this`的事先绑定，推荐使用[core-decorators](https://www.npmjs.com/package/core-decorators)库提供的相关装饰器实现。

## JSX

- [强制]没有子节点的非DOM组件使用自闭合语法。

    对于DOM节点，按照HTML编码规范相关规则进行闭合，**其中void element使用自闭合语法**。

    ```javascript
    // Bad
    <Foo></Foo>

    // Good
    <Foo />
    ```

- [强制]保持起始和结束标签在同一层缩进。

    对于标签前面有其它语句（如`return`的情况，使用括号进行换行和缩进）。

    ```javascript
    // Bad
    class Message {
        render() {
            return <div>
                <span>Hello World</span>
            </div>;
        }
    }

    // Good
    class Message {
        render() {
            return (
                <div>
                    <span>Hello World</span>
                </div>
            );
        }
    }
    ```

    对于直接`return`的函数组件，可以直接使用括号而省去大括号和`return`关键字：

    ```javascript
    let Message = () => (
        <div>
            <span>Hello World</span>
        </div>
    );
    ```

- [强制]对于多属性需要换行，从第一个属性开始，每个属性一行。

    ```javascript
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

- [强制]以字符串字面量作为值的属性使用双引号（`"`），在其它类型表达式中的字符串使用单引号（`'`）。

    ```javascript
    // Bad
    <Foo bar='bar' />
    <Foo style={{width: "20px"}} />

    // Good
    <Foo bar="bar" />
    <Foo style={{width: '20px'}} />
    ```

- [强制]自闭合标签的`/>`前添加一个空格。

    ```javascript
    // Bad
    <Foo bar="bar"/>
    <Foo bar="bar"  />

    // Good
    <Foo bar="bar" />
    ```

- [强制]对于值为`true`的属性，省去值部分。

    ```javascript
    // Bad
    <Foo visible={true} />

    // Good
    <Foo visible />
    ```

- [强制]对于需要使用`key`的场合，提供一个唯一标识作为`key`属性的值，禁止使用可能会变化的属性（如索引）。

    `key`属性是React在进行列表更新时的重要属性，如该属性会发生变化，渲染的性能和**正确性**都无法得到保证。

    ```javascript
    // Bad
    {list.map((item, index) => <Foo key={index} {...item} />)}

    // Good
    {list.map(item => <Foo key={item.id} {...item} />)}
    ```

- [建议]避免在JSX的属性值中直接使用对象和函数表达式。

    `PureComponent`使用[`shallowEqual`](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/shallowEqual.js)对`props`和`state`进行比较来决定是否需要渲染，而在JSX的属性值中使用对象、函数表达式会造成每一次的对象引用不同，从而`shallowEqual`会返回`false`，导致不必要的渲染。


    ```javascript
    // Bad
    class WarnButton {
        alertMessage(message) {
            alert(message);
        }

        render() {
            return <button type="button" onClick={() => this.alertMessage(this.props.message)}>提示</button>
        }
    }

    // Good
    class WarnButton {
        @autobind
        alertMessage() {
            alert(this.props.message);
        }

        render() {
            return <button type="button" onClick={this.alertMessage}>提示</button>
        }
    }
    ```

- [建议]将JSX的层级控制在3层以内。

    JSX提供了基于组件的便携的复用形式，因此可以通过将结构中的一部分封装为一个函数组件来很好地拆分大型复杂的结构。层次过深的结构会带来过多缩进、可读性下降等缺点。如同控制函数内代码行数和分支层级一样，对JSX的层级进行控制可以有效提升代码的可维护性。

    ```javascript
    // Bad
    let List = ({items}) => (
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

    // Good
    let Header = ({title, subtitle}) => (
        <header>
            <h3>{title}</h3>
            <span>{subtitle}</span>
        </header>
    );

    let Content = ({content}) => <section>{content}</section>;

    let Footer = ({author, postTime}) => (
        <footer>
            <span>{author}</span>@<time>{postTime}</time>
        </footer>
    );

    let Item = item => (
        <div>
            <Header {...item} />
            <Content {...item} />
            <Footer {...item} />
        </div>
    );

    let List = ({items}) => (
        <ul>
            {items.map(Item)}
        </ul>
    );
    ```
