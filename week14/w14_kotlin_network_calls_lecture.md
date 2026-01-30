# Long Running Tasks and Web APIs in Android

## Table of Contents

1. [Learning Outcomes](#learning-outcomes)
2. [Introduction to Long Running Tasks](#introduction-to-long-running-tasks)
3. [Understanding the Main Thread](#understanding-the-main-thread)
4. [The Problem: Application Not Responding (ANR)](#the-problem-application-not-responding-anr)
5. [Threading Basics in Kotlin](#threading-basics-in-kotlin)
6. [Creating Threads with Runnable](#creating-threads-with-runnable)
7. [Returning to the Main Thread](#returning-to-the-main-thread)
8. [Introduction to Web APIs](#introduction-to-web-apis)
9. [RESTful APIs and HTTP Methods](#restful-apis-and-http-methods)
10. [Making Network Requests in Android](#making-network-requests-in-android)
11. [Working with JSON Responses](#working-with-json-responses)
12. [Type Casts and Type Checks in Kotlin](#type-casts-and-type-checks-in-kotlin)
13. [Complete Example: Fetching Data from a Web API](#complete-example-fetching-data-from-a-web-api)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain why long running tasks cannot execute on the main thread in Android applications
- Identify the consequences of blocking the UI thread and understand ANR errors
- Create and start background threads using Kotlin's Thread class
- Use `runOnUiThread()` and View's `post()` method to update the UI from background threads
- Describe what Web APIs are and understand RESTful architecture principles
- Make HTTP GET requests to public APIs using Java's URL and HttpURLConnection classes
- Parse JSON responses returned from web services
- Apply type casting and type checking in Kotlin using `as` and `is` operators
- Utilize Kotlin's smart cast feature for type-safe code
- Build a complete Android application that fetches and displays data from a web API

---

## Introduction to Long Running Tasks

Modern mobile applications need to perform operations that may take considerable time to complete. These operations fall into two main categories:

### Network Operations
Network requests involve communicating with remote servers over the internet. Examples include:
- Fetching data from web APIs
- Downloading images or files
- Sending data to a server
- Authenticating users with remote services

Network operations are unpredictable in duration because they depend on:
- Internet connection speed
- Server response time
- Network latency
- Data size being transferred

### Computationally Expensive Tasks
Some operations require significant processing power and time:
- Image processing and filtering
- Encrypting or decrypting data
- Sorting large datasets
- Complex mathematical calculations
- Video encoding/decoding

Both types of operations share a common characteristic: they take longer than a few milliseconds to complete. This creates a fundamental problem in Android development that we must address.

---

## Understanding the Main Thread

Every Android application runs on a **main thread**, also called the **UI thread**. This thread has a critical responsibility: it handles all user interface operations and user interactions.

### The Main Thread's Responsibilities

The main thread is responsible for:
- Drawing views and updating the screen (at 60 frames per second ideally)
- Processing user input (touch events, button clicks, gestures)
- Running Activity lifecycle methods (`onCreate()`, `onResume()`, etc.)
- Executing event handlers and callbacks

### Why the Main Thread Matters

```
+---------------------------+
|   Android Application     |
|                           |
|  +---------------------+  |
|  |    Main Thread      |  |
|  |   (UI Thread)       |  |
|  |                     |  |
|  |  - onCreate()       |  |
|  |  - Button clicks    |  |
|  |  - Screen drawing   |  |
|  |  - User input       |  |
|  +---------------------+  |
|                           |
+---------------------------+
```

The main thread operates in a loop, constantly checking for events and updating the UI. If we block this thread with a long-running operation, the entire application becomes unresponsive because:
- The screen stops updating
- Touch events are not processed
- Buttons don't respond to clicks
- Animations freeze

---

## The Problem: Application Not Responding (ANR)

When the main thread is blocked for too long, Android's system detects this and presents the user with an "Application Not Responding" (ANR) dialog.

### ANR Threshold
Android will trigger an ANR if:
- The main thread is blocked for **5 seconds** or more
- A BroadcastReceiver doesn't complete within **10 seconds**

### ANR Dialog Appearance

```
+--------------------------------+
|  📱 Android Device Screen      |
|                                |
|  +---------------------------+ |
|  |  BadRandomNumberGen...    | |
|  |  isn't responding         | |
|  |                           | |
|  |  ❌  Close app            | |
|  |                           | |
|  |  ⏱  Wait                  | |
|  +---------------------------+ |
|                                |
+--------------------------------+
```

This dialog gives users two options:
- **Close app**: Forcibly terminate the application
- **Wait**: Continue waiting (but the user experience is already ruined)

### Demonstrating the Problem

Let's look at code that would cause an ANR:

```kotlin
package com.example.badrandomnumber

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Find views
        val button: Button = findViewById(R.id.randomButton)
        val resultText: TextView = findViewById(R.id.resultText)
        
        button.setOnClickListener {
            // BAD PRACTICE: Long operation on main thread
            // This will cause an ANR!
            val randomNumber = slowRandomNumberGeneration()
            resultText.text = randomNumber.toString()
        }
    }
    
    // Simulates a slow operation (DO NOT DO THIS ON MAIN THREAD)
    private fun slowRandomNumberGeneration(): Int {
        // Sleep for 10 seconds to simulate slow computation
        Thread.sleep(10000)
        return (0..999999).random()
    }
}
```

**What happens when the button is clicked:**
1. The main thread starts executing `slowRandomNumberGeneration()`
2. The thread sleeps for 10 seconds
3. During these 10 seconds, no UI updates occur
4. The screen freezes completely
5. After 5 seconds, Android shows the ANR dialog
6. User experience is terrible

**Output:** The app becomes completely unresponsive, and after 5 seconds, the ANR dialog appears.

---

## Threading Basics in Kotlin

To avoid blocking the main thread, we execute long-running tasks on separate **background threads**. A thread is an independent path of execution within a program.

### Thread Concept

```
Main Thread (UI)          Background Thread
     |                          |
     |--- Start Thread -------->|
     |                          |
     |                          |-- Long operation
     |  (UI remains responsive) |   (network call)
     |                          |   (processing)
     |                          |
     |<--- Return result -------|
     |                          |
     |-- Update UI              |
```

### Creating a Thread with Runnable

In Kotlin, we can create threads using the `Thread` class combined with a `Runnable` object. A `Runnable` is an interface with a single method: `run()`.

**Syntax 1: Using an anonymous object**

```kotlin
val thread = Thread(object : Runnable {
    override fun run() {
        // Long running task logic here
        // This code executes on a background thread
    }
})
thread.start()  // Start the thread execution
```

**Syntax 2: Using Kotlin's lambda syntax (more concise)**

```kotlin
val thread = Thread {
    // Long running task logic here
    // This code executes on a background thread
}
thread.start()  // Start the thread execution
```

### Important Thread Methods

- **`start()`**: Begins thread execution. The JVM calls the `run()` method on a new thread.
- **`run()`**: Contains the code to be executed. Do not call this directly; use `start()` instead.
- **`sleep(milliseconds)`**: Pauses the current thread for the specified time (static method).
- **`join()`**: Waits for a thread to complete before continuing.

---

## Creating Threads with Runnable

Let's fix our previous ANR example by moving the slow operation to a background thread:

```kotlin
package com.example.goodrandomnumber

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val button: Button = findViewById(R.id.randomButton)
        val resultText: TextView = findViewById(R.id.resultText)
        
        button.setOnClickListener {
            // GOOD PRACTICE: Run long operation on background thread
            val thread = Thread {
                // This code runs on a background thread
                val randomNumber = slowRandomNumberGeneration()
                
                // We'll address updating the UI in the next section
                // For now, this just demonstrates background threading
            }
            thread.start()  // Start the background thread
            
            // The main thread continues here immediately
            // UI remains responsive!
        }
    }
    
    private fun slowRandomNumberGeneration(): Int {
        // Simulate slow computation
        Thread.sleep(10000)  // 10 seconds
        return (0..999999).random()
    }
}
```

**What happens now:**
1. User clicks the button
2. A new background thread is created and started
3. The main thread immediately returns to processing UI events
4. The app remains responsive while the background thread works
5. After 10 seconds, the background thread completes

**Problem:** We still need to update the UI with the result, but there's a critical rule in Android: **Only the main thread can update UI elements!**

---

## Returning to the Main Thread

Once a background thread completes its work, we often need to update the UI with the results. However, Android enforces a strict rule: **UI elements can only be modified from the main thread.**

Attempting to update UI from a background thread will cause a `CalledFromWrongThreadException`:

```kotlin
// DON'T DO THIS - Will crash!
val thread = Thread {
    val result = slowOperation()
    textView.text = result  // CRASH! Wrong thread!
}
thread.start()
```

Android provides two main mechanisms to safely return to the main thread:

### Method 1: Using `runOnUiThread()`

The `Activity` class provides the `runOnUiThread()` method that accepts a `Runnable`:

```kotlin
package com.example.threadingupdates

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val button: Button = findViewById(R.id.fetchButton)
        val statusText: TextView = findViewById(R.id.statusText)
        val resultText: TextView = findViewById(R.id.resultText)
        
        button.setOnClickListener {
            // Update UI to show we're loading
            statusText.text = "Fetching data..."
            resultText.text = ""
            
            // Start background thread
            Thread {
                // Background work
                val data = performNetworkRequest()
                
                // Return to main thread to update UI
                runOnUiThread(object : Runnable {
                    override fun run() {
                        // This code runs on the main thread
                        // Safe to update UI here
                        statusText.text = "Data loaded!"
                        resultText.text = data
                    }
                })
            }.start()
        }
    }
    
    // Simulates a network request (runs on background thread)
    private fun performNetworkRequest(): String {
        Thread.sleep(3000)  // Simulate 3 second delay
        return "Sample data from server"
    }
}
```

**Using Kotlin lambda syntax (more concise):**

```kotlin
button.setOnClickListener {
    statusText.text = "Loading..."
    
    Thread {
        val data = performNetworkRequest()
        
        // Kotlin lambda syntax for runOnUiThread
        runOnUiThread {
            statusText.text = "Complete!"
            resultText.text = data
        }
    }.start()
}
```

**Output flow:**
```
User clicks button
  -> statusText shows "Fetching data..."
  -> Background thread starts
  -> (3 seconds pass, UI remains responsive)
  -> runOnUiThread executes
  -> statusText shows "Data loaded!"
  -> resultText shows "Sample data from server"
```

### Method 2: Using View's `post()` Method

Every `View` has a `post()` method that schedules code to run on the main thread:

```kotlin
package com.example.viewpostmethod

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val button: Button = findViewById(R.id.calculateButton)
        val progressText: TextView = findViewById(R.id.progressText)
        val resultText: TextView = findViewById(R.id.resultText)
        
        button.setOnClickListener {
            // Background calculation
            Thread {
                // Simulate calculation steps
                for (i in 1..5) {
                    Thread.sleep(1000)  // 1 second per step
                    
                    // Update UI using View's post() method
                    progressText.post {
                        progressText.text = "Step $i of 5..."
                    }
                }
                
                // Final result
                val result = performComplexCalculation()
                
                // Update final result on main thread
                resultText.post {
                    resultText.text = "Result: $result"
                    progressText.text = "Complete!"
                }
            }.start()
        }
    }
    
    private fun performComplexCalculation(): Int {
        // Simulate complex calculation
        return (1..1000).sum()
    }
}
```

**Output progression:**
```
User clicks button
  -> progressText: "Step 1 of 5..."
  -> (1 second)
  -> progressText: "Step 2 of 5..."
  -> (1 second)
  -> progressText: "Step 3 of 5..."
  -> (1 second)
  -> progressText: "Step 4 of 5..."
  -> (1 second)
  -> progressText: "Step 5 of 5..."
  -> (1 second)
  -> resultText: "Result: 500500"
  -> progressText: "Complete!"
```

### Choosing Between `runOnUiThread()` and `post()`

- **`runOnUiThread()`**: Use when you need Activity context or are updating multiple views
- **`post()`**: Use when updating a specific view; more concise and doesn't require Activity reference

Both methods are thread-safe and ensure UI updates happen on the main thread.

---

## Introduction to Web APIs

A **Web API** (Application Programming Interface) is a set of rules and protocols that allows different software applications to communicate with each other over the internet.

### What is an API?

An API is like a waiter in a restaurant:
- **You (the client)** don't go into the kitchen yourself
- **The waiter (the API)** takes your order to the kitchen
- **The kitchen (the server)** prepares your food
- **The waiter** brings your food back to you

In software terms:
```
Android App  -->  API Request  -->  Web Server
            <--  API Response <--
```

### Types of APIs

**Public APIs (Open APIs)**
- Free to use, no authentication required
- Examples: 
  - OpenWeatherMap (basic tier)
  - JSONPlaceholder (testing API)
  - PokéAPI
  - REST Countries

**Private APIs (Authenticated)**
- Require API keys or authentication tokens
- Examples:
  - Google Maps API
  - Twitter API
  - Stripe Payment API

### Common Use Cases

Mobile apps use APIs to:
- Fetch weather data
- Get social media posts
- Retrieve news articles
- Access map information
- Process payments
- Authenticate users
- Store data in the cloud

---

## RESTful APIs and HTTP Methods

Most modern web APIs follow **REST** (REpresentational State Transfer) architectural principles.

### REST Principles

1. **Stateless**: Each request contains all information needed; server doesn't store client state
2. **Client-Server**: Clear separation between client and server
3. **Uniform Interface**: Standardized way of communicating
4. **Resource-Based**: Everything is a resource with a unique URL

### HTTP Methods (Verbs)

RESTful APIs use standard HTTP methods:

| Method | Purpose | Example |
|--------|---------|---------|
| **GET** | Retrieve data | Get user profile, fetch products |
| **POST** | Create new data | Create new user, submit form |
| **PUT** | Update existing data (full) | Update entire user profile |
| **PATCH** | Update existing data (partial) | Update just user email |
| **DELETE** | Remove data | Delete user account |

**In this lecture, we focus on GET requests** - the most common operation for mobile apps.

### Making a GET Request in a Browser

You can test APIs directly in your browser by typing the URL:

```
https://jsonplaceholder.typicode.com/posts/1
```

This retrieves post #1 from the JSONPlaceholder API.

### API Response Formats

APIs typically return data in structured formats:

**JSON (JavaScript Object Notation)** - Most common

```json
{
  "userId": 1,
  "id": 1,
  "title": "Sample Post",
  "body": "This is the post content"
}
```

**XML (eXtensible Markup Language)**

```xml
<post>
  <userId>1</userId>
  <id>1</id>
  <title>Sample Post</title>
  <body>This is the post content</body>
</post>
```

**YAML** - Less common for APIs

```yaml
userId: 1
id: 1
title: Sample Post
body: This is the post content
```

Modern APIs predominantly use JSON due to its:
- Simplicity and readability
- Lightweight format
- Native support in most programming languages
- Easy parsing

---

## Making Network Requests in Android

Android applications can make HTTP network requests using Java's built-in networking classes: `URL` and `HttpURLConnection`.

### Required Permission

First, add the internet permission to your `AndroidManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.networkrequest">
    
    <!-- Add this permission before the <application> tag -->
    <uses-permission android:name="android.permission.INTERNET" />
    
    <application
        ...>
        ...
    </application>
</manifest>
```

### Basic Network Request Structure

The general pattern for making a network request:

1. Create a `URL` object with the API endpoint
2. Open a connection using `openConnection()`
3. Cast to `HttpURLConnection` (we'll explain this shortly)
4. Set request properties (method, headers, etc.)
5. Make the request and check response code
6. Read the response data
7. Close the connection

### Simple Network Request Example

```kotlin
package com.example.basicnetwork

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import java.net.HttpURLConnection
import java.net.URL

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val fetchButton: Button = findViewById(R.id.fetchButton)
        val resultText: TextView = findViewById(R.id.resultText)
        val statusText: TextView = findViewById(R.id.statusText)
        
        fetchButton.setOnClickListener {
            statusText.text = "Fetching data..."
            resultText.text = ""
            
            // Network request MUST run on background thread
            Thread {
                try {
                    // Step 1: Create URL object
                    val url = URL("https://jsonplaceholder.typicode.com/posts/1")
                    
                    // Step 2 & 3: Open connection and cast to HttpURLConnection
                    val connection = url.openConnection() as HttpURLConnection
                    
                    // Step 4: Configure the connection
                    connection.requestMethod = "GET"
                    connection.connectTimeout = 10000  // 10 seconds
                    connection.readTimeout = 10000     // 10 seconds
                    
                    // Step 5: Make the request and check response code
                    val responseCode = connection.responseCode
                    
                    if (responseCode == HttpURLConnection.HTTP_OK) {
                        // Step 6: Read the response
                        val response = connection.inputStream.bufferedReader().use { it.readText() }
                        
                        // Update UI on main thread
                        runOnUiThread {
                            statusText.text = "Success! (Code: $responseCode)"
                            resultText.text = response
                        }
                    } else {
                        runOnUiThread {
                            statusText.text = "Error: HTTP $responseCode"
                        }
                    }
                    
                    // Step 7: Close connection
                    connection.disconnect()
                    
                } catch (e: Exception) {
                    // Handle errors (network issues, timeouts, etc.)
                    runOnUiThread {
                        statusText.text = "Error: ${e.message}"
                    }
                }
            }.start()
        }
    }
}
```

**Expected Output:**
```
Status: Success! (Code: 200)
Result:
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur ..."
}
```

### Understanding HTTP Response Codes

- **200 OK**: Request succeeded
- **404 Not Found**: Resource doesn't exist
- **500 Internal Server Error**: Server error
- **401 Unauthorized**: Authentication required
- **403 Forbidden**: Access denied

---

## Working with JSON Responses

Raw JSON strings aren't very useful. We need to **parse** them into Kotlin objects we can work with.

Android includes `org.json` classes for JSON parsing:
- `JSONObject`: Represents a JSON object `{ }`
- `JSONArray`: Represents a JSON array `[ ]`

### Parsing a JSON Object

```kotlin
package com.example.jsonparsing

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import org.json.JSONObject
import java.net.HttpURLConnection
import java.net.URL

// Data class to hold post information
data class Post(
    val userId: Int,
    val id: Int,
    val title: String,
    val body: String
)

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val fetchButton: Button = findViewById(R.id.fetchButton)
        val titleText: TextView = findViewById(R.id.titleText)
        val bodyText: TextView = findViewById(R.id.bodyText)
        val metaText: TextView = findViewById(R.id.metaText)
        
        fetchButton.setOnClickListener {
            Thread {
                try {
                    // Fetch JSON from API
                    val url = URL("https://jsonplaceholder.typicode.com/posts/1")
                    val connection = url.openConnection() as HttpURLConnection
                    connection.requestMethod = "GET"
                    
                    if (connection.responseCode == HttpURLConnection.HTTP_OK) {
                        val jsonString = connection.inputStream.bufferedReader().use { it.readText() }
                        
                        // Parse JSON string into JSONObject
                        val jsonObject = JSONObject(jsonString)
                        
                        // Extract individual fields
                        val userId = jsonObject.getInt("userId")
                        val id = jsonObject.getInt("id")
                        val title = jsonObject.getString("title")
                        val body = jsonObject.getString("body")
                        
                        // Create Post object
                        val post = Post(userId, id, title, body)
                        
                        // Update UI
                        runOnUiThread {
                            titleText.text = post.title
                            bodyText.text = post.body
                            metaText.text = "Post ID: ${post.id}, User ID: ${post.userId}"
                        }
                    }
                    
                    connection.disconnect()
                    
                } catch (e: Exception) {
                    runOnUiThread {
                        titleText.text = "Error: ${e.message}"
                    }
                }
            }.start()
        }
    }
}
```

**Expected Output:**
```
Title: sunt aut facere repellat provident occaecati excepturi optio reprehenderit
Body: quia et suscipit suscipit recusandae consequuntur expedita et cum reprehenderit molestiae ut ut quas totam nostrum rerum est autem sunt rem eveniet architecto
Meta: Post ID: 1, User ID: 1
```

### Parsing a JSON Array

Many APIs return arrays of objects. Here's how to parse them:

```kotlin
package com.example.jsonarray

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import org.json.JSONArray
import org.json.JSONObject
import java.net.HttpURLConnection
import java.net.URL

data class User(
    val id: Int,
    val name: String,
    val email: String,
    val username: String
)

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val fetchButton: Button = findViewById(R.id.fetchUsersButton)
        val resultText: TextView = findViewById(R.id.resultText)
        
        fetchButton.setOnClickListener {
            resultText.text = "Loading users..."
            
            Thread {
                try {
                    val url = URL("https://jsonplaceholder.typicode.com/users")
                    val connection = url.openConnection() as HttpURLConnection
                    connection.requestMethod = "GET"
                    
                    if (connection.responseCode == HttpURLConnection.HTTP_OK) {
                        val jsonString = connection.inputStream.bufferedReader().use { it.readText() }
                        
                        // Parse JSON string into JSONArray
                        val jsonArray = JSONArray(jsonString)
                        
                        // Create list to store users
                        val users = mutableListOf<User>()
                        
                        // Iterate through array
                        for (i in 0 until jsonArray.length()) {
                            // Get each JSON object from array
                            val jsonObject: JSONObject = jsonArray.getJSONObject(i)
                            
                            // Extract fields
                            val id = jsonObject.getInt("id")
                            val name = jsonObject.getString("name")
                            val email = jsonObject.getString("email")
                            val username = jsonObject.getString("username")
                            
                            // Create User object and add to list
                            users.add(User(id, name, email, username))
                        }
                        
                        // Build display string
                        val displayText = buildString {
                            appendLine("Found ${users.size} users:\n")
                            users.forEach { user ->
                                appendLine("${user.id}. ${user.name}")
                                appendLine("   @${user.username}")
                                appendLine("   ${user.email}")
                                appendLine()
                            }
                        }
                        
                        // Update UI
                        runOnUiThread {
                            resultText.text = displayText
                        }
                    }
                    
                    connection.disconnect()
                    
                } catch (e: Exception) {
                    runOnUiThread {
                        resultText.text = "Error: ${e.message}"
                    }
                }
            }.start()
        }
    }
}
```

**Expected Output:**
```
Found 10 users:

1. Leanne Graham
   @Bret
   Sincere@april.biz

2. Ervin Howell
   @Antonette
   Shanna@melissa.tv

3. Clementine Bauch
   @Samantha
   Nathan@yesenia.net

... (continues for all 10 users)
```

---

## Type Casts and Type Checks in Kotlin

When we call `url.openConnection()`, it returns a `URLConnection` object. However, for HTTP URLs, we know it will actually be an `HttpURLConnection` object (which is a subclass of `URLConnection`).

### The Class Hierarchy

```
URLConnection (parent class)
      |
      +-- HttpURLConnection (subclass)
      |
      +-- JarURLConnection (subclass)
```

According to the Android documentation:

> "If for the URL's protocol (such as HTTP or JAR), there exists a public, specialized URLConnection subclass belonging to one of the following packages or one of their subpackages: java.lang, java.io, java.util, java.net, the connection returned will be of that subclass. For example, for HTTP an HttpURLConnection will be returned..."

This means for HTTP/HTTPS URLs, `openConnection()` returns an `HttpURLConnection`, but the method signature declares it returns the more general `URLConnection` type.

### Type Casting with `as`

To access `HttpURLConnection`-specific methods, we need to **cast** the result:

```kotlin
import java.net.HttpURLConnection
import java.net.URL

// Example 1: Basic type cast
val url = URL("https://api.example.com/data")
val connection = url.openConnection() as HttpURLConnection

// Now we can use HttpURLConnection methods
connection.requestMethod = "GET"
connection.setRequestProperty("Content-Type", "application/json")
val responseCode = connection.responseCode  // HttpURLConnection-specific method
```

**What the `as` operator does:**
- Tells Kotlin: "I know this is actually an HttpURLConnection, treat it as such"
- If the cast is invalid (object isn't actually of that type), throws a `ClassCastException`

### Type Checking with `is`

The `is` operator checks if an object is of a specific type (like `instanceof` in Java):

```kotlin
import java.net.HttpURLConnection
import java.net.URL
import java.net.URLConnection

fun demonstrateTypeCheck() {
    val url = URL("https://api.example.com/data")
    val connection: URLConnection = url.openConnection()
    
    // Check if connection is actually an HttpURLConnection
    if (connection is HttpURLConnection) {
        // Inside this block, Kotlin automatically casts connection
        // This is called "Smart Cast"
        println("Response code: ${connection.responseCode}")
        connection.requestMethod = "GET"
    } else {
        println("Not an HTTP connection")
    }
}
```

### Smart Casting

After an `is` check, Kotlin **automatically casts** the variable for you within that scope. This is called **smart casting**:

```kotlin
fun processConnection(connection: URLConnection) {
    // connection is of type URLConnection here
    
    if (connection is HttpURLConnection) {
        // connection is automatically smart cast to HttpURLConnection here!
        // No explicit cast needed
        connection.requestMethod = "POST"
        connection.doOutput = true
        
        // Access HttpURLConnection-specific properties
        println("Response: ${connection.responseCode}")
    }
    
    // Outside the if block, connection is URLConnection again
}
```

### Complete Example: Type Checking and Smart Casting

```kotlin
package com.example.typecasting

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import java.net.HttpURLConnection
import java.net.URL
import java.net.URLConnection

class MainActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val testButton: Button = findViewById(R.id.testButton)
        val resultText: TextView = findViewById(R.id.resultText)
        
        testButton.setOnClickListener {
            Thread {
                val result = fetchWithTypeCheck("https://jsonplaceholder.typicode.com/posts/1")
                runOnUiThread {
                    resultText.text = result
                }
            }.start()
        }
    }
    
    // Demonstrates type checking and smart casting
    private fun fetchWithTypeCheck(urlString: String): String {
        return try {
            val url = URL(urlString)
            val connection: URLConnection = url.openConnection()
            
            // Use type check instead of direct cast
            if (connection is HttpURLConnection) {
                // Smart cast: connection is now HttpURLConnection
                // No explicit cast needed!
                
                connection.requestMethod = "GET"
                connection.connectTimeout = 10000
                
                // Access HttpURLConnection-specific method
                val responseCode = connection.responseCode
                
                if (responseCode == HttpURLConnection.HTTP_OK) {
                    val response = connection.inputStream.bufferedReader().use { it.readText() }
                    connection.disconnect()
                    "Success! Received ${response.length} characters"
                } else {
                    connection.disconnect()
                    "Error: HTTP $responseCode"
                }
            } else {
                "Not an HTTP connection"
            }
            
        } catch (e: Exception) {
            "Error: ${e.message}"
        }
    }
}
```

**Expected Output:**
```
Success! Received 292 characters
```

### When to Use Each Approach

**Use `as` (type cast) when:**
- You are certain of the type
- You want the code to fail fast if the type is wrong
- More concise for simple cases

```kotlin
val connection = url.openConnection() as HttpURLConnection
```

**Use `is` (type check) when:**
- You're not 100% certain of the type
- You want to handle different types differently
- You want safer code that won't crash on wrong types

```kotlin
if (connection is HttpURLConnection) {
    // Safe to use as HttpURLConnection
}
```

### Safe Cast with `as?`

Kotlin also provides a **safe cast operator** `as?` that returns `null` instead of throwing an exception if the cast fails:

```kotlin
val connection = url.openConnection() as? HttpURLConnection

if (connection != null) {
    // Successfully cast
    connection.requestMethod = "GET"
} else {
    // Cast failed, connection is null
    println("Not an HttpURLConnection")
}
```

---


## Summary

In this lecture, we've covered the essential concepts for handling long-running tasks and making network requests in Android applications:

**Threading Concepts:**
- Android applications run on a main/UI thread that must remain responsive
- Long-running operations cause ANR (Application Not Responding) errors
- Background threads prevent blocking the UI thread
- Only the main thread can update UI elements

**Thread Communication:**
- `runOnUiThread()` schedules code execution on the main thread
- View's `post()` method provides an alternative way to update UI
- Both methods ensure thread-safe UI updates

**Web APIs:**
- APIs provide programmatic access to web services
- RESTful APIs use standard HTTP methods (GET, POST, PUT, DELETE)
- JSON is the most common data format for modern APIs
- Network requests require the INTERNET permission

**Making Network Requests:**
- Use `URL` and `HttpURLConnection` classes for HTTP requests
- Always execute network operations on background threads
- Check HTTP response codes to handle success and errors
- Parse JSON responses using `JSONObject` and `JSONArray`

**Kotlin Type System:**
- `as` operator performs type casting
- `is` operator checks types and enables smart casting
- Smart casting automatically casts variables after type checks
- `as?` provides safe casting that returns null on failure

These concepts form the foundation for building Android applications that interact with web services and maintain responsive user interfaces. As you continue developing Android apps, you'll use these patterns frequently for network communication, data synchronization, and real-time updates.

---
