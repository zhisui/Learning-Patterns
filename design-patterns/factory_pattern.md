# 工厂模式
用工厂函数来创建对象

使用工厂模式，我们可以使用工厂函数来创建新对象，当一个函数不使用 new 关键字而返回一个新对象时，它就是一个工厂函数！

假设我们的应用程序需要许多用户。我们可以使用 firstName、 lastName 和 email 属性创建新用户。工厂函数还将 `fullName` 属性添加到新创建的对象中，该属性返回 firstName 和 lastName。
```js
const createUser = ({ firstName, lastName, email }) => ({
  firstName,
  lastName,
  email,
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
});
```
很好，通过调用`createUser`函数我们可创建多个用户。

```js
const createUser = ({ firstName, lastName, email }) => ({
  firstName,
  lastName,
  email,
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
});

const user1 = createUser({
  firstName: "John",
  lastName: "Doe",
  email: "john@doe.com"
});

const user2 = createUser({
  firstName: "Jane",
  lastName: "Doe",
  email: "jane@doe.com"
});

console.log(user1);
console.log(user2);
```

如果我们要创建相对复杂和可配置的对象，工厂模式可能很有用。可能发生的情况是，键和值的值依赖于某个环境或配置。使用工厂模式，我们可以轻松地创建包含自定义键和值的新对象！
```js
const createObjectFromArray = ([key, value]) => ({
  [key]: value
});
createObjectFromArray(["name", "John"]); // { name: "John" }
```

## 优点
当我们必须创建共享相同属性的较小对象时，工厂模式非常有用。工厂函数可以根据当前环境或用户特定的配置轻松返回自定义对象。

## 缺点

在 JavaScript 中，工厂模式仅仅是一个不使用 new 关键字返回对象的函数，ES6中的箭头函数允许我们创建小型工厂函数，每次隐式返回一个对象。但是，在许多情况下，每次创建新实例而不是新对象可能更有效地提高内存使用效率。
```js
class User {
  constructor(firstName, lastName, email) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.email = email;
  }

  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}

const user1 = new User({
  firstName: "John",
  lastName: "Doe",
  email: "john@doe.com"
});

const user2 = new User({
  firstName: "Jane",
  lastName: "Doe",
  email: "jane@doe.com"
});
```
