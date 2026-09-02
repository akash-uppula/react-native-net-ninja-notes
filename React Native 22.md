# 📱 React Native — Video #22 Notes

## Complete React Native Tutorial #22 — Books Context

Video #22 introduces a **Books Context** so that book-related data and functions can be shared across different screens without passing props everywhere.

This builds on:

* **Video #14:** Auth Context
* **Video #18:** Initial Auth State
* **Video #21:** Appwrite Database

The idea is to create a dedicated context for **books**.

---

# 1. Why Do We Need a Books Context?

Imagine you have:

```text
Home
Books
Profile
Book Details
```

and multiple screens need access to the books.

Without Context, you might have to pass data through props:

```text
App
 ↓
Home
 ↓
Books
 ↓
BookCard
```

This can become difficult to manage.

Instead, we can create:

```text
BooksContext
      ↓
  All book-related screens
```

Then any screen can access the books directly.

---

# 2. What Is Context?

React Context allows us to make data available to multiple components without manually passing it through props.

You already did this with authentication:

```jsx
<AuthContext.Provider value={value}>
  {children}
</AuthContext.Provider>
```

Now we're doing the same thing for books:

```jsx
<BooksContext.Provider value={value}>
  {children}
</BooksContext.Provider>
```

---

# 3. Create `BooksContext`

Create a context file, for example:

```text
contexts/
├── AuthContext.jsx
└── BooksContext.jsx
```

Then:

```jsx
import { createContext } from "react";

export const BooksContext = createContext(null);
```

Just like:

```jsx
export const AuthContext = createContext(null);
```

---

# 4. Create `BooksProvider`

The provider will contain the book-related state and functions.

```jsx
import { createContext, useState } from "react";

export const BooksContext = createContext(null);

const BooksProvider = ({ children }) => {
  const [books, setBooks] = useState([]);

  return (
    <BooksContext.Provider value={{ books }}>
      {children}
    </BooksContext.Provider>
  );
};

export default BooksProvider;
```

Now:

```text
BooksProvider
     ↓
   books
     ↓
Any child component
```

---

# 5. Why `useState([])`?

We start with an empty array:

```jsx
const [books, setBooks] = useState([]);
```

because eventually we'll retrieve multiple books from Appwrite.

Initially:

```text
books = []
```

After fetching:

```text
books = [
  book1,
  book2,
  book3
]
```

---

# 6. Add a `getBooks()` Function

The context shouldn't only contain the data.

It can also contain the functions that work with that data.

For example:

```jsx
const getBooks = async () => {
  // Get books from Appwrite
};
```

Eventually:

```text
BooksContext
│
├── books
├── getBooks()
├── createBook()
├── updateBook()
└── deleteBook()
```

This gives us one central place for book-related logic.

---

# 7. Import Appwrite `databases`

From Video #21, we created:

```jsx
export const databases = new Databases(client);
```

Now we can import it:

```jsx
import { databases } from "../lib/appwrite";
```

So the Books Context can communicate with the Appwrite database.

---

# 8. Database Constants

We also need to tell Appwrite:

> Which database and collection should I use?

For example:

```jsx
const DATABASE_ID = "your-database-id";
const COLLECTION_ID = "your-collection-id";
```

These are the IDs you created in the Appwrite Console.

Conceptually:

```text
Appwrite
   ↓
Database ID
   ↓
Books Collection ID
   ↓
Books Documents
```

---

# 9. Fetching Books

The basic idea of `getBooks()` is:

```jsx
const getBooks = async () => {
  try {
    const response = await databases.listDocuments(
      DATABASE_ID,
      COLLECTION_ID
    );

    setBooks(response.documents);
  } catch (error) {
    console.log(error);
  }
};
```

The important Appwrite method is:

```jsx
databases.listDocuments(...)
```

It retrieves documents from a collection.

---

# 10. Understand `listDocuments()`

Conceptually:

