# SQLite Databases

---

## Table of Contents

1. [Learning Outcomes](#learning-outcomes)
2. [What is SQLite?](#what-is-sqlite)
3. [Setting Up: The SQLiteOpenHelper Class](#setting-up-the-sqliteopenhelper-class)
4. [The SQLiteDatabase Class and CRUD Operations](#the-sqlitedatabase-class-and-crud-operations)
5. [Inserting Data with ContentValues](#inserting-data-with-contentvalues)
6. [Querying Data with the Cursor Class](#querying-data-with-the-cursor-class)
7. [Updating and Deleting Records](#updating-and-deleting-records)
8. [Using Constants for Table and Column Names](#using-constants-for-table-and-column-names)
9. [Putting It All Together: A Complete Example](#putting-it-all-together-a-complete-example)

---

## Learning Outcomes

By the end of this session, you will be able to:

- Explain what SQLite is and why it is used in Android development
- Subclass `SQLiteOpenHelper` to create and manage a local database
- Use `SQLiteDatabase` methods to perform Create, Read, Update, and Delete (CRUD) operations
- Use `ContentValues` to insert and update rows in a database table
- Use a `Cursor` to iterate over and retrieve query results
- Apply best practices such as using constants for table and column names to reduce errors

---

## What is SQLite?

[↑ Back to Table of Contents](#table-of-contents)

### Overview

SQLite is a **lightweight, file-based relational database engine** written in C. Unlike database systems such as MySQL or PostgreSQL that require a running server process, SQLite stores an entire database in a single file on disk. This makes it ideal for mobile applications where simplicity and low overhead matter.

Android ships with built-in support for SQLite — you do not need to install or configure anything extra. Android provides a set of Java/Kotlin wrapper classes so that you can work with SQLite without writing any C code directly.

### Why SQLite on Android?

When your app needs to store **structured, relational data** that persists across sessions, SQLite is a strong choice. Compare it with other storage options:

| Storage Option | Best For |
|---|---|
| `SharedPreferences` | Simple key-value pairs (settings, flags) |
| Internal/External Files | Unstructured data (text files, images) |
| **SQLite** | **Structured, relational data with complex queries** |
| Room (Jetpack) | SQLite with compile-time verification and less boilerplate |

> **Note:** Room is the modern recommended abstraction over SQLite, but understanding raw SQLite is important because Room is built on top of it — you will understand what Room is doing under the hood.

### How Android Wraps SQLite

Android provides three key classes you will use constantly:

- **`SQLiteOpenHelper`** — manages database creation and versioning
- **`SQLiteDatabase`** — provides methods for CRUD operations
- **`Cursor`** — holds and navigates query results

The relationship between these looks like this:

```
+---------------------------+
|    Your Activity / ViewModel   |
+---------------------------+
            |
            v
+---------------------------+
|     SQLiteOpenHelper      |  <-- You subclass this
|  (DatabaseHelper.kt)      |
+---------------------------+
            |
            v
+---------------------------+
|      SQLiteDatabase       |  <-- Used to run queries
+---------------------------+
            |
            v
+---------------------------+
|    SQLite Database File   |  <-- Stored on device
|    (myapp.db)             |
+---------------------------+
```

---

## Setting Up: The SQLiteOpenHelper Class

[↑ Back to Table of Contents](#table-of-contents)

### What is SQLiteOpenHelper?

`SQLiteOpenHelper` is an **abstract class** that handles two important lifecycle concerns for your database:

1. **Creation** — runs `CREATE TABLE` SQL statements the first time the database is needed
2. **Version management** — runs migration SQL when the database schema changes across app versions

Because it is abstract, you **must** subclass it and override two methods:

- `onCreate(db: SQLiteDatabase)` — called when the database is first created
- `onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int)` — called when you increment the database version number

### Deferred Initialisation

An important detail: the database is **not created or opened when you instantiate your helper**. It is only created or opened the first time you call `getWritableDatabase()` or `getReadableDatabase()`. This prevents slow start-up times if you have a large database.

### A Simple DatabaseHelper

```kotlin
import android.content.Context
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

// DatabaseHelper subclasses SQLiteOpenHelper.
// It manages the creation and versioning of our SQLite database.
class DatabaseHelper(context: Context) : SQLiteOpenHelper(
    context,
    DATABASE_NAME, // The filename for the .db file
    null,          // CursorFactory - null uses the default
    DATABASE_VERSION
) {

    companion object {
        // Using a companion object for constants keeps them
        // associated with the class and avoids magic strings.
        const val DATABASE_NAME = "contacts.db"
        const val DATABASE_VERSION = 1

        // Table and column name constants
        const val TABLE_CONTACTS = "Contacts"
        const val COLUMN_ID = "Id"
        const val COLUMN_NAME = "Name"
        const val COLUMN_POSTCODE = "Postcode"
    }

    // onCreate is called ONCE - the very first time the database is needed.
    // This is where we create our tables.
    override fun onCreate(db: SQLiteDatabase) {
        // Build a CREATE TABLE SQL statement using our constants
        val createTable = """
            CREATE TABLE $TABLE_CONTACTS (
                $COLUMN_ID INTEGER PRIMARY KEY AUTOINCREMENT,
                $COLUMN_NAME TEXT NOT NULL,
                $COLUMN_POSTCODE TEXT
            )
        """.trimIndent()

        // execSQL runs a SQL statement that does not return data
        db.execSQL(createTable)
    }

    // onUpgrade is called when DATABASE_VERSION is incremented.
    // This is where you migrate data from old schema to new schema.
    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        // Simple strategy: drop the old table and recreate it.
        // In a real app you would write proper migrations to preserve user data!
        db.execSQL("DROP TABLE IF EXISTS $TABLE_CONTACTS")
        onCreate(db)
    }
}
```

> **Key point:** `execSQL()` executes any SQL statement but **does not return a value**. It is useful for DDL statements like `CREATE TABLE`, `DROP TABLE`, or `ALTER TABLE`.

---

## The SQLiteDatabase Class and CRUD Operations

[↑ Back to Table of Contents](#table-of-contents)

### Getting a Database Instance

Once you have a `DatabaseHelper`, you retrieve a `SQLiteDatabase` object by calling one of two methods:

- `getWritableDatabase()` — returns a database you can read from **and** write to
- `getReadableDatabase()` — returns a database for reading only (falls back to writable if possible)

```kotlin
val dbHelper = DatabaseHelper(context)

// For insert, update, delete operations:
val writableDb: SQLiteDatabase = dbHelper.writableDatabase

// For select/query operations:
val readableDb: SQLiteDatabase = dbHelper.readableDatabase
```

### CRUD Method Overview

`SQLiteDatabase` provides convenience methods that correspond to each CRUD operation:

| Operation | Method | SQL Equivalent |
|---|---|---|
| **Create** | `insert()` | `INSERT INTO ...` |
| **Read** | `query()` / `rawQuery()` | `SELECT ...` |
| **Update** | `update()` | `UPDATE ... SET ...` |
| **Delete** | `delete()` | `DELETE FROM ...` |

You can also use `execSQL()` for any SQL statement, and `rawQuery()` to run a full `SELECT` statement as a string.

### When to Use rawQuery vs query

- **`query()`** — structured, parameterised helper method. Less SQL, more Kotlin.
- **`rawQuery()`** — you write the full SQL yourself. More flexible, but more room for errors.

```kotlin
// Using rawQuery - you write the full SQL string
// The "?" placeholder is replaced by the value in the array (prevents SQL injection)
val cursor = db.rawQuery(
    "SELECT * FROM Contacts WHERE Name = ?",
    arrayOf("Andrew")
)

// Using query - the helper builds the SQL for you
val cursor2 = db.query(
    "Contacts",          // table name
    null,                // columns (null = all columns, i.e. SELECT *)
    "Name = ?",          // WHERE clause
    arrayOf("Andrew"),   // WHERE clause arguments
    null,                // GROUP BY
    null,                // HAVING
    null                 // ORDER BY
)
```

---

## Inserting Data with ContentValues

[↑ Back to Table of Contents](#table-of-contents)

### What is ContentValues?

`ContentValues` is a key-value store where:
- The **key** is a column name (a `String`)
- The **value** is the data you want to store in that column

Think of it as a `Map<String, Any?>` specifically designed for use with SQLite insert and update operations. It represents the contents of **one row** that you want to insert or update.

```
ContentValues (one row of data)
+-------------+-------------+
|    Key      |    Value    |
+-------------+-------------+
| "Name"      | "Andrew"    |
| "Postcode"  | "CH2 4NU"   |
+-------------+-------------+
```

### Inserting a Row

```kotlin
import android.content.ContentValues
import android.content.Context
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

class DatabaseHelper(context: Context) : SQLiteOpenHelper(context, "contacts.db", null, 1) {

    companion object {
        const val TABLE_CONTACTS = "Contacts"
        const val COLUMN_ID = "Id"
        const val COLUMN_NAME = "Name"
        const val COLUMN_POSTCODE = "Postcode"
    }

    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL("""
            CREATE TABLE $TABLE_CONTACTS (
                $COLUMN_ID INTEGER PRIMARY KEY AUTOINCREMENT,
                $COLUMN_NAME TEXT NOT NULL,
                $COLUMN_POSTCODE TEXT
            )
        """.trimIndent())
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS $TABLE_CONTACTS")
        onCreate(db)
    }

    // Insert a new contact into the database.
    // Returns the row ID of the newly inserted row, or -1 if an error occurred.
    fun insertContact(name: String, postcode: String): Long {
        // Get a writable database instance
        val db = writableDatabase

        // Create a ContentValues object and populate it
        val contentValues = ContentValues()
        contentValues.put(COLUMN_NAME, name)       // "Name" column = name parameter
        contentValues.put(COLUMN_POSTCODE, postcode) // "Postcode" column = postcode parameter

        // insert() returns the row ID of the newly inserted row, or -1 on failure
        val rowId = db.insert(TABLE_CONTACTS, null, contentValues)

        // Always close the database when done
        db.close()

        return rowId
    }
}
```

> **The second parameter of `insert()`** is the "null column hack" — a column name to explicitly insert NULL into if `ContentValues` is empty. You can safely pass `null` here in almost all cases.

---

## Querying Data with the Cursor Class

[↑ Back to Table of Contents](#table-of-contents)

### What is a Cursor?

When you run a `query()` or `rawQuery()`, Android does not immediately hand you a list of results. Instead, it gives you a **`Cursor`** — an object that points to the result set and lets you navigate through it row by row.

Think of a cursor like a reading head on a tape:

```
  Result rows:
  +----+----------+------------+
  | Id |   Name   |  Postcode  |
  +----+----------+------------+
  |  1 |  Andrew  |  CH2 4NU   |  <-- cursor starts BEFORE this row
  |  2 |  Sarah   |  M1 2AB    |
  |  3 |  James   |  SW1A 1AA  |
  +----+----------+------------+
  ^
  Cursor position: before first row
```

You call `moveToFirst()` or `moveToNext()` to advance through the rows.

### Key Cursor Methods

| Method | Description |
|---|---|
| `moveToFirst()` | Moves to the first row. Returns `false` if the result is empty. |
| `moveToNext()` | Moves to the next row. Returns `false` when there are no more rows. |
| `getColumnIndex(columnName)` | Returns the integer index of a column by name |
| `getString(columnIndex)` | Returns the value of the column at the given index as a `String` |
| `getInt(columnIndex)` | Returns the value as an `Int` |
| `getLong(columnIndex)` | Returns the value as a `Long` |
| `close()` | Releases the cursor's resources — **always call this when done** |

### Querying All Contacts

```kotlin
import android.content.ContentValues
import android.content.Context
import android.database.Cursor
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

// A simple data class to hold contact information
data class Contact(val id: Int, val name: String, val postcode: String)

class DatabaseHelper(context: Context) : SQLiteOpenHelper(context, "contacts.db", null, 1) {

    companion object {
        const val TABLE_CONTACTS = "Contacts"
        const val COLUMN_ID = "Id"
        const val COLUMN_NAME = "Name"
        const val COLUMN_POSTCODE = "Postcode"
    }

    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL("""
            CREATE TABLE $TABLE_CONTACTS (
                $COLUMN_ID INTEGER PRIMARY KEY AUTOINCREMENT,
                $COLUMN_NAME TEXT NOT NULL,
                $COLUMN_POSTCODE TEXT
            )
        """.trimIndent())
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS $TABLE_CONTACTS")
        onCreate(db)
    }

    // Retrieve all contacts from the database and return them as a List
    fun getAllContacts(): List<Contact> {
        val contacts = mutableListOf<Contact>()

        // Get a readable database - we are only reading data
        val db = readableDatabase

        // query() with all nulls after the table name means: SELECT * FROM Contacts
        val cursor: Cursor = db.query(
            TABLE_CONTACTS, // Table name
            null,           // Columns (null = all)
            null,           // WHERE clause
            null,           // WHERE arguments
            null,           // GROUP BY
            null,           // HAVING
            null            // ORDER BY
        )

        // moveToFirst() returns false if the result set is empty
        if (cursor.moveToFirst()) {
            do {
                // Get the column index for each column by name
                val idIndex       = cursor.getColumnIndex(COLUMN_ID)
                val nameIndex     = cursor.getColumnIndex(COLUMN_NAME)
                val postcodeIndex = cursor.getColumnIndex(COLUMN_POSTCODE)

                // Read the values at each column index
                val contact = Contact(
                    id       = cursor.getInt(idIndex),
                    name     = cursor.getString(nameIndex),
                    postcode = cursor.getString(postcodeIndex)
                )

                contacts.add(contact)

            } while (cursor.moveToNext()) // Advance to the next row; stop when false
        }

        // IMPORTANT: Always close the cursor to release resources
        cursor.close()
        db.close()

        return contacts
    }
}
```

**Sample output** (when used in a test context):

```
Contact(id=1, name=Andrew, postcode=CH2 4NU)
Contact(id=2, name=Sarah, postcode=M1 2AB)
Contact(id=3, name=James, postcode=SW1A 1AA)
```

---

## Updating and Deleting Records

[↑ Back to Table of Contents](#table-of-contents)

### Updating a Record

The `update()` method works similarly to `insert()` — you use `ContentValues` to specify the new column values, plus a `WHERE` clause to identify which rows to update.

```kotlin
// Update a contact's details by their Id.
// Returns the number of rows affected.
fun updateContact(id: Int, newName: String, newPostcode: String): Int {
    val db = writableDatabase

    // ContentValues holds the NEW values for the columns being updated
    val contentValues = ContentValues()
    contentValues.put(COLUMN_NAME, newName)
    contentValues.put(COLUMN_POSTCODE, newPostcode)

    // "Id=?" is the WHERE clause.
    // The "?" is replaced safely by the value in the args array.
    // Using "?" instead of string interpolation prevents SQL injection attacks.
    val args = arrayOf(id.toString())
    val rowsAffected = db.update(
        TABLE_CONTACTS,  // Table to update
        contentValues,   // New values
        "$COLUMN_ID=?",  // WHERE clause
        args             // Arguments for the WHERE clause
    )

    db.close()
    return rowsAffected
}
```

> **Why use `"Id=?"` instead of `"Id=$id"`?**
> Using parameterised queries (the `?` placeholder) prevents **SQL injection** — a security vulnerability where malicious input is crafted to manipulate your SQL statements. Always prefer parameterised queries over string interpolation in SQL.

### Deleting a Record

```kotlin
// Delete a contact by their Id.
// Returns the number of rows deleted.
fun deleteContact(id: Int): Int {
    val db = writableDatabase

    // Specify which rows to delete using a WHERE clause
    val rowsDeleted = db.delete(
        TABLE_CONTACTS, // Table to delete from
        "$COLUMN_ID=?", // WHERE clause - which rows to delete
        arrayOf(id.toString()) // Arguments replacing "?"
    )

    db.close()
    return rowsDeleted
}
```

### Deleting All Records

If you want to clear a table entirely, you can pass `null` for the `WHERE` clause:

```kotlin
// Delete ALL contacts - use with care!
fun deleteAllContacts(): Int {
    val db = writableDatabase
    val rowsDeleted = db.delete(TABLE_CONTACTS, null, null)
    db.close()
    return rowsDeleted
}
```

---

## Using Constants for Table and Column Names

[↑ Back to Table of Contents](#table-of-contents)

### Why Constants Matter

Throughout the previous examples you may have noticed we consistently used constants like `TABLE_CONTACTS` and `COLUMN_NAME` instead of typing the string `"Contacts"` or `"Name"` directly in every method.

Consider what happens without constants:

```kotlin
// Without constants - easy to introduce bugs
db.query("Contacts", null, "name = ?", ...)  // lowercase "name"
// But elsewhere you write:
db.query("Contact", null, ...)               // missing 's'
// And in onUpgrade:
db.execSQL("DROP TABLE IF EXISTS contacts")  // all lowercase
```

These are all different strings to Kotlin but refer to the same table. SQLite may or may not handle some of these — and tracking down the resulting crash can be very frustrating.

### Declaring Constants

In Kotlin, the natural place for database constants is in the **companion object** of your `DatabaseHelper`:

```kotlin
import android.content.Context
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

class DatabaseHelper(context: Context) : SQLiteOpenHelper(context, DATABASE_NAME, null, DATABASE_VERSION) {

    companion object {
        // --- Database metadata ---
        const val DATABASE_NAME    = "myapp.db"
        const val DATABASE_VERSION = 1

        // --- Table: Contacts ---
        const val TABLE_CONTACTS   = "Contacts"
        const val COLUMN_ID        = "Id"
        const val COLUMN_NAME      = "Name"
        const val COLUMN_POSTCODE  = "Postcode"

        // --- Table: Notes (example of a second table) ---
        const val TABLE_NOTES      = "Notes"
        const val COLUMN_NOTE_ID   = "NoteId"
        const val COLUMN_NOTE_TEXT = "NoteText"
        const val COLUMN_CONTACT_FK = "ContactId" // Foreign key to Contacts
    }

    override fun onCreate(db: SQLiteDatabase) {
        // Using the constants means a single change here fixes every reference
        db.execSQL("""
            CREATE TABLE $TABLE_CONTACTS (
                $COLUMN_ID INTEGER PRIMARY KEY AUTOINCREMENT,
                $COLUMN_NAME TEXT NOT NULL,
                $COLUMN_POSTCODE TEXT
            )
        """.trimIndent())

        db.execSQL("""
            CREATE TABLE $TABLE_NOTES (
                $COLUMN_NOTE_ID INTEGER PRIMARY KEY AUTOINCREMENT,
                $COLUMN_NOTE_TEXT TEXT,
                $COLUMN_CONTACT_FK INTEGER,
                FOREIGN KEY($COLUMN_CONTACT_FK) REFERENCES $TABLE_CONTACTS($COLUMN_ID)
            )
        """.trimIndent())
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS $TABLE_NOTES")
        db.execSQL("DROP TABLE IF EXISTS $TABLE_CONTACTS")
        onCreate(db)
    }
}
```

**Benefits of this approach:**

- A typo in the constant definition causes a **compile error**, not a **runtime crash**
- Renaming a column only requires changing one line
- IDE autocompletion helps you find the right constant name
- All database "magic strings" are gathered in one visible location

---

## Putting It All Together: A Complete Example

[↑ Back to Table of Contents](#table-of-contents)

### A Full Contacts Database App

The following is a self-contained example showing all CRUD operations working together. In a real Android app, the `DatabaseHelper` would use a `Context` from an `Activity` or `Application` — here we demonstrate the logic with a standalone runnable structure to illustrate all the concepts covered.

```kotlin
import android.content.ContentValues
import android.content.Context
import android.database.Cursor
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

// --- Data class to represent a Contact ---
data class Contact(
    val id: Int,
    val name: String,
    val postcode: String
)

// --- DatabaseHelper: manages creation and access to the contacts database ---
class ContactsDatabaseHelper(context: Context)
    : SQLiteOpenHelper(context, DATABASE_NAME, null, DATABASE_VERSION) {

    companion object {
        const val DATABASE_NAME    = "contacts.db"
        const val DATABASE_VERSION = 1

        const val TABLE_CONTACTS   = "Contacts"
        const val COLUMN_ID        = "Id"
        const val COLUMN_NAME      = "Name"
        const val COLUMN_POSTCODE  = "Postcode"
    }

    // Called once when the database is first created
    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL("""
            CREATE TABLE $TABLE_CONTACTS (
                $COLUMN_ID       INTEGER PRIMARY KEY AUTOINCREMENT,
                $COLUMN_NAME     TEXT NOT NULL,
                $COLUMN_POSTCODE TEXT
            )
        """.trimIndent())
    }

    // Called when the database version number increases
    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS $TABLE_CONTACTS")
        onCreate(db)
    }

    // CREATE - Insert a new contact. Returns the new row's ID.
    fun insertContact(name: String, postcode: String): Long {
        val db = writableDatabase
        val values = ContentValues().apply {
            put(COLUMN_NAME, name)
            put(COLUMN_POSTCODE, postcode)
        }
        val newRowId = db.insert(TABLE_CONTACTS, null, values)
        db.close()
        return newRowId
    }

    // READ - Get all contacts as a List<Contact>
    fun getAllContacts(): List<Contact> {
        val contacts = mutableListOf<Contact>()
        val db = readableDatabase

        val cursor: Cursor = db.query(
            TABLE_CONTACTS, null, null, null, null, null, null
        )

        if (cursor.moveToFirst()) {
            do {
                contacts.add(
                    Contact(
                        id       = cursor.getInt(cursor.getColumnIndex(COLUMN_ID)),
                        name     = cursor.getString(cursor.getColumnIndex(COLUMN_NAME)),
                        postcode = cursor.getString(cursor.getColumnIndex(COLUMN_POSTCODE))
                    )
                )
            } while (cursor.moveToNext())
        }

        cursor.close()
        db.close()
        return contacts
    }

    // READ - Get a single contact by ID. Returns null if not found.
    fun getContactById(id: Int): Contact? {
        val db = readableDatabase
        val cursor = db.query(
            TABLE_CONTACTS,
            null,
            "$COLUMN_ID = ?",   // WHERE Id = ?
            arrayOf(id.toString()),
            null, null, null
        )

        val contact = if (cursor.moveToFirst()) {
            Contact(
                id       = cursor.getInt(cursor.getColumnIndex(COLUMN_ID)),
                name     = cursor.getString(cursor.getColumnIndex(COLUMN_NAME)),
                postcode = cursor.getString(cursor.getColumnIndex(COLUMN_POSTCODE))
            )
        } else {
            null // No row matched the given ID
        }

        cursor.close()
        db.close()
        return contact
    }

    // UPDATE - Modify an existing contact. Returns number of rows updated.
    fun updateContact(id: Int, newName: String, newPostcode: String): Int {
        val db = writableDatabase
        val values = ContentValues().apply {
            put(COLUMN_NAME, newName)
            put(COLUMN_POSTCODE, newPostcode)
        }
        val rowsAffected = db.update(
            TABLE_CONTACTS,
            values,
            "$COLUMN_ID = ?",
            arrayOf(id.toString())
        )
        db.close()
        return rowsAffected
    }

    // DELETE - Remove a contact by ID. Returns number of rows deleted.
    fun deleteContact(id: Int): Int {
        val db = writableDatabase
        val rowsDeleted = db.delete(
            TABLE_CONTACTS,
            "$COLUMN_ID = ?",
            arrayOf(id.toString())
        )
        db.close()
        return rowsDeleted
    }
}
```

### How This Would Be Used in an Activity

```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private lateinit var dbHelper: ContactsDatabaseHelper

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Instantiate the helper - database is NOT yet opened at this point
        dbHelper = ContactsDatabaseHelper(this)

        // INSERT three contacts - database is opened here for the first time
        val id1 = dbHelper.insertContact("Andrew", "CH2 4NU")
        val id2 = dbHelper.insertContact("Sarah",  "M1 2AB")
        val id3 = dbHelper.insertContact("James",  "SW1A 1AA")

        // READ all contacts and log them
        val allContacts = dbHelper.getAllContacts()
        for (contact in allContacts) {
            println("Contact: $contact")
        }

        // UPDATE Andrew's postcode
        dbHelper.updateContact(id1.toInt(), "Andrew", "CH3 5PQ")

        // READ a single contact to verify the update
        val updated = dbHelper.getContactById(id1.toInt())
        println("Updated: $updated")

        // DELETE Sarah
        dbHelper.deleteContact(id2.toInt())

        // READ again to confirm deletion
        val remaining = dbHelper.getAllContacts()
        println("Remaining contacts: $remaining")
    }
}
```

**Expected output (in Logcat):**

```
Contact: Contact(id=1, name=Andrew, postcode=CH2 4NU)
Contact: Contact(id=2, name=Sarah, postcode=M1 2AB)
Contact: Contact(id=3, name=James, postcode=SW1A 1AA)

Updated: Contact(id=1, name=Andrew, postcode=CH3 5PQ)

Remaining contacts: [
    Contact(id=1, name=Andrew, postcode=CH3 5PQ),
    Contact(id=3, name=James, postcode=SW1A 1AA)
]
```

### How the App Screen Might Look

```
+---------------------------+
|  Contacts App             |
+---------------------------+
|                           |
|  [+] Add Contact          |
|                           |
|  +---------------------+  |
|  | Andrew   CH3 5PQ  ✏ |  |
|  +---------------------+  |
|  | James    SW1A 1AA ✏ |  |
|  +---------------------+  |
|                           |
|  (Sarah was deleted)      |
|                           |
+---------------------------+
```

---

[↑ Back to Table of Contents](#table-of-contents)
