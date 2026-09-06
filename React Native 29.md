# 📱 React Native — Video #29 Notes

## **Deleting Books**

This is the **final video (#29)** of Net Ninja's *Complete React Native* course. The course sequence ends with deleting a specific book record from Appwrite. ([YouTube][1])

The important thing is that this video brings together several things you've already learned:

```text
Dynamic Route
     ↓
Book ID
     ↓
Single Book
     ↓
Delete that document
     ↓
Update the UI
```

---

# 1. 🎯 What Are We Learning?

In Video #28, we learned how to fetch **one specific book**:

```jsx
databases.getDocument({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
  documentId: id,
});
```

Now we want to delete that same book.

Appwrite provides:

```jsx
databases.deleteDocument()
```

So the overall flow becomes:

```text
/books/[id]
     ↓
useLocalSearchParams()
     ↓
id
     ↓
deleteDocument()
     ↓
Appwrite
     ↓
Book deleted
```

---

# 2. 🗑️ `deleteDocument()`

The key Appwrite method is:

```jsx
await databases.deleteDocument({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
  documentId: id,
});
```

There are **three important identifiers**:

### `databaseId`

Which Appwrite database?

```jsx
databaseId: DATABASE_ID
```

### `collectionId`

Which collection?

```jsx
collectionId: COLLECTION_ID
```

### `documentId`

Which specific document?

```jsx
documentId: id
```

The final one is the important part for deleting a particular book.

---

# 3. 📍 Where Does `id` Come From?

Remember Video #27:

```jsx
router.push(`/books/${item.$id}`);
```

Suppose:

```text
item.$id = "abc123"
```

Then:

```text
/books/abc123
```

Our dynamic route:

```text
books/[id].jsx
```

receives:

```jsx
import { useLocalSearchParams } from "expo-router";

const { id } = useLocalSearchParams();
```

Therefore:

```text
item.$id
   ↓
/books/abc123
   ↓
[id].jsx
   ↓
id = "abc123"
```

And that same ID can be given to Appwrite:

```jsx
documentId: id
```

---

# 4. 🧩 Create a `deleteBook()` Function

The deletion logic should live in your **BooksContext**, just like `addBook()` and `getBooks()`.

For example:

```jsx
const deleteBook = async (id) => {
  try {
    await databases.deleteDocument({
      databaseId: DATABASE_ID,
      collectionId: COLLECTION_ID,
      documentId: id,
    });
  } catch (error) {
    console.log("Delete Book Error:", error);
    throw error;
  }
};
```

Notice:

```jsx
const deleteBook = async (id) => {
```

We're passing the book's ID into the function.

Then:

```jsx
documentId: id
```

tells Appwrite exactly which document to delete.

---

# 5. 🏗️ Why Put This in `BooksContext`?

Your screen shouldn't need to know all the details of Appwrite.

Instead:

```text
Book Details Screen
       ↓
deleteBook(id)
       ↓
BooksContext
       ↓
Appwrite
```

This follows the same architecture you've been using throughout the course.

### Creating

```text
Screen
 ↓
addBook()
 ↓
BooksContext
 ↓
Appwrite
```

### Fetching

```text
Screen
 ↓
getBooks()
 ↓
BooksContext
 ↓
Appwrite
```

### Deleting

```text
Screen
 ↓
deleteBook()
 ↓
BooksContext
 ↓
Appwrite
```

The Context becomes the place where your book-related backend operations live.

---

# 6. 📦 BooksContext

Your context can now contain:

```jsx
const BooksContext = createContext(null);

const BooksProvider = ({ children }) => {
  const [books, setBooks] = useState([]);

  const getBooks = async () => {
    // fetch all books
  };

  const addBook = async (book) => {
    // create book
  };

  const deleteBook = async (id) => {
    try {
      await databases.deleteDocument({
        databaseId: DATABASE_ID,
        collectionId: COLLECTION_ID,
        documentId: id,
      });
    } catch (error) {
      console.log("Delete Book Error:", error);
      throw error;
    }
  };

  return (
    <BooksContext.Provider
      value={{
        books,
        getBooks,
        addBook,
        deleteBook,
      }}
    >
      {children}
    </BooksContext.Provider>
  );
};

export default BooksProvider;
```

The important addition is:

```jsx
deleteBook
```

inside the Context value.

---

# 7. 📲 Use `deleteBook()` in the Details Screen

In your `[id].jsx` screen:

```jsx
import { useLocalSearchParams, router } from "expo-router";

import { useBooks } from "../../hooks/useBooks";

const BookDetails = () => {
  const { id } = useLocalSearchParams();

  const { deleteBook } = useBooks();

  // ...
};
```

Then you can connect it to a button:

```jsx
<Pressable onPress={() => deleteBook(id)}>
  <Text>Delete Book</Text>
</Pressable>
```

But there is one more important thing.

After deleting the book, the user shouldn't remain on a screen displaying a book that no longer exists.

---

# 8. ↩️ Navigate Away After Deletion

You can delete and then navigate back:

```jsx
const handleDelete = async () => {
  try {
    await deleteBook(id);

    router.back();
  } catch (error) {
    console.log(error);
  }
};
```

Then:

```jsx
<Pressable onPress={handleDelete}>
  <Text>Delete Book</Text>
</Pressable>
```

The sequence becomes:

```text
User presses Delete
       ↓
handleDelete()
       ↓
deleteBook(id)
       ↓
Appwrite deletes document
       ↓
router.back()
       ↓
User returns to book list
```

---

# 9. ⚠️ Don't Navigate Before Deletion Finishes

Avoid:

```jsx
router.back();

await deleteBook(id);
```

Because you're navigating away **before knowing whether deletion succeeded**.

Instead:

```jsx
await deleteBook(id);

router.back();
```

This means:

> Delete successfully first → then navigate.

This is especially important when dealing with asynchronous operations.

---

# 10. ⏳ Add a Loading State

Deletion is an asynchronous operation, so it's useful to prevent the user from pressing the button multiple times.

```jsx
const [loading, setLoading] = useState(false);
```

Then:

```jsx
const handleDelete = async () => {
  try {
    setLoading(true);

    await deleteBook(id);

    router.back();
  } catch (error) {
    console.log("Delete Error:", error);
  } finally {
    setLoading(false);
  }
};
```

Button:

```jsx
<Pressable
  onPress={handleDelete}
  disabled={loading}
>
  {loading ? (
    <ActivityIndicator />
  ) : (
    <Text>Delete Book</Text>
  )}
</Pressable>
```

Now the user can't repeatedly trigger the deletion while the first request is still running.

---

# 11. 🛑 Confirmation Before Deleting

Deletion is destructive.

A good app shouldn't immediately delete something just because the user accidentally tapped a button.

A confirmation dialog is a better UX.

React Native provides `Alert`:

```jsx
import { Alert } from "react-native";
```

Then:

```jsx
const confirmDelete = () => {
  Alert.alert(
    "Delete Book",
    "Are you sure you want to delete this book?",
    [
      {
        text: "Cancel",
        style: "cancel",
      },
      {
        text: "Delete",
        style: "destructive",
        onPress: handleDelete,
      },
    ]
  );
};
```

Then:

```jsx
<Pressable onPress={confirmDelete}>
  <Text>Delete Book</Text>
</Pressable>
```

The flow becomes:

```text
Delete button
      ↓
Confirmation dialog
      ↓
   ┌───────┐
   │       │
Cancel   Delete
           ↓
      handleDelete()
           ↓
      deleteBook()
           ↓
        Appwrite
```

---

# 12. 🔄 What Happens to the List?

This connects directly to **Video #26 — Real-Time Data**.

Suppose your list currently contains:

```text
Atomic Habits
Clean Code
Deep Work
```

You open:

```text
/books/abc123
```

and delete:

```text
Clean Code
```

Appwrite removes the document.

If your realtime subscription is correctly handling delete events, the list can automatically update:

```text
Atomic Habits
Deep Work
```

The important concept is:

```text
Appwrite changes
       ↓
Realtime event
       ↓
BooksContext
       ↓
books state
       ↓
FlatList re-renders
```

So the final videos connect together very nicely.

---

# 13. 🧠 Delete vs Remove From React State

There are actually **two different things** you could mean by "delete."

### Delete from Appwrite

```jsx
await databases.deleteDocument({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
  documentId: id,
});
```

This permanently removes the backend document.

### Remove from React state

You could also do:

```jsx
setBooks((currentBooks) =>
  currentBooks.filter((book) => book.$id !== id)
);
```

This removes the book from the current UI state.

But:

```jsx
setBooks(...)
```

**doesn't delete the Appwrite document.**

It only changes what the current app instance displays.

Therefore:

```text
setBooks(...)
       ↓
UI changes

deleteDocument(...)
       ↓
Backend data changes
```

This distinction is extremely important.

---

# 14. 🧩 Why Realtime Helps Here

Suppose you delete a book through:

```jsx
deleteDocument()
```

Appwrite knows the document was deleted.

Your realtime listener can receive a delete event.

Then your `BooksContext` can update:

```jsx
setBooks((currentBooks) =>
  currentBooks.filter((book) => book.$id !== deletedId)
);
```

So you don't necessarily need to fetch the entire collection again.

Conceptually:

```text
BEFORE

Appwrite:
A
B ← delete
C

React:
A
B
C


DELETE B


AFTER

Appwrite:
A
C

React:
A
C
```

---

# 15. 🔐 Permissions Matter

Just like creating documents, deleting documents depends on **Appwrite permissions**.

Your app may correctly call:

```jsx
databases.deleteDocument(...)
```

but Appwrite can still reject the request if the current user doesn't have permission to delete that document.

So if you see a permission error, don't immediately assume your JavaScript is wrong.

Check:

```text
Appwrite
 ↓
Database
 ↓
Collection
 ↓
Document permissions
```

This becomes particularly important in a multi-user application.

For example, you generally don't want:

```text
User A
   ↓
delete
   ↓
User B's book
```

unless your application's permissions intentionally allow it.

---

# 16. 🧱 CRUD — You Now Know the Full Cycle

This course has effectively taught you **CRUD** for books.

### C — Create

```jsx
databases.createDocument()
```

Video #23.

### R — Read

All records:

```jsx
databases.listDocuments()
```

Video #24.

One record:

```jsx
databases.getDocument()
```

Video #28.

### U — Update

The course doesn't implement a full update/edit feature, but Appwrite provides document update functionality.

### D — Delete

```jsx
databases.deleteDocument()
```

Video #29.

So:

```text
             CRUD

Create  → createDocument()
Read    → listDocuments()
Read    → getDocument()
Update  → updateDocument()
Delete  → deleteDocument()
```

This is a **very important backend-development concept**.

---

# 17. 🔥 Full Shelfie Architecture

At this point, you can understand your entire Shelfie application.

```text
                    APPWRITE
                       │
             ┌─────────┴─────────┐
             │                   │
          Account             Database
             │                   │
          AuthContext        BooksContext
                                 │
                 ┌───────────────┼───────────────┐
                 │               │               │
             addBook()       getBooks()      deleteBook()
                 │               │               │
                 ↓               ↓               ↓
             CREATE          READ MANY        DELETE
                                 │
                                 ↓
                              books[]
                                 │
                                 ↓
                              FlatList
                                 │
                                 ↓
                            item.$id
                                 │
                                 ↓
                         router.push()
                                 │
                                 ↓
                           /books/[id]
                                 │
                                 ↓
                    useLocalSearchParams()
                                 │
                                 ↓
                                id
                                 │
                                 ↓
                          getDocument()
                                 │
                                 ↓
                          Single Book
```

---

# 18. 📚 Videos 23–29 — The Whole Database Section

Now that you've completed the course, here's the progression:

### Video 23 — Create

```text
Form
 ↓
addBook()
 ↓
createDocument()
 ↓
Appwrite
```

### Video 24 — Read all

```text
Appwrite
 ↓
listDocuments()
 ↓
books[]
```

### Video 25 — Display

```text
books[]
 ↓
FlatList
 ↓
Book cards
```

### Video 26 — Realtime

```text
Appwrite changes
 ↓
subscribe()
 ↓
BooksContext
 ↓
UI updates
```

### Video 27 — Dynamic routes

```text
book.$id
 ↓
/books/[id]
```

### Video 28 — Read one

```text
id
 ↓
getDocument()
 ↓
single book
```

### Video 29 — Delete

```text
id
 ↓
deleteDocument()
 ↓
book removed
```

This is the complete flow.

---

# 19. 🧠 The Most Important Code

If you remember only the essential deletion code, remember this:

### BooksContext

```jsx
const deleteBook = async (id) => {
  try {
    await databases.deleteDocument({
      databaseId: DATABASE_ID,
      collectionId: COLLECTION_ID,
      documentId: id,
    });
  } catch (error) {
    console.log("Delete Book Error:", error);
    throw error;
  }
};
```

### Details screen

```jsx
const { id } = useLocalSearchParams();
const { deleteBook } = useBooks();

const handleDelete = async () => {
  try {
    await deleteBook(id);
    router.back();
  } catch (error) {
    console.log("Delete Error:", error);
  }
};
```

That's the core idea of the lesson.

---

# ⭐ Video #29 — Must Remember

### 1. Delete a document with:

```jsx
databases.deleteDocument({
  databaseId: DATABASE_ID,
  collectionId: COLLECTION_ID,
  documentId: id,
});
```

---

### 2. `documentId` identifies the exact book

```jsx
documentId: id
```

where:

```jsx
const { id } = useLocalSearchParams();
```

---

### 3. Keep database logic in `BooksContext`

```text
Screen
 ↓
deleteBook()
 ↓
BooksContext
 ↓
Appwrite
```

---

### 4. Wait for deletion before navigating

```jsx
await deleteBook(id);
router.back();
```

Not:

```jsx
router.back();
await deleteBook(id);
```

---

### 5. Use loading state

```jsx
const [loading, setLoading] = useState(false);
```

This prevents repeated delete requests.

---

### 6. `deleteDocument()` changes the backend

```jsx
deleteDocument()
```

actually removes the Appwrite document.

Whereas:

```jsx
setBooks(...)
```

only changes React's local state.

---

### 7. Confirmation dialogs are good UX

For destructive operations, use:

```jsx
Alert.alert(...)
```

before actually deleting.

---

### 8. Permissions can prevent deletion

If Appwrite returns a permission error, check the document/collection permissions and the current user's access.

---

### 9. CRUD

```text
CREATE → createDocument()
READ   → listDocuments()
READ   → getDocument()
UPDATE → updateDocument()
DELETE → deleteDocument()
```

---

# 🧠 Final Mental Model

You can now think about the entire Shelfie app like this:

```text
                    USER
                     │
                     ↓
                BOOK LIST
                     │
                     ↓
                 item.$id
                     │
                     ↓
              /books/[id]
                     │
                     ↓
        useLocalSearchParams()
                     │
                     ↓
                    id
                     │
          ┌──────────┴──────────┐
          ↓                     ↓
   getDocument()         deleteDocument()
          ↓                     ↓
    Show book details     Remove book
          │                     │
          └──────────┬──────────┘
                     ↓
                   Appwrite
                     │
                     ↓
               BooksContext
                     │
                     ↓
                  books[]
                     │
                     ↓
                 FlatList
```

### The big picture:

> **Your screen handles UI, your Context handles book operations, and Appwrite handles persistent data.**

And that is essentially the architecture Net Ninja has built throughout the final database section of the course. ([YouTube][1])

**You've now completed all 29 videos of the course. 🎉**

[1]: https://www.youtube.com/watch?v=8Y1oPt_Pw0k&utm_source=chatgpt.com "Complete React Native Tutorial #29 - Deleting Books - YouTube"
