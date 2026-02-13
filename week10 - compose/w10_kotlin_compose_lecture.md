# Android Development with Compose
**CO5225 – Lecture Session**

---

## Table of Contents

1. [Learning Outcomes](#learning-outcomes)
2. [Introduction to Jetpack Compose](#introduction-to-jetpack-compose)
3. [Activities with Compose](#activities-with-compose)
4. [Understanding Compose Syntax](#understanding-compose-syntax)
5. [Composable Functions](#composable-functions)
6. [Pre-defined Composable Functions](#pre-defined-composable-functions)
7. [Layout Composables](#layout-composables)
8. [Material Design Components](#material-design-components)
9. [Building a Complete Example](#building-a-complete-example)
10. [Summary](#summary)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Understand the fundamental concepts of Jetpack Compose for Android UI development
- Create Activities that use Compose instead of XML for layout
- Write and use Composable functions with proper naming conventions
- Apply higher-order functions and lambda syntax in Compose contexts
- Utilize pre-defined Layout composables (Box, Column, Row, Spacer) to structure UI
- Implement Material Design components (Button, Text, TextField, etc.) in your applications
- Build a complete Android application using Compose from scratch

---

## Introduction to Jetpack Compose

Jetpack Compose is Android's modern toolkit for building native user interfaces. Unlike the traditional XML-based approach, Compose uses a declarative paradigm where you describe what your UI should look like, and the framework handles the rest.

### Key Advantages of Compose

- **Declarative UI**: You describe what the UI should look like rather than how to build it
- **Less boilerplate**: No need for separate XML layout files
- **Kotlin-first**: Fully integrated with Kotlin's language features
- **Live previews**: See changes instantly without running the app
- **Simplified state management**: UI automatically updates when state changes

### Compose vs Traditional XML Approach

In traditional Android development, you would:
1. Define layouts in XML files
2. Reference views using `findViewById()`
3. Manually update UI elements when data changes

With Compose, you:
1. Define UI directly in Kotlin using Composable functions
2. No need to reference views
3. UI automatically recomposes when data changes

---

## Activities with Compose

When using Jetpack Compose, your Activity class extends `ComponentActivity` rather than the traditional `AppCompatActivity` used with XML layouts.

### Activity Hierarchy

```
Activity (base class)
    ├── AppCompatActivity (for XML-based layouts)
    └── ComponentActivity (for Compose-based layouts)
```

### Basic Activity Structure

Here's a complete example of an Activity using Compose:

```kotlin
// MainActivity.kt
package com.example.composebasics

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.material3.Text

/**
 * Main Activity for the application.
 * Extends ComponentActivity to support Jetpack Compose.
 */
class MainActivity : ComponentActivity() {
    
    /**
     * Called when the activity is first created.
     * This is where we set up the UI using Compose.
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // setContent establishes the Compose UI for this Activity
        setContent {
            // This lambda contains our Composable UI
            Text(text = "Hello World")
        }
    }
}
```

**Sample Output:**
```
┌─────────────────────┐
│                     │
│   Hello World       │
│                     │
│                     │
│                     │
│                     │
│                     │
└─────────────────────┘
```

### Key Points About Activities with Compose

1. **ComponentActivity**: The base class that provides Compose support
2. **onCreate method**: Where we initialize our UI
3. **setContent**: The method that establishes our Compose UI hierarchy
4. **No XML files**: All UI is defined in Kotlin code

---

## Understanding Compose Syntax

The syntax used in Compose leverages Kotlin's powerful features, particularly higher-order functions and lambda expressions.

### The setContent Method

Let's examine the signature of `setContent`:

```kotlin
public fun ComponentActivity.setContent(
    parent: CompositionContext? = null,
    content: @Composable () -> Unit
) {
    // implementation is here
}
```

**Parameter breakdown:**

1. **parent: CompositionContext? = null**
   - An optional parameter with a default value of null
   - Provides context for composition (rarely used directly)

2. **content: @Composable () -> Unit**
   - A Composable function that takes no parameters
   - Returns Unit (equivalent to void in Java)
   - This is where you define your UI

### Lambda Syntax Convention

In Kotlin, when the last parameter of a function is a lambda, you can move it outside the parentheses:

```kotlin
// These two calls are equivalent:

// Style 1: Lambda inside parentheses
setContent(content = {
    Text(text = "Hello World")
})

// Style 2: Lambda outside parentheses (preferred)
setContent {
    Text(text = "Hello World")
}
```

This convention makes the code more readable and is the standard way to call `setContent`.

### Higher-Order Functions Reminder

A higher-order function is a function that either:
- Takes functions as parameters, or
- Returns a function

Here's a simple example to refresh your memory:

```kotlin
package com.example.higherorderdemo

/**
 * Demonstrates higher-order functions in Kotlin.
 * This example shows how functions can take other functions as parameters.
 */

// A higher-order function that takes a function as a parameter
fun performOperation(x: Int, y: Int, operation: (Int, Int) -> Int): Int {
    return operation(x, y)
}

fun main() {
    // Pass different functions to the same higher-order function
    
    val sum = performOperation(5, 3) { a, b -> a + b }
    println("Sum: $sum")  // Output: Sum: 8
    
    val product = performOperation(5, 3) { a, b -> a * b }
    println("Product: $product")  // Output: Product: 15
    
    // Compose's setContent works similarly - it takes a Composable function
    // as its last parameter, which we provide as a lambda
}
```

**Sample Output:**
```
Sum: 8
Product: 15
```

---

## Composable Functions

Composable functions are the building blocks of Compose UI. They describe a piece of your user interface.

### Defining Composable Functions

A Composable function is any function annotated with `@Composable`:

```kotlin
package com.example.composablebasics

import androidx.compose.material3.Text
import androidx.compose.runtime.Composable

/**
 * A simple Composable function that displays a greeting.
 * Note: Composable functions use UpperCamelCase naming convention.
 */
@Composable
fun Greeting(name: String) {
    Text(text = "Hello, $name!")
}

/**
 * Another Composable that uses the Greeting Composable.
 * Composables can call other Composables.
 */
@Composable
fun GreetingScreen() {
    Greeting(name = "Android Developer")
}
```

### Naming Convention: UpperCamelCase

Unlike regular Kotlin functions which use lowerCamelCase, Composable functions use UpperCamelCase (also called PascalCase):

```kotlin
// Regular Kotlin function - lowerCamelCase
fun calculateSum(a: Int, b: Int): Int {
    return a + b
}

// Composable function - UpperCamelCase
@Composable
fun CalculatorDisplay(result: Int) {
    Text(text = "Result: $result")
}
```

**Why this convention?**
- Composables represent UI components, similar to classes
- Makes it clear at a glance that a function is a Composable
- Aligns with naming conventions for UI elements (like `Button`, `Text`, etc.)

### Complete Example with Multiple Composables

```kotlin
package com.example.composableexample

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Column
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable

/**
 * Demonstrates creating and using custom Composable functions.
 */
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            UserProfileScreen()
        }
    }
}

/**
 * A Composable that displays a user's profile information.
 * This demonstrates composing multiple smaller Composables together.
 */
@Composable
fun UserProfileScreen() {
    Column {
        ProfileHeader(name = "John Doe")
        ProfileDetail(label = "Email", value = "john.doe@example.com")
        ProfileDetail(label = "Location", value = "London, UK")
    }
}

/**
 * Displays the user's name as a header.
 */
@Composable
fun ProfileHeader(name: String) {
    Text(text = "Profile: $name")
}

/**
 * Displays a single detail about the user.
 */
@Composable
fun ProfileDetail(label: String, value: String) {
    Text(text = "$label: $value")
}
```

**Sample Output:**
```
┌─────────────────────────────────┐
│                                 │
│  Profile: John Doe              │
│  Email: john.doe@example.com    │
│  Location: London, UK           │
│                                 │
└─────────────────────────────────┘
```

---

## Pre-defined Composable Functions

Android provides three main categories of pre-defined Composable functions:

### 1. Layout Composables

These determine how UI components are arranged on screen:
- **Column**: Arranges children vertically
- **Row**: Arranges children horizontally
- **Box**: Stacks children on top of each other
- **Spacer**: Adds empty space

### 2. Foundation Composables

Basic UI elements without specific styling or theme:
- **Text**: Displays text
- **Canvas**: For custom drawing
- **Shape**: For custom shapes
- **Image**: Displays images

### 3. Material Design Composables

Components that follow Google's Material Design guidelines:
- **Button**: Interactive button
- **TextField**: Text input field
- **Checkbox**: Selection checkbox
- **Card**: Elevated container
- **Switch**: Toggle switch
- **Slider**: Value selector
- **FloatingActionButton**: Primary action button
- **Surface**: Container with elevation and shape

---

## Layout Composables

Layout composables are essential for structuring your UI. Let's explore each of the key layout components.

### Column - Vertical Arrangement

A Column arranges its children vertically from top to bottom:

```kotlin
package com.example.layouts

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

/**
 * Demonstrates the Column layout composable.
 * Column arranges children vertically (top to bottom).
 */
class ColumnExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ColumnDemo()
        }
    }
}

@Composable
fun ColumnDemo() {
    // Column with padding applied
    Column(
        modifier = Modifier
            .fillMaxSize()  // Fill the entire screen
            .padding(16.dp)  // Add padding around the column
    ) {
        Text(text = "First Item")
        Text(text = "Second Item")
        Text(text = "Third Item")
        Text(text = "Fourth Item")
    }
}
```

**Visual Layout:**
```
┌─────────────────────┐
│                     │
│  First Item         │
│  Second Item        │
│  Third Item         │
│  Fourth Item        │
│                     │
│                     │
└─────────────────────┘
```

### Row - Horizontal Arrangement

A Row arranges its children horizontally from left to right:

```kotlin
package com.example.layouts

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

/**
 * Demonstrates the Row layout composable.
 * Row arranges children horizontally (left to right).
 */
class RowExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            RowDemo()
        }
    }
}

@Composable
fun RowDemo() {
    // Row with padding applied
    Row(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(text = "Item 1")
        Text(text = "Item 2")
        Text(text = "Item 3")
    }
}
```

**Visual Layout:**
```
┌─────────────────────────────────┐
│                                 │
│  Item 1  Item 2  Item 3         │
│                                 │
│                                 │
└─────────────────────────────────┘
```

### Box - Stacking Layout

A Box stacks its children on top of each other:

```kotlin
package com.example.layouts

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

/**
 * Demonstrates the Box layout composable.
 * Box stacks children on top of each other (z-axis).
 */
class BoxExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            BoxDemo()
        }
    }
}

@Composable
fun BoxDemo() {
    // Box with centered content
    Box(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.LightGray),
        contentAlignment = Alignment.Center  // Center content in the box
    ) {
        // Background element (drawn first)
        Box(
            modifier = Modifier
                .size(200.dp)
                .background(Color.Blue)
        )
        
        // Foreground text (drawn on top)
        Text(
            text = "Centered Text",
            color = Color.White,
            modifier = Modifier.padding(16.dp)
        )
    }
}
```

**Visual Layout:**
```
┌─────────────────────┐
│                     │
│   ┌───────────┐     │
│   │           │     │
│   │ Centered  │     │
│   │   Text    │     │
│   │           │     │
│   └───────────┘     │
│                     │
└─────────────────────┘
```

### Spacer - Adding Empty Space

A Spacer adds flexible or fixed empty space between components:

```kotlin
package com.example.layouts

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

/**
 * Demonstrates the Spacer composable.
 * Spacer adds empty space between components.
 */
class SpacerExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            SpacerDemo()
        }
    }
}

@Composable
fun SpacerDemo() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(text = "First Item")
        
        // Add 24dp of vertical space
        Spacer(modifier = Modifier.height(24.dp))
        
        Text(text = "Second Item (24dp gap above)")
        
        // Add 8dp of vertical space
        Spacer(modifier = Modifier.height(8.dp))
        
        Text(text = "Third Item (8dp gap above)")
    }
}
```

**Visual Layout:**
```
┌─────────────────────────────────┐
│                                 │
│  First Item                     │
│                                 │
│         [24dp space]            │
│                                 │
│  Second Item (24dp gap above)   │
│    [8dp space]                  │
│  Third Item (8dp gap above)     │
│                                 │
└─────────────────────────────────┘
```

### Combining Layout Composables

You can nest layout composables to create complex UIs:

```kotlin
package com.example.layouts

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

/**
 * Demonstrates combining multiple layout composables.
 * Creates a card-like structure with header and details.
 */
class CombinedLayoutExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            CombinedLayoutDemo()
        }
    }
}

@Composable
fun CombinedLayoutDemo() {
    // Outer Column for vertical arrangement
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(text = "User Profile")
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Row for horizontal arrangement of name and age
        Row {
            Text(text = "Name:")
            Spacer(modifier = Modifier.width(8.dp))
            Text(text = "Alice Smith")
        }
        
        Spacer(modifier = Modifier.height(8.dp))
        
        // Another Row for location
        Row {
            Text(text = "Location:")
            Spacer(modifier = Modifier.width(8.dp))
            Text(text = "Manchester")
        }
    }
}
```

**Sample Output:**
```
┌─────────────────────────────────┐
│                                 │
│  User Profile                   │
│                                 │
│  Name:    Alice Smith           │
│  Location:    Manchester        │
│                                 │
└─────────────────────────────────┘
```

---

## Material Design Components

Material Design components provide ready-to-use UI elements that follow Google's Material Design guidelines. These components are styled and themed consistently.

### Text - Displaying Content

The Text composable displays text content:

```kotlin
package com.example.material

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

/**
 * Demonstrates the Text composable with various styling options.
 */
class TextExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            TextDemo()
        }
    }
}

@Composable
fun TextDemo() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // Simple text
        Text(text = "Simple Text")
        
        // Text with custom size
        Text(
            text = "Large Text",
            fontSize = 24.sp
        )
        
        // Text with custom color and weight
        Text(
            text = "Bold Blue Text",
            fontSize = 18.sp,
            fontWeight = FontWeight.Bold,
            color = Color.Blue
        )
    }
}
```

### Button - Interactive Component

Buttons handle user interactions:

```kotlin
package com.example.material

import android.os.Bundle
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Button
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.unit.dp

/**
 * Demonstrates the Button composable.
 * Buttons respond to user clicks and can trigger actions.
 */
class ButtonExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ButtonDemo()
        }
    }
}

@Composable
fun ButtonDemo() {
    // Get context for showing Toast messages
    val context = LocalContext.current
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // Simple button
        Button(onClick = {
            Toast.makeText(context, "Button clicked!", Toast.LENGTH_SHORT).show()
        }) {
            Text(text = "Click Me")
        }
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Button with icon and text (common pattern)
        Button(onClick = {
            Toast.makeText(context, "Submit clicked!", Toast.LENGTH_SHORT).show()
        }) {
            Text(text = "Submit")
        }
    }
}
```

**Sample Output (before click):**
```
┌─────────────────────┐
│                     │
│  ┌──────────────┐   │
│  │  Click Me    │   │
│  └──────────────┘   │
│                     │
│  ┌──────────────┐   │
│  │  Submit      │   │
│  └──────────────┘   │
│                     │
└─────────────────────┘
```

### TextField - Text Input

TextField allows users to input text:

```kotlin
package com.example.material

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

/**
 * Demonstrates the TextField composable.
 * TextField allows users to input and edit text.
 */
class TextFieldExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            TextFieldDemo()
        }
    }
}

@Composable
fun TextFieldDemo() {
    // State to hold the text field value
    var textValue by remember { mutableStateOf("") }
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(text = "Enter your name:")
        
        Spacer(modifier = Modifier.height(8.dp))
        
        // TextField that updates state when text changes
        TextField(
            value = textValue,
            onValueChange = { newText ->
                textValue = newText
            },
            label = { Text("Name") },
            placeholder = { Text("John Doe") }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Display the current value
        Text(text = "You entered: $textValue")
    }
}
```

**Sample Output (after typing "Alice"):**
```
┌─────────────────────────────┐
│                             │
│  Enter your name:           │
│                             │
│  ┌──────────────────────┐   │
│  │ Alice            │   │   │
│  │ Name             │   │   │
│  └──────────────────────┘   │
│                             │
│  You entered: Alice         │
│                             │
└─────────────────────────────┘
```

### Checkbox - Selection Component

Checkbox for boolean selection:

```kotlin
package com.example.material

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Checkbox
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

/**
 * Demonstrates the Checkbox composable.
 * Checkbox provides a boolean on/off selection.
 */
class CheckboxExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            CheckboxDemo()
        }
    }
}

@Composable
fun CheckboxDemo() {
    // State to hold checkbox values
    var isChecked1 by remember { mutableStateOf(false) }
    var isChecked2 by remember { mutableStateOf(true) }
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // First checkbox with label
        Row(
            verticalAlignment = Alignment.CenterVertically
        ) {
            Checkbox(
                checked = isChecked1,
                onCheckedChange = { isChecked1 = it }
            )
            Spacer(modifier = Modifier.width(8.dp))
            Text(text = "Accept terms and conditions")
        }
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Second checkbox with label
        Row(
            verticalAlignment = Alignment.CenterVertically
        ) {
            Checkbox(
                checked = isChecked2,
                onCheckedChange = { isChecked2 = it }
            )
            Spacer(modifier = Modifier.width(8.dp))
            Text(text = "Receive newsletter")
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // Display current state
        Text(text = "Terms accepted: $isChecked1")
        Text(text = "Newsletter: $isChecked2")
    }
}
```

### Card - Container Component

Card provides an elevated container for grouping content:

```kotlin
package com.example.material

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

/**
 * Demonstrates the Card composable.
 * Card provides an elevated container with rounded corners.
 */
class CardExample : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            CardDemo()
        }
    }
}

@Composable
fun CardDemo() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // Card containing user information
        Card(
            modifier = Modifier
                .fillMaxWidth()
                .padding(8.dp),
            elevation = CardDefaults.cardElevation(defaultElevation = 4.dp)
        ) {
            Column(
                modifier = Modifier.padding(16.dp)
            ) {
                Text(text = "User Card", fontSize = 20.sp)
                Spacer(modifier = Modifier.height(8.dp))
                Text(text = "Name: Bob Johnson")
                Text(text = "Email: bob@example.com")
                Text(text = "Role: Developer")
            }
        }
    }
}
```

**Visual Layout:**
```
┌─────────────────────────────────┐
│                                 │
│  ┏━━━━━━━━━━━━━━━━━━━━━━━━━┓   │
│  ┃ User Card               ┃   │
│  ┃                         ┃   │
│  ┃ Name: Bob Johnson       ┃   │
│  ┃ Email: bob@example.com  ┃   │
│  ┃ Role: Developer         ┃   │
│  ┗━━━━━━━━━━━━━━━━━━━━━━━━━┛   │
│                                 │
└─────────────────────────────────┘
```

---

## Building a Complete Example

Let's bring everything together in a complete application that demonstrates multiple concepts:

```kotlin
package com.example.completeapp

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

/**
 * Complete example application demonstrating Jetpack Compose.
 * This app allows users to create a simple profile with validation.
 */
class CompleteApp : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            // Apply Material Design theme
            MaterialTheme {
                // Use Surface for proper background
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    ProfileCreationScreen()
                }
            }
        }
    }
}

/**
 * Main screen for profile creation.
 * Demonstrates: Column layout, TextField, Checkbox, Button, and Card.
 */
@Composable
fun ProfileCreationScreen() {
    // State variables for form fields
    var name by remember { mutableStateOf("") }
    var email by remember { mutableStateOf("") }
    var agreeToTerms by remember { mutableStateOf(false) }
    var profileCreated by remember { mutableStateOf(false) }
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        // Header
        Text(
            text = "Create Profile",
            fontSize = 28.sp,
            fontWeight = FontWeight.Bold
        )
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // Name input field
        TextField(
            value = name,
            onValueChange = { name = it },
            label = { Text("Name") },
            placeholder = { Text("Enter your name") },
            modifier = Modifier.fillMaxWidth()
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Email input field
        TextField(
            value = email,
            onValueChange = { email = it },
            label = { Text("Email") },
            placeholder = { Text("Enter your email") },
            modifier = Modifier.fillMaxWidth()
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Terms and conditions checkbox
        Row(
            verticalAlignment = Alignment.CenterVertically,
            modifier = Modifier.fillMaxWidth()
        ) {
            Checkbox(
                checked = agreeToTerms,
                onCheckedChange = { agreeToTerms = it }
            )
            Spacer(modifier = Modifier.width(8.dp))
            Text(text = "I agree to the terms and conditions")
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // Submit button (enabled only when form is valid)
        Button(
            onClick = {
                profileCreated = true
            },
            enabled = name.isNotBlank() && email.isNotBlank() && agreeToTerms,
            modifier = Modifier.fillMaxWidth()
        ) {
            Text(text = "Create Profile")
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // Display profile card if created
        if (profileCreated) {
            ProfileCard(name = name, email = email)
        }
    }
}

/**
 * Displays the created profile in a card.
 * Demonstrates: Card composable for grouping related content.
 */
@Composable
fun ProfileCard(name: String, email: String) {
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(8.dp),
        elevation = CardDefaults.cardElevation(defaultElevation = 8.dp)
    ) {
        Column(
            modifier = Modifier.padding(16.dp)
        ) {
            Text(
                text = "Profile Created!",
                fontSize = 20.sp,
                fontWeight = FontWeight.Bold,
                color = MaterialTheme.colorScheme.primary
            )
            
            Spacer(modifier = Modifier.height(12.dp))
            
            Text(text = "Name: $name", fontSize = 16.sp)
            Spacer(modifier = Modifier.height(4.dp))
            Text(text = "Email: $email", fontSize = 16.sp)
            Spacer(modifier = Modifier.height(4.dp))
            Text(text = "Status: Active", fontSize = 16.sp)
        }
    }
}
```

**Sample Output (after filling form):**
```
┌─────────────────────────────────────┐
│                                     │
│        Create Profile               │
│                                     │
│  ┌──────────────────────────────┐   │
│  │ Alice Smith              ✓   │   │
│  │ Name                         │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │ alice@example.com        ✓   │   │
│  │ Email                        │   │
│  └──────────────────────────────┘   │
│                                     │
│  ☑ I agree to terms and conditions  │
│                                     │
│  ┌──────────────────────────────┐   │
│  │    Create Profile            │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓   │
│  ┃ Profile Created!           ┃   │
│  ┃                            ┃   │
│  ┃ Name: Alice Smith          ┃   │
│  ┃ Email: alice@example.com   ┃   │
│  ┃ Status: Active             ┃   │
│  ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛   │
│                                     │
└─────────────────────────────────────┘
```

### Key Concepts Demonstrated

1. **State Management**: Using `remember` and `mutableStateOf` to maintain UI state
2. **Form Validation**: Enabling/disabling button based on field values
3. **Conditional UI**: Showing profile card only after creation
4. **Material Theming**: Using `MaterialTheme` for consistent styling
5. **Layout Composition**: Nesting Column, Row, and other composables
6. **User Interaction**: Handling text input, checkbox changes, and button clicks

---

## Summary

In this lecture, we've covered the fundamentals of Android development with Jetpack Compose:

**Key Takeaways:**

1. **Activities with Compose** use `ComponentActivity` and `setContent` to define UI in Kotlin rather than XML

2. **Compose Syntax** leverages Kotlin's lambda expressions and higher-order functions for clean, readable code

3. **Composable Functions** are annotated with `@Composable` and use UpperCamelCase naming convention

4. **Layout Composables** (Column, Row, Box, Spacer) provide flexible ways to arrange UI components

5. **Material Components** (Button, TextField, Checkbox, Card, etc.) offer ready-to-use, styled UI elements

6. **Declarative UI** means you describe what the UI should look like, and Compose handles updates automatically

7. **State Management** with `remember` and `mutableStateOf` enables reactive UIs that update when data changes

**Advantages of Compose:**
- Less boilerplate code compared to XML
- Type-safe Kotlin instead of string-based XML
- Better preview and development experience
- Simplified state management
- Easier testing and reusability

Jetpack Compose represents the future of Android UI development, making it faster and more enjoyable to build beautiful, responsive applications. As you continue practicing with Compose, you'll discover its power in creating complex, interactive user interfaces with significantly less code than traditional approaches.
