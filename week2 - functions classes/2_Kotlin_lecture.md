# Kotlin Functions and Classes: Comprehensive Lecture Content

## Table of Contents

| Section | Topic | Time Allocation |
|---------|-------|-----------------|
| 1 | Introduction & Overview | 2 minutes |
| 2 | Functions Overview and Types | 3 minutes |
| 3 | Default Values & Named Parameters | 4 minutes |
| 4 | Lambda Expressions | 6 minutes |
| 5 | Higher Order Functions | 6 minutes |
| 6 | Basic Class Syntax | 6 minutes |
| 7 | Getters and Setters | 5 minutes |
| 8 | Primary Constructor | 5 minutes |
| 9 | Init Blocks | 4 minutes |
| 10 | Secondary Constructors | 5 minutes |
| 11 | Data Classes | 4 minutes |
| 12 | Extension Functions and Properties | 5 minutes |
| **Total** | | **55 minutes** |

## SMART Objectives

| Objective | Description |
|-----------|-------------|
| **Specific** | Students will learn to create functions with various signatures, work with lambda expressions and higher-order functions, then apply these concepts to build Kotlin classes with various constructor types, implement custom getters and setters, use data classes, and create extension functions |
| **Measurable** | Students will be able to write at least two higher-order functions with lambda expressions, create three different class implementations with various constructor types, and implement two extension functions by the end of the session |
| **Achievable** | Content is structured progressively from functional programming concepts to object-oriented concepts, with clear code examples and explanations suitable for students with basic programming knowledge |
| **Relevant** | These concepts are fundamental to modern Kotlin development and are essential for Android development, server-side Kotlin applications, and understanding the Kotlin standard library |
| **Time-bound** | All learning objectives will be covered within the 55-minute lecture period |

## Learning Outcomes

| Code | Learning Outcome |
|------|------------------|
| **LO1** | Understand function types and syntax in Kotlin, including how functions can be treated as first-class citizens |
| **LO2** | Implement functions with default parameters and use named parameter passing for improved code readability |
| **LO3** | Create and use lambda expressions with various syntax forms, including single-parameter shorthand notation |
| **LO4** | Design and implement higher-order functions that accept functions as parameters or return functions |
| **LO5** | Understand and implement basic Kotlin class syntax with properties and methods |
| **LO6** | Design classes with custom getters and setters, including private set modifiers for controlled access |
| **LO7** | Create classes using primary constructors and understand their capabilities and limitations |
| **LO8** | Implement initialization logic using init blocks for complex setup requirements |
| **LO9** | Design classes with multiple constructors using secondary constructor syntax |
| **LO10** | Utilize data classes for simple data-holding objects with automatic functionality |
| **LO11** | Extend existing classes with extension functions and properties without inheritance |

---

## Section 1: Introduction & Overview (2 minutes)

Welcome to this comprehensive lecture on Kotlin functions and classes. Today we will embark on a journey that begins with understanding how Kotlin treats functions as first-class citizens, enabling powerful functional programming patterns. 

We will then explore how these functional concepts integrate seamlessly with Kotlin's approach to object-oriented programming, which significantly improves upon traditional class structures.

The lecture is intentionally organized to teach functions first because understanding how functions work as values is crucial to grasping many of Kotlin's modern features. 

Once we have established a solid foundation in functional programming concepts including lambdas and higher-order functions, we will transition to exploring classes. 

You will see how the functional concepts we learn early on enhance our ability to work with classes through features like extension functions.

By structuring our learning this way, you will develop a complete understanding of both paradigms and see how Kotlin elegantly combines functional and object-oriented programming. 

This combination makes Kotlin particularly expressive and powerful for modern application development.

---

## Section 2: Functions Overview and Types (3 minutes)

### Understanding Kotlin Functions

In traditional Java programming, we typically refer to methods, which are functions that belong to a class. 

Kotlin takes a broader and more flexible approach by allowing functions to exist independently outside of classes. 

While every method is indeed a function, not every function needs to be a method. This distinction becomes particularly important when we explore functional programming patterns.

* Functions in Kotlin are first-class citizens, which means they have types just like variables do. 

The type of a function describes its signature, specifically what parameters it accepts and what type of value it returns. 

This type system uses a special syntax that makes the function's contract immediately clear and enables us to `pass functions around as values`.

### Function Types Example

```kotlin
/**
 * A regular function that greets someone
 * This demonstrates the basic function syntax in Kotlin
 */
fun greet(forename: String, surname: String): String {
    return "Hello $forename $surname"
}

/**
 * Function type demonstration
 * This function's type is: (String, String) -> String
 * It takes two String parameters and returns a String
 */
fun demonstrateFunctionTypes() {
    // We can store a reference to the greet function using ::
    val greetingFunction: (String, String) -> String = ::greet
    
    // Now we can call it through the variable
    val message = greetingFunction("John", "Smith")
    println(message)
}

/**
 * Function that takes no parameters and returns nothing (Unit)
 * Function type: () -> Unit
 * Unit is Kotlin's equivalent to void in Java
 */
fun printWelcome(): Unit {
    println("Welcome to Kotlin!")
}

/**
 * Function that takes an Int and returns an Int
 * Function type: (Int) -> Int
 */
fun square(x: Int): Int {
    return x * x
}

/**
 * Function that takes multiple parameters of different types
 * Function type: (String, Int, Boolean) -> String
 */
fun formatPerson(name: String, age: Int, isStudent: Boolean): String {
    val status = if (isStudent) "student" else "professional"
    return "$name is $age years old and is a $status"
}

/**
 * Main function demonstrating function types
 */
fun main() {
    println("Function Types in Kotlin:\n")
    
    // Function type: (String, String) -> String
    println("1. Function with two String parameters returning String:")
    println("   Type: (String, String) -> String")
    val result1 = greet("Alice", "Johnson")
    println("   Result: $result1")
    
    println("\n" + "-".repeat(60) + "\n")
    
    // Function type: () -> Unit
    println("2. Function with no parameters returning Unit:")
    println("   Type: () -> Unit")
    printWelcome()
    
    println("\n" + "-".repeat(60) + "\n")
    
    // Function type: (Int) -> Int
    println("3. Function with Int parameter returning Int:")
    println("   Type: (Int) -> Int")
    println("   square(5) = ${square(5)}")
    
    println("\n" + "-".repeat(60) + "\n")
    
    // Function type: (String, Int, Boolean) -> String
    println("4. Function with mixed parameters:")
    println("   Type: (String, Int, Boolean) -> String")
    println("   ${formatPerson("Bob", 25, true)}")
    
    println("\n" + "-".repeat(60) + "\n")
    
    // Storing function references
    println("5. Storing function references:")
    val mySquare: (Int) -> Int = ::square
    println("   Using stored reference: mySquare(7) = ${mySquare(7)}")
    
    // Calling the demonstration
    println("\n" + "-".repeat(60) + "\n")
    println("6. Calling function through reference:")
    demonstrateFunctionTypes()
}
```

**Sample Output:**
```
Function Types in Kotlin:

1. Function with two String parameters returning String:
   Type: (String, String) -> String
   Result: Hello Alice Johnson

------------------------------------------------------------

2. Function with no parameters returning Unit:
   Type: () -> Unit
Welcome to Kotlin!

------------------------------------------------------------

3. Function with Int parameter returning Int:
   Type: (Int) -> Int
   square(5) = 25

------------------------------------------------------------

4. Function with mixed parameters:
   Type: (String, Int, Boolean) -> String
   Bob is 25 years old and is a student

------------------------------------------------------------

5. Storing function references:
   Using stored reference: mySquare(7) = 49

------------------------------------------------------------

6. Calling function through reference:
Hello John Smith
```

### Understanding Function Type Syntax

The function type syntax is straightforward once you understand the pattern. 

You write the parameter types inside parentheses, separated by commas, then use an arrow operator, and finally specify the return type. 

For example, a function that takes two integers and returns an integer has the type `(Int, Int) -> Int`. 

* A function with no parameters that returns nothing has the type `() -> Unit`.

This type system is what makes functions first-class citizens in Kotlin. 

* Because functions have types, you can store them in variables, pass them as arguments to other functions, and return them from functions. 

Understanding function types is crucial because it opens up powerful programming patterns that we will explore throughout this lecture.

---

## Section 3: Default Values & Named Parameters (4 minutes)

### Understanding Default Parameters

Kotlin provides an elegant solution to a common problem in programming: 

* the need for multiple versions of the same function that differ only in having different numbers of parameters. 

By allowing you to specify default values for function parameters, Kotlin eliminates much of the boilerplate code that would require multiple `overloaded methods` in languages like Java.

When you define a function with default parameter values, callers can omit those parameters when calling the function, and the defaults will be used automatically. 

This makes your code more flexible and easier to maintain. 

Furthermore, Kotlin enhances this feature with named parameters, which allow you to specify which argument corresponds to which parameter, `regardless of order.`

Named parameters become particularly valuable when a function has many parameters, as they make the code self-documenting and prevent errors that might occur from passing arguments in the wrong order.

