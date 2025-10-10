# 🗒️ **MongoDB Notes (Mongoose)**

## 🧱 **Creating a Mongoose Schema & Model**

```js
const mongoose = require("mongoose");

const movieSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: true,
      trim: true,
    },
    releaseYear: {
      type: Number,
      required: true,
    },
    actors: [
      {
        type: String,
      },
    ],
    genre: [
      {
        type: String,
        enum: ["Action", "Drama", "Comedy", "Thriller", "Sci-Fi"],
      },
    ],
    rating: {
      type: Number,
      min: 0,
      max: 10,
      default: 0,
    },
  },
  { timestamps: true }
);

const Movie = mongoose.model("Movie", movieSchema);
module.exports = Movie;
```

---

## 💾 **Inserting Data into MongoDB**

### **1️⃣ Create a new document and save**

```js
const movie = new Movie({
  title: "Inception",
  releaseYear: 2010,
  actors: ["Leonardo DiCaprio"],
  genre: ["Action", "Sci-Fi"],
  rating: 9,
});

const savedMovie = await movie.save();
console.log("Saved Movie:", savedMovie);
```

### **2️⃣ Insert multiple documents**

```js
await Movie.insertMany([
  { title: "Avatar", releaseYear: 2009, genre: ["Sci-Fi"], rating: 8 },
  { title: "Joker", releaseYear: 2019, genre: ["Drama"], rating: 9 },
]);
```

---

## 🔍 **Reading / Querying Data**

### **1️⃣ Find all documents**

```js
const movies = await Movie.find();
```

### **2️⃣ Find with conditions**

```js
const actionMovies = await Movie.find({ genre: "Action" });
```

### **3️⃣ Find one document**

```js
const singleMovie = await Movie.findOne({ title: "Inception" });
```

### **4️⃣ Find by ID**

```js
const movieById = await Movie.findById("6521f0e87a4f99c9b8b1b2a1");
```

### **5️⃣ Select specific fields**

```js
const movie = await Movie.findOne({ title: "Joker" }).select("title rating");
```

### **6️⃣ Sort results**

```js
const sortedMovies = await Movie.find().sort({ rating: -1 }); // Descending order
```

### **7️⃣ Limit results**

```js
const topMovies = await Movie.find().sort({ rating: -1 }).limit(5);
```

---

## ✏️ **Updating Data**

### **1️⃣ Update by ID**

```js
const updatedMovie = await Movie.findByIdAndUpdate(
  "6521f0e87a4f99c9b8b1b2a1",
  { rating: 9.5 },
  { new: true } // returns updated document
);
```

### **2️⃣ Update one document**

```js
const updated = await Movie.findOneAndUpdate(
  { title: "Avatar" },
  { rating: 8.5 },
  { new: true }
);
```

### **3️⃣ Update multiple documents**

```js
await Movie.updateMany({ genre: "Action" }, { $set: { rating: 10 } });
```

---

## 🗑️ **Deleting Data**

### **1️⃣ Delete by ID**

```js
await Movie.findByIdAndDelete("6521f0e87a4f99c9b8b1b2a1");
```

### **2️⃣ Delete one document**

```js
await Movie.findOneAndDelete({ title: "Avatar" });
```

### **3️⃣ Delete multiple documents**

```js
await Movie.deleteMany({ genre: "Drama" });
```

---

## ⚙️ **Other Useful Mongoose Methods**

| Method                 | Description                                            |
| ---------------------- | ------------------------------------------------------ |
| `.countDocuments()`    | Counts total matching documents                        |
| `.exists()`            | Checks if a document exists                            |
| `.distinct(field)`     | Returns distinct values of a field                     |
| `.populate()`          | Populates referenced documents (used in relationships) |
| `.lean()`              | Returns plain JS objects instead of Mongoose docs      |
| `.aggregate(pipeline)` | Performs complex aggregation queries                   |

Example:

```js
const count = await Movie.countDocuments({ genre: "Action" });
const uniqueGenres = await Movie.distinct("genre");
```

---

## 🧩 **Chaining Example**

You can combine queries like:

```js
const results = await Movie.find({ genre: "Action" })
  .select("title rating")
  .sort({ rating: -1 })
  .limit(3)
  .lean();
```

---

## 💡 **Tips**

* Always wrap DB calls in `try...catch` for error handling.
* Use `{ new: true }` when updating to get the latest document.
* Use `.lean()` for faster reads when you don’t need document methods.
* Use indexes (`schema.index()`) for fields frequently queried.

---
