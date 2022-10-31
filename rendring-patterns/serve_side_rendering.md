# 服务端渲染

在服务端生成要渲染的 HTML 以响应用户请求

## 服务端渲染

服务器端渲染(SSR)是最古老的渲染 Web 内容的方法之一。SSR 为响应用户请求而呈现的页面内容生成完整的 HTML。内容可能包括来自数据存储或外部 API 的数据。

连接和获取操作在服务器上处理。格式化内容所需的 HTML 也在服务器上生成。因此，使用 SSR，我们可以避免进行额外的数据获取和模板化往返。因此，在客户端上不需要呈现代码，并且不需要向客户端发送相应的 JavaScript。

使用 SSR，每个请求都被独立处理，并由服务器作为新请求处理。即使两个连续请求的输出没有很大的不同，服务器也会从头处理并生成它。由于服务器对于多个用户是通用的，因此在给定的时间内所有活动用户共享处理能力。

## 经典的 SSR 实现

让我们看看如何使用经典的 SSR 和 JavaScript 创建显示当前时间的页面。

```js
<!DOCTYPE html>
<html>
   <head>
       <title>Time</title>
   </head>
   <body>
       <div>
       <h1>Hello, world!</h1>
       <b>It is <div id=currentTime></div></b>
       </div>
   </body>
</html>
```

注意这与提供相同输出的 CSR 代码有何不同。还要注意，虽然 HTML 是由服务器呈现的，但这里显示的时间是由 JavaScript 函数 tick ()填充的客户机上的本地时间。如果您希望显示任何其他特定于服务器的数据，例如，服务器时间，则需要在呈现之前将其嵌入 HTML 中。这意味着如果没有到服务器的往返过程，它将不会自动刷新。

# 服务端渲染的优缺点

服务器上执行呈现代码和减少 JavaScript 具有以下优点
**较小的 JavaScript 导致更快的 FCP 和 TTI**

在页面上有多个 UI 元素和应用程序逻辑的情况下，与 CSR 相比，SSR 的 JavaScript 要少得多。因此，加载和处理脚本所需的时间较少。FP、 FCP 和 TTI 较短，FCP = TTI。使用 SSR，用户将不会等待所有屏幕元素出现，并且屏幕元素变为交互式的。
**为客户端 JavaScript 提供额外的预算**

开发团队需要使用一个 JS 预算来限制页面上 JS 的数量，以实现所需的性能。使用 SSR，由于您直接消除了呈现页面所需的 JS，因此它为应用程序可能需要的任何第三方 JS 创建了额外的空间。

 **启用搜索引擎优化**
 搜索引擎爬虫很容易抓取 SSR 应用程序的内容，从而确保页面的搜索引擎优化更高。

 由于上述优点，SSR 可以很好地处理静态内容。然而，它确实有一些缺点，因为它并不适用于所有场景。

 ## 低TTFB

 由于所有处理都发生在服务器上，因此在以下一种或多种情况下，服务器的响应可能会延迟
  - 多个并发用户导致服务器负载过大。
  - 网络缓慢
  - 服务器代码优化。

  **某些交互需要重新加载整个页面**

  由于客户端上没有可用的所有代码，所以导致重新加载整个页面的所有关键操作都需要频繁地往返于服务器。这可能会增加交互之间的时间，因为用户需要在两个操作之间等待更长的时间。因此，单页应用不适合用SSR。

  为了解决这些缺点，现代框架和库允许在同一个应用程序的服务器和客户端上呈现。我们将在下面的部分中详细介绍这些内容。首先，让我们看看使用 Next.js 的 SSR 的简单形式。

  ## Next.js和CSR

  Next.js框架还支持 SSR。这会在每个请求上预先呈现服务器上的页面。它可以通过从如下页面导出一个名为 getServerSideProps ()的异步函数来实现。

  ```js
  export async function getServerSideProps(context)
  return {
    props: {}, // will be passed to the page component as props
  }
}
```


上下文对象包含 HTTP 请求和响应对象的键、路由参数、 querystring、 locale 等。

下面的实现演示如何使用 getServerSideProps ()在使用 React 格式化的页面上呈现数据。完整的实现可以这里找到。

## React中的服务端渲染

React 可以同构地呈现，这意味着它既可以在浏览器上运行，也可以在服务器等其他平台上运行。因此，UI 元素可以使用 React 在服务器上呈现。

React 也可以与通用代码一起使用，通用代码允许相同的代码在多种环境中运行。这是通过在服务器上使用 Node.js 或所谓的 Node 服务器实现的。因此，可以使用通用 JavaScript 在服务器上获取数据，然后使用同构 React 进行渲染。

让我们来看看使这成为可能的React函数。

``` js
ReactDOMServer.renderToString(element)
```

此函数返回与 React 元素对应的 HTML 字符串。然后可以将 HTML 呈现给客户端，以便更快地加载页面。

[RenderToString ()](https://reactjs.org/docs/react-dom-server.html#rendertostring)函数可以与 [ReactDOM.hyde ()](https://reactjs.org/docs/react-dom.html#hydrate)一起使用。这将确保呈现的 HTML 在客户端保持原样，并且只在加载后附加事件处理程序。