### Default Values Example

```kotlin
/**
 * Function with default parameters for describing exam marks
 * This demonstrates how default values reduce the need for overloading
 */
fun describeMark(
    mark: Int, 
    pass: Int = 50, 
    merit: Int = 60, 
    distinction: Int = 70
): String {
    return when {
        mark >= distinction -> "Distinction"
        mark >= merit -> "Merit"
        mark >= pass -> "Pass"
        else -> "Fail"
    }
}

/**
 * Function to create a user profile with many default parameters
 * This shows how defaults make APIs more user-friendly
 */
fun createProfile(
    username: String,
    email: String,
    age: Int = 18,
    country: String = "Unknown",
    isVerified: Boolean = false,
    newsletter: Boolean = true
): String {
    return """
        Profile Created:
        - Username: $username
        - Email: $email
        - Age: $age
        - Country: $country
        - Verified: $isVerified
        - Newsletter: $newsletter
    """.trimIndent()
}

/**
 * Function for calculating shipping cost with defaults
 * Demonstrates how defaults can represent typical scenarios
 */
fun calculateShipping(
    weight: Double,
    distance: Int = 100,
    express: Boolean = false,
    insurance: Boolean = false
): Double {
    var cost = weight * 0.5 + distance * 0.1
    if (express) cost *= 1.5
    if (insurance) cost += 5.0
    return cost
}

/**
 * Main function demonstrating default values and named parameters
 */
fun main() {
    println("Default Parameters and Named Arguments:\n")
    
    // Using all default parameters
    println("1. Using all defaults (pass=50, merit=60, distinction=70):")
    println("   Mark 75: ${describeMark(75)}")
    println("   Mark 65: ${describeMark(65)}")
    println("   Mark 55: ${describeMark(55)}")
    println("   Mark 45: ${describeMark(45)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Overriding some defaults in order
    println("2. Overriding trailing defaults:")
    println("   describeMark(62, 40, 60, 80):")
    println("   Result: ${describeMark(62, 40, 60, 80)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Using named parameters - can skip defaults in the middle
    println("3. Named parameters - skipping middle defaults:")
    println("   describeMark(mark=65, distinction=65):")
    println("   Result: ${describeMark(mark = 65, distinction = 65)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Named parameters in any order
    println("4. Named parameters in any order:")
    println("   describeMark(merit=65, pass=50, mark=62):")
    println("   Result: ${describeMark(merit = 65, pass = 50, mark = 62)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Creating profiles with different combinations
    println("5. Creating user profiles:\n")
    
    // Minimum required parameters
    println("Profile A (minimal):")
    println(createProfile("alice123", "alice@example.com"))
    
    println("\n" + "-".repeat(60) + "\n")
    
    // Overriding some defaults
    println("Profile B (with age and country):")
    println(createProfile("bob456", "bob@example.com", age = 25, country = "USA"))
    
    println("\n" + "-".repeat(60) + "\n")
    
    // Using named parameters to set specific values
    println("Profile C (selective parameters):")
    println(createProfile(
        username = "charlie789",
        email = "charlie@example.com",
        isVerified = true,
        age = 30
    ))
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Shipping calculation examples
    println("6. Shipping cost calculations:\n")
    
    val weight = 5.0
    
    println("Standard shipping (5kg):")
    println("   Cost: $${String.format("%.2f", calculateShipping(weight))}")
    
    println("\nExpress shipping (5kg):")
    println("   Cost: $${String.format("%.2f", calculateShipping(weight, express = true))}")
    
    println("\nExpress with insurance (5kg, 200km):")
    println("   Cost: $${String.format("%.2f", 
        calculateShipping(weight, distance = 200, express = true, insurance = true)
    )}")
    
    println("\nCustom distance with insurance (5kg, 300km):")
    println("   Cost: $${String.format("%.2f", 
        calculateShipping(weight, 300, insurance = true)
    )}")
}
```

**Sample Output:**
```
Default Parameters and Named Arguments:

1. Using all defaults (pass=50, merit=60, distinction=70):
   Mark 75: Distinction
   Mark 65: Merit
   Mark 55: Pass
   Mark 45: Fail

============================================================

2. Overriding trailing defaults:
   describeMark(62, 40, 60, 80):
   Result: Merit

============================================================

3. Named parameters - skipping middle defaults:
   describeMark(mark=65, distinction=65):
   Result: Distinction

============================================================

4. Named parameters in any order:
   describeMark(merit=65, pass=50, mark=62):
   Result: Pass

============================================================

5. Creating user profiles:

Profile A (minimal):
Profile Created:
- Username: alice123
- Email: alice@example.com
- Age: 18
- Country: Unknown
- Verified: false
- Newsletter: true

------------------------------------------------------------

Profile B (with age and country):
Profile Created:
- Username: bob456
- Email: bob@example.com
- Age: 25
- Country: USA
- Verified: false
- Newsletter: true

------------------------------------------------------------

Profile C (selective parameters):
Profile Created:
- Username: charlie789
- Email: charlie@example.com
- Age: 30
- Country: Unknown
- Verified: true
- Newsletter: true

============================================================

6. Shipping cost calculations:

Standard shipping (5kg):
   Cost: $12.50

Express shipping (5kg):
   Cost: $18.75

Express with insurance (5kg, 200km):
   Cost: $38.75

Custom distance with insurance (5kg, 300km):
   Cost: $37.50
```

### Benefits of Default Parameters and Named Arguments

Default parameters significantly reduce code duplication by eliminating the need for multiple overloaded methods that differ only in which parameters they accept. 

Instead of writing three or four versions of the same function, you write one comprehensive version with sensible defaults. 

* This approach is more maintainable because any changes to the function logic only need to be made in one place.

* Named parameters make your code more self-documenting and less error-prone, especially when calling functions with many parameters or when you want to override only specific defaults while leaving others unchanged. 

The combination of these two features allows you to create flexible, user-friendly APIs that are both powerful and easy to use correctly.

---

## Section 4: Lambda Expressions (6 minutes)

### Understanding Lambda Expressions

Lambda expressions represent one of the most powerful features in Kotlin, bringing the elegance of functional programming into your everyday coding. 

A lambda expression is essentially an `anonymous function,` meaning it is a block of code that can be treated as a value without needing a formal function declaration with a name. 

You can:

* store lambdas in variables
* pass them as arguments to other functions
* return them from functions

The syntax for lambda expressions is distinctive: the code is enclosed in curly braces, parameters are listed before an arrow operator, and `the function body follows the arrow`. 

When a lambda has only a single parameter, Kotlin provides a convenient shorthand by allowing you to omit the parameter declaration entirely and refer to that parameter using the special identifier `it`.

`The last expression in a lambda automatically becomes the return value,` so you typically do not need explicit return statements. 

This makes lambdas particularly concise for simple operations while still being powerful enough for complex logic.

### Lambda Expression Example

