# 📱 React Native — Video #26 Notes

## Real-Time Data

This video introduces **real-time database updates with Appwrite**.

Up to Video #25, our app only fetched books when we explicitly called `getBooks()`.

Now we want something better:

> **When data changes in Appwrite, the app should know about that change and update the UI automatically.**

---

# 1. 🎯 The Problem With Normal Fetching

Our previous flow was:

```text
Screen opens
    ↓
getBooks()
    ↓
Appwrite
    ↓
listDocuments()
    ↓
setBooks()
    ↓
FlatList displays books
```

This works, but imagine another user adds a book to the database.

Our app won't automatically know.

The app would still have:

```text
books = [Book 1, Book 2]
```

even though Appwrite now contains:

```text
Book 1
Book 2
Book 3 ← newly added
```

We would have to call:

```jsx
getBooks();
```

again.

---

# 2. ⚡ What Is Real-Time Data?

**Real-time data** means the application can receive information about changes as they happen, instead of repeatedly asking the server for the latest data.

Instead of:

```text
App → "Any changes?"
Server → "No."
App → "Any changes?"
Server → "No."
App → "Any changes?"
Server → "Yes, a book was added."
```

we can establish a real-time subscription:

```text
App
 │
 │ subscribe
 ↓
Appwrite
 │
 │ change happens
 ↓
App
 │
 ↓
Update state
 │
 ↓
UI updates
```

This is called a **subscription**.

---

# 3. 🔌 Appwrite Realtime

Appwrite provides a realtime service through the client.

The important method is:

```jsx
client.subscribe()
```

So we need access to the `client` that we created in our Appwrite setup.

Our `appwrite.js` already has:

```jsx
import { Client } from "react-native-appwrite";

export const client = new Client();
```

So we can export and use that client.

---

# 4. 📡 What Does `subscribe()` Do?

Conceptually:

```jsx
const unsubscribe = client.subscribe(
  "some-channel",
  (response) => {
    // something changed
  }
);
```

The subscription listens for changes.

When the subscribed data changes, Appwrite sends an event to our application.

For example:

```text
Database
   ↓
Book created
   ↓
Realtime event
   ↓
client.subscribe()
   ↓
Our callback runs
```

---

# 5. 🧠 Why Do We Need This in `BooksContext`?

Remember our architecture:

```text
BooksContext
    │
    ├── books
    ├── getBooks()
    └── addBook()
```

Real-time book updates are also **book/data logic**.

So it makes sense to put the subscription in `BooksContext`.

Then the screens don't need to know anything about Appwrite realtime.

The screen simply does:

```jsx
const { books } = useBooks();
```

and receives the updated state.

---

# 6. 🏗️ The New Architecture

Before:

```text
Appwrite
   ↓
getBooks()
   ↓
BooksContext
   ↓
books
   ↓
FlatList
```

After adding realtime:

```text
                    Appwrite
                       │
              ┌────────┴────────┐
              ↓                 ↓
        getBooks()        Realtime events
              │                 │
              └────────┬────────┘
                       ↓
                 BooksContext
                       ↓
                    books
                       ↓
                   FlatList
```

This is the major idea of Video #26.

---

# 7. 🔄 Using `useEffect` for the Subscription

A subscription is something that should be created when the component/provider starts and cleaned up when it goes away.

Therefore, we use:

```jsx
useEffect()
```

For example:

```jsx
useEffect(() => {
  const unsubscribe = client.subscribe(
    "databases.DATABASE_ID.collections.COLLECTION_ID.documents",
    (response) => {
      console.log(response);
    }
  );

  return () => {
    unsubscribe();
  };
}, []);
```

There are two important parts here:

```text
Subscribe
    ↓
Listen for changes

Cleanup
    ↓
Unsubscribe when no longer needed
```

---

# 8. 🧹 Why Do We Need to Unsubscribe?

This is extremely important.

When we call:

```jsx
client.subscribe(...)
```

we establish a listener.

If we don't remove it, the listener can continue running even when we no longer need it.

That can lead to:

* unnecessary network activity
* multiple callbacks
* duplicated updates
* memory/resource issues

So we return a cleanup function:

```jsx
return () => {
  unsubscribe();
};
```

Think:

```text
Component starts
     ↓
Subscribe

Component stops
     ↓
Unsubscribe
```

This is the standard React pattern for subscriptions.

---

# 9. 🧩 Understanding the Channel

Appwrite realtime uses **channels** to specify what you want to listen to.

For a particular collection, the channel conceptually looks like:

```text
databases.{databaseId}.collections.{collectionId}.documents
```

For example:

```jsx
const channel =
  `databases.${DATABASE_ID}.collections.${COLLECTION_ID}.documents`;
```

