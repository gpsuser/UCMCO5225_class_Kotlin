# Worksheet

---

## Quiz

### Part A: Fill in the Blanks

Complete each sentence by choosing the correct word from the word bank below each question. Each word bank contains extra words — not all of them will be used.

---

**Question 1**

When you add a library to an Android project, Gradle also downloads any libraries that the added library itself depends on. These are called __________ dependencies. The process of making these libraries available to your code is triggered by __________ the project in Android Studio.

**Word Bank (choose 2):**

> `transitive` &nbsp;|&nbsp; `recursive` &nbsp;|&nbsp; `syncing` &nbsp;|&nbsp; `compiling` &nbsp;|&nbsp; `parallel` &nbsp;|&nbsp; `building`

---

**Question 2**

Gson is used to convert a JSON string into a Kotlin object — a process called __________. The reverse process, converting a Kotlin object into a JSON string, is called __________. Both directions are handled automatically when your Kotlin class __________ the JSON structure.

**Word Bank (choose 3):**

> `serialisation` &nbsp;|&nbsp; `deserialisation` &nbsp;|&nbsp; `mirrors` &nbsp;|&nbsp; `extends` &nbsp;|&nbsp; `compilation` &nbsp;|&nbsp; `parsing` &nbsp;|&nbsp; `overrides`

---

**Question 3**

Volley is a networking library developed by __________. It is particularly well-suited for __________ and frequent HTTP requests. When a request completes, Volley calls your __________ on the main thread so you can safely update the UI.

**Word Bank (choose 3):**

> `Google` &nbsp;|&nbsp; `Square` &nbsp;|&nbsp; `small` &nbsp;|&nbsp; `large` &nbsp;|&nbsp; `listener` &nbsp;|&nbsp; `handler` &nbsp;|&nbsp; `interface` &nbsp;|&nbsp; `callback`

---

### Part B: Multiple Choice

Circle or highlight the letter of the best answer.

---

**Question 4**

Which of the following is a risk of using third-party libraries in your Android app?

- A) The library's code is always faster than code you write yourself
- B) Security vulnerabilities in the library become vulnerabilities in your app
- C) Libraries automatically keep themselves up-to-date without any action from you
- D) Using a library removes the need to test your own code

---

**Question 5**

You are building an app that displays a photo gallery with hundreds of images loaded from URLs. Which library would be the most appropriate choice for loading and displaying those images?

- A) Gson — because it handles data serialisation efficiently
- B) Volley — because it makes HTTP GET requests automatically
- C) Glide — because it supports animated GIFs, flexible caching, and performs well with large volumes of images
- D) Parceler — because it passes image objects between Activities

---

**Question 6**

Study the code snippet below:

```kotlin
val type = object : TypeToken<List<Product>>() {}.type
val products: List<Product> = gson.fromJson(jsonArray, type)
```

Why is `TypeToken` used here rather than simply `Product::class.java`?

- A) `TypeToken` makes the code run faster on older Android devices
- B) Kotlin cannot use `.class.java` syntax at all
- C) `TypeToken` is needed because generic type information (such as `List<Product>`) is erased at runtime, and `TypeToken` preserves it so Gson knows what type to create
- D) `TypeToken` converts the JSON array into a `Set` rather than a `List`

---

## Tasks

### Task 1 — Parse a JSON Object with Gson

A web API returns the following JSON describing a weather reading:

```json
{
    "city": "Manchester",
    "temperature": 12.4,
    "condition": "Cloudy",
    "humidity": 78
}
```

Complete the scaffolded code below to parse this JSON into a Kotlin data class and print the details in the format shown in the sample output.

```kotlin
import com.google.gson.Gson

// TODO 1: Define a data class called WeatherReading whose fields
//         match the JSON keys above (city, temperature, condition, humidity)
data class WeatherReading(/* your fields here */)

fun main() {
    val json = """
        {
            "city": "Manchester",
            "temperature": 12.4,
            "condition": "Cloudy",
            "humidity": 78
        }
    """.trimIndent()

    val gson = Gson()

    // TODO 2: Use gson.fromJson() to parse the json string into a WeatherReading object


    // TODO 3: Print the result in the format shown below
}
```

