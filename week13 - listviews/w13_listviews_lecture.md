# ListViews, Menus, and Context Menus in Android

## Table of Contents

1. [Learning Outcomes](#learning-outcomes)
2. [Introduction to ListViews](#introduction-to-listviews)
3. [Understanding ArrayAdapter](#understanding-arrayadapter)
4. [Working with the lateinit Keyword](#working-with-the-lateinit-keyword)
5. [Creating and Handling Menus](#creating-and-handling-menus)
6. [Implementing Context Menus](#implementing-context-menus)
7. [Practical Application: Complete ListView App](#practical-application-complete-listview-app)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain what a ListView is and when to use it in Android applications
- Create and configure ArrayAdapters to populate ListViews with data
- Use the `lateinit` keyword to handle delayed initialization in Kotlin
- Implement options menus in Android activities
- Create and handle context menus for long-press interactions
- Build a complete Android application that integrates ListViews with menu functionality

---

## Introduction to ListViews

### What is a ListView?

A ListView is a view group that displays a scrollable list of items. It's one of the fundamental UI components in Android for presenting collections of data to users. While RecyclerView is often preferred for more complex scenarios, ListView remains a straightforward choice for simple list displays.

### When to Use ListView

ListView is appropriate when you need to:
- Display a simple, scrollable list of items
- Show data that fits well into a linear, vertical format
- Implement basic list functionality without complex layout requirements

### Mobile Device Representation

```
┌─────────────────────┐
│  Shopping List  ☰   │ ← App Bar with Menu
├─────────────────────┤
│  Apples             │
├─────────────────────┤
│  Bananas            │
├─────────────────────┤
│  Carrots            │
├─────────────────────┤
│  Dates              │
├─────────────────────┤
│  Eggs               │
├─────────────────────┤
│  ↓ Scrollable ↓     │
└─────────────────────┘
```

### Data Storage for ListViews

ListViews typically work with ordered collections such as:
- `MutableList<String>` (most common for simple cases)
- `ArrayList<T>` (Java collection type)
- `List<T>` (immutable version)

**Important Concept**: To display items in a ListView, we need an adapter that implements the `ListAdapter` interface. The most commonly used implementation is `ArrayAdapter`.

---

## Understanding ArrayAdapter

### What is an ArrayAdapter?

An ArrayAdapter is a bridge between your data (a collection) and the ListView that displays it. It handles the creation and recycling of view items as the user scrolls through the list.

### Key ArrayAdapter Concepts

1. **Generic Type**: The adapter has a type parameter that must match your collection type
2. **Layout Selection**: ArrayAdapter uses a layout template that's repeated for each item
3. **Automatic String Conversion**: If items aren't strings, the adapter calls `toString()` on objects

### Predefined Android Layouts

Android provides several built-in layouts for ListViews:

- `android.R.layout.simple_list_item_1` - A single TextView (most common)
- `android.R.layout.simple_list_item_2` - Two TextViews stacked vertically

### Visual Representation

```
Simple List Item 1 Layout:
┌─────────────────────┐
│  Item Text          │
└─────────────────────┘

Simple List Item 2 Layout:
┌─────────────────────┐
│  Primary Text       │
│  Secondary Text     │
└─────────────────────┘
```

### Complete ArrayAdapter Example

Here's a complete Android activity demonstrating basic ArrayAdapter usage:

```kotlin
package com.example.listviewdemo

import android.os.Bundle
import android.widget.ArrayAdapter
import android.widget.ListView
import androidx.appcompat.app.AppCompatActivity

/**
 * Simple ListView Activity demonstrating ArrayAdapter usage
 * This example displays a list of programming languages
 */
class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Create the data source - a mutable list of strings
        val programmingLanguages = mutableListOf(
            "Kotlin",
            "Java",
            "Python",
            "JavaScript",
            "Swift",
            "C++",
            "Ruby",
            "Go",
            "Rust",
            "TypeScript"
        )
        
        // Find the ListView from the layout
        val listView = findViewById<ListView>(R.id.listView)
        
        // Create the ArrayAdapter
        // Parameters: context, layout resource, data list
        val adapter = ArrayAdapter(
            this,                                    // Context (this activity)
            android.R.layout.simple_list_item_1,    // Layout for each item
            programmingLanguages                     // Data source
        )
        
        // Attach the adapter to the ListView
        listView.adapter = adapter
        
        // Optional: Set an item click listener
        listView.setOnItemClickListener { parent, view, position, id ->
            val selectedItem = programmingLanguages[position]
            println("Clicked on: $selectedItem at position $position")
        }
    }
}
```

**Sample Output** (when user clicks on "Python"):
```
Clicked on: Python at position 2
```

### Working with Custom Objects

When displaying custom objects, the ArrayAdapter calls `toString()`:

```kotlin
package com.example.listviewdemo

import android.os.Bundle
import android.widget.ArrayAdapter
import android.widget.ListView
import androidx.appcompat.app.AppCompatActivity

/**
 * Data class representing a book
 */
data class Book(
    val title: String,
    val author: String,
    val year: Int
) {
    // Override toString() to control how the book appears in the ListView
    override fun toString(): String {
        return "$title by $author ($year)"
    }
}

/**
 * Activity demonstrating ListView with custom objects
 */
class BookListActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Create a list of Book objects
        val books = mutableListOf(
            Book("1984", "George Orwell", 1949),
            Book("To Kill a Mockingbird", "Harper Lee", 1960),
            Book("The Great Gatsby", "F. Scott Fitzgerald", 1925),
            Book("Pride and Prejudice", "Jane Austen", 1813),
            Book("The Catcher in the Rye", "J.D. Salinger", 1951)
        )
        
        // Find the ListView
        val listView = findViewById<ListView>(R.id.listView)
        
        // Create and set the adapter
        val adapter = ArrayAdapter(
            this,
            android.R.layout.simple_list_item_1,
            books  // The adapter will call toString() on each Book object
        )
        
        listView.adapter = adapter
        
        // Handle item clicks
        listView.setOnItemClickListener { _, _, position, _ ->
            val selectedBook = books[position]
            println("Selected: ${selectedBook.title} by ${selectedBook.author}")
        }
    }
}
```

**Sample Output**:
```
ListView displays:
- 1984 by George Orwell (1949)
- To Kill a Mockingbird by Harper Lee (1960)
- The Great Gatsby by F. Scott Fitzgerald (1925)
- Pride and Prejudice by Jane Austen (1813)
- The Catcher in the Rye by J.D. Salinger (1951)

When user clicks on second item:
Selected: To Kill a Mockingbird by Harper Lee
```

### Important Notes About ArrayAdapter

- For layouts with more than a single TextView, you'll need to create a custom adapter by subclassing `ArrayAdapter` and overriding `getView()`
- The adapter type parameter must match the collection type exactly
- ArrayAdapter provides methods like `add()`, `remove()`, `clear()` for modifying the list

---

## Working with the lateinit Keyword

### The Problem: Kotlin's Initialization Requirements

Kotlin enforces strict null-safety by requiring that all properties be initialized either:
- At declaration time
- In the constructor
- In an `init` block

However, this creates a challenge in Android development where some objects cannot be initialized until the `onCreate()` method runs.

### The Challenge in Android

Consider this common Android scenario:

```kotlin
class MainActivity : AppCompatActivity() {
    
    // ERROR: Property must be initialized or be abstract
    private var adapter: ArrayAdapter<String>
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // We can only create the adapter here, after setContentView()
        adapter = ArrayAdapter(this, android.R.layout.simple_list_item_1, myList)
    }
}
```

### Traditional Solution: Nullable Types

One solution is to make the property nullable:

```kotlin
private var adapter: ArrayAdapter<String>? = null
```

But this forces you to use null-safe operators (`?.` or `!!`) every time you access the property, even though you know it will have a value when used.

### The lateinit Solution

The `lateinit` keyword allows you to declare a non-null property without initializing it immediately. You're essentially "promising" Kotlin that you'll initialize it before its first use.

### lateinit Syntax and Rules

```kotlin
private lateinit var adapter: ArrayAdapter<String>
```

**Rules for lateinit**:
1. Can only be used with `var` (not `val`)
2. Can only be used with non-null types
3. Cannot be used with primitive types (Int, Boolean, etc.)
4. Property must be initialized before first use, or a runtime exception occurs

### Complete lateinit Example

```kotlin
package com.example.lateinitdemo

import android.os.Bundle
import android.widget.ArrayAdapter
import android.widget.Button
import android.widget.ListView
import androidx.appcompat.app.AppCompatActivity

/**
 * Activity demonstrating proper use of lateinit keyword
 * Shows how to delay initialization of adapter until onCreate()
 */
class LateinitDemoActivity : AppCompatActivity() {
    
    // Declare adapter with lateinit - we'll initialize it in onCreate()
    private lateinit var adapter: ArrayAdapter<String>
    
    // Store the data list
    private val items = mutableListOf("Item 1", "Item 2", "Item 3")
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Find views
        val listView = findViewById<ListView>(R.id.listView)
        val addButton = findViewById<Button>(R.id.addButton)
        
        // NOW we can initialize the adapter (after setContentView is called)
        adapter = ArrayAdapter(
            this,
            android.R.layout.simple_list_item_1,
            items
        )
        
        // Set the adapter on the ListView
        listView.adapter = adapter
        
        // Use the adapter in button click handler
        // No need for null checks because we used lateinit
        addButton.setOnClickListener {
            addNewItem()
        }
    }
    
    /**
     * Adds a new item to the list
     * Notice we can use 'adapter' directly without null checks
     */
    private fun addNewItem() {
        val newItemNumber = items.size + 1
        items.add("Item $newItemNumber")
        
        // Notify the adapter that data has changed
        // No null-safe operator needed!
        adapter.notifyDataSetChanged()
        
        println("Added: Item $newItemNumber. Total items: ${items.size}")
    }
    
    /**
     * Optional: Check if lateinit property has been initialized
     */
    private fun isAdapterInitialized(): Boolean {
        return ::adapter.isInitialized
    }
}
```

**Sample Output** (when user clicks add button three times):
```
Added: Item 4. Total items: 4
Added: Item 5. Total items: 5
Added: Item 6. Total items: 6
```

### Checking if lateinit is Initialized

You can check if a `lateinit` property has been initialized:

```kotlin
package com.example.lateinitdemo

/**
 * Demonstrates checking lateinit initialization status
 */
class LateinitCheckDemo {
    
    private lateinit var name: String
    
    fun demonstrateCheck() {
        // Check if the property is initialized
        if (::name.isInitialized) {
            println("Name is: $name")
        } else {
            println("Name has not been initialized yet")
        }
        
        // Initialize the property
        name = "Kotlin"
        
        // Check again
        if (::name.isInitialized) {
            println("Name is now: $name")
        }
    }
}

/**
 * Entry point for demonstration
 */
fun main() {
    val demo = LateinitCheckDemo()
    demo.demonstrateCheck()
}
```

**Sample Output**:
```
Name has not been initialized yet
Name is now: Kotlin
```

### When to Use lateinit

Use `lateinit` when:
- You cannot initialize a property in the constructor
- You need to initialize it in a lifecycle method (like `onCreate()`)
- You know the property will be initialized before first use
- You want to avoid nullable types and null-safety operators

**Do not use lateinit** when:
- The property might legitimately be null
- You're working with primitive types (use lazy delegation instead)
- You're unsure when initialization will occur

---

## Creating and Handling Menus

### Understanding Android Menus

Menus provide a consistent interface for accessing app functionality. The options menu (also called the app bar menu) appears in the app bar and typically contains actions relevant to the current screen.

### Menu Visual Representation

```
┌─────────────────────┐
│  My App        ⋮    │ ← Three-dot menu icon
├─────────────────────┤
│                     │
│  Main Content       │
│                     │
└─────────────────────┘

When menu icon clicked:
┌─────────────────────┐
│  My App        ⋮    │
├─────────────────────┤
│  ┌───────────────┐  │
│  │ Add Item      │  │
│  │ Settings      │  │
│  │ About         │  │
│  └───────────────┘  │
│                     │
└─────────────────────┘
```

### Creating a Menu Resource File

Menus are defined in XML resource files. Android Studio's Basic Activity template provides a menu template, but you can create your own.

**File: res/menu/menu_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    
    <!-- Menu item that shows in the app bar -->
    <item
        android:id="@+id/menu_add"
        android:title="@string/menu_add"
        android:icon="@android:drawable/ic_menu_add"
        app:showAsAction="ifRoom" />
    
    <!-- Menu item that appears in overflow menu -->
    <item
        android:id="@+id/menu_delete_all"
        android:title="@string/menu_delete_all" />
    
    <item
        android:id="@+id/menu_settings"
        android:title="@string/menu_settings" />
    
    <item
        android:id="@+id/menu_about"
        android:title="@string/menu_about" />
</menu>
```

### Handling Menu Selections

To respond to menu item selections, override the `onOptionsItemSelected()` method:

```kotlin
package com.example.menusdemo

import android.os.Bundle
import android.view.Menu
import android.view.MenuItem
import android.widget.ArrayAdapter
import android.widget.ListView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

/**
 * Activity demonstrating options menu implementation
 * Shows how to create and handle menu items in the app bar
 */
class MenuDemoActivity : AppCompatActivity() {
    
    private lateinit var adapter: ArrayAdapter<String>
    private val items = mutableListOf<String>()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Initialize some sample data
        items.addAll(listOf("Apple", "Banana", "Cherry", "Date", "Elderberry"))
        
        // Set up ListView with adapter
        val listView = findViewById<ListView>(R.id.listView)
        adapter = ArrayAdapter(
            this,
            android.R.layout.simple_list_item_1,
            items
        )
        listView.adapter = adapter
    }
    
    /**
     * Inflate the menu resource file
     * This method is called by the Android framework
     */
    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        menuInflater.inflate(R.menu.menu_main, menu)
        return true
    }
    
    /**
     * Handle menu item selections
     * Return true when you've handled an item, false otherwise
     */
    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        // Use when expression to handle different menu items
        return when (item.itemId) {
            R.id.menu_add -> {
                handleAddItem()
                true  // Indicate we've handled this item
            }
            R.id.menu_delete_all -> {
                handleDeleteAll()
                true
            }
            R.id.menu_settings -> {
                handleSettings()
                true
            }
            R.id.menu_about -> {
                handleAbout()
                true
            }
            else -> {
                // If we haven't handled it, pass to parent class
                super.onOptionsItemSelected(item)
            }
        }
    }
    
    /**
     * Handle the "Add Item" menu action
     */
    private fun handleAddItem() {
        val newItem = "Item ${items.size + 1}"
        items.add(newItem)
        adapter.notifyDataSetChanged()
        
        Toast.makeText(this, "Added: $newItem", Toast.LENGTH_SHORT).show()
        println("Menu: Added new item - $newItem")
    }
    
    /**
     * Handle the "Delete All" menu action
     */
    private fun handleDeleteAll() {
        val count = items.size
        items.clear()
        adapter.notifyDataSetChanged()
        
        Toast.makeText(this, "Deleted $count items", Toast.LENGTH_SHORT).show()
        println("Menu: Deleted all $count items")
    }
    
    /**
     * Handle the "Settings" menu action
     */
    private fun handleSettings() {
        Toast.makeText(this, "Settings clicked", Toast.LENGTH_SHORT).show()
        println("Menu: Settings option selected")
        // In a real app, you would launch a settings activity here
    }
    
    /**
     * Handle the "About" menu action
     */
    private fun handleAbout() {
        Toast.makeText(this, "About clicked", Toast.LENGTH_SHORT).show()
        println("Menu: About option selected")
        // In a real app, you might show an about dialog here
    }
}
```

**Sample Output** (when user selects different menu items):
```
Menu: Added new item - Item 6
Menu: Settings option selected
Menu: About option selected
Menu: Deleted all 6 items
```

### Menu Item Properties

Key attributes for menu items:

- `android:id` - Unique identifier used in `onOptionsItemSelected()`
- `android:title` - Text displayed to the user
- `android:icon` - Optional icon for the menu item
- `app:showAsAction` - Controls if/how item appears in app bar
  - `ifRoom` - Show in app bar if space available
  - `always` - Always show in app bar
  - `never` - Always show in overflow menu

---

## Implementing Context Menus

### What are Context Menus?

Context menus (also called contextual menus) appear when a user performs a long-press on a view. They provide actions relevant to the selected item.

### Context Menu Visual Representation

```
Before long-press:
┌─────────────────────┐
│  Shopping List      │
├─────────────────────┤
│  Apples             │ ← User long-presses here
├─────────────────────┤
│  Bananas            │
├─────────────────────┤
│  Carrots            │
└─────────────────────┘

After long-press:
┌─────────────────────┐
│  Shopping List      │
├─────────────────────┤
│  ┌───────────────┐  │
│  │ Edit          │  │
│  │ Delete        │  │
│  │ Share         │  │
│  └───────────────┘  │
│  Bananas            │
├─────────────────────┤
│  Carrots            │
└─────────────────────┘
```

### Step 1: Create the Context Menu Resource

**File: res/menu/context_menu_list.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    
    <item
        android:id="@+id/menu_edit"
        android:title="@string/edit" />
    
    <item
        android:id="@+id/menu_delete"
        android:title="@string/delete" />
    
    <item
        android:id="@+id/menu_share"
        android:title="@string/share" />
    
</menu>
```

### Step 2: Register the View for Context Menu

Register the view in your activity's `onCreate()` method:

```kotlin
registerForContextMenu(listView)
```

### Step 3: Implement Context Menu Methods

### Complete Context Menu Example

```kotlin
package com.example.contextmenudemo

import android.os.Bundle
import android.view.ContextMenu
import android.view.MenuItem
import android.view.View
import android.widget.AdapterView
import android.widget.ArrayAdapter
import android.widget.ListView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

/**
 * Activity demonstrating context menu implementation
 * Shows how to create and handle long-press menus on ListView items
 */
class ContextMenuActivity : AppCompatActivity() {
    
    private lateinit var adapter: ArrayAdapter<String>
    private val groceryItems = mutableListOf(
        "Apples",
        "Bananas",
        "Carrots",
        "Dates",
        "Eggs",
        "Flour",
        "Grapes"
    )
    
    // Store the position of the item that was long-pressed
    private var selectedPosition: Int = -1
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Set up ListView
        val listView = findViewById<ListView>(R.id.listView)
        adapter = ArrayAdapter(
            this,
            android.R.layout.simple_list_item_1,
            groceryItems
        )
        listView.adapter = adapter
        
        // Register the ListView for context menus
        // This enables long-press functionality
        registerForContextMenu(listView)
        
        println("Context menu registered for ListView")
    }
    
    /**
     * Called when user long-presses on a registered view
     * This is where we inflate the context menu
     */
    override fun onCreateContextMenu(
        menu: ContextMenu,
        v: View,
        menuInfo: ContextMenu.ContextMenuInfo?
    ) {
        super.onCreateContextMenu(menu, v, menuInfo)
        
        // Get information about the item that was long-pressed
        val info = menuInfo as AdapterView.AdapterContextMenuInfo
        selectedPosition = info.position
        
        // Get the item text to show in menu title
        val itemName = groceryItems[selectedPosition]
        
        // Set a title for the context menu (optional)
        menu.setHeaderTitle("Options for $itemName")
        
        // Inflate the context menu from XML
        menuInflater.inflate(R.menu.context_menu_list, menu)
        
        println("Context menu created for: $itemName at position $selectedPosition")
    }
    
    /**
     * Called when user selects an item from the context menu
     * Return true if you've handled the selection, false otherwise
     */
    override fun onContextItemSelected(item: MenuItem): Boolean {
        // Get information about which list item was long-pressed
        val info = item.menuInfo as AdapterView.AdapterContextMenuInfo
        val position = info.position
        val itemName = groceryItems[position]
        
        // Handle different menu options
        return when (item.itemId) {
            R.id.menu_edit -> {
                handleEdit(position, itemName)
                true
            }
            R.id.menu_delete -> {
                handleDelete(position, itemName)
                true
            }
            R.id.menu_share -> {
                handleShare(position, itemName)
                true
            }
            else -> {
                super.onContextItemSelected(item)
            }
        }
    }
    
    /**
     * Handle the "Edit" context menu option
     */
    private fun handleEdit(position: Int, itemName: String) {
        // In a real app, you might open a dialog or new screen to edit
        val editedName = "$itemName (edited)"
        groceryItems[position] = editedName
        adapter.notifyDataSetChanged()
        
        Toast.makeText(this, "Edited: $itemName", Toast.LENGTH_SHORT).show()
        println("Context menu: Edited item at position $position to '$editedName'")
    }
    
    /**
     * Handle the "Delete" context menu option
     */
    private fun handleDelete(position: Int, itemName: String) {
        groceryItems.removeAt(position)
        adapter.notifyDataSetChanged()
        
        Toast.makeText(this, "Deleted: $itemName", Toast.LENGTH_SHORT).show()
        println("Context menu: Deleted '$itemName' at position $position")
    }
    
    /**
     * Handle the "Share" context menu option
     */
    private fun handleShare(position: Int, itemName: String) {
        // In a real app, you might use an Intent to share via other apps
        Toast.makeText(this, "Sharing: $itemName", Toast.LENGTH_SHORT).show()
        println("Context menu: Sharing '$itemName'")
    }
}
```

**Sample Output** (when user long-presses on "Bananas" and selects Delete):
```
Context menu registered for ListView
Context menu created for: Bananas at position 1
Context menu: Deleted 'Bananas' at position 1
```

### Context Menu with Multiple Views

If you have multiple views registered for context menus, you can determine which view triggered the menu:

```kotlin
package com.example.contextmenudemo

import android.os.Bundle
import android.view.ContextMenu
import android.view.MenuItem
import android.view.View
import android.widget.ListView
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

/**
 * Demonstrates handling context menus for multiple views
 */
class MultipleContextMenuActivity : AppCompatActivity() {
    
    private lateinit var listView: ListView
    private lateinit var headerText: TextView
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        listView = findViewById(R.id.listView)
        headerText = findViewById(R.id.headerText)
        
        // Register both views for context menus
        registerForContextMenu(listView)
        registerForContextMenu(headerText)
    }
    
    /**
     * Inflate different menus based on which view was long-pressed
     */
    override fun onCreateContextMenu(
        menu: ContextMenu,
        v: View,
        menuInfo: ContextMenu.ContextMenuInfo?
    ) {
        super.onCreateContextMenu(menu, v, menuInfo)
        
        // Check which view triggered the context menu
        when (v.id) {
            R.id.listView -> {
                menu.setHeaderTitle("List Options")
                menuInflater.inflate(R.menu.context_menu_list, menu)
                println("Context menu created for ListView")
            }
            R.id.headerText -> {
                menu.setHeaderTitle("Header Options")
                menuInflater.inflate(R.menu.context_menu_header, menu)
                println("Context menu created for Header TextView")
            }
        }
    }
    
    override fun onContextItemSelected(item: MenuItem): Boolean {
        // Handle menu items for different views
        println("Context menu item selected: ${item.title}")
        return super.onContextItemSelected(item)
    }
}
```

### Important Context Menu Concepts

1. **AdapterContextMenuInfo**: Provides information about which item in an AdapterView (like ListView) was long-pressed
   - `position`: The position of the item in the adapter
   - `id`: The row ID of the item

2. **Menu Header**: You can set a title for the context menu using `menu.setHeaderTitle()`

3. **Multiple Menus**: You can have different context menus for different views by checking the view ID in `onCreateContextMenu()`

---

## Practical Application: Complete ListView App

Let's bring everything together in a comprehensive example that demonstrates ListViews, ArrayAdapter, lateinit, options menus, and context menus.

```kotlin
package com.example.completelistviewapp

import android.os.Bundle
import android.view.ContextMenu
import android.view.Menu
import android.view.MenuItem
import android.view.View
import android.widget.*
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity

/**
 * Data class representing a task in our to-do list
 */
data class Task(
    var title: String,
    var isCompleted: Boolean = false
) {
    override fun toString(): String {
        val status = if (isCompleted) "✓" else "○"
        return "$status $title"
    }
}

/**
 * Complete task management application demonstrating:
 * - ListView with ArrayAdapter
 * - lateinit keyword for delayed initialization
 * - Options menu (app bar menu)
 * - Context menu (long-press menu)
 */
class TaskManagerActivity : AppCompatActivity() {
    
    // Use lateinit because we can't initialize until onCreate()
    private lateinit var adapter: ArrayAdapter<Task>
    
    // Task list that will be displayed
    private val tasks = mutableListOf<Task>()
    
    // Store position for context menu operations
    private var selectedTaskPosition: Int = -1
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Initialize with some sample tasks
        tasks.addAll(
            listOf(
                Task("Buy groceries"),
                Task("Finish homework", isCompleted = true),
                Task("Call dentist"),
                Task("Prepare presentation"),
                Task("Exercise")
            )
        )
        
        // Set up the ListView
        setupListView()
        
        println("TaskManager initialized with ${tasks.size} tasks")
    }
    
    /**
     * Initialize the ListView with adapter and listeners
     */
    private fun setupListView() {
        val listView = findViewById<ListView>(R.id.listView)
        
        // Initialize the adapter (this is why we needed lateinit)
        adapter = ArrayAdapter(
            this,
            android.R.layout.simple_list_item_1,
            tasks
        )
        
        // Attach adapter to ListView
        listView.adapter = adapter
        
        // Register for context menu (long-press)
        registerForContextMenu(listView)
        
        // Handle regular clicks (toggle completion)
        listView.setOnItemClickListener { _, _, position, _ ->
            toggleTaskCompletion(position)
        }
    }
    
    // ============ Options Menu Implementation ============
    
    /**
     * Inflate the options menu
     */
    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        menuInflater.inflate(R.menu.menu_main, menu)
        return true
    }
    
    /**
     * Handle options menu selections
     */
    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        return when (item.itemId) {
            R.id.menu_add_task -> {
                showAddTaskDialog()
                true
            }
            R.id.menu_delete_completed -> {
                deleteCompletedTasks()
                true
            }
            R.id.menu_delete_all -> {
                deleteAllTasks()
                true
            }
            R.id.menu_statistics -> {
                showStatistics()
                true
            }
            else -> super.onOptionsItemSelected(item)
        }
    }
    
    // ============ Context Menu Implementation ============
    
    /**
     * Create context menu for ListView items
     */
    override fun onCreateContextMenu(
        menu: ContextMenu,
        v: View,
        menuInfo: ContextMenu.ContextMenuInfo?
    ) {
        super.onCreateContextMenu(menu, v, menuInfo)
        
        // Get the position of the long-pressed item
        val info = menuInfo as AdapterView.AdapterContextMenuInfo
        selectedTaskPosition = info.position
        
        // Get task details for menu title
        val task = tasks[selectedTaskPosition]
        menu.setHeaderTitle(task.title)
        
        // Inflate the context menu
        menuInflater.inflate(R.menu.context_menu_task, menu)
        
        // Customize menu based on task state
        val completeItem = menu.findItem(R.id.menu_mark_complete)
        val incompleteItem = menu.findItem(R.id.menu_mark_incomplete)
        
        // Show appropriate option based on current state
        completeItem.isVisible = !task.isCompleted
        incompleteItem.isVisible = task.isCompleted
    }
    
    /**
     * Handle context menu selections
     */
    override fun onContextItemSelected(item: MenuItem): Boolean {
        val info = item.menuInfo as AdapterView.AdapterContextMenuInfo
        val position = info.position
        
        return when (item.itemId) {
            R.id.menu_edit -> {
                editTask(position)
                true
            }
            R.id.menu_mark_complete -> {
                markTaskComplete(position)
                true
            }
            R.id.menu_mark_incomplete -> {
                markTaskIncomplete(position)
                true
            }
            R.id.menu_delete -> {
                deleteTask(position)
                true
            }
            else -> super.onContextItemSelected(item)
        }
    }
    
    // ============ Task Management Functions ============
    
    /**
     * Toggle task completion status on regular click
     */
    private fun toggleTaskCompletion(position: Int) {
        val task = tasks[position]
        task.isCompleted = !task.isCompleted
        adapter.notifyDataSetChanged()
        
        val status = if (task.isCompleted) "completed" else "incomplete"
        Toast.makeText(this, "Marked as $status", Toast.LENGTH_SHORT).show()
        println("Toggled task: ${task.title} - now $status")
    }
    
    /**
     * Show dialog to add a new task
     */
    private fun showAddTaskDialog() {
        val input = EditText(this)
        input.hint = "Enter task title"
        
        AlertDialog.Builder(this)
            .setTitle("Add New Task")
            .setView(input)
            .setPositiveButton("Add") { _, _ ->
                val taskTitle = input.text.toString().trim()
                if (taskTitle.isNotEmpty()) {
                    addTask(taskTitle)
                }
            }
            .setNegativeButton("Cancel", null)
            .show()
    }
    
    /**
     * Add a new task to the list
     */
    private fun addTask(title: String) {
        val newTask = Task(title)
        tasks.add(newTask)
        adapter.notifyDataSetChanged()
        
        Toast.makeText(this, "Task added", Toast.LENGTH_SHORT).show()
        println("Added new task: $title (Total: ${tasks.size})")
    }
    
    /**
     * Edit an existing task
     */
    private fun editTask(position: Int) {
        val task = tasks[position]
        val input = EditText(this)
        input.setText(task.title)
        
        AlertDialog.Builder(this)
            .setTitle("Edit Task")
            .setView(input)
            .setPositiveButton("Save") { _, _ ->
                val newTitle = input.text.toString().trim()
                if (newTitle.isNotEmpty()) {
                    task.title = newTitle
                    adapter.notifyDataSetChanged()
                    Toast.makeText(this, "Task updated", Toast.LENGTH_SHORT).show()
                    println("Updated task at position $position to: $newTitle")
                }
            }
            .setNegativeButton("Cancel", null)
            .show()
    }
    
    /**
     * Mark a task as complete
     */
    private fun markTaskComplete(position: Int) {
        tasks[position].isCompleted = true
        adapter.notifyDataSetChanged()
        Toast.makeText(this, "Task completed", Toast.LENGTH_SHORT).show()
        println("Marked task as complete: ${tasks[position].title}")
    }
    
    /**
     * Mark a task as incomplete
     */
    private fun markTaskIncomplete(position: Int) {
        tasks[position].isCompleted = false
        adapter.notifyDataSetChanged()
        Toast.makeText(this, "Task marked incomplete", Toast.LENGTH_SHORT).show()
        println("Marked task as incomplete: ${tasks[position].title}")
    }
    
    /**
     * Delete a specific task
     */
    private fun deleteTask(position: Int) {
        val taskTitle = tasks[position].title
        tasks.removeAt(position)
        adapter.notifyDataSetChanged()
        
        Toast.makeText(this, "Task deleted", Toast.LENGTH_SHORT).show()
        println("Deleted task: $taskTitle (Remaining: ${tasks.size})")
    }
    
    /**
     * Delete all completed tasks
     */
    private fun deleteCompletedTasks() {
        val initialSize = tasks.size
        tasks.removeAll { it.isCompleted }
        adapter.notifyDataSetChanged()
        
        val deletedCount = initialSize - tasks.size
        Toast.makeText(this, "Deleted $deletedCount completed tasks", Toast.LENGTH_SHORT).show()
        println("Deleted $deletedCount completed tasks (Remaining: ${tasks.size})")
    }
    
    /**
     * Delete all tasks
     */
    private fun deleteAllTasks() {
        AlertDialog.Builder(this)
            .setTitle("Delete All Tasks")
            .setMessage("Are you sure you want to delete all tasks?")
            .setPositiveButton("Delete") { _, _ ->
                val count = tasks.size
                tasks.clear()
                adapter.notifyDataSetChanged()
                Toast.makeText(this, "All tasks deleted", Toast.LENGTH_SHORT).show()
                println("Deleted all $count tasks")
            }
            .setNegativeButton("Cancel", null)
            .show()
    }
    
    /**
     * Show statistics about tasks
     */
    private fun showStatistics() {
        val total = tasks.size
        val completed = tasks.count { it.isCompleted }
        val incomplete = total - completed
        val completionRate = if (total > 0) {
            (completed.toFloat() / total * 100).toInt()
        } else {
            0
        }
        
        val message = """
            Total tasks: $total
            Completed: $completed
            Incomplete: $incomplete
            Completion rate: $completionRate%
        """.trimIndent()
        
        AlertDialog.Builder(this)
            .setTitle("Task Statistics")
            .setMessage(message)
            .setPositiveButton("OK", null)
            .show()
        
        println("Statistics: $total total, $completed completed, $completionRate% rate")
    }
}

/**
 * Entry point for testing (not used in Android app)
 */
fun main() {
    println("Task Manager Application")
    println("This is an Android activity - run it on an Android device or emulator")
}
```

**Sample Output** (simulated user interactions):
```
TaskManager initialized with 5 tasks
Toggled task: Buy groceries - now completed
Added new task: Read chapter 5 (Total: 6)
Updated task at position 2 to: Call dentist about appointment
Marked task as complete: Exercise
Deleted 2 completed tasks (Remaining: 4)
Statistics: 4 total, 1 completed, 25% rate
Deleted all 4 tasks
```

### Required XML Resource Files

**res/menu/menu_main.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/menu_add_task"
        android:title="Add Task" />
    <item
        android:id="@+id/menu_delete_completed"
        android:title="Delete Completed" />
    <item
        android:id="@+id/menu_delete_all"
        android:title="Delete All" />
    <item
        android:id="@+id/menu_statistics"
        android:title="Statistics" />
</menu>
```

**res/menu/context_menu_task.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/menu_edit"
        android:title="Edit" />
    <item
        android:id="@+id/menu_mark_complete"
        android:title="Mark Complete" />
    <item
        android:id="@+id/menu_mark_incomplete"
        android:title="Mark Incomplete" />
    <item
        android:id="@+id/menu_delete"
        android:title="Delete" />
</menu>
```

---

## Summary

In this lecture, we've covered essential Android UI components and patterns:

**ListViews** provide a simple way to display scrollable lists of data, though RecyclerView is often preferred for complex scenarios.

**ArrayAdapter** bridges the gap between your data collections and the ListView, handling view creation and recycling automatically. It works with predefined Android layouts or custom layouts for more complex displays.

**The lateinit keyword** solves a common Android development challenge by allowing delayed initialization of non-null properties. This is particularly useful for components that must be initialized in lifecycle methods like `onCreate()`.

**Options menus** appear in the app bar and provide primary navigation and actions. They're defined in XML resource files and handled through the `onOptionsItemSelected()` method.

**Context menus** appear on long-press gestures and provide item-specific actions. They require registering views with `registerForContextMenu()` and implementing both `onCreateContextMenu()` and `onContextItemSelected()`.

Together, these components form the foundation for creating interactive list-based Android applications with intuitive user interfaces.