```kotlin
/**
 * Main function demonstrating lambda expressions
 * This comprehensive example shows various lambda patterns
 */
fun main() {
    println("Lambda Expressions in Kotlin:\n")
    
    // 1. Simplest lambda - no parameters
    println("1. Lambda with no parameters:")
    val printHello = {
        println("Hello from lambda!")
    }
    printHello() // Call the lambda
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 2. Lambda with one parameter - explicit syntax
    println("2. Lambda with one parameter (explicit):")
    val greetPerson = { name: String ->
        println("Hello, $name!")
    }
    greetPerson("Alice")
    greetPerson("Bob")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 3. Lambda with one parameter - using 'it'
    println("3. Lambda with one parameter (using 'it'):")
    val square: (Int) -> Int = { it * it }
    println("   square(5) = ${square(5)}")
    println("   square(7) = ${square(7)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 4. Lambda with multiple parameters
    println("4. Lambda with multiple parameters:")
    val add = { a: Int, b: Int ->
        a + b
    }
    println("   add(10, 20) = ${add(10, 20)}")
    
    val greet = { firstName: String, lastName: String ->
        "Hello $firstName $lastName"
    }
    println("   ${greet("John", "Doe")}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 5. Lambda with explicit return type
    println("5. Lambda assigned to typed variable:")
    val multiply: (Int, Int) -> Int = { x, y ->
        x * y
    }
    println("   multiply(6, 7) = ${multiply(6, 7)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 6. Lambdas with multiple statements
    println("6. Lambda with multiple statements:")
    val calculateGrade = { mark: Int ->
        // Multiple statements in lambda
        val percentage = mark.toDouble()
        val grade = when {
            percentage >= 70 -> "A"
            percentage >= 60 -> "B"
            percentage >= 50 -> "C"
            percentage >= 40 -> "D"
            else -> "F"
        }
        "Mark: $mark -> Grade: $grade" // Last expression is returned
    }
    println("   ${calculateGrade(85)}")
    println("   ${calculateGrade(65)}")
    println("   ${calculateGrade(45)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 7. Using lambdas with standard library functions
    println("7. Lambdas with collection operations:")
    
    val numbers = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    
    // Filter even numbers
    val evenNumbers = numbers.filter { it % 2 == 0 }
    println("   Even numbers: $evenNumbers")
    
    // Map - square each number
    val squared = numbers.map { it * it }
    println("   Squared: $squared")
    
    // Filter and map combined
    val evenSquares = numbers.filter { it % 2 == 0 }.map { it * it }
    println("   Even squares: $evenSquares")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 8. Lambda capturing variables from outer scope
    println("8. Lambda capturing variables (closure):")
    var count = 0
    val increment = {
        count++
        println("   Count is now: $count")
    }
    
    increment()
    increment()
    increment()
    println("   Final count: $count")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 9. Practical example - data processing
    println("9. Practical example - student grade processing:")
    
    data class Student(val name: String, val mark: Int)
    
    val students = listOf(
        Student("Alice", 85),
        Student("Bob", 72),
        Student("Charlie", 45),
        Student("Diana", 91),
        Student("Eve", 68)
    )
    
    // Find students who passed (mark >= 50)
    val passed = students.filter { it.mark >= 50 }
    println("   Passed students:")
    passed.forEach { println("      ${it.name}: ${it.mark}") }
    
    // Get names of top performers (mark >= 70)
    val topPerformers = students
        .filter { it.mark >= 70 }
        .map { it.name }
    println("\n   Top performers (70+): $topPerformers")
    
    // Calculate average mark
    val averageMark = students.map { it.mark }.average()
    println("\n   Average mark: ${"%.2f".format(averageMark)}")
    
    // Find highest scorer
    val topStudent = students.maxByOrNull { it.mark }
    println("   Highest scorer: ${topStudent?.name} with ${topStudent?.mark}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 10. Storing and passing lambdas
    println("10. Storing lambdas in a list:")
    
    val operations = listOf<(Int, Int) -> Int>(
        { a, b -> a + b },
        { a, b -> a - b },
        { a, b -> a * b },
        { a, b -> a / b }
    )
    
    val operationNames = listOf("Addition", "Subtraction", "Multiplication", "Division")
    
    val x = 20
    val y = 5
    
    for (i in operations.indices) {
        val result = operations[i](x, y)
        println("   ${operationNames[i]}: $x and $y = $result")
    }
}
```

**Sample Output:**
```
Lambda Expressions in Kotlin:

1. Lambda with no parameters:
Hello from lambda!

============================================================

2. Lambda with one parameter (explicit):
Hello, Alice!
Hello, Bob!

============================================================

3. Lambda with one parameter (using 'it'):
   square(5) = 25
   square(7) = 49

============================================================

4. Lambda with multiple parameters:
   add(10, 20) = 30
   Hello John Doe

============================================================

5. Lambda assigned to typed variable:
   multiply(6, 7) = 42

============================================================

6. Lambda with multiple statements:
   Mark: 85 -> Grade: A
   Mark: 65 -> Grade: B
   Mark: 45 -> Grade: D

============================================================

7. Lambdas with collection operations:
   Even numbers: [2, 4, 6, 8, 10]
   Squared: [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
   Even squares: [4, 16, 36, 64, 100]

============================================================

8. Lambda capturing variables (closure):
   Count is now: 1
   Count is now: 2
   Count is now: 3
   Final count: 3

============================================================

9. Practical example - student grade processing:
   Passed students:
      Alice: 85
      Bob: 72
      Diana: 91
      Eve: 68

   Top performers (70+): [Alice, Bob, Diana]

   Average mark: 72.20
   Highest scorer: Diana with 91

============================================================

10. Storing lambdas in a list:
   Addition: 20 and 5 = 25
   Subtraction: 20 and 5 = 15
   Multiplication: 20 and 5 = 100
   Division: 20 and 5 = 4
```

### Understanding Lambda Syntax and Behavior

Lambda expressions provide an incredibly concise way to represent small pieces of functionality. 

The implicit return of the last expression means you rarely need explicit return statements, making the code cleaner and more readable. 

When a lambda captures variables from its surrounding scope, it creates what is called a `closure`, allowing the lambda to access and modify those variables even after the function that created them has finished executing.

The single-parameter shorthand using `it` is particularly convenient for simple operations, though `you should use explicit parameter names when it improves readability`. 

Lambda expressions work seamlessly with Kotlin's collection functions like filter, map, and forEach, enabling you to write expressive data processing pipelines with `minimal code`.

---

## Section 5: Higher Order Functions (6 minutes)

### Understanding Higher Order Functions

A higher-order function is a function that elevates the concept of functions to a new level by either accepting other functions as parameters, returning functions as results, or both. This concept is central to functional programming and enables powerful abstractions that make your code more reusable and expressive. Because Kotlin treats functions as first-class citizens with their own types, they can be passed around just like any other value such as strings or integers.

The syntax for calling higher-order functions in Kotlin has a special convenience feature. When the function parameter is the last parameter in the parameter list, you can move the lambda expression outside the parentheses. If the lambda is the only parameter, you can omit the parentheses entirely. This syntactic sugar makes the code read more naturally, almost like adding new control structures to the language.

Higher-order functions allow you to separate the logic of what to do from when or how to do it, leading to more modular and testable code. They are the foundation of many functional programming patterns and are used extensively throughout the Kotlin standard library.

### Higher Order Function Example

```kotlin
/**
 * Simple higher-order function - repeats an action n times
 * The action parameter is a function that takes no parameters and returns Unit
 */
fun doNTimes(n: Int, action: () -> Unit) {
    for (i in 1..n) {
        action()
    }
}

/**
 * Higher-order function with parameter in the lambda
 * Executes action for each number from 1 to n, passing the number to the action
 */
fun repeatWithIndex(n: Int, action: (Int) -> Unit) {
    for (i in 1..n) {
        action(i)
    }
}

/**
 * Higher-order function that filters a list based on a predicate
 * The filter parameter is a function that takes a grade and returns Boolean
 * This demonstrates how we can make operations customizable
 */
fun filteredGrades(grades: List<Int>, filter: (Int) -> Boolean): List<Int> {
    val result = mutableListOf<Int>()
    for (grade in grades) {
        if (filter(grade)) {
            result.add(grade)
        }
    }
    return result
}

/**
 * Higher-order function that transforms each element
 * This is a generic function that works with any types
 */
fun <T, R> transformList(list: List<T>, transformer: (T) -> R): List<R> {
    val result = mutableListOf<R>()
    for (item in list) {
        result.add(transformer(item))
    }
    return result
}

/**
 * Higher-order function for calculation with custom operation
 * This shows how we can make a calculator that accepts any operation
 */
fun calculate(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

/**
 * Higher-order function that returns a function
 * This demonstrates function composition and factory patterns
 */
fun createMultiplier(factor: Int): (Int) -> Int {
    return { number -> number * factor }
}

/**
 * Higher-order function for retry logic
 * This is a practical example of how higher-order functions enable reusable patterns
 */
fun <T> retry(times: Int, action: () -> T): T? {
    var lastException: Exception? = null
    
    repeat(times) { attempt ->
        try {
            return action()
        } catch (e: Exception) {
            lastException = e
            println("Attempt ${attempt + 1} failed: ${e.message}")
        }
    }
    
    return null
}

/**
 * Main function demonstrating higher-order functions
 */
fun main() {
    println("Higher-Order Functions in Kotlin:\n")
    
    // 1. Basic higher-order function
    println("1. Repeating an action:")
    doNTimes(3) {
        println("   Hello!")
    }
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 2. Higher-order function with indexed action
    println("2. Repeating with index:")
    repeatWithIndex(5) { index ->
        println("   Iteration $index")
    }
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 3. Filtering with different predicates
    println("3. Filtering grades with custom predicates:")
    val grades = listOf(45, 55, 62, 73, 81, 49, 68, 92, 38, 77)
    println("   All grades: $grades")
    
    // Filter passing grades (>= 50)
    val passing = filteredGrades(grades) { it >= 50 }
    println("   Passing (>= 50): $passing")
    
    // Filter distinction grades (>= 70)
    val distinction = filteredGrades(grades) { it >= 70 }
    println("   Distinction (>= 70): $distinction")
    
    // Filter failing grades (< 50)
    val failing = filteredGrades(grades) { it < 50 }
    println("   Failing (< 50): $failing")
    
    // Using 'it' shorthand
    val highScores = filteredGrades(grades) { it > 80 }
    println("   High scores (> 80): $highScores")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 4. Transform list with different transformations
    println("4. Transforming lists:")
    val numbers = listOf(1, 2, 3, 4, 5)
    println("   Original: $numbers")
    
    val squared = transformList(numbers) { it * it }
    println("   Squared: $squared")
    
    val doubled = transformList(numbers) { it * 2 }
    println("   Doubled: $doubled")
    
    val asStrings = transformList(numbers) { "Number: $it" }
    println("   As strings: $asStrings")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 5. Calculator with different operations
    println("5. Calculator with custom operations:")
    val x = 10
    val y = 5
    
    val sum = calculate(x, y) { a, b -> a + b }
    println("   $x + $y = $sum")
    
    val product = calculate(x, y) { a, b -> a * b }
    println("   $x * $y = $product")
    
    val max = calculate(x, y) { a, b -> if (a > b) a else b }
    println("   max($x, $y) = $max")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 6. Function that returns a function
    println("6. Creating custom multiplier functions:")
    val double = createMultiplier(2)
    val triple = createMultiplier(3)
    val timesTen = createMultiplier(10)
    
    val value = 7
    println("   double($value) = ${double(value)}")
    println("   triple($value) = ${triple(value)}")
    println("   timesTen($value) = ${timesTen(value)}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 7. Practical example - processing student data
    println("7. Student data processing with higher-order functions:")
    
    data class Student(val name: String, val mark: Int)
    
    fun processStudents(
        students: List<Student>,
        filter: (Student) -> Boolean,
        transform: (Student) -> String
    ): List<String> {
        return students
            .filter(filter)
            .map(transform)
    }
    
    val students = listOf(
        Student("Alice", 85),
        Student("Bob", 72),
        Student("Charlie", 45),
        Student("Diana", 91),
        Student("Eve", 68)
    )
    
    // Get formatted strings for passing students
    val passingStudents = processStudents(
        students,
        { it.mark >= 50 },
        { "${it.name}: ${it.mark} points" }
    )
    
    println("   Passing students:")
    passingStudents.forEach { println("      $it") }
    
    // Get names of top performers
    val topPerformers = processStudents(
        students,
        { it.mark >= 70 },
        { "${it.name} (${it.mark})" }
    )
    
    println("\n   Top performers (70+):")
    topPerformers.forEach { println("      $it") }
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 8. Combining higher-order functions
    println("8. Chaining operations:")
    
    val scores = listOf(45, 67, 89, 34, 72, 91, 55, 78)
    
    val result = scores
        .filter { it >= 50 }
        .map { it * 2 }
        .sortedDescending()
        .take(3)
    
    println("   Original: $scores")
    println("   After: filter (>= 50), double, sort desc, take 3")
    println("   Result: $result")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // 9. Retry logic example
    println("9. Retry logic with higher-order function:")
    
    var attemptCount = 0
    val result2 = retry(3) {
        attemptCount++
        if (attemptCount < 3) {
            throw Exception("Not ready yet")
        }
        "Success on attempt $attemptCount!"
    }
    
    println("   Final result: $result2")
}
```

