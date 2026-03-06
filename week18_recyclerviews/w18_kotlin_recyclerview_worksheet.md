# Worksheet: RecyclerViews

---

## Table of Contents

- [Quiz](#quiz)
  - [Part A: Fill in the Blanks](#part-a-fill-in-the-blanks)
  - [Part B: Multiple Choice](#part-b-multiple-choice)
- [Tasks](#tasks)
  - [Task 1: Numbered List Items](#task-1-numbered-list-items)
  - [Task 2: Click Listener on Cards](#task-2-click-listener-on-cards)
  - [Challenge Task: Swappable Layout Manager](#challenge-task-swappable-layout-manager)

---

## Quiz

### Part A: Fill in the Blanks

Fill each blank with the correct word from the word bank below each question.

---

**Question 1**

A `RecyclerView` improves on `ListView` because it __________ views that have scrolled off screen, placing them into a __________ and __________ them for newly visible items. This means that only the __________ items require active `View` objects at any given time.

**Word bank (scrambled):** `pool` &nbsp;|&nbsp; `reuses` &nbsp;|&nbsp; `visible` &nbsp;|&nbsp; `recycles`

---

**Question 2**

When you extend `RecyclerView.Adapter`, you must implement three methods. `__________` is called when a brand-new view is needed and returns a `ViewHolder`. `__________` is called each time an item scrolls into view and fills the views with data. `__________` tells the `RecyclerView` how many rows exist in the dataset.

**Word bank (scrambled):** `getItemCount()` &nbsp;|&nbsp; `onBindViewHolder()` &nbsp;|&nbsp; `onCreateViewHolder()`

---

**Question 3**

A `ViewHolder` stores __________ to the views inside a single item layout. This avoids repeated calls to `__________`, which is expensive because it traverses the entire __________. The `ViewHolder` is typically defined as an __________ class inside the `Adapter`.

**Word bank (scrambled):** `view hierarchy` &nbsp;|&nbsp; `inner` &nbsp;|&nbsp; `references` &nbsp;|&nbsp; `findViewById()`

---

### Part B: Multiple Choice

Circle or write the letter of the single best answer.

---

**Question 4**

Which `LayoutManager` would you use to display `RecyclerView` items in a **two-column grid**?

- A) `LinearLayoutManager(this)`
- B) `GridLayoutManager(this, 2)`
- C) `StaggeredGridLayoutManager(this)`
- D) `TableLayoutManager(this, 2)`

---

**Question 5**

In the following code, what is the purpose of passing `false` as the third argument to `inflate()`?

```kotlin
val view = LayoutInflater.from(parent.context)
    .inflate(R.layout.card_layout, parent, false)
```

- A) It prevents the card layout from being cached in memory
- B) It inflates the layout without attaching it to the parent, because `RecyclerView` manages its own attachment
- C) It disables the drop shadow on the `CardView`
- D) It tells the inflater to skip applying the parent's layout parameters

---

**Question 6**

Consider this diagram of a scrolling `RecyclerView`:

```
Before scroll:           After user scrolls down:
+---------------+        +---------------+
|  Item 1  [V1] |        |  Item 2  [V2] |
|  Item 2  [V2] |  -->   |  Item 3  [V3] |
|  Item 3  [V3] |        |  Item 4  [V1] |  <-- What happened to V1?
+---------------+        +---------------+
```

`[V1]`, `[V2]`, `[V3]` represent `View` objects. What happened to `V1` when `Item 1` scrolled off screen?

- A) It was destroyed and a new `View` object was created for `Item 4`
- B) It was hidden with `visibility = GONE` and kept in memory unused
- C) It was placed into the recycle pool and reused as the `View` for `Item 4`
- D) It was saved to disk and reloaded when the user scrolls back up

---

## Tasks

### Task 1: Numbered List Items

**Goal:** Modify an adapter so that each card displays an **item number** alongside the title and subtitle — for example, `"1. Kotlin"` instead of `"Kotlin"`.

You have the following `card_layout.xml` with two `TextView`s (`textViewTitle` and `textViewSubtitle`) and the `ListItem` data class from the lecture.

Complete the `onBindViewHolder()` method in the adapter below so that the title is prefixed with its **1-based position number** (i.e. position 0 appears as `"1. Kotlin"`).

```kotlin
class NumberedAdapter(
    private val items: List<ListItem>
) : RecyclerView.Adapter<NumberedAdapter.NumberedViewHolder>() {

    inner class NumberedViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val binding = CardLayoutBinding.bind(itemView)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): NumberedViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.card_layout, parent, false)
        return NumberedViewHolder(view)
    }

    override fun onBindViewHolder(holder: NumberedViewHolder, position: Int) {
        val item = items[position]

        // TODO: Set textViewTitle to show the 1-based position number and the title
        //       e.g. "1. Kotlin"

        // TODO: Set textViewSubtitle to show the subtitle as normal

    }

    override fun getItemCount(): Int = items.size
}
```

