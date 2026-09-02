# 📱 React Native — Video #23 Notes

## Creating New Records

This is **Video #23 — “Creating New Records”** from Net Ninja’s Complete React Native course. It builds directly on **Video #21 (Appwrite Database Setup)** and **Video #22 (Books Context)**. ([YouTube][1])

The main goal is:

> **Take data entered in a form → send it to Appwrite → create a new book document in the database.**

---

# 1. 🎯 What We Are Building

In the previous video, we created the **Books Context** to centralize book-related data and functions.

Now we want the user to be able to add a new book.

The overall flow is:

```text
User fills form
      ↓
Press "Add Book"
      ↓
Books Context function
      ↓
Appwrite createDocument()
      ↓
New document created
      ↓
Book is stored in database
```

So instead of putting Appwrite database code directly inside the screen, we keep it inside **BooksContext**.

That keeps our application architecture clean.

---

# 2. 🗄️ Appwrite Database Structure

From Video #21, our structure is:

```text
Database
   ↓
Collection
   ↓
Documents
   ↓
Attributes
```

For example:

```text
Books Database
   │
   └── Books Collection
          │
          ├── Document 1
          │     ├── title
          │     ├── author
          │     ├── description
          │     └── rating
          │
          ├── Document 2
          │
          └── Document 3
```

When we create a new book, we're essentially creating:

```text
NEW DOCUMENT
```

inside the Books collection.

---

# 3. 🧠 What Is a Record?

In this context, a **record** means one document stored in the database.

For example:

```js
{
  title: "Atomic Habits",
  author: "James Clear",
  description: "A book about building good habits.",
  rating: 5
}
```

This object becomes the data stored in one Appwrite document.

Think:

```text
JavaScript object
       ↓
Appwrite document
```

---

# 4. 📦 `createDocument()`

The important Appwrite operation in this video is:

```js
databases.createDocument()
```

Its job is to create a new document inside a collection.

Conceptually:

```js
await databases.createDocument(...)
```

means:

> "Appwrite, create a new document using this data."

---

# 5. 🧩 Books Context

The database operation belongs in our `BooksContext`.

Previously we had something like:

```jsx
import { createContext, useState } from "react";

import { databases } from "../lib/appwrite";

export const BooksContext = createContext(null);

const BooksProvider = ({ children }) => {
  const [books, setBooks] = useState([]);

  const getBooks = async () => {
    // fetch books
  };

  return (
    <BooksContext.Provider value={{ books, getBooks }}>
      {children}
    </BooksContext.Provider>
  );
};

export default BooksProvider;
```

Now we're going to add a function for creating a book.

---

# 6. ✨ Creating `addBook()`

The basic idea is:

```jsx
const addBook = async (book) => {
  try {
    // create document
  } catch (error) {
    console.log("Add Book Error:", error);
  }
};
```

We pass the book data into the function:

```js
addBook({
  title,
  author,
  description,
  rating,
});
```

---

# 7. 🆔 Document IDs

Appwrite documents need an ID.

For a new document, we can generate a unique ID using:

```jsx
ID.unique()
```

So your Appwrite imports should include `ID`:

```jsx
import { ID } from "react-native-appwrite";
```

And your Appwrite library should already export it:

```jsx
export { ID };
```

Then:

```jsx
ID.unique()
```

generates a unique document ID.

---

# 8. 📝 Creating the Document

The important concept is:

```jsx
await databases.createDocument({
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
```

### What each part means

| Property       | Meaning                 |
| -------------- | ----------------------- |
| `databaseId`   | Which Appwrite database |
| `collectionId` | Which collection        |
| `documentId`   | ID for the new document |
| `data`         | Actual book information |

So:

```text
databaseId
    ↓
Which database?

collectionId
    ↓
Which collection?

documentId
    ↓
Which document ID?

data
    ↓
What information should be stored?
```

> **Note:** Appwrite SDK method signatures have changed across versions. The object-style form above matches current Appwrite SDK conventions; if your installed `react-native-appwrite` version differs from the tutorial, use the signature supported by that version.

---

# 9. 📚 Complete Books Context Example

A clean version of the context can look like this:

```jsx
import { createContext, useState } from "react";
import { ID } from "react-native-appwrite";

import { databases } from "../lib/appwrite";

export const BooksContext = createContext(null);

const DATABASE_ID = "your-database-id";
const COLLECTION_ID = "your-collection-id";

const BooksProvider = ({ children }) => {
  const [books, setBooks] = useState([]);

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

---

# 10. 🧠 Why `throw error`?

Notice:

```jsx
catch (error) {
  console.log("Add Book Error:", error);
  throw error;
}
```

Why throw it again?

Because the context catches the error, but the **screen may also need to know that something went wrong**.

For example:

```text
BooksContext
     ↓
Appwrite fails
     ↓
catch(error)
     ↓
throw error
     ↓
Add Book screen
     ↓
show error message
```

This is the same pattern we used with authentication.

---

# 11. 🖥️ Using `addBook()` in a Screen

Our screen can use the Books Context:

```jsx
import { useState } from "react";
import { Pressable, Text, TextInput, View } from "react-native";

import { useBooks } from "../hooks/useBooks";

const AddBook = () => {
  const { addBook } = useBooks();

  const [title, setTitle] = useState("");
  const [author, setAuthor] = useState("");
  const [description, setDescription] = useState("");
  const [rating, setRating] = useState("");

  const handleSubmit = async () => {
    try {
      await addBook({
        title,
        author,
        description,
        rating: Number(rating),
      });

      console.log("Book added!");
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <TextInput
        placeholder="Book title"
        value={title}
        onChangeText={setTitle}
      />

      <TextInput
        placeholder="Author"
        value={author}
        onChangeText={setAuthor}
      />

      <TextInput
        placeholder="Description"
        value={description}
        onChangeText={setDescription}
      />

      <TextInput
        placeholder="Rating"
        value={rating}
        onChangeText={setRating}
        keyboardType="numeric"
      />

      <Pressable onPress={handleSubmit}>
        <Text>Add Book</Text>
      </Pressable>
    </View>
  );
};

export default AddBook;
```

---

# 12. 🔄 Controlled Inputs

Notice this pattern:

```jsx
const [title, setTitle] = useState("");
```

and:

```jsx
<TextInput
  value={title}
  onChangeText={setTitle}
/>
```

This means React controls the input value.

The flow is:

```text
User types
   ↓
onChangeText
   ↓
setTitle()
   ↓
title state changes
   ↓
TextInput displays new value
```

This is called a **controlled input**.

You already saw this concept in Video #13 when creating the login/signup forms.

---

# 13. 🔢 Be Careful With Numbers

`TextInput` values are strings.

Even if the user types:

```text
5
```

React Native gives you:

```js
"5"
```

not:

```js
5
```

Therefore, if your Appwrite `rating` attribute is a number, convert it:

```js
Number(rating)
```

For example:

```js
rating: Number(rating)
```

Without conversion:

```js
rating: "5"
```

With conversion:

```js
rating: 5
```

This distinction is important when your Appwrite attribute is configured as an integer/number.

---

# 14. 📦 Passing Data to `addBook()`

Instead of doing this:

```js
addBook(title, author, description, rating);
```

you can pass one object:

```js
addBook({
  title,
  author,
  description,
  rating,
});
```

Then inside the context:

```js
const addBook = async (book) => {
  console.log(book.title);
  console.log(book.author);
};
```

This is useful because a book has multiple related properties.

---

# 15. 🧠 Why Put `addBook()` in the Context?

You could technically write:

```jsx
databases.createDocument(...)
```

directly inside your screen.

But that's not ideal.

Instead:

```text
               BooksContext
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
      getBooks()          addBook()
          │                   │
          └─────────┬─────────┘
                    ↓
                 Appwrite
```

Now every screen can use the same book functions.

For example:

```jsx
const { addBook } = useBooks();
```

or:

```jsx
const { books, getBooks } = useBooks();
```

This keeps database logic in one place.

---

# 16. 🔗 `useBooks()` Custom Hook

From Video #22:

```jsx
import { useContext } from "react";

import { BooksContext } from "../contexts/BooksContext";

export const useBooks = () => {
  return useContext(BooksContext);
};
```

Then your screen simply does:

```jsx
const { addBook } = useBooks();
```

Instead of:

```jsx
const context = useContext(BooksContext);
```

The custom hook makes consuming the context cleaner.

---

# 17. 🏗️ Overall Architecture

At this point your application is becoming much more structured:

```text
                React Native App
                       │
        ┌──────────────┴──────────────┐
        │                             │
   AuthContext                  BooksContext
        │                             │
        │                    ┌────────┴────────┐
        │                    │                 │
      login()             getBooks()        addBook()
      register()                              │
      logout()                                │
        │                                     │
        ↓                                     ↓
   Appwrite Account                    Appwrite Database
```

This is the important architectural lesson.

---

# 18. ⚠️ Database Permissions

One very important thing when creating documents is **permissions**.

Your Appwrite collection must allow the current user to create documents.

If your collection permissions don't allow the operation, the code can be completely correct but Appwrite will reject the request.

So if you get a permission-related error:

```text
Appwrite
   ↓
createDocument()
   ↓
❌ Permission denied
```

check your Appwrite database/collection permissions.

Also, don't make a production database broadly writable just to make development work. Permissions should be designed around your application's users and access rules.

---

# 19. 🧪 Example of What Happens

Suppose the user enters:

```text
Title:
Atomic Habits

Author:
James Clear

Description:
A book about habits.

Rating:
5
```

The screen creates:

```js
{
  title: "Atomic Habits",
  author: "James Clear",
  description: "A book about habits.",
  rating: 5
}
```

Then:

```js
addBook(book);
```

calls:

```js
databases.createDocument(...)
```

Appwrite creates:

```text
Books Collection
      │
      ├── existing book
      ├── existing book
      └── Atomic Habits ← NEW
```

---

# 20. 🔄 Complete Flow to Remember

```text
                 ADD BOOK SCREEN
                       │
                       │ user enters data
                       ↓
                React useState
                       │
                       │ handleSubmit()
                       ↓
                  addBook(book)
                       │
                       ↓
                BooksContext
                       │
                       ↓
           databases.createDocument()
                       │
                       ↓
                 Appwrite
                       │
                       ↓
              Books Collection
                       │
                       ↓
               NEW DOCUMENT
```

This is the key concept of Video #23.

---

# ⭐ Video #23 — Must Remember

### 1. `createDocument()`

Used to create a new Appwrite database document.

```js
databases.createDocument(...)
```

### 2. `ID.unique()`

Generates a unique document ID:

```js
documentId: ID.unique()
```

### 3. `data`

Contains the actual record:

```js
data: {
  title,
  author,
  description,
  rating,
}
```

### 4. Keep database logic in Context

Prefer:

```text
Screen
  ↓
BooksContext
  ↓
Appwrite
```

rather than:

```text
Screen
  ↓
Appwrite
```

### 5. `TextInput` gives strings

Convert numeric input when necessary:

```js
Number(rating)
```

### 6. Throw errors back to the screen

```js
catch (error) {
  console.log(error);
  throw error;
}
```

This allows the UI to display an error.

### 7. Permissions matter

A valid `createDocument()` call can still fail if the Appwrite collection doesn't allow the user to create documents.

---

# 🧠 Final Mental Model

Think of **Video #23** as teaching you how to move from:

```text
FORM
```

to:

```text
DATABASE
```

The important chain is:

```text
TextInput
   ↓
useState
   ↓
handleSubmit()
   ↓
addBook()
   ↓
BooksContext
   ↓
createDocument()
   ↓
Appwrite
   ↓
New Book Document
```

And the architectural principle is:

> **Screens collect user input. Contexts handle application/data logic. Appwrite stores the data.**

That separation will become especially important in **Video #24 — Fetching Book Records**, where you'll retrieve those stored books back from Appwrite. The course sequence confirms that #24 follows #23 directly. ([TutoHub][2])

[1]: https://www.youtube.com/watch?v=xh7-JVZmM6w&utm_source=chatgpt.com "Complete React Native Tutorial #23 - Creating New Records - YouTube"
[2]: https://tutohub.com/course/react-native-by-net-ninja?utm_source=chatgpt.com "React Native by Net Ninja"
