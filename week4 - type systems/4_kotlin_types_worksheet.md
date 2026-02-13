# Lecture 3: Kotlin Worksheet

## Quiz Section

### Question 1: Fill in the Blanks

Complete the following sentences using words from the word bank below.

In a __________ typed language, the type of every variable is known at __________. The compiler verifies that all operations are type-safe before generating the executable program. In contrast, __________ typed languages check types at __________, which means variables can hold values of any type and the type can change during program execution.

**Word Bank:** dynamically, compile time, statically, runtime

---

### Question 2: Fill in the Blanks

Complete the following sentences using words from the word bank below.

Kotlin's type system is considered __________ typed because it requires __________ type conversions and enforces type compatibility strictly. For example, you cannot directly add an Int to a Double without using the __________ method. This prevents __________ bugs and makes all conversions visible in the code.

**Word Bank:** toDouble(), explicit, strongly, accidental

---

### Question 3: Fill in the Blanks

Complete the following sentences using words from the word bank below.

__________ provide a way to write flexible, reusable code while maintaining type safety. A __________ type like `List` has a type __________ that acts as a placeholder. When you create `List<String>`, String is the type __________ and `List<String>` is called a __________ type.

**Word Bank:** argument, parameter, generic, Generics, parameterized

---

### Question 4: Multiple Choice

What is the main advantage of static typing over dynamic typing?

A) Static typing allows variables to change types during execution  
B) Static typing catches type errors at compile time before the program runs  
C) Static typing requires less code to be written  
D) Static typing is faster at runtime in all cases

---

### Question 5: Multiple Choice

Consider the following Kotlin code:

```kotlin
val numbers = mutableListOf(1, 2, 3)
numbers.add("four")
```

What will happen when you try to compile this code?

A) It will compile successfully and add the string "four" to the list  
B) It will compile but throw a runtime error  
C) It will not compile because of a type mismatch error  
D) It will compile and convert "four" to the number 4

---

### Question 6: Multiple Choice

Which of the following statements about Kotlin's type inference is TRUE?

A) Type inference means Kotlin is dynamically typed  
B) Type inference allows you to change a variable's type after declaration  
C) Type inference lets the compiler deduce types automatically while maintaining static type checking  
D) Type inference is only available for primitive types like Int and String

---

## Task Section

### Task 1: Library Book Collection

Create a type-safe book management system using generics. You need to implement a function that processes different types of collections.

**Requirements:**
- Complete the `printCollection` function that takes a generic list and prints each item with its index
- Create three different collections: one for book titles (String), one for publication years (Int), and one for prices (Double)
- Demonstrate that the function works with all three types

**Code Scaffolding:**

```kotlin
// Complete this generic function
fun <T> printCollection(items: List<T>, collectionName: String) {
    // TODO: Print the collection name
    // TODO: Iterate through items and print each with its index
    // Format: "[index]: item"
}

fun main() {
    // TODO: Create a list of book titles
    val bookTitles = listOf(/* add 3 book titles */)
    
    // TODO: Create a list of publication years
    val publicationYears = listOf(/* add 3 years */)
    
    // TODO: Create a list of prices
    val prices = listOf(/* add 3 prices */)
    
    // TODO: Call printCollection for each list
}
```

**Expected Output:**
```
Book Titles:
[0]: The Hobbit
[1]: 1984
[2]: Pride and Prejudice

Publication Years:
[0]: 1937
[1]: 1949
[2]: 1813

Prices:
[0]: 12.99
[1]: 9.99
[2]: 14.99
```

---

### Task 2: Temperature Converter with Type Safety

Create a temperature conversion program that demonstrates Kotlin's strong typing and explicit conversion requirements.

**Requirements:**
- Complete the functions to convert temperatures between Celsius and Fahrenheit
- The functions should work with Double precision
- Demonstrate that Kotlin requires explicit conversion when mixing Int and Double types
- Calculate and display average temperatures

**Code Scaffolding:**

```kotlin
// Convert Celsius to Fahrenheit
// Formula: F = C * 9/5 + 32
fun celsiusToFahrenheit(celsius: Double): Double {
    // TODO: Implement the conversion formula
    return 0.0  // Replace this
}

// Convert Fahrenheit to Celsius
// Formula: C = (F - 32) * 5/9
fun fahrenheitToCelsius(fahrenheit: Double): Double {
    // TODO: Implement the conversion formula
    return 0.0  // Replace this
}

// Calculate average temperature
fun calculateAverage(temps: List<Double>): Double {
    // TODO: Calculate and return the average
    return 0.0  // Replace this
}

fun main() {
    // TODO: Create a list of temperatures in Celsius (use Int values: 0, 20, 35)
    val celsiusTemps = listOf(/* add temperatures */)
    
    println("Celsius temperatures: $celsiusTemps")
    
    // TODO: Convert each to Fahrenheit (remember to convert Int to Double!)
    // Create a new list with the converted values
    
    // TODO: Calculate and print the average Celsius temperature
    
    // TODO: Calculate and print the average Fahrenheit temperature
}
```

**Expected Output:**
```
Celsius temperatures: [0, 20, 35]
Fahrenheit temperatures: [32.0, 68.0, 95.0]

Average Celsius: 18.33°C
Average Fahrenheit: 65.0°F
```

**Hint:** Remember that Kotlin requires explicit conversion from Int to Double using `.toDouble()`

---

**End of Worksheet**