Then:

```jsx
client.subscribe(channel, (response) => {
  console.log(response);
});
```

This means:

> Listen for document changes in this collection.

---

# 10. 📢 What Does the Callback Receive?

When an event happens, Appwrite sends information to the callback:

```jsx
client.subscribe(channel, (response) => {
  console.log(response);
});
```

The response contains information about the event.

For example, the event can tell us that a document was:

```text
created
updated
deleted
```

This is where the application can decide what to do with the changed data.

---

# 11. 🆕 Book Created

Suppose our database currently contains:

```text
Book A
Book B
```

Our app has:

```js
books = [Book A, Book B]
```

Someone creates:

```text
Book C
```

Appwrite generates a realtime event.

```text
Book C created
      ↓
Appwrite Realtime
      ↓
subscription callback
      ↓
update books
      ↓
React re-renders
      ↓
FlatList shows Book C
```

No manual refresh is necessary.

---

# 12. ✏️ Book Updated

The same concept works when a document changes.

For example:

```text
Rating:
4 → 5
```

Appwrite sends an update event.

```text
Database document updated
          ↓
Realtime event
          ↓
BooksContext
          ↓
books state updated
          ↓
FlatList re-renders
```

The user sees the updated value.

---

# 13. 🗑️ Book Deleted

Realtime can also notify the app when a document is deleted.

```text
Book deleted from Appwrite
          ↓
Realtime event
          ↓
BooksContext
          ↓
Remove book from state
          ↓
FlatList updates
```

So realtime isn't just about **new records**.

It's about keeping the local UI synchronized with backend changes.

---

# 14. 🧠 Two Different Jobs

It's important not to confuse:

### `getBooks()`

Used for the **initial fetch**.

```jsx
getBooks();
```

It asks:

> "Give me the books that currently exist."

### `subscribe()`

Used for **future changes**.

```jsx
client.subscribe(...);
```

It says:

> "Tell me when something changes."

Therefore we often need **both**:

```text
Initial state
    ↓
getBooks()
    ↓
Current books

Then
    ↓
subscribe()
    ↓
Future changes
```

---

# 15. 🔄 Complete Initialization Flow

When the Books Context starts:

```text
BooksContext mounts
       ↓
getBooks()
       ↓
Fetch existing books
       ↓
setBooks()
       ↓
UI displays current books

       +

Subscribe to realtime
       ↓
Wait for changes
       ↓
Database changes
       ↓
Realtime event
       ↓
Update books
       ↓
UI updates
```

This is the mental model you should remember.

---

# 16. 🧪 Example Books Context

A simplified structure could look like:

```jsx
import { createContext, useEffect, useState } from "react";

import { client, databases } from "../lib/appwrite";

export const BooksContext = createContext(null);

const DATABASE_ID = "your-database-id";
const COLLECTION_ID = "your-collection-id";

const BooksProvider = ({ children }) => {
  const [books, setBooks] = useState([]);

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

  useEffect(() => {
    getBooks();

    const channel =
      `databases.${DATABASE_ID}.collections.${COLLECTION_ID}.documents`;

    const unsubscribe = client.subscribe(channel, (response) => {
      console.log("Realtime event:", response);
    });

    return () => {
      unsubscribe();
    };
  }, []);

  return (
    <BooksContext.Provider value={{ books, getBooks }}>
      {children}
    </BooksContext.Provider>
  );
};

export default BooksProvider;
```

The important thing here is the **pattern**, not blindly copying every line.

---

# 17. ⚠️ Important: Realtime Doesn't Automatically Update Your State

One subtle but important point:

Simply doing:

```jsx
client.subscribe(channel, (response) => {
  console.log(response);
});
```

does **not** automatically modify:

```jsx
books
```

You still need to decide what to do with the event.

For example, a simple strategy is:

```jsx
client.subscribe(channel, () => {
  getBooks();
});
```

Then:

```text
Database changes
      ↓
Realtime event
      ↓
getBooks()
      ↓
listDocuments()
      ↓
setBooks()
      ↓
UI updates
```

This is easy to understand and guarantees that the local list reflects the backend.

A more optimized approach is to inspect the event and update `books` locally rather than fetching the entire collection again.

---

# 18. 🚀 Two Ways to Handle Realtime Events

### Approach 1 — Refetch

```jsx
client.subscribe(channel, () => {
  getBooks();
});
```

Flow:

```text
Change
 ↓
Realtime event
 ↓
Fetch entire collection
 ↓
Replace books state
```

**Pros:**

* simple
* easy to understand
* keeps state synchronized

**Cons:**

* makes another database request
* potentially inefficient for large datasets

---

### Approach 2 — Update State Directly

For example, if the event tells you a document was created:

