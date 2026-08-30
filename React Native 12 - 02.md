Sure. Here is a **short revision note** for everything we did for **Complete React Native Tutorial #12 — Backend Setup with Appwrite**.

# React Native Tutorial #12 — Backend Setup with Appwrite

### 1. What is Appwrite?

**Appwrite** is a backend-as-a-service (BaaS) platform that provides:

* Authentication
* Databases
* File Storage
* User management
* Permissions
* APIs

Instead of building an Express/Node.js backend from scratch, we can use Appwrite as our backend.

---

### 2. Install Appwrite SDK

For our **Expo React Native** project:

```bash
npx expo install react-native-appwrite react-native-url-polyfill
```

---

### 3. Create Appwrite Project

We created a project in the **Appwrite Console**.

Important information:

```text
Project ID
Project Name
```

We also added our Android application as a platform.

Example:

```text
Android Package:
com.akash.notes
```

The package name must match the package configured in our Expo project.

---

### 4. Create Appwrite Configuration

We created:

```text
lib/
└── appwrite.ts
```

Basic configuration:

```tsx
import "react-native-url-polyfill/auto";

import { Client, Account } from "react-native-appwrite";

const client = new Client();

client
  .setEndpoint("https://cloud.appwrite.io/v1")
  .setProject("YOUR_PROJECT_ID")
  .setPlatform("YOUR_ANDROID_PACKAGE_NAME");

export const account = new Account(client);
```

This file creates the connection between:

```text
React Native App
       ↓
   Appwrite SDK
       ↓
   Appwrite Cloud
```

---

### 5. Expo Router Structure

Our project currently has:

```text
app/
└── (app)/
    └── index.tsx
```

We created the temporary testing screen here:

```text
app/
└── (app)/
    ├── index.tsx
    └── test-appwrite.tsx
```

Because `(app)` is a **route group**, the route is:

```text
/test-appwrite
```

not:

```text
/(app)/test-appwrite
```

---

### 6. Test Appwrite Connection

Inside:

```text
app/(app)/test-appwrite.tsx
```

we used:

```tsx
import { useEffect } from "react";
import { Text, View } from "react-native";

import { account } from "../../lib/appwrite";

export default function TestAppwrite() {
  useEffect(() => {
    const testAppwrite = async () => {
      try {
        const user = await account.get();

        console.log("✅ Appwrite connected!");
        console.log("User:", user);
      } catch (error) {
        console.log("❌ Appwrite error:");
        console.log(error);
      }
    };

    testAppwrite();
  }, []);

  return (
    <View>
      <Text>Testing Appwrite...</Text>
    </View>
  );
}
```

---

### 7. Navigate to Test Screen

We can temporarily add:

```tsx
import { Link } from "expo-router";

<Link href="/test-appwrite">
  Test Appwrite
</Link>
```

Then:

```text
Home
   ↓
Test Appwrite
   ↓
/test-appwrite
```

---

### 8. What does `account.get()` do?

```tsx
await account.get();
```

It asks Appwrite:

> "Is there currently a logged-in user? If yes, give me that user's information."

If nobody is logged in, we get an authentication error.

That's **normal at this stage**.

---

## Final Structure

At the end of Tutorial #12, our important structure is:

```text
project/
│
├── app/
│   └── (app)/
│       ├── index.tsx
│       └── test-appwrite.tsx
│
├── lib/
│   └── appwrite.ts
│
├── app.json
├── package.json
└── ...
```

### What we learned

```text
Appwrite
   │
   ├── Create Project
   │
   ├── Add Android Platform
   │
   ├── Install React Native SDK
   │
   ├── Configure Client
   │
   ├── Connect Expo App
   │
   └── Test Connection
```

### Next Tutorial 🚀

**Tutorial #13 — Appwrite Authentication**

We'll implement:

```text
Register
   ↓
Login
   ↓
Create Session
   ↓
Get Current User
   ↓
Logout
```

using Appwrite's `Account` API.
