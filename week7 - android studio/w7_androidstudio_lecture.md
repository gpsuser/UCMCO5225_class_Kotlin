# Android Development with Android Studio

## Table of Contents

1. [Learning Outcomes](#learning-outcomes)
2. [Introduction to Android Studio](#introduction-to-android-studio)
3. [Android Version Management](#android-version-management)
4. [Virtual Devices and Testing](#virtual-devices-and-testing)
5. [Android Project Structure](#android-project-structure)
6. [Android UI and Activities](#android-ui-and-activities)
7. [View Binding](#view-binding)
8. [String Resources and Internationalization](#string-resources-and-internationalization)
9. [Testing Applications](#testing-applications)
10. [Interfaces in Android: OnClickListener](#interfaces-in-android-onclicklistener)
11. [Summary](#summary)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Understand the role and features of Android Studio as an IDE for Android development
- Navigate and utilize the Android project structure effectively
- Implement View Binding to access UI elements safely
- Manage string resources for internationalization
- Create and configure virtual devices for testing
- Understand and implement interfaces in Android, particularly OnClickListener
- Handle user interactions through multiple approaches
- Set up and run Android applications in the emulator

---

## Introduction to Android Studio

### What is Android Studio?

Android Studio is the official Integrated Development Environment (IDE) for Android application development. It provides a comprehensive suite of tools specifically designed for building Android applications efficiently.

### Key Features

**Similarity to IntelliJ IDEA**

Android Studio is built on IntelliJ IDEA, which means if you're familiar with IntelliJ, you'll find Android Studio's interface and workflow quite familiar. This includes features like intelligent code completion, refactoring tools, and powerful debugging capabilities.

**The Gradle Build System**

One of the most important components of Android Studio is the Gradle build system. But what exactly does Gradle do for us?

Android applications are complex. They involve:
- Multiple configuration options
- Various external libraries
- Different device targets and screen sizes
- Diverse resource files (images, strings, layouts)

The build process transforms your source code and resources into a deployable Android application package (APK). This process includes:

1. **Resource Packaging**: Compiling and packaging resources like images and strings
2. **Code Compilation**: Converting Kotlin (or Java) source code into bytecode
3. **Digital Signing**: Signing the application for security
4. **Optimization**: Optimizing the final package for distribution

Gradle automates all of this, so you don't have to manually manage these complex steps. You simply write your code, and Gradle handles the rest.

### A Simple "Hello World" Example

Let's look at a minimal Android application structure:

```kotlin
// MainActivity.kt
package com.example.helloworld

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

/**
 * MainActivity - The entry point of our Android application
 * This activity extends AppCompatActivity, providing compatibility
 * with older Android versions
 */
class MainActivity : AppCompatActivity() {
    
    /**
     * onCreate() is called when the activity is first created
     * This is where we initialize our activity and set up the UI
     * 
     * @param savedInstanceState Contains data about the previous state
     *                           (null if this is a fresh start)
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        // Always call the superclass implementation first
        super.onCreate(savedInstanceState)
        
        // Set the layout for this activity
        // R.layout.activity_main references the XML layout file
        setContentView(R.layout.activity_main)
    }
}
```

**What's happening here?**

1. We import necessary Android classes
2. Our `MainActivity` class extends `AppCompatActivity`, making it an Android Activity
3. We override the `onCreate()` method, which is called when the activity starts
4. We call `setContentView()` to specify which XML layout file defines our UI

---

## Android Version Management

### The Challenge of Fragmentation

Unlike iOS development, where most users quickly update to the latest version, Android has significant version fragmentation. This creates both challenges and opportunities for developers.

### Understanding API Levels

Each Android version has an associated API level number. For example:
- Android 13 (Tiramisu) = API Level 33
- Android 12 (S) = API Level 31
- Android 11 (R) = API Level 30
- Android 10 (Q) = API Level 29
- Android 9 (Pie) = API Level 28

### Why Version Management Matters

**Supporting Older Versions**

As developers, we typically need to support older Android versions to reach the maximum number of users. However, this comes with trade-offs:

1. **Limited Functionality**: Newer Android features aren't available on older versions
2. **Conditional Code**: Sometimes we need different code paths for different Android versions
3. **Testing Complexity**: More versions to support means more testing scenarios

**Google Play Requirements**

Google Play Store has minimum requirements for new apps. Apps must target API level 33 or above for distribution, though they can still support running on older versions (minimum SDK).

### Example: Version-Specific Code

```kotlin
// Example of handling different Android versions
import android.os.Build
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

/**
 * Demonstration of version-specific code handling
 */
class VersionAwareActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Check Android version before using version-specific features
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
            // Code for Android 13 (API 33) and above
            println("Running on Android 13 or higher")
            // Use newer APIs safely here
        } else {
            // Fallback code for older versions
            println("Running on Android 12 or lower")
            // Use alternative approach for older devices
        }
    }
    
    /**
     * Example method showing conditional feature usage
     */
    private fun handleNotifications() {
        when {
            Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU -> {
                // Android 13+ requires runtime permission for notifications
                println("Request notification permission for Android 13+")
            }
            Build.VERSION.SDK_INT >= Build.VERSION_CODES.O -> {
                // Android 8+ uses notification channels
                println("Set up notification channels")
            }
            else -> {
                // Older approach for legacy devices
                println("Use legacy notification approach")
            }
        }
    }
}
```

**Sample Output:**
```
Running on Android 13 or higher
Request notification permission for Android 13+
```

---

## Virtual Devices and Testing

### Why Virtual Devices?

Testing Android applications requires running them on actual Android devices. However, purchasing multiple physical devices representing different screen sizes, Android versions, and hardware capabilities would be prohibitively expensive. This is where virtual devices become essential.

### Android Virtual Device (AVD) Features

The Android Emulator can simulate:
- Different screen sizes and resolutions
- Various Android versions
- Different hardware features (GPS, cameras, sensors)
- Network conditions
- Battery states

### Resource Considerations

**Important Performance Note:**

Virtual devices and Android Studio are both resource-intensive applications. Here's what you need to know:

1. **RAM Usage**: Both consume significant memory (3-4 GB each is common)
2. **Slow Boot**: Virtual devices can take several minutes to start
3. **Best Practice**: Keep the emulator running once started, rather than closing and restarting it repeatedly

### Setting Up a Virtual Device

```kotlin
// While we don't write code to create virtual devices,
// here's an example of checking if we're running in an emulator
import android.os.Build
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

/**
 * Example activity that detects emulator environment
 */
class EmulatorDetectionActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Check if running on emulator
        val isEmulator = isRunningOnEmulator()
        println("Running on emulator: $isEmulator")
    }
    
    /**
     * Detects if the app is running on an emulator
     * @return true if running on emulator, false otherwise
     */
    private fun isRunningOnEmulator(): Boolean {
        return (Build.FINGERPRINT.startsWith("generic")
                || Build.FINGERPRINT.startsWith("unknown")
                || Build.MODEL.contains("google_sdk")
                || Build.MODEL.contains("Emulator")
                || Build.MODEL.contains("Android SDK built for x86")
                || Build.MANUFACTURER.contains("Genymotion")
                || Build.BRAND.startsWith("generic") && Build.DEVICE.startsWith("generic")
                || "google_sdk" == Build.PRODUCT)
    }
}
```

**Sample Output:**
```
Running on emulator: true
```

### Virtual Device Wireframe Visualization

```
┌─────────────────────────┐
│  ○ 4:24 [Signal] [Bat]  │ ← Status Bar
├─────────────────────────┤
│                         │
│    Your App Content     │
│                         │
│    (Activity Layout)    │
│                         │
│                         │
│                         │
│                         │
│                         │
│                         │
├─────────────────────────┤
│  [◁]  [○]  [▢]         │ ← Navigation Bar
└─────────────────────────┘
      Virtual Device
```

---

## Android Project Structure

### Understanding the File Organization

When you create an Android project, you'll see several important folders and files. Let's explore each one:

### The `manifests` Folder

**AndroidManifest.xml**

This is one of the most important files in your Android project. It contains essential information about your application:

- **Permissions**: What system features your app needs (camera, internet, location, etc.)
- **Theme**: The visual style of your app
- **Initial Activity**: Which screen appears when your app launches
- **App Components**: All activities, services, and broadcast receivers

```xml
<!-- Example AndroidManifest.xml -->
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp">

    <!-- Request internet permission -->
    <uses-permission android:name="android.permission.INTERNET" />
    
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/Theme.MyApp">
        
        <!-- Main Activity - entry point of the app -->
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

### The `java` Folder

**Don't be confused by the name!**

Despite being called "java", this folder contains your Kotlin source code files. It's named this way for historical reasons (Android originally only supported Java).

This folder contains:
- Your Activity classes
- Other Kotlin/Java source files
- Test files (in separate test folders)

### The `res` Folder

The `res` folder contains all your application's resources:

**Key Subfolders:**

- **drawable**: Images and graphics (PNG, JPG, WebP, vector drawables)
- **layout**: XML files defining user interface layouts
- **values**: XML files containing values like strings, colors, dimensions
- **mipmap**: App launcher icons at different resolutions
- **menu**: XML files defining menu structures

### Example Resource Structure

```kotlin
// Accessing resources in Kotlin code
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

/**
 * Activity demonstrating resource access
 */
class ResourceDemoActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // The R class provides access to all resources
        // R.layout references layout files
        // R.drawable references images
        // R.string references string resources
        // R.color references color resources
        
        demonstrateResourceAccess()
    }
    
    /**
     * Shows different ways to access resources
     */
    private fun demonstrateResourceAccess() {
        // Access string resource
        val appName = getString(R.string.app_name)
        println("App name: $appName")
        
        // Access color resource (returns an integer color value)
        val primaryColor = getColor(R.color.primary_color)
        println("Primary color: $primaryColor")
        
        // Access dimension resource
        val buttonHeight = resources.getDimension(R.dimen.button_height)
        println("Button height: $buttonHeight px")
        
        // Access drawable resource ID
        val iconId = R.drawable.ic_launcher
        println("Icon resource ID: $iconId")
    }
}
```

**Sample Output:**
```
App name: My Android App
Primary color: -16776961
Button height: 48.0 px
Icon resource ID: 2131165299
```

### Project Structure Visualization

```
MyAndroidApp/
├── manifests/
│   └── AndroidManifest.xml          (App configuration)
├── java/
│   └── com.example.myapp/
│       ├── MainActivity.kt           (Main source code)
│       └── OtherActivity.kt
└── res/
    ├── drawable/                     (Images)
    │   ├── icon.png
    │   └── background.xml
    ├── layout/                       (UI layouts)
    │   ├── activity_main.xml
    │   └── activity_other.xml
    ├── values/                       (Resource values)
    │   ├── strings.xml
    │   ├── colors.xml
    │   └── dimens.xml
    └── mipmap/                       (App icons)
        ├── ic_launcher.png
        └── ic_launcher_round.png
```

---

## Android UI and Activities

### What is an Activity?

An Activity represents a single screen in your Android application. Most apps have multiple activities, each handling a different screen or task.

For example, an email app might have:
- An Activity for the inbox list
- An Activity for composing a new email
- An Activity for viewing email details
- An Activity for settings

### Activity and XML Relationship

Activities have two main components:

1. **Kotlin Code File**: Contains the logic and behavior (e.g., `MainActivity.kt`)
2. **XML Layout File**: Defines the user interface (e.g., `activity_main.xml`)

**Important Naming Convention:**

Notice the naming pattern:
- `MainActivity.kt` (class name)
- `activity_main.xml` (layout file)

The relationship is:
- **Class name**: PascalCase (MainActivity)
- **Layout file**: snake_case with activity prefix (activity_main)

This is a convention, not a requirement, but it's widely followed in the Android community.

### Creating a Simple Activity

```kotlin
// MainActivity.kt
package com.example.uidemo

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

/**
 * MainActivity - The main screen of our application
 * This activity demonstrates the basic structure of an Android Activity
 */
class MainActivity : AppCompatActivity() {
    
    /**
     * onCreate is the entry point of the activity lifecycle
     * Called when the activity is first created
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        // Always call super.onCreate() first
        super.onCreate(savedInstanceState)
        
        // Link this activity to its XML layout file
        // R.layout.activity_main refers to res/layout/activity_main.xml
        setContentView(R.layout.activity_main)
        
        println("MainActivity has been created!")
    }
    
    /**
     * onStart is called when the activity becomes visible to the user
     */
    override fun onStart() {
        super.onStart()
        println("MainActivity is now visible")
    }
    
    /**
     * onResume is called when the activity starts interacting with the user
     */
    override fun onResume() {
        super.onResume()
        println("MainActivity is now active and in the foreground")
    }
}
```

### Understanding XML Layouts

XML layouts define the visual structure of your Activity. Here's a simple example:

```xml
<!-- activity_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- TextView displays text to the user -->
    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/hello_world"
        android:textSize="24sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### Editing XML: Two Approaches

Android Studio provides two ways to work with XML layouts:

1. **Design View**: Visual drag-and-drop editor
2. **Code View**: Direct XML editing

**When to use each:**

- **Design View**: Quick prototyping, positioning elements visually
- **Code View**: Precise control, setting specific attributes, better for complex layouts

Often, you'll need to switch between both views. The visual editor is convenient, but sometimes manually editing XML gives you more control.

### Mobile Screen Wireframe Example

```
┌───────────────────────────────┐
│  ≡  My App         [⋮]        │ ← Toolbar/Action Bar
├───────────────────────────────┤
│                               │
│         Hello World!          │ ← TextView
│                               │
│                               │
│    ┌─────────────────┐        │
│    │   Click Me!     │        │ ← Button
│    └─────────────────┘        │
│                               │
│    [________________]         │ ← EditText (input field)
│                               │
│                               │
│    ┌──────────────────┐       │
│    │  Submit          │       │ ← Another Button
│    └──────────────────┘       │
│                               │
│                               │
└───────────────────────────────┘
```

### Activity Lifecycle Example

```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

/**
 * Comprehensive Activity lifecycle demonstration
 * This shows all major lifecycle methods and when they're called
 */
class LifecycleActivity : AppCompatActivity() {
    
    private var activityState = "Not created"
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_lifecycle)
        activityState = "Created"
        println("1. onCreate() - Activity is being created")
        println("   State: $activityState")
    }
    
    override fun onStart() {
        super.onStart()
        activityState = "Started"
        println("2. onStart() - Activity is becoming visible")
        println("   State: $activityState")
    }
    
    override fun onResume() {
        super.onResume()
        activityState = "Resumed"
        println("3. onResume() - Activity is now in foreground")
        println("   State: $activityState")
    }
    
    override fun onPause() {
        super.onPause()
        activityState = "Paused"
        println("4. onPause() - Another activity is coming to foreground")
        println("   State: $activityState")
    }
    
    override fun onStop() {
        super.onStop()
        activityState = "Stopped"
        println("5. onStop() - Activity is no longer visible")
        println("   State: $activityState")
    }
    
    override fun onDestroy() {
        super.onDestroy()
        activityState = "Destroyed"
        println("6. onDestroy() - Activity is being destroyed")
        println("   State: $activityState")
    }
}
```

**Sample Output (app launch):**
```
1. onCreate() - Activity is being created
   State: Created
2. onStart() - Activity is becoming visible
   State: Started
3. onResume() - Activity is now in foreground
   State: Resumed
```

---

## View Binding

### The Old Way: findViewById()

Before View Binding, accessing UI elements required using `findViewById()`, which had several problems:

```kotlin
// Old approach - findViewById() (Don't use this!)
val button = findViewById<Button>(R.id.myButton)
button.text = "Click me"
```

**Problems with this approach:**

1. **Null Safety**: Could return null if ID doesn't exist
2. **Type Safety**: Easy to cast to wrong type
3. **Boilerplate**: Repetitive code for each view
4. **Performance**: Multiple calls traverse the view hierarchy repeatedly

### The Modern Way: View Binding

View Binding generates a binding class for each XML layout, providing type-safe and null-safe access to all views with IDs.

### Setting Up View Binding

First, enable View Binding in your module-level `build.gradle` file:

```gradle
android {
    ...
    buildFeatures {
        viewBinding = true
    }
}
```

After syncing, Android Studio automatically generates binding classes based on your XML layout files.

### Naming Convention

The binding class name is derived from the XML filename:

- `activity_main.xml` → `ActivityMainBinding`
- `fragment_profile.xml` → `FragmentProfileBinding`
- `item_book.xml` → `ItemBookBinding`

Pattern: Convert snake_case to PascalCase and append "Binding"

### Using View Binding in an Activity

```kotlin
// MainActivity.kt
package com.example.viewbindingdemo

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.viewbindingdemo.databinding.ActivityMainBinding

/**
 * MainActivity demonstrating proper View Binding usage
 * View Binding provides type-safe access to all views in the layout
 */
class MainActivity : AppCompatActivity() {
    
    // Declare binding variable as lateinit
    // We can't initialize it immediately because we need the layout inflater
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Initialize the binding
        // inflate() creates the binding object from the XML layout
        binding = ActivityMainBinding.inflate(layoutInflater)
        
        // Get the root view from the binding
        // This is the top-level view from our XML layout
        val view = binding.root
        
        // Set the content view to the root view
        setContentView(view)
        
        // Now we can safely access all views through the binding object
        setupViews()
    }
    
    /**
     * Set up view properties and listeners
     * Notice how we access views through the binding object
     */
    private fun setupViews() {
        // Access TextView - no casting needed, completely type-safe
        binding.textView.text = getString(R.string.welcome_message)
        binding.textView.textSize = 18f
        
        // Access EditText
        binding.editText.hint = "Enter your name"
        
        // Access Button and set click listener
        binding.submitButton.setOnClickListener {
            val userName = binding.editText.text.toString()
            binding.textView.text = "Hello, $userName!"
            println("User entered: $userName")
        }
        
        println("All views initialized successfully through View Binding")
    }
}
```

**Sample Output (after user types "Alice" and clicks button):**
```
All views initialized successfully through View Binding
User entered: Alice
```

### Corresponding XML Layout

```xml
<!-- activity_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <!-- TextView - accessed via binding.textView -->
    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/welcome_message"
        android:textSize="24sp"
        android:layout_marginBottom="16dp" />

    <!-- EditText - accessed via binding.editText -->
    <EditText
        android:id="@+id/editText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter text"
        android:layout_marginBottom="16dp" />

    <!-- Button - accessed via binding.submitButton -->
    <Button
        android:id="@+id/submitButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Submit" />

</LinearLayout>
```

### View Binding vs Kotlin Android Extensions

You might see older code using Kotlin Android Extensions (synthetic properties):

```kotlin
// OLD WAY - Kotlin Android Extensions (Deprecated!)
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Direct access by ID name
        textView.text = "Hello"
    }
}
```

**Why this was deprecated:**

1. **Namespace pollution**: All IDs from all layouts were accessible
2. **Null safety issues**: Could access views that don't exist in current layout
3. **Build performance**: Slower builds
4. **No type safety**: Easy to make mistakes

**Always use View Binding in new projects!**

### Complete View Binding Example

```kotlin
import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityCalculatorBinding

/**
 * Calculator Activity using View Binding
 * Demonstrates comprehensive View Binding usage with multiple views
 */
class CalculatorActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityCalculatorBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Initialize binding
        binding = ActivityCalculatorBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupCalculator()
    }
    
    /**
     * Set up calculator functionality
     */
    private fun setupCalculator() {
        // Set up add button click listener
        binding.addButton.setOnClickListener {
            performCalculation { a, b -> a + b }
        }
        
        // Set up subtract button click listener
        binding.subtractButton.setOnClickListener {
            performCalculation { a, b -> a - b }
        }
        
        // Set up multiply button click listener
        binding.multiplyButton.setOnClickListener {
            performCalculation { a, b -> a * b }
        }
        
        // Set up divide button click listener
        binding.divideButton.setOnClickListener {
            performCalculation { a, b -> 
                if (b != 0.0) a / b else {
                    showError("Cannot divide by zero")
                    0.0
                }
            }
        }
        
        // Clear button
        binding.clearButton.setOnClickListener {
            clearInputs()
        }
    }
    
    /**
     * Perform calculation with given operation
     * @param operation Lambda function that performs the calculation
     */
    private fun performCalculation(operation: (Double, Double) -> Double) {
        val num1Str = binding.number1Input.text.toString()
        val num2Str = binding.number2Input.text.toString()
        
        if (num1Str.isBlank() || num2Str.isBlank()) {
            showError("Please enter both numbers")
            return
        }
        
        try {
            val num1 = num1Str.toDouble()
            val num2 = num2Str.toDouble()
            val result = operation(num1, num2)
            
            // Display result
            binding.resultText.text = "Result: $result"
            println("Calculation: $num1, $num2 = $result")
        } catch (e: NumberFormatException) {
            showError("Invalid number format")
        }
    }
    
    /**
     * Clear all inputs and results
     */
    private fun clearInputs() {
        binding.number1Input.text.clear()
        binding.number2Input.text.clear()
        binding.resultText.text = "Result: "
        println("Calculator cleared")
    }
    
    /**
     * Show error message as Toast
     */
    private fun showError(message: String) {
        Toast.makeText(this, message, Toast.LENGTH_SHORT).show()
        println("Error: $message")
    }
}
```

**Sample Output:**
```
Calculation: 15.0, 3.0 = 18.0
Calculation: 15.0, 3.0 = 5.0
Calculator cleared
```

---

## String Resources and Internationalization

### Why Not Hardcode Strings?

You might be tempted to write:

```kotlin
// BAD PRACTICE - Don't do this!
textView.text = "Welcome to my app"
button.text = "Click here"
```

This creates several problems:

1. **Internationalization**: Can't easily translate to other languages
2. **Maintenance**: Hard to update text across the entire app
3. **Consistency**: Same text might be typed differently in different places
4. **Professionalism**: Proper apps separate content from code

### Using String Resources

Instead, store strings in `res/values/strings.xml`:

```xml
<!-- strings.xml -->
<resources>
    <string name="app_name">My Android App</string>
    <string name="welcome_message">Welcome to my app</string>
    <string name="button_click">Click here</string>
    <string name="enter_name">Please enter your name</string>
    <string name="greeting">Hello, %1$s!</string>
</resources>
```

### Accessing Strings in Kotlin

```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityMainBinding

/**
 * Demonstration of proper string resource usage
 */
class StringResourceActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        demonstrateStringResources()
    }
    
    /**
     * Various ways to use string resources
     */
    private fun demonstrateStringResources() {
        // Method 1: Simple string resource
        binding.textView.text = getString(R.string.welcome_message)
        
        // Method 2: String with placeholder
        val userName = "Alice"
        val greeting = getString(R.string.greeting, userName)
        binding.greetingText.text = greeting
        
        // Method 3: Directly in XML (see XML section below)
        // android:text="@string/button_click"
        
        println("Welcome message: ${getString(R.string.welcome_message)}")
        println("Greeting: $greeting")
    }
}
```

### Accessing Strings in XML

```xml
<!-- activity_main.xml -->
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <!-- Reference string resource with @string/ -->
    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/welcome_message" />

    <Button
        android:id="@+id/clickButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/button_click" />

</LinearLayout>
```

### Internationalization (i18n)

Supporting multiple languages is straightforward with string resources.

**File Structure:**
```
res/
├── values/                    (Default - English)
│   └── strings.xml
├── values-fr/                 (French)
│   └── strings.xml
├── values-es/                 (Spanish)
│   └── strings.xml
└── values-de/                 (German)
    └── strings.xml
```

**Example: Multiple Languages**

```xml
<!-- res/values/strings.xml (Default - English) -->
<resources>
    <string name="app_name">Hello World</string>
    <string name="press_me">Press me</string>
    <string name="greeting">Good morning!</string>
</resources>

<!-- res/values-fr/strings.xml (French) -->
<resources>
    <string name="app_name">Bonjour le monde</string>
    <string name="press_me">Presse moi</string>
    <string name="greeting">Bonjour!</string>
</resources>

<!-- res/values-es/strings.xml (Spanish) -->
<resources>
    <string name="app_name">Hola Mundo</string>
    <string name="press_me">Presioname</string>
    <string name="greeting">¡Buenos días!</string>
</resources>
```

### Using Localized Strings

```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityMainBinding
import java.util.*

/**
 * Activity demonstrating internationalization
 * The correct strings are automatically selected based on device language
 */
class InternationalActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        displayLocalizedContent()
    }
    
    /**
     * Display content that automatically adapts to device language
     */
    private fun displayLocalizedContent() {
        // Android automatically selects the right string resource
        // based on the device's language setting
        val appName = getString(R.string.app_name)
        val greeting = getString(R.string.greeting)
        
        binding.titleText.text = appName
        binding.greetingText.text = greeting
        binding.actionButton.text = getString(R.string.press_me)
        
        // Get current locale for debugging
        val currentLocale = Locale.getDefault()
        println("Current locale: ${currentLocale.language}")
        println("App name: $appName")
        println("Greeting: $greeting")
    }
}
```

**Sample Output (English device):**
```
Current locale: en
App name: Hello World
Greeting: Good morning!
```

**Sample Output (French device):**
```
Current locale: fr
App name: Bonjour le monde
Greeting: Bonjour!
```

### String Formatting with Arguments

```kotlin
// strings.xml
// <string name="items_count">You have %1$d items in your cart</string>
// <string name="price_total">Total: %1$.2f USD</string>
// <string name="full_name">%1$s %2$s</string>

/**
 * Example of formatted string resources
 */
fun demonstrateStringFormatting() {
    // Integer argument
    val itemCount = 5
    val itemsMessage = getString(R.string.items_count, itemCount)
    println(itemsMessage)  // "You have 5 items in your cart"
    
    // Floating point argument
    val price = 49.99
    val priceMessage = getString(R.string.price_total, price)
    println(priceMessage)  // "Total: 49.99 USD"
    
    // Multiple arguments
    val firstName = "Alice"
    val lastName = "Johnson"
    val fullName = getString(R.string.full_name, firstName, lastName)
    println(fullName)  // "Alice Johnson"
}
```

---

## Testing Applications

### Using the Android Emulator

The Android Emulator allows you to test your applications without needing physical devices for every configuration you want to support.

### Key Points About the Emulator

1. **Slow Boot Time**: Emulators can take 1-3 minutes to boot initially
2. **Keep It Running**: Don't close the emulator between runs; just deploy new builds to it
3. **Resource Intensive**: Expect 3-4 GB RAM usage
4. **Hardware Acceleration**: Ensure virtualization is enabled in your BIOS for best performance

### Running Your Application

```kotlin
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityMainBinding

/**
 * Simple test activity to verify app is running correctly
 */
class TestActivity : AppCompatActivity() {
    
    companion object {
        private const val TAG = "TestActivity"
    }
    
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        // Log to Logcat for debugging
        Log.d(TAG, "TestActivity created successfully")
        
        setupTestUI()
    }
    
    /**
     * Set up test UI elements
     */
    private fun setupTestUI() {
        binding.statusText.text = "App is running!"
        
        binding.testButton.setOnClickListener {
            val message = "Test button clicked at ${System.currentTimeMillis()}"
            Log.d(TAG, message)
            binding.statusText.text = message
        }
        
        Log.d(TAG, "UI setup complete")
    }
    
    override fun onStart() {
        super.onStart()
        Log.d(TAG, "Activity started")
    }
    
    override fun onResume() {
        super.onResume()
        Log.d(TAG, "Activity resumed - visible to user")
    }
}
```

### Viewing Logs in Logcat

When testing, use Android Studio's Logcat to see debug output:

**Sample Logcat Output:**
```
2024-11-28 14:23:45.123  D/TestActivity: TestActivity created successfully
2024-11-28 14:23:45.156  D/TestActivity: UI setup complete
2024-11-28 14:23:45.189  D/TestActivity: Activity started
2024-11-28 14:23:45.223  D/TestActivity: Activity resumed - visible to user
2024-11-28 14:23:52.456  D/TestActivity: Test button clicked at 1701181432456
```

### Emulator Screen Representation

```
┌─────────────────────────────────┐
│ ☰  Test App            [⋮]      │ ← App Bar
├─────────────────────────────────┤
│                                 │
│   ╔═══════════════════════╗     │
│   ║  App is running!      ║     │ ← Status TextView
│   ╚═══════════════════════╝     │
│                                 │
│                                 │
│        ┌─────────────┐          │
│        │ Test Button │          │ ← Clickable Button
│        └─────────────┘          │
│                                 │
│                                 │
│                                 │
│                                 │
│                                 │
│                                 │
└─────────────────────────────────┘
       Android Emulator
```

### Creating and Selecting a Virtual Device

When you click "Run" in Android Studio:

1. You'll see a "Select Deployment Target" dialog
2. If no devices exist, click "Create New Virtual Device"
3. Choose a device definition (e.g., Pixel 4)
4. Select a system image (Android version)
5. Configure settings (RAM, storage, etc.)
6. Click "Finish"

The emulator will boot and your app will automatically install and launch.

---

## Interfaces in Android: OnClickListener

### Recap: What is an Interface?

An interface defines a contract - a set of methods that a class must implement. Think of it as a promise: "Any class that implements this interface will provide these specific methods."

**Key characteristics:**

- Defines method signatures without implementation
- Cannot store state (no properties with backing fields)
- A class can implement multiple interfaces
- Kotlin interfaces can have default implementations

### The OnClickListener Interface

One of the most commonly used interfaces in Android is `View.OnClickListener`. It defines how click events should be handled.

```kotlin
// The OnClickListener interface (simplified version)
interface OnClickListener {
    /**
     * Called when a view has been clicked
     * @param v The view that was clicked
     */
    fun onClick(v: View)
}
```

### Approach 1: Activity Implements OnClickListener

The Activity itself can implement the interface:

```kotlin
import android.os.Bundle
import android.view.View
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityClickDemoBinding

/**
 * Activity that implements OnClickListener interface
 * This approach is useful when handling clicks for multiple buttons
 */
class ClickDemoActivity : AppCompatActivity(), View.OnClickListener {
    
    private lateinit var binding: ActivityClickDemoBinding
    private var clickCount = 0
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityClickDemoBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        // Set this activity as the click listener for buttons
        // 'this' refers to the Activity, which implements OnClickListener
        binding.button1.setOnClickListener(this)
        binding.button2.setOnClickListener(this)
        binding.button3.setOnClickListener(this)
        
        println("Click handlers set up using Activity as listener")
    }
    
    /**
     * Handle clicks for all buttons
     * We use the view ID to determine which button was clicked
     */
    override fun onClick(v: View?) {
        clickCount++
        
        when (v?.id) {
            R.id.button1 -> {
                binding.resultText.text = "Button 1 clicked"
                println("Button 1 clicked (total clicks: $clickCount)")
            }
            R.id.button2 -> {
                binding.resultText.text = "Button 2 clicked"
                println("Button 2 clicked (total clicks: $clickCount)")
            }
            R.id.button3 -> {
                binding.resultText.text = "Button 3 clicked"
                println("Button 3 clicked (total clicks: $clickCount)")
            }
        }
        
        Toast.makeText(this, "Total clicks: $clickCount", Toast.LENGTH_SHORT).show()
    }
}
```

**Sample Output:**
```
Click handlers set up using Activity as listener
Button 1 clicked (total clicks: 1)
Button 2 clicked (total clicks: 2)
Button 1 clicked (total clicks: 3)
```

### Approach 2: Anonymous Object

Create an anonymous object that implements the interface:

```kotlin
import android.os.Bundle
import android.view.View
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityAnonymousBinding

/**
 * Using anonymous objects to implement OnClickListener
 * Each button gets its own listener object
 */
class AnonymousListenerActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityAnonymousBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityAnonymousBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupAnonymousListeners()
    }
    
    /**
     * Set up click listeners using anonymous objects
     */
    private fun setupAnonymousListeners() {
        // Create an anonymous object implementing OnClickListener
        val listener1 = object : View.OnClickListener {
            override fun onClick(v: View?) {
                binding.displayText.text = "First button clicked"
                println("Anonymous listener 1 triggered")
            }
        }
        
        val listener2 = object : View.OnClickListener {
            override fun onClick(v: View?) {
                binding.displayText.text = "Second button clicked"
                println("Anonymous listener 2 triggered")
            }
        }
        
        // Assign listeners to buttons
        binding.firstButton.setOnClickListener(listener1)
        binding.secondButton.setOnClickListener(listener2)
        
        println("Anonymous listeners configured")
    }
}
```

**Sample Output:**
```
Anonymous listeners configured
Anonymous listener 1 triggered
Anonymous listener 2 triggered
```

### Approach 3: Lambda Expression (Most Common)

Kotlin allows us to use lambda expressions when implementing interfaces with a single method (SAM - Single Abstract Method):

```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityLambdaBinding

/**
 * Using lambda expressions for click listeners
 * This is the most concise and commonly used approach
 */
class LambdaListenerActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityLambdaBinding
    private var counter = 0
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityLambdaBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupLambdaListeners()
    }
    
    /**
     * Set up click listeners using lambda expressions
     * This is the most modern and concise approach
     */
    private fun setupLambdaListeners() {
        // Simple lambda - single line
        binding.incrementButton.setOnClickListener {
            counter++
            updateDisplay()
            println("Counter incremented: $counter")
        }
        
        // Lambda with explicit parameter (though we don't use it here)
        binding.decrementButton.setOnClickListener { view ->
            counter--
            updateDisplay()
            println("Counter decremented: $counter")
        }
        
        // Lambda calling a separate function
        binding.resetButton.setOnClickListener {
            resetCounter()
        }
        
        // Multi-line lambda
        binding.doubleButton.setOnClickListener {
            val oldValue = counter
            counter *= 2
            updateDisplay()
            println("Counter doubled from $oldValue to $counter")
        }
        
        println("Lambda listeners configured successfully")
    }
    
    /**
     * Update the display with current counter value
     */
    private fun updateDisplay() {
        binding.counterText.text = "Count: $counter"
    }
    
    /**
     * Reset counter to zero
     */
    private fun resetCounter() {
        counter = 0
        updateDisplay()
        println("Counter reset to 0")
    }
}
```

**Sample Output:**
```
Lambda listeners configured successfully
Counter incremented: 1
Counter incremented: 2
Counter doubled from 2 to 4
Counter decremented: 3
Counter reset to 0
```

### Approach 4: XML onClick Attribute

You can also specify a click handler method name directly in XML:

```xml
<!-- activity_xml_click.xml -->
<Button
    android:id="@+id/myButton"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Click Me"
    android:onClick="handleButtonClick" />
```

```kotlin
import android.os.Bundle
import android.view.View
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityXmlClickBinding

/**
 * Using XML onClick attribute
 * The method name is specified in XML, and must be public with View parameter
 */
class XmlClickActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityXmlClickBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityXmlClickBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        println("Activity created - XML onClick methods ready")
    }
    
    /**
     * This method is called when the button is clicked
     * Method name matches android:onClick attribute in XML
     * Must be public, return void, and take a View parameter
     */
    fun handleButtonClick(view: View) {
        binding.messageText.text = "Button clicked via XML onClick!"
        println("XML onClick method triggered")
    }
    
    /**
     * Another example XML onClick handler
     */
    fun handleSecondButton(view: View) {
        binding.messageText.text = "Second button clicked!"
        println("Second XML onClick method triggered")
    }
}
```

**Sample Output:**
```
Activity created - XML onClick methods ready
XML onClick method triggered
Second XML onClick method triggered
```

### Comparing All Approaches

```kotlin
import android.os.Bundle
import android.view.View
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.example.demo.databinding.ActivityComparisonBinding

/**
 * Comprehensive example showing all click listener approaches
 * Demonstrates when to use each technique
 */
class ComparisonActivity : AppCompatActivity(), View.OnClickListener {
    
    private lateinit var binding: ActivityComparisonBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityComparisonBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupAllApproaches()
    }
    
    /**
     * Demonstrate all four approaches to click handling
     */
    private fun setupAllApproaches() {
        // Approach 1: Activity implements interface
        // Good when: Handling many buttons, need to share state
        binding.approach1Button.setOnClickListener(this)
        
        // Approach 2: Anonymous object
        // Good when: Need explicit object, more complex logic
        val anonymousListener = object : View.OnClickListener {
            override fun onClick(v: View?) {
                showMessage("Anonymous object approach")
                println("Approach 2: Anonymous object")
            }
        }
        binding.approach2Button.setOnClickListener(anonymousListener)
        
        // Approach 3: Lambda expression (RECOMMENDED)
        // Good when: Simple, modern, most cases
        binding.approach3Button.setOnClickListener {
            showMessage("Lambda expression approach")
            println("Approach 3: Lambda expression")
        }
        
        // Approach 4: XML onClick is set in XML file
        // (See xmlOnClickHandler method below)
        // Good when: Very simple actions, prototyping
        
        println("All click handler approaches configured")
    }
    
    /**
     * Implementation of OnClickListener interface (Approach 1)
     */
    override fun onClick(v: View?) {
        when (v?.id) {
            R.id.approach1Button -> {
                showMessage("Activity implements interface")
                println("Approach 1: Activity as listener")
            }
        }
    }
    
    /**
     * XML onClick handler (Approach 4)
     * Referenced in XML: android:onClick="xmlOnClickHandler"
     */
    fun xmlOnClickHandler(view: View) {
        showMessage("XML onClick attribute")
        println("Approach 4: XML onClick")
    }
    
    /**
     * Helper method to show toast and update display
     */
    private fun showMessage(message: String) {
        binding.resultText.text = "Used: $message"
        Toast.makeText(this, message, Toast.LENGTH_SHORT).show()
    }
}
```

**Sample Output:**
```
All click handler approaches configured
Approach 1: Activity as listener
Approach 2: Anonymous object
Approach 3: Lambda expression
Approach 4: XML onClick
```

### When to Use Each Approach

**Lambda Expression (Approach 3)** - RECOMMENDED
- ✅ Most concise and readable
- ✅ Modern Kotlin style
- ✅ Use for most situations

**Activity Implements Interface (Approach 1)**
- ✅ Good when handling many buttons
- ✅ Need to share state between handlers
- ⚠️ More verbose

**Anonymous Object (Approach 2)**
- ✅ When you need an explicit object reference
- ✅ Complex logic requiring multiple methods
- ⚠️ More verbose than lambda

**XML onClick (Approach 4)**
- ✅ Quick prototyping
- ⚠️ Less flexible
- ⚠️ Harder to refactor
- ⚠️ Not type-safe

---

## Summary

### Key Concepts Covered

We've explored the fundamentals of Android development with Android Studio:

1. **Android Studio IDE**
   - Based on IntelliJ IDEA
   - Uses Gradle build system for automation
   - Handles compilation, packaging, and signing

2. **Android Version Management**
   - API levels correspond to Android versions
   - Must target recent APIs for Play Store
   - Handle version differences with conditional code

3. **Virtual Devices**
   - Enable testing without physical devices
   - Resource-intensive (keep running once started)
   - Emulate various device configurations

4. **Project Structure**
   - `manifests/`: App configuration and permissions
   - `java/`: Kotlin/Java source code
   - `res/`: All resources (layouts, strings, images)

5. **Activities and UI**
   - Activities represent screens
   - XML defines layouts
   - Naming convention: MainActivity.kt ↔ activity_main.xml

6. **View Binding**
   - Type-safe, null-safe view access
   - Replaces findViewById() and Kotlin synthetics
   - Generated classes based on XML layouts

7. **String Resources**
   - Never hardcode strings
   - Enable easy internationalization
   - Accessed via R.string.string_name

8. **Testing**
   - Use emulator for testing
   - Logcat for debugging output
   - Keep emulator running between builds

9. **Interfaces and Click Listeners**
   - Interfaces define contracts
   - OnClickListener handles click events
   - Multiple implementation approaches (prefer lambdas)

### Learning Outcomes Achievement

You should now be able to:

✅ Set up and navigate Android Studio
✅ Understand Android project structure
✅ Create Activities with XML layouts
✅ Use View Binding safely
✅ Manage string resources properly
✅ Test apps in the emulator
✅ Implement click handlers multiple ways
✅ Build simple interactive Android applications

### Quick Reference: Essential Code Patterns

**Setting up an Activity with View Binding:**
```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
    }
}
```

**Handling clicks (recommended approach):**
```kotlin
binding.myButton.setOnClickListener {
    // Handle click
    binding.textView.text = "Button clicked!"
}
```

**Using string resources:**
```kotlin
// In Kotlin
val message = getString(R.string.my_message)

// In XML
android:text="@string/my_message"
```

### Next Steps

Now that you understand the fundamentals, you're ready to:
- Build more complex user interfaces
- Work with multiple Activities
- Handle data persistence
- Integrate with Android architecture components
- Explore modern Android development with Jetpack Compose

Continue practicing by building small applications and experimenting with different UI components and layouts. The best way to learn Android development is through hands-on experience!

---

## Section-to-Learning Outcome Mapping

| Section | Learning Outcomes Addressed |
|---------|----------------------------|
| Introduction to Android Studio | Understanding IDE role and features |
| Android Version Management | Understanding version requirements and compatibility |
| Virtual Devices and Testing | Creating and using virtual devices, testing applications |
| Android Project Structure | Navigating project structure effectively |
| Android UI and Activities | Understanding Activities, XML layouts, UI basics |
| View Binding | Implementing View Binding for safe view access |
| String Resources and Internationalization | Managing string resources, supporting multiple languages |
| Testing Applications | Running apps in emulator, debugging with Logcat |
| Interfaces in Android | Understanding interfaces, implementing OnClickListener |

---

*End of Lecture*
