# Worksheet: More RecyclerViews, Keyboard Handling & Extension Functions

---

## Quiz

### Part A — Fill in the Blanks

For each question, choose the correct words from the scrambled word bank to complete the statement. Each word is used **once**.

---

**Question 1**

To hide the soft keyboard programmatically, you request the `___________` service from the system using `___________`. You then call `___________` on it, passing a valid window token obtained from any view currently attached to the screen.

**Word bank (scrambled):** `getSystemService` · `hideSoftInputFromWindow` · `InputMethodManager`

---

**Question 2**

A `TextView.OnEditorActionListener` receives three parameters in its `onEditorAction` callback. The `___________` parameter identifies which keyboard action was triggered. When the soft keyboard's action button is used (rather than a hardware key), the `___________` parameter will be `null`. Returning `___________` from the method signals that your code has handled the event and Android should not process it further.

**Word bank (scrambled):** `event` · `true` · `actionId`

---

**Question 3**

In a `RecyclerView`, when a `ViewHolder` scrolls off screen it is placed in a pool and reused — this is called ___________. Only the properties explicitly set inside `___________` are refreshed when a view is reused. To reset view state before it is returned to the pool, you can override the `___________` method in the adapter.

**Word bank (scrambled):** `onBindViewHolder` · `onViewRecycled` · `recycling`

---

### Part B — Multiple Choice

Circle or highlight the single best answer for each question.

---

**Question 4**

Which of the following best describes what an extension function does in Kotlin?

- A) It subclasses an existing class to add new behaviour
- B) It adds a new method to an existing class without modifying its source or subclassing it
- C) It overrides an existing method in a class at runtime
- D) It creates a static utility class that wraps the target class

---

**Question 5**

You are building a `RecyclerView` list where some items display a red background to indicate an error. After testing on a device, you notice that items that should *not* have a red background sometimes appear with one after scrolling. What is the most likely cause?

- A) The `LayoutManager` is not set correctly on the `RecyclerView`
- B) The `onCreateViewHolder` method is being called too many times
- C) `onBindViewHolder` only sets the background for the error case, not the default (no-error) case
- D) `notifyItemChanged` was not called after modifying the data list

---

**Question 6**

When implementing `ItemTouchHelper.Callback` for a `RecyclerView`, which method is responsible for defining which directions are enabled for drag and swipe gestures?

- A) `onMove`
- B) `onSwiped`
- C) `getMovementFlags`
- D) `attachToRecyclerView`

---

## Tasks

### Task 1 — Extension Function for Input Validation

Write a Kotlin extension function called `trimmedText()` on `android.widget.EditText`. The function should return the current text of the `EditText` as a `String` with any leading or trailing whitespace removed.

Then, write a second extension function called `isBlankInput()` on `EditText` that returns `true` if the trimmed text is empty, and `false` otherwise.

Use these two extension functions inside an `Activity` so that when a **Submit** button is clicked:

- If the input is blank, show a `Toast` saying `"Please enter something"`
- Otherwise, show a `Toast` saying `"You entered: [trimmed text]"`

**Scaffolding:**

```kotlin
import android.content.Context
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

// --- Write your extension functions here ---

// fun EditText.trimmedText(): String { ... }

// fun EditText.isBlankInput(): Boolean { ... }

// -------------------------------------------

class Task1Activity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val editText = findViewById<EditText>(R.id.editTextInput)
        val submitButton = findViewById<Button>(R.id.buttonSubmit)

        submitButton.setOnClickListener {
            // Use your extension functions here
            if (/* TODO */) {
                Toast.makeText(this, "Please enter something", Toast.LENGTH_SHORT).show()
            } else {
                Toast.makeText(this, "You entered: ${/* TODO */}", Toast.LENGTH_SHORT).show()
            }
        }
    }
}
```

**Sample interaction:**

```
User taps Submit with input "   "    ->  Toast: "Please enter something"
User taps Submit with input "  Hi "  ->  Toast: "You entered: Hi"
```

---

### Task 2 — RecyclerView with Safe Recycling

You are given a data class `Contact` with two fields: `name: String` and `isFavourite: Boolean`.

Write a `RecyclerView.Adapter` called `ContactAdapter` that displays a list of `Contact` objects. The adapter must:

- Display the contact's name in a `TextView`
- Show **bold text and a yellow background** for favourite contacts
- Show **normal text and a white background** for non-favourite contacts
- Correctly handle view recycling so no visual state bleeds between items when the user scrolls

**Scaffolding:**

