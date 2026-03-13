# Worksheet: Fragments

---

## Quiz

### Part A — Fill in the Blanks

For each question, choose the correct word from the scrambled word bank to complete the sentence. Each word is used once.

---

**Question 1**

Word bank: *(scrambled)* `onViewCreated` &nbsp;|&nbsp; `onCreate` &nbsp;|&nbsp; `binding` &nbsp;|&nbsp; `onCreateView`

Complete the following passage about the Fragment lifecycle:

> When a Fragment is first constructed, _______________ is called, but the UI does not yet exist. The Fragment inflates its XML layout inside _______________, at which point the _______________ object is assigned. The earliest safe place to interact with views — for example, setting up click listeners — is inside _______________.

---

**Question 2**

Word bank: *(scrambled)* `FragmentContainerView` &nbsp;|&nbsp; `android:name` &nbsp;|&nbsp; `supportFragmentManager` &nbsp;|&nbsp; `findFragmentById`

Complete the following passage about hosting and locating Fragments:

> To place a Fragment inside an Activity's XML layout, you use a _______________. The attribute _______________ specifies which Fragment class to instantiate at runtime. Once the app is running, an Activity can locate a hosted Fragment programmatically by calling _______________ on the result of _______________.

---

**Question 3**

Word bank: *(scrambled)* `isInitialized` &nbsp;|&nbsp; `lateinit` &nbsp;|&nbsp; `onAttach` &nbsp;|&nbsp; `interface`

Complete the following passage about safe Fragment patterns:

> Because the Fragment's binding is declared as a _______________ variable, it cannot be checked for `null` to determine whether it has been assigned yet. Instead, Kotlin provides the `::binding.` _______________ syntax to guard against accessing it too early. When communicating from a Fragment to its host Activity, the recommended approach is to define an _______________ that the Activity implements. The Fragment captures the Activity as a listener inside _______________, where the host is guaranteed to exist.

---

### Part B — Multiple Choice

Circle or note the letter of the best answer for each question.

---

**Question 4**

A developer adds a `Button` to a Fragment's XML layout and sets `android:onClick="handleClick"`. The `handleClick` method is defined inside the Fragment class. What happens at runtime?

- A) The button click calls `handleClick()` in the Fragment as expected
- B) The app crashes because Android looks for `handleClick` in the **Activity**, not the Fragment
- C) The button is ignored silently because `onClick` is not supported in Fragment layouts
- D) Android automatically delegates the call to the Fragment after searching the Activity

---

**Question 5**

A Fragment has a `lateinit var binding` property. An Activity calls a public method on the Fragment during the Activity's own `onCreate()`, before the Fragment's `onCreateView()` has run. The method internally accesses `binding`. Which of the following statements is correct?

- A) The call is safe because `lateinit var` is automatically initialised to `null`
- B) The app crashes because accessing an uninitialised `lateinit var` throws a `UninitializedPropertyAccessException`
- C) The Fragment silently skips the method body until `onCreateView` has run
- D) Android queues the method call and replays it after `onViewCreated`

---

**Question 6**

Which of the following best describes the recommended communication pattern from a Fragment to its host Activity?

```
Fragment  --(A)-->  Activity
Fragment  --(B)-->  Activity
Fragment  --(C)-->  Activity
Fragment  --(D)-->  Activity
```

- A) Cast the Fragment's `activity` property to the known Activity class and access its views directly
- B) Use a shared `ViewModel` to post events to a `LiveData` object observed by the Activity
- C) Define an interface inside the Fragment; have the Activity implement it; capture the Activity as a listener in `onAttach()`
- D) Call `parentFragmentManager.findFragmentById()` from the Fragment to retrieve the Activity's root view

---

## Tasks

### Task 1 — Fragment with a Stateful Counter

Create a Fragment that displays a counter value in a `TextView` and provides two buttons: one to **increment** the counter and one to **reset** it to zero. The counter should update the `TextView` immediately on each button press.

**Scaffolding:**

```kotlin
// CounterFragment.kt
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class CounterFragment : Fragment() {

    private lateinit var binding: FragmentCounterBinding

    // TODO: declare a private variable to hold the counter value

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentCounterBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // TODO: set up the increment button click listener
        //       - increase the counter
        //       - update binding.counterTextView with the new value

        // TODO: set up the reset button click listener
        //       - reset the counter to zero
        //       - update binding.counterTextView to show "0"
    }

    // TODO: add a private helper function updateDisplay() that sets
    //       binding.counterTextView.text to the current counter value
}
```

**Fragment layout (`fragment_counter.xml`) — for reference:**

