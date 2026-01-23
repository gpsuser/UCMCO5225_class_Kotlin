# Worksheet

## Quiz Section

### Question 1

Complete the following statement by filling in the blanks:

A __________ is a view group that displays a scrollable list of items. To populate it with data, we need an __________ which acts as a bridge between the data collection and the ListView. This adapter implements the __________ interface.

**Word Bank (scrambled):** `ListAdapter`, `ArrayAdapter`, `ListView`

---

### Question 2

Complete the following code snippet by filling in the blanks:

```kotlin
private __________ var adapter: ArrayAdapter<String>
    
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    
    adapter = ArrayAdapter(this, android.R.layout.simple_list_item_1, items)
    
    if (::adapter.__________) {
        println("Adapter is ready")
    }
}
```

**Word Bank (scrambled):** `isInitialized`, `lateinit`

---

### Question 3

Complete the following statement by filling in the blanks:

To enable long-press menus on a view, you must first call __________ in your activity's onCreate() method. When the user performs a long-press, the __________ method is called to inflate the menu. User selections are then handled in the __________ method.

**Word Bank (scrambled):** `onCreateContextMenu`, `registerForContextMenu`, `onContextItemSelected`

---

### Question 4

Which of the following statements about ArrayAdapter is TRUE?

A) ArrayAdapter can only work with List<String> collections  
B) ArrayAdapter automatically calls toString() on objects to display them in the ListView  
C) You must always create a custom adapter, you cannot use predefined Android layouts  
D) ArrayAdapter cannot be modified after creation

---

### Question 5

What is the primary purpose of the `lateinit` keyword in Kotlin?

A) To make a property immutable  
B) To allow delayed initialization of non-null properties  
C) To create nullable properties automatically  
D) To initialize primitive types with default values

---

### Question 6

Which method must you override to handle selections from the options menu (app bar menu)?

A) `onCreateMenu()`  
B) `onMenuSelected()`  
C) `onOptionsItemSelected()`  
D) `onContextItemSelected()`

---

## Task Section

### Task 1: Student Grade Manager

Create a simple student grade management system using a ListView. The app should display a list of students with their grades, and allow users to add new students via an options menu.

**Requirements:**
- Create a `Student` data class with `name` and `grade` properties
- Display students in a ListView using ArrayAdapter
- Override `toString()` to show "Name: Grade" format
- Implement an options menu with an "Add Student" option
- Use `lateinit` for the adapter

**Code Scaffolding:**

```kotlin
package com.example.grademanager

import android.os.Bundle
import android.view.Menu
import android.view.MenuItem
import android.widget.ArrayAdapter
import android.widget.ListView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

/**
 * Data class representing a student
 */
data class Student(
    val name: String,
    val grade: Int
) {
    // TODO: Override toString() to return "Name: Grade" format
}

class GradeManagerActivity : AppCompatActivity() {
    
    // TODO: Declare lateinit adapter variable
    
    private val students = mutableListOf(
        Student("Alice", 85),
        Student("Bob", 92),
        Student("Charlie", 78)
    )
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val listView = findViewById<ListView>(R.id.listView)
        
        // TODO: Initialize the adapter
        
        // TODO: Set adapter on ListView
    }
    
    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        // TODO: Add menu item programmatically
        // Hint: Use menu.add() to create "Add Student" option
        return true
    }
    
    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        // TODO: Handle menu selection
        // When selected, add a new student "David" with grade 88
        // Don't forget to notify the adapter!
        return true
    }
}
```

**Sample Output:**
```
Initial ListView display:
- Alice: 85
- Bob: 92
- Charlie: 78

After selecting "Add Student":
- Alice: 85
- Bob: 92
- Charlie: 78
- David: 88
```

---

### Task 2: Movie Rating System with Context Menu

Create a movie rating app where users can long-press on a movie to rate it or remove it from the list.

**Requirements:**
- Create a `Movie` data class with `title` and `rating` properties (rating can be null initially)
- Display movies in a ListView
- Implement a context menu with "Rate Movie" and "Remove Movie" options
- When "Rate Movie" is selected, set the rating to 5 stars
- When "Remove Movie" is selected, delete the movie from the list

**Code Scaffolding:**

```kotlin
package com.example.movierating

import android.os.Bundle
import android.view.ContextMenu
import android.view.MenuItem
import android.view.View
import android.widget.AdapterView
import android.widget.ArrayAdapter
import android.widget.ListView
import androidx.appcompat.app.AppCompatActivity

data class Movie(
    val title: String,
    var rating: Int? = null
) {
    override fun toString(): String {
        // TODO: Return "Title - ★★★★★" if rated, "Title - Not rated" if not
        return ""
    }
}

class MovieRatingActivity : AppCompatActivity() {
    
    private lateinit var adapter: ArrayAdapter<Movie>
    private val movies = mutableListOf(
        Movie("The Matrix"),
        Movie("Inception", 5),
        Movie("Interstellar")
    )
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val listView = findViewById<ListView>(R.id.listView)
        
        // TODO: Initialize adapter and attach to ListView
        
        // TODO: Register ListView for context menu
    }
    
    override fun onCreateContextMenu(
        menu: ContextMenu,
        v: View,
        menuInfo: ContextMenu.ContextMenuInfo?
    ) {
        super.onCreateContextMenu(menu, v, menuInfo)
        
        // TODO: Add two menu items: "Rate Movie" (id: 1) and "Remove Movie" (id: 2)
        // Hint: Use menu.add(groupId, itemId, order, title)
    }
    
    override fun onContextItemSelected(item: MenuItem): Boolean {
        val info = item.menuInfo as AdapterView.AdapterContextMenuInfo
        val position = info.position
        
        // TODO: Handle both menu options
        // Rate Movie: Set rating to 5
        // Remove Movie: Remove from list
        
        return true
    }
}
```

