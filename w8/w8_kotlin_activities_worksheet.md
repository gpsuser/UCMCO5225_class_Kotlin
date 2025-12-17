# Worksheet

## Quiz Section

### Question 1

Complete the following paragraph by filling in the blanks using words from the word bank below:

An Activity represents a single, focused thing that the user can do. When you open an email app, the inbox is one _______, composing a new email is another _______, and reading an email is yet another _______. Each Activity is _______ but can start other Activities. Activities typically occupy the _______ screen, though they can also be floating windows.

**Word Bank:** independent, full, Activity, Activity, Activity

---

### Question 2

Fill in the blanks to complete the statements about measurement units in Android:

For text sizes, you should always use _______ units because they scale with the user's font size preferences in system settings. For everything else like margins, padding, and dimensions, you should use _______ units to ensure consistent sizing across different screen densities. The minimum touch target size for accessibility should be _______ to ensure buttons are easy to tap.

**Word Bank:** 48dp, sp, dp

---

### Question 3

Complete the following statements about passing data between Activities:

To pass data from one Activity to another, you add data to an Intent using the _______ method with key-value pairs. To retrieve a String value in the receiving Activity, you use the _______ method. When you need to get a result back from an Activity, you must register an _______ before onCreate() is called.

**Word Bank:** Activity Result Launcher, getStringExtra(), putExtra()

---

### Question 4

Which sequence correctly shows the Activity lifecycle methods called when an Activity starts for the first time?

A) onCreate() → onResume() → onStart()  
B) onStart() → onCreate() → onResume()  
C) onCreate() → onStart() → onResume()  
D) onResume() → onCreate() → onStart()

---

### Question 5

Which lifecycle method should you use to save important data that needs to survive configuration changes like screen rotation?

A) onPause()  
B) onStop()  
C) onSaveInstanceState()  
D) onDestroy()

---

### Question 6

Which of the following statements about ConstraintLayout is TRUE?

A) ConstraintLayout is deprecated and should not be used in modern Android apps  
B) ConstraintLayout requires deep view hierarchy nesting to work properly  
C) ConstraintLayout allows you to create complex layouts with a flat view hierarchy  
D) ConstraintLayout can only be used with programmatically created views, not XML

---

## Task Section

### Task 1: Activity State Counter

Create an Activity called `StateCounterActivity` that demonstrates the Activity lifecycle by counting how many times specific lifecycle methods are called.

**Requirements:**
- Track and display counts for `onCreate()`, `onStart()`, `onResume()`, `onPause()`, and `onStop()`
- Display all counts in a TextView
- Ensure counts survive screen rotation using `onSaveInstanceState()`

**Code Scaffolding:**

```kotlin
package com.example.statecounter

import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class StateCounterActivity : AppCompatActivity() {
    
    private lateinit var counterTextView: TextView
    
    // TODO: Declare counter variables for each lifecycle method
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_state_counter)
        
        counterTextView = findViewById(R.id.counterTextView)
        
        // TODO: Restore saved counts from savedInstanceState if it exists
        
        // TODO: Increment onCreate counter
        
        updateDisplay()
    }
    
    // TODO: Override onStart(), onResume(), onPause(), and onStop()
    // In each method: call super, increment the appropriate counter, and update display
    
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        // TODO: Save all counter values to outState
    }
    
    private fun updateDisplay() {
        // TODO: Update counterTextView to show all counts
        // Format: "onCreate: X\nonStart: Y\nonResume: Z\n..."
    }
}
```

**Sample Output:**

Initial display:
```
onCreate: 1
onStart: 1
onResume: 1
onPause: 0
onStop: 0
```

After rotating device:
```
onCreate: 2
onStart: 2
onResume: 2
onPause: 1
onStop: 1
```

---

### Task 2: Temperature Converter

Create two Activities: `InputActivity` and `ResultActivity`. The InputActivity should accept a temperature value and unit selection, then pass this data to ResultActivity which performs the conversion and returns the converted value.

**Requirements:**
- `InputActivity` has an EditText for temperature input and two buttons: "Convert to Celsius" and "Convert to Fahrenheit"
- Pass the temperature value and target unit to `ResultActivity`
- `ResultActivity` performs the conversion, displays the result, and returns the converted value when the user taps "Return"
- Display the returned value in `InputActivity`

**Conversion formulas:**
- Fahrenheit to Celsius: (F - 32) × 5/9
- Celsius to Fahrenheit: (C × 9/5) + 32

**Code Scaffolding:**

```kotlin
package com.example.tempconverter

import android.app.Activity
import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity

class InputActivity : AppCompatActivity() {
    
    private lateinit var temperatureInput: EditText
    private lateinit var resultDisplay: TextView
    
    // TODO: Register Activity Result Launcher to receive converted value
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_input)
        
        temperatureInput = findViewById(R.id.temperatureInput)
        resultDisplay = findViewById(R.id.resultDisplay)
        val toCelsiusButton: Button = findViewById(R.id.toCelsiusButton)
        val toFahrenheitButton: Button = findViewById(R.id.toFahrenheitButton)
        
        toCelsiusButton.setOnClickListener {
            // TODO: Get temperature value, create Intent with data, launch ResultActivity
        }
        
        toFahrenheitButton.setOnClickListener {
            // TODO: Get temperature value, create Intent with data, launch ResultActivity
        }
    }
}

class ResultActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_result)
        
        val resultText: TextView = findViewById(R.id.resultText)
        val returnButton: Button = findViewById(R.id.returnButton)
        
        // TODO: Get temperature and target unit from Intent extras
        
        // TODO: Perform conversion based on target unit
        
        // TODO: Display result in resultText
        
        returnButton.setOnClickListener {
            // TODO: Create result Intent with converted value, set result, and finish
        }
    }
}
```

**Sample Input/Output:**

Input in InputActivity:
```
Temperature: 100
[Tap "Convert to Celsius"]
```

Display in ResultActivity:
```
100.0°F = 37.8°C
[Return button]
```

Display back in InputActivity:
```
Converted value: 37.8
```