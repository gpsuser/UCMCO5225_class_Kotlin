# Worksheet

## Quiz Section

### Question 1: Fill in the Blanks

Complete the following paragraph by filling in the blanks with words from the word bank below:

"Android Studio uses the __________ build system to automate the process of transforming source code into a deployable Android application package (APK). This process includes resource __________, code __________, and digital __________. The build system handles all these complex steps automatically, so developers can focus on writing code."

**Word Bank:** `compilation`, `signing`, `Gradle`, `packaging`, `Maven`, `optimization`, `testing`, `deployment`

---

### Question 2: Fill in the Blanks

Complete the following paragraph about View Binding by filling in the blanks:

"View Binding generates a __________ class for each XML layout file, providing type-safe and __________-safe access to all views with IDs. The binding class name follows a naming convention: `activity_main.xml` becomes __________. To use View Binding, you must first enable it in your module-level __________ file."

**Word Bank:** `ActivityMainBinding`, `null`, `gradle`, `binding`, `MainActivityBinding`, `manifest`, `type`, `build`

---

### Question 3: Fill in the Blanks

Complete the following paragraph about string resources:

"String resources should be stored in the __________ file located in the `res/values/` folder. This approach enables easy __________ by creating additional folders like `values-fr/` for French or `values-es/` for Spanish. In Kotlin code, you access string resources using __________, and in XML layouts, you reference them using the __________ syntax."

**Word Bank:** `@string/resource_name`, `strings.xml`, `internationalization`, `getString()`, `R.string.resource_name`, `localization`, `@resource/string_name`, `resources.xml`

---

### Question 4: Multiple Choice

What is the primary reason for keeping the Android Emulator running once it has started?

A) To save battery life on your development machine  
B) The emulator boot process takes several minutes and consumes significant resources  
C) Android Studio requires a constant connection to the emulator  
D) To prevent memory leaks in your application  

---

### Question 5: Multiple Choice

When handling version-specific Android features, which approach is correct?

A) Always use the latest Android APIs and ignore older devices  
B) Use `if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TARGET_VERSION)` to conditionally execute version-specific code  
C) Create separate apps for each Android version  
D) Disable features that aren't available on older Android versions  

---

### Question 6: Multiple Choice

Which approach to handling button clicks is recommended as the most modern and concise in Kotlin?

A) Implementing `View.OnClickListener` in the Activity class  
B) Creating anonymous objects that implement `OnClickListener`  
C) Using lambda expressions with `setOnClickListener`  
D) Using the `android:onClick` XML attribute  

---

## Task Section

### Task 1: Temperature Converter with View Binding

Create an Android Activity that converts temperatures between Celsius and Fahrenheit. The app should have two input fields, two buttons, and a result TextView.

**Requirements:**
- Use View Binding to access UI elements
- One button converts Celsius to Fahrenheit
- One button converts Fahrenheit to Celsius
- Display the result in a TextView
- Handle empty input gracefully (show an error message)

**Code Scaffolding:**

```kotlin
package com.example.temperatureconverter

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.temperatureconverter.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupConverters()
    }
    
    private fun setupConverters() {
        // TODO: Set up click listener for Celsius to Fahrenheit button
        binding.celsiusToFahrenheitButton.setOnClickListener {
            // TODO: Get Celsius value from input
            // TODO: Convert to Fahrenheit using formula: F = (C * 9/5) + 32
            // TODO: Display result
        }
        
        // TODO: Set up click listener for Fahrenheit to Celsius button
        
    }
    
    private fun convertCelsiusToFahrenheit(celsius: Double): Double {
        // TODO: Implement conversion formula
        return 0.0
    }
    
    private fun convertFahrenheitToCelsius(fahrenheit: Double): Double {
        // TODO: Implement conversion formula
        return 0.0
    }
}
```

