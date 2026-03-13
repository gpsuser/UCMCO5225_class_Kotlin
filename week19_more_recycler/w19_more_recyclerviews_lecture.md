# More RecyclerViews, Keyboard Handling & Extension Functions

---

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [1. Keyboard Handling — The Basics](#1-keyboard-handling--the-basics)
- [2. Hiding the Keyboard Programmatically](#2-hiding-the-keyboard-programmatically)
- [3. Keyboard Events with EditText](#3-keyboard-events-with-edittext)
- [4. The TextView.OnEditorActionListener in Detail](#4-the-textviewoneditoractionlistener-in-detail)
- [5. Extension Functions](#5-extension-functions)
- [6. RecyclerView Drag & Swipe](#6-recyclerview-drag--swipe)
- [7. The RecyclerView Recycling Caveat](#7-the-recyclerview-recycling-caveat)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

1. Explain how Android manages the soft keyboard and how to hide it programmatically.
2. Implement a `TextView.OnEditorActionListener` to respond to keyboard action events on an `EditText`.
3. Write Kotlin extension functions to add behaviour to existing classes without subclassing.
4. Enable drag-and-drop and swipe-to-delete functionality in a `RecyclerView` using `ItemTouchHelper`.
5. Identify and resolve common view-recycling bugs in a `RecyclerView` adapter.

---

## 1. Keyboard Handling — The Basics

[↑ Back to Table of Contents](#table-of-contents)

### What Android Gives You for Free

Android's soft keyboard (the on-screen keyboard) comes with a lot of default behaviour that you don't need to write yourself:

- When a user taps on an `EditText`, the keyboard appears automatically.
- When the user presses the **Done** button (the IME action button at the bottom-right of the keyboard), the keyboard hides itself.
- The layout may scroll or resize to keep the focused field visible.

This is useful for simple scenarios — a basic "hello world" style app with a single text field just works. However, once your requirements get slightly more complex — for example, hiding the keyboard when the user taps somewhere outside the text field, or dismissing it after processing input — you need to step in and manage it yourself.

Consider what the UI might look like on a device:

```
+---------------------------+
|                           |
|   Enter your name:        |
|  +---------------------+  |
|  | John                |  |
|  +---------------------+  |
|                           |
|  [SUBMIT]                 |
|                           |
+---------------------------+
|  q  w  e  r  t  y  u  i  |
|   a  s  d  f  g  h  j  k |
|    z  x  c  v  b  n  m   |
|  [  ] [      SPACE    ][Done]|
+---------------------------+
```

The keyboard appears from the bottom and occupies roughly half the screen. Managing when it appears and disappears is important for a smooth user experience.

---

## 2. Hiding the Keyboard Programmatically

[↑ Back to Table of Contents](#table-of-contents)

### Why You Need This

There are common situations where you want to dismiss the keyboard in code:

- The user submits a form via a button tap.
- The user taps outside the text field.
- Navigation occurs (moving to another screen).

To do this, you request the system's **Input Method Manager** service and call `hideSoftInputFromWindow`. You need a reference to the current window token, which comes from any `View` that is currently attached to the window.

### Code Example — Hiding the Keyboard from an Activity

```kotlin
import android.content.Context
import android.os.Bundle
import android.view.View
import android.view.inputmethod.InputMethodManager
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val editText = findViewById<EditText>(R.id.editTextName)
        val submitButton = findViewById<Button>(R.id.buttonSubmit)
        val resultText = findViewById<TextView>(R.id.textViewResult)

        submitButton.setOnClickListener { view ->
            // Retrieve the text entered by the user
            val name = editText.text.toString()
            resultText.text = "Hello, $name!"

            // Hide the soft keyboard — we pass the view that triggered the event
            // so we have access to a valid window token
            hideKeyboard(view)
        }
    }

    /**
     * Hides the soft keyboard programmatically.
     *
     * @param view Any view currently attached to the window.
     *             We need it only for its windowToken.
     */
    private fun hideKeyboard(view: View) {
        // Ask the system for the Input Method Manager service
        val inputMethodManager =
            getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager

        // Ask the IMM to hide the keyboard associated with this window
        // Flags: 0 means no special behaviour requested
        inputMethodManager.hideSoftInputFromWindow(view.windowToken, 0)
    }
}
```

> **Key point:** The `view` parameter is needed purely for its `windowToken` — it tells Android *which window's* keyboard to hide. Any view currently on screen and attached to the window will do. In a click listener, the `view` parameter passed to `setOnClickListener` is a convenient choice.

---

## 3. Keyboard Events with EditText

[↑ Back to Table of Contents](#table-of-contents)

### Why Listen for Keyboard Events?

Sometimes you need to react when the user signals they have *finished* typing — for example, pressing the **Done** / **Enter** key on the keyboard. A `Button` click is one approach, but often you want to handle this directly at the text field.

Android provides `TextView.OnEditorActionListener` for exactly this purpose. Even though `EditText` is used for input, it extends `TextView`, so the listener applies.

### Setting Up the Listener

The steps are:

1. Create a class that implements `TextView.OnEditorActionListener`.
2. Override the `onEditorAction` method inside that class.
3. Attach an instance of your class to the `EditText` using `setOnEditorActionListener`.

The simplest structure is an **inner class** — because it lives inside your `Activity`, it can access the Activity's views and methods directly.

```kotlin
import android.os.Bundle
import android.view.KeyEvent
import android.view.inputmethod.EditorInfo
import android.widget.EditText
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val editText = findViewById<EditText>(R.id.editTextSearch)

        // Attach our custom listener to the EditText
        editText.setOnEditorActionListener(EditorEventHandler())
    }

    /**
     * Inner class that handles keyboard action events from an EditText.
     *
     * Being an inner class gives it access to the enclosing Activity's members.
     */
    inner class EditorEventHandler : TextView.OnEditorActionListener {

        /**
         * Called whenever an IME action is triggered on the associated TextView.
         *
         * @param v        The TextView that fired the event.
         * @param actionId The integer ID of the action (e.g. IME_ACTION_DONE = 6).
         * @param event    A KeyEvent if triggered by a hardware key; null for software keyboard.
         * @return         true if you have handled the event; false to let the system handle it.
         */
        override fun onEditorAction(v: TextView?, actionId: Int, event: KeyEvent?): Boolean {
            // Check if the user pressed the Done action on the soft keyboard
            if (actionId == EditorInfo.IME_ACTION_DONE) {
                val input = v?.text.toString()
                Toast.makeText(
                    this@MainActivity,
                    "You entered: $input",
                    Toast.LENGTH_SHORT
                ).show()
                // Return true — we have handled this event
                return true
            }
            // Return false — let the system deal with any other actions
            return false
        }
    }
}
```

**Sample interaction:** User types "Android" and presses Done → Toast displays: `You entered: Android`

---

## 4. The TextView.OnEditorActionListener in Detail

[↑ Back to Table of Contents](#table-of-contents)

### Understanding the Parameters

The `onEditorAction` callback receives three parameters. Understanding each one helps you handle edge cases confidently.

#### `actionId: Int`

- Identifies which IME action was triggered.
- The most common value is `EditorInfo.IME_ACTION_DONE`, which has an integer value of `6`.
- You can customise this value in your layout XML using the `android:imeOptions` attribute on the `EditText`:

```xml
<EditText
    android:id="@+id/editTextSearch"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Search..."
    android:imeOptions="actionSearch"
    android:inputType="text" />
```

- With `actionSearch`, the keyboard's action button becomes a magnifying glass, and `actionId` will equal `EditorInfo.IME_ACTION_SEARCH` (value `3`).

#### `event: KeyEvent?`

- This is `null` when the action is triggered by the soft keyboard's action button.
- It holds a `KeyEvent` when triggered by a physical/hardware keyboard key (specifically the Enter key).
- Because it can be null, always use safe-call (`?.`) or a null check before accessing it.

#### `return true` vs `return false`

| Return value | Meaning |
|---|---|
| `true` | You handled the event. Android will not process it further. |
| `false` | You did NOT handle it. Android will apply its default behaviour. |

> **Important caveat:** Parameter values do not always match what you expect. On some devices or keyboard apps (IMEs), `actionId` may arrive as `0` even when Done is pressed. A robust approach is to check *both* `actionId` and whether `event` corresponds to the Enter key:

```kotlin
import android.os.Bundle
import android.view.KeyEvent
import android.view.inputmethod.EditorInfo
import android.widget.EditText
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

class RobustKeyboardActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val editText = findViewById<EditText>(R.id.editTextInput)

        editText.setOnEditorActionListener { v, actionId, event ->
            // Condition 1: standard Done action from the soft keyboard button
            val isDoneAction = actionId == EditorInfo.IME_ACTION_DONE

            // Condition 2: hardware Enter key pressed (action = UP, keyCode = ENTER)
            val isEnterKey = event?.action == KeyEvent.ACTION_UP &&
                    event.keyCode == KeyEvent.KEYCODE_ENTER

            if (isDoneAction || isEnterKey) {
                val text = v?.text.toString()
                Toast.makeText(this, "Submitted: $text", Toast.LENGTH_SHORT).show()
                true  // Handled
            } else {
                false // Not handled
            }
        }
    }
}
```

> **Note on TextWatcher:** If you need to react to *every individual character* as the user types (e.g. for live search filtering), you should use a `TextWatcher` instead of an `OnEditorActionListener`. The listener fires once at the end; the watcher fires on every change.

---

## 5. Extension Functions

[↑ Back to Table of Contents](#table-of-contents)

### The Problem They Solve

Imagine you frequently need to convert the text in a `TextView` to uppercase across your app. The obvious approach is subclassing — create `UpperCaseTextView extends TextView` — but this is verbose and cumbersome for a one-line operation. Java's usual solution is a static utility method, which is clean but breaks object-oriented readability.

Kotlin offers a more elegant solution: **extension functions**.

### What Is an Extension Function?

An extension function lets you add new methods to an *existing* class without modifying its source code and without subclassing it. You declare the function outside the class, but prefix the function name with the type you are extending (called the **receiver type**).

```
fun ReceiverType.functionName(params): ReturnType {
    // 'this' refers to the receiver instance
}
```

Inside the function body, `this` refers to the instance the function was called on — just like a regular member function.

### Simple Example — Extending String

```kotlin
/**
 * Extension function on String.
 * Reverses the string and returns the result.
 *
 * 'this' inside the function refers to the String instance it is called on.
 */
fun String.reversed(): String {
    return this.reversed()   // Standard library method for illustration
}

fun main() {
    val word = "Android"
    println(word.reversed())   // niordn A
}
```

> Note: `reversed()` already exists on `String` in the standard library. This example is for illustration. The point is you call it *as if it were a member method* of the class.

### Practical Android Example — Extending TextView

This is the kind of example you will use in real apps. We add a method to `TextView` that converts its displayed text to uppercase:

```kotlin
import android.widget.TextView

/**
 * Extension function on TextView.
 * Converts the current text content to uppercase and updates the view.
 *
 * 'this' refers to the TextView instance this method is called on.
 */
fun TextView.makeUpperCase() {
    this.text = this.text.toString().uppercase()
}
```

Once this function is declared (typically in a separate Kotlin file such as `Extensions.kt`), it can be called on *any* `TextView` instance as if it were a built-in method:

```kotlin
import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

// If the extension function is in a different file, it just needs to be in scope
fun TextView.makeUpperCase() {
    this.text = this.text.toString().uppercase()
}

class ExtensionDemoActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val label = findViewById<TextView>(R.id.textViewLabel)
        val button = findViewById<Button>(R.id.buttonUpperCase)

        label.text = "hello world"

        button.setOnClickListener {
            // Call our extension function exactly like a normal method
            label.makeUpperCase()
        }
    }
}
```

**Before click:** `hello world`  
**After click:** `HELLO WORLD`

### Extending Android Framework Classes — Hiding the Keyboard

Extension functions become especially powerful when applied to Android's own classes. Recall the keyboard-hiding code from earlier — it's verbose and hard to remember. We can tidy it up significantly:

```kotlin
import android.content.Context
import android.view.View
import android.view.inputmethod.InputMethodManager

/**
 * Extension function on View.
 * Hides the soft keyboard by using this view's windowToken.
 *
 * Place this in a file like Extensions.kt so it is available across your project.
 */
fun View.hideKeyboard() {
    val imm = context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
    imm.hideSoftInputFromWindow(this.windowToken, 0)
}
```

Now anywhere you have a `View` reference, you can call:

```kotlin
submitButton.setOnClickListener { view ->
    // Clean, readable, and reusable
    view.hideKeyboard()
}
```

> **Key insight:** Extension functions do not actually modify the class. They are syntactic sugar — at compile time Kotlin converts them into regular static methods with the receiver as the first parameter. This means they cannot access private or protected members of the class.

---

## 6. RecyclerView Drag & Swipe

[↑ Back to Table of Contents](#table-of-contents)

### Overview

Android's `RecyclerView` supports drag-to-reorder and swipe-to-delete out of the box through a class called `ItemTouchHelper`. It acts as a bridge between touch gestures and your adapter, notifying the adapter when items are moved or removed.

The setup involves three steps:

1. Create a class that extends `ItemTouchHelper.Callback` and configure its behaviour.
2. Implement the required callbacks (`onMove`, `onSwiped`).
3. Attach the helper to your `RecyclerView`.

```
RecyclerView
     |
     |  attached via attachToRecyclerView()
     |
ItemTouchHelper
     |
     |  delegates gesture events to
     |
ItemTouchHelper.Callback
     |
     |  notifies adapter via
     |
RecyclerView.Adapter (your custom adapter)
```

### Step 1 — Implement ItemTouchHelper.Callback

```kotlin
import androidx.recyclerview.widget.ItemTouchHelper
import androidx.recyclerview.widget.RecyclerView

/**
 * Callback class that handles drag-to-reorder and swipe-to-delete gestures
 * in a RecyclerView.
 *
 * @param adapter  The adapter managing the data list, so we can notify it of changes.
 */
class DragSwipeCallback(
    private val adapter: TaskAdapter
) : ItemTouchHelper.Callback() {

    /**
     * Defines which gestures are enabled.
     *
     * makeMovementFlags(dragFlags, swipeFlags):
     * - dragFlags:  UP | DOWN allows vertical reordering
     * - swipeFlags: START | END allows left and right swipe-to-delete
     */
    override fun getMovementFlags(
        recyclerView: RecyclerView,
        viewHolder: RecyclerView.ViewHolder
    ): Int {
        val dragFlags = ItemTouchHelper.UP or ItemTouchHelper.DOWN
        val swipeFlags = ItemTouchHelper.START or ItemTouchHelper.END
        return makeMovementFlags(dragFlags, swipeFlags)
    }

    /**
     * Called when an item is dragged from one position to another.
     *
     * @param viewHolder The ViewHolder being dragged.
     * @param target     The ViewHolder currently occupying the target position.
     * @return           true to confirm the move was handled.
     */
    override fun onMove(
        recyclerView: RecyclerView,
        viewHolder: RecyclerView.ViewHolder,
        target: RecyclerView.ViewHolder
    ): Boolean {
        // Get the positions using adapterPosition
        val fromPosition = viewHolder.adapterPosition
        val toPosition = target.adapterPosition

        // Tell the adapter to update its data and notify the RecyclerView
        adapter.moveItem(fromPosition, toPosition)
        return true
    }

    /**
     * Called when an item is swiped off screen.
     *
     * @param viewHolder The ViewHolder that was swiped.
     * @param direction  The direction of the swipe (START or END).
     */
    override fun onSwiped(viewHolder: RecyclerView.ViewHolder, direction: Int) {
        val position = viewHolder.adapterPosition
        adapter.removeItem(position)
    }
}
```

### Step 2 — Adapter with Move and Remove Support

```kotlin
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView
import java.util.Collections

/**
 * Adapter for a simple list of task strings.
 * Supports item movement and deletion for use with ItemTouchHelper.
 */
class TaskAdapter(
    private val tasks: MutableList<String>
) : RecyclerView.Adapter<TaskAdapter.TaskViewHolder>() {

    class TaskViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val textView: TextView = itemView.findViewById(android.R.id.text1)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TaskViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(android.R.layout.simple_list_item_1, parent, false)
        return TaskViewHolder(view)
    }

    override fun onBindViewHolder(holder: TaskViewHolder, position: Int) {
        holder.textView.text = tasks[position]
    }

    override fun getItemCount(): Int = tasks.size

    /**
     * Swaps items in the data list between fromPosition and toPosition,
     * then notifies the RecyclerView of the move.
     */
    fun moveItem(fromPosition: Int, toPosition: Int) {
        Collections.swap(tasks, fromPosition, toPosition)
        notifyItemMoved(fromPosition, toPosition)
    }

    /**
     * Removes the item at the given position from the data list,
     * then notifies the RecyclerView of the removal.
     */
    fun removeItem(position: Int) {
        tasks.removeAt(position)
        notifyItemRemoved(position)
    }
}
```

### Step 3 — Attaching ItemTouchHelper in the Activity

```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.ItemTouchHelper
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView

class DragDropActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val recyclerView = findViewById<RecyclerView>(R.id.recyclerView)

        // Sample data
        val taskList = mutableListOf(
            "Buy groceries",
            "Write report",
            "Call dentist",
            "Water the plants",
            "Go for a run"
        )

        // Create the adapter
        val adapter = TaskAdapter(taskList)

        // Set up the RecyclerView
        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = adapter

        // Create the callback and the helper
        val callback = DragSwipeCallback(adapter)
        val itemTouchHelper = ItemTouchHelper(callback)

        // Attach the helper to the RecyclerView — this wires up all the touch handling
        itemTouchHelper.attachToRecyclerView(recyclerView)
    }
}
```

**Result:**
```
+-------------------------------+
| Buy groceries          [   ]  |
| Write report           [   ]  |   <-- drag handle area
| Call dentist           [   ]  |
| Water the plants       [   ]  |
| Go for a run           [   ]  |
+-------------------------------+
  ^--- swipe left/right to delete
  ^--- drag up/down to reorder
```

---

## 7. The RecyclerView Recycling Caveat

[↑ Back to Table of Contents](#table-of-contents)

### How RecyclerView Recycling Works

`RecyclerView` earns its name because it *recycles* `ViewHolder` objects. When a list item scrolls off screen, rather than being destroyed and garbage collected, its `ViewHolder` is placed in a pool and reused for newly visible items. This makes scrolling fast and memory-efficient.

However, this creates a subtle but important bug if you are not careful.

### The Problem

When a `ViewHolder` is recycled, its views **still hold any state that was previously applied to them**. Only the properties you explicitly update in `onBindViewHolder` are refreshed. Any other visual state — background colour, checked state, font style, visibility, etc. — will persist from the previous item.

**Example of the bug:**

Imagine a to-do list where completed items are shown with a strikethrough and a grey background. If item 3 is marked as complete, and then the user scrolls down so that the `ViewHolder` for item 3 gets recycled for item 12 — item 12 will appear with a strikethrough and grey background even if it is not complete, because `onBindViewHolder` only set the text, not the background or text style.

```
+-----------------------+       recycled        +-----------------------+
| Item 3 (complete)     |  ---------------->    | Item 12 (not complete)|
| [DONE] strikethrough  |   ViewHolder reused   | [BUG] strikethrough!  |
| grey background       |                       | grey background BUG!  |
+-----------------------+                       +-----------------------+
```

### Solution 1 — Always Set All Properties in `onBindViewHolder`

The most reliable fix is to explicitly set *every* visual property for every item, including the "default" state:

```kotlin
import android.graphics.Color
import android.graphics.Paint
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

data class Task(val name: String, val isComplete: Boolean)

class SafeTaskAdapter(
    private val tasks: List<Task>
) : RecyclerView.Adapter<SafeTaskAdapter.TaskViewHolder>() {

    class TaskViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val textView: TextView = itemView.findViewById(android.R.id.text1)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TaskViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(android.R.layout.simple_list_item_1, parent, false)
        return TaskViewHolder(view)
    }

    override fun onBindViewHolder(holder: TaskViewHolder, position: Int) {
        val task = tasks[position]
        holder.textView.text = task.name

        if (task.isComplete) {
            // Apply "complete" visual state
            holder.textView.paintFlags =
                holder.textView.paintFlags or Paint.STRIKE_THRU_TEXT_FLAG
            holder.itemView.setBackgroundColor(Color.LTGRAY)
        } else {
            // IMPORTANT: Always reset to the default state.
            // Without this, a recycled ViewHolder would keep the previous item's appearance.
            holder.textView.paintFlags =
                holder.textView.paintFlags and Paint.STRIKE_THRU_TEXT_FLAG.inv()
            holder.itemView.setBackgroundColor(Color.WHITE)
        }
    }

    override fun getItemCount(): Int = tasks.size
}
```

### Solution 2 — Using `onViewRecycled`

For more complex scenarios, you can override `onViewRecycled` in the adapter. This is called by `RecyclerView` *just before* a `ViewHolder` is placed back in the recycle pool, giving you a dedicated place to reset its state to a clean baseline.

```kotlin
import android.graphics.Color
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

class ResetOnRecycleAdapter(
    private val items: List<String>
) : RecyclerView.Adapter<ResetOnRecycleAdapter.ItemViewHolder>() {

    class ItemViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val textView: TextView = itemView.findViewById(android.R.id.text1)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ItemViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(android.R.layout.simple_list_item_1, parent, false)
        return ItemViewHolder(view)
    }

    override fun onBindViewHolder(holder: ItemViewHolder, position: Int) {
        holder.textView.text = items[position]
        // Apply any item-specific styling here
    }

    override fun getItemCount(): Int = items.size

    /**
     * Called just before this ViewHolder is returned to the recycle pool.
     * Use this to reset any state that might not be overwritten in onBindViewHolder.
     *
     * This acts as a safety net to prevent recycled views from appearing incorrectly.
     */
    override fun onViewRecycled(holder: ItemViewHolder) {
        super.onViewRecycled(holder)
        // Reset to default visual state
        holder.itemView.setBackgroundColor(Color.TRANSPARENT)
        holder.textView.text = ""
        // Reset any other state as needed (checked state, image, etc.)
    }
}
```

> **Summary:** Always account for recycled state. Either explicitly set every property you might change in `onBindViewHolder` (covering both the modified and the default case), or use `onViewRecycled` to perform a clean reset before the view is reused. Overlooking this is one of the most common sources of visual bugs in `RecyclerView` lists.

---

[↑ Back to Table of Contents](#table-of-contents)
