# Next.js概述
合成React应用的vercel框架

## Next.js介绍

由 Vercel 创建的 Next.js 是用于混合 React 应用程序的框架。通常很难理解加载内容的所有不同方式。Next.js对此进行了抽象，以使其尽可能简单。该框架允许您构建可伸缩的、高性能的 React 代码，并提供了零配置方法。这使得开发人员可以专注于构建特性。
让我们探讨一下与我们的讨论相关的 Next.js 特性

# 基本特性
## 预渲染
默认情况下，Next.js 提前为每个页面生成 HTML，而不是在客户端。这个过程称为[预渲染](https://nextjs.org/docs/basic-features/pages#pre-rendering)。Js 确保使页面完全交互所需的 JavaScript 代码与生成的 HTML 相关联。此 JavaScript 代码在页面加载后运行。此时，React JS 在 Shadow DOM 中工作，以确保呈现的内容与 React 应用程序将呈现的内容相匹配，而无需实际操作它。这个过程叫做[水合作用](https://blog.somewhatabstract.com/2020/03/16/hydration-and-server-side-rendering/)。

每个页面都是从page目录中的.js, .jsx.或者.tsx 文件导出。路由是根据文件名确定的。例如，page/about.js 对应于path/about。React.js 通过服务器端渲染(SSR)和静态生成来支持预渲染。您还可以在同一个应用程序中混合使用不同的呈现方法，其中一些页面是使用 SSR 生成的，另一些是使用静态生成的。客户端呈现也可用于呈现这些页面的某些部分。

## 数据获取
Next.js同时支持 SSR 和静态生成的数据获取。

`1、getStaticProps`
 - 与静态生成一起使用，以渲染数据

 `2、getStaticPaths`
 - 与静态生成一起使用，以呈现动态路由

  `3、getServerSideProps`
 - 适用于SSR

 ## 静态文件服务
像图像这样的静态文件可以在根目录中名为 public 的文件夹下提供。然后，可以使用根 URL 在不同页面的 < img> 标记代码中引用相同的图像。例如，src =/logo.png。
 ## 自动图像优化
 Next.js实现了自动图像优化，它允许在浏览器支持的情况下调整大小、优化和提供现代格式的图像。因此，当需要时，大图像可以根据较小的视图窗口调整大小。图像优化是通过导入 Next.js Image 组件来实现的，Next.js Image 组件是 HTML < img > 元素的扩展。要使用 Image 组件，应该按如下方式导入它。
 ```js
 import Image from 'next/image'
 ```
 图像组件可以使用以下代码在页面上提供。
 ```js
 <Image src="/logo.png" alt="Logo" width={500} height={500} />
 ```
Next.js 支持通过page目录进行路由。每一个在这个目录或其嵌套子目录中的 .js 文件成为一个带有相应路由的页面。Next.js还支持使用命名参数创建动态路由，其中显示的实际文档由参数值决定。

例如，页面page/products/[ pid ].js，将被匹配到/post/1之类的路由，pid = 1作为查询参数之一。Next.js 也支持链接到[其他页面上](https://nextjs.org/docs/api-reference/next/link#if-the-route-has-dynamic-segments)的这些动态路由

## 代码分割

代码分割确保只向客户端发送所需的 JavaScript，这有助于提高性能。React.js支持两种类型的代码分割。

**1.基于路由**：这是在 Next.js 中默认实现的。当用户访问路由时，Next.js 只发送初始路由所需的代码。当用户在应用程序周围导航时，其他块将根据需要下载。这限制了需要立即解析和编译的代码量，从而提高了页面加载时间。

**1.基于组件**：这种类型的代码分割允许将大型组件分割成单独的块，在需要时可以延迟加载这些块。Next.js通过[动态导入()](https://nextjs.org/docs/advanced-features/dynamic-import)支持基于组件的代码分割。这允许您动态导入 JavaScript 模块(包括 React 组件) ，并将每个导入作为一个单独的块加载。
## 开始使用 Next.js
Next.js可以安装在任何具有 Node.js 10.13或更高版本的 Linux、 Windows 或 Mac 操作系统上。自动和手动[创建](https://nextjs.org/docs/getting-started)都可以。

### 自动创建 ###
```js
npx create-next-app
# or
yarn create next-app
```
一旦安装好，你就可以运行开发服务器并访问 http://localhost:3000上的页面。
通过对 Next.js 的介绍，我们现在可以研究不同模式的实现