```jsx
databases.listDocuments(
  DATABASE_ID,
  COLLECTION_ID
);
```

means:

```text
Go to this database
       ↓
Go to this collection
       ↓
Give me its documents
```

The response contains information about the documents.

Most importantly:

```jsx
response.documents
```

contains the actual documents.

---

# 11. Store the Documents

Once we receive the documents:

```jsx
setBooks(response.documents);
```

Our state changes:

```text
Before:

books = []


After Appwrite request:

books = [
  document1,
  document2,
  document3
]
```

React then re-renders components using `books`.

---

# 12. Complete Basic Books Context

A basic version looks like:

```jsx
import { createContext, useState } from "react";

import { databases } from "../lib/appwrite";

export const BooksContext = createContext(null);

const DATABASE_ID = "your-database-id";
const COLLECTION_ID = "your-collection-id";

const BooksProvider = ({ children }) => {
  const [books, setBooks] = useState([]);

  const getBooks = async () => {
    try {
      const response = await databases.listDocuments(
        DATABASE_ID,
        COLLECTION_ID
      );

      setBooks(response.documents);
    } catch (error) {
      console.log("Get Books Error:", error);
    }
  };

  const value = {
    books,
    getBooks,
  };

  return (
    <BooksContext.Provider value={value}>
      {children}
    </BooksContext.Provider>
  );
};

export default BooksProvider;
```

---

# 13. Books Context vs Auth Context

You now have two different contexts.

### Auth Context

Responsible for:

```text
User
Login
Register
Logout
Authentication
```

### Books Context

Responsible for:

```text
Books
Get books
Create books
Update books
Delete books
```

Keep these responsibilities separate.

```text
AuthContext
     ↓
Authentication

BooksContext
     ↓
Book data
```

---

# 14. Why Separate Contexts?

Don't put everything into one giant context:

```text
AppContext
├── user
├── login
├── logout
├── books
├── getBooks
├── createBook
├── updateBook
├── deleteBook
├── ...
```

As the application grows, that becomes difficult to maintain.

Instead:

```text
AuthContext
     ↓
Authentication

BooksContext
     ↓
Books

OtherContext
     ↓
Other feature
```

This is a cleaner architecture.

---

# 15. Create `useBooks` Hook

Just like your existing:

```jsx
const { login } = useAuth();
```

you'll want:

```jsx
const { books, getBooks } = useBooks();
```

Create:

```text
hooks/
├── useAuth.js
└── useBooks.js
```

Then:

```jsx
import { useContext } from "react";

import { BooksContext } from "../contexts/BooksContext";

export const useBooks = () => {
  return useContext(BooksContext);
};
```

Now components don't need to directly use:

```jsx
useContext(BooksContext)
```

every time.

Instead:

```jsx
const { books, getBooks } = useBooks();
```

---

# 16. Why Custom Hooks?

Your existing authentication pattern is:

```jsx
const { user, login, register, logout } = useAuth();
```

Books should follow the same pattern:

```jsx
const { books, getBooks } = useBooks();
```

This makes your code much cleaner.

---

# 17. Add `BooksProvider`

The context only works for components **inside the provider**.

For example:

```jsx
<BooksProvider>
  <Stack />
</BooksProvider>
```

Then:

```text
BooksProvider
     ↓
    Stack
     ↓
  Screens
     ↓
useBooks()
```

If a screen is outside the provider, it won't have access to the Books Context.

---

# 18. AuthProvider + BooksProvider

Your application can now have:

```jsx
<AuthProvider>
  <BooksProvider>
    <Stack />
  </BooksProvider>
</AuthProvider>
```

The structure is:

```text
AuthProvider
      ↓
BooksProvider
      ↓
   Navigation
      ↓
    Screens
```

This means:

```text
Screens
  ↓
useAuth()  → AuthContext

Screens
  ↓
useBooks() → BooksContext
```

---

