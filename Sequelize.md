## Que-1 What is `Op` in Sequelize?

`Op` stands for **Sequelize Operators** — it’s a set of **comparison and query operators** that Sequelize uses to build SQL queries more expressively and flexibly.

### 📦 How to use it:

You import it like this:

```js
const { Op } = require('sequelize');
```

Then you use it in your query conditions like:

```js
where: {
  rating: { [Op.gt]: 7 },             // rating > 7
  genre: { [Op.iLike]: '%action%' },  // genre ILIKE '%action%'
}
```

### 🧠 Why use `Op`?

Because Sequelize doesn't allow raw SQL like:

```js
where: "rating > 7"
```

Instead, it provides a safe and structured way using `Op`.

### ✅ Common Sequelize Operators (`Op`)

| Operator   | Description              | SQL Equivalent    |
| ---------- | ------------------------ | ----------------- |
| `Op.eq`    | Equal to                 | `=`               |
| `Op.ne`    | Not equal to             | `!=`              |
| `Op.gt`    | Greater than             | `>`               |
| `Op.gte`   | Greater than or equal    | `>=`              |
| `Op.lt`    | Less than                | `<`               |
| `Op.lte`   | Less than or equal       | `<=`              |
| `Op.like`  | Case-sensitive pattern   | `LIKE '%value%'`  |
| `Op.iLike` | Case-insensitive pattern | `ILIKE '%value%'` |
| `Op.in`    | Value in array           | `IN (...)`        |
| `Op.notIn` | Value not in array       | `NOT IN (...)`    |
| `Op.or`    | Logical OR               | `OR`              |
| `Op.and`   | Logical AND              | `AND`             |


### 📌 Example: Search for movies with genre including "action" and rating > 7

```js
const movies = await movieModel.findAll({
  where: {
    genre: { [Op.iLike]: '%action%' },
    rating: { [Op.gt]: 7 }
  }
});
```

---

## Que-2 What is `include` in Sequelize?

In Sequelize, `include` is used to **fetch associated (related) models** when you query a model. It's like doing a SQL `JOIN`.

### 💡 Example

Let’s say you have two models:

#### 1. `watchlistModel`

Has fields: `id`, `userId`, `movieId`

#### 2. `movieModel`

Has fields: `id`, `title`, `rating`, `releaseYear`, etc.

And you have this association set up:

```js
watchlistModel.belongsTo(movieModel, { foreignKey: 'movieId' });
movieModel.hasMany(watchlistModel, { foreignKey: 'movieId' });
```

Now, when you want to get all movies from the watchlist **with full movie details**, you write:

Example 1:
```js
const data = await watchlistModel.findAll({
  include: [
    {
      model: movieModel,
      attributes: ['title', 'rating', 'releaseYear']
    }
  ],
  order: [
    [Movie, sortBy, order.toUpperCase() === "ASC" ? "ASC" : "DESC"],
  ],
});
```

Example 2:
```js
const data = await watchlistModel.findAll({ // 🔍 Fetch all entries from the watchlist table
  include: [ // 👥 Include associated models (related data from other tables)
    {
      model: movieModel, // 🎬 Include related Movie data using the association (watchlist belongsTo movie)
      attributes: ['title', 'rating', 'releaseYear'] // 🧾 Only select these specific columns from the Movie model
    }
  ],
  order: [ // 📊 Sort the result based on a column inside the included Movie model
    [
      Movie,               // 🔗 Referencing the Movie model for sorting
      sortBy,              // 📌 The column name to sort by (e.g., 'rating' or 'releaseYear')
      order.toUpperCase() === "ASC" ? "ASC" : "DESC" // 🔼⬇️ Decide ascending or descending order based on `order` variable
    ],
  ],
});
```

Example 3:
```js
User.findAll({                  // 🔍 Get all users
  include: [                    // 👥 Include related data
    {
      model: Watchlist,         // 📄 Each user’s watchlist
      attributes: ['id', 'createdAt'],
      include: [                // ⬇️ Nested include inside watchlist
        {
          model: Movie,         // 🎬 Include movie data for each watchlist item
          attributes: ['title', 'rating', 'releaseYear']
        }
      ]
    }
  ]
});
```

Example 4:
```js
User.findAll({
  where: { role: 'subscriber' },          // 🔍 Only subscribers
  include: [                              // 👥 Include nested models
    {
      model: Watchlist,                   // 🗂️ Each user's watchlist
      include: [
        {
          model: Movie,                   // 🎬 Movie in each watchlist
          attributes: ['title', 'rating']
        }
      ]
    }
  ],
  order: [['createdAt', 'DESC']]          // 📌 Sort users by created date
});

```

This `include` will:

* Fetch data from `watchlistModel`
* Join and pull in the related `movieModel` data
* Only return the specified movie attributes (`title`, `rating`, etc.)


### 🧾 What SQL this generates:

```sql
SELECT "watchlist"."id", "watchlist"."userId", ..., 
       "movie"."title", "movie"."rating", "movie"."releaseYear"
FROM "watchlists" AS "watchlist"
LEFT OUTER JOIN "movies" AS "movie"
ON "watchlist"."movieId" = "movie"."id"
```


### ✅ Why `include` is useful:

* You **avoid extra queries**. Instead of fetching `watchlist` and then querying `movieModel` again, you get everything in **one go**.
* You can sort, filter, or select based on associated table fields.


### 🔁 Summary:

| Sequelize Keyword      | Meaning                                                        |
| ---------------------- | -------------------------------------------------------------- |
| `include`              | Tells Sequelize to **JOIN** with another table (related model) |
| `model` inside include | Which model to join                                            |
| `attributes`           | What fields to pick from that joined model                     |

---

## Que-3 Foreign Key Constraints in Sequelize (`onDelete` & `onUpdate`)

### 🔹 What are Foreign Key Constraints?
When one table references another via a **foreign key**, you can control what should happen to the **child records** if the **parent record** is **deleted or updated**.

In Sequelize, this is managed using:
* `onDelete`
* `onUpdate`

## 🔹 Why use them?

Let’s say you have a document system:

* One **Folder** can contain many **Files**
* If a Folder is deleted, what should happen to its Files?

To control this behavior, we use `onDelete` and `onUpdate`.

## 🔹 Common Options & Their Meaning

| Constraint  | What it does                                             |
| ----------- | -------------------------------------------------------- |
| `CASCADE`   | Propagate the action (delete or update) to child records |
| `SET NULL`  | Set the child foreign key to `NULL`                      |
| `RESTRICT`  | Prevent the action if dependent child records exist      |
| `NO ACTION` | Similar to RESTRICT but depends on DB engine             |


## 📁 Real-World Example: Folder → Files

### 🔧 Models

### `Folder.js`

```js
module.exports = (sequelize, DataTypes) => {
  const Folder = sequelize.define('Folder', {
    folderId: {
      type: DataTypes.UUID,
      defaultValue: DataTypes.UUIDV4,
      primaryKey: true
    },
    name: DataTypes.STRING,
    type: DataTypes.STRING,
    maxFileLimit: DataTypes.INTEGER
  });

  Folder.associate = models => {
    Folder.hasMany(models.File, {
      foreignKey: 'folderId',
      onDelete: 'CASCADE',   // 🔥 If folder deleted, delete files
      onUpdate: 'CASCADE'    // 🔄 If folderId changed, update in files
    });
  };

  return Folder;
};
```

### `File.js`

```js
module.exports = (sequelize, DataTypes) => {
  const File = sequelize.define('File', {
    fileId: {
      type: DataTypes.UUID,
      defaultValue: DataTypes.UUIDV4,
      primaryKey: true
    },
    name: DataTypes.STRING,
    folderId: {
      type: DataTypes.UUID,
      allowNull: false
    }
  });

  File.associate = models => {
    File.belongsTo(models.Folder, {
      foreignKey: 'folderId',
      onDelete: 'CASCADE',
      onUpdate: 'CASCADE'
    });
  };

  return File;
};
```

## 📄 Migration Example

In `create-file.js` migration:

```js
await queryInterface.createTable('files', {
  fileId: {
    type: Sequelize.UUID,
    primaryKey: true,
    defaultValue: Sequelize.UUIDV4
  },
  name: {
    type: Sequelize.STRING,
    allowNull: false
  },
  folderId: {
    type: Sequelize.UUID,
    allowNull: false,
    references: {
      model: 'folders',
      key: 'folderId'
    },
    onDelete: 'CASCADE',  // Ensures DB enforces the rule
    onUpdate: 'CASCADE'
  },
  createdAt: Sequelize.DATE,
  updatedAt: Sequelize.DATE
});
```

## 🔍 Explanation of Behavior

| Scenario          | With `CASCADE`        | With `SET NULL`            | With `RESTRICT`                    |
| ----------------- | --------------------- | -------------------------- | ---------------------------------- |
| Delete folder     | Files are deleted too | Files’ `folderId` set NULL | Error: Cannot delete folder        |
| Update `folderId` | Updated in files too  | Files’ `folderId` remains  | Error if `folderId` is foreign key |


## ✅ Best Practice for Your Case

Use `onDelete: 'CASCADE'` for a document/file system like yours because:

* You **don’t want orphan files** if a folder is deleted.
* You ensure **clean, automatic file cleanup**.

## 🧠 Key Points to Remember

* **Define it in both:**

  * Sequelize model associations
  * Sequelize migrations
* `onDelete` and `onUpdate` only work **if foreign keys are present**
* Make sure `foreignKey` is defined explicitly in `associate` methods

## 📝 Summary

| Where to set? | Field      | Constraint to apply    |
| ------------- | ---------- | ---------------------- |
| Model         | `folderId` | `onDelete`, `onUpdate` |
| Migration     | `folderId` | `onDelete`, `onUpdate` |

---
