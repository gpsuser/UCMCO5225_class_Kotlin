# Lecture 5: Kotlin Worksheet

## Quiz Section

### Question 1

Complete the following definition by filling in the blanks:

A code smell is a __________ indication that usually corresponds to a deeper __________ in the system. It's not a __________ but rather a warning sign that something in your code __________ could be improved.

**Word Bank:** bug, surface, problem, design

---

### Question 2

Fill in the blanks to complete this statement about refactoring:

Refactoring is the process of improving your code's __________ structure without changing its __________ behavior. The key principle is to make __________ changes that can be easily __________.

**Word Bank:** external, internal, incremental, verified

---

### Question 3

Complete the following statement about the relationship between testing and refactoring:

Unit tests enable __________ refactoring because with a comprehensive test __________, you can refactor confidently knowing that if you break something, the tests will __________ it. Tests should still __________ after refactoring.

**Word Bank:** suite, safe, catch, pass

---

### Question 4

Which of the following is NOT a characteristic of the "Long Method" code smell?

A) The method tries to do too many things  
B) The method is difficult to test all different paths  
C) The method has too few parameters  
D) The method violates the Single Responsibility Principle

---

### Question 5

When should you use the "Extract Method" refactoring technique?

A) When a method is too short and needs more functionality  
B) When you have code fragments that can be grouped together or when a method is too long and complex  
C) When you want to make all methods public  
D) When you need to add more parameters to a method

---

### Question 6

What is the primary benefit of the "Replace Type Code with Class" refactoring?

A) It makes the code run faster  
B) It reduces the number of classes in the system  
C) It provides type safety and prevents invalid values from being used  
D) It eliminates the need for enums in Kotlin

---

## Task Section

### Task 1: Refactor Duplicated Code

The following code contains duplicated validation logic. Your task is to refactor it by extracting the duplicated code into separate methods.

**Starting Code:**

```kotlin
class ProductManager {
    private val products = mutableListOf<Pair<String, Double>>()
    
    fun addProduct(name: String, price: Double): Boolean {
        // Validation (DUPLICATED)
        if (name.isEmpty()) {
            println("Error: Product name cannot be empty")
            return false
        }
        if (name.length > 50) {
            println("Error: Product name too long")
            return false
        }
        if (price < 0) {
            println("Error: Price cannot be negative")
            return false
        }
        if (price > 100000) {
            println("Error: Price exceeds maximum allowed")
            return false
        }
        
        products.add(Pair(name, price))
        println("Product added: $name at £$price")
        return true
    }
    
    fun updateProduct(index: Int, name: String, price: Double): Boolean {
        if (index < 0 || index >= products.size) {
            println("Error: Invalid product index")
            return false
        }
        
        // Same validation DUPLICATED here
        if (name.isEmpty()) {
            println("Error: Product name cannot be empty")
            return false
        }
        if (name.length > 50) {
            println("Error: Product name too long")
            return false
        }
        if (price < 0) {
            println("Error: Price cannot be negative")
            return false
        }
        if (price > 100000) {
            println("Error: Price exceeds maximum allowed")
            return false
        }
        
        products[index] = Pair(name, price)
        println("Product updated: $name at £$price")
        return true
    }
}

fun main() {
    val manager = ProductManager()
    
    // Test cases
    manager.addProduct("Laptop", 899.99)
    manager.addProduct("", 50.0)
    manager.addProduct("Very Long Product Name That Exceeds The Maximum Length Allowed", 100.0)
    manager.updateProduct(0, "Gaming Laptop", 1299.99)
    manager.updateProduct(0, "Product", -50.0)
}
```

**Your Task:**

Refactor the code above by:
1. Creating a `validateProductName()` method that returns a Boolean
2. Creating a `validateProductPrice()` method that returns a Boolean
3. Using these methods in both `addProduct()` and `updateProduct()`

**Expected Output:**
```
Product added: Laptop at £899.99
Error: Product name cannot be empty
Error: Product name too long
Product updated: Gaming Laptop at £1299.99
Error: Price cannot be negative
```

---

### Task 2: Apply Encapsulation

The following code violates encapsulation principles. Your task is to refactor it to properly encapsulate the collection and provide controlled access.

**Starting Code:**

```kotlin
class Library {
    // Public mutable list - anyone can modify it!
    val books = mutableListOf<String>()
    
    fun printLibrary() {
        println("Library contains ${books.size} books:")
        books.forEach { println("  - $it") }
    }
}

fun main() {
    val library = Library()
    
    // Direct manipulation of the collection
    library.books.add("1984")
    library.books.add("Brave New World")
    library.printLibrary()
    
    // Problem: anyone can do this!
    library.books.clear()
    println("\nAfter external modification:")
    library.printLibrary()
}
```

**Your Task:**

Refactor the `Library` class to:
1. Make the `books` list private (use a backing field like `_books`)
2. Provide a public read-only property that returns a copy of the list
3. Create an `addBook(title: String)` method with validation (title cannot be blank)
4. Create a `removeBook(title: String)` method that returns Boolean
5. Create a `getBookCount()` method

**Expected Output:**
```
Added: 1984
Added: Brave New World
Library contains 2 books:
  - 1984
  - Brave New World
Error: Book title cannot be blank
Removed: Brave New World
Library contains 1 books:
  - 1984
```

---

**End of Worksheet**