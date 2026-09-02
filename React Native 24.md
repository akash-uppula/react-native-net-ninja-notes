# 📱 React Native — Video #24 Notes

## Fetching Book Records

**Video #24 — “Fetching Book Records”** continues directly from the Books Context and database work from Videos #21–#23. The course sequence confirms that fetching comes immediately after creating records. ([YouTube][1])

The main goal of this video is:

> **Fetch the books stored in Appwrite and put them into React state so the UI can use them.**

---

# 1. 🎯 What Are We Doing?

In Video #23, we learned how to **create** a book:

```text
Form
  ↓
addBook()
  ↓
Appwrite
  ↓
Database
  ↓
New book document
```

Now we need to get those books back:

```text
Appwrite Database
       ↓
listDocuments()
       ↓
Books Context
       ↓
books state
       ↓
Screen
```

So we're moving in the opposite direction.

---

# 2. 🧠 The Important Appwrite Method

The key method is:

```jsx
databases.listDocuments()
```

It retrieves documents from an Appwrite collection.

Conceptually:

```jsx
const response = await databases.listDocuments(...);
```

The response contains the documents retrieved from the collection.

We then take:

```jsx
response.documents
```

and store them in our React state.

---

# 3. 📦 Our Books State

From Video #22, we created:

```jsx
const [books, setBooks] = useState([]);
```

Initially:

```text
books = []
```

After fetching:

```text
books = [
  book1,
  book2,
  book3,
  ...
]
```

So `books` becomes the central piece of state containing the books retrieved from Appwrite.

---

# 4. 🔄 Creating `getBooks()`

Inside `BooksContext`, create a function:

```jsx
const getBooks = async () => {
  try {
    const response = await databases.listDocuments({
      databaseId: DATABASE_ID,
      collectionId: COLLECTION_ID,
    });

    setBooks(response.documents);
  } catch (error) {
    console.log("Get Books Error:", error);
  }
};
```

The important part is:

```jsx
setBooks(response.documents);
```

That takes the documents returned by Appwrite and puts them into React state.

---

# 5. 🗄️ Where Does `response.documents` Come From?

Imagine Appwrite returns something conceptually like:

```js
{
  total: 3,
  documents: [
    {
      $id: "1",
      title: "Atomic Habits",
      author: "James Clear"
    },
    {
      $id: "2",
      title: "The Alchemist",
      author: "Paulo Coelho"
    },
    {
      $id: "3",
      title: "1984",
      author: "George Orwell"
    }
  ]
}
```

We don't need the entire response.

We want:

```js
response.documents
```

which gives:

```js
[
  {
    $id: "1",
    title: "Atomic Habits",
    author: "James Clear"
  },
  {
    $id: "2",
    title: "The Alchemist",
    author: "Paulo Coelho"
  },
  {
    $id: "3",
    title: "1984",
    author: "George Orwell"
  }
]
```

Then:

```jsx
setBooks(response.documents);
```

stores that array.

---

# 6. 🧩 Complete `BooksContext`

Putting the previous video and this video together:

```jsx
import { createContext, useState } from "react";
import { ID } from "react-native-appwrite";

import { databases } from "../lib/appwrite";

export const BooksContext = createContext(null);

const DATABASE_ID = "your-database-id";
const COLLECTION_ID = "your-collection-id";

const BooksProvider = ({ children }) => {
  const [books, setBooks] = useState([]);

  // Fetch all books
  const getBooks = async () => {
    try {
      const response = await databases.listDocuments({
        databaseId: DATABASE_ID,
        collectionId: COLLECTION_ID,
      });

      setBooks(response.documents);
    } catch (error) {
      console.log("Get Books Error:", error);
    }
  };

  // Create a new book
  const addBook = async (book) => {
    try {
      const response = await databases.createDocument({
        databaseId: DATABASE_ID,
        collectionId: COLLECTION_ID,
        documentId: ID.unique(),
        data: {
          title: book.title,
          author: book.author,
          description: book.description,
          rating: book.rating,
        },
      });

      return response;
    } catch (error) {
      console.log("Add Book Error:", error);
      throw error;
    }
  };

  const value = {
    books,
    getBooks,
    addBook,
  };

  return (
    <BooksContext.Provider value={value}>
      {children}
    </BooksContext.Provider>
  );
};

export default BooksProvider;
```

