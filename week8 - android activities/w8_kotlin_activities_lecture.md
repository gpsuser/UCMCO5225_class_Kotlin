# Android Activities in Kotlin

## Table of Contents

1. [Learning Outcomes](#learning-outcomes)
2. [What is an Activity?](#what-is-an-activity)
3. [Understanding the Activity Lifecycle](#understanding-the-activity-lifecycle)
4. [Layouts and UI Design](#layouts-and-ui-design)
5. [Measurement Units for Different Screens](#measurement-units-for-different-screens)
6. [Starting and Switching Between Activities](#starting-and-switching-between-activities)
7. [Passing Data Between Activities](#passing-data-between-activities)
8. [Receiving Data Back from Activities](#receiving-data-back-from-activities)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain what an Activity is and its role in Android applications
- Describe the Activity lifecycle and implement appropriate lifecycle methods
- Choose and implement appropriate layouts for different screen scenarios
- Use density-independent pixels (dp) and scalable pixels (sp) correctly
- Create Intents to navigate between Activities
- Pass data to Activities using Intent extras
- Return data from Activities and handle the results

---

## What is an Activity?

An Activity represents a single, focused thing that the user can do. Think of it as one screen in your application. When you open an email app, the inbox is one Activity, composing a new email is another Activity, and reading an email is yet another Activity.

### Key Characteristics of Activities

- **Single Screen Focus**: Each Activity typically provides one complete screen of user interface
- **Full Screen by Default**: Activities usually occupy the entire screen, though they can also be floating windows
- **Entry Point**: An Activity serves as an entry point for interacting with the user
- **Independent yet Connected**: Each Activity is independent but can start other Activities

### Basic Activity Structure

Here's a complete, minimal Activity in Kotlin:

```kotlin
package com.example.myapp

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

/**
 * MainActivity - The entry point of our application
 * This Activity is launched when the app starts
 */
class MainActivity : AppCompatActivity() {
    
    /**
     * onCreate() is called when the Activity is first created
     * This is where we set up our initial UI and initialize components
     * 
     * @param savedInstanceState Bundle containing the Activity's previously saved state
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Set the content view to our layout resource
        // R.layout.activity_main refers to res/layout/activity_main.xml
        setContentView(R.layout.activity_main)
        
        // Additional initialization code would go here
        // For example: finding views, setting up listeners, etc.
    }
}
```

**What This Code Does:**
- Extends `AppCompatActivity`, which provides backward compatibility features
- Overrides `onCreate()`, the first lifecycle method called when the Activity starts
- Calls `setContentView()` to inflate and display the user interface from an XML layout file

**Sample Output:**
When this Activity runs, it displays whatever UI elements are defined in `activity_main.xml`. This could be a simple screen with text, buttons, or other interface elements.

---

## Understanding the Activity Lifecycle

The Activity lifecycle is one of the most important concepts in Android development. Android can destroy and recreate Activities at any time to manage memory, so understanding this lifecycle is crucial.

### The Lifecycle States

```
┌─────────────────┐
│  Activity       │
│  Launched       │
└────────┬────────┘
         │
         ▼
   ┌──────────┐
   │onCreate()│ ◄──────────────┐
   └────┬─────┘                │
        │                      │
        ▼                      │
   ┌──────────┐           ┌────────────┐
   │onStart() │           │onRestart() │
   └────┬─────┘           └─────▲──────┘
        │                       │
        ▼                       │
   ┌───────────┐                │
   │onResume() │                │
   └────┬──────┘                │
        │                       │
        ▼                       │
   ┌──────────────┐             │
   │   Activity   │             │
   │   Running    │             │
   └──────┬───────┘             │
          │                     │
          ▼                     │
   ┌───────────┐                │
   │onPause()  │                │
   └────┬──────┘                │
        │                       │
        ├─────►(Back Button)────┤
        │                       │
        ▼                       │
   ┌──────────┐                 │
   │onStop()  │─────────────────┘
   └────┬─────┘
        │
        ▼
   ┌────────────┐
   │onDestroy() │
   └────┬───────┘
        │
        ▼
   ┌──────────────┐
   │   Activity   │
   │  Shut Down   │
   └──────────────┘
```

### Implementing Lifecycle Methods

Here's a complete Activity demonstrating all the main lifecycle methods:

```kotlin
package com.example.lifecycledemo

import android.os.Bundle
import android.util.Log
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

/**
 * LifecycleDemo - An Activity that demonstrates the Android lifecycle
 * This Activity logs each lifecycle method call to help understand the flow
 */
class LifecycleDemo : AppCompatActivity() {
    
    // TAG for logging - helps identify our log messages
    private val TAG = "LifecycleDemo"
    
    // TextView to display lifecycle events to the user
    private lateinit var statusTextView: TextView
    private val lifecycleEvents = mutableListOf<String>()
    
    /**
     * Called when the Activity is first created
     * This is where you should do all of your normal static setup
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_lifecycle_demo)
        
        statusTextView = findViewById(R.id.statusTextView)
        
        // Log the lifecycle event
        logLifecycleEvent("onCreate()")
        
        // Check if we're recreating from a saved state
        if (savedInstanceState != null) {
            val savedMessage = savedInstanceState.getString("saved_message", "")
            logLifecycleEvent("Restored: $savedMessage")
        }
    }
    
    /**
     * Called when the Activity is becoming visible to the user
     * This is a good place to begin animations or other visual setup
     */
    override fun onStart() {
        super.onStart()
        logLifecycleEvent("onStart()")
    }
    
    /**
     * Called when the Activity will start interacting with the user
     * This is where you typically start animations, open exclusive-access devices
     */
    override fun onResume() {
        super.onResume()
        logLifecycleEvent("onResume() - Activity is now in foreground")
    }
    
    /**
     * Called when the system is about to start resuming another Activity
     * Use this to pause ongoing actions like animations or music playback
     */
    override fun onPause() {
        super.onPause()
        logLifecycleEvent("onPause() - Another Activity is taking focus")
        
        // IMPORTANT: This is where you should commit any changes to persistent data
        // The system may kill your app after this method returns
    }
    
    /**
     * Called when the Activity is no longer visible to the user
     * This can happen because it's being destroyed, or because another Activity
     * has resumed and is covering this one
     */
    override fun onStop() {
        super.onStop()
        logLifecycleEvent("onStop() - Activity no longer visible")
    }
    
    /**
     * Called after onStop() when the Activity is coming back to interact with user
     * This is followed by onStart()
     */
    override fun onRestart() {
        super.onRestart()
        logLifecycleEvent("onRestart() - Activity restarting")
    }
    
    /**
     * Called before the Activity is destroyed
     * This is the final call you will receive
     */
    override fun onDestroy() {
        super.onDestroy()
        logLifecycleEvent("onDestroy() - Activity being destroyed")
    }
    
    /**
     * Called to retrieve per-instance state from an Activity before being killed
     * This is called before onStop() and is used to save state during configuration changes
     * like screen rotation
     */
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        
        // Save the current state
        outState.putString("saved_message", "Activity was recreated after rotation!")
        logLifecycleEvent("onSaveInstanceState() - Saving state")
    }
    
    /**
     * Helper method to log and display lifecycle events
     */
    private fun logLifecycleEvent(message: String) {
        Log.d(TAG, message)
        lifecycleEvents.add(message)
        statusTextView.text = lifecycleEvents.joinToString("\n")
    }
}
```

**Sample Output (in Logcat and on screen):**
```
onCreate()
onStart()
onResume() - Activity is now in foreground
[User rotates device]
onPause() - Another Activity is taking focus
onStop() - Activity no longer visible
onSaveInstanceState() - Saving state
onDestroy() - Activity being destroyed
onCreate()
Restored: Activity was recreated after rotation!
onStart()
onResume() - Activity is now in foreground
```

### Critical Points About the Lifecycle

1. **Configuration Changes**: When the device configuration changes (such as screen rotation), the Activity is destroyed and recreated
2. **Saving State**: Use `onSaveInstanceState()` to save important data that should survive configuration changes
3. **Persistent Data**: Use `onPause()` to commit any data that needs to be written to disk, as the system may kill your app after this point
4. **Resuming**: When an Activity comes back to the foreground, it goes through `onRestart()`, `onStart()`, and `onResume()`

---

## Layouts and UI Design

Android devices come in countless screen sizes and resolutions. Layouts help us design interfaces that work across all these devices without hardcoding pixel positions.

### Understanding Constraint Layout

ConstraintLayout is the default and most flexible layout in modern Android development. It allows you to create complex layouts with a flat view hierarchy, which improves performance.

### Mobile Device Screen Representation

```
┌─────────────────────────────┐
│   Status Bar                │
├─────────────────────────────┤
│   ┌───────────────────┐     │
│   │   TextView        │     │  ← Constrained to top and horizontal center
│   │   "Welcome"       │     │
│   └───────────────────┘     │
│                             │
│   ┌───────────────────┐     │
│   │   EditText        │     │  ← Constrained below TextView
│   │   [Enter name]    │     │     and to left/right margins
│   └───────────────────┘     │
│                             │
│         ┌─────────┐         │
│         │ Button  │         │  ← Constrained to bottom
│         │  "Next" │         │     and horizontal center
│         └─────────┘         │
│                             │
├─────────────────────────────┤
│   Navigation Bar            │
└─────────────────────────────┘
```

### Creating a Constraint Layout

Here's a complete Activity that programmatically creates a Constraint Layout:

```kotlin
package com.example.layoutdemo

import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.constraintlayout.widget.ConstraintLayout
import androidx.constraintlayout.widget.ConstraintSet

/**
 * ConstraintLayoutDemo - Demonstrates creating layouts programmatically
 * Typically layouts are defined in XML, but this shows the concepts more clearly
 */
class ConstraintLayoutDemo : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Create the main ConstraintLayout
        val constraintLayout = ConstraintLayout(this).apply {
            id = ConstraintLayout.generateViewId()
            layoutParams = ConstraintLayout.LayoutParams(
                ConstraintLayout.LayoutParams.MATCH_PARENT,
                ConstraintLayout.LayoutParams.MATCH_PARENT
            )
        }
        
        // Create a TextView for the title
        val titleText = TextView(this).apply {
            id = ConstraintLayout.generateViewId()
            text = "Welcome to Android"
            textSize = 24f
            tag = "title"  // Tag helps identify views
        }
        
        // Create an EditText for user input
        val nameInput = EditText(this).apply {
            id = ConstraintLayout.generateViewId()
            hint = "Enter your name"
            tag = "nameInput"
        }
        
        // Create a Button
        val submitButton = Button(this).apply {
            id = ConstraintLayout.generateViewId()
            text = "Submit"
            tag = "submitButton"
            
            // Set click listener
            setOnClickListener {
                val name = nameInput.text.toString()
                if (name.isNotEmpty()) {
                    Toast.makeText(
                        this@ConstraintLayoutDemo,
                        "Hello, $name!",
                        Toast.LENGTH_SHORT
                    ).show()
                } else {
                    Toast.makeText(
                        this@ConstraintLayoutDemo,
                        "Please enter your name",
                        Toast.LENGTH_SHORT
                    ).show()
                }
            }
        }
        
        // Add all views to the layout
        constraintLayout.addView(titleText)
        constraintLayout.addView(nameInput)
        constraintLayout.addView(submitButton)
        
        // Define the constraints
        val constraintSet = ConstraintSet()
        constraintSet.clone(constraintLayout)
        
        // Title constraints - centered horizontally, 100dp from top
        constraintSet.connect(
            titleText.id,
            ConstraintSet.TOP,
            ConstraintSet.PARENT_ID,
            ConstraintSet.TOP,
            100
        )
        constraintSet.connect(
            titleText.id,
            ConstraintSet.START,
            ConstraintSet.PARENT_ID,
            ConstraintSet.START
        )
        constraintSet.connect(
            titleText.id,
            ConstraintSet.END,
            ConstraintSet.PARENT_ID,
            ConstraintSet.END
        )
        
        // Input constraints - below title, with margins on left and right
        constraintSet.connect(
            nameInput.id,
            ConstraintSet.TOP,
            titleText.id,
            ConstraintSet.BOTTOM,
            50
        )
        constraintSet.connect(
            nameInput.id,
            ConstraintSet.START,
            ConstraintSet.PARENT_ID,
            ConstraintSet.START,
            32
        )
        constraintSet.connect(
            nameInput.id,
            ConstraintSet.END,
            ConstraintSet.PARENT_ID,
            ConstraintSet.END,
            32
        )
        
        // Button constraints - centered horizontally, below input
        constraintSet.connect(
            submitButton.id,
            ConstraintSet.TOP,
            nameInput.id,
            ConstraintSet.BOTTOM,
            50
        )
        constraintSet.connect(
            submitButton.id,
            ConstraintSet.START,
            ConstraintSet.PARENT_ID,
            ConstraintSet.START
        )
        constraintSet.connect(
            submitButton.id,
            ConstraintSet.END,
            ConstraintSet.PARENT_ID,
            ConstraintSet.END
        )
        
        // Apply the constraints
        constraintSet.applyTo(constraintLayout)
        
        // Set the layout as our content view
        setContentView(constraintLayout)
    }
}
```

**What This Code Does:**
- Creates a ConstraintLayout programmatically (normally you'd use XML)
- Adds three views: TextView, EditText, and Button
- Uses ConstraintSet to define relationships between views
- Connects each view to the parent or to other views using constraints
- Shows a Toast message when the button is clicked

**Sample Output:**
When run, you'll see a screen with:
- "Welcome to Android" text centered near the top
- An input field for entering a name
- A "Submit" button below the input
- When you tap Submit with a name entered, you'll see "Hello, [name]!" in a Toast

### Other Layout Types

**LinearLayout** - Arranges children in a single row or column:
```kotlin
// Children are stacked vertically or horizontally
// Simple but less flexible than ConstraintLayout
val linearLayout = LinearLayout(this).apply {
    orientation = LinearLayout.VERTICAL  // or HORIZONTAL
}
```

**RelativeLayout** - Positions children relative to each other:
```kotlin
// Legacy layout, mostly replaced by ConstraintLayout
// Useful for understanding relationships between views
```

**TableLayout** - Arranges children in rows and columns:
```kotlin
// Good for grid-based layouts like calculators
// Each TableRow contains columns of views
```

---

## Measurement Units for Different Screens

Android devices have vastly different screen sizes and pixel densities. A button that's 100 pixels wide might look fine on one device but tiny on another with higher pixel density.

### Understanding Density-Independent Pixels (dp)

**Density-independent pixels (dp)** provide a consistent visual size across different screen densities. The Android system automatically converts dp to the appropriate number of physical pixels based on the device's screen density.

### Understanding Scalable Pixels (sp)

**Scalable pixels (sp)** are like dp but also scale based on the user's font size preference in system settings. Always use sp for text sizes to respect user accessibility settings.

### Complete Example with Proper Units

```kotlin
package com.example.measurementdemo

import android.os.Bundle
import android.util.TypedValue
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.constraintlayout.widget.ConstraintLayout
import androidx.constraintlayout.widget.ConstraintSet

/**
 * MeasurementUnitsDemo - Demonstrates the proper use of dp and sp
 * This Activity creates UI elements with appropriate measurement units
 */
class MeasurementUnitsDemo : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Create the constraint layout
        val constraintLayout = ConstraintLayout(this).apply {
            id = ConstraintLayout.generateViewId()
            layoutParams = ConstraintLayout.LayoutParams(
                ConstraintLayout.LayoutParams.MATCH_PARENT,
                ConstraintLayout.LayoutParams.MATCH_PARENT
            )
            // Set padding using dp
            val paddingDp = dpToPx(16)
            setPadding(paddingDp, paddingDp, paddingDp, paddingDp)
        }
        
        // Create a heading TextView - use SP for text size
        val headingText = TextView(this).apply {
            id = ConstraintLayout.generateViewId()
            text = "Measurement Units Demo"
            // Use SP for text size - this will scale with user's font settings
            setTextSize(TypedValue.COMPLEX_UNIT_SP, 24f)
            tag = "heading"
        }
        
        // Create a body text TextView - smaller SP value
        val bodyText = TextView(this).apply {
            id = ConstraintLayout.generateViewId()
            text = "This text uses 16sp, which will scale with user preferences.\n\n" +
                   "The padding and margins use dp to ensure consistent spacing " +
                   "across different screen densities."
            // Body text typically uses 14-16sp
            setTextSize(TypedValue.COMPLEX_UNIT_SP, 16f)
            // Set width in dp for consistent layout
            val widthDp = dpToPx(280)
            layoutParams = ConstraintLayout.LayoutParams(widthDp, 
                ConstraintLayout.LayoutParams.WRAP_CONTENT)
            tag = "body"
        }
        
        // Create a button with dp-based dimensions
        val actionButton = Button(this).apply {
            id = ConstraintLayout.generateViewId()
            text = "Button (48dp min height)"
            // Text size in SP
            setTextSize(TypedValue.COMPLEX_UNIT_SP, 14f)
            // Minimum touch target size should be 48dp (accessibility guideline)
            val minHeightDp = dpToPx(48)
            minHeight = minHeightDp
            // Width in dp
            val widthDp = dpToPx(200)
            layoutParams = ConstraintLayout.LayoutParams(widthDp, 
                ConstraintLayout.LayoutParams.WRAP_CONTENT)
            tag = "button"
        }
        
        // Add views to layout
        constraintLayout.addView(headingText)
        constraintLayout.addView(bodyText)
        constraintLayout.addView(actionButton)
        
        // Set up constraints
        val constraintSet = ConstraintSet()
        constraintSet.clone(constraintLayout)
        
        // Heading constraints - use dp for margins
        constraintSet.connect(
            headingText.id,
            ConstraintSet.TOP,
            ConstraintSet.PARENT_ID,
            ConstraintSet.TOP,
            dpToPx(32)  // 32dp margin
        )
        constraintSet.connect(
            headingText.id,
            ConstraintSet.START,
            ConstraintSet.PARENT_ID,
            ConstraintSet.START
        )
        constraintSet.connect(
            headingText.id,
            ConstraintSet.END,
            ConstraintSet.PARENT_ID,
            ConstraintSet.END
        )
        
        // Body text constraints
        constraintSet.connect(
            bodyText.id,
            ConstraintSet.TOP,
            headingText.id,
            ConstraintSet.BOTTOM,
            dpToPx(24)  // 24dp margin
        )
        constraintSet.connect(
            bodyText.id,
            ConstraintSet.START,
            ConstraintSet.PARENT_ID,
            ConstraintSet.START
        )
        constraintSet.connect(
            bodyText.id,
            ConstraintSet.END,
            ConstraintSet.PARENT_ID,
            ConstraintSet.END
        )
        
        // Button constraints
        constraintSet.connect(
            actionButton.id,
            ConstraintSet.TOP,
            bodyText.id,
            ConstraintSet.BOTTOM,
            dpToPx(32)  // 32dp margin
        )
        constraintSet.connect(
            actionButton.id,
            ConstraintSet.START,
            ConstraintSet.PARENT_ID,
            ConstraintSet.START
        )
        constraintSet.connect(
            actionButton.id,
            ConstraintSet.END,
            ConstraintSet.PARENT_ID,
            ConstraintSet.END
        )
        
        constraintSet.applyTo(constraintLayout)
        setContentView(constraintLayout)
    }
    
    /**
     * Converts density-independent pixels (dp) to actual pixels (px)
     * This ensures consistent sizing across different screen densities
     * 
     * @param dp The value in density-independent pixels
     * @return The value in actual pixels for this device
     */
    private fun dpToPx(dp: Int): Int {
        return TypedValue.applyDimension(
            TypedValue.COMPLEX_UNIT_DIP,
            dp.toFloat(),
            resources.displayMetrics
        ).toInt()
    }
}
```

**What This Code Does:**
- Demonstrates proper use of sp for text sizes
- Uses dp for all spacing (margins, padding, dimensions)
- Includes a helper method to convert dp to pixels
- Follows Android accessibility guidelines (48dp minimum touch target)

**Sample Output:**
The screen displays consistently across devices:
- Large heading at 24sp
- Body text at 16sp (both scale with user's font size settings)
- Consistent spacing regardless of screen density
- Button with proper minimum touch target size

### When to Use Each Unit

| Unit | Usage | Example |
|------|-------|---------|
| **sp** | Text sizes | `textSize = 16sp` |
| **dp** | Everything else (margins, padding, dimensions) | `margin = 16dp`, `width = 48dp` |
| **px** | Almost never (unless you specifically need pixels) | Very rare |

---

## Starting and Switching Between Activities

Android apps typically consist of multiple Activities. Users navigate between them using Intents, which are messaging objects used to request an action from another app component.

### Understanding Intents

An Intent is essentially a message that describes:
- **What** action should be performed
- **What** data should be operated on
- **Which** component should handle the action

### Simple Activity Navigation

Here's a complete example with two Activities:

```kotlin
package com.example.navigation

import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

/**
 * MainActivity - The first Activity in our app
 * Demonstrates starting another Activity using an Intent
 */
class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Find the button and text view from the layout
        val startButton: Button = findViewById(R.id.startButton)
        val infoText: TextView = findViewById(R.id.infoText)
        
        // Set informational text
        infoText.text = "This is the Main Activity.\nTap the button to start the Second Activity."
        
        // Set up button click listener
        startButton.setOnClickListener {
            // Create an Intent to start SecondActivity
            // The Intent constructor takes:
            // 1. Context (this Activity)
            // 2. The class of the Activity to start
            val intent = Intent(this, SecondActivity::class.java)
            
            // Start the new Activity
            startActivity(intent)
            
            // Optional: Add a transition animation
            // overridePendingTransition(android.R.anim.fade_in, android.R.anim.fade_out)
        }
    }
}

/**
 * SecondActivity - The destination Activity
 * This Activity is started by MainActivity
 */
class SecondActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_second)
        
        val infoText: TextView = findViewById(R.id.infoText)
        val backButton: Button = findViewById(R.id.backButton)
        
        // Set informational text
        infoText.text = "Welcome to the Second Activity!\n\n" +
                       "You successfully navigated from MainActivity.\n\n" +
                       "Tap the back button or use the system back button to return."
        
        // Set up back button
        backButton.setOnClickListener {
            // Finish this Activity and return to the previous one
            finish()
        }
    }
    
    /**
     * Called when the user presses the system back button
     */
    override fun onBackPressed() {
        super.onBackPressed()
        // You could add custom logic here, like showing a confirmation dialog
    }
}
```

**What This Code Does:**
- Creates an Intent specifying which Activity to start
- Uses `startActivity()` to launch the new Activity
- The second Activity has a button to return to the first
- `finish()` closes the current Activity and returns to the previous one

**Sample Output:**

```
Screen 1 (MainActivity):
┌─────────────────────────────┐
│   Status Bar                │
├─────────────────────────────┤
│                             │
│  This is the Main Activity. │
│  Tap the button to start    │
│  the Second Activity.       │
│                             │
│      ┌──────────────┐       │
│      │ Start Second │       │
│      │   Activity   │       │
│      └──────────────┘       │
│                             │
└─────────────────────────────┘

[User taps button]

Screen 2 (SecondActivity):
┌─────────────────────────────┐
│   Status Bar                │
├─────────────────────────────┤
│                             │
│  Welcome to the Second      │
│  Activity!                  │
│                             │
│  You successfully navigated │
│  from MainActivity.         │
│                             │
│      ┌──────────────┐       │
│      │  Go Back     │       │
│      └──────────────┘       │
│                             │
└─────────────────────────────┘
```

---

## Passing Data Between Activities

Most of the time, you'll need to pass data from one Activity to another. Android provides the Intent extras system for this purpose.

### Passing Basic Data Types

Here's a complete example showing how to pass different types of data:

```kotlin
package com.example.datapassing

import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

/**
 * SenderActivity - Sends data to another Activity
 * Demonstrates passing various data types using Intent extras
 */
class SenderActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_sender)
        
        val nameInput: EditText = findViewById(R.id.nameInput)
        val ageInput: EditText = findViewById(R.id.ageInput)
        val sendButton: Button = findViewById(R.id.sendButton)
        
        sendButton.setOnClickListener {
            // Get values from input fields
            val name = nameInput.text.toString()
            val age = ageInput.text.toString().toIntOrNull() ?: 0
            
            // Create Intent for the receiving Activity
            val intent = Intent(this, ReceiverActivity::class.java)
            
            // Add data as extras using key-value pairs
            // putExtra() is overloaded to accept many data types
            
            // String extra
            intent.putExtra("USER_NAME", name)
            
            // Integer extra
            intent.putExtra("USER_AGE", age)
            
            // Boolean extra
            intent.putExtra("IS_STUDENT", true)
            
            // Double extra
            intent.putExtra("SCORE", 95.5)
            
            // You can also use constants for keys (recommended)
            intent.putExtra(KEY_TIMESTAMP, System.currentTimeMillis())
            
            // Start the Activity with the data
            startActivity(intent)
        }
    }
    
    // It's good practice to define keys as constants
    companion object {
        const val KEY_TIMESTAMP = "timestamp"
    }
}

/**
 * ReceiverActivity - Receives and displays data from another Activity
 * Demonstrates retrieving various data types from Intent extras
 */
class ReceiverActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_receiver)
        
        val displayText: TextView = findViewById(R.id.displayText)
        
        // Get the Intent that started this Activity
        val receivedIntent = intent
        
        // Retrieve data using the same keys
        // Each get method has a default value parameter for when the key doesn't exist
        
        val userName = receivedIntent.getStringExtra("USER_NAME") ?: "Unknown"
        val userAge = receivedIntent.getIntExtra("USER_AGE", 0)
        val isStudent = receivedIntent.getBooleanExtra("IS_STUDENT", false)
        val score = receivedIntent.getDoubleExtra("SCORE", 0.0)
        val timestamp = receivedIntent.getLongExtra("timestamp", 0L)
        
        // Format and display the received data
        val message = buildString {
            appendLine("Received Data:")
            appendLine("─────────────────")
            appendLine("Name: $userName")
            appendLine("Age: $userAge")
            appendLine("Student: ${if (isStudent) "Yes" else "No"}")
            appendLine("Score: $score")
            appendLine("Timestamp: $timestamp")
            appendLine()
            appendLine("All data successfully passed!")
        }
        
        displayText.text = message
    }
}
```

**What This Code Does:**
- Uses `putExtra()` to add data to the Intent
- Each piece of data needs a unique key (String identifier)
- Uses type-specific `get` methods to retrieve data
- Provides default values in case keys don't exist

**Sample Output:**
```
Sender Screen:
┌─────────────────────────────┐
│   Enter Your Information    │
│                             │
│   Name: [Alice Smith]       │
│   Age:  [23]                │
│                             │
│      ┌──────────────┐       │
│      │    Send      │       │
│      └──────────────┘       │
└─────────────────────────────┘

[User taps Send]

Receiver Screen:
┌─────────────────────────────┐
│   Received Data:            │
│   ─────────────────         │
│   Name: Alice Smith         │
│   Age: 23                   │
│   Student: Yes              │
│   Score: 95.5               │
│   Timestamp: 1701234567890  │
│                             │
│   All data successfully     │
│   passed!                   │
└─────────────────────────────┘
```

### Passing Complex Objects

For complex objects, you need to make them Serializable or Parcelable:

```kotlin
import java.io.Serializable

/**
 * User - A data class representing a user
 * Implements Serializable so it can be passed via Intent extras
 */
data class User(
    val name: String,
    val email: String,
    val age: Int,
    val isActive: Boolean
) : Serializable  // Implementing Serializable allows this object to be passed in Intents

// Sending a complex object
val user = User("John Doe", "john@example.com", 30, true)
val intent = Intent(this, DetailActivity::class.java)
intent.putExtra("USER_OBJECT", user)
startActivity(intent)

// Receiving a complex object
val user = intent.getSerializableExtra("USER_OBJECT") as? User
if (user != null) {
    // Use the user object
    println("Name: ${user.name}, Email: ${user.email}")
}
```

---

## Receiving Data Back from Activities

Sometimes you need to start an Activity and get a result back from it. For example, starting a camera Activity to take a photo and getting the photo back, or starting a contact picker and getting the selected contact.

### Using Activity Result Launcher

The modern way to get results from Activities uses the Activity Result API:

```kotlin
package com.example.activityresult

import android.app.Activity
import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import android.widget.Toast
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity

/**
 * CallerActivity - Launches another Activity and receives a result back
 * Demonstrates the modern Activity Result API
 */
class CallerActivity : AppCompatActivity() {
    
    private lateinit var resultText: TextView
    
    /**
     * Activity Result Launcher - registered before onCreate()
     * This replaces the deprecated startActivityForResult() method
     */
    private val activityResultLauncher = registerForActivityResult(
        ActivityResultContracts.StartActivityForResult()
    ) { result ->
        // This callback is invoked when the launched Activity returns
        
        // Check if the result is successful
        if (result.resultCode == Activity.RESULT_OK) {
            // Get the data from the returned Intent
            val data: Intent? = result.data
            
            // Extract the values that were returned
            val selectedColor = data?.getStringExtra("SELECTED_COLOR") ?: "None"
            val selectedNumber = data?.getIntExtra("SELECTED_NUMBER", 0) ?: 0
            
            // Display the results
            val message = "Received from Activity:\n" +
                         "Color: $selectedColor\n" +
                         "Number: $selectedNumber"
            resultText.text = message
            
            Toast.makeText(this, "Result received!", Toast.LENGTH_SHORT).show()
        } else if (result.resultCode == Activity.RESULT_CANCELED) {
            resultText.text = "User canceled the operation"
            Toast.makeText(this, "Operation canceled", Toast.LENGTH_SHORT).show()
        }
    }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_caller)
        
        val launchButton: Button = findViewById(R.id.launchButton)
        resultText = findViewById(R.id.resultText)
        
        resultText.text = "No result yet.\nTap the button to launch the Activity."
        
        launchButton.setOnClickListener {
            // Create Intent to launch the Activity that will return a result
            val intent = Intent(this, ReturnActivity::class.java)
            
            // You can also pass data to the Activity
            intent.putExtra("INITIAL_MESSAGE", "Please select your preferences")
            
            // Launch the Activity using the result launcher
            activityResultLauncher.launch(intent)
        }
    }
}

/**
 * ReturnActivity - Returns data back to the calling Activity
 * Demonstrates setting a result and finishing the Activity
 */
class ReturnActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_return)
        
        val instructionText: TextView = findViewById(R.id.instructionText)
        val returnBlueButton: Button = findViewById(R.id.returnBlueButton)
        val returnRedButton: Button = findViewById(R.id.returnRedButton)
        val cancelButton: Button = findViewById(R.id.cancelButton)
        
        // Get any data that was passed to this Activity
        val initialMessage = intent.getStringExtra("INITIAL_MESSAGE") ?: "Make a selection"
        instructionText.text = initialMessage
        
        // Button to return "Blue" as the result
        returnBlueButton.setOnClickListener {
            returnColorResult("Blue", 1)
        }
        
        // Button to return "Red" as the result
        returnRedButton.setOnClickListener {
            returnColorResult("Red", 2)
        }
        
        // Button to cancel and return no result
        cancelButton.setOnClickListener {
            // Set result as CANCELED
            setResult(Activity.RESULT_CANCELED)
            finish()
        }
    }
    
    /**
     * Helper method to return a color result
     * Creates an Intent with data and finishes the Activity
     */
    private fun returnColorResult(color: String, number: Int) {
        // Create an Intent to hold the result data
        val resultIntent = Intent()
        
        // Add the data to return
        resultIntent.putExtra("SELECTED_COLOR", color)
        resultIntent.putExtra("SELECTED_NUMBER", number)
        
        // Set the result as OK with the data
        setResult(Activity.RESULT_OK, resultIntent)
        
        // Finish this Activity and return to the caller
        finish()
    }
    
    /**
     * Handle the system back button
     */
    override fun onBackPressed() {
        // Treat back button as cancel
        setResult(Activity.RESULT_CANCELED)
        super.onBackPressed()
    }
}
```

**What This Code Does:**
1. **CallerActivity** registers an Activity Result Launcher before onCreate()
2. When the button is clicked, it launches ReturnActivity using the launcher
3. **ReturnActivity** displays options and returns data based on user selection
4. When ReturnActivity calls `setResult()` and `finish()`, control returns to CallerActivity
5. The callback in CallerActivity receives the result and processes the data

**Sample Output:**

```
Screen 1 (CallerActivity - initial):
┌─────────────────────────────┐
│   Activity Result Demo      │
│                             │
│   No result yet.            │
│   Tap the button to launch  │
│   the Activity.             │
│                             │
│      ┌──────────────┐       │
│      │Launch Activity│      │
│      └──────────────┘       │
└─────────────────────────────┘

[User taps Launch Activity]

Screen 2 (ReturnActivity):
┌─────────────────────────────┐
│   Make Your Selection       │
│                             │
│   Please select your        │
│   preferences               │
│                             │
│      ┌──────────────┐       │
│      │  Blue (1)    │       │
│      └──────────────┘       │
│      ┌──────────────┐       │
│      │  Red (2)     │       │
│      └──────────────┘       │
│      ┌──────────────┐       │
│      │   Cancel     │       │
│      └──────────────┘       │
└─────────────────────────────┘

[User taps Blue]

Screen 1 (CallerActivity - after result):
┌─────────────────────────────┐
│   Activity Result Demo      │
│                             │
│   Received from Activity:   │
│   Color: Blue               │
│   Number: 1                 │
│                             │
│   [Toast: "Result received!"]│
│                             │
│      ┌──────────────┐       │
│      │Launch Activity│      │
│      └──────────────┘       │
└─────────────────────────────┘
```

### Key Points About Activity Results

1. **Register Before onCreate**: The Activity Result Launcher must be registered before the Activity is created
2. **Result Codes**: Use `RESULT_OK`, `RESULT_CANCELED`, or custom codes to indicate the result status
3. **Data Intent**: Create a new Intent to hold result data (don't reuse the incoming Intent)
4. **Finish**: Always call `finish()` after `setResult()` to return control to the caller
5. **Handle Back Button**: Override `onBackPressed()` to set an appropriate result when the user presses back

### Complete Flow Example

```kotlin
/**
 * Complete example showing the entire flow of Activity communication
 */
package com.example.completeflow

import android.app.Activity
import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity

class FirstActivity : AppCompatActivity() {
    
    private lateinit var statusText: TextView
    
    // Register the result launcher
    private val activityLauncher = registerForActivityResult(
        ActivityResultContracts.StartActivityForResult()
    ) { result ->
        when (result.resultCode) {
            Activity.RESULT_OK -> {
                val returnedValue = result.data?.getStringExtra("RESULT") ?: "No data"
                statusText.text = "Success! Received: $returnedValue"
            }
            Activity.RESULT_CANCELED -> {
                statusText.text = "Operation was canceled"
            }
            else -> {
                statusText.text = "Unknown result code: ${result.resultCode}"
            }
        }
    }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_first)
        
        statusText = findViewById(R.id.statusText)
        val startButton: Button = findViewById(R.id.startButton)
        
        startButton.setOnClickListener {
            val intent = Intent(this, SecondActivity::class.java)
            intent.putExtra("QUESTION", "What is your favorite programming language?")
            activityLauncher.launch(intent)
        }
    }
}

class SecondActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_second)
        
        val questionText: TextView = findViewById(R.id.questionText)
        val answerInput: EditText = findViewById(R.id.answerInput)
        val submitButton: Button = findViewById(R.id.submitButton)
        
        // Display the question that was passed in
        val question = intent.getStringExtra("QUESTION") ?: "Enter your answer:"
        questionText.text = question
        
        submitButton.setOnClickListener {
            val answer = answerInput.text.toString()
            
            if (answer.isNotEmpty()) {
                // Create result intent with the answer
                val resultIntent = Intent()
                resultIntent.putExtra("RESULT", answer)
                setResult(Activity.RESULT_OK, resultIntent)
                finish()
            } else {
                answerInput.error = "Please enter an answer"
            }
        }
    }
}
```

**Sample Output:**
```
First Activity shows:
"Tap to ask a question"

[User taps button]

Second Activity shows:
"What is your favorite programming language?"
[Text input field]
[Submit button]

[User types "Kotlin" and taps Submit]

First Activity shows:
"Success! Received: Kotlin"
```

---

## Summary

In this lecture, we've covered the essential concepts of Android Activities:

**Activities** are the building blocks of Android apps, representing single screens that users interact with. Each Activity has a specific purpose and can start other Activities to accomplish more complex tasks.

**The Activity Lifecycle** is crucial for managing resources and state. Understanding when `onCreate()`, `onStart()`, `onResume()`, `onPause()`, `onStop()`, and `onDestroy()` are called helps you save data appropriately and respond to configuration changes like screen rotation.

**Layouts** organize UI elements across different screen sizes. ConstraintLayout is the most flexible and efficient option, allowing you to define relationships between views without deep nesting. Other layouts like LinearLayout, RelativeLayout, and TableLayout serve specific purposes.

**Measurement Units** ensure consistency across devices. Use **dp** for dimensions, spacing, and margins to maintain visual consistency across different screen densities. Use **sp** for text sizes to respect user accessibility preferences.

**Intents** are the messaging system for starting Activities and passing data. Create an Intent with the target Activity, optionally add data using `putExtra()`, and call `startActivity()` to navigate.

**Activity Results** allow bidirectional communication between Activities. Use the Activity Result API to launch an Activity and receive data back. The calling Activity registers a result launcher, and the launched Activity returns data using `setResult()` before calling `finish()`.

These concepts form the foundation of Android navigation and interaction. As you build more complex apps, you'll combine these techniques to create seamless user experiences across multiple screens.