```xml
<!-- Three views are provided: a TextView for the count and two Buttons -->
<!-- counterTextView, incrementButton, resetButton -->
```

**Expected behaviour:**

```
Initial display:
  Count: 0

After pressing Increment three times:
  Count: 3

After pressing Reset:
  Count: 0
```

---

### Task 2 — Fragment-to-Activity Communication with a Result

Create a Fragment (`TemperatureFragment`) that contains an `EditText` for the user to enter a temperature in Celsius and a Button labelled **Convert**. When the button is pressed, the Fragment should notify the host Activity via an interface. The Activity should convert the value to Fahrenheit (formula: `F = C × 9/5 + 32`) and display the result in a `TextView` that exists in the **Activity's** layout (not the Fragment's layout).

**Scaffolding:**

```kotlin
// TemperatureFragment.kt
import android.content.Context
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class TemperatureFragment : Fragment() {

    private lateinit var binding: FragmentTemperatureBinding

    // TODO: define an interface with a single method:
    //       fun onConvertRequested(celsius: Double)

    // TODO: declare a nullable listener property of the interface type

    override fun onAttach(context: Context) {
        super.onAttach(context)
        // TODO: check that context implements the interface and assign to listener
        //       throw a RuntimeException if it does not
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentTemperatureBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // TODO: set up the convert button click listener
        //       - read the text from binding.celsiusInput
        //       - convert it to a Double (use toDoubleOrNull() to handle bad input)
        //       - if valid, call listener?.onConvertRequested(value)
    }

    override fun onDetach() {
        super.onDetach()
        // TODO: set listener to null
    }
}
```

```kotlin
// MainActivity.kt
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

// TODO: implement the TemperatureFragment interface
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }

    // TODO: override the interface method
    //       - calculate Fahrenheit from the received Celsius value
    //       - display the result in binding.resultTextView in the Activity layout
    //       - format to one decimal place using String.format("%.1f", value)
}
```

**Expected behaviour:**

```
User types: 100
Presses Convert

Activity displays: 100.0°C = 212.0°F

User types: 0
Presses Convert

Activity displays: 0.0°C = 32.0°F
```

---

### Challenge Task — Two Fragments, One Coordinator

Build an Activity that hosts **two Fragments side by side**:

- **InputFragment** — contains an `EditText` and an **Add** button. When Add is pressed, it sends the entered text to the Activity via an interface.
- **ListFragment** — contains a `TextView` that displays all submitted entries, one per line, in the order they were added.

The Activity acts as the coordinator: it receives new entries from `InputFragment` and passes them to `ListFragment` to display. The list in `ListFragment` should be maintained as a `MutableList<String>` and rebuilt each time a new item is added.

**Scaffolding:**

```kotlin
// InputFragment.kt
import android.content.Context
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class InputFragment : Fragment() {

    private lateinit var binding: FragmentInputBinding

    interface InputListener {
        fun onEntryAdded(text: String)
    }

    private var listener: InputListener? = null

    override fun onAttach(context: Context) {
        super.onAttach(context)
        // TODO: assign listener, throw if not implemented
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentInputBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        binding.addButton.setOnClickListener {
            val text = binding.entryInput.text.toString().trim()
            // TODO: if text is not blank, call listener?.onEntryAdded(text)
            //       then clear binding.entryInput
        }
    }

    override fun onDetach() {
        super.onDetach()
        listener = null
    }
}
```

```kotlin
// ListFragment.kt
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class ListFragment : Fragment() {

    private lateinit var binding: FragmentListBinding

    // TODO: declare a MutableList<String> to store entries

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentListBinding.inflate(inflater)
        return binding.root
    }

    // Public method called by the Activity to add a new entry
    fun addEntry(text: String) {
        if (this::binding.isInitialized) {
            // TODO: add text to the list
            // TODO: join the list with "\n" and set it on binding.listTextView
        }
    }
}
```

```kotlin
// MainActivity.kt
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity(), InputFragment.InputListener {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        // Layout contains two FragmentContainerViews:
        //   R.id.inputFragmentContainer  → InputFragment
        //   R.id.listFragmentContainer   → ListFragment
    }

    override fun onEntryAdded(text: String) {
        // TODO: use supportFragmentManager.findFragmentById() to get the ListFragment
        //       then call listFragment.addEntry(text)
    }
}
```

**Expected behaviour:**

```
User types "Kotlin" → presses Add
User types "Android" → presses Add
User types "Fragments" → presses Add

ListFragment displays:
  Kotlin
  Android
  Fragments

Input field is cleared after each submission.
```