### The context now has three important things:

```text
books
  ↓
Stored book records

getBooks()
  ↓
Fetch books from Appwrite

addBook()
  ↓
Create a book in Appwrite
```

---

# 7. 🔌 Exposing `getBooks()`

Don't forget to include it in the context value:

```jsx
const value = {
  books,
  getBooks,
  addBook,
};
```

Otherwise the screen won't be able to access it.

Then:

```jsx
const { books, getBooks } = useBooks();
```

works.

---

# 8. 🖥️ Calling `getBooks()` From a Screen

Suppose we have a Books screen.

We can get the function from our custom hook:

```jsx
import { useBooks } from "../hooks/useBooks";

const Books = () => {
  const { books, getBooks } = useBooks();

  // ...
};
```

Now we have access to:

```text
books
getBooks()
```

---

# 9. ⚛️ Why `useEffect` Is Important

We don't want to wait for the user to press a button before loading the books.

We normally want:

> When the Books screen loads → fetch the books.

That's where `useEffect` comes in.

```jsx
import { useEffect } from "react";

useEffect(() => {
  getBooks();
}, []);
```

The empty dependency array:

```jsx
[]
```

means the effect runs when the component mounts.

So:

```text
Books screen opens
       ↓
useEffect()
       ↓
getBooks()
       ↓
Appwrite
       ↓
response.documents
       ↓
setBooks()
       ↓
books state updated
```

---

# 10. 📱 Complete Example

```jsx
import { useEffect } from "react";
import { Text, View } from "react-native";

import { useBooks } from "../hooks/useBooks";

const Books = () => {
  const { books, getBooks } = useBooks();

  useEffect(() => {
    getBooks();
  }, []);

  return (
    <View>
      <Text>My Books</Text>

      {books.map((book) => (
        <Text key={book.$id}>
          {book.title}
        </Text>
      ))}
    </View>
  );
};

export default Books;
```

Now if Appwrite has:

```text
Atomic Habits
The Alchemist
1984
```

the screen can display:

```text
My Books

Atomic Habits
The Alchemist
1984
```

---

# 11. 🔑 Why Use `book.$id`?

Every Appwrite document has a unique ID.

Appwrite exposes it as:

```jsx
book.$id
```

So when rendering a list:

```jsx
<Text key={book.$id}>
  {book.title}
</Text>
```

we use the document ID as the React key.

This is better than using:

```jsx
key={book.title}
```

because titles aren't guaranteed to be unique.

---

# 12. 🧠 `useEffect` + Database Fetching

This pattern is extremely important:

```jsx
useEffect(() => {
  getBooks();
}, []);
```

Think of it as:

> **When this component appears, perform this side effect.**

Fetching data from a backend is a **side effect**, so `useEffect` is commonly used for it.

---

# 13. ⚠️ Don't Do This

Avoid putting the fetch directly in the component body:

```jsx
const Books = () => {
  const { getBooks } = useBooks();

  getBooks(); // ❌
  
  return (...);
};
```

Why?

Rendering can happen multiple times.

That could result in:

```text
Render
 ↓
getBooks()

Render again
 ↓
getBooks()

Render again
 ↓
getBooks()
```

Potentially causing repeated API requests.

Instead:

```jsx
useEffect(() => {
  getBooks();
}, []);
```

---

# 14. 🔄 State Update Causes Re-render

This is another important React concept.

Initially:

```jsx
books = []
```

The screen renders:

```text
My Books
```

Then:

```jsx
getBooks()
```

fetches the data.

Then:

```jsx
setBooks(response.documents);
```

changes the state.

React re-renders the component.

Now:

```text
books = [
  Atomic Habits,
  The Alchemist,
  1984
]
```

and those books appear on screen.

The complete flow:

```text
Initial Render
     ↓
books = []
     ↓
useEffect()
     ↓
getBooks()
     ↓
Appwrite request
     ↓
response.documents
     ↓
setBooks(...)
     ↓
State changes
     ↓
React re-renders
     ↓
Books appear
```

---

# 15. 🧠 Why Fetching Belongs in `BooksContext`

We could technically write:

```jsx
databases.listDocuments(...)
```

inside the Books screen.

But we're deliberately separating responsibilities.

### Screen

Responsible for:

```text
UI
User interaction
Displaying books
```

### BooksContext

Responsible for:

