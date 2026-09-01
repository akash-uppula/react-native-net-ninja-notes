# 📱 React Native — Video #21 Notes

## Complete React Native Tutorial #21 — Database Setup (Appwrite)

Video #21 introduces the **Appwrite Database** and sets up the database that we'll use to store application data.

Since your Shelfie project is already using Appwrite Authentication, this is the next major step:

```text
Authentication
     ↓
Who is the user?
     ↓
Database
     ↓
What data belongs to that user?
```

---

# 1. What Is a Database?

A database is where we **store application data** so that it doesn't disappear when the app closes.

For your Shelfie app, we might eventually store:

```text
Books
Authors
Categories
User's books
Reading status
Favorites
```

Instead of:

```jsx
const books = [
  ...
];
```

inside your React Native code, the data will eventually come from Appwrite.

---

# 2. Appwrite Database Structure

The basic Appwrite database structure is:

```text
Appwrite
   ↓
Database
   ↓
Collection
   ↓
Documents
   ↓
Attributes
```

Think of it like:

```text
Database
│
└── Collection
      │
      ├── Document
      ├── Document
      └── Document
```

---

# 3. Database

A **database** is the overall container for related data.

For example:

```text
Shelfie Database
```

could contain:

```text
Books Collection
Users Collection
Reviews Collection
```

You don't necessarily need multiple databases for a small application.

---

# 4. Collection

A **collection** contains documents of a particular type.

For Shelfie:

```text
Database
   ↓
Books Collection
```

The Books collection contains book records.

For example:

```text
Books
│
├── Book 1
├── Book 2
├── Book 3
└── Book 4
```

---

# 5. Document

A **document** is an individual record inside a collection.

For example:

```text
Books Collection
      ↓
Document
      ↓
{
  title: "Atomic Habits",
  author: "James Clear"
}
```

Another document could be:

```text
{
  title: "The Alchemist",
  author: "Paulo Coelho"
}
```

So:

```text
Collection = group of records

Document = one record
```

---

# 6. Attributes

Attributes define the fields that documents can contain.

For example, your Books collection might have:

```text
Books
│
├── title
├── author
├── description
├── image
└── rating
```

Then a document might contain:

```text
title       → Atomic Habits
author      → James Clear
description → Book description
image       → image URL
rating      → 4.8
```

---

# 7. Think of It Like a Table

If you're familiar with SQL databases, you can roughly think of:

```text
Collection ≈ Table
Document   ≈ Row
Attribute  ≈ Column
```

For example:

| title         | author       | rating |
| ------------- | ------------ | -----: |
| Atomic Habits | James Clear  |    4.8 |
| The Alchemist | Paulo Coelho |    4.7 |

In Appwrite terminology:

```text
Collection
    ↓
Documents
    ↓
Attributes
```

---

# 8. Creating the Database in Appwrite

Go to your Appwrite Console.

Inside your project:

```text
Appwrite Console
      ↓
Your Project
      ↓
Databases
      ↓
Create Database
```

Create your database.

For example:

```text
Database name:
Shelfie Database
```

Appwrite will assign a **Database ID**.

You will use this ID in your React Native code.

---

# 9. Database ID

You should distinguish between:

```text
Database Name
```

and:

```text
Database ID
```

For example:

```text
Name:
Shelfie Database

ID:
shelfie_database
```

The ID is what your application uses to communicate with Appwrite.

Don't confuse it with the display name.

---

# 10. Create a Collection

Inside the database:

```text
Database
   ↓
Create Collection
```

For example:

```text
Collection name:
Books
```

Again, Appwrite gives the collection an ID.

You'll eventually have something like:

```text
DATABASE_ID
COLLECTION_ID
```

These IDs are important when making database requests.

---

# 11. Collection Permissions

This is an **important part of Appwrite databases**.

A collection needs permissions that determine:

> **Who can access the documents?**

For example:

```text
User
 ↓
Can read?
Can create?
Can update?
Can delete?
```

Appwrite uses permissions to control access.

You should **not simply make your entire database publicly writable** just to make development easier.

---

# 12. Database Security

Think about your Shelfie application.

If a user has:

```text
User A
```

you don't want:

```text
User B
```

to modify User A's private data.

So eventually we'll need permissions such as:

```text
User A
   ↓
Own document
   ↓
Read ✓
Update ✓
Delete ✓

User B
   ↓
User A's document
   ↓
Access ✗
```

This becomes especially important when we start storing user-specific data.

---

# 13. Create Attributes

After creating your collection, you can add attributes.

For example, for a simple Books collection:

```text
title
author
description
```

You select the appropriate attribute type.

Common types include:

```text
String
Integer
Float
Boolean
Email
URL
DateTime
```

The exact options depend on the Appwrite version you're using.

---

# 14. Example Book Attributes

A possible Shelfie book structure could eventually look like:

```text
Books Collection

title
author
description
cover
rating
```

Conceptually:

```jsx
{
  title: "Atomic Habits",
  author: "James Clear",
  description: "An easy and proven way...",
  cover: "https://...",
  rating: 4.8
}
```

---

# 15. Required vs Optional Attributes

When creating an attribute, you can generally decide whether it is required.

For example:

```text
title → Required ✓
author → Required ✓
description → Optional
cover → Optional
```

This means a document can't be created without the required fields.

---

# 16. Default Values

Some attributes can also have default values.

