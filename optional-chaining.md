### 🔍 **Optional Chaining (`?.`)**

**Definition:**
Optional chaining is a **safe way to access deeply nested properties** of an object **without causing errors** if some part of the chain is `undefined` or `null`.

---

### 🧩 Example Without Optional Chaining:

```js
const user = {
  name: "Ajmal",
  address: {
    city: "Lucknow",
  },
};

console.log(user.address.city);     // ✅ Works fine
console.log(user.contact.phone);    // ❌ Error: Cannot read properties of undefined
```

Because `user.contact` doesn’t exist, trying to access `user.contact.phone` crashes the code.

---

### ✅ Example With Optional Chaining:

```js
console.log(user?.address?.city);    // "Lucknow"
console.log(user?.contact?.phone);   // undefined (No error!)
```

Now, if any part before `?.` is `undefined` or `null`,
JavaScript **stops** and safely returns `undefined` — instead of throwing an error.

---

### 💡 In React (like your code):

```js
data?.map(movie => <li>{movie.title}</li>)
```

Means:

> “If `data` exists, map over it. If not, just skip this part — don’t crash.”

This is especially helpful because, before the API data loads,
`data` might be `undefined` for a short time.

---

### 🔒 In simple words:

> **Optional chaining** is like saying:
> “Check if it exists first — if not, just ignore it safely.”

---
