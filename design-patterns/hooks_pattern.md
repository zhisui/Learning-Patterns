# HOOKS 模式

使用函数在整个应用程序的多个组件之间复用状态逻辑
React 16.8 推出一种新的特性——Hooks,Hooks 不必通过 ES2015 的类组件去使用 Reat 的状态和生命周期方法

即使 Hooks 并不是必须的设计模式，但是在应用程序设计中起着重大作用，很多传统的设计模式可能会被 Hooks 所取代。

## 类组件

在 Hooks 未被推出前，我们得通过类组件添加状态和使用生命周期到组件中，一个典型 React 类组件如下所示：

```js
class MyComponent extends React.Component {
  /* Adding state and binding custom methods */
  constructor() {
    super()
    this.state = { ... }

    this.customMethodOne = this.customMethodOne.bind(this)
    this.customMethodTwo = this.customMethodTwo.bind(this)
  }

  /* Lifecycle Methods */
  componentDidMount() { ...}
  componentWillUnmount() { ... }

  /* Custom methods */
  customMethodOne() { ... }
  customMethodTwo() { ... }

  render() { return { ... }}
}
```

一个类组件可包含在构造函数中声明的状态，生命周期方法，如 componentDidMount 和 componentWillUnmount 可以根据组件的生命周期执行副作用，自定义方法可以向类中添加额外的逻辑。

虽然在推出 React Hooks 后我们任然可以使用类组件，但是类组件有一些弊端，让我们看下在使用类组件的时候普遍出现的一些问题。

## 理解 ES2015 中的类

React Hook 出现之前类组件是唯一能够处理状态和生命周期方法的组件，因此我们最终不得不将函数组件重构为类组件，以添加额外的功能

在这个例子中，我们有一个 Button 函数返回一个简单的 div

```js
function Button() {
  return <div className="btn">disabled</div>
}
```

这个按钮并不总是显示`disabled` 当用户点击按钮时，我们想把它变成`enable` ,并添加一些样式

为了实现这个，我们需要给组件添加状态确定当前状态是`enabled`还是`disabled`,也就是说我们不得不重构整个函数组件，使它变成一个类组件从而跟踪按钮的状态。

```js
export default class Button extends React.Component {
  constructor() {
    super();
    this.state = { enabled: false };
  }

  render() {
    const { enabled } = this.state;
    const btnText = enabled ? "enabled" : "disabled";

    return (
      <div
        className={`btn enabled-${enabled}`}
        onClick={() => this.setState({ enabled: !enabled })}
      >
        {btnText}
      </div>
    );
  }
}
```