**Sample output (cards on screen):**

```
+------------------------------------------+
|  1. Kotlin                               |
|  Modern, concise language for Android    |
+------------------------------------------+

+------------------------------------------+
|  2. Java                                 |
|  Original Android language               |
+------------------------------------------+

+------------------------------------------+
|  3. Python                               |
|  Popular for scripting and data science  |
+------------------------------------------+
```

---

### Task 2: Click Listener on Cards

**Goal:** Make each card respond to a tap by displaying a `Toast` message that reads `"You selected: <title>"` — e.g. `"You selected: Kotlin"`.

The adapter constructor already accepts a lambda parameter `onItemClick` that receives the clicked `ListItem`. Your job is to wire it up inside `onBindViewHolder()`.

```kotlin
class ClickableAdapter(
    private val items: List<ListItem>,
    private val onItemClick: (ListItem) -> Unit   // lambda called when a card is tapped
) : RecyclerView.Adapter<ClickableAdapter.ClickableViewHolder>() {

    inner class ClickableViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val binding = CardLayoutBinding.bind(itemView)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ClickableViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.card_layout, parent, false)
        return ClickableViewHolder(view)
    }

    override fun onBindViewHolder(holder: ClickableViewHolder, position: Int) {
        val item = items[position]
        holder.binding.textViewTitle.text    = item.title
        holder.binding.textViewSubtitle.text = item.subtitle

        // TODO: Set a click listener on holder.itemView so that when the card
        //       is tapped, onItemClick is called with the current item
    }

    override fun getItemCount(): Int = items.size
}
```

In `MainActivity`, the adapter is created like this — complete the lambda:

```kotlin
recyclerAdapter = ClickableAdapter(itemList) { clickedItem ->
    // TODO: Show a Toast that reads "You selected: <clickedItem.title>"
    //       Hint: Toast.makeText(this, "...", Toast.LENGTH_SHORT).show()
}
```

**Sample behaviour:**

```
User taps the "Kotlin" card
  --> Toast appears: "You selected: Kotlin"

User taps the "Python" card
  --> Toast appears: "You selected: Python"
```

---

### Challenge Task: Swappable Layout Manager

**Goal:** Add a **toggle button** to `activity_main.xml` that switches the `RecyclerView` between a `LinearLayoutManager` (vertical list) and a `GridLayoutManager` with 2 columns each time it is pressed. The button label should update to reflect the **current** layout mode.

The layout XML and partial `MainActivity` are given below. Complete the missing logic.

**`activity_main.xml` (already provided — do not change):**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/btnToggleLayout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Switch to Grid"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toBottomOf="@id/btnToggleLayout"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

**`MainActivity.kt` — complete the missing sections:**

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding
    private lateinit var recyclerAdapter: MyRecyclerAdapter

    // TODO: Declare a Boolean variable to track whether the grid is currently active
    //       Start with the list layout active (isGridLayout = false)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val items = listOf(
            ListItem("Kotlin",     "Modern Android language"),
            ListItem("Java",       "Original Android language"),
            ListItem("Python",     "Popular for scripting"),
            ListItem("Swift",      "Apple's iOS language"),
            ListItem("JavaScript", "The language of the web"),
            ListItem("C#",         "Used in .NET and Unity"),
            ListItem("Rust",       "Focused on memory safety"),
            ListItem("Go",         "Google's back-end language")
        )

        recyclerAdapter = MyRecyclerAdapter(items)
        binding.recyclerView.adapter = recyclerAdapter

        // TODO: Set the initial LayoutManager to LinearLayoutManager

        binding.btnToggleLayout.setOnClickListener {
            // TODO: Toggle isGridLayout between true and false

            // TODO: If isGridLayout is now true:
            //         - Set LayoutManager to GridLayoutManager(this, 2)
            //         - Update button text to "Switch to List"
            //       If isGridLayout is now false:
            //         - Set LayoutManager to LinearLayoutManager(this)
            //         - Update button text to "Switch to Grid"
        }
    }
}
```

**Expected behaviour:**

```
App launches:
  RecyclerView shows a vertical list
  Button reads "Switch to Grid"

User taps button:
  RecyclerView switches to a 2-column grid
  Button reads "Switch to List"

User taps button again:
  RecyclerView switches back to a vertical list
  Button reads "Switch to Grid"
```
