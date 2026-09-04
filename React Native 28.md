# 📱 React Native — Video #28 Notes

## **Fetching Single Records**

[Complete React Native Tutorial #28 - Fetching Single Records](https://www.youtube.com/watch?v=T5fZBjWF8U4&utm_source=chatgpt.com)

This is **Video #28** of Net Ninja's 29-part *Complete React Native* course. The lesson is about taking the `id` from the dynamic route you created in Video #27 and using that ID to fetch **one specific book document from Appwrite**. ([netninja.dev][1])

---

# 1. 🎯 What We Are Building

In Video #27, we created a dynamic route:

```text
books/
├── index.jsx
└── [id].jsx
```

When a user taps a book:

```text
Book List
   ↓
Book ID
   ↓
/books/abc123
   ↓
[id].jsx
```

In Video #27, `[id].jsx` could **receive the ID**, but we didn't actually retrieve the book.

Now Video #28 completes the process:

```text
/books/abc123
      ↓
useLocalSearchParams()
      ↓
id = "abc123"
      ↓
Appwrite getDocument()
      ↓
Single book document
      ↓
Display book details
```

---

# 2. 🧠 `listDocuments()` vs `getDocument()`

This is one of the most important concepts in this video.

### Fetch multiple records

We previously used:

```jsx
databases.listDocuments({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
});
```

This gives us:

```text
documents
├── Book 1
├── Book 2
├── Book 3
└── Book 4
```

### Fetch one record

Now we use:

```jsx
databases.getDocument({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
  documentId: id,
});
```

This gives us:

```text
Book 2
```

So remember:

| Method             | Purpose                   |
| ------------------ | ------------------------- |
| `listDocuments()`  | Get many documents        |
| `getDocument()`    | Get one specific document |
| `createDocument()` | Create a document         |
| `deleteDocument()` | Delete a document         |

---

# 3. 📍 Get the ID From the Dynamic Route

Our file is:

```text
app/
└── books/
    ├── index.jsx
    └── [id].jsx
```

Inside `[id].jsx`:

```jsx
import { useLocalSearchParams } from "expo-router";

const BookDetails = () => {
  const { id } = useLocalSearchParams();

  console.log(id);

  return null;
};

export default BookDetails;
```

If we navigated to:

```text
/books/abc123
```

then:

```jsx
const { id } = useLocalSearchParams();
```

gives:

```text
id = "abc123"
```

### Important

`id` comes from the filename:

```text
[id].jsx
 ↑
parameter name
```

So:

```jsx
const { id } = useLocalSearchParams();
```

matches `[id]`.

---

# 4. 🔗 Where Does This ID Come From?

Remember Video #27.

Our Appwrite books have:

```jsx
book.$id
```

For example:

```js
{
  $id: "67abc123",
  title: "Atomic Habits",
  author: "James Clear"
}
```

When the user taps that book:

```jsx
router.push(`/books/${item.$id}`);
```

The URL becomes:

```text
/books/67abc123
```

Then `[id].jsx` receives:

```jsx
id === "67abc123"
```

So the Appwrite document ID travels through the route.

---

# 5. 📡 Fetch One Document From Appwrite

We can now create a function:

```jsx
const getBook = async () => {
  try {
    const response = await databases.getDocument({
      databaseId: DATABASE_ID,
      collectionId: COLLECTION_ID,
      documentId: id,
    });

    console.log(response);
  } catch (error) {
    console.log("Get Book Error:", error);
  }
};
```

The important part is:

```jsx
documentId: id
```

We're telling Appwrite:

> "Go to this database and collection and give me the document whose ID is this."

---

# 6. 🗃️ Store the Book in State

We don't want to only `console.log()` the result.

We need to store it in React state:

```jsx
import { useEffect, useState } from "react";
import { useLocalSearchParams } from "expo-router";

const BookDetails = () => {
  const { id } = useLocalSearchParams();

  const [book, setBook] = useState(null);

  const getBook = async () => {
    try {
      const response = await databases.getDocument({
        databaseId: DATABASE_ID,
        collectionId: COLLECTION_ID,
        documentId: id,
      });

      setBook(response);
    } catch (error) {
      console.log("Get Book Error:", error);
    }
  };

  useEffect(() => {
    getBook();
  }, [id]);

  return null;
};

export default BookDetails;
```

Now:

```jsx
book
```

contains the complete Appwrite document.

For example:

```js
{
  $id: "67abc123",
  title: "Atomic Habits",
  author: "James Clear",
  description: "An easy and proven way...",
  rating: 5
}
```

---

# 7. ⚡ Why `useEffect()`?

We don't want this:

```jsx
const BookDetails = () => {
  getBook();

  return ...
};
```

because the component renders → `getBook()` runs → state changes → component renders again → `getBook()` runs again...

This can cause repeated requests.

Instead:

```jsx
useEffect(() => {
  getBook();
}, [id]);
```

This tells React:

> Run `getBook()` when this screen needs to load the book for this `id`.

---

# 8. 🖥️ Display the Book

Once the request finishes:

```jsx
setBook(response);
```

React re-renders.

We can then use:

```jsx
{book && (
  <View>
    <Text>{book.title}</Text>
    <Text>{book.author}</Text>
    <Text>{book.description}</Text>
    <Text>{book.rating}</Text>
  </View>
)}
```

The `book &&` is important because initially:

```jsx
book === null
```

The request hasn't finished yet.

After Appwrite responds:

```jsx
book === {
  $id: "...",
  title: "...",
  ...
}
```

and the details appear.

---

# 9. 🧩 Complete Practical Example

For your Shelfie project, the screen can follow this structure:

```jsx
import { useEffect, useState } from "react";
import { Text, View } from "react-native";
import { useLocalSearchParams } from "expo-router";

import { databases } from "../../lib/appwrite";

const DATABASE_ID = "YOUR_DATABASE_ID";
const COLLECTION_ID = "YOUR_COLLECTION_ID";

const BookDetails = () => {
  const { id } = useLocalSearchParams();

  const [book, setBook] = useState(null);

  const getBook = async () => {
    try {
      const response = await databases.getDocument({
        databaseId: DATABASE_ID,
        collectionId: COLLECTION_ID,
        documentId: id,
      });

      setBook(response);
    } catch (error) {
      console.log("Get Book Error:", error);
    }
  };

  useEffect(() => {
    getBook();
  }, [id]);

  return (
    <View>
      {book && (
        <>
          <Text>{book.title}</Text>
          <Text>{book.author}</Text>
          <Text>{book.description}</Text>
          <Text>{book.rating}</Text>
        </>
      )}
    </View>
  );
};

export default BookDetails;
```

**Note:** Appwrite's SDK uses the object-style API in current versions, so `getDocument({ databaseId, collectionId, documentId })` is the form to prefer in your project.

---

# 10. 🔄 Complete Shelfie Data Flow

This is the most important mental model for Videos **23–28**.

### Creating a book — Video #23

```text
Form
 ↓
addBook()
 ↓
createDocument()
 ↓
Appwrite
```

### Fetching all books — Video #24

```text
Appwrite
 ↓
listDocuments()
 ↓
books array
 ↓
BooksContext
 ↓
FlatList
```

### Clicking a book — Video #27

```text
FlatList item
 ↓
item.$id
 ↓
router.push(`/books/${item.$id}`)
 ↓
/books/[id].jsx
```

### Fetching that specific book — Video #28

```text
[id].jsx
 ↓
useLocalSearchParams()
 ↓
id
 ↓
getDocument()
 ↓
Appwrite
 ↓
single book
 ↓
setBook()
 ↓
UI
```

Put together:

```text
                 APPWRITE
                    │
          ┌─────────┴─────────┐
          │                   │
    listDocuments()     getDocument()
          │                   │
          ↓                   ↓
      All books          One book
          │                   │
          ↓                   ↓
       FlatList           Book Details
          │
          ↓
      item.$id
          │
          ↓
   /books/[id].jsx
          │
          ↓
useLocalSearchParams()
          │
          ↓
          id
          │
          └──────→ getDocument()
```

---

# 11. 🆚 `listDocuments()` vs `getDocument()` — Very Important

Suppose Appwrite contains:

```text
Collection: books

1. Atomic Habits       → $id = abc
2. Clean Code          → $id = def
3. The Alchemist       → $id = ghi
4. Deep Work           → $id = jkl
```

### `listDocuments()`

```jsx
const response = await databases.listDocuments({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
});
```

Result:

```text
[
  Atomic Habits,
  Clean Code,
  The Alchemist,
  Deep Work
]
```

### `getDocument()`

```jsx
const response = await databases.getDocument({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
  documentId: "def",
});
```

Result:

```text
Clean Code
```

That's the fundamental difference.

---

# 12. ⚠️ What If the ID Doesn't Exist?

Suppose the route is:

```text
/books/does-not-exist
```

Then:

```jsx
await databases.getDocument(...)
```

will fail.

That's why we use:

```jsx
try {
  ...
} catch (error) {
  console.log("Get Book Error:", error);
}
```

In a production app, you'd normally show something useful:

```jsx
<Text>Book not found.</Text>
```

rather than exposing a raw backend error.

---

# 13. ⏳ Loading State

There's another practical improvement.

Initially:

```jsx
book === null
```

But `null` can mean either:

1. We are still loading.
2. No book exists / request failed.

A separate loading state makes this clearer:

```jsx
const [book, setBook] = useState(null);
const [loading, setLoading] = useState(true);
```

Then:

```jsx
const getBook = async () => {
  try {
    const response = await databases.getDocument({
      databaseId: DATABASE_ID,
      collectionId: COLLECTION_ID,
      documentId: id,
    });

    setBook(response);
  } catch (error) {
    console.log("Get Book Error:", error);
  } finally {
    setLoading(false);
  }
};
```

And:

```jsx
if (loading) {
  return <Text>Loading...</Text>;
}
```

This connects nicely with the `ActivityIndicator` concept from Video #20.

---

# 14. 🧠 Why We Don't Pass the Whole Book Through the Route

You might wonder why we don't do something like:

```jsx
router.push({
  pathname: "/books/[id]",
  params: {
    title: item.title,
    author: item.author,
  },
});
```

Instead, we normally pass the identifier:

```jsx
router.push(`/books/${item.$id}`);
```

Then fetch the authoritative record from Appwrite.

Why?

Because the route only needs to identify the resource:

```text
/books/abc123
```

The backend remains the source of truth.

This becomes particularly important when data can change in real time.

---

# 15. 🔥 Important Connection With Video #26

Remember realtime data?

```jsx
client.subscribe(...)
```

That allows your app to know that something changed.

But when opening a detail page, we can fetch the latest version:

```jsx
getDocument(...)
```

So you can think of:

```text
Realtime subscription
        ↓
Keep list data updated

getDocument()
        ↓
Fetch the specific record
```

They solve different problems.

---

# ⭐ Video #28 — Must Remember

### 1. `[id].jsx` is a dynamic route

```text
books/[id].jsx
```

means `id` is supplied dynamically.

---

### 2. Get the route parameter with:

```jsx
import { useLocalSearchParams } from "expo-router";

const { id } = useLocalSearchParams();
```

---

### 3. `id` comes from the URL

```text
/books/abc123
```

gives:

```jsx
id === "abc123"
```

---

### 4. Fetch one Appwrite document with:

```jsx
databases.getDocument({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
  documentId: id,
});
```

---

### 5. `listDocuments()` vs `getDocument()`

```text
listDocuments() → many documents
getDocument()   → one document
```

---

### 6. Store the response in state

```jsx
setBook(response);
```

---

### 7. Fetch inside `useEffect`

```jsx
useEffect(() => {
  getBook();
}, [id]);
```

---

### 8. Handle asynchronous loading/errors

Use:

```jsx
try / catch / finally
```

and preferably a dedicated:

```jsx
loading
```

state.

---

### 9. The Appwrite `$id` is what connects everything

```text
book.$id
   ↓
router.push()
   ↓
[id].jsx
   ↓
useLocalSearchParams()
   ↓
getDocument()
   ↓
specific book
```

---

# 🧠 Final Mental Model

Think of a dynamic detail page as:

```text
USER TAPS BOOK
      ↓
item.$id
      ↓
router.push(`/books/${item.$id}`)
      ↓
URL: /books/abc123
      ↓
[id].jsx
      ↓
useLocalSearchParams()
      ↓
id = "abc123"
      ↓
databases.getDocument()
      ↓
Appwrite finds document "abc123"
      ↓
setBook(response)
      ↓
React re-renders
      ↓
BOOK DETAILS SHOWN
```

### The key idea:

> **Video #27 taught you how to identify a record through the URL. Video #28 teaches you how to use that ID to retrieve the actual record from Appwrite.**

You're now one step away from the final database feature: **deleting the book**, which is Video #29. The course officially lists Video #28 as “Fetching Single Records” and Video #29 as “Deleting Books.” ([netninja.dev][1])

[1]: https://netninja.dev/p/complete-react-native?utm_source=chatgpt.com "Complete React Native | Net Ninja"