**XML Layout (activity_main.xml):**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Temperature Converter"
        android:textSize="24sp"
        android:layout_marginBottom="24dp" />

    <EditText
        android:id="@+id/celsiusInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Celsius"
        android:inputType="numberDecimal|numberSigned"
        android:layout_marginBottom="8dp" />

    <Button
        android:id="@+id/celsiusToFahrenheitButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Convert C to F"
        android:layout_marginBottom="16dp" />

    <EditText
        android:id="@+id/fahrenheitInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Fahrenheit"
        android:inputType="numberDecimal|numberSigned"
        android:layout_marginBottom="8dp" />

    <Button
        android:id="@+id/fahrenheitToCelsiusButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Convert F to C"
        android:layout_marginBottom="24dp" />

    <TextView
        android:id="@+id/resultText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Result: "
        android:textSize="18sp" />

</LinearLayout>
```

**Sample Input/Output:**

```
Input (Celsius): 100
Click "Convert C to F"
Output: Result: 100.0°C = 212.0°F

Input (Fahrenheit): 32
Click "Convert F to C"
Output: Result: 32.0°F = 0.0°C

Input (Celsius): -40
Click "Convert C to F"
Output: Result: -40.0°C = -40.0°F
```

---

### Task 2: Multi-Language Greeting App

Create an Android app that displays greetings in different languages. The app should have three buttons (English, French, Spanish) and display the appropriate greeting when each button is clicked. Use string resources with proper internationalization support.

**Requirements:**
- Create string resources for three languages
- Use View Binding to access UI elements
- Each button displays a greeting in the corresponding language
- Show a timestamp of when the greeting was displayed

**Code Scaffolding:**

```kotlin
package com.example.greetingapp

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.greetingapp.databinding.ActivityMainBinding
import java.text.SimpleDateFormat
import java.util.*

class MainActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupGreetingButtons()
    }
    
    private fun setupGreetingButtons() {
        // TODO: Set up click listener for English button
        binding.englishButton.setOnClickListener {
            // TODO: Display English greeting from string resources
            // TODO: Add timestamp
        }
        
        // TODO: Set up click listener for French button
        
        // TODO: Set up click listener for Spanish button
        
    }
    
    private fun displayGreeting(greeting: String, language: String) {
        // TODO: Format and display the greeting with timestamp
        val timestamp = getCurrentTimestamp()
        // TODO: Update the greetingText TextView
    }
    
    private fun getCurrentTimestamp(): String {
        val sdf = SimpleDateFormat("HH:mm:ss", Locale.getDefault())
        return sdf.format(Date())
    }
}
```

**XML Layout (activity_main.xml):**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    android:gravity="center_horizontal">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/app_title"
        android:textSize="24sp"
        android:layout_marginBottom="32dp" />

    <Button
        android:id="@+id/englishButton"
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:text="English"
        android:layout_marginBottom="8dp" />

    <Button
        android:id="@+id/frenchButton"
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:text="Français"
        android:layout_marginBottom="8dp" />

    <Button
        android:id="@+id/spanishButton"
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:text="Español"
        android:layout_marginBottom="32dp" />

    <TextView
        android:id="@+id/greetingText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/select_language"
        android:textSize="20sp"
        android:textAlignment="center"
        android:layout_marginTop="24dp" />

</LinearLayout>
```

**String Resources (res/values/strings.xml):**

```xml
<resources>
    <string name="app_name">Greeting App</string>
    <string name="app_title">Multi-Language Greetings</string>
    <string name="select_language">Select a language</string>
    <string name="greeting_english">Hello! Welcome!</string>
    <string name="greeting_french">Bonjour! Bienvenue!</string>
    <string name="greeting_spanish">¡Hola! ¡Bienvenido!</string>
</resources>
```

**Sample Input/Output:**

```
Click "English" button
Output: Hello! Welcome!
        (English - 14:23:45)

Click "Français" button
Output: Bonjour! Bienvenue!
        (French - 14:23:52)

Click "Español" button
Output: ¡Hola! ¡Bienvenido!
        (Spanish - 14:24:01)
```

---

*End of Worksheet*