```text
Book state
Fetching books
Creating books
Database communication
```

### Appwrite

Responsible for:

```text
Storing the actual data
```

So:

```text
             Books Screen
                  │
                  │ useBooks()
                  ↓
            Books Context
             │         │
       getBooks()   addBook()
             │         │
             └────┬────┘
                  ↓
              Appwrite
                  │
                  ↓
              Database
```

This is a very important architecture pattern.

---

# 16. 🔐 Authentication vs Database

At this stage you have two separate contexts:

### `AuthContext`

Handles:

```text
login()
register()
logout()
user
```

### `BooksContext`

Handles:

```text
books
getBooks()
addBook()
```

So:

```text
AuthContext
     ↓
Who is the user?

BooksContext
     ↓
What books are we working with?
```

This separation prevents one giant context from handling everything.

---

# 17. 🧪 What Happens If Fetching Fails?

Use `try/catch`:

```jsx
const getBooks = async () => {
  try {
    const response = await databases.listDocuments({
      databaseId: DATABASE_ID,
      collectionId: COLLECTION_ID,
    });

    setBooks(response.documents);
  } catch (error) {
    console.log("Get Books Error:", error);
  }
};
```

If Appwrite fails:

```text
Appwrite
   ↓
❌ Error
   ↓
catch(error)
   ↓
Log error
```

Later, you can improve this by storing an error state and displaying a user-friendly message, just like you did for authentication.

---

# 18. 🆚 `listDocuments()` vs `createDocument()`

This distinction is worth memorizing.

### Creating

```jsx
databases.createDocument(...)
```

means:

> **PUT NEW DATA INTO THE DATABASE**

### Fetching

```jsx
databases.listDocuments(...)
```

means:

> **GET EXISTING DATA FROM THE DATABASE**

So:

```text
CREATE
createDocument()
     ↓
Database

FETCH
listDocuments()
     ↑
Database
```

---

# 19. 📚 Why This Sets Up Video #25

At the moment, we can fetch:

```js
books
```

but we're using:

```jsx
books.map(...)
```

to display them.

That works for small lists, but React Native provides a much better component for larger lists:

```text
FlatList
```

And that's exactly what comes next in **Video #25 — “Using the FlatList Components.”** The course curriculum places FlatList immediately after fetching records. ([TutoHub][2])

---

# ⭐ Video #24 — Must Remember

### 1. `listDocuments()`

Used to retrieve documents from an Appwrite collection:

```jsx
const response = await databases.listDocuments({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
});
```

### 2. Get the actual documents

```jsx
response.documents
```

### 3. Store them in state

```jsx
setBooks(response.documents);
```

### 4. Fetch when the screen loads

```jsx
useEffect(() => {
  getBooks();
}, []);
```

### 5. Don't fetch directly during render

❌

```jsx
getBooks();
```

✅

```jsx
useEffect(() => {
  getBooks();
}, []);
```

### 6. Appwrite document IDs

Use:

```jsx
book.$id
```

for a unique document identifier.

### 7. Context separates data logic from UI

```text
Screen
  ↓
useBooks()
  ↓
BooksContext
  ↓
Appwrite
```

---

# 🧠 Final Mental Model

Think of the entire database workflow you've learned so far:

```text
                  APPWRITE
                     │
              ┌──────┴──────┐
              │             │
          DATABASE       AUTH
              │             │
              │             │
        BooksContext    AuthContext
              │             │
       ┌──────┴──────┐      │
       │             │      │
   addBook()     getBooks() │
       │             │      │
       ↓             ↓      ↓
   CREATE          FETCH    LOGIN
       │             │      │
       └──────┬──────┘      │
              ↓             ↓
          React State    User State
              │             │
              └──────┬──────┘
                     ↓
                    UI
```

### The key idea from Video #24:

> **`listDocuments()` gets the records → `response.documents` gives you the records → `setBooks()` stores them in React state → React re-renders the UI.**

Once you understand that chain, **fetching data from a backend in React Native becomes much easier to understand.**

[1]: https://www.youtube.com/watch?v=NQiEaaTgWdQ&utm_source=chatgpt.com "Complete React Native Tutorial #24 - Fetching Book Records - YouTube"
[2]: https://tutohub.com/course/react-native-by-net-ninja?utm_source=chatgpt.com "React Native by Net Ninja"