```jsx
setBooks((currentBooks) => [
  ...currentBooks,
  newBook,
]);
```

Flow:

```text
Change
 ↓
Realtime event
 ↓
Read event data
 ↓
Update local state
```

This can be more efficient, but requires more careful handling of:

* create events
* update events
* delete events
* duplicate events
* ordering

For learning purposes, understanding the **refetch approach first** is useful.

---

# 19. 🧠 Why Functional State Updates Matter

When updating state based on the previous state, prefer:

```jsx
setBooks((currentBooks) => [
  ...currentBooks,
  newBook,
]);
```

instead of relying on a potentially stale `books` value:

```jsx
setBooks([...books, newBook]);
```

The functional form:

```jsx
setBooks((currentBooks) => ...)
```

explicitly gives you the latest state.

This becomes particularly useful when realtime events can arrive quickly.

---

# 20. 📱 What Happens to `FlatList`?

The great thing is that **we don't need to change our FlatList**.

Suppose it already has:

```jsx
<FlatList
  data={books}
  keyExtractor={(item) => item.$id}
  renderItem={({ item }) => (
    <Text>{item.title}</Text>
  )}
/>
```

When:

```jsx
setBooks(...)
```

changes the state:

```text
books changes
    ↓
Books component re-renders
    ↓
FlatList receives new data
    ↓
FlatList updates
```

So realtime is essentially feeding updated data into the same UI.

---

# 21. 🔗 The Connection Between Videos 24–26

These three videos form one important concept.

### Video #24 — Fetching

```text
listDocuments()
```

Gets existing records.

### Video #25 — FlatList

```text
books → FlatList
```

Displays those records efficiently.

### Video #26 — Real-Time

```text
subscribe()
```

Keeps those records synchronized with future database changes.

So:

```text
        Appwrite
           │
    ┌──────┴──────┐
    ↓             ↓
listDocuments   subscribe
    ↓             ↓
initial data   future changes
    │             │
    └──────┬──────┘
           ↓
      books state
           ↓
        FlatList
           ↓
           UI
```

---

# 22. ⚠️ Common Mistakes

### ❌ Forgetting the cleanup

```jsx
useEffect(() => {
  client.subscribe(...);
}, []);
```

Better:

```jsx
useEffect(() => {
  const unsubscribe = client.subscribe(...);

  return () => {
    unsubscribe();
  };
}, []);
```

---

### ❌ Creating subscriptions during render

Don't do:

```jsx
const Books = () => {
  client.subscribe(...); // ❌
  
  return (...);
};
```

Use an effect.

---

### ❌ Forgetting the correct channel

Your subscription needs to target the database/collection you actually want to monitor.

---

### ❌ Assuming `subscribe()` changes React state automatically

It doesn't.

You receive an event and then decide how to update your state.

---

# ⭐ Video #26 — Must Remember

### 1. Real-time data

Allows the app to respond to backend changes without requiring a manual refresh.

### 2. Appwrite `client.subscribe()`

Used to listen for realtime events:

```jsx
client.subscribe(channel, callback);
```

### 3. `useEffect`

Subscriptions belong in an effect:

```jsx
useEffect(() => {
  // subscribe
}, []);
```

### 4. Always clean up

```jsx
return () => {
  unsubscribe();
};
```

### 5. `getBooks()` and `subscribe()` have different jobs

```text
getBooks()
    ↓
Initial data

subscribe()
    ↓
Future changes
```

### 6. Realtime events can represent

```text
CREATE
UPDATE
DELETE
```

### 7. Realtime belongs naturally in `BooksContext`

Because the context already owns:

```text
books
getBooks()
addBook()
```

and therefore should also own the synchronization logic.

---

# 🧠 Final Mental Model

The biggest thing to understand from this video is that **fetching and realtime are not the same thing**.

Think of it like this:

```text
             APPWRITE DATABASE
                    │
          ┌─────────┴─────────┐
          │                   │
          │                   │
   listDocuments()       subscribe()
          │                   │
          ↓                   ↓
   "What exists now?"   "Tell me what changes"
          │                   │
          └─────────┬─────────┘
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
                    UI
```

### 🔑 One sentence to remember:

> **`listDocuments()` gives you the current data, while `client.subscribe()` lets your app react to future changes in real time.**

And the complete Shelfie flow you've learned so far is:

```text
CREATE
addBook()
   ↓
Appwrite

FETCH
getBooks()
   ↓
listDocuments()
   ↓
books

DISPLAY
books
   ↓
FlatList

REAL-TIME
subscribe()
   ↓
database changes
   ↓
update/refetch books
   ↓
FlatList updates
```

That's the core of **Video #26 — Real-Time Data**.
