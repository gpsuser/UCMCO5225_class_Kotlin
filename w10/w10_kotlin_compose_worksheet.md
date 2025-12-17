# Worksheet

## Quiz Section

### Question 1

Complete the following sentences by filling in the blanks using the words provided in the word bank below.

When using Jetpack Compose, your Activity class extends __________ rather than AppCompatActivity. Inside the onCreate method, you use the __________ method to establish your Compose UI hierarchy. All UI components in Compose are defined using __________, which are Kotlin functions annotated with @Composable.

**Word Bank:** ComponentActivity, setContent, Composable functions, XML layouts, ViewGroup, findViewById

---

### Question 2

Fill in the blanks using the words from the word bank below.

Compose provides three main layout composables for arranging UI elements. A __________ arranges its children vertically from top to bottom, while a __________ arranges its children horizontally from left to right. If you want to stack elements on top of each other, you should use a __________.

**Word Bank:** Column, Row, Box, LinearLayout, FrameLayout, GridLayout

---

### Question 3

Complete the following sentences using the words from the word bank.

In Compose, you manage UI state using the __________ function combined with __________. When the state value changes, the UI automatically __________ to reflect the new data. This is the core of Compose's declarative UI approach.

**Word Bank:** remember, mutableStateOf, recomposes, updates, setState, observes

---

### Question 4

Which naming convention should Composable functions follow?

a) lowerCamelCase (e.g., `myComposable()`)  
b) UpperCamelCase (e.g., `MyComposable()`)  
c) snake_case (e.g., `my_composable()`)  
d) UPPER_SNAKE_CASE (e.g., `MY_COMPOSABLE()`)

---

### Question 5

What is the primary purpose of the Spacer composable?

a) To create clickable areas in the UI  
b) To add empty space between UI components  
c) To display loading indicators  
d) To handle keyboard input

---

### Question 6

In the Activity hierarchy for Compose-based applications, which of the following is correct?

a) Activity → AppCompatActivity → ComponentActivity  
b) ComponentActivity → AppCompatActivity → Activity  
c) Activity → ComponentActivity (for Compose apps)  
d) AppCompatActivity → ComponentActivity → Activity

---

## Task Section

### Task 1: Simple Counter Application

Create a Compose application that displays a counter and has two buttons: one to increment the counter and one to reset it to zero.

**Requirements:**
- Display the current count value
- Include an "Increment" button that increases the count by 1
- Include a "Reset" button that sets the count back to 0
- Use proper state management

**Scaffolding:**

```kotlin
package com.example.counterapp

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            CounterScreen()
        }
    }
}

@Composable
fun CounterScreen() {
    // TODO: Create state variable for counter
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        // TODO: Display the counter value with Text composable
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // TODO: Create Increment button
        
        Spacer(modifier = Modifier.height(8.dp))
        
        // TODO: Create Reset button
    }
}
```

**Sample Output (Initial State):**
```
┌─────────────────────┐
│                     │
│     Count: 0        │
│                     │
│  ┌──────────────┐   │
│  │  Increment   │   │
│  └──────────────┘   │
│  ┌──────────────┐   │
│  │    Reset     │   │
│  └──────────────┘   │
│                     │
└─────────────────────┘
```

**Sample Output (After 3 Increments):**
```
┌─────────────────────┐
│                     │
│     Count: 3        │
│                     │
│  ┌──────────────┐   │
│  │  Increment   │   │
│  └──────────────┘   │
│  ┌──────────────┐   │
│  │    Reset     │   │
│  └──────────────┘   │
│                     │
└─────────────────────┘
```

---

### Task 2: Temperature Converter

Create a Compose application that converts temperature from Celsius to Fahrenheit. The app should have a TextField for input and display the converted temperature.

**Requirements:**
- TextField for entering Celsius temperature
- Display the Fahrenheit equivalent automatically
- Handle empty or invalid input gracefully
- Use the formula: F = (C × 9/5) + 32

**Scaffolding:**

```kotlin
package com.example.tempconverter

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            TemperatureConverterScreen()
        }
    }
}

@Composable
fun TemperatureConverterScreen() {
    // TODO: Create state variable for celsius input
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(
            text = "Temperature Converter",
            fontSize = 24.sp
        )
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // TODO: Create TextField for Celsius input
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // TODO: Calculate and display Fahrenheit value
        // Handle the case when input is empty or invalid
    }
}
```