**Sample Output:**
```
Higher-Order Functions in Kotlin:

1. Repeating an action:
   Hello!
   Hello!
   Hello!

============================================================

2. Repeating with index:
   Iteration 1
   Iteration 2
   Iteration 3
   Iteration 4
   Iteration 5

============================================================

3. Filtering grades with custom predicates:
   All grades: [45, 55, 62, 73, 81, 49, 68, 92, 38, 77]
   Passing (>= 50): [55, 62, 73, 81, 68, 92, 77]
   Distinction (>= 70): [73, 81, 92, 77]
   Failing (< 50): [45, 49, 38]
   High scores (> 80): [81, 92]

============================================================

4. Transforming lists:
   Original: [1, 2, 3, 4, 5]
   Squared: [1, 4, 9, 16, 25]
   Doubled: [2, 4, 6, 8, 10]
   As strings: [Number: 1, Number: 2, Number: 3, Number: 4, Number: 5]

============================================================

5. Calculator with custom operations:
   10 + 5 = 15
   10 * 5 = 50
   max(10, 5) = 10

============================================================

6. Creating custom multiplier functions:
   double(7) = 14
   triple(7) = 21
   timesTen(7) = 70

============================================================

7. Student data processing with higher-order functions:
   Passing students:
      Alice: 85 points
      Bob: 72 points
      Diana: 91 points
      Eve: 68 points

   Top performers (70+):
      Alice (85)
      Bob (72)
      Diana (91)

============================================================

8. Chaining operations:
   Original: [45, 67, 89, 34, 72, 91, 55, 78]
   After: filter (>= 50), double, sort desc, take 3
   Result: [182, 178, 156]

============================================================

9. Retry logic with higher-order function:
Attempt 1 failed: Not ready yet
Attempt 2 failed: Not ready yet
   Final result: Success on attempt 3!
```

### The Power of Higher-Order Functions

Higher-order functions enable you to write more abstract and reusable code by separating what you want to do from how you want to do it. Instead of writing multiple similar functions that differ only in one operation, you can write a single higher-order function and pass different operations as parameters. This leads to code that is both more concise and more flexible.

The ability to compose functions by chaining higher-order operations is particularly powerful in data processing scenarios. You can build complex transformations by combining simple, well-tested operations. This approach is not only more readable but also easier to maintain and extend. Higher-order functions are extensively used throughout the Kotlin standard library in collection operations, scope functions, and many other areas, making them essential to writing idiomatic Kotlin code.

---

## Section 6: Basic Class Syntax (6 minutes)

### Understanding Kotlin Classes

Now that we have explored how Kotlin treats functions as first-class citizens and enables powerful functional programming patterns, we turn our attention to classes and object-oriented programming. You will see how the functional concepts we just learned integrate beautifully with Kotlin's approach to classes, particularly when we discuss extension functions later.

Kotlin classes are declared using the `class` keyword, and unlike Java, they are public by default. This reduces boilerplate since you do not need to write `public` for every class. One of the key requirements that distinguishes Kotlin from Java is that all properties must be initialized either at declaration, in the constructor, or within an init block. This requirement ensures that objects are always in a valid state when created, eliminating a whole class of potential bugs.

Properties in Kotlin can be declared using `var` for mutable properties or `val` for immutable properties. When you declare a property at the class level using these keywords, Kotlin automatically generates backing fields and accessor methods, which we will explore in more detail shortly.

### Basic Class Example

```kotlin
/**
 * A simple Contact class demonstrating basic Kotlin class syntax
 * All properties must be initialized to ensure objects are in valid state
 */
class Contact {
    // Mutable properties with default values
    var name = ""
    var email = ""
    var phone = ""
    
    /**
     * A method to display contact information
     * Methods in Kotlin classes work just like functions
     */
    fun displayInfo() {
        println("Contact: $name")
        println("Email: $email")
        println("Phone: $phone")
    }
    
    /**
     * A method to validate if contact has required information
     * Returns Boolean indicating validity
     */
    fun isValid(): Boolean {
        return name.isNotEmpty() && (email.isNotEmpty() || phone.isNotEmpty())
    }
}

/**
 * Main function demonstrating the Contact class
 */
fun main() {
    // Creating an instance of Contact
    val contact = Contact()
    
    // Setting properties - Kotlin allows direct property access
    contact.name = "Alice Johnson"
    contact.email = "alice@example.com"
    contact.phone = "+1-555-0123"
    
    // Using methods
    contact.displayInfo()
    println("\nIs valid contact? ${contact.isValid()}")
}
```

**Sample Output:**
```
Contact: Alice Johnson
Email: alice@example.com
Phone: +1-555-0123

Is valid contact? true
```

### Key Points About Basic Classes

In this example, we see three properties being declared with the `var` keyword, which makes them mutable. Each property is initialized with an empty string, satisfying Kotlin's requirement that all properties must have initial values. The class contains two methods that demonstrate how to work with these properties, using string interpolation to create formatted output.

Notice that we access properties directly using dot notation rather than calling getter and setter methods as you would in Java. This is because Kotlin generates these methods automatically behind the scenes. Classes and methods in Kotlin are public by default, so we do not need to explicitly declare them as public. If you wanted to create private fields similar to Java's instance variables, you would use the `private` modifier before the property declaration.

---

## Section 7: Getters and Setters (5 minutes)

### Understanding Property Access

In Kotlin, when you declare a property using `var` or `val`, the language automatically generates both getter and setter methods for `var` properties, or just a getter for `val` properties. This is fundamentally different from Java, where you must explicitly create these methods. However, Kotlin gives you complete flexibility to customize this behavior when your requirements go beyond simple property access.

A common requirement in object-oriented programming is to have a property that can be read publicly but only modified internally within the class. Kotlin provides an elegant solution through the `private set` modifier, which makes the setter private while keeping the getter public. You can also create completely custom getters and setters when you need to add validation, transformation, or other logic.

### Custom Getters and Setters Example

