# 📱 React Native — Video #12 Notes

## Complete React Native Tutorial #12 — Backend Setup with Appwrite

This video introduces **Appwrite** as the backend for the React Native application.

The big idea is:

```text
React Native App
      ↓
   Appwrite
      ↓
Backend services
```

Instead of building your own backend server from scratch, Appwrite provides ready-made backend services such as **authentication, databases, storage, and more**.

---

# 1. What is a Backend?

So far, your React Native app has mainly been running on the user's device.

For example:

```text
React Native
     ↓
UI
     ↓
TextInput
     ↓
Pressable
```

But real applications need somewhere to store and manage data.

For example, your **My Shelf** app eventually needs to store:

```text
User
 ├── Email
 ├── Password
 └── Account

Books
 ├── Title
 ├── Author
 ├── Description
 └── Image
```

That information needs a backend.

---

# 2. What is Appwrite?

**Appwrite** is a backend-as-a-service platform.

It provides backend functionality without requiring you to build everything yourself.

You can use Appwrite for:

* Authentication
* Databases
* File storage
* Server-side functions
* Permissions
* User management

Think of it as:

```text
Your React Native App
        │
        │ API requests
        ↓
     Appwrite
        │
   ┌────┼────┐
   ↓    ↓    ↓
 Users DB  Storage
```

---

# 3. Why Use Appwrite?

Without a backend, your app could only keep information locally.

For example:

```text
Phone
 │
 └── My Shelf data
```

If the user deletes the app, that local data may disappear.

With a backend:

```text
Phone
  │
  │ Internet
  ↓
Appwrite
  │
  ├── User account
  ├── Books
  └── Images
```

The user's data can be stored remotely and accessed again after logging in.

---

# 4. Create an Appwrite Account

The first step is to create an account on Appwrite.

Then you can access the **Appwrite Console**.

The console is where you manage your backend projects.

Conceptually:

```text
Appwrite Console
       │
       ↓
    Project
       │
 ┌─────┼─────┐
 ↓     ↓     ↓
Auth  Database Storage
```

---

# 5. Create a Project

Inside Appwrite, create a new project.

For your project, you could use something like:

```text
My Shelf
```

The project acts as the container for your backend resources.

Think:

```text
Appwrite
   │
   └── My Shelf Project
          │
          ├── Authentication
          ├── Database
          └── Storage
```

---

# 6. Project ID

When you create the project, Appwrite gives it a:

```text
Project ID
```

You'll need this when connecting your React Native application to Appwrite.

You'll eventually have configuration similar to:

```tsx
const client = new Client();

client
  .setEndpoint("...")
  .setProject("YOUR_PROJECT_ID");
```

The important concept is:

```text
setProject()
     ↓
Tell Appwrite which project
your app belongs to
```

---

# 7. Appwrite Endpoint

Your application also needs to know **where Appwrite is running**.

This is done with:

```tsx
setEndpoint()
```

Conceptually:

```tsx
client
  .setEndpoint("APPWRITE_ENDPOINT")
  .setProject("PROJECT_ID");
```

So:

```text
setEndpoint()
      ↓
Where is Appwrite?

setProject()
      ↓
Which Appwrite project?
```

---

# 8. Appwrite Client

The main Appwrite object used to communicate with the backend is the:

```tsx
Client
```

Import it using ES6 imports:

```tsx
import { Client } from "react-native-appwrite";
```

Then:

```tsx
const client = new Client();
```

Now configure it:

```tsx
client
  .setEndpoint("APPWRITE_ENDPOINT")
  .setProject("PROJECT_ID");
```

---

# 9. Installing Appwrite SDK

You need the Appwrite SDK in your React Native project.

With Expo, install it using:

```bash
npx expo install react-native-appwrite
```

Then:

```tsx
import {
  Client,
} from "react-native-appwrite";
```

This allows your React Native application to communicate with Appwrite.

---

# 10. Create an Appwrite Client File

It's a good idea to keep backend configuration separate from your screens.

For example:

```text
MyShelf
│
├── app
│   ├── index.tsx
│   ├── login.tsx
│   └── signup.tsx
│
├── lib
│   └── appwrite.ts
│
└── package.json
```

