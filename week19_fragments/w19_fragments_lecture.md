# Fragments

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [1. What is a Fragment?](#1-what-is-a-fragment)
- [2. Creating a Fragment](#2-creating-a-fragment)
- [3. The Fragment Lifecycle](#3-the-fragment-lifecycle)
- [4. Lifecycle Issue: Binding Initialisation](#4-lifecycle-issue-binding-initialisation)
- [5. Adding a Fragment to an Activity](#5-adding-a-fragment-to-an-activity)
- [6. Communicating Between a Fragment and its Activity](#6-communicating-between-a-fragment-and-its-activity)
- [7. Communication Between Fragments](#7-communication-between-fragments)
- [8. Caveat: onClick Handlers in Fragment XML](#8-caveat-onclick-handlers-in-fragment-xml)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain what a Fragment is and why it is useful in Android development
- Create a Fragment with an associated XML layout
- Describe the Fragment lifecycle and its relationship to the Activity lifecycle
- Add a Fragment to an Activity layout using `FragmentContainerView`
- Use an interface to enable communication from a Fragment to its host Activity
- Retrieve and communicate with Fragments via the `FragmentManager`
- Identify the caveat around `onClick` handlers in Fragment XML layouts

---

## 1. What is a Fragment?

Think of an Activity as the whole "screen" your user sees. A **Fragment** is a self-contained, reusable portion of that screen — a modular building block that lives *inside* an Activity. You can think of it like a panel: a screen might have a list panel on the left and a detail panel on the right, each being a separate Fragment hosted by the same Activity.

Fragments were introduced to help Android apps scale across different screen sizes. A phone might show one Fragment at a time, while a tablet might show two side-by-side within the same Activity.

### Key Characteristics of a Fragment

- A Fragment represents a **modular section of an Activity**
- It has its **own lifecycle**, which is influenced by (and nested inside) the Activity's lifecycle
- Fragments can be **added and removed** while the Activity is running — you are not limited to one static arrangement
- A single Activity can host **multiple Fragments simultaneously**
- Fragments are **designed to be reusable** — the same Fragment class can be used in multiple Activities
- A Fragment typically has an **associated XML layout file** that defines its UI
- Fragments can be added directly to an Activity's XML layout; you can specify a `layout` property on the Fragment element to see a preview in Android Studio

### A Simple Mental Model

```
+----------------------------------------------------+
|                  MainActivity                      |
|                                                    |
|   +-------------------+  +---------------------+  |
|   |   ListFragment    |  |   DetailFragment    |  |
|   |                   |  |                     |  |
|   |  - Item 1         |  |  Title: Item 1      |  |
|   |  - Item 2         |  |  Description: ...   |  |
|   |  - Item 3         |  |                     |  |
|   +-------------------+  +---------------------+  |
|                                                    |
+----------------------------------------------------+
```

On a tablet, both fragments are visible at once. On a phone, you would navigate from the list to the detail, with each Fragment potentially being swapped in and out of the same Activity.

### Why Not Just Use Multiple Activities?

You could use separate Activities for each screen, and for simple apps that is perfectly fine. However, Fragments give you finer-grained control: they share the same Activity context, making data sharing simpler, and they can be dynamically swapped without launching a new Activity. This makes transitions smoother and the architecture more flexible.

[↑ Back to Table of Contents](#table-of-contents)

---

## 2. Creating a Fragment

Creating a Fragment in Android Studio is very similar to creating an Activity:

1. Right-click your package in the Project view
2. Select **New → Fragment → Fragment (Blank)**
3. Give your Fragment a name (e.g. `MyFragment`)
4. Check the option to create an associated XML layout file

Android Studio will generate two files:
- `MyFragment.kt` — the Kotlin class for your Fragment
- `fragment_my.xml` — the associated XML layout (defaulting to `FrameLayout`, which you can change to `ConstraintLayout` or any other layout type as needed)

### Cleaning Up the Boilerplate

When Android Studio generates a Fragment, it adds a significant amount of boilerplate code that you will not typically need for straightforward Fragments. It is good practice to **remove this unnecessary code** so you are not maintaining or debugging code that serves no purpose.

After removing the boilerplate, a clean Fragment class looks something like this:

```kotlin
// Import statements needed for Fragment functionality and view binding
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

// MyFragment extends Fragment, meaning it inherits all Fragment lifecycle
// behaviour and can be hosted inside an Activity
class MyFragment : Fragment() {

    // 'binding' gives us type-safe access to the views in fragment_my.xml
    // 'lateinit' means we promise to assign it before using it
    private lateinit var binding: FragmentMyBinding

    // onCreateView is called when the Fragment needs to draw its UI.
    // 'inflater' converts the XML layout into actual View objects.
    // 'container' is the parent ViewGroup from the Activity.
    // 'savedInstanceState' holds any previously saved state.
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        // Inflate the layout and create the binding object.
        // The binding object provides direct references to all views in the XML.
        binding = FragmentMyBinding.inflate(inflater)

        // Return the root view of the inflated layout so Android knows what to display
        return binding.root
    }
}
```

> **Note on View Binding:** The class name `FragmentMyBinding` is automatically generated from the layout file name `fragment_my.xml`. Android Studio generates a binding class for each layout file when View Binding is enabled in your `build.gradle` file (`buildFeatures { viewBinding = true }`).

### Sample Output (Logcat)

There is no console output for a Fragment creation itself, but you would see the Fragment appear within the Activity's layout on screen. You can verify lifecycle callbacks are firing by adding log statements:

```kotlin
import android.os.Bundle
import android.util.Log
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class MyFragment : Fragment() {

    private lateinit var binding: FragmentMyBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // This log confirms the Fragment's onCreate has been called
        Log.d("MyFragment", "onCreate called")
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        Log.d("MyFragment", "onCreateView called")
        binding = FragmentMyBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // onViewCreated is where you safely interact with the views,
        // as the layout is fully inflated at this point
        Log.d("MyFragment", "onViewCreated called - UI is ready")
    }
}
```

```
// Sample Logcat output when Fragment is attached and displayed:
D/MyFragment: onCreate called
D/MyFragment: onCreateView called
D/MyFragment: onViewCreated called - UI is ready
```

[↑ Back to Table of Contents](#table-of-contents)

---

## 3. The Fragment Lifecycle

The Fragment lifecycle is similar to the Activity lifecycle, but it has some additional callbacks to account for the fact that a Fragment is embedded inside an Activity. Understanding this lifecycle is essential for writing Fragments that behave correctly.

### Lifecycle Diagram

```
Fragment Lifecycle        Fragment Callbacks         View Lifecycle
                         +------------------+
                         |   onCreate()     |
                         +--------+---------+
                                  |
    CREATED ---->        +--------+---------+
                         | onCreateView()   |        INITIALIZED
                         +--------+---------+
                                  |
                         +--------+---------+
                         | onViewCreated()  |
                         +--------+---------+
                                  |
                         +-----------------+
                         |onViewStateRes.. |        CREATED
                         +-----------------+
                                  |
    STARTED ---->        +--------+---------+
                         |   onStart()     |        STARTED
                         +--------+---------+
                                  |
    RESUMED ---->        +--------+---------+
                         |   onResume()    |        RESUMED
                         +--------+---------+
                                  |
                                  | (user navigates away)
                                  |
    STARTED ---->        +--------+---------+
                         |   onPause()     |        STARTED
                         +--------+---------+
                                  |
                         +--------+---------+
                         |   onStop()      |
                         +--------+---------+
                                  |
    CREATED ---->        +-----------------+
                         |onSaveInstance.. |        CREATED
                         +-----------------+
                                  |
                         +-----------------+
                         | onDestroyView() |        DESTROYED
                         +-----------------+
                                  |
    DESTROYED ---->      +-----------------+
                         |  onDestroy()    |
                         +-----------------+
```

### Key Differences From the Activity Lifecycle

The most important difference to understand is this: a Fragment has **two creation phases** before it is fully visible to the user.

| Callback | What Happens |
|---|---|
| `onCreate()` | The Fragment is created, but has no UI yet |
| `onCreateView()` | The Fragment inflates its XML layout and creates its `binding` |
| `onViewCreated()` | The layout is fully ready — safe to interact with views here |
| `onStart()` | Fragment becomes visible |
| `onResume()` | Fragment is in the foreground and interactive |
| `onPause()` | Another Fragment or Activity is taking focus |
| `onStop()` | Fragment is no longer visible |
| `onSaveInstanceState()` | Save data before the view is torn down |
| `onDestroyView()` | The view hierarchy is destroyed, but the Fragment object lives on |
| `onDestroy()` | The Fragment itself is destroyed |

### The Critical Insight: onViewCreated

A very common mistake for developers new to Fragments is trying to interact with views inside `onCreate()`. At that point, the view does not yet exist — it is only created during `onCreateView()`. The safe place to set up click listeners, populate data into views, and so on is inside **`onViewCreated()`**.

```kotlin
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.Toast
import androidx.fragment.app.Fragment

class MyFragment : Fragment() {

    private lateinit var binding: FragmentMyBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        // Inflate the layout — the views exist from this point
        binding = FragmentMyBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // CORRECT: Setting up a click listener here, in onViewCreated,
        // because we know the binding (and therefore the button) exists
        binding.myButton.setOnClickListener {
            Toast.makeText(requireContext(), "Button clicked!", Toast.LENGTH_SHORT).show()
        }
    }
}
```

[↑ Back to Table of Contents](#table-of-contents)

---

## 4. Lifecycle Issue: Binding Initialisation

Now that you understand the Fragment lifecycle, there is an important practical problem to be aware of when Fragments and Activities interact.

### The Problem

When an Activity's `onCreate()` method runs, any Fragments in the layout are not yet fully initialised. Specifically, the Fragment's `onCreateView()` has not yet been called, which means the `binding` property has not been assigned yet.

This matters when an Activity tries to call a **public method** on a Fragment that internally uses the binding. The Fragment object exists, but `binding` is uninitialised, and accessing it will crash the app.

You might think you could check `if (binding == null)` to guard against this. However, because `binding` is declared as `lateinit var`, it is never `null` — it is simply uninitialised. Checking for `null` does not detect this state.

### The Solution: `isInitialized`

Kotlin provides a way to check whether a `lateinit var` has been initialised yet, using the `::propertyName.isInitialized` syntax. You use this via the `this::` reference:

```kotlin
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class MyFragment : Fragment() {

    private lateinit var binding: FragmentMyBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentMyBinding.inflate(inflater)
        return binding.root
    }

    // This is a public method that the host Activity might call.
    // We guard against the case where the Activity calls this before
    // the Fragment's view has been created.
    fun updateLabel(text: String) {
        // Check whether 'binding' has actually been initialised yet.
        // If the Fragment's onCreateView hasn't run, this will be false.
        if (this::binding.isInitialized) {
            binding.myTextView.text = text
        }
        // If binding is not initialised, we silently do nothing —
        // the Activity is calling too early in the lifecycle.
    }
}
```

```
// If the Activity calls updateLabel() before onCreateView has run:
// Nothing crashes — the isInitialized check returns false, and the block is skipped.

// If the Activity calls updateLabel() after onViewCreated has run:
// binding.myTextView.text is updated successfully.
```

This is an elegant Kotlin feature that helps you write more defensive, lifecycle-aware code. As a general principle, you should be careful about designing Fragment APIs that rely on the view being ready — but when you need to, `isInitialized` gives you a safe escape hatch.

[↑ Back to Table of Contents](#table-of-contents)

---

## 5. Adding a Fragment to an Activity

There are two ways to add a Fragment to an Activity: statically in XML, or dynamically in code. The most common approach for beginners is the **static XML approach**, using `FragmentContainerView`.

### Using FragmentContainerView

In Android Studio's Layout Editor, you can drag a `FragmentContainerView` from the Palette onto your Activity's layout — exactly as you would drag a Button or TextView. When you do this, Android Studio will ask you to:

1. **Specify the class** — which Fragment class should be displayed in this container (e.g. `MyFragment`)
2. **Specify the layout** — this is used only for the preview in the design editor; it does not affect runtime behaviour

> **Gotcha:** It is possible to accidentally specify the wrong layout for the preview. This only affects what you see in the editor, not the actual running app, but it can be confusing. Always double-check which layout is associated with which Fragment class.

### What the XML Looks Like

```xml
<!-- activity_main.xml -->
<!-- This is the Activity's layout file. It hosts a FragmentContainerView
     which tells Android to place MyFragment in this area of the screen. -->

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- FragmentContainerView is the recommended way to host a Fragment in XML.
         android:name specifies which Fragment class to instantiate.
         tools:layout is only for the editor preview — not used at runtime. -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragmentContainerView"
        android:name="com.example.myapp.MyFragment"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### The Corresponding Activity

```kotlin
// MainActivity.kt
// The Activity itself is straightforward — Android handles creating
// and attaching the Fragment automatically because it is declared in the XML layout.

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // setContentView loads activity_main.xml, which contains the
        // FragmentContainerView. Android sees the 'android:name' attribute
        // and automatically creates and attaches MyFragment.
        setContentView(R.layout.activity_main)
    }
}
```

```
// When the app runs:
// 1. MainActivity's onCreate() is called
// 2. activity_main.xml is inflated
// 3. Android sees FragmentContainerView with android:name="...MyFragment"
// 4. MyFragment is automatically created and its lifecycle begins
// 5. The Fragment's UI is displayed inside the container
```

### Screen Layout (Phone vs Tablet)

```
Phone (single fragment at a time):       Tablet (two fragments side-by-side):

+---------------------------+            +-------------------------------+
|      MainActivity         |            |        MainActivity           |
|                           |            |                               |
|  +---------------------+  |            |  +-----------+ +-----------+  |
|  |                     |  |            |  |           | |           |  |
|  |    MyFragment       |  |            |  |  ListFrag | | DetailFrg |  |
|  |                     |  |            |  |           | |           |  |
|  |  Hello from a       |  |            |  |  Item 1   | | Details.. |  |
|  |  Fragment!          |  |            |  |  Item 2   | |           |  |
|  |                     |  |            |  |           | |           |  |
|  +---------------------+  |            |  +-----------+ +-----------+  |
|                           |            |                               |
+---------------------------+            +-------------------------------+
```

[↑ Back to Table of Contents](#table-of-contents)

---

## 6. Communicating Between a Fragment and its Activity

This is one of the most important topics in Fragment development, and also one of the most commonly done incorrectly.

### The Temptation: Direct Casting

A Fragment has a nullable `activity` property that gives it a reference to its host Activity. You might be tempted to simply cast this to the known Activity type and then access its views or methods directly:

```kotlin
// DO NOT DO THIS
// This is an anti-pattern — it hard-codes the Fragment to one specific Activity
val mainActivity = activity as MainActivity
mainActivity.someTextView.text = "Updated from Fragment"
```

This approach **destroys reusability**. If you want to use this Fragment in a different Activity, you would have to modify the Fragment's code. The Fragment should not need to know which specific Activity is hosting it.

### The Correct Approach: Interfaces

The proper solution is to define an **interface** that describes the communication contract, and then have the Activity implement that interface. The Fragment holds a reference to the interface type, not to any specific Activity class.

Here is a complete example of the three-part pattern:

**Step 1 — Define the interface inside the Fragment:**

```kotlin
// MyFragment.kt
import android.content.Context
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class MyFragment : Fragment() {

    private lateinit var binding: FragmentMyBinding

    // Define the interface that any host Activity must implement.
    // This describes WHAT the Fragment can ask the Activity to do,
    // without caring HOW the Activity does it.
    interface FragmentListener {
        fun onItemSelected(item: String)
    }

    // This property holds a reference to whatever object implements FragmentListener.
    // In practice, this will be the host Activity — but the Fragment doesn't know that.
    private var listener: FragmentListener? = null

    // onAttach is called when the Fragment is first attached to its Activity.
    // This is the ideal place to capture the Activity as a listener,
    // because the Activity is guaranteed to exist at this point.
    override fun onAttach(context: Context) {
        super.onAttach(context)
        // Check that the Activity actually implements the interface.
        // If it doesn't, throw an informative error immediately.
        if (context is FragmentListener) {
            listener = context
        } else {
            throw RuntimeException("$context must implement FragmentListener")
        }
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentMyBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // When the user taps an item in the Fragment, notify the listener.
        // We use the interface method — not anything Activity-specific.
        binding.myButton.setOnClickListener {
            listener?.onItemSelected("Hello from Fragment!")
        }
    }

    // When the Fragment is detached, clear the listener reference
    // to avoid memory leaks
    override fun onDetach() {
        super.onDetach()
        listener = null
    }
}
```

**Step 2 — Implement the interface in the Activity:**

```kotlin
// MainActivity.kt
import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

// The Activity implements the Fragment's interface
class MainActivity : AppCompatActivity(), MyFragment.FragmentListener {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }

    // This method is called by the Fragment via the interface.
    // The Activity decides what to do with the information.
    override fun onItemSelected(item: String) {
        // In a real app, you might update another part of the UI here.
        Toast.makeText(this, "Activity received: $item", Toast.LENGTH_SHORT).show()
    }
}
```

```
// Runtime flow when the button in MyFragment is tapped:
// 1. MyFragment.onViewCreated: button click fires
// 2. MyFragment calls: listener?.onItemSelected("Hello from Fragment!")
// 3. MainActivity.onItemSelected("Hello from Fragment!") is invoked
// 4. Toast appears: "Activity received: Hello from Fragment!"
```

### Converting an Existing Activity Into a Fragment

When you refactor an existing Activity into a Fragment, you also need to update any calls to Activity methods. For example, if your Activity called `setResult()` to return a result to a calling Activity, inside a Fragment you must prefix this with the `activity?.` reference:

```kotlin
// Inside an Activity:
setResult(Activity.RESULT_OK, intent)

// The equivalent inside a Fragment:
// Note the '?' — this is a safe call because 'activity' is nullable in a Fragment
activity?.setResult(Activity.RESULT_OK, intent)
```

As the slides note, it is worth considering whether a Fragment *should* be terminating an Activity at all — this is arguably a responsibility that belongs to the Activity itself, not to any Fragment it contains.

[↑ Back to Table of Contents](#table-of-contents)

---

## 7. Communication Between Fragments

Once you have multiple Fragments in one Activity, you may need them to share data. For example, selecting an item in a `ListFragment` should update the content shown in a `DetailFragment`.

### Using the FragmentManager

The Activity has a `FragmentManager` object that keeps track of all the Fragments it hosts. You can use it to retrieve a specific Fragment by its container view ID:

```kotlin
// MainActivity.kt
// The Activity acts as a go-between for two Fragments.
// Fragment A notifies the Activity → Activity updates Fragment B.

import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.fragment.app.FragmentManager

class MainActivity : AppCompatActivity(), ListFragment.ListFragmentListener {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }

    // Called by ListFragment when the user selects an item
    override fun onItemSelected(item: String) {
        // Use the FragmentManager to find the DetailFragment by its container ID
        // The ID corresponds to the FragmentContainerView in activity_main.xml
        val fragmentManager: FragmentManager = supportFragmentManager
        val detailFragment = fragmentManager
            .findFragmentById(R.id.detailFragmentContainer) as? DetailFragment

        // If the DetailFragment is present (it might not be on a phone layout),
        // call its public method to update it
        if (detailFragment != null) {
            detailFragment.showDetail(item)
        } else {
            // On a phone, the detail fragment isn't visible — handle this case
            Toast.makeText(this, "Selected: $item", Toast.LENGTH_SHORT).show()
        }
    }
}
```

```kotlin
// ListFragment.kt
// Sends item selection events to the Activity via an interface

import android.content.Context
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class ListFragment : Fragment() {

    private lateinit var binding: FragmentListBinding

    interface ListFragmentListener {
        fun onItemSelected(item: String)
    }

    private var listener: ListFragmentListener? = null

    override fun onAttach(context: Context) {
        super.onAttach(context)
        listener = context as? ListFragmentListener
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentListBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // Simulate a list item click — in a real app this would come from
        // a RecyclerView adapter or similar
        binding.item1Button.setOnClickListener {
            listener?.onItemSelected("Item 1 selected")
        }
    }

    override fun onDetach() {
        super.onDetach()
        listener = null
    }
}
```

```kotlin
// DetailFragment.kt
// Receives and displays information passed to it by the Activity

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class DetailFragment : Fragment() {

    private lateinit var binding: FragmentDetailBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentDetailBinding.inflate(inflater)
        return binding.root
    }

    // Public method that MainActivity calls to update this Fragment's content.
    // The Activity retrieved this Fragment via the FragmentManager.
    fun showDetail(item: String) {
        if (this::binding.isInitialized) {
            binding.detailTextView.text = item
        }
    }
}
```

```
// Communication flow:
// User taps "Item 1" button in ListFragment
//        |
//        v
// ListFragment calls: listener?.onItemSelected("Item 1 selected")
//        |
//        v
// MainActivity.onItemSelected("Item 1 selected") runs
//        |
//        v
// MainActivity uses FragmentManager to find DetailFragment
//        |
//        v
// MainActivity calls: detailFragment.showDetail("Item 1 selected")
//        |
//        v
// DetailFragment updates its TextView to show "Item 1 selected"
```

### Should They Really Be Fragments?

There is an important design question worth asking: if two Fragments need to talk directly to each other constantly and share a lot of state, are they really *separate* enough to justify being Fragments? The Activity-as-liaison pattern can become cumbersome if overused.

If the coupling between two UI components is very tight, it may be simpler and cleaner to combine them into a single Fragment, or to keep them in the same Activity layout without using Fragments at all. Good architecture means choosing the right tool for the situation.

[↑ Back to Table of Contents](#table-of-contents)

---

## 8. Caveat: onClick Handlers in Fragment XML

There is one important gotcha that catches many developers when they first work with Fragments.

### The Problem

In Activity XML layouts, it is possible (though not recommended) to specify an `onClick` attribute directly in the XML, pointing to a method name:

```xml
<!-- This works in an Activity layout, but NOT in a Fragment layout -->
<Button
    android:id="@+id/myButton"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Click Me"
    android:onClick="handleClick" />
```

When you use this attribute in a **Fragment's XML layout**, Android will look for the `handleClick` method in the **Activity**, not in the Fragment. This almost certainly does not exist in the Activity, and the app will crash with a runtime exception.

### The Solution

Always set up click listeners **programmatically** in `onViewCreated()`, using the binding. This is actually the correct approach regardless of whether you are in a Fragment or an Activity:

```kotlin
// MyFragment.kt — the correct way to handle button clicks in a Fragment

import android.os.Bundle
import android.util.Log
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class MyFragment : Fragment() {

    private lateinit var binding: FragmentMyBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentMyBinding.inflate(inflater)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // CORRECT: Set the click listener in code, not in XML.
        // This listener is defined inside the Fragment, exactly where it belongs.
        binding.myButton.setOnClickListener {
            handleClick()
        }
    }

    // The handler method is private to this Fragment — it has nothing to do with the Activity
    private fun handleClick() {
        Log.d("MyFragment", "Button was clicked inside MyFragment")
        // Handle the click here...
    }
}
```

```
// If you had used android:onClick="handleClick" in the XML and the method
// was not in the Activity, you would see a runtime crash like:
// java.lang.IllegalStateException: Could not find method handleClick(View)
//     in a parent or ancestor Context for android:onClick attribute

// With the programmatic approach, everything works correctly:
// D/MyFragment: Button was clicked inside MyFragment
```

### Why the XML onClick Approach is Problematic in General

Even in Activities, the `android:onClick` XML attribute is considered poor practice in modern Android development. It creates an implicit coupling between the XML layout and a specific method signature, makes the code harder to test, and can be confusing to maintain. Using `setOnClickListener` in code is the recommended approach in all cases — and it is the only approach that works correctly in Fragments.

[↑ Back to Table of Contents](#table-of-contents)
