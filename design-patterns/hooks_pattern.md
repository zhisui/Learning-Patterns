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

<iframe src="https://codesandbox.io/embed/hooks-5-xplez?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="hooks-5"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

使用键输入逻辑并不是只能在`Input`组件中，在多个组件间也可以使用，不需要重复写相同的逻辑。
Hooks两外一个好处是，在社区中可以构建和共享组件，我们只是自己编写了 useKeyPress 钩子，但实际上根本没有这个必要！如果别人已经写好了这个钩子，我们只需要找到[相应的库](https://github.com/streamich/react-use/blob/master/docs/useKeyPress.md)，安装它就可以使用了。
这里有一些由社区构建钩子的网站，可以在你的应用程序中使用起来。
* [React Use](https://github.com/streamich/react-use)
* [useHook](https://github.com/uidotdev/usehooks)
* [Collection of React Hooks](https://github.com/nikgraf/react-hooks)

重写一遍之前章节的计数器例子。我们用React Hook而非类组件来重写它。
<iframe src="https://codesandbox.io/embed/hooks-6-2w0ll?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="hooks-6"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>
我们将`APP`里面的逻辑分成以下几个部分：
`useCounter`：返回当前`count`值, `increment`和`decrement`方法的自定义hook。
`useWindowWidth`:返回当前窗口宽度的自定义hook。
`App`：返回 `Counter` 和 `Width` 状态函数组件。

通过使用 React Hooks 而不是类组件，我们能够将逻辑分解为更小的、可重用的部分，以分离逻辑。

使用 React Hooks 可以更清楚地将组件的逻辑分成几个较小的部分。更容易逻辑复用，如果我们想要使组件有状态，我们不再需要将函数组件重写为类组件，不再需要对 ES2015类有很好的了解，拥有可重用的状态逻辑可以提高组件的可测试性、灵活性和可读性。

## 其他Hooks指南
### Adding Hooks
与其他组件一样，当您希望将 Hook 添加到所编写的代码中时，可以使用一些特殊的函数组件。以下简要概述常见的 Hook 函数:

**1.useState**

`useState` Hook使开发人员无需将其转换为类组件便可更改函数组件内部的状态，这个 Hook 的一个优点是它很简单，不像其他 React Hook 那样复杂。

**2.useEffect**

`useEffect` Hook主要用于函数组件在主要生命周期中执行代码，函数组件的主体不允许变更、订阅、计时器、日志记录和其他副作用。如果这些副作用被执行，可能会导致一些意想不到的bug和和页面卡顿。`useEffect` hook可以防止所有这些“副作用”，提高页面流畅度。它包含了
`componentDidMount` , `componentDidUpdate` 和 `componentWillUnmount`这几个生命周期。

**3.useContext**
useContext Hook接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值。UseContext Hook 还与 React Context API 一起工作，以便在整个应用程序中共享数据，而无需通过不同层级中传递props。

需要注意的是useContext 的参数必须是 context 对象本身，调用了 useContext 的组件总会在 context 值变化时重新渲染

**useReducer**
useState的替代方案，在某些场景下，useReducer 会比 useState 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。
它接收一个形如 (state, action) => newState 的 reducer和初始值，并返回当前的 state 以及与其配套的 dispatch 方法。使用 useReducer 还能给那些会触发深更新的组件做性能优化

**使用react Hooks的优缺点**
使用hooks的好处：
Hooks可根据功能和关注点组织代码，而不是通过生命周期，使得代码更加清晰简洁。下面是使用 React 搜索产品数据表的简单有状态组件与使用 useState 关键字后在 Hooks 中的外观的比较。

**状态组件**
```js
class TweetSearchResults extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        filterText: '',
        inThisLocation: false
      };

      this.handleFilterTextChange = this.handleFilterTextChange.bind(this);
      this.handleInThisLocationChange = this.handleInThisLocationChange.bind(this);
    }

    handleFilterTextChange(filterText) {
      this.setState({
        filterText: filterText
      });
    }

    handleInThisLocationChange(inThisLocation) {
      this.setState({
        inThisLocation: inThisLocation
      })
    }

    render() {
      return (
        <div>
          <SearchBar
            filterText={this.state.filterText}
            inThisLocation={this.state.inThisLocation}
            onFilterTextChange={this.handleFilterTextChange}
            onInThisLocationChange={this.handleInThisLocationChange}
          />
          <TweetList
            tweets={this.props.tweets}
            filterText={this.state.filterText}
            inThisLocation={this.state.inThisLocation}
          />
        </div>
      );
    }
  }
```
**相同的组件在Hooks中的体现**
```js
const TweetSearchResults = ({tweets}) => {
  const [filterText, setFilterText] = useState('');
  const [inThisLocation, setInThisLocation] = useState(false);
  return (
    <div>
      <SearchBar
        filterText={filterText}
        inThisLocation={inThisLocation}
        setFilterText={setFilterText}
        setInThisLocation={setInThisLocation}
      />
      <TweetList
        tweets={tweets}
        filterText={filterText}
        inThisLocation={inThisLocation}
      />
    </div>
  );
}
```
**简化组件呢的复杂性**
JavaScript 类很难管理，在热重载时很难使用，而且可能不会缩小。React Hooks 解决了这些问题，并确保函数式编程变得简单。有了 Hooks 的实现，我们就不需要类组件了。

在 JavaScript 中重用状态逻辑类可以促进多级继承，从而大大增加整体复杂性和出错的可能性。但是，Hooks 允许您在不编写类的情况下使用 state 和其他 React 特性。使用 React可以复用状态逻辑，而无需一遍又一遍地重写代码。这减少了出错的机会，并允许使用普通函数进行组合。

**共享非可视化逻辑**

在 Hooks 的实现之前，React 没有办法提取和共享非可视化逻辑。这最终导致了更多的复杂性，比如 HOC 模式和render props，只是为了解决一个常见的问题。但是，Hook 的引入解决了这个问题，因为它允许将有状态逻辑提取到一个简单的 JavaScript 函数中。

当然，Hook 也有一些潜在的不利之处，值得我们牢记在心:
* 必须遵守它的规则，如果没有linter插件，很难知道哪条规则已经被打破
* 需要相当长的时间练习才能更好地使用（如 useEffect）
* 避免错误使用（如useCallback, useMemo）

## React Hooks vs Classes
React引入Hooks时产生了一个新问题: 我们如何知道何时将函数组件与 Hooks 和 class 组件一起使用？在 Hook 的帮助下，甚至在函数组件中也可以获得状态和部分生命周期 Hook。Hooks 还允许您在不编写类的情况下使用本地状态和其他 React 特性。
下面是 Hooks 和 Class 之间的一些区别，可以帮助您做出决定

| React Hooks   | Classes |
| ----------- | ----------- |
| 有助于避免多个层次结构并使代码更清晰 | 通常，当您使用 HOC 或 renderProps 时，必须使用多个层次结构重新构建应用程序|
|提供React组件的一致性   | 由于需要理解绑定和函数调用的上下文，类使人和机器都感到困惑|
