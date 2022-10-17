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