The purpose of:

```text
lib/appwrite.ts
```

is to keep your Appwrite setup in one place.

---

# 11. Basic `appwrite.ts`

For example:

```tsx
import { Client } from "react-native-appwrite";

const client = new Client();

client
  .setEndpoint("YOUR_APPWRITE_ENDPOINT")
  .setProject("YOUR_PROJECT_ID");

export default client;
```

Then other files can import the configured client:

```tsx
import client from "../lib/appwrite";
```

This is much cleaner than creating a new client configuration in every screen.

---

# 12. Authentication

One of the main reasons we're setting up Appwrite is **authentication**.

Authentication means managing users who:

```text
Sign up
   ↓
Create account
   ↓
Login
   ↓
Use application
```

For your My Shelf app:

```text
User
 ↓
Signup
 ↓
Appwrite Authentication
 ↓
Account created
 ↓
Login
 ↓
My Shelf
```

---

# 13. Appwrite Account

Appwrite provides an account service.

You'll eventually use something like:

```tsx
Account
```

to perform authentication-related operations.

Import:

```tsx
import {
  Account,
} from "react-native-appwrite";
```

Then create an account service using the client:

```tsx
const account = new Account(client);
```

Conceptually:

```text
Client
  ↓
Account
  ↓
Authentication
```

---

# 14. Signup Flow

The signup process will eventually look like:

```text
User enters:

Name
Email
Password

       ↓

React Native Form

       ↓

Appwrite Account

       ↓

Account created
```

For example:

```text
signup.tsx
    ↓
name
email
password
    ↓
Appwrite
    ↓
new account
```

The detailed signup implementation comes later when working with the forms.

---

# 15. Login Flow

Similarly:

```text
Login Screen
     ↓
Email + Password
     ↓
Appwrite
     ↓
Authentication
     ↓
User logged in
```

So Video #12 is preparing the backend that Video #13's forms can eventually communicate with.

That's an important connection:

```text
Video #12
Appwrite backend
       ↓
Video #13
Login / Signup forms
       ↓
User authentication
```

---

# 16. Database

Appwrite can also provide a database.

Your **My Shelf** app could eventually have data such as:

```text
Books
────────────────────────
id
title
author
description
image
userId
```

Then:

```text
React Native
      ↓
Appwrite Database
      ↓
Book records
```

---

# 17. Storage

Books might have cover images.

Instead of putting large image files directly into a database, you can use Appwrite's storage service.

Conceptually:

```text
Book
 │
 ├── title → Database
 ├── author → Database
 └── cover → Storage
```

So:

```text
Database
   ↓
Text/data

Storage
   ↓
Files/images
```

---

# 18. Permissions

Backend applications need to control **who can access data**.

For example:

```text
User A
 ↓
Can access User A's books

User B
 ↓
Can access User B's books
```

You don't want:

```text
User A
 ↓
❌ Access User B's private books
```

Appwrite provides permissions that can be used to control access to resources.

This becomes especially important once your My Shelf application has multiple users.

---

# 19. Appwrite Services

The major services to remember are:

```text
             Appwrite
                │
      ┌─────────┼─────────┐
      ↓         ↓         ↓
Authentication Database Storage
      │         │         │
    Users      Data      Files
```

And Appwrite also provides other capabilities such as Functions.

---

# 20. Environment Variables

You will eventually have configuration values such as:

```text
APPWRITE_ENDPOINT
APPWRITE_PROJECT_ID
```

It's generally better not to scatter configuration values throughout your source code.

You can use environment variables for configuration.

For example, conceptually:

```text
.env
 ↓
APPWRITE_ENDPOINT
APPWRITE_PROJECT_ID
 ↓
React Native app
```

However, remember:

> **Environment variables in a client/mobile app are not a secure place for secrets.**

Anything bundled into a mobile application can potentially be discovered by someone inspecting the app.

So **never put private API keys, passwords, or other server secrets into your React Native client**.

---

# 21. Client vs Server

This distinction is extremely important.

