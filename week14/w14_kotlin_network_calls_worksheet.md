# Worksheet

## Quiz Section

### Question 1: Fill in the Blanks

Complete the following paragraph by filling in the blanks with the appropriate terms from the word bank below.

"Every Android application runs on a __________ thread, also called the __________ thread. This thread is responsible for drawing views and updating the screen, processing user input, and running Activity lifecycle methods. If this thread is blocked for __________ seconds or more, Android will trigger an __________ error."

**Word Bank:** UI, ANR, main, 5, background, 10, crash

---

### Question 2: Fill in the Blanks

Complete the following paragraph about returning to the main thread.

"When a background thread completes its work and needs to update UI elements, it must return to the __________. Android provides two main mechanisms for this: the Activity's __________ method and any View's __________ method. Attempting to update UI from a background thread will cause a __________."

**Word Bank:** main thread, post(), CalledFromWrongThreadException, runOnUiThread(), background thread, start(), NetworkOnMainThreadException

---

### Question 3: Fill in the Blanks

Complete the following paragraph about RESTful APIs and HTTP.

"RESTful APIs use standard HTTP methods to perform operations. The __________ method is used to retrieve data from a server, while __________ is used to create new data. When a request succeeds, the server typically returns a __________ response code. APIs commonly return data in __________ format, which is lightweight and easy to parse."

**Word Bank:** GET, POST, 200, 404, JSON, XML, DELETE, PUT

---

### Question 4: Multiple Choice

What is the primary reason that network operations must run on a background thread in Android?

A) To save battery power  
B) To prevent blocking the UI thread and causing ANR errors  
C) To make the network request faster  
D) To comply with Google Play Store requirements  

---

### Question 5: Multiple Choice

Which of the following code snippets correctly demonstrates creating and starting a background thread in Kotlin?

A) 
```kotlin
Thread {
    // Background work here
}.run()
```

B)
```kotlin
val thread = Thread {
    // Background work here
}
thread.start()
```

C)
```kotlin
runOnUiThread {
    // Background work here
}
```

D)
```kotlin
Thread.sleep(1000) {
    // Background work here
}
```

---

### Question 6: Multiple Choice

Consider the following code:

```kotlin
val url = URL("https://api.example.com/data")
val connection = url.openConnection() as HttpURLConnection
```

What does the `as` keyword do in this context?

A) It checks if the connection is an HttpURLConnection and returns null if it isn't  
B) It converts the URLConnection to an HttpURLConnection, throwing an exception if the cast fails  
C) It creates a new HttpURLConnection object  
D) It automatically handles the network request  

---

## Task Section

### Task 1: Temperature Converter Service

Create a simple Android application that simulates fetching temperature conversion data from a service. The app should convert Celsius to Fahrenheit on a background thread and update the UI safely.

**Requirements:**
- User enters a temperature in Celsius
- When the "Convert" button is clicked, start a background thread
- Simulate a network delay of 2 seconds using `Thread.sleep(2000)`
- Calculate: Fahrenheit = (Celsius × 9/5) + 32
- Update the UI with the result using `runOnUiThread()`

**Scaffolding Code:**

```kotlin
package com.example.tempconverter

import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    
    private lateinit var celsiusInput: EditText
    private lateinit var convertButton: Button
    private lateinit var statusText: TextView
    private lateinit var resultText: TextView
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        celsiusInput = findViewById(R.id.celsiusInput)
        convertButton = findViewById(R.id.convertButton)
        statusText = findViewById(R.id.statusText)
        resultText = findViewById(R.id.resultText)
        
        convertButton.setOnClickListener {
            val celsiusStr = celsiusInput.text.toString()
            
            if (celsiusStr.isEmpty()) {
                statusText.text = "Please enter a temperature"
                return@setOnClickListener
            }
            
            val celsius = celsiusStr.toDouble()
            
            // TODO: Create and start a background thread
            // TODO: Inside the thread:
            //   - Update statusText to "Converting..." (use runOnUiThread)
            //   - Sleep for 2 seconds to simulate network delay
            //   - Calculate Fahrenheit
            //   - Update resultText with the result (use runOnUiThread)
            //   - Update statusText to "Conversion complete!" (use runOnUiThread)
        }
    }
}
```

**Sample Input/Output:**

```
Input: 0
Output: "0.0°C = 32.0°F"

Input: 100
Output: "100.0°C = 212.0°F"

Input: 25
Output: "25.0°C = 77.0°F"
```

---

### Task 2: JSON User Parser

Create an application that parses a JSON string containing user information and displays it in the UI. This task focuses on JSON parsing without making actual network requests.

**Requirements:**
- Parse the provided JSON string containing user data
- Extract: id, name, email, and phone number
- Display the information in separate TextViews
- Use a background thread (even though parsing is fast, this practices the pattern)
- Update UI using `post()` method on a View

**Scaffolding Code:**

