# 📱 React Native — Video #27 Notes

## Dynamic Routes

This is **Video #27 — “Dynamic Routes”** in Net Ninja's Complete React Native course. It comes after `FlatList` and realtime data, and prepares the app for **fetching and displaying one specific book** in Video #28. The lesson is 8:19 long. ([YouTube][1])

The main idea is:

> **Use a value from the URL/path to identify which record the user wants to view.**

For Shelfie, that means:

```text
Books List
    ↓
User taps a book
    ↓
/books/abc123
    ↓
abc123 = book ID
    ↓
Book Details Screen
```

---

# 1. 🎯 What Is a Dynamic Route?

Until now, we've had routes like:

```text
/about
/profile
/books
```

These are **static routes**.

The route is always the same.

A **dynamic route** contains a variable part.

For example:

```text
/books/123
/books/456
/books/789
```

The `123`, `456`, and `789` are different values.

Instead of creating a separate screen for every book, we create **one dynamic screen**.

```text
/books/[id].jsx
```

The `[id]` means:

> "This part of the route can change."

---

# 2. 🧠 Static vs Dynamic Routes

### Static route

```text
/books
```

Always points to:

```text
books.jsx
```

### Dynamic route

```text
/books/[id]
```

Can point to:

```text
/books/abc123
/books/xyz456
/books/789
```

All of them use the same:

```text
[id].jsx
```

So:

```text
Static
/books
    ↓
books.jsx


Dynamic
/books/[id]
    ↓
[id].jsx
```

---

# 3. 📁 File-Based Routing

This builds directly on what you learned in Video #3.

Suppose our app has:

```text id="g8px5f"
app/
│
├── _layout.jsx
│
├── books/
│   ├── index.jsx
│   └── [id].jsx
│
└── profile.jsx
```

This gives us:

```text id="xj1c3a"
books/index.jsx
       ↓
/books

books/[id].jsx
       ↓
/books/:id
```

For example:

```text
/books/abc123
```

will render:

```text
books/[id].jsx
```

---

# 4. 🔑 What Does `[id]` Mean?

This:

```text id="q9tq4h"
[id].jsx
```

is a **dynamic route segment**.

`id` is the parameter name.

For example:

```text
/books/abc123
```

gives:

```js
id = "abc123"
```

And:

```text
/books/xyz789
```

gives:

```js
id = "xyz789"
```

The filename determines the parameter name.

```text
[id].jsx
  ↑
parameter name
```

---

# 5. 🧩 Why Do We Need This for Shelfie?

Our Appwrite books have unique document IDs:

```js
{
  $id: "abc123",
  title: "Atomic Habits",
  author: "James Clear"
}
```

The `$id` uniquely identifies that book.

So when the user taps:

```text
Atomic Habits
```

we can navigate to:

```text
/books/abc123
```

Then the details screen knows:

```text
id = abc123
```

and later, in Video #28, we can use that ID to fetch the specific Appwrite document.

---

# 6. 📱 Passing the Book ID

Suppose our `FlatList` displays books.

We can use Expo Router's `Link`:

```jsx
import { Link } from "expo-router";
```

Then:

```jsx
<Link href={`/books/${item.$id}`}>
  <Text>{item.title}</Text>
</Link>
```

If the book has:

```js
item.$id = "abc123"
```

the resulting route becomes:

```text
/books/abc123
```

If another book has:

```js
item.$id = "xyz789"
```

the route becomes:

```text
/books/xyz789
```

Same screen:

```text
books/[id].jsx
```

Different parameter.

---

# 7. 🔄 Dynamic Route Flow

This is the important flow:

```text id="n0h9q2"
FlatList
   ↓
User taps book
   ↓
Book ID = abc123
   ↓
/books/abc123
   ↓
[id].jsx
   ↓
id = abc123
```

The dynamic route acts as the bridge between:

```text
Book in the list
```

and:

```text
Book details
```

---

# 8. 🧭 Using `router.push()`

You can also navigate programmatically using Expo Router's `router`.

```jsx
import { router } from "expo-router";
```

Then:

```jsx
router.push(`/books/${item.$id}`);
```

For example:

```jsx
const handlePress = () => {
  router.push(`/books/${item.$id}`);
};
```

So if:

```js
item.$id === "abc123"
```

then:

```js
router.push("/books/abc123");
```

---

# 9. 🆚 `Link` vs `router.push()`

You already learned about `Link` in Video #3.

### `Link`

Good when the navigation is directly represented by a UI element:

```jsx
<Link href={`/books/${item.$id}`}>
  <Text>{item.title}</Text>
</Link>
```

### `router.push()`

Useful when navigation happens as part of a function:

```jsx
const handlePress = () => {
  router.push(`/books/${item.$id}`);
};
```

For example, inside a `Pressable`:

```jsx
<Pressable onPress={() => router.push(`/books/${item.$id}`)}>
  <Text>{item.title}</Text>
</Pressable>
```

Both can navigate to a dynamic route.

---

# 10. 📄 Creating `[id].jsx`

Inside:

```text
books/
```

create:

```text
[id].jsx
```

For example:

```jsx
import { Text, View } from "react-native";

const BookDetails = () => {
  return (
    <View>
      <Text>Book Details</Text>
    </View>
  );
};

export default BookDetails;
```

Now:

```text
/books/abc123
```

will render this component.

But we haven't actually used `abc123` yet.

That's the next important concept.

---

# 11. 📦 Reading the Dynamic Parameter

Expo Router provides the route parameters.

One way to access them is:

```jsx
import { useLocalSearchParams } from "expo-router";
```

Then:

```jsx
const BookDetails = () => {
  const { id } = useLocalSearchParams();

  return (
    <View>
      <Text>Book ID: {id}</Text>
    </View>
  );
};
```

If you navigate to:

```text
/books/abc123
```

you get:

```text
Book ID: abc123
```

If you navigate to:

```text
/books/xyz789
```

you get:

```text
Book ID: xyz789
```

---

# 12. 🧠 `useLocalSearchParams()`

This hook:

```jsx
useLocalSearchParams()
```

gives you the parameters associated with the current route.

For:

```text
/books/abc123
```

and:

```text
[id].jsx
```

you can get:

```jsx
const { id } = useLocalSearchParams();
```

So:

```text
URL
 ↓
/books/abc123
 ↓
[id].jsx
 ↓
useLocalSearchParams()
 ↓
id = "abc123"
```

---

# 13. 🔥 Why Is This Powerful?

Without dynamic routes, you might imagine needing:

```text
book1.jsx
book2.jsx
book3.jsx
book4.jsx
...
```

That's obviously impossible for a database containing hundreds of books.

Dynamic routing gives you:

```text
[id].jsx
```

**one screen** that can represent any book.

```text
/books/1   → [id].jsx
/books/2   → [id].jsx
/books/3   → [id].jsx
/books/4   → [id].jsx
```

This is one of the most important concepts in file-based routing.

---

# 14. 🏗️ Shelfie Example

Imagine Appwrite returns:

```js
[
  {
    $id: "a123",
    title: "Atomic Habits",
  },
  {
    $id: "b456",
    title: "The Alchemist",
  },
  {
    $id: "c789",
    title: "1984",
  },
]
```

Our FlatList:

```jsx
<FlatList
  data={books}
  keyExtractor={(item) => item.$id}
  renderItem={({ item }) => (
    <Pressable
      onPress={() => router.push(`/books/${item.$id}`)}
    >
      <Text>{item.title}</Text>
    </Pressable>
  )}
/>
```

Now:

### User taps Atomic Habits

```text
/books/a123
```

### User taps The Alchemist

```text
/books/b456
```

### User taps 1984

```text
/books/c789
```

All three go to:

```text
books/[id].jsx
```

---

# 15. 🧩 Complete Example

### `books/index.jsx`

```jsx
import { FlatList, Pressable, Text } from "react-native";
import { router } from "expo-router";

import { useBooks } from "../../hooks/useBooks";

const Books = () => {
  const { books } = useBooks();

  return (
    <FlatList
      data={books}
      keyExtractor={(item) => item.$id}
      renderItem={({ item }) => (
        <Pressable
          onPress={() => router.push(`/books/${item.$id}`)}
        >
          <Text>{item.title}</Text>
          <Text>{item.author}</Text>
        </Pressable>
      )}
    />
  );
};

export default Books;
```

### `books/[id].jsx`

```jsx
import { Text, View } from "react-native";
import { useLocalSearchParams } from "expo-router";

const BookDetails = () => {
  const { id } = useLocalSearchParams();

  return (
    <View>
      <Text>Book Details</Text>
      <Text>Book ID: {id}</Text>
    </View>
  );
};

export default BookDetails;
```

Now:

```text
Books List
     ↓
Tap Atomic Habits
     ↓
/books/a123
     ↓
[id].jsx
     ↓
id = "a123"
```

---

# 16. 🔗 Dynamic Route + Appwrite

This is where the whole database architecture starts coming together.

We already have:

```text
Appwrite document
       ↓
$id
```

For example:

```js
{
  $id: "abc123",
  title: "Atomic Habits"
}
```

We pass `$id` into the route:

```jsx
router.push(`/books/${item.$id}`);
```

Then the dynamic screen receives:

```jsx
const { id } = useLocalSearchParams();
```

Now:

```text id="5ks0h9"
Appwrite
   ↓
$id = abc123
   ↓
Dynamic URL
   ↓
/books/abc123
   ↓
[id].jsx
   ↓
id = abc123
```

