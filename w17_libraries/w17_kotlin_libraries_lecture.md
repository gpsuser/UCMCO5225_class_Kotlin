# Kotlin for Android: Using Libraries

---

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [1. What Are Libraries?](#1-what-are-libraries)
- [2. Adding a Library to Your Project](#2-adding-a-library-to-your-project)
- [3. Risks of Using Third-Party Libraries](#3-risks-of-using-third-party-libraries)
- [4. Networking Libraries: Volley and Retrofit](#4-networking-libraries-volley-and-retrofit)
- [5. Image Loading Libraries: Picasso and Glide](#5-image-loading-libraries-picasso-and-glide)
- [6. Data Handling Libraries: Gson](#6-data-handling-libraries-gson)
- [7. Other Useful Libraries: Parceler and IcePick](#7-other-useful-libraries-parceler-and-icepick)
- [8. Demo: Simplifying Network Requests with Volley](#8-demo-simplifying-network-requests-with-volley)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain what a library is and why we use them in Android development
- Describe how to add a library dependency to an Android project's `build.gradle` file
- Identify the key libraries used in Android development for networking, image loading, and data handling
- Explain the risks associated with using third-party libraries
- Write Kotlin code that uses Volley to make HTTP network requests
- Use Gson to parse JSON data returned from a network call

---

## 1. What Are Libraries?

[↑ Back to Table of Contents](#table-of-contents)

When you build an Android app, you don't have to write every single piece of functionality from scratch. **Libraries** are collections of pre-written, tested code that you can add to your project to handle common tasks — saving you time and reducing the chance of bugs in your own code.

Think of a library like a toolbox. Instead of forging your own hammer and screwdriver, you pick them up from a toolbox maintained by experienced engineers and focus on building the thing you actually care about.

### Why Use Libraries?

- They solve common problems (networking, image loading, data parsing) in a well-tested, optimised way
- They reduce the amount of boilerplate code you need to write
- They are often maintained by large communities or organisations, meaning bugs get fixed quickly
- They allow you to ship features faster

### Common Use Cases in Android

Libraries in Android development typically cover areas such as:

- **Networking** — Making HTTP requests, handling responses
- **Image Loading** — Downloading and displaying images from URLs efficiently
- **Data Serialisation** — Converting JSON or XML into Kotlin objects and vice versa
- **State Management** — Saving and restoring UI state across configuration changes

---

## 2. Adding a Library to Your Project

[↑ Back to Table of Contents](#table-of-contents)

Adding a library to an Android project is a straightforward process managed through **Gradle**, Android's build system.

### Step-by-Step Process

1. **Find the library** — Most libraries are hosted on [Maven Central](https://search.maven.org/) or [JitPack](https://jitpack.io/). The library's documentation will tell you the exact dependency string to use.
2. **Add a dependency** to your app's `build.gradle` file (the one inside the `app/` folder, not the top-level one)
3. **Sync the project** — Android Studio will prompt you to sync; this downloads the library
4. **Write code** using the library's API

### Example: Adding a Dependency

```kotlin
// In your app/build.gradle (or build.gradle.kts) file:
dependencies {
    implementation("com.android.volley:volley:1.2.1")
}
```

After adding this line and syncing the project, Volley's classes become available throughout your app's code.

> **Note:** In modern Android projects using Kotlin DSL (`build.gradle.kts`), the syntax uses double quotes and function-call style. In older Groovy-based `build.gradle` files, you may see single quotes instead. Both mean the same thing.

### What Happens During a Sync?

When you sync, Gradle:

- Downloads the library (and any libraries **it** depends on — these are called *transitive dependencies*)
- Makes the library's classes available to your code at compile time and at runtime

---

## 3. Risks of Using Third-Party Libraries

[↑ Back to Table of Contents](#table-of-contents)

Using third-party libraries is powerful, but it comes with responsibilities. Any code you add to your project becomes **part of your application** — so the library's bugs, vulnerabilities, and legal obligations become yours too.

### Security Vulnerabilities

A library might have known security flaws. If you include it, those flaws are in your app. This is especially concerning for libraries that handle sensitive data such as authentication tokens or user information.

- Always check whether a library is actively maintained
- Watch for security advisories related to libraries you use
- Keep dependencies up-to-date

### Licence Compliance

Libraries come with licences (e.g., MIT, Apache 2.0, GPL). Some licences impose obligations on your app — for example, requiring you to include the library's licence notice, or even to open-source your own code.

- Read the licence before adding a library
- Popular Android libraries like Volley, Retrofit, and Glide use the Apache 2.0 licence, which is very permissive

### Bloating Your App

Every library you add increases the size of your APK (the installable package). Large apps can deter users from downloading them, particularly on devices with limited storage or slow connections.

- Only add libraries you genuinely need
- Consider whether writing a small piece of code yourself might be better than pulling in a large library for a tiny feature

### Abandoned Libraries

If a library is no longer maintained, it won't receive updates for new versions of Android. This can break your app or leave it exposed to security issues.

- Check the GitHub page of any library you consider using
- Look at the last commit date, open issues, and the number of contributors

---

## 4. Networking Libraries: Volley and Retrofit

[↑ Back to Table of Contents](#table-of-contents)

Making HTTP requests in Android requires careful handling of background threads, response parsing, error handling, and caching. Networking libraries abstract away much of this complexity.

### Volley

**Volley** is a networking library developed by Google and released as open source. It was designed specifically for Android and handles many of the tricky details of networking automatically:

- Automatic scheduling of network requests
- Multiple concurrent network connections
- Transparent disk and memory response caching
- Cancellation of requests
- Request prioritisation

Volley is particularly well-suited for **frequent, small HTTP requests** — such as loading data to populate list items or a news feed.

### Retrofit

**Retrofit** is a type-safe HTTP client for Android developed by Square. It takes a different approach to Volley:

- You define your API endpoints as a Kotlin **interface**
- Retrofit generates the implementation for you at runtime
- It integrates cleanly with Gson for JSON parsing and with Kotlin Coroutines for asynchronous operations

Retrofit tends to be preferred for apps with a well-defined REST API, where clean architecture and type safety are priorities.

#### Comparison at a Glance

```
+------------------+-----------------------------+-----------------------------+
| Feature          | Volley                      | Retrofit                    |
+------------------+-----------------------------+-----------------------------+
| Developed by     | Google                      | Square                      |
| Best for         | Simple, frequent requests   | Structured REST APIs        |
| JSON Parsing     | Manual or with Gson plugin  | Automatic via converter     |
| Async support    | Callback-based              | Coroutines / RxJava         |
| Caching          | Built-in                    | Via OkHttp                  |
+------------------+-----------------------------+-----------------------------+
```

---

## 5. Image Loading Libraries: Picasso and Glide

[↑ Back to Table of Contents](#table-of-contents)

Loading images from the internet into an Android `ImageView` seems simple, but doing it correctly is surprisingly complex. You need to:

- Perform the download on a background thread (not the main UI thread)
- Cache the image to avoid re-downloading it on every screen rotation
- Resize the image to fit the `ImageView` (to avoid wasting memory)
- Handle errors gracefully (e.g., a broken image URL)

Both **Picasso** and **Glide** handle all of this for you.

### Picasso

Developed by Square, Picasso offers a clean, fluent API:

```kotlin
// Pseudocode — not runnable standalone (requires Android context and ImageView)
// Picasso.get()
//     .load("https://example.com/image.jpg")
//     .into(imageView)
```

Picasso is lightweight and straightforward, making it ideal for simpler apps.

### Glide

Developed by BumpTech (with contributions from Google), Glide supports:

- Animated GIFs out of the box
- More flexible caching strategies
- A broader range of media types (video thumbnails, etc.)

Glide tends to perform better with large volumes of images (e.g., a photo gallery app).

```kotlin
// Pseudocode — not runnable standalone (requires Android context and ImageView)
// Glide.with(context)
//     .load("https://example.com/image.jpg")
//     .into(imageView)
```

Both libraries follow a similar fluent pattern — you tell the library **where** the image comes from, **where** it should go, and the library handles the rest.

---

## 6. Data Handling Libraries: Gson

[↑ Back to Table of Contents](#table-of-contents)

When your app communicates with a web API, the data is almost always returned as **JSON** (JavaScript Object Notation). You need to convert that JSON text into Kotlin objects you can work with in your code. This process is called **deserialisation** (or parsing). The reverse — converting Kotlin objects back to JSON — is called **serialisation**.

**Gson** is a library developed by Google that handles both directions automatically.

### How Gson Works

You define a Kotlin data class whose properties match the JSON structure, and Gson maps the JSON fields to your class automatically.

```kotlin
import com.google.gson.Gson

// Define a data class that mirrors the JSON structure
data class User(
    val id: Int,
    val name: String,
    val email: String
)

fun main() {
    val gson = Gson()

    // --- Deserialisation: JSON String → Kotlin Object ---
    val jsonString = """
        {
            "id": 42,
            "name": "Alice",
            "email": "alice@example.com"
        }
    """.trimIndent()

    // Gson reads the JSON and fills in the User fields automatically
    val user: User = gson.fromJson(jsonString, User::class.java)

    println("Parsed user: ${user.name}, email: ${user.email}")
    // Output: Parsed user: Alice, email: alice@example.com

    // --- Serialisation: Kotlin Object → JSON String ---
    val newUser = User(id = 99, name = "Bob", email = "bob@example.com")
    val outputJson = gson.toJson(newUser)

    println("JSON output: $outputJson")
    // Output: JSON output: {"id":99,"name":"Bob","email":"bob@example.com"}
}
```

**Sample Output:**
```
Parsed user: Alice, email: alice@example.com
JSON output: {"id":99,"name":"Bob","email":"bob@example.com"}
```

### Gson with Lists

Real APIs often return arrays of objects. Gson handles these too, using a `TypeToken` to describe the type at runtime:

```kotlin
import com.google.gson.Gson
import com.google.gson.reflect.TypeToken

data class Product(val id: Int, val name: String, val price: Double)

fun main() {
    val gson = Gson()

    val jsonArray = """
        [
            {"id": 1, "name": "Laptop",  "price": 999.99},
            {"id": 2, "name": "Mouse",   "price": 19.99},
            {"id": 3, "name": "Monitor", "price": 299.00}
        ]
    """.trimIndent()

    // TypeToken tells Gson the full generic type (List<Product>)
    val type = object : TypeToken<List<Product>>() {}.type
    val products: List<Product> = gson.fromJson(jsonArray, type)

    // Print each product
    for (product in products) {
        println("${product.name}: £${"%.2f".format(product.price)}")
    }
}
```

**Sample Output:**
```
Laptop: £999.99
Mouse: £19.99
Monitor: £299.00
```

---

## 7. Other Useful Libraries: Parceler and IcePick

[↑ Back to Table of Contents](#table-of-contents)

### Parceler

In Android, when you pass objects between Activities or Fragments, you typically need to use **Parcels** — a fast Android-specific serialisation mechanism. However, implementing the `Parcelable` interface by hand is tedious and error-prone, requiring you to write `writeToParcel()` and `createFromParcel()` methods manually.

**Parceler** automates this using annotations. You simply annotate your class with `@Parcel`, and Parceler generates the required boilerplate at compile time.

```kotlin
// Pseudocode illustration of Parceler usage (not standalone runnable)
// In a real project:
// import org.parceler.Parcel

// @Parcel
// data class Item(val id: Int = 0, val title: String = "")
//
// Wrapping for use in an Intent:
// val wrapped = Parcels.wrap(Item(1, "Example"))
// intent.putExtra("item", wrapped)
//
// Unwrapping:
// val item: Item = Parcels.unwrap(intent.getParcelableExtra("item"))
```

> **Why the default values?** Parceler requires a no-argument constructor. In Kotlin data classes, you achieve this by giving all parameters default values.

### IcePick

When a user rotates their phone, Android destroys and recreates the Activity. If you don't save your UI state, your data disappears. Android provides `onSaveInstanceState()` for this, but writing the save/restore code for many fields is repetitive.

**IcePick** uses annotations to generate this boilerplate automatically:

```kotlin
// Pseudocode illustration of IcePick usage (not standalone runnable)
// In a real Android Activity:
// import icepick.Icepick
// import icepick.State

// @State var username: String = ""
// @State var score: Int = 0
//
// override fun onCreate(savedInstanceState: Bundle?) {
//     super.onCreate(savedInstanceState)
//     Icepick.restoreInstanceState(this, savedInstanceState) // Restores @State fields
// }
//
// override fun onSaveInstanceState(outState: Bundle) {
//     super.onSaveInstanceState(outState)
//     Icepick.saveInstanceState(this, outState) // Saves @State fields
// }
```

Both Parceler and IcePick are examples of **annotation processors** — they generate code at compile time, so they add zero runtime overhead.

---

## 8. Demo: Simplifying Network Requests with Volley

[↑ Back to Table of Contents](#table-of-contents)

Now let's look at how Volley actually works in practice. We'll examine the key classes and patterns you'll use, then see a complete example.

### Key Volley Concepts

**RequestQueue** — The central object that manages and dispatches your network requests. You typically keep one per application.

**StringRequest** — A request that returns the response body as a raw `String`. Useful for quick experiments or when you handle parsing yourself.

**JsonObjectRequest** — A request that automatically parses the response into a `JSONObject`.

**Response.Listener / Response.ErrorListener** — Callbacks invoked on the main thread when the request succeeds or fails.

### The Request Lifecycle

```
App Code
   |
   |  1. Create a Request (StringRequest, JsonObjectRequest, etc.)
   v
RequestQueue
   |
   |  2. Queues and dispatches request on a background thread
   v
Network (Internet)
   |
   |  3. Response returned
   v
RequestQueue
   |
   |  4. Calls your listener on the MAIN thread with the result
   v
App Code (updates UI safely)
```

### A Realistic Volley Example

The following example demonstrates how Volley is used in an Android app. Because Volley requires an Android `Context`, the code below is structured as it would appear inside an Activity. This is not runnable as a plain Kotlin program — it is representative of real Android code.

```kotlin
// File: MainActivity.kt
// This represents how you would use Volley inside an Android Activity.
// Requires:
//   implementation("com.android.volley:volley:1.2.1") in app/build.gradle
//   <uses-permission android:name="android.permission.INTERNET"/> in AndroidManifest.xml

import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.android.volley.Request
import com.android.volley.RequestQueue
import com.android.volley.toolbox.JsonObjectRequest
import com.android.volley.toolbox.Volley

class MainActivity : AppCompatActivity() {

    // The RequestQueue manages and dispatches all network requests
    private lateinit var requestQueue: RequestQueue

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Initialise the RequestQueue — this is typically done once per app
        // 'this' refers to the Activity Context
        requestQueue = Volley.newRequestQueue(this)

        // Call our function to fetch data
        fetchUserData()
    }

    private fun fetchUserData() {
        // A public, free-to-use test API that returns sample user data
        val url = "https://jsonplaceholder.typicode.com/users/1"

        // Create a JsonObjectRequest:
        //   - Request.Method.GET   = we're reading data, not sending any
        //   - url                  = where to send the request
        //   - null                 = no request body (it's a GET request)
        //   - Response.Listener    = lambda called on success, on the main thread
        //   - Response.ErrorListener = lambda called on failure
        val request = JsonObjectRequest(
            Request.Method.GET,
            url,
            null,
            { response ->
                // SUCCESS: response is a JSONObject parsed from the server's reply
                // We can safely update the UI here — Volley calls this on the main thread
                val name = response.getString("name")
                val email = response.getString("email")
                Log.d("Volley", "Received user: $name ($email)")

                // In a real app, you would update a TextView here:
                // binding.nameTextView.text = name
            },
            { error ->
                // ERROR: something went wrong (no network, server error, etc.)
                Log.e("Volley", "Request failed: ${error.message}")
            }
        )

        // Add the request to the queue — Volley will handle it from here
        requestQueue.add(request)
    }

    override fun onStop() {
        super.onStop()
        // Cancel all pending requests when the Activity is no longer visible
        // This prevents callbacks from arriving after the Activity is destroyed
        requestQueue.cancelAll(this)
    }
}
```

**What the API returns (JSON):**
```json
{
  "id": 1,
  "name": "Leanne Graham",
  "email": "Sincere@april.biz",
  "phone": "1-770-736-0988",
  "website": "hildegard.org"
}
```

**Logcat output (what you'd see in Android Studio):**
```
D/Volley: Received user: Leanne Graham (Sincere@april.biz)
```

### Putting It All Together: Volley + Gson

In practice, you'll often use Volley to fetch raw JSON and Gson to parse it into a data class. Here is a self-contained Kotlin example (not Android-specific) that demonstrates the Gson parsing side you'd apply to the Volley response body:

```kotlin
import com.google.gson.Gson

// Data class matching the structure of the API response
data class ApiUser(
    val id: Int,
    val name: String,
    val email: String,
    val phone: String,
    val website: String
)

fun main() {
    // Simulate the JSON string you would receive from Volley's onResponse callback
    val jsonFromServer = """
        {
            "id": 1,
            "name": "Leanne Graham",
            "email": "Sincere@april.biz",
            "phone": "1-770-736-0988",
            "website": "hildegard.org"
        }
    """.trimIndent()

    val gson = Gson()

    // Parse the JSON into an ApiUser object
    val user: ApiUser = gson.fromJson(jsonFromServer, ApiUser::class.java)

    // Display the result — in a real app this data would populate your UI
    println("=== User Profile ===")
    println("Name:    ${user.name}")
    println("Email:   ${user.email}")
    println("Phone:   ${user.phone}")
    println("Website: ${user.website}")
}
```

**Sample Output:**
```
=== User Profile ===
Name:    Leanne Graham
Email:   Sincere@april.biz
Phone:   1-770-736-0988
Website: hildegard.org
```

### What a Volley-Powered App Looks Like

```
+---------------------------+
|       Android App         |
|                           |
|  +-----------+            |
|  | Activity  |            |
|  |           |            |
|  | 1. Create |            |
|  |   Request |            |
|  +-----+-----+            |
|        |                  |
|  +-----v-----------+      |
|  |   RequestQueue  |      |
|  |  (Volley Core)  |      |
|  +-----+-----------+      |
|        |                  |
+--------|------------------+
         | HTTPS
+--------v------------------+
|     Web API / Server      |
|  Returns JSON response    |
+---------------------------+
         |
+--------v------------------+
|     RequestQueue          |
|  Calls onResponse()       |
|  on the Main Thread       |
+--------+------------------+
         |
+--------v------------------+
|   Activity updates UI     |
|   (TextViews, RecyclerView|
|    etc.)                  |
+---------------------------+
```

---

[↑ Back to Table of Contents](#table-of-contents)