**Sample Input:**
```
Celsius: 0
```

**Sample Output:**
```
┌─────────────────────────────────┐
│                                 │
│  Temperature Converter          │
│                                 │
│  ┌───────────────────────────┐  │
│  │ 0                    ✓    │  │
│  │ Celsius                   │  │
│  └───────────────────────────┘  │
│                                 │
│  Fahrenheit: 32.0°F             │
│                                 │
└─────────────────────────────────┘
```

**Sample Input:**
```
Celsius: 25
```

**Sample Output:**
```
┌─────────────────────────────────┐
│                                 │
│  Temperature Converter          │
│                                 │
│  ┌───────────────────────────┐  │
│  │ 25                   ✓    │  │
│  │ Celsius                   │  │
│  └───────────────────────────┘  │
│                                 │
│  Fahrenheit: 77.0°F             │
│                                 │
└─────────────────────────────────┘
```

---

### Task 3: Interactive Task Item (Challenge)

Create a reusable TaskItem composable that displays a task with a checkbox and a delete button. The component should show a task description, allow marking it as complete, and provide a way to delete it.

**Requirements:**
- Display a task description text
- Include a checkbox to mark the task as complete/incomplete
- Include a delete button
- Strike through the text when the task is marked as complete
- Use a Row layout to arrange components horizontally
- Make the component reusable with parameters

**Scaffolding:**

```kotlin
package com.example.taskitem

import android.os.Bundle
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.dp

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            TaskItemDemo()
        }
    }
}

@Composable
fun TaskItemDemo() {
    val context = LocalContext.current
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(text = "My Tasks", style = MaterialTheme.typography.headlineMedium)
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Example usage of TaskItem
        TaskItem(
            taskDescription = "Complete Compose worksheet",
            onDelete = {
                Toast.makeText(context, "Task deleted", Toast.LENGTH_SHORT).show()
            }
        )
        
        Spacer(modifier = Modifier.height(8.dp))
        
        TaskItem(
            taskDescription = "Study for exam",
            onDelete = {
                Toast.makeText(context, "Task deleted", Toast.LENGTH_SHORT).show()
            }
        )
    }
}

/**
 * A reusable composable that displays a task item.
 * 
 * @param taskDescription The text description of the task
 * @param onDelete Callback function invoked when delete button is clicked
 */
@Composable
fun TaskItem(
    taskDescription: String,
    onDelete: () -> Unit
) {
    // TODO: Create state variable to track if task is completed
    
    Card(
        modifier = Modifier.fillMaxWidth(),
        elevation = CardDefaults.cardElevation(defaultElevation = 2.dp)
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(8.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            // TODO: Add Checkbox that updates the completed state
            
            Spacer(modifier = Modifier.width(8.dp))
            
            // TODO: Add Text that displays taskDescription
            // Apply TextDecoration.LineThrough when completed is true
            // Use Modifier.weight(1f) to make it take up remaining space
            
            Spacer(modifier = Modifier.width(8.dp))
            
            // TODO: Add Delete Button that calls onDelete
        }
    }
}
```

**Sample Output (Initial State):**
```
┌─────────────────────────────────────────┐
│                                         │
│  My Tasks                               │
│                                         │
│  ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓  │
│  ┃ ☐ Complete Compose worksheet    ┃  │
│  ┃                          [Delete]┃  │
│  ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛  │
│                                         │
│  ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓  │
│  ┃ ☐ Study for exam                ┃  │
│  ┃                          [Delete]┃  │
│  ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛  │
│                                         │
└─────────────────────────────────────────┘
```

**Sample Output (First Task Completed):**
```
┌─────────────────────────────────────────┐
│                                         │
│  My Tasks                               │
│                                         │
│  ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓  │
│  ┃ ☑ Complete Compose worksheet    ┃  │
│  ┃   (strikethrough text)  [Delete]┃  │
│  ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛  │
│                                         │
│  ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓  │
│  ┃ ☐ Study for exam                ┃  │
│  ┃                          [Delete]┃  │
│  ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛  │
│                                         │
└─────────────────────────────────────────┘
```