```kotlin
/**
 * A User class demonstrating custom getters and setters
 * This shows various patterns for controlling property access
 */
class User {
    // Property with custom getter and setter
    // The getter transforms the value, setter validates it
    var name: String = ""
        get() = field.uppercase() // Always return uppercase
        set(value) {
            field = value.trim() // Remove whitespace before storing
        }
    
    // Property with private setter - can only be modified within the class
    var accountBalance: Double = 0.0
        private set
    
    // Computed property - no backing field, value calculated on each access
    val displayName: String
        get() = "User: $name"
    
    // Property with validation in setter
    var age: Int = 0
        set(value) {
            if (value >= 0) {
                field = value
            } else {
                println("Age cannot be negative. Setting to 0.")
                field = 0
            }
        }
    
    /**
     * Public method to deposit money - the only way to modify accountBalance
     * This demonstrates encapsulation
     */
    fun deposit(amount: Double) {
        if (amount > 0) {
            accountBalance += amount
            println("Deposited $$amount. New balance: $$accountBalance")
        }
    }
    
    /**
     * Public method to withdraw money
     * Returns true if successful, false otherwise
     */
    fun withdraw(amount: Double): Boolean {
        return if (amount > 0 && amount <= accountBalance) {
            accountBalance -= amount
            println("Withdrew $$amount. New balance: $$accountBalance")
            true
        } else {
            println("Insufficient funds or invalid amount")
            false
        }
    }
}

/**
 * Main function demonstrating custom getters and setters
 */
fun main() {
    val user = User()
    
    // Name is automatically trimmed and returned in uppercase
    user.name = "  john doe  "
    println("Name: ${user.name}") // Prints "JOHN DOE"
    
    // Display name is computed on each access
    println(user.displayName)
    
    // Age validation
    user.age = 25
    println("Age: ${user.age}")
    user.age = -5 // This will print a warning and set age to 0
    println("Age after invalid set: ${user.age}")
    
    // Account balance can only be modified through deposit/withdraw methods
    user.deposit(100.0)
    user.withdraw(30.0)
    println("Final balance: $${user.accountBalance}")
    
    // This would cause a compilation error:
    // user.accountBalance = 500.0 // Cannot assign - setter is private
}
```

**Sample Output:**
```
Name: JOHN DOE
User: JOHN DOE
Age: 25
Age cannot be negative. Setting to 0.
Age after invalid set: 0
Deposited $100.0. New balance: $100.0
Withdrew $30.0. New balance: $70.0
Final balance: $70.0
```

### Understanding Backing Fields and Custom Accessors

When you create custom getters or setters, Kotlin provides the `field` keyword to access the backing field, which is the actual storage location for the property's value. This is necessary because using the property name directly would create recursive calls to the getter or setter. The convention in setters is to use `value` as the parameter name, which represents the new value being assigned to the property.

Computed properties like `displayName` in our example do not have backing fields at all because their values are calculated on each access rather than stored. This is efficient for values that are simple transformations of other properties. An important rule to remember is that properties with custom getters and setters must be initialized inline rather than in constructors or init blocks. This ensures the custom logic is applied from the very beginning of the property's lifetime.

The `private set` pattern is particularly useful for implementing encapsulation, where you want to expose the current state of an object for reading but control how that state changes through specific methods that can enforce business rules and validation.

---

## Section 8: Primary Constructor (5 minutes)

### Understanding Primary Constructors

The primary constructor is a distinctive feature of Kotlin that allows you to declare and initialize properties directly in the class header, significantly reducing boilerplate code compared to Java. When you place `var` or `val` before a constructor parameter, that parameter automatically becomes a class property with the appropriate visibility and mutability.

However, the primary constructor comes with an important limitation: it cannot contain any executable code beyond simple parameter assignments. This is because the primary constructor is not a separate code block but rather part of the class header. If you need to perform operations or calculations during initialization, you will need to use init blocks, which we will discuss in the next section, or secondary constructors for more complex initialization scenarios.

### Primary Constructor Example

```kotlin
/**
 * Contact class using primary constructor
 * Properties declared in the constructor automatically become class properties
 */
class Contact(var name: String) {
    // Additional properties declared in the class body
    var email = ""
    var phone = ""
    
    /**
     * Method to format contact information
     * This demonstrates mixing constructor properties with body properties
     */
    fun getFormattedInfo(): String {
        return """
            Name: $name
            Email: ${email.ifEmpty { "Not provided" }}
            Phone: ${phone.ifEmpty { "Not provided" }}
        """.trimIndent()
    }
}

/**
 * A more complete example with multiple constructor parameters
 * This shows the full power of primary constructors
 */
class Employee(
    var firstName: String,
    var lastName: String,
    var employeeId: Int
) {
    // Additional properties not in primary constructor
    var department: String = "Unassigned"
    var salary: Double = 0.0
    
    // Computed property using constructor parameters
    val fullName: String
        get() = "$firstName $lastName"
    
    /**
     * Method to display employee information
     * Demonstrates using both constructor and body properties
     */
    fun displayEmployee() {
        println("Employee ID: $employeeId")
        println("Name: $fullName")
        println("Department: $department")
        println("Salary: $$salary")
    }
}

/**
 * Main function demonstrating primary constructors
 */
fun main() {
    // Creating a Contact with primary constructor
    val contact1 = Contact("Sarah Williams")
    contact1.email = "sarah@example.com"
    contact1.phone = "555-0100"
    
    println("Contact Information:")
    println(contact1.getFormattedInfo())
    
    println("\n" + "=".repeat(40) + "\n")
    
    // Creating an Employee with multiple constructor parameters
    val employee = Employee("Michael", "Chen", 12345)
    employee.department = "Engineering"
    employee.salary = 75000.0
    
    println("Employee Information:")
    employee.displayEmployee()
    
    // Accessing and modifying properties directly
    employee.firstName = "Mike" // We can change it because it's a var
    println("\nUpdated name: ${employee.fullName}")
}
```

**Sample Output:**
```
Contact Information:
Name: Sarah Williams
Email: sarah@example.com
Phone: 555-0100

========================================

Employee Information:
Employee ID: 12345
Name: Michael Chen
Department: Engineering
Salary: $75000.0

Updated name: Mike Chen
```

### When to Use Primary Constructors

Primary constructors are excellent when you have properties that need to be set at object creation time and do not require any complex initialization logic. They make your class declarations extremely concise while still being clear about what data the class holds. The combination of constructor parameters and class body properties gives you flexibility in deciding which properties are required at construction time and which can have default values.

However, primary constructors have limitations that you need to be aware of. You cannot perform operations or calculations within the primary constructor itself. If you need immutable properties whose values must be computed differently based on how the object is created, or if you need multiple ways to construct an object with different initialization logic, you may need to combine primary constructors with init blocks or use secondary constructors instead. These patterns give you the full power of Java constructors while maintaining Kotlin's concise syntax.

---

## Section 9: Init Blocks (4 minutes)

### Understanding Init Blocks

Init blocks provide a way to execute initialization code that cannot be placed directly in the primary constructor. These blocks run immediately after the primary constructor completes and have access to all constructor parameters and properties. You can have multiple init blocks in a class, and they execute in the order they appear in the code, interleaved with property initializers.

Init blocks are particularly useful for complex initialization logic, validation, or setting up computed properties that depend on constructor parameters. They bridge the gap between the simple parameter assignments of the primary constructor and the more complex initialization that might be needed for real-world objects.

### Init Block Example

```kotlin
import java.time.LocalDate
import java.time.Period

/**
 * Person class demonstrating init blocks
 * Shows how to perform complex initialization after construction
 */
class Person(val firstName: String, val lastName: String, val birthYear: Int) {
    // Properties that will be set in init blocks
    val fullName: String
    val age: Int
    
    // First init block - executes first
    init {
        fullName = "$firstName $lastName"
        println("Initializing person: $fullName")
    }
    
    // Second init block - executes after the first
    init {
        val currentYear = LocalDate.now().year
        age = currentYear - birthYear
        println("Calculated age: $age years")
        
        // Validation logic can be placed here
        if (age < 0) {
            throw IllegalArgumentException("Birth year cannot be in the future!")
        }
        if (age > 150) {
            println("Warning: Age seems unusually high. Please verify birth year.")
        }
    }
    
    /**
     * Method to display person information
     * Shows that init blocks have completed before methods are called
     */
    fun introduce() {
        println("Hello, I'm $fullName and I'm $age years old.")
    }
}

/**
 * BankAccount class showing init block for setup logic
 * Demonstrates validation during initialization
 */
class BankAccount(val accountNumber: String, initialBalance: Double) {
    var balance: Double = 0.0
        private set
    
    var transactionCount: Int = 0
        private set
    
    // Init block performs validation and initialization
    init {
        require(initialBalance >= 0) { 
            "Initial balance cannot be negative. Provided: $initialBalance" 
        }
        
        balance = initialBalance
        transactionCount = 0
        
        println("Account $accountNumber created with initial balance: $$balance")
    }
    
    /**
     * Method to deposit money
     * Demonstrates how init blocks set up valid initial state
     */
    fun deposit(amount: Double) {
        balance += amount
        transactionCount++
    }
}

/**
 * Main function demonstrating init blocks
 */
fun main() {
    println("Creating Person objects:\n")
    
    // Creating a person - watch init blocks execute in order
    val person1 = Person("Emma", "Watson", 1990)
    println()
    person1.introduce()
    
    println("\n" + "=".repeat(50) + "\n")
    
    // Creating another person to see the pattern again
    val person2 = Person("Daniel", "Radcliffe", 1989)
    println()
    person2.introduce()
    
    println("\n" + "=".repeat(50) + "\n")
    
    // Bank account example showing validation
    println("Creating Bank Accounts:\n")
    val account = BankAccount("ACC-001", 1000.0)
    account.deposit(250.0)
    println("Balance after deposit: $${account.balance}")
    println("Total transactions: ${account.transactionCount}")
    
    // Trying to create account with invalid initial balance
    try {
        val invalidAccount = BankAccount("ACC-002", -100.0)
    } catch (e: IllegalArgumentException) {
        println("\nError creating account: ${e.message}")
    }
}
```