# 19. Why BooksProvider Can Be Inside AuthProvider

This becomes useful because book data may eventually depend on the authenticated user.

For example:

```text
Current User
     ↓
User ID
     ↓
Books belonging to user
```

So:

```text
AuthProvider
    ↓
knows user
    ↓
BooksProvider
    ↓
can use authentication information
```

This relationship will become more important as your app develops.

---

# 20. Getting Books From a Screen

Once the provider is configured:

```jsx
const { books, getBooks } = useBooks();
```

You can call:

```jsx
await getBooks();
```

Then:

```jsx
books
```

contains the documents retrieved from Appwrite.

For example:

```jsx
console.log(books);
```

could give you:

```text
[
  {
    $id: "...",
    title: "Atomic Habits",
    author: "James Clear"
  },
  {
    $id: "...",
    title: "The Alchemist",
    author: "Paulo Coelho"
  }
]
```

---

# 21. Context Data Flow

The complete flow is:

```text
             Appwrite
                ↓
        databases.listDocuments()
                ↓
           getBooks()
                ↓
        setBooks(documents)
                ↓
          BooksContext
                ↓
           useBooks()
                ↓
             Screen
                ↓
          Display books
```

This is the important concept of the video.

---

# 22. Context Centralizes API Logic

Without Books Context, you might have:

```jsx
const getBooks = async () => {
  // Appwrite code
};
```

inside several screens.

That's not good.

Instead:

```text
BooksContext
     ↓
getBooks()
     ↓
Appwrite
```

Every screen uses the same function:

```jsx
const { getBooks } = useBooks();
```

---

# 23. One Place for Database Logic

Eventually, your Books Context can become:

```jsx
const value = {
  books,
  getBooks,
  getBook,
  createBook,
  updateBook,
  deleteBook,
};
```

So your screen doesn't need to know the Appwrite implementation.

It only says:

```jsx
await createBook(bookData);
```

This separation makes the application easier to maintain.

---

# 24. Books Context Architecture

Think of it like this:

```text
                 BooksProvider
                      │
             ┌────────┴────────┐
             ↓                 ↓
          State             Functions
             ↓                 ↓
           books          getBooks()
                          createBook()
                          updateBook()
                          deleteBook()
             │                 │
             └────────┬────────┘
                      ↓
                  Appwrite
                      ↓
                  Database
```

---

# ⭐ Video #22 — Must Remember

### 1. Create a context

```jsx
export const BooksContext = createContext(null);
```

### 2. Create state

```jsx
const [books, setBooks] = useState([]);
```

### 3. Connect to Appwrite

```jsx
import { databases } from "../lib/appwrite";
```

### 4. Get documents

```jsx
const response = await databases.listDocuments(
  DATABASE_ID,
  COLLECTION_ID
);
```

### 5. Store documents

```jsx
setBooks(response.documents);
```

### 6. Provide the data

```jsx
<BooksContext.Provider value={value}>
  {children}
</BooksContext.Provider>
```

### 7. Create a custom hook

```jsx
export const useBooks = () => {
  return useContext(BooksContext);
};
```

### 8. Use it inside components

```jsx
const { books, getBooks } = useBooks();
```

---

# 🧠 Final Mental Model

Video #22 is basically teaching you to create a **central place for all book-related state and database operations**.

```text
                    App
                     ↓
              AuthProvider
                     ↓
              BooksProvider
                     ↓
              BooksContext
                     ↓
          ┌──────────┴──────────┐
          ↓                     ↓
        books               getBooks()
                                ↓
                           Appwrite
                                ↓
                           Database
```

And screens simply consume the context:

```jsx
const { books, getBooks } = useBooks();
```

instead of directly managing Appwrite database requests themselves.

### 🔑 Core lesson

> **AuthContext manages authentication. BooksContext manages book data.**

This separation will make the next videos—where we actually **fetch, display, create, update, and delete books**—much easier to understand.
