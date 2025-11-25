# 📝 **Notes: Difference Between `Model.create()` vs `new Model() + save()` in Mongoose**

---

## **1. `Model.create()`**

### ✔ What it does

* Creates a new document **and saves it** to MongoDB in **one step**.
* Automatically validates the schema.
* Returns the saved document.

### ✔ When to use

* When you want a **simple create + save**.
* No extra logic needed before saving.
* Cleaner and shorter code.

### ✔ Example

```js
await Cart.create({
  userId,
  items: [{ productId, quantity, size }]
});
```

---

## **2. `new Model()` + `.save()`**

### ✔ What it does

* Creates a new document **in memory**.
* You can modify it **before saving**.
* Must manually call `.save()` to store it in DB.

### ✔ When to use

* When you need **extra processing** before saving:

  * Add more fields
  * Push more items
  * Recalculate totals
  * Apply custom logic

### ✔ Example

```js
const cart = new Cart({ userId });
cart.items.push({ productId, quantity });
await cart.save();
```

---

# 🧠 **3. Key Differences (Very Important)**

| Feature            | `Model.create()` | `new Model() + save()`           |
| ------------------ | ---------------- | -------------------------------- |
| Creates instance   | ✔ Yes            | ✔ Yes                            |
| Saves to DB        | ✔ Auto           | ❌ Manual                         |
| Modify before save | ❌ No             | ✔ Yes                            |
| Lines of code      | 1                | 2 or more                        |
| Use case           | Simple insert    | Logical processing before insert |

---

# 🛠 **4. Internal Technical Difference**

### **`Model.create()`**

* Internally does:

  ```js
  new Model(data).save()
  ```
* Atomic create + save.
* Good for performance (shorter roundtrip).

### **`new Model()`**

* Only creates an instance.
* `.save()` triggers:

  * Validation
  * Pre-save middleware
  * Hooks
  * Database write

---

# ⭐ **5. Final Recommendation**

* Use **`Model.create()`** → for simple inserts.
* Use **`new Model() + save()`** → when modifying data before saving.

