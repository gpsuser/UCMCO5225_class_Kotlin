# RecyclerViews

---

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [1. What is a RecyclerView?](#1-what-is-a-recyclerview)
- [2. Setting Up Dependencies](#2-setting-up-dependencies)
- [3. Adding a RecyclerView to Your Layout](#3-adding-a-recyclerview-to-your-layout)
- [4. Creating a CardView Item Layout](#4-creating-a-cardview-item-layout)
- [5. The Layout Manager](#5-the-layout-manager)
- [6. Understanding RecyclerView.Adapter and ViewHolder](#6-understanding-recyclerviewadapter-and-viewholder)
- [7. Implementing RecyclerView.Adapter](#7-implementing-recyclerviewadapter)
- [8. Linking Everything Together in Your Activity](#8-linking-everything-together-in-your-activity)
- [9. A Complete Working Example](#9-a-complete-working-example)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain what a `RecyclerView` is and why it is preferred over `ListView`
- Add the required Gradle dependencies for `RecyclerView` and `CardView`
- Add a `RecyclerView` widget to an Activity layout
- Create an individual item layout using `CardView`
- Instantiate and assign a `LayoutManager` to a `RecyclerView`
- Describe the role of `RecyclerView.Adapter` and `RecyclerView.ViewHolder`
- Implement a concrete `RecyclerView.Adapter` with an inner `ViewHolder` class
- Wire together a `RecyclerView`, `LayoutManager`, and `Adapter` inside an `Activity`

---

## 1. What is a RecyclerView?

### Overview

When you want to display a long, scrollable list of items in Android — such as a list of contacts, messages, or products — you need a widget that can handle potentially hundreds (or thousands) of entries efficiently. The `RecyclerView` is Android's modern, recommended solution for exactly this problem.

### Why Not Just Use a ListView?

`ListView` is an older widget that was commonly used before `RecyclerView` was introduced. Its key limitation is that it creates a new `View` object for every single item in your list, even items that have scrolled off the screen. For a list of 500 items, that means 500 `View` objects live in memory simultaneously — which is wasteful and can lead to poor performance and janky scrolling.

`RecyclerView` solves this with a clever mechanism: **it recycles views**. When an item scrolls off the top of the screen, its `View` is not destroyed. Instead, it is placed into a pool and **reused** for the next item that scrolls in at the bottom. This means that at any given moment, only the visible items (plus a small buffer) need `View` objects — regardless of how large the dataset is.

```
+---------------------------------------------+
|  SCREEN                                     |
|  +---------------------------------------+  |
|  |  Item  1  (View object in use)        |  |
|  +---------------------------------------+  |
|  |  Item  2  (View object in use)        |  |
|  +---------------------------------------+  |
|  |  Item  3  (View object in use)        |  |
|  +---------------------------------------+  |
|  |  Item  4  (View object in use)        |  |
|  +---------------------------------------+  |
|       ... user scrolls down ...             |
|  +---------------------------------------+  |
|  |  Item  5  (RECYCLED from Item 1)      |  |  <-- same View object reused
|  +---------------------------------------+  |
+---------------------------------------------+

  Items 100, 200, 300... never create View objects
  until they scroll into view
```

### The Trade-Off: Complexity

This power comes with a cost: `RecyclerView` is more complex to set up than `ListView`. A working `RecyclerView` requires you to provide:

- A **`RecyclerView.Adapter`** — responsible for creating views and binding data to them
- A **`RecyclerView.ViewHolder`** — a helper object that caches references to the views inside each list item
- A **Layout Manager** — determines how items are arranged on screen (list, grid, staggered grid, etc.)
- An **item layout** — the XML layout for a single row/card in the list

We will build each of these pieces step by step.

[↑ Back to Table of Contents](#table-of-contents)

---

## 2. Setting Up Dependencies

### Gradle Dependencies

Before you can use `RecyclerView` or `CardView` in your project, you must declare them as dependencies in your **module-level** `build.gradle` file (the one inside your `app/` folder — not the project-level one).

Open `app/build.gradle` and add the following lines inside the `dependencies { }` block:

```groovy
dependencies {
    // RecyclerView - the scrollable list widget
    implementation 'androidx.recyclerview:recyclerview:1.4.0'

    // CardView - a Material Design card container for each list item
    implementation 'androidx.cardview:cardview:1.0.0'

    // ... your other existing dependencies
}
```

After editing `build.gradle`, Android Studio will show a banner prompting you to **Sync Now**. Always sync your project after changing Gradle files — without this step, the new libraries will not be available to your code.

> **Why are these separate dependencies?**  
> Android Jetpack libraries are modular — you only include what you need. `RecyclerView` and `CardView` are separate libraries so that apps that don't need them aren't bloated with unused code.

[↑ Back to Table of Contents](#table-of-contents)

---

## 3. Adding a RecyclerView to Your Layout

### Placing RecyclerView in an Activity Layout

Once the dependency is synced, you can add a `RecyclerView` widget to an Activity's XML layout file (e.g. `activity_main.xml`). Typically, you want it to fill all available space:

```xml
<!-- activity_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- RecyclerView fills the whole screen -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Key points about this layout:
- The `id` (`recyclerView`) is how you will reference this widget from Kotlin code
- Setting `layout_width` and `layout_height` to `0dp` with `ConstraintLayout` constraints makes it fill the available space
- At this stage the `RecyclerView` renders nothing — it needs an `Adapter` and `LayoutManager` to display data

[↑ Back to Table of Contents](#table-of-contents)

---

## 4. Creating a CardView Item Layout

### What is a CardView?

A `CardView` is a container widget from the Material Design library that displays content inside a rounded-corner card with an optional drop shadow (elevation). It is the standard way to present individual items in a `RecyclerView` — each row in the list is a separate card.

### Creating the Item Layout File

You need to create a **separate** XML layout file for the individual item — this is the template that will be inflated for every row in your list.

In Android Studio: right-click `res/layout` → **New** → **Layout Resource File**. Name it something like `card_layout.xml`.

The **root element** must be `androidx.cardview.widget.CardView`, and critically, its height should be **`wrap_content`** — not `match_parent`. If you used `match_parent`, each card would fill the entire screen and only one item would be visible at a time.

```xml
<!-- res/layout/card_layout.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="8dp"
    app:cardElevation="4dp"
    android:layout_margin="8dp">

    <!-- ConstraintLayout organises the views inside the card -->
    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <!-- Title text for the item -->
        <TextView
            android:id="@+id/textViewTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textSize="18sp"
            android:textStyle="bold"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />

        <!-- Subtitle / description text -->
        <TextView
            android:id="@+id/textViewSubtitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:textSize="14sp"
            app:layout_constraintTop_toBottomOf="@id/textViewTitle"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</androidx.cardview.widget.CardView>
```

Here is a wireframe of what a single card looks like on screen:

```
+------------------------------------------+
|  +--------------------------------------+ |
|  |  Card Title                 [bold]   | |
|  |  Card subtitle text here             | |
|  +--------------------------------------+ |
+------------------------------------------+
```

And several cards stacked in a `RecyclerView`:

```
+------------------------------------------+
|  +--------------------------------------+ |
|  |  First Item                          | |
|  |  Description for first item          | |
|  +--------------------------------------+ |
|                                          |
|  +--------------------------------------+ |
|  |  Second Item                         | |
|  |  Description for second item         | |
|  +--------------------------------------+ |
|                                          |
|  +--------------------------------------+ |
|  |  Third Item                          | |
|  |  Description for third item          | |
|  +--------------------------------------+ |
+------------------------------------------+
```

[↑ Back to Table of Contents](#table-of-contents)

---

## 5. The Layout Manager

### What Does a Layout Manager Do?

The `LayoutManager` is responsible for deciding **how** the items in a `RecyclerView` are arranged on screen. Android provides three built-in managers:

| Layout Manager | Behaviour |
|---|---|
| `LinearLayoutManager` | Items in a single vertical (or horizontal) scrolling list |
| `GridLayoutManager` | Items arranged in a fixed-column grid |
| `StaggeredGridLayoutManager` | Items in a grid where rows can have different heights |

For most standard scrolling lists, `LinearLayoutManager` is the right choice.

### Assigning a LayoutManager in Your Activity

You assign the `LayoutManager` in the `onCreate()` method of your `Activity`, after inflating the layout with View Binding:

```kotlin
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.recyclerview.widget.LinearLayoutManager
import com.example.myapp.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    // View Binding reference — gives us type-safe access to views
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Inflate layout using View Binding
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Create a LinearLayoutManager and assign it to the RecyclerView.
        // 'this' refers to the Activity, which serves as the Context.
        binding.recyclerView.layoutManager = LinearLayoutManager(this)
    }
}
```

> **What is `context`?**  
> Many Android components (including `LinearLayoutManager`) need a `Context` to access system resources. Inside an `Activity`, `this` is a valid `Context`.

### Using a GridLayoutManager

If you want a two-column grid instead of a list, the only change is the `LayoutManager` you assign:

```kotlin
// Show items in a 2-column grid
binding.recyclerView.layoutManager = GridLayoutManager(this, 2)
//                                                           ^
//                                                    number of columns
```

[↑ Back to Table of Contents](#table-of-contents)

---

## 6. Understanding RecyclerView.Adapter and ViewHolder

### The Role of the Adapter

`RecyclerView.Adapter` is an **abstract class** — meaning it defines a contract (a set of methods) that you must implement, but does not provide the implementation itself. Your job is to create a class that **extends** `RecyclerView.Adapter` and fills in those methods.

The Adapter acts as a bridge between your **data source** (e.g. a list of objects) and the **`RecyclerView`** that displays them. It answers three questions:

1. **How many items** are in the dataset? (`getItemCount()`)
2. **How should a new item view be created?** (`onCreateViewHolder()`)
3. **What data should be displayed** at a given position? (`onBindViewHolder()`)

### The Role of the ViewHolder

`RecyclerView.ViewHolder` is a helper class that **caches references to the views inside a single item layout**. Without it, every time the `RecyclerView` needs to display an item, it would have to call `findViewById()` repeatedly — which is expensive because it traverses the entire view hierarchy.

By storing view references in a `ViewHolder` once (at creation time), lookups in `onBindViewHolder()` become instant.

### Generic Type Parameter: The Bounded Type

`RecyclerView.Adapter` is a **generic class**: `RecyclerView.Adapter<VH : RecyclerView.ViewHolder>`. The type parameter `VH` must be (or extend) `RecyclerView.ViewHolder`. This is called a **bounded type parameter** — the compiler enforces that you can only supply a valid `ViewHolder` type.

The common pattern is to define your `ViewHolder` as an **inner class** inside your `Adapter` class, and then use it as the type argument:

```kotlin
// The type argument is our own inner class MyViewHolder,
// which extends RecyclerView.ViewHolder
class MyRecyclerAdapter : RecyclerView.Adapter<MyRecyclerAdapter.MyViewHolder>() {

    // Inner ViewHolder class — defined inside the Adapter
    inner class MyViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        // cache view references here
    }
}
```

This pattern keeps the `ViewHolder` and `Adapter` tightly coupled, which makes sense since they are designed to work together.

[↑ Back to Table of Contents](#table-of-contents)

---

## 7. Implementing RecyclerView.Adapter

### The Three Required Methods

When you extend `RecyclerView.Adapter`, Android Studio will flag your class as having errors until you implement all three abstract methods. Here is what each one does:

| Method | When Called | Your Responsibility |
|---|---|---|
| `onCreateViewHolder()` | When a new item view is needed (not yet in the recycle pool) | Inflate the item layout XML and return a `ViewHolder` wrapping it |
| `onBindViewHolder()` | When an item is about to become visible | Populate the views in the `ViewHolder` with data for the given position |
| `getItemCount()` | Whenever RecyclerView needs to know the list size | Return the number of items in your data source |

### A Data Class for Our Items

Before writing the adapter, let us define a simple data class to represent each item in our list:

```kotlin
// A data class representing one item in our RecyclerView list.
// Kotlin data classes automatically get equals(), hashCode(), toString(), and copy().
data class ListItem(
    val title: String,       // The bold heading shown on the card
    val subtitle: String     // The smaller description below the heading
)
```

### Full Adapter Implementation with View Binding

```kotlin
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import com.example.myapp.databinding.CardLayoutBinding

// MyRecyclerAdapter manages a list of ListItem objects and binds them to CardViews.
// The type parameter specifies our inner ViewHolder class.
class MyRecyclerAdapter(
    private val items: List<ListItem>  // The dataset — passed in from the Activity
) : RecyclerView.Adapter<MyRecyclerAdapter.MyViewHolder>() {

    // -------------------------------------------------------------------------
    // Inner ViewHolder class
    // Holds a binding reference to one inflated card_layout.xml
    // -------------------------------------------------------------------------
    inner class MyViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        // CardLayoutBinding gives us type-safe references to every
        // view declared in card_layout.xml
        val binding = CardLayoutBinding.bind(itemView)
    }

    // -------------------------------------------------------------------------
    // onCreateViewHolder — called when RecyclerView needs a brand-new view
    // (either at start, or when the recycle pool is empty)
    // -------------------------------------------------------------------------
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
        // Inflate the card layout XML into a View object.
        // attachToRoot = false because RecyclerView manages its own attachment.
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.card_layout, parent, false)

        // Wrap the inflated view in our ViewHolder and return it
        return MyViewHolder(view)
    }

    // -------------------------------------------------------------------------
    // onBindViewHolder — called each time an item scrolls into view.
    // Reuses ViewHolder objects from the recycle pool.
    // -------------------------------------------------------------------------
    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        // Retrieve the data object for this position
        val currentItem = items[position]

        // Populate the views inside the card using the cached binding
        holder.binding.textViewTitle.text    = currentItem.title
        holder.binding.textViewSubtitle.text = currentItem.subtitle
    }

    // -------------------------------------------------------------------------
    // getItemCount — RecyclerView calls this to know how many rows to show
    // -------------------------------------------------------------------------
    override fun getItemCount(): Int {
        return items.size
    }
}
```

### What is `LayoutInflater`?

`LayoutInflater` converts an XML layout file into a live `View` object that can be displayed on screen. It needs a `Context` to do this — we obtain one via `parent.context`, where `parent` is the `RecyclerView` itself.

The call `LayoutInflater.from(parent.context).inflate(R.layout.card_layout, parent, false)` reads `card_layout.xml`, builds the `View` hierarchy it describes, and returns the root `View` — in our case, the `CardView`.

[↑ Back to Table of Contents](#table-of-contents)

---

## 8. Linking Everything Together in Your Activity

### The Final Step

Now that we have:
- A `RecyclerView` in the Activity layout ✓
- A `CardView` item layout ✓
- A `LayoutManager` ✓
- An `Adapter` ✓

...we need to connect them all inside the Activity.

```kotlin
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.recyclerview.widget.LinearLayoutManager
import com.example.myapp.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    // Keep a reference to the adapter — useful if you need to update the list later
    private lateinit var recyclerAdapter: MyRecyclerAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // --- 1. Prepare the dataset ---
        // In a real app this data might come from a database or a network call
        val itemList = listOf(
            ListItem("Kotlin",       "A modern language for Android development"),
            ListItem("Java",         "The original Android language"),
            ListItem("Python",       "Popular for data science and scripting"),
            ListItem("Swift",        "Apple's language for iOS development"),
            ListItem("JavaScript",   "The language of the web")
        )

        // --- 2. Create the Adapter, passing in the dataset ---
        recyclerAdapter = MyRecyclerAdapter(itemList)

        // --- 3. Assign the LayoutManager to the RecyclerView ---
        binding.recyclerView.layoutManager = LinearLayoutManager(this)

        // --- 4. Assign the Adapter to the RecyclerView ---
        binding.recyclerView.adapter = recyclerAdapter
    }
}
```

After `adapter` is assigned, the `RecyclerView` will immediately call `getItemCount()` to learn the list size, then call `onCreateViewHolder()` and `onBindViewHolder()` for each visible item. Your list will appear on screen.

> **Why keep a reference to the adapter?**  
> If the dataset changes after the `RecyclerView` is first displayed (e.g. the user adds a new item), you need to notify the adapter. Calling `recyclerAdapter.notifyDataSetChanged()` tells the `RecyclerView` to re-read the data and refresh all visible items.

[↑ Back to Table of Contents](#table-of-contents)

---

## 9. A Complete Working Example

### Putting It All Together

The following shows every file needed for a minimal, fully working `RecyclerView` app. This is intended so you can see the complete picture in one place.

---

#### `ListItem.kt` — Data Model

```kotlin
package com.example.myapp

// Simple data class representing one item in our list.
// Kotlin generates constructor, getters, equals, hashCode, and toString automatically.
data class ListItem(
    val title: String,
    val subtitle: String
)
```

---

#### `MyRecyclerAdapter.kt` — The Adapter

```kotlin
package com.example.myapp

import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import com.example.myapp.databinding.CardLayoutBinding

/**
 * Adapter for the RecyclerView.
 *
 * @param items  The list of ListItem objects to display.
 */
class MyRecyclerAdapter(
    private val items: List<ListItem>
) : RecyclerView.Adapter<MyRecyclerAdapter.MyViewHolder>() {

    /**
     * ViewHolder caches binding references to the views in one card.
     * Avoids repeated expensive calls to findViewById() during scrolling.
     */
    inner class MyViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val binding: CardLayoutBinding = CardLayoutBinding.bind(itemView)
    }

    /**
     * Called when RecyclerView needs a completely new view (or takes one from
     * the recycle pool). Inflates card_layout.xml and wraps it in a ViewHolder.
     */
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
        val view = LayoutInflater
            .from(parent.context)
            .inflate(R.layout.card_layout, parent, false)
        return MyViewHolder(view)
    }

    /**
     * Called each time an item scrolls into view.
     * Reads the data object at [position] and writes its fields into the card's views.
     */
    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        val item = items[position]
        holder.binding.textViewTitle.text    = item.title
        holder.binding.textViewSubtitle.text = item.subtitle
    }

    /**
     * Tells the RecyclerView how many items exist in the dataset.
     */
    override fun getItemCount(): Int = items.size
}
```

---

#### `MainActivity.kt` — The Activity

```kotlin
package com.example.myapp

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.recyclerview.widget.LinearLayoutManager
import com.example.myapp.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    // View Binding for activity_main.xml
    private lateinit var binding: ActivityMainBinding

    // Keep a reference so we can call notifyDataSetChanged() later if needed
    private lateinit var recyclerAdapter: MyRecyclerAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Inflate layout and set as content view
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Sample dataset — in a real app this could come from a database or API
        val programmingLanguages = listOf(
            ListItem("Kotlin",      "Modern, concise language for Android"),
            ListItem("Java",        "Original Android language, still widely used"),
            ListItem("Python",      "Popular for scripting and data science"),
            ListItem("Swift",       "Apple's language for iOS and macOS"),
            ListItem("JavaScript",  "Runs in browsers and on servers via Node.js"),
            ListItem("C#",          "Microsoft language used in .NET and Unity"),
            ListItem("Rust",        "Systems language focused on memory safety"),
            ListItem("Go",          "Google's language for scalable back-ends")
        )

        // Create the adapter with our dataset
        recyclerAdapter = MyRecyclerAdapter(programmingLanguages)

        // Assign a LinearLayoutManager — items displayed in a vertical scrolling list
        binding.recyclerView.layoutManager = LinearLayoutManager(this)

        // Assign the adapter — RecyclerView will now render the list
        binding.recyclerView.adapter = recyclerAdapter
    }
}
```

---

### Expected Output

When you run this app, the screen displays a vertically scrollable list of cards:

```
+------------------------------------------+
|  +--------------------------------------+ |
|  |  Kotlin                              | |
|  |  Modern, concise language for Android| |
|  +--------------------------------------+ |
|                                          |
|  +--------------------------------------+ |
|  |  Java                                | |
|  |  Original Android language, still... | |
|  +--------------------------------------+ |
|                                          |
|  +--------------------------------------+ |
|  |  Python                              | |
|  |  Popular for scripting and data sc...| |
|  +--------------------------------------+ |
|                                          |
|  +--------------------------------------+ |
|  |  Swift                               | |
|  |  Apple's language for iOS and macOS  | |
|  +--------------------------------------+ |
|                                          |
|  ... scrollable ...                      |
+------------------------------------------+
```

All eight items are stored in the list, but only the ones visible on screen have active `View` objects. As you scroll down, the cards that leave the top are recycled and reused for the items appearing at the bottom.

[↑ Back to Table of Contents](#table-of-contents)