**Sample Output:**
```
Initial ListView display:
- The Matrix - Not rated
- Inception - ★★★★★
- Interstellar - Not rated

After long-press on "The Matrix" → Select "Rate Movie":
- The Matrix - ★★★★★
- Inception - ★★★★★
- Interstellar - Not rated

After long-press on "Inception" → Select "Remove Movie":
- The Matrix - ★★★★★
- Interstellar - Not rated
```

---

### Challenge Task: Contact Manager with Multiple Menus

Create a comprehensive contact management app that uses both options menus and context menus with multiple features.

**Requirements:**
- Create a `Contact` data class with `name`, `phone`, and `isFavorite` properties
- Display contacts in ListView (show ★ for favorites)
- **Options Menu** should have:
  - "Add Contact" - adds a new contact
  - "Show Favorites Only" - filters to show only favorites
  - "Show All" - shows all contacts
- **Context Menu** should have:
  - "Toggle Favorite" - adds/removes from favorites
  - "Call" - displays a toast with the phone number
  - "Delete" - removes the contact

**Code Scaffolding:**

```kotlin
package com.example.contactmanager

import android.os.Bundle
import android.view.ContextMenu
import android.view.Menu
import android.view.MenuItem
import android.view.View
import android.widget.AdapterView
import android.widget.ArrayAdapter
import android.widget.ListView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

data class Contact(
    val name: String,
    val phone: String,
    var isFavorite: Boolean = false
) {
    override fun toString(): String {
        // TODO: Return "★ Name (Phone)" if favorite, "Name (Phone)" if not
        return ""
    }
}

class ContactManagerActivity : AppCompatActivity() {
    
    private lateinit var adapter: ArrayAdapter<Contact>
    
    // Store all contacts
    private val allContacts = mutableListOf(
        Contact("Alice Smith", "555-0101", true),
        Contact("Bob Jones", "555-0102"),
        Contact("Charlie Brown", "555-0103", true),
        Contact("Diana Prince", "555-0104")
    )
    
    // Currently displayed contacts (for filtering)
    private val displayedContacts = mutableListOf<Contact>()
    
    private var showingFavoritesOnly = false
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // TODO: Copy all contacts to displayedContacts initially
        
        val listView = findViewById<ListView>(R.id.listView)
        
        // TODO: Initialize adapter with displayedContacts
        
        // TODO: Register for context menu
    }
    
    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        // TODO: Add three menu items with IDs 1, 2, 3
        // 1: "Add Contact"
        // 2: "Show Favorites Only"  
        // 3: "Show All"
        return true
    }
    
    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        return when (item.itemId) {
            1 -> {
                // TODO: Add new contact "Eve Wilson" with phone "555-0105"
                // Add to allContacts
                // If showing all, also add to displayedContacts
                true
            }
            2 -> {
                // TODO: Filter to show only favorites
                // Clear displayedContacts
                // Add only favorites from allContacts
                // Set showingFavoritesOnly = true
                true
            }
            3 -> {
                // TODO: Show all contacts
                // Clear displayedContacts
                // Add all from allContacts
                // Set showingFavoritesOnly = false
                true
            }
            else -> super.onOptionsItemSelected(item)
        }
    }
    
    override fun onCreateContextMenu(
        menu: ContextMenu,
        v: View,
        menuInfo: ContextMenu.ContextMenuInfo?
    ) {
        super.onCreateContextMenu(menu, v, menuInfo)
        
        // TODO: Add three context menu items with IDs 101, 102, 103
        // 101: "Toggle Favorite"
        // 102: "Call"
        // 103: "Delete"
    }
    
    override fun onContextItemSelected(item: MenuItem): Boolean {
        val info = item.menuInfo as AdapterView.AdapterContextMenuInfo
        val position = info.position
        val contact = displayedContacts[position]
        
        return when (item.itemId) {
            101 -> {
                // TODO: Toggle favorite status
                // Update both displayedContacts and allContacts
                true
            }
            102 -> {
                // TODO: Show toast "Calling [phone]"
                true
            }
            103 -> {
                // TODO: Remove from both displayedContacts and allContacts
                true
            }
            else -> super.onContextItemSelected(item)
        }
    }
}
```

**Sample Output:**
```
Initial display (Show All):
- ★ Alice Smith (555-0101)
- Bob Jones (555-0102)
- ★ Charlie Brown (555-0103)
- Diana Prince (555-0104)

After selecting "Show Favorites Only":
- ★ Alice Smith (555-0101)
- ★ Charlie Brown (555-0103)

After long-press on "Bob Jones" → "Toggle Favorite":
- ★ Bob Jones (555-0102)

After long-press on "Alice Smith" → "Call":
Toast displays: "Calling 555-0101"

After selecting "Add Contact":
New contact "Eve Wilson (555-0105)" appears in list
```
