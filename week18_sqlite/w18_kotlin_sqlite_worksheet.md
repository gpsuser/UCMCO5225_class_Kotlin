# Worksheet: SQLite Databases

---

## Section 1: Quiz

---

### Part A: Fill in the Blanks

For each question below, choose the correct word from the scrambled word bank to complete the sentence. Each word is used **once only**.

---

**Question 1**

Word bank (scrambled): `onUpgrade` &nbsp;|&nbsp; `execSQL` &nbsp;|&nbsp; `onCreate` &nbsp;|&nbsp; `abstract` &nbsp;|&nbsp; `DATABASE_VERSION`

`SQLiteOpenHelper` is an __________ class that you must subclass. The method __________ is called exactly once when the database is first created, while __________ is called whenever the __________ constant is incremented. To run SQL statements that do not return data — such as `CREATE TABLE` — you call __________ on the `SQLiteDatabase` object.

---

**Question 2**

Word bank (scrambled): `ContentValues` &nbsp;|&nbsp; `insert` &nbsp;|&nbsp; `null` &nbsp;|&nbsp; `Long` &nbsp;|&nbsp; `put`

To add a new row to a table, you first create a __________ object and call __________ on it for each column you want to set. You then pass this object to the __________ method on `SQLiteDatabase`. This method returns a __________ representing the new row's ID, or -1 on failure. When the values object is not empty, the second parameter (the "null column hack") should be passed as __________.

---

**Question 3**

Word bank (scrambled): `moveToNext` &nbsp;|&nbsp; `getColumnIndex` &nbsp;|&nbsp; `Cursor` &nbsp;|&nbsp; `close` &nbsp;|&nbsp; `moveToFirst`

Running a `query()` returns a __________ object rather than a direct list of results. You call __________ to position it at the first row — this returns `false` if the result set is empty. To read a value from a column, you first call __________ with the column name to get an integer index, then pass that index to a method such as `getString()`. You advance through subsequent rows using __________, and once you are finished, you must always call __________ to release resources.

---

### Part B: Multiple Choice

Circle or highlight the **one** best answer for each question.

---

**Question 4**

Which method would you use to retrieve a `SQLiteDatabase` instance when you intend to **read data only**?

- A) `getWritableDatabase()`
- B) `getReadableDatabase()`
- C) `openDatabase()`
- D) `readableDatabase()`

---

**Question 5**

Consider the following two approaches to building a `WHERE` clause:

```
// Approach A
"Id = $id"

// Approach B
"Id = ?"  with  arrayOf(id.toString())
```

Why is **Approach B** the recommended practice?

- A) It runs faster because the query is cached by Android.
- B) It prevents SQL injection by separating data from the SQL statement.
- C) It is required because `SQLiteDatabase` does not support string interpolation.
- D) It allows the column name to be changed without editing the query.

---

**Question 6**

A developer defines the following constants in a `DatabaseHelper`:

```kotlin
companion object {
    const val TABLE_CONTACTS = "Contacts"
    const val COLUMN_NAME    = "Name"
}
```

What is the **primary benefit** of using these constants instead of typing `"Contacts"` and `"Name"` directly in each method?

- A) Constants are stored in faster memory, improving database query speed.
- B) A typo in the constant definition produces a compile-time error rather than a hard-to-find runtime crash.
- C) SQLite requires constants for all table names to function correctly.
- D) Constants allow the database to be shared between multiple apps.

---

## Section 2: Coding Tasks

---

### Task 1 — Search Contacts by Name

The contacts database currently retrieves **all** contacts at once. Your task is to add a method called `searchByName` to the `ContactsDatabaseHelper` class that accepts a partial name string and returns only the contacts whose name **contains** that string (case-insensitive).

For example, searching for `"an"` should match both `"Andrew"` and `"Diane"`.

> **Hint:** In SQL, the `LIKE` operator with `%` wildcards performs a partial match:
> `WHERE Name LIKE '%an%'`

**Scaffolding:**

```kotlin
// Add this method inside ContactsDatabaseHelper

fun searchByName(partialName: String): List<Contact> {
    val contacts = mutableListOf<Contact>()
    val db = readableDatabase

    // TODO: build a query using a LIKE clause and the % wildcard
    // The selection argument should wrap partialName with % on both sides
    val selection = "$COLUMN_NAME LIKE ?"
    val selectionArgs = arrayOf(/* TODO: wrap partialName with % */)

    val cursor = db.query(
        TABLE_CONTACTS,
        null,
        selection,
        selectionArgs,
        null, null, null
    )

    if (cursor.moveToFirst()) {
        do {
            // TODO: read each row and add a Contact to the list
        } while (cursor.moveToNext())
    }

    cursor.close()
    db.close()
    return contacts
}
```

**Sample usage and expected output:**