**Sample Output:**
```
Creating Person objects:

Initializing person: Emma Watson
Calculated age: 35 years

Hello, I'm Emma Watson and I'm 35 years old.

==================================================

Initializing person: Daniel Radcliffe
Calculated age: 36 years

Hello, I'm Daniel Radcliffe and I'm 36 years old.

==================================================

Creating Bank Accounts:

Account ACC-001 created with initial balance: $1000.0
Balance after deposit: $1250.0
Total transactions: 1

Error creating account: Initial balance cannot be negative. Provided: -100.0
```

### Best Practices for Init Blocks

Init blocks are ideal for complex initialization logic that goes beyond simple property assignment. They execute in declaration order, which can be useful when later initialization depends on earlier setup. You can use init blocks for validation that should prevent object creation if certain conditions are not met, as we saw with the bank account example where negative balances are rejected.

However, keep init blocks focused and avoid overly complex logic that might be better placed in separate methods. If you find yourself writing very long init blocks, consider whether that logic should be extracted into private initialization methods that the init block calls. Remember that init blocks run every time an object is created, so any expensive operations placed there will impact the performance of object construction.

---

## Section 10: Secondary Constructors (5 minutes)

### Understanding Secondary Constructors

While the primary constructor is declared in the class header, secondary constructors are declared within the class body using the `constructor` keyword. Secondary constructors provide flexibility when you need multiple ways to create an object with different sets of parameters. They are particularly useful for providing convenience constructors that set sensible defaults or perform different initialization based on what information is provided.

An important rule governs secondary constructors: if a primary constructor exists, every secondary constructor must call it either directly or indirectly through another secondary constructor using the `this` keyword. This ensures that the primary constructor's initialization always occurs, maintaining consistency in how objects are created. Secondary constructors can contain additional logic beyond simple property assignments, making them more flexible than primary constructors.

### Secondary Constructor Example

```kotlin
import java.time.LocalDateTime
import java.time.format.DateTimeFormatter

/**
 * Contact class with primary and secondary constructors
 * Demonstrates constructor chaining and convenience constructors
 */
class Contact(var name: String) {
    var email: String = ""
    var phone: String = ""
    var address: String = ""
    var createdAt: String = ""
    
    // Secondary constructor with name and email
    // Must call primary constructor with 'this'
    constructor(name: String, email: String) : this(name) {
        this.email = email
        this.createdAt = LocalDateTime.now().format(DateTimeFormatter.ISO_LOCAL_DATE_TIME)
        println("Contact created with name and email")
    }
    
    // Secondary constructor with name, email, and phone
    // Calls the secondary constructor above, which then calls primary
    constructor(name: String, email: String, phone: String) : this(name, email) {
        this.phone = phone
        println("Contact created with name, email, and phone")
    }
    
    // Secondary constructor with all fields
    // Creates a complete chain of constructor calls
    constructor(name: String, email: String, phone: String, address: String) 
            : this(name, email, phone) {
        this.address = address
        println("Contact created with all details")
    }
    
    /**
     * Display contact information
     * Shows how all constructors lead to valid objects
     */
    fun display() {
        println("""
            Contact Details:
            Name: $name
            Email: ${email.ifEmpty { "N/A" }}
            Phone: ${phone.ifEmpty { "N/A" }}
            Address: ${address.ifEmpty { "N/A" }}
            Created: ${createdAt.ifEmpty { "N/A" }}
        """.trimIndent())
    }
}

/**
 * Rectangle class demonstrating constructors without primary constructor
 * Shows that primary constructors are optional
 */
class Rectangle {
    var width: Double = 0.0
    var height: Double = 0.0
    
    // Constructor for square (width = height)
    constructor(side: Double) {
        this.width = side
        this.height = side
        println("Created square with side: $side")
    }
    
    // Constructor for rectangle with different dimensions
    constructor(width: Double, height: Double) {
        this.width = width
        this.height = height
        println("Created rectangle with width: $width, height: $height")
    }
    
    /**
     * Calculate area of the rectangle
     */
    fun area(): Double = width * height
    
    /**
     * Calculate perimeter of the rectangle
     */
    fun perimeter(): Double = 2 * (width + height)
    
    /**
     * Check if this is a square
     */
    fun isSquare(): Boolean = width == height
}

/**
 * Main function demonstrating secondary constructors
 */
fun main() {
    println("Creating contacts with different constructors:\n")
    
    // Using primary constructor only
    val contact1 = Contact("Alice Johnson")
    contact1.email = "alice@example.com"
    println()
    contact1.display()
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Using secondary constructor with name and email
    val contact2 = Contact("Bob Smith", "bob@example.com")
    println()
    contact2.display()
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Using secondary constructor with name, email, and phone
    val contact3 = Contact("Carol White", "carol@example.com", "555-0123")
    println()
    contact3.display()
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Using secondary constructor with all fields
    val contact4 = Contact(
        "David Brown", 
        "david@example.com", 
        "555-0124",
        "123 Main St"
    )
    println()
    contact4.display()
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Rectangle examples showing constructors without primary
    println("Creating shapes:\n")
    val square = Rectangle(5.0)
    println("Area: ${square.area()}, Perimeter: ${square.perimeter()}, Is square? ${square.isSquare()}")
    
    println()
    
    val rectangle = Rectangle(4.0, 6.0)
    println("Area: ${rectangle.area()}, Perimeter: ${rectangle.perimeter()}, Is square? ${rectangle.isSquare()}")
}
```

**Sample Output:**
```
Creating contacts with different constructors:

Contact created with name and email

Contact Details:
Name: Alice Johnson
Email: N/A
Phone: N/A
Address: N/A
Created: N/A

============================================================

Contact created with name and email

Contact Details:
Name: Bob Smith
Email: bob@example.com
Phone: N/A
Address: N/A
Created: 2025-10-23T14:30:45.123

============================================================

Contact created with name and email
Contact created with name, email, and phone

Contact Details:
Name: Carol White
Email: carol@example.com
Phone: 555-0123
Address: N/A
Created: 2025-10-23T14:30:45.456

============================================================

Contact created with name and email
Contact created with name, email, and phone
Contact created with all details

Contact Details:
Name: David Brown
Email: david@example.com
Phone: 555-0124
Address: 123 Main St
Created: 2025-10-23T14:30:45.789

============================================================

Creating shapes:

Created square with side: 5.0
Area: 25.0, Perimeter: 20.0, Is square? true

Created rectangle with width: 4.0, height: 6.0
Area: 24.0, Perimeter: 20.0, Is square? false
```

### When to Use Secondary Constructors

Secondary constructors are particularly useful when you need convenience constructors that provide different ways to initialize an object based on what information is available. They allow you to create a chain of constructor calls, where each constructor adds more initialization logic while ensuring that the fundamental initialization from the primary constructor always occurs.

This pattern is common when you want to provide sensible defaults while still allowing full customization. The constructor chaining we see in the Contact example shows how each level of detail builds upon the previous one, creating a natural hierarchy of initialization options. The Rectangle example demonstrates that primary constructors are actually optional in Kotlin, though they are preferred when possible for their conciseness.

---

## Section 11: Data Classes (4 minutes)

### Understanding Data Classes

Data classes are a special type of class in Kotlin designed specifically for holding data rather than behavior. When you prefix a class with the `data` keyword, Kotlin automatically generates several useful methods based on the properties declared in the primary constructor. These include `equals()` for value-based equality comparison, `hashCode()` for use in collections, `toString()` for readable string representation, and a `copy()` method for creating modified copies of objects.

The requirements for data classes are straightforward: they must have a primary constructor with at least one parameter, all primary constructor parameters must be marked as `val` or `var`, and data classes cannot be open for inheritance. These restrictions ensure that data classes maintain their intended simple, data-focused nature.

### Data Class Example

