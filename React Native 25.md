# 📱 React Native — Video #25 Notes

## Using the `FlatList` Component

This video teaches how to properly display the books fetched from Appwrite using React Native's **`FlatList`** component. It follows directly from Video #24, where `books` was populated using `listDocuments()`. The Net Ninja course lists this as a 7:10 lesson. ([YouTube][1])

The main idea is:

> **Instead of manually mapping an array of books with `.map()`, use `FlatList` to efficiently render a list of data.**

---

# 1. 🎯 Why Do We Need `FlatList`?

In the previous video, we had:

```jsx
{books.map((book) => (
  <Text key={book.$id}>
    {book.title}
  </Text>
))}
```

This works.

But imagine you have:

```text
10 books
100 books
1,000 books
10,000 books
```

Rendering everything at once isn't ideal for a mobile application.

React Native provides:

```jsx
FlatList
```

which is designed specifically for efficiently rendering lists.

---

# 2. 📦 Importing `FlatList`

Use an ES6 import:

```jsx
import { FlatList } from "react-native";
```

Remember, we're using:

```jsx
import
```

and **not** `require()`.

---

# 3. 🧩 Basic `FlatList`

The simplest example:

```jsx
<FlatList
  data={books}
  renderItem={({ item }) => (
    <Text>{item.title}</Text>
  )}
/>
```

There are two particularly important props here:

```text
data
renderItem
```

---

# 4. 📊 `data` Prop

The `data` prop tells `FlatList`:

> **"Here is the array of data that you need to display."**

For our application:

```jsx
data={books}
```

If:

```js
books = [
  {
    $id: "1",
    title: "Atomic Habits",
  },
  {
    $id: "2",
    title: "The Alchemist",
  },
];
```

then:

```jsx
<FlatList data={books} />
```

tells `FlatList` to work through that array.

---

# 5. 🎨 `renderItem` Prop

`renderItem` tells `FlatList`:

> **"For each item in the array, this is what you should render."**

Example:

```jsx
renderItem={({ item }) => (
  <Text>{item.title}</Text>
)}
```

Here:

```jsx
item
```

represents the current book.

So internally, you can think of it like:

```text
books
 │
 ├── book 1 → item
 ├── book 2 → item
 ├── book 3 → item
 └── book 4 → item
```

For every book, `renderItem` produces the UI.

---

# 6. 🧠 Understanding `{ item }`

This syntax:

```jsx
renderItem={({ item }) => (
  <Text>{item.title}</Text>
)}
```

can initially look confusing.

`renderItem` receives an object containing information about the current list item.

Conceptually:

```js
{
  item,
  index,
  separators
}
```

We only need `item` right now.

So:

```jsx
({ item })
```

is destructuring the `item` property.

You could also write:

```jsx
renderItem={(data) => (
  <Text>{data.item.title}</Text>
)}
```

but:

```jsx
({ item })
```

is much cleaner.

---

# 7. 🔄 `FlatList` vs `.map()`

### Using `.map()`

```jsx
{books.map((book) => (
  <Text key={book.$id}>
    {book.title}
  </Text>
))}
```

### Using `FlatList`

```jsx
<FlatList
  data={books}
  renderItem={({ item }) => (
    <Text>{item.title}</Text>
  )}
/>
```

The important difference is that `FlatList` is specifically optimized for displaying lists in React Native.

It can manage which items need to be rendered as the user scrolls instead of treating the entire collection like one giant block of UI.

---

# 8. 🆔 What About `key`?

With `.map()` we manually use:

```jsx
key={book.$id}
```

With `FlatList`, React Native can use each item's identity through the `keyExtractor` mechanism.

For Appwrite documents, we can explicitly tell it to use:

```jsx
keyExtractor={(item) => item.$id}
```

Example:

```jsx
<FlatList
  data={books}
  keyExtractor={(item) => item.$id}
  renderItem={({ item }) => (
    <Text>{item.title}</Text>
  )}
/>
```

This is a good pattern for your Shelfie app because every Appwrite document has a `$id`.

---

# 9. 🧱 Creating a Book Item Component

Instead of putting all the book UI inside `renderItem`, it's often cleaner to create a separate component.

For example:

```jsx
const BookItem = ({ book }) => {
  return (
    <View>
      <Text>{book.title}</Text>
      <Text>{book.author}</Text>
    </View>
  );
};
```

Then:

```jsx
<FlatList
  data={books}
  keyExtractor={(item) => item.$id}
  renderItem={({ item }) => (
    <BookItem book={item} />
  )}
/>
```

Now the responsibilities are clearer:

```text
FlatList
   ↓
Which books?
   ↓
BookItem
   ↓
How should one book look?
```

---

# 10. 📱 A More Realistic Shelfie Example

Suppose each book contains:

```js
{
  $id: "abc123",
  title: "Atomic Habits",
  author: "James Clear",
  description: "A book about habits",
  rating: 5
}
```

We could display:

```jsx
import { FlatList, StyleSheet, Text, View } from "react-native";

const Books = ({ books }) => {
  return (
    <FlatList
      data={books}
      keyExtractor={(item) => item.$id}
      renderItem={({ item }) => (
        <View style={styles.book}>
          <Text style={styles.title}>{item.title}</Text>
          <Text>{item.author}</Text>
          <Text>Rating: {item.rating}</Text>
        </View>
      )}
    />
  );
};

export default Books;

const styles = StyleSheet.create({
  book: {
    padding: 16,
    marginBottom: 10,
  },

  title: {
    fontSize: 18,
    fontWeight: "bold",
  },
});
```

---

# 11. 🔗 Connecting It With `useBooks()`

In our actual Shelfie architecture, `books` comes from the Books Context.

So:

```jsx
import { FlatList, Text } from "react-native";
import { useBooks } from "../hooks/useBooks";

const Books = () => {
  const { books } = useBooks();

  return (
    <FlatList
      data={books}
      keyExtractor={(item) => item.$id}
      renderItem={({ item }) => (
        <Text>{item.title}</Text>
      )}
    />
  );
};

export default Books;
```

The complete chain is now:

```text
Appwrite
   ↓
listDocuments()
   ↓
response.documents
   ↓
setBooks()
   ↓
BooksContext
   ↓
useBooks()
   ↓
books
   ↓
FlatList
   ↓
Book UI
```

That's the important connection between Videos **22 → 24 → 25**.

---

# 12. 📭 What If There Are No Books?

`FlatList` provides a useful prop:

```jsx
ListEmptyComponent
```

For example:

```jsx
<FlatList
  data={books}
  keyExtractor={(item) => item.$id}
  renderItem={({ item }) => (
    <Text>{item.title}</Text>
  )}
  ListEmptyComponent={
    <Text>No books found.</Text>
  }
/>
```

When:

```js
books = []
```

the empty component can be displayed.

So:

```text
books.length > 0
      ↓
show books

books.length === 0
      ↓
show empty message
```

---

# 13. 📜 `FlatList` Is Scrollable

One major advantage is that `FlatList` handles scrolling for you.

You don't normally need to wrap it inside a `ScrollView`.

For example:

```jsx
<FlatList
  data={books}
  renderItem={({ item }) => (
    <Text>{item.title}</Text>
  )}
/>
```

The list itself can scroll.

### ⚠️ Avoid unnecessary nesting

Don't generally do:

```jsx
<ScrollView>
  <FlatList ... />
</ScrollView>
```

for the same scrolling content.

`FlatList` already provides the scrolling behavior and virtualization intended for long lists.

---

# 14. ⚡ Why `FlatList` Is Better for Large Lists

Imagine:

```text
1000 books
```

A simple `.map()` approach conceptually creates UI for all 1000 items.

`FlatList` is designed around **virtualized rendering**.

It can prioritize rendering the items that are currently needed/visible and manage additional items as you scroll.

That's why it is much more appropriate for potentially large lists.

Think:

```text
                 FlatList
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
     Visible items       More items
          │                   │
          ↓                   ↓
      Render now       Render as needed
```

---

# 15. 🧠 `renderItem` Is a Function

Remember:

```jsx
renderItem={({ item }) => (
  <Text>{item.title}</Text>
)}
```

`renderItem` isn't the component itself.

It is a **function that tells FlatList what UI to create for each item**.

Conceptually:

```js
const renderBook = ({ item }) => {
  return (
    <Text>
      {item.title}
    </Text>
  );
};
```

Then:

```jsx
<FlatList
  data={books}
  renderItem={renderBook}
/>
```

This can be useful when your item UI becomes more complex.

---

# 16. 🔢 The `index`

`renderItem` also provides the item's index:

```jsx
<FlatList
  data={books}
  renderItem={({ item, index }) => (
    <Text>
      {index + 1}. {item.title}
    </Text>
  )}
/>
```

If the array is:

```text
Atomic Habits
The Alchemist
1984
```

you could display:

```text
1. Atomic Habits
2. The Alchemist
3. 1984
```

You don't always need `index`, but it's useful to know that `renderItem` provides it.

---

# 17. 🧩 Important `FlatList` Props

You don't need to memorize every prop, but these are worth knowing:

| Prop                  | Purpose                  |
| --------------------- | ------------------------ |
| `data`                | Array of items           |
| `renderItem`          | UI for each item         |
| `keyExtractor`        | Unique key for each item |
| `ListEmptyComponent`  | UI when list is empty    |
| `ListHeaderComponent` | UI above the list        |
| `ListFooterComponent` | UI below the list        |
| `horizontal`          | Makes list horizontal    |
| `numColumns`          | Creates multiple columns |

For now, the core three are:

```text
data
renderItem
keyExtractor
```

---

# 18. 🏗️ Your Books Architecture Now

After Videos 21–25, your architecture is becoming:

```text
                     Appwrite
                        │
             ┌──────────┴──────────┐
             │                     │
       createDocument()      listDocuments()
             │                     │
             ↓                     ↓
          addBook()            getBooks()
             │                     │
             └──────────┬──────────┘
                        ↓
                  BooksContext
                        │
                        ↓
                      books
                        │
                        ↓
                    useBooks()
                        │
                        ↓
                    FlatList
                        │
                        ↓
                    Book Item
```

This is a very important point in the course.

---

# 19. 🆚 `map()` vs `FlatList` — When to Use Which?

### `.map()`

Good for:

```text
Small/simple collections
Static UI
Rendering a few elements
```

Example:

```jsx
{items.map((item) => (
  <Text key={item.id}>{item.name}</Text>
))}
```

### `FlatList`

Prefer for:

```text
Long lists
Dynamic data
Backend/database records
Scrollable lists
Performance-sensitive lists
```

For your Shelfie books coming from Appwrite:

> **`FlatList` is the appropriate choice.**

---

# ⭐ Video #25 — Must Remember

### 1. Import `FlatList`

```jsx
import { FlatList } from "react-native";
```

### 2. `data`

Tells `FlatList` what array to display:

```jsx
data={books}
```

### 3. `renderItem`

Tells it how to display each item:

```jsx
renderItem={({ item }) => (
  <Text>{item.title}</Text>
)}
```

### 4. `keyExtractor`

Give each item a stable unique key:

```jsx
keyExtractor={(item) => item.$id}
```

For Appwrite documents, `$id` is a natural choice.

### 5. `FlatList` handles scrolling

You generally don't need a `ScrollView` around it.

### 6. `ListEmptyComponent`

Useful when there are no books:

```jsx
ListEmptyComponent={<Text>No books found.</Text>}
```

### 7. `FlatList` is designed for efficient lists

It's preferable to `.map()` when dealing with potentially large/dynamic collections.

---

# 🧠 Final Mental Model

Don't think of `FlatList` as complicated.

Think:

```text
FlatList
   │
   ├── data
   │      ↓
   │    "What should I display?"
   │
   ├── renderItem
   │      ↓
   │    "How should ONE item look?"
   │
   └── keyExtractor
          ↓
        "How do I uniquely identify each item?"
```

For Shelfie:

```text
books
  ↓
FlatList
  ↓
book 1 → Book UI
book 2 → Book UI
book 3 → Book UI
book 4 → Book UI
...
```

And your complete data flow is:

```text
Appwrite Database
       ↓
listDocuments()
       ↓
getBooks()
       ↓
BooksContext
       ↓
books state
       ↓
useBooks()
       ↓
FlatList
       ↓
Individual Book Items
```

### 🔑 One sentence to remember:

> **`FlatList` takes an array through `data` and uses `renderItem` to efficiently render each item as a scrollable list.**

**Next in the course:** Video #26 — **Real-Time Data**, where the app starts responding automatically when database data changes. ([netninja.dev][2])

[1]: https://www.youtube.com/watch?v=xT96_qMggT8&utm_source=chatgpt.com "Complete React Native Tutorial #25 - Using the FlatList Components - YouTube"
[2]: https://netninja.dev/p/complete-react-native?utm_source=chatgpt.com "Complete React Native | Net Ninja"