**Expected Output:**
```
City:        Manchester
Temperature: 12.4°C
Condition:   Cloudy
Humidity:    78%
```

---

### Task 2 — Serialise a List to JSON with Gson

You have a list of book objects and need to convert the entire list into a JSON string (for example, to send to a server or save to a file).

Complete the scaffolded code below to serialise the list and then print each field of each book after deserialising back to a list.

```kotlin
import com.google.gson.Gson
import com.google.gson.reflect.TypeToken

data class Book(val title: String, val author: String, val year: Int)

fun main() {
    val books = listOf(
        Book("Clean Code", "Robert C. Martin", 2008),
        Book("The Pragmatic Programmer", "David Thomas", 1999),
        Book("Kotlin in Action", "Dmitry Jemerov", 2017)
    )

    val gson = Gson()

    // TODO 1: Serialise the books list to a JSON string using gson.toJson()
    //         and print it


    // TODO 2: Deserialise your JSON string back into a List<Book>
    //         (remember to use TypeToken)


    // TODO 3: Loop through the deserialised list and print each book
    //         in the format shown below
}
```

**Expected Output:**
```
Serialised JSON:
[{"title":"Clean Code","author":"Robert C. Martin","year":2008},{"title":"The Pragmatic Programmer","author":"David Thomas","year":1999},{"title":"Kotlin in Action","author":"Dmitry Jemerov","year":2017}]

Deserialised books:
- Clean Code by Robert C. Martin (2008)
- The Pragmatic Programmer by David Thomas (1999)
- Kotlin in Action by Dmitry Jemerov (2017)
```

---

### Challenge Task — Build a Simple In-Memory API Simulator

Real Android apps use Volley to fetch JSON from an API, then Gson to parse it. This challenge simulates that pipeline entirely in plain Kotlin — no Android needed.

You are given a function `simulateFetch(endpoint: String): String` that pretends to be a network call. It returns different JSON strings depending on the endpoint string passed in. Your job is to:

1. Call `simulateFetch()` with the correct endpoint
2. Parse the returned JSON using Gson
3. Display the results in a formatted table

```kotlin
import com.google.gson.Gson
import com.google.gson.reflect.TypeToken

// --- Data class for an API product ---
data class StoreProduct(
    val id: Int,
    val name: String,
    val category: String,
    val price: Double,
    val inStock: Boolean
)

// --- Simulated network layer (do not modify) ---
fun simulateFetch(endpoint: String): String {
    return when (endpoint) {
        "/products" -> """
            [
                {"id":1,"name":"Wireless Keyboard","category":"Peripherals","price":49.99,"inStock":true},
                {"id":2,"name":"USB-C Hub","category":"Peripherals","price":34.50,"inStock":false},
                {"id":3,"name":"27in Monitor","category":"Displays","price":299.00,"inStock":true},
                {"id":4,"name":"Webcam HD","category":"Peripherals","price":79.99,"inStock":true}
            ]
        """.trimIndent()
        else -> "[]"
    }
}

fun main() {
    val gson = Gson()

    // TODO 1: Call simulateFetch() with the "/products" endpoint
    //         and store the returned JSON string


    // TODO 2: Use Gson and TypeToken to parse the JSON string
    //         into a List<StoreProduct>


    // TODO 3: Print a formatted table header:
    //         ID | Name                | Category    | Price   | In Stock
    //         ---|---------------------|-------------|---------|----------


    // TODO 4: Loop through the products and print each row.
    //         Format price to 2 decimal places.
    //         Show "Yes" or "No" for inStock.


    // TODO 5: After the table, print a summary line showing:
    //         - Total number of products
    //         - Number in stock
    //         - Total value of in-stock items only (sum of their prices)
}
```

**Expected Output:**
```
ID | Name                | Category    | Price   | In Stock
---|---------------------|-------------|---------|----------
 1 | Wireless Keyboard   | Peripherals | £49.99  | Yes
 2 | USB-C Hub           | Peripherals | £34.50  | No
 3 | 27in Monitor        | Displays    | £299.00 | Yes
 4 | Webcam HD           | Peripherals | £79.99  | Yes

Summary
-------
Total products : 4
In stock       : 3
In-stock value : £428.98
```