```kotlin
/**
 * Data class representing a user account
 * Automatically generates equals, hashCode, toString, and copy methods
 */
data class User(
    val id: Int,
    val username: String,
    val email: String,
    var isActive: Boolean = true
)

/**
 * Data class for representing a product
 * Shows mixing val and var properties
 */
data class Product(
    val id: String,
    val name: String,
    val price: Double,
    val category: String,
    var quantity: Int = 0
)

/**
 * Data class for coordinate points
 * Demonstrates that data classes can have additional methods
 */
data class Point(val x: Double, val y: Double) {
    // Data classes can have additional methods in the body
    fun distanceFromOrigin(): Double {
        return kotlin.math.sqrt(x * x + y * y)
    }
}

/**
 * Regular class for comparison (without data keyword)
 * Shows what we would need to implement manually without data classes
 */
class RegularUser(val id: Int, val username: String)

/**
 * Main function demonstrating data class features
 */
fun main() {
    println("Data Class Demonstrations:\n")
    
    // Creating data class instances
    val user1 = User(1, "alice123", "alice@example.com")
    val user2 = User(1, "alice123", "alice@example.com")
    val user3 = User(2, "bob456", "bob@example.com", false)
    
    // Automatic toString() - produces readable output
    println("User 1: $user1")
    println("User 2: $user2")
    println("User 3: $user3")
    
    // Automatic equals() - compares by value, not reference
    println("\nEquality comparisons:")
    println("user1 == user2: ${user1 == user2}") // true - same values
    println("user1 === user2: ${user1 === user2}") // false - different objects
    println("user1 == user3: ${user1 == user3}") // false - different values
    
    // Automatic hashCode() - consistent with equals()
    println("\nHash codes:")
    println("user1.hashCode(): ${user1.hashCode()}")
    println("user2.hashCode(): ${user2.hashCode()}")
    println("user3.hashCode(): ${user3.hashCode()}")
    
    // The copy() method - create modified copies
    println("\nUsing copy() method:")
    val user4 = user1.copy(isActive = false)
    println("Original: $user1")
    println("Modified copy: $user4")
    
    // Copy with multiple changes
    val user5 = user1.copy(id = 5, username = "alice_new")
    println("Another copy: $user5")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Product example demonstrating copy with mutable property
    val product1 = Product("P001", "Laptop", 999.99, "Electronics", 5)
    val product2 = product1.copy(quantity = 10)
    
    println("Original product: $product1")
    println("Updated quantity: $product2")
    
    // Destructuring - automatic component functions
    println("\nDestructuring:")
    val (id, name, price, category, qty) = product1
    println("ID: $id, Name: $name, Price: $$price, Category: $category, Qty: $qty")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Point example with additional method
    val point1 = Point(3.0, 4.0)
    val point2 = Point(3.0, 4.0)
    
    println("Point 1: $point1")
    println("Point 2: $point2")
    println("Are equal: ${point1 == point2}")
    println("Distance from origin: ${point1.distanceFromOrigin()}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Comparison with regular class
    println("Regular class vs Data class:")
    val regUser1 = RegularUser(1, "charlie")
    val regUser2 = RegularUser(1, "charlie")
    
    println("Regular class toString(): $regUser1") // Shows object reference
    println("Regular class equality: ${regUser1 == regUser2}") // false - compares references
    
    val dataUser1 = User(1, "charlie", "charlie@example.com")
    val dataUser2 = User(1, "charlie", "charlie@example.com")
    
    println("Data class toString(): $dataUser1") // Shows property values
    println("Data class equality: ${dataUser1 == dataUser2}") // true - compares values
}
```

**Sample Output:**
```
Data Class Demonstrations:

User 1: User(id=1, username=alice123, email=alice@example.com, isActive=true)
User 2: User(id=1, username=alice123, email=alice@example.com, isActive=true)
User 3: User(id=2, username=bob456, email=bob@example.com, isActive=false)

Equality comparisons:
user1 == user2: true
user1 === user2: false
user1 == user3: false

Hash codes:
user1.hashCode(): 1234567890
user2.hashCode(): 1234567890
user3.hashCode(): 987654321

Using copy() method:
Original: User(id=1, username=alice123, email=alice@example.com, isActive=true)
Modified copy: User(id=1, username=alice123, email=alice@example.com, isActive=false)
Another copy: User(id=5, username=alice_new, email=alice@example.com, isActive=true)

============================================================

Original product: Product(id=P001, name=Laptop, price=999.99, category=Electronics, quantity=5)
Updated quantity: Product(id=P001, name=Laptop, price=999.99, category=Electronics, quantity=10)

Destructuring:
ID: P001, Name: Laptop, Price: $999.99, Category: Electronics, Qty: 5

============================================================

Point 1: Point(x=3.0, y=4.0)
Point 2: Point(x=3.0, y=4.0)
Are equal: true
Distance from origin: 5.0

============================================================

Regular class vs Data class:
Regular class toString(): RegularUser@15db9742
Regular class equality: false
Data class toString(): User(id=1, username=charlie, email=charlie@example.com, isActive=true)
Data class equality: true
```

### Benefits of Data Classes

Data classes dramatically reduce boilerplate code while providing powerful functionality that would require significant manual implementation in Java or with regular Kotlin classes. The automatically generated methods work correctly with collections, making data classes perfect for storing objects in sets or using them as map keys. The hash code implementation is consistent with the equals method, which is a requirement for correct behavior in hash-based collections.

The copy method is particularly useful for creating modified versions of immutable objects, which is a common pattern in functional programming. Instead of changing properties directly, you create a new instance with the desired changes while leaving the original untouched. Destructuring declarations allow you to extract all properties from a data class into separate variables in a single statement, making it easy to work with the individual components of the data.

---

## Section 12: Extension Functions and Properties (5 minutes)

### Understanding Extensions

Extension functions are one of Kotlin's most powerful features, allowing you to add new functions to existing classes without modifying their source code or inheriting from them. This capability is particularly useful when working with classes from libraries or the standard library that you cannot modify. You create an extension function by prepending the class name to the function name with a dot, essentially telling Kotlin which class you are extending.

Extension properties work similarly to extension functions but have a limitation: they cannot have backing fields, so they are typically used for computed values based on the object's existing properties. Extensions can access the public members of the class they extend but not private or protected members, maintaining proper encapsulation.

This feature brings together the functional and object-oriented concepts we have learned in this lecture. Extensions allow you to add functionality to classes while keeping that functionality organized separately from the class definition itself.

### Extension Function Example

```kotlin
/**
 * Extension function to check if an Int is a valid hour of the day
 * This extends the Int class without modifying it
 */
fun Int.isValidHourOfDay(): Boolean {
    return this in 0..23
}

/**
 * Extension function to check if a String is a valid email format
 * This is a simplified check for demonstration purposes
 */
fun String.isValidEmail(): Boolean {
    return this.contains("@") && this.contains(".") && this.length >= 5
}

/**
 * Extension function to capitalize first letter of each word
 * Demonstrates string manipulation through extensions
 */
fun String.toTitleCase(): String {
    return this.split(" ")
        .joinToString(" ") { word ->
            word.replaceFirstChar { 
                if (it.isLowerCase()) it.titlecase() else it.toString() 
            }
        }
}

/**
 * Extension property for String - get initials
 * Shows how computed properties work as extensions
 */
val String.initials: String
    get() {
        val initials = StringBuilder()
        var spaceFound = true
        
        for (i in this.indices) {
            if (this[i] == ' ') {
                spaceFound = true
            } else if (spaceFound) {
                initials.append(this[i].uppercase())
                spaceFound = false
            }
        }
        
        return initials.toString()
    }

/**
 * Extension property for Int - calculate negative value
 * Demonstrates simple computed extension properties
 */
val Int.negative: Int
    get() = -this

/**
 * Extension property to get penalty (for demonstration)
 * Shows how extension properties can combine with other extensions
 */
val Int.penalty: Int
    get() = 10 * this.negative

/**
 * Extension function for List<Int> to calculate average
 * Demonstrates extending generic types
 */
fun List<Int>.average(): Double {
    return if (this.isEmpty()) 0.0 else this.sum().toDouble() / this.size
}

/**
 * Extension function for Double to format as currency
 * Shows practical utility extensions
 */
fun Double.toCurrency(): String {
    return "$%.2f".format(this)
}

/**
 * Main function demonstrating extension functions and properties
 */
fun main() {
    println("Extension Function Demonstrations:\n")
    
    // Int extension - valid hour check
    println("Hour validation:")
    val hour1 = 14
    val hour2 = 25
    println("Is $hour1 a valid hour? ${hour1.isValidHourOfDay()}")
    println("Is $hour2 a valid hour? ${hour2.isValidHourOfDay()}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // String extension - email validation
    println("Email validation:")
    val email1 = "user@example.com"
    val email2 = "invalid-email"
    println("Is '$email1' valid? ${email1.isValidEmail()}")
    println("Is '$email2' valid? ${email2.isValidEmail()}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // String extension - title case
    println("Title case conversion:")
    val text = "the quick brown fox jumps over the lazy dog"
    println("Original: $text")
    println("Title case: ${text.toTitleCase()}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Extension property - initials
    println("Getting initials:")
    val fullName = "John Ronald Reuel Tolkien"
    println("Name: $fullName")
    println("Initials: ${fullName.initials}")
    
    val anotherName = "Alice Bob Carol"
    println("Name: $anotherName")
    println("Initials: ${anotherName.initials}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Extension property - negative and penalty
    println("Negative and penalty calculations:")
    val value = 10
    println("Value: $value")
    println("Negative: ${value.negative}")
    println("Penalty: ${value.penalty}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // List extension - average
    println("List operations:")
    val numbers = listOf(10, 20, 30, 40, 50)
    println("Numbers: $numbers")
    println("Average: ${numbers.average()}")
    
    val emptyList = emptyList<Int>()
    println("Empty list average: ${emptyList.average()}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Double extension - currency formatting
    println("Currency formatting:")
    val price1 = 19.99
    val price2 = 1234.5
    val price3 = 0.99
    
    println("Price 1: ${price1.toCurrency()}")
    println("Price 2: ${price2.toCurrency()}")
    println("Price 3: ${price3.toCurrency()}")
    
    println("\n" + "=".repeat(60) + "\n")
    
    // Practical example combining extensions
    println("Practical example - user input validation:")
    
    data class UserInput(val name: String, val email: String, val age: Int)
    
    val inputs = listOf(
        UserInput("john doe", "john@example.com", 25),
        UserInput("jane smith", "invalid", 150),
        UserInput("bob wilson", "bob@test.com", 30)
    )
    
    for (input in inputs) {
        println("\nProcessing: ${input.name}")
        println("  Formatted name: ${input.name.toTitleCase()}")
        println("  Email valid: ${input.email.isValidEmail()}")
        println("  Initials: ${input.name.initials}")
    }
}
```