```kotlin
package com.example.jsonparser

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import org.json.JSONObject

data class User(
    val id: Int,
    val name: String,
    val email: String,
    val phone: String
)

class MainActivity : AppCompatActivity() {
    
    private lateinit var parseButton: Button
    private lateinit var nameText: TextView
    private lateinit var emailText: TextView
    private lateinit var phoneText: TextView
    private lateinit var idText: TextView
    
    // Sample JSON data
    private val jsonString = """
        {
            "id": 42,
            "name": "Alice Johnson",
            "email": "alice.johnson@example.com",
            "phone": "+44 20 7946 0958",
            "address": {
                "street": "123 Tech Street",
                "city": "London"
            }
        }
    """.trimIndent()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        parseButton = findViewById(R.id.parseButton)
        nameText = findViewById(R.id.nameText)
        emailText = findViewById(R.id.emailText)
        phoneText = findViewById(R.id.phoneText)
        idText = findViewById(R.id.idText)
        
        parseButton.setOnClickListener {
            // TODO: Create and start a background thread
            Thread {
                // TODO: Parse the JSON string into a JSONObject
                // TODO: Extract id, name, email, and phone fields
                // TODO: Create a User object with the extracted data
                // TODO: Use nameText.post {} to update all TextViews on the main thread
                //       Format: "Name: Alice Johnson"
                //               "Email: alice.johnson@example.com"
                //               "Phone: +44 20 7946 0958"
                //               "User ID: 42"
            }.start()
        }
    }
}
```

**Sample Output:**

```
Name: Alice Johnson
Email: alice.johnson@example.com
Phone: +44 20 7946 0958
User ID: 42
```

---

### Challenge Task: News Article Fetcher

Create a more complex application that fetches multiple news articles from a JSON array, parses them, and displays them in a formatted list. This task combines threading, type checking, and JSON array parsing.

**Requirements:**
- Parse a JSON array containing multiple article objects
- Each article has: id, title, author, publishedDate, and content
- Display all articles in a single TextView with proper formatting
- Use background threading for parsing
- Implement proper error handling with try-catch
- Use type checking (`is`) to verify the JSON structure before parsing

**Scaffolding Code:**

```kotlin
package com.example.newsreader

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import org.json.JSONArray
import org.json.JSONObject

data class Article(
    val id: Int,
    val title: String,
    val author: String,
    val publishedDate: String,
    val content: String
)

class MainActivity : AppCompatActivity() {
    
    private lateinit var fetchButton: Button
    private lateinit var articlesText: TextView
    private lateinit var statusText: TextView
    
    // Sample JSON data representing news articles
    private val jsonArrayString = """
        [
            {
                "id": 1,
                "title": "Kotlin Becomes Top Mobile Language",
                "author": "Sarah Chen",
                "publishedDate": "2025-01-28",
                "content": "Kotlin has officially become the most popular language for mobile development..."
            },
            {
                "id": 2,
                "title": "New Android Features Announced",
                "author": "Michael Brown",
                "publishedDate": "2025-01-29",
                "content": "Google has announced exciting new features for Android developers..."
            },
            {
                "id": 3,
                "title": "Thread Safety in Mobile Apps",
                "author": "Emma Wilson",
                "publishedDate": "2025-01-30",
                "content": "Understanding thread safety is crucial for building robust mobile applications..."
            }
        ]
    """.trimIndent()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        fetchButton = findViewById(R.id.fetchButton)
        articlesText = findViewById(R.id.articlesText)
        statusText = findViewById(R.id.statusText)
        
        fetchButton.setOnClickListener {
            statusText.text = "Loading articles..."
            articlesText.text = ""
            fetchButton.isEnabled = false
            
            Thread {
                try {
                    // TODO: Parse jsonArrayString into a JSONArray
                    // TODO: Create a mutableListOf<Article>() to store parsed articles
                    // TODO: Iterate through the JSONArray (0 until jsonArray.length())
                    // TODO: For each element:
                    //       - Get the JSONObject at index i
                    //       - Verify it's a JSONObject using 'is' operator (for practice)
                    //       - Extract all fields (id, title, author, publishedDate, content)
                    //       - Create an Article object and add to the list
                    
                    // TODO: Build a formatted string to display all articles
                    // Format should be:
                    // =====================================
                    // Article 1
                    // =====================================
                    // Title: [title]
                    // Author: [author]
                    // Date: [date]
                    // 
                    // [content preview - first 80 characters]...
                    //
                    // (repeat for each article)
                    
                    // TODO: Update the UI using runOnUiThread
                    //       - Set articlesText.text to the formatted string
                    //       - Set statusText.text to "Loaded X articles"
                    //       - Re-enable the fetchButton
                    
                } catch (e: Exception) {
                    // TODO: Handle errors - update statusText with error message
                    //       and re-enable the button
                }
            }.start()
        }
    }
    
    // Helper function you may want to use
    private fun formatArticle(article: Article, index: Int): String {
        // TODO: Implement this helper to format a single article
        // Return a formatted string for one article
        return ""
    }
}
```

**Sample Output:**

```
Status: Loaded 3 articles

Articles Display:
=====================================
Article 1
=====================================
Title: Kotlin Becomes Top Mobile Language
Author: Sarah Chen
Date: 2025-01-28

Kotlin has officially become the most popular language for mobile development...

=====================================
Article 2
=====================================
Title: New Android Features Announced
Author: Michael Brown
Date: 2025-01-29

Google has announced exciting new features for Android developers...

=====================================
Article 3
=====================================
Title: Thread Safety in Mobile Apps
Author: Emma Wilson
Date: 2025-01-30

Understanding thread safety is crucial for building robust mobile applications...
```

**Hints for Challenge Task:**
- Use `buildString { }` to construct the formatted output
- Remember to use `appendLine()` for line breaks
- The content preview should be: `content.take(80) + "..."`
- Don't forget to re-enable the button in both the success and error cases
