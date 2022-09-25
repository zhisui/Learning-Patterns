# 享元模式

使用相同对象时候复用实例

当我们使用大量对象时，享元模式是一种节约内存的有用方法。

在我们的应用程序中，我们希望用户能够添加图书。所有的书都有一个`标题`、一个`作者`和一个 `isbn` 数字！然而，一个图书馆通常不只有一本书: 它通常有同一本书的多个副本.
如果同一本书有多个副本，那么每次创建一个新的图书实例就没有多大用处了。相反，我们希望创建 `Book` 构造函数的多个实例，这些实例表示一本书。

```js
class Book {
  constructor(title, author, isbn) {
    this.title = title
    this.author = author
    this.isbn = isbn
  }
}
```

让我们创建将新书添加到列表的功能。如果一本书有相同的 ISBN 号，那么是完全相同的书籍类型，我们不希望创建一个全新的 Book 实例。相反，我们应该首先检查这本书是否已经存在。

```js
const books = new Map()

const createBook = (title, author, isbn) => {
  const existingBook = books.has(isbn)

  if (existingBook) {
    return books.get(isbn)
  }
}
```

如果它还没有包含书的 ISBN 号，我们就创建一本新书，并将其 ISBN 号添加到 `isbnNumbers` 集中

```js
const createBook = (title, author, isbn) => {
  const existingBook = books.has(isbn)

  if (existingBook) {
    return books.get(isbn)
  }

  const book = new Book(title, author, isbn)
  books.set(isbn, book)

  return book
}
```

CreateBook 函数帮助我们创建一种类型的图书的新实例.
然而，一个图书馆通常包含同一本书的多个副本！让我们创建一个 addBook 函数，它允许我们添加同一本书的多个副本。它应该调用 createBook 函数，该函数返回新创建的 Book 实例，或者返回已经存在的实例.
为了跟踪复本的总量，让我们创建一个 bookList 数组，它包含图书馆中的图书总量。

```js
const bookList = []

const addBook = (title, author, isbn, availability, sales) => {
  const book = {
    ...createBook(title, author, isbn),
    sales,
    availability,
    isbn,
  }

  bookList.push(book)
  return book
}
```

非常好，并不是每次添加副本都创建一个 Book 实例，我们可以有效地将已经存在的 Book 实例用于该特定副本。
让我们创建 3 本书的 5 个副本， Harry Potter, To Kill a Mockingbird, and The Great Gatsby

```js
addBook('Harry Potter', 'JK Rowling', 'AB123', false, 100)
addBook('Harry Potter', 'JK Rowling', 'AB123', true, 50)
addBook('To Kill a Mockingbird', 'Harper Lee', 'CD345', true, 10)
addBook('To Kill a Mockingbird', 'Harper Lee', 'CD345', false, 20)
addBook('The Great Gatsby', 'F. Scott Fitzgerald', 'EF567', false, 20)
```
虽然这里有5个副本，但是只有3个实例。
<iframe src="https://codesandbox.io/embed/flyweight-pattern-1-m5c31?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="flyweight-pattern-1"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

   当你创建大量对象时，这个享元模式非常有用，因为它可能会耗尽所有可用的内存。它使我们能够最小化消耗的内存量。
   在 JavaScript 中，我们可以通过[原型链继承](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)轻松地解决这个问题。如今，硬件有大量的内存，这使得享元模式变得不那么重要。