**Sample Output:**
```
Extension Function Demonstrations:

Hour validation:
Is 14 a valid hour? true
Is 25 a valid hour? false

============================================================

Email validation:
Is 'user@example.com' valid? true
Is 'invalid-email' valid? false

============================================================

Title case conversion:
Original: the quick brown fox jumps over the lazy dog
Title case: The Quick Brown Fox Jumps Over The Lazy Dog

============================================================

Getting initials:
Name: John Ronald Reuel Tolkien
Initials: JRRT
Name: Alice Bob Carol
Initials: ABC

============================================================

Negative and penalty calculations:
Value: 10
Negative: -10
Penalty: -100

============================================================

List operations:
Numbers: [10, 20, 30, 40, 50]
Average: 30.0
Empty list average: 0.0

============================================================

Currency formatting:
Price 1: $19.99
Price 2: $1234.50
Price 3: $0.99

============================================================

Practical example - user input validation:

Processing: john doe
  Formatted name: John Doe
  Email valid: true
  Initials: JD

Processing: jane smith
  Formatted name: Jane Smith
  Email valid: false
  Initials: JS

Processing: bob wilson
  Formatted name: Bob Wilson
  Email valid: true
  Initials: BW
```

### Key Points About Extensions

Extension functions and properties provide a clean way to add functionality to existing types without modification or inheritance. They are resolved statically at compile time, meaning the actual function called is determined by the declared type of the variable, not the runtime type. This makes them predictable and efficient while avoiding the complexity of dynamic dispatch.

Extensions have access to public members of the class but not to private or protected members, maintaining proper encapsulation boundaries. This is an important distinction from inheritance, where subclasses can access protected members. Extensions are particularly useful for adding utility methods to standard library classes or third-party library classes that you cannot modify.

The combination of extension functions with lambda expressions and higher-order functions creates powerful patterns for building domain-specific languages and fluent APIs. Many of the convenient methods you use on collections in Kotlin, such as map and filter, are actually implemented as extension functions that take lambda expressions as parameters, demonstrating how these concepts work together to create expressive, maintainable code.

---

## Time Allocation Summary

| Section | Topic | Time | Learning Outcomes |
|---------|-------|------|-------------------|
| 1 | Introduction & Overview | 2 min | Overview understanding |
| 2 | Functions Overview & Types | 3 min | LO1 |
| 3 | Default Values & Named Parameters | 4 min | LO2 |
| 4 | Lambda Expressions | 6 min | LO3 |
| 5 | Higher Order Functions | 6 min | LO4 |
| 6 | Basic Class Syntax | 6 min | LO5 |
| 7 | Getters and Setters | 5 min | LO6 |
| 8 | Primary Constructor | 5 min | LO7 |
| 9 | Init Blocks | 4 min | LO8 |
| 10 | Secondary Constructors | 5 min | LO9 |
| 11 | Data Classes | 4 min | LO10 |
| 12 | Extension Functions & Properties | 5 min | LO11 |
| **Total** | | **55 min** | **All LOs** |

---

## Resources for Further Reading and Practice

### Official Kotlin Documentation
- **Kotlin Functions**: https://kotlinlang.org/docs/functions.html - Comprehensive guide to function syntax and features
- **Lambdas and Higher-Order Functions**: https://kotlinlang.org/docs/lambdas.html - Deep dive into functional programming in Kotlin
- **Classes and Objects**: https://kotlinlang.org/docs/classes.html - Complete reference for class features
- **Properties**: https://kotlinlang.org/docs/properties.html - Understanding getters, setters, and backing fields
- **Constructors**: https://kotlinlang.org/docs/classes.html#constructors - Primary and secondary constructor patterns
- **Data Classes**: https://kotlinlang.org/docs/data-classes.html - When and how to use data classes effectively
- **Extensions**: https://kotlinlang.org/docs/extensions.html - Adding functionality to existing classes

### Interactive Learning Platforms
- **Kotlin Koans**: https://play.kotlinlang.org/koans - Interactive exercises covering all Kotlin concepts including functions and classes
- **Exercism Kotlin Track**: https://exercism.org/tracks/kotlin - Mentored exercises with community feedback on your solutions
- **JetBrains Academy**: https://hyperskill.org/tracks - Structured learning paths with real-world projects
- **Kotlin by Example**: https://play.kotlinlang.org/byExample - Learn through annotated code examples

### Practice Resources
- **Kotlin Playground**: https://play.kotlinlang.org - Write and run Kotlin code directly in your browser
- **LeetCode Kotlin Solutions**: https://leetcode.com - Practice algorithms and data structures using Kotlin
- **HackerRank Kotlin**: https://www.hackerrank.com/domains/tutorials/30-days-of-code - Coding challenges with Kotlin support
- **Codewars**: https://www.codewars.com - Kata exercises specifically for Kotlin practice

### Books and In-Depth Guides
- **Kotlin in Action** by Dmitry Jemerov and Svetlana Isakova - Written by JetBrains developers, comprehensive coverage of language features
- **Atomic Kotlin** by Bruce Eckel and Svetlana Isakova - Step-by-step approach perfect for learning both paradigms
- **Kotlin Programming: The Big Nerd Ranch Guide** - Practical, hands-on approach to Kotlin development
- **Head First Kotlin** by Dawn Griffiths and David Griffiths - Visual learning approach with engaging examples

### Video Tutorials
- **Kotlin YouTube Channel**: https://www.youtube.com/c/Kotlin - Official videos from JetBrains with feature explanations
- **FreeCodeCamp Kotlin Course**: https://www.youtube.com/freecodecamp - Comprehensive free video tutorials
- **Coursera Kotlin for Java Developers**: https://www.coursera.org/learn/kotlin-for-java-developers - Structured course with certificates
- **Udacity Kotlin Bootcamp**: https://www.udacity.com - Hands-on projects and exercises

### Community and Support
- **Kotlin Slack**: https://surveys.jetbrains.com/s3/kotlin-slack-sign-up - Active community discussion channels
- **Stack Overflow Kotlin Tag**: https://stackoverflow.com/questions/tagged/kotlin - Question and answer forum with active community
- **Kotlin Reddit**: https://www.reddit.com/r/Kotlin - Community discussions, news, and learning resources
- **Kotlin Discussions**: https://discuss.kotlinlang.org - Official forum for Kotlin-related topics

### Advanced Topics for Further Study
- **Coroutines**: https://kotlinlang.org/docs/coroutines-overview.html - Asynchronous programming in Kotlin
- **Kotlin Flow**: https://kotlinlang.org/docs/flow.html - Reactive streams and data flow
- **Multiplatform**: https://kotlinlang.org/docs/multiplatform.html - Sharing code across platforms
- **DSL Design**: https://kotlinlang.org/docs/type-safe-builders.html - Building domain-specific languages

### Practical Application Resources
- **Android Development**: https://developer.android.com/kotlin - Kotlin for Android app development
- **Spring Boot with Kotlin**: https://spring.io/guides/tutorials/spring-boot-kotlin - Server-side development
- **Kotlin for Data Science**: https://kotlinlang.org/docs/data-science-overview.html - Using Kotlin in data analysis

---

This completes the comprehensive lecture content for Kotlin Functions and Classes. The material is now organized to teach functional programming concepts first, establishing a foundation in how functions work as first-class citizens, followed by object-oriented concepts that build upon and integrate with those functional patterns. Students now have a complete understanding of both paradigms and can see how Kotlin elegantly combines functional and object-oriented programming to create expressive, maintainable code. The progression from functions to lambdas to higher-order functions, then to classes and finally to extension functions, shows how these concepts build upon each other to create Kotlin's powerful and flexible programming model.