That `id` is what we'll use to fetch the **individual document** in Video #28.

---

# 17. 🧠 Route Parameter vs Query Parameter

Don't confuse these two.

### Dynamic route parameter

```text
/books/abc123
```

Here:

```text
abc123
```

is part of the route.

With:

```text
[id].jsx
```

we access:

```jsx
const { id } = useLocalSearchParams();
```

### Query parameter

A URL could also look like:

```text
/books?id=abc123
```

Here `id` is a query parameter.

Both can be accessed through `useLocalSearchParams()`, but they represent different URL structures.

For this course's book details flow, the important structure is:

```text
/books/[id]
```

---

# 18. 📁 Route Structure to Remember

A useful structure for Shelfie:

```text id="3gn3ef"
app/
│
├── _layout.jsx
│
├── (tabs)/
│   ├── _layout.jsx
│   └── books/
│       ├── index.jsx
│       └── [id].jsx
│
└── ...
```

Conceptually:

```text
books/index.jsx
      ↓
    /books

books/[id].jsx
      ↓
 /books/:id
```

The exact placement can vary depending on how you've structured your route groups, but the `[id]` concept remains the same.

---

# 19. ⚠️ Common Mistakes

### ❌ Naming it `id.jsx`

```text
books/
  id.jsx
```

That's a static route:

```text
/books/id
```

It does **not** mean a dynamic ID.

You need:

```text
books/
  [id].jsx
```

The square brackets are what make it dynamic.

---

### ❌ Forgetting the `$id`

For Appwrite documents:

```jsx
item.$id
```

not:

```jsx
item.id
```

unless you've separately created an `id` attribute.

---

### ❌ Forgetting to read the parameter

Creating:

```text
[id].jsx
```

doesn't automatically give you a variable called `id` in your component.

You need:

```jsx
const { id } = useLocalSearchParams();
```

---

# 20. 🔄 Complete Video 21 → 28 Picture

You're now building a complete data-driven application:

```text
              APPWRITE
                 │
                 ↓
            Books Database
                 │
                 ↓
          listDocuments()
                 │
                 ↓
            BooksContext
                 │
                 ↓
               books
                 │
                 ↓
              FlatList
                 │
                 ↓
          User taps a book
                 │
                 ↓
       router.push(`/books/${id}`)
                 │
                 ↓
           books/[id].jsx
                 │
                 ↓
      useLocalSearchParams()
                 │
                 ↓
                id
                 │
                 ↓
     fetch single Appwrite document
                 │
                 ↓
          Book Details
```

Video #27 is essentially teaching the **routing portion** of this chain.

---

# ⭐ Video #27 — Must Remember

### 1. Dynamic routes use square brackets

```text
[id].jsx
```

### 2. `[id]` represents a variable route segment

```text
/books/abc123
/books/xyz789
```

Both use:

```text
[id].jsx
```

### 3. Navigate using the actual ID

For Appwrite:

```jsx
router.push(`/books/${item.$id}`);
```

### 4. Read the parameter with `useLocalSearchParams()`

```jsx
import { useLocalSearchParams } from "expo-router";

const { id } = useLocalSearchParams();
```

### 5. One screen can represent many records

```text
/books/1 → [id].jsx
/books/2 → [id].jsx
/books/3 → [id].jsx
```

### 6. Appwrite `$id` is perfect for dynamic routes

```jsx
item.$id
```

becomes:

```text
/books/{item.$id}
```

### 7. Dynamic routing doesn't fetch the book by itself

It only gives you the identifier.

```text
Dynamic route
      ↓
gets ID
      ↓
Video #28
      ↓
use ID to fetch book
```

---

# 🧠 Final Mental Model

The simplest way to remember dynamic routes is:

```text
                 BOOK LIST
                    │
                    ↓
             User taps a book
                    │
                    ↓
              book.$id
                    │
                    ↓
        /books/abc123
                    │
                    ↓
              [id].jsx
                    │
                    ↓
       useLocalSearchParams()
                    │
                    ↓
              id = abc123
                    │
                    ↓
         "Which book is this?"
                    │
                    ↓
       Fetch that book from Appwrite
```

### 🔑 One sentence to remember:

> **`[id].jsx` creates a dynamic route, the book's `$id` becomes part of the URL, and `useLocalSearchParams()` lets the destination screen retrieve that ID.**

And this is exactly why **Video #28 — Fetching Single Records** is the natural next step: once `[id].jsx` gives us the document ID, we can use that ID to retrieve the corresponding book from Appwrite. ([netninja.dev][2])

[1]: https://www.youtube.com/watch?v=H0hk9pr9Sdg&utm_source=chatgpt.com "Complete React Native Tutorial #27 - Dynamic Routes - YouTube"
[2]: https://netninja.dev/p/complete-react-native?utm_source=chatgpt.com "Complete React Native | Net Ninja"
