# Type Systems and Generics in Kotlin
**CO5225 – Lecture 4**

---

## Table of Contents

| Section | Topic | Time |
|---------|-------|------|
| 1 | [Learning Outcomes](#learning-outcomes) | 2 min |
| 2 | [Introduction to Type Systems](#1-introduction-to-type-systems) | 5 min |
| 3 | [Type Safety](#2-type-safety) | 5 min |
| 4 | [Static vs Dynamic Typing](#3-static-vs-dynamic-typing) | 10 min |
| 5 | [Kotlin's Type System Features](#4-kotlins-type-system-features) | 10 min |
| 6 | [Strong vs Weak Typing](#5-strong-vs-weak-typing) | 8 min |
| 7 | [Introduction to Generics](#6-introduction-to-generics) | 10 min |
| 8 | [Working with Generic Types in Kotlin](#7-working-with-generic-types-in-kotlin) | 5 min |

---

## Learning Outcomes

By the end of this lecture, you will be able to:

1. **Explain** the difference between statically typed and dynamically typed languages
2. **Describe** what type safety means and why it matters in programming
3. **Compare** Kotlin's type system with other languages (Java, JavaScript)
4. **Distinguish** between strong and weak typing
5. **Apply** generics to create type-safe collections in Kotlin
6. **Analyze** how generic type parameters work and why they're useful

---

## 1. Introduction to Type Systems

**Time: 5 minutes**

Every programming language has a **type system** - a set of rules that defines how the language categorizes and manages different kinds of data. Understanding type systems is crucial because they affect how you write code, when errors are caught, and how flexible your programs can be.

### What is a Type?

A type defines:
- What **kind** of data a variable can hold (numbers, text, boolean values, etc.)
- What **operations** you can perform on that data
- How much **memory** is needed to store the data

### Two Fundamental Approaches

Programming languages fall into two main categories:

1. **Statically Typed Languages** - Types are checked at compile time (before the program runs)
   - Examples: Kotlin, Java, C++, TypeScript
   
2. **Dynamically Typed Languages** - Types are checked at runtime (while the program runs)
   - Examples: JavaScript, Python, Ruby

This distinction has profound implications for how you develop software, debug errors, and maintain code over time.

---

## 2. Type Safety

**Time: 5 minutes**

### What is Type Safety?

**Type safety** refers to how well a programming language prevents **type errors** - situations where one data type is unintentionally treated as another.

A type error occurs when you try to perform an operation on data that doesn't support that operation. For example:
- Trying to add a number to a piece of text
- Calling a method that doesn't exist for a particular type
- Passing the wrong type of argument to a function

### Why Does Type Safety Matter?

Type-safe languages help you:
- **Catch errors early** - Many bugs are discovered before you run the program
- **Write more reliable code** - The compiler acts as a safety net
- **Improve code clarity** - Types serve as documentation
- **Enable better tooling** - IDEs can provide accurate autocomplete and refactoring

### The Trade-off

More type safety generally means:
- ✅ Fewer runtime errors
- ✅ Better IDE support
- ❌ More verbose code (sometimes)
- ❌ Less flexibility in certain scenarios

Different languages make different choices about where to sit on this spectrum.

---

## 3. Static vs Dynamic Typing

**Time: 10 minutes**

### Statically Typed Languages

In a **statically typed language**, the type of every variable is known at **compile time**. The compiler verifies that all operations are type-safe before generating the executable program.

**Example - Kotlin (Static):**

```kotlin
// Complete Kotlin program demonstrating static typing

fun main() {
    // Declare a variable with explicit type
    var name: String = "Andrew"
    println("Name is: $name")
    println("Type: String")
    
    // This line would cause a COMPILE ERROR in Kotlin
    // name = 6  // Error: Type mismatch: inferred type is Int but String was expected
    
    // To change the value, it must be the same type
    name = "Sarah"
    println("Name is now: $name")
}

/*
Output:
Name is: Andrew
Type: String
Name is now: Sarah
*/
```

**Key point:** The Kotlin compiler won't let you compile code that assigns an `Int` to a `String` variable. The error is caught before you even run the program.

### Dynamically Typed Languages

In a **dynamically typed language**, types are resolved at **runtime**. Variables can hold values of any type, and the type can change during program execution.

**Example - JavaScript (Dynamic):**

```javascript
// JavaScript program demonstrating dynamic typing

var name = "Andrew";
console.log("Name is: " + name);
console.log("Type: " + typeof name);

// This is VALID in JavaScript - the type can change
name = 6;
console.log("Name is now: " + name);
console.log("Type: " + typeof name);

/*
Output:
Name is: Andrew
Type: string
Name is now: 6
Type: number
*/
```

**Key point:** JavaScript allows you to reassign variables to different types. This provides flexibility but can lead to unexpected behavior if you're not careful.

### Practical Demonstration: Function Type Checking

Let's see how static and dynamic typing affect function calls.

**Kotlin - Static Type Checking:**

```kotlin
// Complete Kotlin program demonstrating compile-time type checking

// Function that adds two integers
fun sum(x: Int, y: Int): Int {
    return x + y
}

fun main() {
    // Valid call - both arguments are Int
    val resultA = sum(1, 1)
    println("sum(1, 1) = $resultA")
    
    // Invalid call - would not compile
    // val resultB = sum("1", "1")  
    // Error: Type mismatch: inferred type is String but Int was expected
    
    // This demonstrates that type errors are caught at COMPILE TIME
    println("Program compiled successfully - all types are correct!")
}

/*
Output:
sum(1, 1) = 2
Program compiled successfully - all types are correct!
*/
```

**JavaScript - Runtime Type Checking:**

```javascript
// JavaScript program demonstrating runtime type behavior

// Function that adds two values
function sum(x, y) {
    return x + y;
}

// Valid call - both arguments are numbers
var resultA = sum(1, 1);
console.log("sum(1, 1) = " + resultA);  // Output: 2

// "Valid" call - but produces unexpected results!
var resultB = sum("1", "1");
console.log("sum('1', '1') = " + resultB);  // Output: "11" (string concatenation)

// Mixed types - also produces unexpected results
var resultC = sum(1, "1");
console.log("sum(1, '1') = " + resultC);  // Output: "11"

var resultD = sum(1, 2.3);
console.log("sum(1, 2.3) = " + resultD);  // Output: 3.3

/*
Output:
sum(1, 1) = 2
sum('1', '1') = 11
sum(1, '1') = 11
sum(1, 2.3) = 3.3
*/
```

**Key observation:** JavaScript compiles and runs with no errors, but the behavior may not be what you intended. The `+` operator performs string concatenation when given strings, which could be a bug if you expected numeric addition.

---

## 4. Kotlin's Type System Features

**Time: 10 minutes**

### Type Inference

One question you might ask is: "Do statically typed languages require you to declare the type of every variable?"

The answer is **not always**! Kotlin uses **type inference** to deduce types automatically.

```kotlin
// Complete Kotlin program demonstrating type inference

fun main() {
    // Explicit type declaration
    val explicitName: String = "Andrew"
    
    // Type inference - Kotlin infers the type is String
    val inferredName = "Sarah"
    
    // The compiler knows both are String type
    println("Explicit: $explicitName")
    println("Inferred: $inferredName")
    
    // Both variables have the same restrictions
    // explicitName = 6  // Error!
    // inferredName = 6  // Also an error!
    
    // More examples of type inference
    val age = 25  // Inferred as Int
    val height = 1.75  // Inferred as Double
    val isStudent = true  // Inferred as Boolean
    
    println("\nInferred types:")
    println("age is Int: $age")
    println("height is Double: $height")
    println("isStudent is Boolean: $isStudent")
}

/*
Output:
Explicit: Andrew
Inferred: Sarah

Inferred types:
age is Int: 25
height is Double: 1.75
isStudent is Boolean: true
*/
```

### Kotlin vs Java: Type Coercion

While both Kotlin and Java are statically typed, they handle type coercion differently. Let's look at a specific example.

```kotlin
// Complete Kotlin program comparing type coercion

fun sum(x: Double, y: Double): Double {
    return x + y
}

fun main() {
    // Attempting to call sum with an Int and a Double
    // val result = sum(1, 1.2)  // Does NOT compile in Kotlin!
    // Error: Type mismatch: inferred type is Int but Double was expected
    
    // In Kotlin, you must explicitly convert
    val result = sum(1.0, 1.2)  // Use 1.0 (Double literal)
    println("sum(1.0, 1.2) = $result")
    
    // Or use explicit conversion
    val x = 1
    val result2 = sum(x.toDouble(), 1.2)
    println("sum($x.toDouble(), 1.2) = $result2")
    
    println("\nKotlin does NOT automatically coerce Int to Double")
    println("This prevents subtle bugs and makes conversions explicit")
}

/*
Output:
sum(1.0, 1.2) = 2.2
sum(1.toDouble(), 1.2) = 2.2

Kotlin does NOT automatically coerce Int to Double
This prevents subtle bugs and makes conversions explicit
*/
```

**Comparison with Java:**

```java
// Java equivalent - this WOULD compile
public class TypeCoercionExample {
    public static double sum(double x, double y) {
        return x + y;
    }
    
    public static void main(String[] args) {
        double result = sum(1, 1.2);  // Java automatically converts Int to Double
        System.out.println("sum(1, 1.2) = " + result);
    }
}
```

**Key difference:** Java automatically converts (coerces) the `int` value `1` to a `double`. Kotlin requires you to be explicit about such conversions, which makes your code's intentions clearer and prevents accidental precision loss.

### Mutability: var vs val

```kotlin
// Complete Kotlin program demonstrating var vs val

fun main() {
    // val - immutable (read-only) reference
    val immutableName = "Andrew"
    println("Original: $immutableName")
    
    // immutableName = "Sarah"  // Error: Val cannot be reassigned
    
    // var - mutable reference
    var mutableName = "Andrew"
    println("Original: $mutableName")
    
    mutableName = "Sarah"  // This is allowed
    println("Changed: $mutableName")
    
    // However, the TYPE still cannot change
    // mutableName = 123  // Error: Type mismatch
    
    println("\nKey point: var allows reassignment, but not type changes")
}

/*
Output:
Original: Andrew
Original: Andrew
Changed: Sarah

Key point: var allows reassignment, but not type changes
*/
```

---

## 5. Strong vs Weak Typing

**Time: 8 minutes**

### Defining Strong and Weak Typing

The terms "strong" and "weak" typing are less precisely defined than static/dynamic typing, but generally:

**Strongly Typed Languages:**
- Type compatibility is strictly enforced
- Minimal implicit type conversion
- Explicit conversions required
- As Liskov & Zilles (1974) stated: "Whenever an object is passed from a calling function to a called function, its type must be compatible with the type declared in the called function"

**Weakly Typed Languages:**
- More flexible with type conversions
- Automatic type coercion in many situations
- Can sometimes lead to unexpected behavior

### The Spectrum

It's better to think of typing strength as a spectrum rather than a binary choice:

```
Weaker <-----------------------------------------> Stronger
JavaScript    C    Java/Kotlin    Haskell/ML
```

### Examples of Type Flexibility

Let's explore how different languages handle type mixing:

```kotlin
// Complete Kotlin program demonstrating type strictness

fun main() {
    val intValue: Int = 10
    val doubleValue: Double = 5.5
    
    // Kotlin does NOT allow this without explicit conversion
    // val result = intValue + doubleValue  // Error!
    
    // You must explicitly convert
    val result = intValue.toDouble() + doubleValue
    println("$intValue + $doubleValue = $result")
    
    // Comparison with other operations
    val byteValue: Byte = 10
    val intValue2: Int = 20
    
    // Even Byte and Int cannot be mixed without conversion
    // val sum = byteValue + intValue2  // Error!
    
    val sum = byteValue.toInt() + intValue2
    println("Byte($byteValue) + Int($intValue2) = $sum")
    
    println("\nKotlin's strict typing prevents accidental precision loss")
    println("and makes all conversions explicit and visible")
}

/*
Output:
10 + 5.5 = 15.5
Byte(10) + Int(20) = 30

Kotlin's strict typing prevents accidental precision loss
and makes all conversions explicit and visible
*/
```

### Implicit Conversions in Practice

Some languages allow more implicit conversions than others. Here's how Kotlin handles numeric types:

```kotlin
// Complete Kotlin program exploring numeric type conversions

fun demonstrateNumericTypes() {
    // Different numeric types
    val byteValue: Byte = 1
    val shortValue: Short = 2
    val intValue: Int = 3
    val longValue: Long = 4L
    val floatValue: Float = 5.0f
    val doubleValue: Double = 6.0
    
    println("Numeric Types in Kotlin:")
    println("Byte: $byteValue")
    println("Short: $shortValue")
    println("Int: $intValue")
    println("Long: $longValue")
    println("Float: $floatValue")
    println("Double: $doubleValue")
    
    // All conversions must be explicit
    val byteToInt: Int = byteValue.toInt()
    val intToLong: Long = intValue.toLong()
    val intToDouble: Double = intValue.toDouble()
    
    println("\nExplicit Conversions:")
    println("Byte to Int: $byteToInt")
    println("Int to Long: $intToLong")
    println("Int to Double: $intToDouble")
}

fun main() {
    demonstrateNumericTypes()
    
    println("\n" + "=".repeat(50))
    println("Key Insight: Kotlin requires explicit conversions")
    println("This prevents bugs from unexpected type coercion")
    println("=".repeat(50))
}

/*
Output:
Numeric Types in Kotlin:
Byte: 1
Short: 2
Int: 3
Long: 4
Float: 5.0
Double: 6.0

Explicit Conversions:
Byte to Int: 1
Int to Long: 3
Int to Double: 3.0

==================================================
Key Insight: Kotlin requires explicit conversions
This prevents bugs from unexpected type coercion
==================================================
*/
```

### Connection to Static Typing

**Important observation:** Strongly typed languages are more likely to be statically typed. Why? Because enforcing strict type compatibility is easier to do at compile time rather than runtime.

---

## 6. Introduction to Generics

**Time: 10 minutes**

### The Problem Generics Solve

Imagine you're building a program and you need several lists:
- A list of strings (student names)
- A list of integers (test scores)
- A list of dates (assignment due dates)

Without generics, you'd face a dilemma:
1. Create a separate list class for each type? (lots of duplicate code!)
2. Create one list that holds `Any` type? (lose type safety!)

**Generics provide the solution:** Write one list class that works with any type, while maintaining type safety.

### Type Safety Without Generics

Let's see what happens when we don't constrain types in collections:

```kotlin
// Complete Kotlin program demonstrating the problem without proper generics

fun main() {
    // Without type constraints, we can mix types
    // Kotlin infers this as MutableList<Any>
    val mixedList = mutableListOf("Apple", "Banana", 7)
    
    println("Mixed list: $mixedList")
    println("Inferred type: MutableList<Any>")
    
    // Now when we iterate, we only have access to Any methods
    for (item in mixedList) {
        // We can't call .uppercase() because item is of type Any
        // println(item.uppercase())  // Error!
        
        // We only have access to methods from Any
        println("Item: $item (toString from Any)")
    }
    
    // To use String methods, we need to check and cast
    println("\nWith type checking and casting:")
    for (item in mixedList) {
        if (item is String) {
            println("String: ${item.uppercase()}")
        } else {
            println("Not a string: $item")
        }
    }
    
    println("\nProblem: We lose type safety and need runtime checks!")
}

/*
Output:
Mixed list: [Apple, Banana, 7]
Inferred type: MutableList<Any>
Item: Apple (toString from Any)
Item: Banana (toString from Any)
Item: 7 (toString from Any)

With type checking and casting:
String: APPLE
String: BANANA
Not a string: 7

Problem: We lose type safety and need runtime checks!
*/
```

**This is similar to Python's behavior** (shown in the slides), where type errors only appear at runtime.

### The Generic Solution

With generics, we can specify what type of elements a collection should hold:

```kotlin
// Complete Kotlin program demonstrating type-safe generics

fun main() {
    // Create a list that can ONLY hold Strings
    val stringList: MutableList<String> = mutableListOf("Apple", "Banana", "Carrot")
    
    println("String list: $stringList")
    
    // Now we can safely use String methods
    for (item in stringList) {
        println("Uppercase: ${item.uppercase()}")
    }
    
    // Trying to add a non-String will cause a COMPILE error
    // stringList.add(7)  // Error: Type mismatch!
    
    println("\nWith generics, type errors are caught at compile time!")
    
    // Create a list that can ONLY hold Integers
    val intList: MutableList<Int> = mutableListOf(1, 2, 3)
    
    println("\nInteger list: $intList")
    
    for (item in intList) {
        println("Double: ${item * 2}")
    }
    
    // Again, mixing types causes a compile error
    // intList.add("four")  // Error: Type mismatch!
    
    println("\nGenerics maintain type safety while providing flexibility!")
}

/*
Output:
String list: [Apple, Banana, Carrot]
Uppercase: APPLE
Uppercase: BANANA
Uppercase: CARROT

With generics, type errors are caught at compile time!

Integer list: [1, 2, 3]
Double: 2
Double: 4
Double: 6

Generics maintain type safety while providing flexibility!
*/
```

### Key Terminology

Let's clarify some important terms:

**Generic Type:** A class or interface that has type parameters
- Example: `List`, `MutableList`, `Set`

**Type Parameter:** The placeholder in angular brackets `<T>`
- In `List<T>`, the `T` is the type parameter

**Parameterized Type:** A generic type with a specific type argument
- Example: `List<String>` is a parameterized type
- Example: `MutableList<Int>` is a parameterized type

```kotlin
// Complete Kotlin program demonstrating generic terminology

fun main() {
    // MutableList is the GENERIC TYPE
    // String is the TYPE ARGUMENT
    // MutableList<String> is the PARAMETERIZED TYPE
    
    val fruits: MutableList<String> = mutableListOf("Apple", "Banana")
    println("Fruits (MutableList<String>): $fruits")
    
    val numbers: MutableList<Int> = mutableListOf(1, 2, 3)
    println("Numbers (MutableList<Int>): $numbers")
    
    val grades: MutableList<Double> = mutableListOf(85.5, 92.0, 78.5)
    println("Grades (MutableList<Double>): $grades")
    
    println("\nAll three use the same generic type (MutableList)")
    println("but with different type parameters")
}

/*
Output:
Fruits (MutableList<String>): [Apple, Banana]
Numbers (MutableList<Int>): [1, 2, 3]
Grades (MutableList<Double>): [85.5, 92.0, 78.5]

All three use the same generic type (MutableList)
but with different type parameters
*/
```

---

## 7. Working with Generic Types in Kotlin

**Time: 5 minutes**

### Type Inference with Generics

Just like with regular variables, Kotlin can infer generic type parameters:

```kotlin
// Complete Kotlin program demonstrating generic type inference

fun main() {
    // Explicit type parameter
    val explicitList: MutableList<String> = mutableListOf("Apple", "Banana")
    
    // Type inference - Kotlin infers MutableList<String>
    val inferredList = mutableListOf("Apple", "Banana")
    
    println("Explicit list: $explicitList")
    println("Inferred list: $inferredList")
    
    // Both have the same type restrictions
    explicitList.add("Carrot")  // OK
    inferredList.add("Date")    // OK
    
    // explicitList.add(7)  // Error!
    // inferredList.add(7)  // Also an error!
    
    println("\nAfter adding items:")
    println("Explicit list: $explicitList")
    println("Inferred list: $inferredList")
    
    // Type inference with multiple types
    val numbers = mutableListOf(1, 2, 3)  // Inferred as MutableList<Int>
    val decimals = mutableListOf(1.5, 2.5)  // Inferred as MutableList<Double>
    
    println("\nNumbers (Int): $numbers")
    println("Decimals (Double): $decimals")
}

/*
Output:
Explicit list: [Apple, Banana]
Inferred list: [Apple, Banana]

After adding items:
Explicit list: [Apple, Banana, Carrot]
Inferred list: [Apple, Banana, Date]

Numbers (Int): [1, 2, 3]
Decimals (Double): [1.5, 2.5]
*/
```

### The Return Type of Generic Methods

Generic types have methods that also use the type parameter. Let's explore this:

```kotlin
// Complete Kotlin program demonstrating generic method return types

fun main() {
    // Create a list of strings
    val stringList: List<String> = listOf("apple", "banana", "carrot")
    
    // The last() method returns the same type as the list parameter
    val lastString: String = stringList.last()
    println("Last string: $lastString")
    
    // Create a list of integers
    val intList: List<Int> = listOf(1, 2, 3)
    
    // The last() method returns Int for List<Int>
    val lastInt: Int = intList.last()
    println("Last integer: $lastInt")
    
    // This works because List<T>.last() returns T
    // When T = String, last() returns String
    // When T = Int, last() returns Int
    
    println("\nThe return type of generic methods")
    println("depends on the type parameter!")
    
    // More examples
    val firstString = stringList.first()  // Returns String
    val firstInt = intList.first()  // Returns Int
    
    println("\nFirst string: $firstString")
    println("First integer: $firstInt")
    
    // Demonstrating type safety
    val uppercased = lastString.uppercase()  // String methods available
    val doubled = lastInt * 2  // Int operations available
    
    println("\nString method: ${lastString.uppercase()}")
    println("Int operation: $lastInt * 2 = $doubled")
}

/*
Output:
Last string: carrot
Last integer: 3

The return type of generic methods
depends on the type parameter!

First string: apple
First integer: 1

String method: CARROT
Int operation: 3 * 2 = 6
*/
```

### Real-World Example: Type-Safe Collections

Let's see a practical example of how generics help us write safer code:

```kotlin
// Complete Kotlin program: Student grade management system

// Data class to represent a student
data class Student(val name: String, val id: Int)

fun calculateAverage(grades: List<Double>): Double {
    // Calculate the average of a list of grades
    return grades.sum() / grades.size
}

fun findTopStudent(students: List<Student>, grades: List<Double>): Student {
    // Find the student with the highest grade
    val maxGradeIndex = grades.indexOf(grades.maxOrNull())
    return students[maxGradeIndex]
}

fun main() {
    // Type-safe list of students
    val students: List<Student> = listOf(
        Student("Alice", 101),
        Student("Bob", 102),
        Student("Charlie", 103)
    )
    
    // Type-safe list of grades
    val grades: List<Double> = listOf(85.5, 92.0, 78.5)
    
    println("Students and Grades:")
    for (i in students.indices) {
        println("${students[i].name} (ID: ${students[i].id}): ${grades[i]}")
    }
    
    // Calculate average
    val average = calculateAverage(grades)
    println("\nClass average: %.2f".format(average))
    
    // Find top student
    val topStudent = findTopStudent(students, grades)
    val topGrade = grades.maxOrNull()
    println("Top student: ${topStudent.name} with grade $topGrade")
    
    // The compiler ensures type safety:
    // students.add(92.0)  // Error! Can't add Double to List<Student>
    // grades.add(Student("Dave", 104))  // Error! Can't add Student to List<Double>
    
    println("\nGenerics ensure we can't accidentally mix up students and grades!")
}

/*
Output:
Students and Grades:
Alice (ID: 101): 85.5
Bob (ID: 102): 92.0
Charlie (ID: 103): 78.5

Class average: 85.33
Top student: Bob with grade 92.0

Generics ensure we can't accidentally mix up students and grades!
*/
```

---

## Summary

### Key Concepts Covered

| Concept | Key Points |
|---------|-----------|
| **Type Safety** | How well a language prevents type errors; statically typed languages catch errors at compile time |
| **Static Typing** | Types are checked before runtime (Kotlin, Java); provides early error detection and better tooling |
| **Dynamic Typing** | Types are checked at runtime (JavaScript, Python); provides flexibility but can hide bugs |
| **Strong Typing** | Minimal implicit type conversion; explicit conversions required (Kotlin is strongly typed) |
| **Weak Typing** | More automatic type coercion; can lead to unexpected behavior |
| **Generics** | Allow writing flexible, reusable code while maintaining type safety |
| **Type Parameters** | Placeholders (like `<T>`) that make generic types work with specific types |
| **Type Inference** | Kotlin can deduce types automatically, reducing verbosity while keeping type safety |

### Learning Outcomes Achieved

✅ You can now explain the difference between static and dynamic typing  
✅ You understand what type safety means and why Kotlin enforces it  
✅ You can compare Kotlin's type system with JavaScript and Java  
✅ You grasp the distinction between strong and weak typing  
✅ You know how to use generics to create type-safe collections  
✅ You understand how generic type parameters work

### Looking Ahead

The concepts we've covered today form the foundation for understanding:
- More advanced generic features (variance, constraints)
- Collection operations and functional programming
- Building your own generic classes and functions

Remember: Kotlin's type system is designed to catch errors early while maintaining code clarity. Embrace the type safety - it's there to help you write better, more reliable code!

---

**End of Lecture**