# 🧠 useState Initialization — Notes

### 🟩 **1. Normal useState**

```js
const [count, setCount] = useState(0);
```

* React sets the state to `0` on the **first render**.
* On re-renders, it **ignores** the initial value (state is preserved).
* ✅ Best for **simple, static default values**.

---

### 🟨 **2. Expression inside useState**

```js
const [data, setData] = useState(JSON.parse(localStorage.getItem("key")) || []);
```

* The **expression inside** `useState(...)` runs **on every render**.
* React ignores the result after the first render,
  but the code (like `localStorage.getItem`) still executes each time.
* ❌ Can cause **unnecessary work** if the expression is heavy
  (e.g., parsing JSON, reading from localStorage, or doing calculations).

---

### 🟦 **3. Function (Lazy Initialization)**

```js
const [data, setData] = useState(() => {
  const stored = localStorage.getItem("key");
  return stored ? JSON.parse(stored) : [];
});
```

* The **function runs only once**, during the **initial render**.
* React calls the function just to get the initial value,
  and then **never calls it again** on re-renders.
* ✅ Best for **expensive or computed default values**.
* ⚡ Improves performance and avoids repeated operations.

---

### ⚡ **Summary Table**

| Type                | Expression Runs Every Render? | Best For                    | Example                                 |
| ------------------- | ----------------------------- | --------------------------- | --------------------------------------- |
| **Normal**          | ❌ No                          | Simple static value         | `useState(0)`                           |
| **Expression**      | ⚠️ Yes                        | Quick or light logic        | `useState(a + b)`                       |
| **Function (Lazy)** | ✅ Only once                   | Expensive or computed logic | `useState(() => getFromLocalStorage())` |
