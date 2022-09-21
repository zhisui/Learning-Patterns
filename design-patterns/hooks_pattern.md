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
最后，按钮变成我们想要的那样
<iframe src="https://codesandbox.io/embed/hooks-1-2lp9w?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="hooks-1"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

   在这个例子中，这个组件很小，重不重构也没这么大不了的，然而实际上你的组件是由很多行代码，重构代码会非常麻烦。

 除此之外，为了确保在重构组件时不会意外地更改任何行为
 ，你需要理解ES2015中的类，为什么要用`this`去绑定自定义方法，`constructor`是做什么的，`this`关键子指向哪里，很难知道如何正确地重构组件而不意外地更改数据流。

 ## 重构
 在几个组件之间共享代码的常用方法是使用`高阶组件`和`render props`,尽管这两种模式都是有效的，也是一种良好的实践，但是在以后某个时间点添加这些模式需要重新构造应用程序

 除了必须重新构建应用程序(组件越大越棘手)之外，为了在更深层嵌套的组件之间共享代码而使用许多封装组件可能导致“嵌套地狱”的情况。打开您的开发工具通常会看到以下相似的结构：

 ```js
 <WrapperOne>
  <WrapperTwo>
    <WrapperThree>
      <WrapperFour>
        <WrapperFive>
          <Component>
            <h1>Finally in the component!</h1>
          </Component>
        </WrapperFive>
      </WrapperFour>
    </WrapperThree>
  </WrapperTwo>
</WrapperOne>
 ```
嵌套地狱很难理解应用中的数据流向，很难弄清楚为什么会发生意想不到的行为

## 复杂度
随着组件逻辑的增加，组件会越来约庞大，组件中的逻辑可能会变得混乱和非结构化，开发人员很难理解类组件中使用的某些逻辑，代码调试和性能优化更为困难。

生命周期中也会存在很多重复性的代码，让我们看一个`Counter`组件和`Width`组件的例子。

<iframe src="https://codesandbox.io/embed/hooks-2-bzhpw?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="hooks-2"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

  虽然这只是一个小组件，但是组件内部的逻辑已十分复杂。尽管`counter`和`width`中的一些逻辑是确定的，但是随着组件内容的增加，在组件中构造逻辑、在组件中找到相关逻辑会变得越来越困难。

  除了错综复杂的逻辑，我们还在生命周期方法中重复了一些逻辑。在`componentDidMount`和`componentWillUnmoun`,我们为window的`resize`事件定义了行为。

## Hooks
很明显，类组件在 React 中并不总是一个很好的特性，
为了解决 React 开发人员在使用类组件时可能遇到的常见问题，React推出了React Hooks。React Hooks 是可用于管理组件状态和生命周期方法的函数，React Hooks能够：
* 为函数组件添加状态
* 不必使用如`componentDidMount`和`componentWillUnmount`的生命周期方法管理生命周期
* 在整个应用程序的多个组件之间重用状态逻辑

首先，我们先来看下React hooks如何在函数组件中添加状态.

## State Hook
React 提供了一个hook为函数组件添加状态 —— `useState`。
让我们来看下如何用`useState`将一个类组件重构成函数组件。我们有一个`Input`的组件，仅渲染一个输入框，当用户在输入框中输入内容时，state的值会更新

```js
class Input extends React.Component {
  constructor() {
    super();
    this.state = { input: "" };

    this.handleInput = this.handleInput.bind(this);
  }

  handleInput(e) {
    this.setState({ input: e.target.value });
  }

  render() {
    <input onChange={handleInput} value={this.state.input} />;
  }
}
```
为了使用`useState`,我们需要引入React提供的`useState`方法，UseState 方法需要提供一个参数: 这是状态的初始值，在本例中是一个空字符串

我们可以从 useState 方法中解构出两个值：
1.当前转态的值。
2.更新状态的方法。
```js
const [value, setValue] = React.useState(initialValue);
```
第一值相当于类组件中的`this.state.[value]`,第二个值相当于类组件中的`this.setState`方法

因为我们处理的是输入的值，所以让我们调用setInput方法来更新当前的状态值。初始值应该是一个空字符串。

```js
const [input, setInput] = React.useState("");
```
现在我们将类组件重构成有状态的函数组件。
```js
function Input() {
  const [input, setInput] = React.useState("");

  return <input onChange={(e) => setInput(e.target.value)} value={input} />;
}
```
输入字段的值等于当前`input`的值，就像在类组件的例子中一样。当用户在输入字段时，使用`setInput`方法更新输入状态的值。

## Effect Hook
可以看到，我们使用 `useState` 组件来处理函数组件中的状态，但是类组件的另一个好处是可以向组件添加生命周期方法。
使用`useEffect`我们可以“挂钩”到一个组件生命周期，`useEffect`有效结合了`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`
生命周期方法。

```js
componentDidMount() { ... }
useEffect(() => { ... }, [])

componentWillUnmount() { ... }
useEffect(() => { return () => { ... } }, [])

componentDidUpdate() { ... }
useEffect(() => { ... })
```
我们来看下在State Hook一节的`Input`例子，每当用户在输入框中输入值时,我们想把输入值打印在控制台，我们需要用`useEeffet`方法监听`Input`的值，我们可以将`input`添加到`useEffect`的依赖数组中,依赖数组是`useEffect`的第二个参数。

```js
useEffect(() => {
  console.log(`The user typed ${input}`);
}, [input]);
```
我们看下输出结果

<iframe src="https://codesandbox.io/embed/hooks-4-p237n?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="hooks-4"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>
每当用户输入值时，控制台都会打印出来。

## 自定义Hooks

除了React内置的hooks (`useState`, `useEffect`, `useReducer`, `useRef`, `useContext`, `useMemo`, `useImperativeHandle`, `useLayoutEffect`, `useDebugValue`, `useCallback`),们可以很轻松地地创建自定义hook,
你可能已经注意到，所有的hook都是以`use`开头的，hook使用`use`开头以便React检查是否违反了[hook规则](https://reactjs.org/docs/hooks-rules.html)。
假设我们希望记录用户在输入时可能按下的某些键。我们的自定义hook应该能够接收一个参数的作为目标键。

```js
function useKeyPress(targetKey) {}
```
我们希望为参数中传递的键值添加一个 `keydown`和 `keyup` 事件。如果用户按下了这个键，这意味着 keydown 事件被触发，那么钩子中的状态应该切换为 true。否则，当用户停止按下该按钮时，将触发 keyup 事件并将状态切换为 false。

```js
function useKeyPress(targetKey) {
  const [keyPressed, setKeyPressed] = React.useState(false);

  function handleDown({ key }) {
    if (key === targetKey) {
      setKeyPressed(true);
    }
  }

  function handleUp({ key }) {
    if (key === targetKey) {
      setKeyPressed(false);
    }
  }

  React.useEffect(() => {
    window.addEventListener("keydown", handleDown);
    window.addEventListener("keyup", handleUp);

    return () => {
      window.removeEventListener("keydown", handleDown);
      window.removeEventListener("keyup", handleUp);
    };
  }, []);

  return keyPressed;
}
```
很好，我们可以在Input应用中使用自定义hook,每当用户输入`q`,`l`,`w`时在控制台它们打印出来。

