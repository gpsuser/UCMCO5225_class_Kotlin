# Lecture 2: Kotlin Worksheet

---

## Part A: Quiz Questions

### Question 1

Fill in the blanks using the words from the word bank below:

```
A __________ expression is an anonymous function that can be stored in a variable
or passed as an argument. When a lambda has a single parameter, you can use the
special identifier __________ to refer to that parameter. The last expression in
a lambda automatically becomes the __________ value. Lambdas are enclosed in
__________ braces and parameters are listed before an __________ operator.
```

**Word Bank:** `return`, `curly`, `it`, `arrow`, `lambda`

---

### Question 2

Fill in the blanks using the words from the word bank below:

```
A __________ class automatically generates several useful methods including equals(),
hashCode(), toString(), and __________. All primary constructor parameters must be
marked as val or __________. The __________ method allows you to create modified
copies of objects. Data classes must have at least __________ parameter in their
primary constructor.
```

**Word Bank:** `one`, `var`, `copy`, `data`, `new`

---

### Question 3

Which of the following statements about Kotlin function types is **correct**?

A) A function type `(String, Int) -> Boolean` represents a function that takes a String and returns an Int and Boolean  
B) A function type `() -> Unit` represents a function with no parameters that returns nothing  
C) Function types cannot be stored in variables in Kotlin  
D) The arrow operator `->` is optional when declaring function types

---

### Question 4

What is the purpose of an `init` block in a Kotlin class?

A) It defines the primary constructor parameters  
B) It executes initialization code after the primary constructor completes  
C) It replaces the need for getters and setters  
D) It is used to create secondary constructors

---

## Part B: Programming Tasks

### Task 1: Student Grade Analyzer

Create a class called `GradeAnalyzer` that uses higher-order functions to process student grades. 

**Requirements:**
- The class should have a primary constructor that takes a list of integers (grades)
- Implement a method `analyzeGrades` that takes a lambda function as a parameter
- The lambda should process each grade and return a string description
- Create an extension function `percentageToGrade()` on Int that converts a percentage to a letter grade (A: 70+, B: 60-69, C: 50-59, D: 40-49, F: below 40)

**Code Scaffolding:**

```kotlin
class GradeAnalyzer(val grades: List<Int>) {
    
    fun analyzeGrades(processor: (Int) -> String): List<String> {
        // TODO: Implement this method
        // Use the processor lambda to transform each grade
    }
}

// TODO: Create extension function
fun Int.percentageToGrade(): String {
    // TODO: Implement grade conversion logic
}

fun main() {
    val grades = listOf(85, 72, 45, 91, 63, 58, 77)
    val analyzer = GradeAnalyzer(grades)
    
    // Use the analyzer with a lambda
    val results = analyzer.analyzeGrades { grade ->
        "${grade}% = ${grade.percentageToGrade()}"
    }
    
    results.forEach { println(it) }
}
```

**Expected Output:**
```
85% = A
72% = A
45% = D
91% = A
63% = B
58% = C
77% = A
```

---

### Task 2: Book Library System

Create a `Book` data class and a `Library` class that manages a collection of books.

**Requirements:**
- Create a `Book` data class with properties: title, author, year, and isAvailable (default true)
- Create a `Library` class with:
  - A primary constructor that takes the library name
  - A secondary constructor that also initializes it with a list of books
  - A private mutable list of books
  - An init block that prints "Library '{name}' initialized"
  - Methods to: add a book, checkout a book (set isAvailable to false), and get available books

**Code Scaffolding:**

```kotlin
// TODO: Create the Book data class
data class Book(
    // TODO: Add properties
)

class Library(val name: String) {
    private val books = mutableListOf<Book>()
    
    // TODO: Add init block
    
    // TODO: Create secondary constructor that accepts List<Book>
    
    fun addBook(book: Book) {
        // TODO: Implement
    }
    
    fun checkoutBook(title: String): Boolean {
        // TODO: Find the book and set isAvailable to false
        // Return true if successful, false if book not found or already checked out
    }
    
    fun getAvailableBooks(): List<Book> {
        // TODO: Return only available books using filter
    }
}

fun main() {
    val library = Library("City Library")
    
    library.addBook(Book("1984", "George Orwell", 1949))
    library.addBook(Book("Brave New World", "Aldous Huxley", 1932))
    library.addBook(Book("Fahrenheit 451", "Ray Bradbury", 1953))
    
    println("\nAvailable books:")
    library.getAvailableBooks().forEach { println("  ${it.title} by ${it.author}") }
    
    println("\nChecking out '1984'...")
    library.checkoutBook("1984")
    
    println("\nAvailable books after checkout:")
    library.getAvailableBooks().forEach { println("  ${it.title} by ${it.author}") }
}
```

**Expected Output:**
```
Library 'City Library' initialized

Available books:
  1984 by George Orwell
  Brave New World by Aldous Huxley
  Fahrenheit 451 by Ray Bradbury

Checking out '1984'...

Available books after checkout:
  Brave New World by Aldous Huxley
  Fahrenheit 451 by Ray Bradbury
```

---
