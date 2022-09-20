# HOOKS 模式
使用函数在整个应用程序的多个组件之间复用状态逻辑
React 16.8推出一种新的特性——Hooks,Hooks不必通过ES2015的类组件去使用Reat的状态和生命周期方法

即使Hooks并不是必须的设计模式，但是在应用程序设计中起着重大作用，很多传统的设计模式可能会被Hooks所取代。

## 类组件

在Hooks未被推出前，我们得通过类组件添加状态和使用生命周期到组件中，一个典型React类组件如下所示：

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
一个类组件可包含在构造函数中声明的状态，生命周期方法，如componentDidMount和componentWillUnmount 可以根据组件的生命周期执行副作用，自定义方法可以向类中添加额外的逻辑。

虽然在推出React Hooks后我们任然可以使用类组件，但是类组件有一些弊端，让我们看下在使用类组件的时候普遍出现的一些问题。

## 理解ES2015中的类
由于类组件是 React Hook 之前唯一能够处理状态和生命周期方法的组件，因此我们常常不得不将函数组件重构为类组件，以添加额外的功能
