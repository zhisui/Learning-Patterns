# 命令模式
通过向指挥官发送命令来解耦执行任务的方法

使用命令模式，我们可以将执行特定任务的对象与调用该方法的对象解耦。

假设我们有一个在线送餐平台，用户可以下订单、跟踪和取消订单。

```js
class OrderManager() {
  constructor() {
    this.orders = []
  }

  placeOrder(order, id) {
    this.orders.push(id)
    return `You have successfully ordered ${order} (${id})`;
  }

  trackOrder(id) {
    return `Your order ${id} will arrive in 20 minutes.`
  }

  cancelOrder(id) {
    this.orders = this.orders.filter(order => order.id !== id)
    return `You have canceled your order ${id}`
  }
}
```
在OrderManager 类上，我们可以访问 placeOrder、 trackOrder 和 CancelOrder 方法
直接使用这些方法将是完全有效的 JavaScript！

```js
const manager = new OrderManager();

manager.placeOrder("Pad Thai", "1234");
manager.trackOrder("1234");
manager.cancelOrder("1234");
```
但是，直接在`manager`实例上调用这些方法也有缺点。可能发生的情况是，我们决定稍后重命名某些方法，或者方法的功能发生变化.

我们现在将它重命名为 addOrder，而不是叫它 placeOrder！这意味着我们必须确保不在代码库的任何地方调用 placeOrder 方法，这在大型应用程序中可能非常棘手。

相反，我们希望将方法与 manager 对象解耦，并为每个命令创建单独的命令函数！
让我们重构 OrderManager 类: 它将只有一个方法: execute，而不是 placeOrder、 CancelOrder 和 trackOrder 方法。此方法将执行它所给出的任何命令.
每个命令都应该可以访问订单管理器的`orders`，我们将把它作为第一个参数传递。

```js
class OrderManager {
  constructor() {
    this.orders = [];
  }

  execute(command, ...args) {
    return command.execute(this.orders, ...args);
  }
}
```
我们需要为订单管理器创建三个命令:
* PlaceOrderCommand
* CancelOrderCommand
* TrackOrderCommand

```js
class Command {
  constructor(execute) {
    this.execute = execute;
  }
}

function PlaceOrderCommand(order, id) {
  return new Command(orders => {
    orders.push(id);
    return `You have successfully ordered ${order} (${id})`;
  });
}

function CancelOrderCommand(id) {
  return new Command(orders => {
    orders = orders.filter(order => order.id !== id);
    return `You have canceled your order ${id}`;
  });
}

function TrackOrderCommand(id) {
  return new Command(() => `Your order ${id} will arrive in 20 minutes.`);
}
```
完美! 不需要将方法直接耦合到 OrderManager 实例,它们现在是独立的、解耦的函数，我们可以通过 OrderManager 上可用的 execute 方法来调用它们。

## 优点
命令模式允许我们将方法与执行操作的对象解耦，如果您正在处理具有一定生命周期的命令，或者应该排队并在特定时间执行的命令，那么它将为您提供更多的控制。

## 缺点
命令模式的用例非常有限，并且经常向应用程序添加不必要的样板
