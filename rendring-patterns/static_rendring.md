# 静态渲染
构建网站时传送预先渲染生成的THTML内容

## 静态渲染

根据我们对 SSR 的讨论，我们知道服务器上的高请求处理时间对 TTFB 有负面影响。类似地，使用 CSR 时，由于下载和处理脚本所需的时间，大型 JavaScript 包可能会对应用程序的 FCP、 LCP 和 TTI 造成损害。

静态渲染或静态生成(SSG)试图通过向构建站点时生成的客户端提供预呈现的 HTML 内容来解决这些问题。

静态 HTML 文件提前生成，对应于用户可以访问的每个路由。这些静态 HTML 文件可以在服务器或 CDN 上使用，并在客户端请求时获取。

静态文件也可以缓存，从而提供更大的弹性。由于 HTML 响应是提前生成的，因此服务器上的处理时间可以忽略不计，从而导致更快的 TTFB 和更好的性能。在理想的场景中，客户端 JS 应该是最小的，静态页面应该在客户端接收到响应后很快变为交互式的。因此，SSG 有助于实现更快的 FCP/TTI

## SSG-基本结构

顾名思义，静态渲染是静态内容的理想选择，因为页面不需要根据登录用户进行定制(例如个性化推荐)。因此，像“关于我们”、“联系我们”、网站的博客页面或电子商务应用程序的产品页面这样的静态页面是静态渲染的理想选择。像 Next.js、 Gatsby 和 VuePress 这样的框架支持静态生成。让我们从这个没有任何数据的静态内容呈现的简单 Next.js 示例开始。
Next.js:

```js
// pages/about.js

export default function About() {
 return <div>
   <h1>About Us</h1>
   {/* ... */}
 </div>
}
```

当站点被构建(使用下一次构建)时，这个页面将被预先呈现到路由/about 上可访问的 HTML 文件 about. HTML 中

## 带数据的 SSG

类似“ About us”或“ Contact us”页面中的静态内容可以按原样呈现，而不需要从数据存储中获取数据。但是，对于单个博客页面或产品页面之类的内容，来自数据存储的数据必须与特定模板合并，然后在构建时呈现为 HTML。

生成的 HTML 页面的数量将分别取决于博客文章的数量或产品的数量。要访问这些页面，您可能还需要列出页面，这些页面将是包含数据项的分类和格式化列表的 HTML 页面。可以使用 Next.js 静态呈现来处理这些场景。我们可以根据可用的项目生成列表页或单个项目页。让我们看看。

## 列表页-所有项目

清单页面的生成是一个场景，其中要在页面上显示的内容取决于外部数据。此数据将在生成时从数据库中提取，以构造页。在 Next.js 中，这可以通过在页面组件中导出函数 getStaticProps ()来实现。在生成服务器上的生成时调用该函数以获取数据。然后可以将数据传递到页面的道具，以预先呈现页面组件。让我们看看生成产品列表页面的代码，这个页面最初是作为本文的一部分共享的。

```js
// This function runs at build time on the build server
export async function getStaticProps() {
 return {
   props: {
     products: await getProductsFromDatabase()
   }
 }
}

// The page component receives products prop from getStaticProps at build time
export default function Products({ products }) {
 return (
   <>
     <h1>Products</h1>
     <ul>
       {products.map((product) => (
         <li key={product.id}>{product.name}</li>
       ))}
     </ul>
   </>
 )
}
```

该函数不包含在客户端 JS 包中，因此甚至可以用于直接从数据库中获取数据。

## 个别详情页-每个项目

在上面的示例中，我们可以为列表页面上列出的每个产品提供一个单独的详细页面。可以通过单击清单页面上的相应项目或直接通过其他途径访问这些页面。

设我们有产品 ID 为101、102103的产品，以此类推。我们需要他们的信息可在/products/101, /products/102, /products/103等。为了在构建 Next.js 时实现这一点，我们可以结合使用函数 getStaticPaths ()和[动态路由](https://nextjs.org/docs/routing/dynamic-routes)。

我们需要创建一个通用的页面组件products/[id].js,并在其中导出函数 getStaticPath ()。该函数将返回所有可能的产品 ID，这些 ID 可用于在构建时预先呈现单个产品页面。[这里](https://vercel.com/blog/nextjs-server-side-rendering-vs-static-generation#individual-product-page-static-generation-with-data)提供的下面的 Next.js 框架显示了如何为此构造代码。
```js

// pages/products/[id].js

// In getStaticPaths(), you need to return the list of
// ids of product pages (/products/[id]) that you’d
// like to pre-render at build time. To do so,
// you can fetch all products from a database.
export async function getStaticPaths() {
 const products = await getProductsFromDatabase()

 const paths = products.map((product) => ({
   params: { id: product.id }
 }))

 // fallback: false means pages that don’t have the correct id will 404.
 return { paths, fallback: false }
}

// params will contain the id for each generated page.
export async function getStaticProps({ params }) {
 return {
   props: {
     product: await getProductFromDatabase(params.id)
   }
 }
}

export default function Product({ product }) {
 // Render product
}
```
产品页面上的详细信息可以在构建时通过使用特定产品 ID 的函数 getStaticProps 填充。请注意这里使用了fallback: false。这意味着，如果一个页面对应于特定的路由或产品 ID 不可用，那么将显示404错误页面。

因此，我们可以使用 SSG 来预呈现许多不同类型的页面。

## SSG-主要注意事项

正如所讨论的，SSG 为网站带来了很好的性能，因为它减少了客户端和服务器上所需的处理。这些网站也是搜索引擎优化友好的，因为内容已经存在，可以由网络爬虫呈现，没有额外的努力。虽然性能和 SEO 使 SSG 成为一个很好的呈现模式，但是在评估 SSG 对于特定应用程序的适用性时，需要考虑以下因素：
**1、大量的 HTML 文件**: 需要为用户可能访问的每个可能路径生成单独的 HTML 文件。例如，当将其用于博客时，将为数据存储中可用的每篇博客文章生成一个 HTML 文件。随后，对任何文章的编辑都需要重新构建，以便在静态 HTML 文件中反映更新。维护大量的 HTML 文件可能具有挑战性。
**托管依赖**: 对于一个 SSG 站点来说，用于存储和服务 HTML 文件的托管平台也应该是很好的，这样可以实现超级快速的响应。如果一个经过良好调优的 SSG 网站正确地托管在多个 CDN 上，以利用边缘缓存的优势，那么极好的性能是可能的。
**动态内容**: 每次内容更改时，都需要构建和重新部署 SSG 站点。如果在任何内容更改之后仍未建立 + 部署站点，则显示的内容可能过时。这使得 SSG 不适合高度动态的内容