```kotlin
import android.graphics.Color
import android.graphics.Typeface
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

data class Contact(val name: String, val isFavourite: Boolean)

class ContactAdapter(
    private val contacts: List<Contact>
) : RecyclerView.Adapter<ContactAdapter.ContactViewHolder>() {

    class ContactViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val textView: TextView = itemView.findViewById(android.R.id.text1)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ContactViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(android.R.layout.simple_list_item_1, parent, false)
        return ContactViewHolder(view)
    }

    override fun onBindViewHolder(holder: ContactViewHolder, position: Int) {
        val contact = contacts[position]
        holder.textView.text = contact.name

        if (contact.isFavourite) {
            // TODO: set bold typeface and yellow background
        } else {
            // TODO: reset to normal typeface and white background
            //       (this is essential — why?)
        }
    }

    override fun getItemCount(): Int = contacts.size
}
```

**Sample data and expected display:**

```
contacts = [
    Contact("Alice",   isFavourite = true),
    Contact("Bob",     isFavourite = false),
    Contact("Charlie", isFavourite = true),
    Contact("Diana",   isFavourite = false),
]

+----------------------------------+
| Alice   (bold, yellow background)|
| Bob     (normal, white)          |
| Charlie (bold, yellow background)|
| Diana   (normal, white)          |
+----------------------------------+
```

---

### Challenge Task — Filterable RecyclerView with Swipe-to-Delete

Build an `Activity` that displays a list of country names in a `RecyclerView`. The activity has an `EditText` above the list. When the user types a query and presses **Done** on the keyboard, the list filters to show only country names that contain the query (case-insensitive). Swiping an item left or right removes it from the list permanently.

Requirements:

- Use `TextView.OnEditorActionListener` (or its lambda form) to trigger filtering on `IME_ACTION_DONE`
- Use `ItemTouchHelper` with a `Callback` to handle swipe-to-delete
- The adapter must work on a **mutable** list so items can be removed and the list can be replaced when filtering
- Write a `View.hideKeyboard()` extension function and call it after the filter is applied

**Scaffolding:**

```kotlin
import android.content.Context
import android.os.Bundle
import android.view.View
import android.view.inputmethod.EditorInfo
import android.view.inputmethod.InputMethodManager
import android.widget.EditText
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.ItemTouchHelper
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView

// Extension function — implement this
fun View.hideKeyboard() { /* TODO */ }

class ChallengeActivity : AppCompatActivity() {

    // Full list of countries (source of truth for filtering)
    private val allCountries = mutableListOf(
        "Argentina", "Australia", "Brazil", "Canada", "China",
        "France", "Germany", "India", "Italy", "Japan",
        "Mexico", "Nigeria", "Norway", "South Africa", "Spain",
        "Sweden", "Thailand", "Turkey", "United Kingdom", "United States"
    )

    // The adapter works on a mutable copy that gets replaced on each filter
    private lateinit var adapter: CountryAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val searchField = findViewById<EditText>(R.id.editTextSearch)
        val recyclerView = findViewById<RecyclerView>(R.id.recyclerView)

        // TODO: Initialise adapter with a mutable copy of allCountries
        // TODO: Set up RecyclerView with LinearLayoutManager and adapter
        // TODO: Attach ItemTouchHelper for swipe-to-delete
        // TODO: Set OnEditorActionListener on searchField to filter the list on DONE

        searchField.setOnEditorActionListener { v, actionId, _ ->
            if (actionId == EditorInfo.IME_ACTION_DONE) {
                val query = v.text.toString()
                // TODO: filter allCountries by query and update the adapter
                // TODO: hide the keyboard using your extension function
                true
            } else {
                false
            }
        }
    }
}

// TODO: Implement CountryAdapter
// It needs:
//   - a MutableList<String> of country names
//   - a removeItem(position: Int) method
//   - a updateList(newList: List<String>) method that replaces the data and calls notifyDataSetChanged()

class CountryAdapter(/* TODO */) : RecyclerView.Adapter</* TODO */>() {
    // TODO
}

// TODO: Implement DragSwipeCallback (swipe-to-delete only, no drag needed)
class SwipeToDeleteCallback(/* TODO */) : ItemTouchHelper.Callback() {
    // TODO
}
```

**Sample behaviour:**

```
Initial list:                 After typing "an" + Done:
+------------------+          +------------------+
| Argentina        |          | Argentina        |
| Australia        |          | Germany          |
| Brazil           |          | Japan            |
| Canada           |          +------------------+
| China            |
| ...              |          Swipe "Japan" left:
+------------------+          +------------------+
                              | Argentina        |
                              | Germany          |
                              +------------------+
```