For example:

```text
isFavorite
```

could default to:

```text
false
```

So if you create a document without explicitly providing it:

```text
isFavorite
    ↓
false
```

This can simplify your application logic.

---

# 17. Database IDs in Your Code

Just like your Appwrite project has an ID:

```jsx
.setProject("YOUR_PROJECT_ID")
```

your database will have an ID.

You'll eventually define constants such as:

```jsx
const DATABASE_ID = "your-database-id";
const BOOKS_COLLECTION_ID = "your-collection-id";
```

It's better to keep these IDs in one place rather than scattering them throughout your application.

---

# 18. Add `Databases` to Appwrite

Your current Appwrite setup has:

```jsx
import {
  Client,
  Account,
  Avatars,
  ID,
} from "react-native-appwrite";
```

To work with the database, you'll need the Appwrite `Databases` service.

Using ES6 imports, as you've requested throughout your notes:

```jsx
import {
  Client,
  Account,
  Avatars,
  Databases,
  ID,
} from "react-native-appwrite";
```

Then:

```jsx
export const databases = new Databases(client);
```

So your Appwrite services become:

```text
client
 ├── account
 ├── avatars
 └── databases
```

---

# 19. Your `appwrite.js`

Based on the Appwrite setup you've been using, the structure becomes:

```jsx
import "react-native-url-polyfill/auto";

import {
  Client,
  Account,
  Avatars,
  Databases,
  ID,
} from "react-native-appwrite";

export const client = new Client();

client
  .setEndpoint("https://fra.cloud.appwrite.io/v1")
  .setProject("YOUR_PROJECT_ID")
  .setPlatform("YOUR_PLATFORM");

export const account = new Account(client);

export const avatars = new Avatars(client);

export const databases = new Databases(client);

export { ID };
```

The important new line is:

```jsx
export const databases = new Databases(client);
```

---

# 20. Why Use `Databases`?

Now your application has access to Appwrite's database API.

For example, later you'll be able to do things conceptually like:

```jsx
await databases.createDocument(...)
```

to create a document.

And:

```jsx
await databases.listDocuments(...)
```

to retrieve documents.

You'll learn the actual implementation as the tutorial progresses.

---

# 21. Database Flow

Your application architecture is now growing:

```text
React Native
     ↓
Appwrite SDK
     ↓
┌───────────────┐
│               │
↓               ↓
Account       Databases
│               │
↓               ↓
Authentication  App Data
```

Authentication:

```text
Account
  ↓
Login
Register
Logout
Current User
```

Database:

```text
Databases
  ↓
Collections
  ↓
Documents
  ↓
Application Data
```

---

# 22. Authentication vs Database

Don't confuse these two.

### Authentication

Answers:

> **Who is this user?**

```text
Email
Password
Session
User ID
```

### Database

Answers:

> **What data do we store?**

```text
Books
Reviews
Favorites
Reading lists
etc.
```

They work together.

For example:

```text
User logs in
     ↓
Appwrite gives user ID
     ↓
Database
     ↓
Find data belonging to that user
```

---

# 23. User ID Becomes Important

Remember from your registration code:

```jsx
await account.create({
  userId: ID.unique(),
  email,
  password,
  name,
});
```

Appwrite creates a unique user ID.

Later, we can associate database documents with that user.

Conceptually:

```jsx
{
  userId: user.$id,
  title: "Atomic Habits"
}
```

Then we know:

```text
This book belongs to this user.
```

This is going to become very useful for your Shelfie app.

---

# 24. Overall Appwrite Architecture

At this point your project looks like:

```text
                    Appwrite
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
     Authentication              Database
          │                         │
       Account                 Collection
          │                         │
       Session                 Documents
          │                         │
       User ID                 Attributes
```

React Native communicates with both through:

```text
react-native-appwrite
```

---

# ⭐ Video #21 — Must Remember

### 1. Database

Stores your application's data.

### 2. Collection

A group of related documents.

```text
Books
```

### 3. Document

One individual record.

```text
{
  title: "...",
  author: "..."
}
```

### 4. Attribute

A field inside a document.

```text
title
author
rating
```

### 5. Database hierarchy

```text
Database
   ↓
Collection
   ↓
Document
   ↓
Attributes
```

### 6. Add `Databases`

```jsx
import { Databases } from "react-native-appwrite";
```

### 7. Create the service

```jsx
export const databases = new Databases(client);
```

### 8. Database vs Authentication

```text
Authentication → Who is the user?

Database → What data do we store?
```

---

# 🧠 Final Mental Model

Your Appwrite setup is now:

```text
                       Appwrite
                          │
                    Your Project
                          │
              ┌───────────┴───────────┐
              ↓                       ↓
        Authentication             Database
              ↓                       ↓
           Account                 Collection
              ↓                       ↓
           Session                Documents
              ↓                       ↓
           User ID                Attributes
```

And your React Native code communicates with Appwrite through:

```jsx
import {
  Client,
  Account,
  Avatars,
  Databases,
  ID,
} from "react-native-appwrite";
```

with:

```jsx
export const account = new Account(client);

export const avatars = new Avatars(client);

export const databases = new Databases(client);
```

### 🔑 Core lesson

**Appwrite Authentication manages the user/session; Appwrite Database stores the application's data.**

The next database-related videos will build on this setup to actually **create, read, update, and delete documents** from your React Native application.