Your React Native application is:

```text
CLIENT
```

Appwrite provides:

```text
BACKEND
```

So:

```text
┌──────────────────┐
│  React Native    │
│     CLIENT       │
└────────┬─────────┘
         │
         │ API
         ↓
┌──────────────────┐
│     Appwrite     │
│     BACKEND      │
└──────────────────┘
```

The client communicates with the backend through APIs.

---

# 22. Don't Put Secrets in the App

For example, **don't do this**:

```tsx
const secretKey = "MY_PRIVATE_SECRET";
```

inside your React Native application.

Why?

Because your app is distributed to users.

A determined user can inspect the application.

Use server-side mechanisms for secrets.

---

# 23. Appwrite + Expo Router

Your project now has several layers:

```text
                 My Shelf
                    │
       ┌────────────┴────────────┐
       ↓                         ↓
  Expo Router               Appwrite
       │                         │
       ↓                         ↓
Navigation                Backend Services
       │                         │
       ├── Login                 ├── Auth
       ├── Signup                ├── Database
       ├── Home                  └── Storage
       └── Tabs
```

This is starting to look like a real application architecture.

---

# 24. A Clean Appwrite Setup

A simple setup file could look like:

```tsx
import {
  Account,
  Client,
} from "react-native-appwrite";

const client = new Client();

client
  .setEndpoint("YOUR_APPWRITE_ENDPOINT")
  .setProject("YOUR_PROJECT_ID");

const account = new Account(client);

export {
  client,
  account,
};
```

Then your login screen can import:

```tsx
import {
  account,
} from "../lib/appwrite";
```

This is the ES6 `import/export` style you've asked me to use throughout your notes.

---

# 25. Why Separate the Backend Code?

Instead of doing this in every screen:

```tsx
const client = new Client();

client
  .setEndpoint("...")
  .setProject("...");
```

you configure it once.

Then:

```text
lib/appwrite.ts
       ↓
Exports configured services
       ↓
Login
Signup
Home
Books
       ↓
All can use Appwrite
```

This keeps your code organized.

---

# ⭐ Video #12 — Must Remember

### 1. Appwrite

Backend-as-a-service platform providing things like:

```text
Authentication
Database
Storage
Functions
Permissions
```

### 2. Install SDK

```bash
npx expo install react-native-appwrite
```

### 3. Import

```tsx
import {
  Client,
} from "react-native-appwrite";
```

### 4. Create client

```tsx
const client = new Client();
```

### 5. Configure endpoint

```tsx
client.setEndpoint(
  "YOUR_APPWRITE_ENDPOINT"
);
```

### 6. Configure project

```tsx
client.setProject(
  "YOUR_PROJECT_ID"
);
```

### 7. Account service

```tsx
import {
  Account,
} from "react-native-appwrite";

const account = new Account(client);
```

### 8. Keep backend setup separate

For example:

```text
lib/
└── appwrite.ts
```

### 9. Don't put secrets in the client

Anything shipped inside the React Native app should be considered potentially visible to the user.

---

# 🧠 The Big Picture

You can now understand your application like this:

```text
                 MY SHELF APP
                      │
       ┌──────────────┴──────────────┐
       │                             │
       ↓                             ↓
 React Native                    Appwrite
       │                             │
       │                             ├── Authentication
       │                             ├── Database
       │                             └── Storage
       │
       ├── TextInput
       ├── Pressable
       ├── Tabs
       ├── Stack
       └── Expo Router
```

And the upcoming form flow makes much more sense:

```text
Video #12
Appwrite
   ↓
Backend

Video #13
Login / Signup Forms
   ↓
Collect user information

Later
   ↓
Send information to Appwrite
   ↓
Create account / Login
```

### One important correction to keep in your overall notes

You **skipped Video #12 earlier and went directly to #13**, so your learning order should now be:

```text
#10  Tab Bar Icons
 ↓
#11  Safe Area View
 ↓
#12  Backend Setup with Appwrite
 ↓
#13  Login & Signup Forms
```

That sequence is important because **#12 introduces the backend that the authentication forms in #13 will eventually use**.