```
dbHelper.insertContact("Andrew", "CH2 4NU")
dbHelper.insertContact("Sarah",  "M1 2AB")
dbHelper.insertContact("Diane",  "SW1A 1AA")

val results = dbHelper.searchByName("an")
// Expected:
// [Contact(id=1, name=Andrew, postcode=CH2 4NU),
//  Contact(id=3, name=Diane,  postcode=SW1A 1AA)]
```

---

### Task 2 — Count the Number of Contacts

Add a method called `getContactCount` to `ContactsDatabaseHelper` that returns the **total number of contacts** currently stored in the database as an `Int`.

> **Hint:** Use `rawQuery()` with the SQL statement `SELECT COUNT(*) FROM Contacts`. A `COUNT(*)` query returns a single row with a single column — retrieve it using `getInt(0)` on the cursor.

**Scaffolding:**

```kotlin
fun getContactCount(): Int {
    val db = readableDatabase

    // TODO: run a rawQuery using COUNT(*) and retrieve the integer result
    val cursor = db.rawQuery(/* TODO */, null)

    var count = 0
    if (cursor.moveToFirst()) {
        count = /* TODO: read the count value */
    }

    cursor.close()
    db.close()
    return count
}
```

**Sample usage and expected output:**

```
dbHelper.insertContact("Andrew", "CH2 4NU")
dbHelper.insertContact("Sarah",  "M1 2AB")

println(dbHelper.getContactCount())   // 2

dbHelper.insertContact("James", "SW1A 1AA")

println(dbHelper.getContactCount())   // 3
```

---

### Challenge Task — A Second Table with a Foreign Key

Extend `ContactsDatabaseHelper` to support a second table called `PhoneNumbers`. Each contact can have **multiple** phone numbers, and each phone number row must reference the contact it belongs to via a foreign key.

The schema for `PhoneNumbers` should be:

```
+------------+---------+-----------+
| PhoneId    | INTEGER | PK, AUTO  |
| ContactId  | INTEGER | FK -> Contacts.Id |
| PhoneNumber| TEXT    |           |
+------------+---------+-----------+
```

You must:

1. Add the table constants to the `companion object`.
2. Create the `PhoneNumbers` table in `onCreate` (after `Contacts`, since it has a foreign key dependency).
3. Drop `PhoneNumbers` before `Contacts` in `onUpgrade` (to respect foreign key order).
4. Implement `insertPhoneNumber(contactId: Int, phoneNumber: String): Long`.
5. Implement `getPhoneNumbersForContact(contactId: Int): List<String>` that returns all phone numbers for a given contact.

**Scaffolding:**

```kotlin
companion object {
    // ... existing constants ...

    // TODO: add constants for the PhoneNumbers table
    const val TABLE_PHONES       = "PhoneNumbers"
    const val COLUMN_PHONE_ID    = "PhoneId"
    const val COLUMN_CONTACT_FK  = "ContactId"
    const val COLUMN_PHONE_NUMBER = "PhoneNumber"
}

override fun onCreate(db: SQLiteDatabase) {
    // Contacts table (already exists — keep it)
    db.execSQL("""
        CREATE TABLE $TABLE_CONTACTS (
            $COLUMN_ID       INTEGER PRIMARY KEY AUTOINCREMENT,
            $COLUMN_NAME     TEXT NOT NULL,
            $COLUMN_POSTCODE TEXT
        )
    """.trimIndent())

    // TODO: create the PhoneNumbers table here, including the FOREIGN KEY constraint
}

override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
    // TODO: drop PhoneNumbers first, then Contacts
    onCreate(db)
}

fun insertPhoneNumber(contactId: Int, phoneNumber: String): Long {
    val db = writableDatabase
    // TODO: use ContentValues to insert a row into TABLE_PHONES
    db.close()
    return /* row id */
}

fun getPhoneNumbersForContact(contactId: Int): List<String> {
    val numbers = mutableListOf<String>()
    val db = readableDatabase
    // TODO: query TABLE_PHONES filtering by COLUMN_CONTACT_FK = contactId
    db.close()
    return numbers
}
```

**Sample usage and expected output:**

```
val id1 = dbHelper.insertContact("Andrew", "CH2 4NU")
val id2 = dbHelper.insertContact("Sarah",  "M1 2AB")

dbHelper.insertPhoneNumber(id1.toInt(), "07700 900123")
dbHelper.insertPhoneNumber(id1.toInt(), "07700 900456")
dbHelper.insertPhoneNumber(id2.toInt(), "07700 900789")

println(dbHelper.getPhoneNumbersForContact(id1.toInt()))
// Expected: [07700 900123, 07700 900456]

println(dbHelper.getPhoneNumbersForContact(id2.toInt()))
// Expected: [07700 900789]
```

---
