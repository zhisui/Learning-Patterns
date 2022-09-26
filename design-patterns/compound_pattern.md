# 复合模式

在应用程序中，时常有组件相互依赖，他们共享状态和逻辑,可以看到这样的组件，如：`select`、下拉列表组件和菜。复合模式可以创建共同执行任务的组件。

## Contex API

我们来看一个例子，我们有一个老鼠图片列表，除了显示老鼠列表外，我们想要增加一个可以编辑或者删除照片的按钮。当用户切换组件的时候我们可以执行展示列表的`FlyOut`组件

<iframe src="https://codesandbox.io/embed/provider-pattern-2-ck29r?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="provider-pattern-2"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

   在`FlyOut`组件中，有三件事情：
   * 包含切换按钮和列表的`FlyOut`包装器
   * 切换列表的`Toggole`按钮
   * 包含菜单项的`List`列表

使用React中的[Context API](https://reactjs.org/docs/context.html) 来实现组合模式是极好的。

首先，让我们创建 `FlyOut`组件。这个组件保持状态，并返回一个 FlyOutProvider，其中包含它接收到的所有子级的切换值

```js
const FlyOutContext = createContext();

function FlyOut(props) {
  const [open, toggle] = useState(false);

  const providerValue = { open, toggle };

  return (
    <FlyOutContext.Provider value={providerValue}>
      {props.children}
    </FlyOutContext.Provider>
  );
}
```
现在我们有了一个可传递`open`值和`toggle`值的`FlyOut`状态组件.

让我们来创建一个`Toggle 组件,这个组件只渲染了用户点击切换菜单的组件。

```js
function Toggle() {
  const { open, toggle } = useContext(FlyOutContext);

  return (
    <div onClick={() => toggle(!open)}>
      <Icon />
    </div>
  );
}
```
为了使`Toggle`连接到`FlyOutContext`的provider,需要将`Toggle`作为FlyOut的子组件进行渲染。然而，我们同样也可以将Toggle作为FlyOut的一个属性。

```js
const FlyOutContext = createContext();

function FlyOut(props) {
  const [open, toggle] = useState(false);

  return (
    <FlyOutContext.Provider value={{ open, toggle }}>
      {props.children}
    </FlyOutContext.Provider>
  );
}

function Toggle() {
  const { open, toggle } = useContext(FlyOutContext);

  return (
    <div onClick={() => toggle(!open)}>
      <Icon />
    </div>
  );
}

FlyOut.Toggle = Toggle;
```
这意味着在任何文件中我们想使用`FlyOut`只需要导入`FlyOut`组件就行。

```js
mport React from "react";
import { FlyOut } from "./FlyOut";

export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
    </FlyOut>
  );
}
```
只有切换是不够的，同时也需要根据`open`的值来显示和隐藏列表项的`List`

```js
function List({ children }) {
  const { open } = React.useContext(FlyOutContext);
  return open && <ul>{children}</ul>;
}

function Item({ children }) {
  return <li>{children}</li>;
}
```
`List`组件是否渲染其子组件取决于`open`的值是true还是false,
就像·`Toggle`组件那样，我们把`List`和`Item`組件也作为属性添加到`FlyOut`组件中。
```js
const FlyOutContext = createContext();

function FlyOut(props) {
  const [open, toggle] = useState(false);

  return (
    <FlyOutContext.Provider value={{ open, toggle }}>
      {props.children}
    </FlyOutContext.Provider>
  );
}

function Toggle() {
  const { open, toggle } = useContext(FlyOutContext);

  return (
    <div onClick={() => toggle(!open)}>
      <Icon />
    </div>
  );
}

function List({ children }) {
  const { open } = useContext(FlyOutContext);
  return open && <ul>{children}</ul>;
}

function Item({ children }) {
  return <li>{children}</li>;
}

FlyOut.Toggle = Toggle;
FlyOut.List = List;
FlyOut.Item = Item;
```
现在可以将他们作为`FlyOut`的属性使用，在本例中，我们希望向用户显示两个选项: Edit 和 Delete。我们创建一个渲染两个`FlyOut.Item`的组件，一个是编辑选项，一个是删除选项
```js
import React from "react";
import { FlyOut } from "./FlyOut";

export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
      <FlyOut.List>
        <FlyOut.Item>Edit</FlyOut.Item>
        <FlyOut.Item>Delete</FlyOut.Item>
      </FlyOut.List>
    </FlyOut>
  );
}
```
很好，我们创建完整了`FlyOut`组件，并没有在`FlyOutMenu`本身添加状态。

创建自己的组件库的时候很适合用组合模式，你可以在使用UI库如[Semantic UI](https://react.semantic-ui.com/modules/dropdown/#types-dropdown)中看到组合模式。

## [React.Children.map](https://reactjs.org/docs/react-api.html#reactchildrenmap)

我们还可以通过映射组件的子组件来实现组合模式。我们可以通过使用额外的props克隆这些元素，将 open 和 toggle作为props添加到这些子组件中。

```js
export function FlyOut(props) {
  const [open, toggle] = React.useState(false);

  return (
    <div>
      {React.Children.map(props.children, child =>
        React.cloneElement(child, { open, toggle })
      )}
    </div>
  );
}
```
所有的子组件都被复制了，并且将`open`和`toggle`传到了每个子组件中，不像之前那个例子那样使用Cntext API,我们现在可以通过`props`传值。

<iframe src="https://codesandbox.io/embed/provider-pattern-2-forked-37qlqf?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="provider-pattern-2 (forked)"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

## 优点

复合组件管理它们自己的内部状态，它们在几个子组件之间共享内部状态，在实现复合组件时，我们不必担心自己管理状态，导入复合组件时，我们不必显式导入该组件上可用的子组件

```js
import { FlyOut } from "./FlyOut";

export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
      <FlyOut.List>
        <FlyOut.Item>Edit</FlyOut.Item>
        <FlyOut.Item>Delete</FlyOut.Item>
      </FlyOut.List>
    </FlyOut>
  );
}
```

## 缺点
当使用 React. Children.map 提供值时，组件嵌套是有限的，只有父组件的直接子组件才能访问`open`和`toggle`,意味着我们不能将这些组件中的任何一个封装到另一个组件中。

```js
export default function FlyoutMenu() {
  return (
    <FlyOut>
      {/* This breaks */}
      <div>
        <FlyOut.Toggle />
        <FlyOut.List>
          <FlyOut.Item>Edit</FlyOut.Item>
          <FlyOut.Item>Delete</FlyOut.Item>
        </FlyOut.List>
      </div>
    </FlyOut>
  );
}
```
使用 React.cloneElement 克隆元素执行浅合并,
已经存在的props将和新的props合并在一起。如果已经存在的props与我们传递给 React.cloneElement 的props名称相同，之前的props将会被后面的覆盖。
