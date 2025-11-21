# Code Smells and Refactoring in Kotlin

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [Introduction to Code Smells and Refactoring](#introduction-to-code-smells-and-refactoring)
- [Common Code Smells](#common-code-smells)
  - [Duplicated Code](#duplicated-code)
  - [Long Method](#long-method)
  - [Large Class](#large-class)
  - [Long Parameter List](#long-parameter-list)
  - [Temporary Field](#temporary-field)
  - [Data Class](#data-class)
- [Refactoring Techniques: Composing Methods](#refactoring-techniques-composing-methods)
  - [Extract Method](#extract-method)
  - [Introduce Explaining Variable](#introduce-explaining-variable)
  - [Inline Method](#inline-method)
  - [Inline Temp](#inline-temp)
- [Refactoring Techniques: Organizing Data](#refactoring-techniques-organizing-data)
  - [Encapsulate Field](#encapsulate-field)
  - [Encapsulate Collection](#encapsulate-collection)
  - [Replace Type Code with Class](#replace-type-code-with-class)
- [Refactoring Techniques: Making Method Calls Simpler](#refactoring-techniques-making-method-calls-simpler)
  - [Remove Setting Method](#remove-setting-method)
  - [Hide Method](#hide-method)
  - [Rename Method](#rename-method)
  - [Add Parameter and Remove Parameter](#add-parameter-and-remove-parameter)
- [Unit Testing and Refactoring](#unit-testing-and-refactoring)
- [Summary and Best Practices](#summary-and-best-practices)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

1. Define what code smells are and explain why they matter in software development
2. Identify common code smells in Kotlin code including duplicated code, long methods, large classes, long parameter lists, temporary fields, and data classes
3. Understand the concept of refactoring and its role in improving code quality without changing external behavior
4. Apply specific refactoring techniques to eliminate code smells in Kotlin programs
5. Recognize when to use extract method, inline method, encapsulation, and other refactoring patterns
6. Understand the relationship between unit testing and safe refactoring practices
7. Make informed decisions about when and how to refactor code to improve maintainability

---

## Introduction to Code Smells and Refactoring

### What is a Code Smell?

A code smell is not a bug—your code still works! Instead, it's a warning sign that something in your code design or implementation could be improved. Think of it like smoke: where there's smoke, there might be fire. Where there's a code smell, there might be a deeper design problem.

According to Fowler (2006), a code smell is "a surface indication that usually corresponds to a deeper problem in the system." Tufano et al. (2015) describe them as "symptoms of poor design and implementation choices."

### What is Refactoring?

Refactoring is the process of improving your code's internal structure without changing what it does externally. Fowler (1999) defines it as:

> "Changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure. It is a disciplined way to clean up the code that minimizes the chances of introducing bugs."

The key principles of refactoring:
- **No new functionality**: You're not adding features, just improving structure
- **Preserve behavior**: Tests should still pass after refactoring
- **Small steps**: Make incremental changes that can be easily verified
- **Continuous improvement**: Refactor as you go, not as a separate phase

### Why Should We Care?

- **Maintainability**: Clean code is easier to understand and modify
- **Bug prevention**: Simple, clear code has fewer hiding places for bugs
- **Team collaboration**: Other developers can work with your code more easily
- **Technical debt**: Addressing smells prevents accumulation of "debt" that slows future development

---

## Common Code Smells

### Duplicated Code

**The Problem**: The same or very similar code structure appears in multiple places. This is one of the most common and problematic code smells.

Duplicated code can appear:
- Within the same function
- In two different methods in the same class
- In subclasses that share a parent class

**Why It's Bad**:
- If you find a bug, you have to fix it in multiple places
- If you want to change behavior, you have to update multiple locations
- Increases maintenance burden and risk of inconsistency

**Example of Duplicated Code**:

```kotlin
/**
 * Example demonstrating duplicated code smell
 * The validation logic is repeated in multiple places
 */

class UserRegistration {
    // Duplicated validation in register method
    fun registerUser(username: String, email: String, password: String): Boolean {
        // Validate username
        if (username.length < 3) {
            println("Username too short")
            return false
        }
        if (username.length > 20) {
            println("Username too long")
            return false
        }
        
        // Validate email
        if (!email.contains("@")) {
            println("Invalid email")
            return false
        }
        
        println("User registered: $username")
        return true
    }
    
    // Same validation duplicated in update method
    fun updateUser(username: String, email: String): Boolean {
        // Validate username (DUPLICATED!)
        if (username.length < 3) {
            println("Username too short")
            return false
        }
        if (username.length > 20) {
            println("Username too long")
            return false
        }
        
        // Validate email (DUPLICATED!)
        if (!email.contains("@")) {
            println("Invalid email")
            return false
        }
        
        println("User updated: $username")
        return true
    }
}

fun main() {
    val registration = UserRegistration()
    
    // Test registration
    registration.registerUser("ab", "test@email.com", "pass123")
    registration.registerUser("validuser", "test@email.com", "pass123")
    
    // Test update
    registration.updateUser("ab", "newemail@test.com")
    registration.updateUser("validuser", "newemail@test.com")
}
```

**Sample Output**:
```
Username too short
User registered: validuser
Username too short
User updated: validuser
```

Notice how the validation logic is duplicated. If we need to change the minimum username length, we'd have to update it in two places!

---

### Long Method

**The Problem**: A method that tries to do too many things or contains too many lines of code.

**Why It's Bad**:
- Harder to understand what the method does
- Difficult to test all the different paths
- More likely to contain bugs
- Violates the Single Responsibility Principle

**Rule of Thumb**: If you can't see the entire method on your screen, it's probably too long.

**Example of Long Method**:

```kotlin
/**
 * Example demonstrating long method smell
 * This method does too many things: parsing, validation, calculation, and formatting
 */

class OrderProcessor {
    // This method is too long and does too many things!
    fun processOrder(orderData: String): String {
        // Parse the order data
        val parts = orderData.split(",")
        val itemName = parts[0]
        val quantity = parts[1].toInt()
        val priceStr = parts[2]
        val price = priceStr.toDouble()
        
        // Validate the data
        if (itemName.isEmpty()) {
            return "Error: Invalid item name"
        }
        if (quantity <= 0) {
            return "Error: Invalid quantity"
        }
        if (price < 0) {
            return "Error: Invalid price"
        }
        
        // Calculate totals
        val subtotal = quantity * price
        var discount = 0.0
        if (quantity > 10) {
            discount = subtotal * 0.1
        } else if (quantity > 5) {
            discount = subtotal * 0.05
        }
        val tax = (subtotal - discount) * 0.2
        val total = subtotal - discount + tax
        
        // Format the output
        val sb = StringBuilder()
        sb.append("Order Summary\n")
        sb.append("=============\n")
        sb.append("Item: $itemName\n")
        sb.append("Quantity: $quantity\n")
        sb.append("Unit Price: £${"%.2f".format(price)}\n")
        sb.append("Subtotal: £${"%.2f".format(subtotal)}\n")
        sb.append("Discount: £${"%.2f".format(discount)}\n")
        sb.append("Tax: £${"%.2f".format(tax)}\n")
        sb.append("Total: £${"%.2f".format(total)}\n")
        
        return sb.toString()
    }
}

fun main() {
    val processor = OrderProcessor()
    
    // Process an order
    val result = processor.processOrder("Laptop,12,500.00")
    println(result)
}
```

**Sample Output**:
```
Order Summary
=============
Item: Laptop
Quantity: 12
Unit Price: £500.00
Subtotal: £6000.00
Discount: £600.00
Tax: £1080.00
Total: £6480.00
```

This method does parsing, validation, calculation, and formatting all in one place. It's hard to test individual pieces and hard to understand at a glance.

---

### Large Class

**The Problem**: A class that tries to do too much and has too many responsibilities.

**Why It's Bad**:
- Violates the Single Responsibility Principle
- Hard to understand and maintain
- Often has too many instance variables
- Common in GUI classes where everything gets dumped into one place

**Example of Large Class**:

```kotlin
/**
 * Example demonstrating large class smell
 * This class handles user management, email, logging, and database operations
 * It should be split into multiple focused classes
 */

class UserManager {
    // Too many instance variables - warning sign!
    private val users = mutableListOf<String>()
    private val emails = mutableMapOf<String, String>()
    private val logMessages = mutableListOf<String>()
    private var dbConnection: String = "localhost:5432"
    private var emailServer: String = "smtp.example.com"
    private var logFile: String = "app.log"
    
    // User management responsibilities
    fun addUser(username: String, email: String) {
        users.add(username)
        emails[username] = email
        log("User added: $username")
        saveToDatabase(username, email)
    }
    
    fun removeUser(username: String) {
        users.remove(username)
        emails.remove(username)
        log("User removed: $username")
        deleteFromDatabase(username)
    }
    
    // Email responsibilities
    fun sendEmail(username: String, subject: String, body: String) {
        val email = emails[username] ?: return
        log("Sending email to $email")
        println("Connecting to $emailServer...")
        println("Sending to: $email")
        println("Subject: $subject")
        println("Body: $body")
    }
    
    // Logging responsibilities
    fun log(message: String) {
        val timestamp = System.currentTimeMillis()
        val logEntry = "[$timestamp] $message"
        logMessages.add(logEntry)
        println("LOG: $logEntry")
    }
    
    fun printLogs() {
        println("=== Log History ===")
        logMessages.forEach { println(it) }
    }
    
    // Database responsibilities
    fun saveToDatabase(username: String, email: String) {
        println("Connecting to database: $dbConnection")
        println("Saving user: $username")
    }
    
    fun deleteFromDatabase(username: String) {
        println("Connecting to database: $dbConnection")
        println("Deleting user: $username")
    }
    
    // Configuration responsibilities
    fun setDatabaseConnection(connection: String) {
        dbConnection = connection
        log("Database connection updated")
    }
    
    fun setEmailServer(server: String) {
        emailServer = server
        log("Email server updated")
    }
}

fun main() {
    val manager = UserManager()
    
    // Using the bloated class
    manager.addUser("alice", "alice@example.com")
    manager.sendEmail("alice", "Welcome", "Welcome to our service!")
    manager.printLogs()
}
```

**Sample Output**:
```
LOG: [1732198745123] User added: alice
Connecting to database: localhost:5432
Saving user: alice
Connecting to smtp.example.com...
Sending to: alice@example.com
Subject: Welcome
Body: Welcome to our service!
LOG: [1732198745125] Sending email to alice@example.com
=== Log History ===
[1732198745123] User added: alice
[1732198745125] Sending email to alice@example.com
```

This class has at least four distinct responsibilities: user management, email, logging, and database operations. It should be split into separate classes.

---

### Long Parameter List

**The Problem**: Passing too many parameters to a method makes it hard to understand and use.

**Why It's Bad**:
- Hard to remember the order of parameters
- Easy to pass parameters in the wrong order
- Often indicates the method is trying to do too much
- May include redundant parameters that are already instance variables

**Example of Long Parameter List**:

```kotlin
/**
 * Example demonstrating long parameter list smell
 * The method has too many parameters, some of which could be instance variables
 */

class ReportGenerator {
    // Some of these could be instance variables instead of parameters
    fun generateReport(
        title: String,
        author: String,
        date: String,
        department: String,
        category: String,
        priority: Int,
        includeCharts: Boolean,
        includeStatistics: Boolean,
        fontSize: Int,
        fontFamily: String,
        pageSize: String,
        orientation: String
    ): String {
        val report = StringBuilder()
        report.append("=== $title ===\n")
        report.append("Author: $author\n")
        report.append("Date: $date\n")
        report.append("Department: $department\n")
        report.append("Category: $category\n")
        report.append("Priority: $priority\n")
        report.append("Font: $fontFamily, ${fontSize}pt\n")
        report.append("Page: $pageSize ($orientation)\n")
        report.append("Charts: ${if (includeCharts) "Yes" else "No"}\n")
        report.append("Statistics: ${if (includeStatistics) "Yes" else "No"}\n")
        report.append("\n[Report content would go here]\n")
        return report.toString()
    }
}

fun main() {
    val generator = ReportGenerator()
    
    // Look at this overwhelming method call!
    val report = generator.generateReport(
        "Monthly Sales Report",
        "John Smith",
        "2024-11-21",
        "Sales",
        "Financial",
        1,
        true,
        true,
        12,
        "Arial",
        "A4",
        "Portrait"
    )
    
    println(report)
}
```

**Sample Output**:
```
=== Monthly Sales Report ===
Author: John Smith
Date: 2024-11-21
Department: Sales
Category: Financial
Priority: 1
Font: Arial, 12pt
Page: A4 (Portrait)
Charts: Yes
Statistics: Yes

[Report content would go here]
```

This method has 12 parameters! It's error-prone and hard to use. Many of these could be instance variables or could be grouped into configuration objects.

---

### Temporary Field

**The Problem**: An instance variable that is only set or used in certain circumstances, not throughout the object's lifetime.

**Why It's Bad**:
- Confusing because the field is sometimes empty/null
- Wastes memory when not in use
- Makes the object's state unclear
- Often indicates the field should belong to a different class or be a local variable

**Example of Temporary Field**:

```kotlin
/**
 * Example demonstrating temporary field smell
 * The calculationCache field is only used during complex calculations
 * and remains null most of the time
 */

class DataProcessor {
    private val data = mutableListOf<Int>()
    
    // This field is only used during calculateStatistics()
    // It's null most of the time - that's the smell!
    private var calculationCache: MutableMap<String, Double>? = null
    
    fun addData(value: Int) {
        data.add(value)
    }
    
    fun calculateStatistics(): Map<String, Double> {
        // Temporary field is only set here
        calculationCache = mutableMapOf()
        
        val sum = data.sum().toDouble()
        val average = sum / data.size
        val max = data.maxOrNull()?.toDouble() ?: 0.0
        val min = data.minOrNull()?.toDouble() ?: 0.0
        
        // Using the temporary field
        calculationCache!!["sum"] = sum
        calculationCache!!["average"] = average
        calculationCache!!["max"] = max
        calculationCache!!["min"] = min
        
        val result = calculationCache!!.toMap()
        
        // Temporary field is cleared after use
        calculationCache = null
        
        return result
    }
    
    fun printData() {
        println("Data: $data")
        println("Cache status: ${if (calculationCache == null) "Empty" else "Active"}")
    }
}

fun main() {
    val processor = DataProcessor()
    
    // Add some data
    processor.addData(10)
    processor.addData(20)
    processor.addData(30)
    
    println("Before calculation:")
    processor.printData()
    
    // Calculate statistics (this is when the temporary field is used)
    val stats = processor.calculateStatistics()
    println("\nStatistics: $stats")
    
    println("\nAfter calculation:")
    processor.printData()
}
```

**Sample Output**:
```
Before calculation:
Data: [10, 20, 30]
Cache status: Empty

Statistics: {sum=60.0, average=20.0, max=30.0, min=10.0}

After calculation:
Data: [10, 20, 30]
Cache status: Empty
```

The `calculationCache` field is null most of the time and only used during one method. This suggests it should be a local variable or moved to a separate class.

---

### Data Class

**The Problem**: Classes that only contain fields with getters and setters, but no meaningful behavior.

**Why It's Bad**:
- Violates the principle of encapsulation
- Business logic ends up scattered in other classes
- Setters might not be needed at all
- Collections might be fully modifiable from outside, breaking encapsulation

**Note**: Kotlin's `data class` keyword is different—it's a useful language feature. The smell refers to classes that are just "data holders" with no behavior protection.

**Example of Data Class Smell**:

```kotlin
/**
 * Example demonstrating data class smell (not Kotlin's data class feature!)
 * This class exposes all its data with getters and setters
 * and has no meaningful behavior or validation
 */

// This is the "smelly" version - just getters and setters
class BankAccount {
    var accountNumber: String = ""
    var balance: Double = 0.0
    var accountHolder: String = ""
    var isActive: Boolean = true
    
    // No validation, no behavior, just data access
    // Anyone can set balance to negative, change account number, etc.
}

// Example of misuse - business logic is outside the class
class AccountManager {
    fun processWithdrawal(account: BankAccount, amount: Double): Boolean {
        // Business logic is here instead of in BankAccount!
        if (!account.isActive) {
            println("Account is not active")
            return false
        }
        
        if (account.balance < amount) {
            println("Insufficient funds")
            return false
        }
        
        account.balance -= amount
        println("Withdrew £$amount. New balance: £${account.balance}")
        return true
    }
    
    fun processDeposit(account: BankAccount, amount: Double) {
        // More business logic outside the class
        if (amount <= 0) {
            println("Invalid deposit amount")
            return
        }
        
        account.balance += amount
        println("Deposited £$amount. New balance: £${account.balance}")
    }
}

fun main() {
    val account = BankAccount()
    account.accountNumber = "12345"
    account.accountHolder = "Alice Smith"
    account.balance = 1000.0
    
    // The problem: anyone can do this!
    account.balance = -500.0  // No validation!
    println("Balance set to: £${account.balance}")
    
    // Business logic is scattered in other classes
    val manager = AccountManager()
    account.balance = 1000.0  // Reset for example
    manager.processWithdrawal(account, 200.0)
    manager.processDeposit(account, 500.0)
}
```

**Sample Output**:
```
Balance set to: £-500.0
Withdrew £200.0. New balance: £800.0
Deposited £500.0. New balance: £1300.0
```

The problem here is that the `BankAccount` class has no behavior or validation. Anyone can set the balance to negative values, and all the business logic is scattered in other classes.

---

## Refactoring Techniques: Composing Methods

### Extract Method

**When to Use**: When you have code fragments that can be grouped together, or when a method is too long and complex.

**Goal**: Extract code into a clearly named method that describes its purpose.

**Benefits**:
- Improves readability
- Enables code reuse
- Makes testing easier
- Reduces complexity

**Before and After Example**:

```kotlin
/**
 * Example demonstrating Extract Method refactoring
 * We'll refactor the duplicated validation code from earlier
 */

// BEFORE: Duplicated validation code
class UserRegistrationBefore {
    fun registerUser(username: String, email: String, password: String): Boolean {
        // Validation code duplicated
        if (username.length < 3) {
            println("Username too short")
            return false
        }
        if (username.length > 20) {
            println("Username too long")
            return false
        }
        if (!email.contains("@")) {
            println("Invalid email")
            return false
        }
        
        println("User registered: $username")
        return true
    }
    
    fun updateUser(username: String, email: String): Boolean {
        // Same validation duplicated here
        if (username.length < 3) {
            println("Username too short")
            return false
        }
        if (username.length > 20) {
            println("Username too long")
            return false
        }
        if (!email.contains("@")) {
            println("Invalid email")
            return false
        }
        
        println("User updated: $username")
        return true
    }
}

// AFTER: Validation extracted into separate methods
class UserRegistrationAfter {
    // Extracted method for username validation
    private fun isValidUsername(username: String): Boolean {
        if (username.length < 3) {
            println("Username too short")
            return false
        }
        if (username.length > 20) {
            println("Username too long")
            return false
        }
        return true
    }
    
    // Extracted method for email validation
    private fun isValidEmail(email: String): Boolean {
        if (!email.contains("@")) {
            println("Invalid email")
            return false
        }
        return true
    }
    
    // Now the methods are much simpler
    fun registerUser(username: String, email: String, password: String): Boolean {
        if (!isValidUsername(username)) return false
        if (!isValidEmail(email)) return false
        
        println("User registered: $username")
        return true
    }
    
    fun updateUser(username: String, email: String): Boolean {
        if (!isValidUsername(username)) return false
        if (!isValidEmail(email)) return false
        
        println("User updated: $username")
        return true
    }
}

fun main() {
    println("=== BEFORE Refactoring ===")
    val before = UserRegistrationBefore()
    before.registerUser("ab", "test@email.com", "pass123")
    before.registerUser("gooduser", "test@email.com", "pass123")
    
    println("\n=== AFTER Refactoring ===")
    val after = UserRegistrationAfter()
    after.registerUser("ab", "test@email.com", "pass123")
    after.registerUser("gooduser", "test@email.com", "pass123")
}
```

**Sample Output**:
```
=== BEFORE Refactoring ===
Username too short
User registered: gooduser

=== AFTER Refactoring ===
Username too short
User registered: gooduser
```

Notice how the extracted methods have clear, descriptive names that explain what they do. The validation logic is now in one place, making it easier to maintain and test.

---

### Introduce Explaining Variable

**When to Use**: When you have a complicated expression that's hard to understand.

**Goal**: Put the result of the expression (or parts of it) in a well-named temporary variable.

**Benefits**:
- Makes complex logic more readable
- Self-documenting code
- Easier to debug

**Example**:

```kotlin
/**
 * Example demonstrating Introduce Explaining Variable refactoring
 * Complex conditional logic is made clearer with explaining variables
 */

class ShippingCalculator {
    // BEFORE: Complex expression that's hard to understand
    fun calculateShippingBefore(weight: Double, distance: Double, isPriority: Boolean): Double {
        return if ((weight > 10 && distance > 100) || (isPriority && weight > 5)) {
            weight * 2.5 + distance * 0.5 + (if (isPriority) 15.0 else 0.0)
        } else {
            weight * 1.5 + distance * 0.3
        }
    }
    
    // AFTER: Using explaining variables to make logic clear
    fun calculateShippingAfter(weight: Double, distance: Double, isPriority: Boolean): Double {
        // Introduce explaining variables
        val isHeavyAndFar = weight > 10 && distance > 100
        val isPriorityAndHeavy = isPriority && weight > 5
        val requiresSpecialHandling = isHeavyAndFar || isPriorityAndHeavy
        
        // These explaining variables make the calculation clearer
        val baseWeightCost = if (requiresSpecialHandling) weight * 2.5 else weight * 1.5
        val distanceCost = if (requiresSpecialHandling) distance * 0.5 else distance * 0.3
        val prioritySurcharge = if (isPriority) 15.0 else 0.0
        
        val totalCost = baseWeightCost + distanceCost + prioritySurcharge
        
        return totalCost
    }
}

fun main() {
    val calculator = ShippingCalculator()
    
    println("=== BEFORE: Hard to understand ===")
    val cost1 = calculator.calculateShippingBefore(12.0, 150.0, false)
    println("Cost: £${"%.2f".format(cost1)}")
    
    println("\n=== AFTER: Clear and readable ===")
    val cost2 = calculator.calculateShippingAfter(12.0, 150.0, false)
    println("Cost: £${"%.2f".format(cost2)}")
    
    // Test another scenario
    println("\n=== Priority shipment ===")
    val cost3 = calculator.calculateShippingAfter(6.0, 80.0, true)
    println("Cost: £${"%.2f".format(cost3)}")
}
```

**Sample Output**:
```
=== BEFORE: Hard to understand ===
Cost: £105.00

=== AFTER: Clear and readable ===
Cost: £105.00

=== Priority shipment ===
Cost: £54.00
```

The refactored version is much easier to understand. Each variable name explains what the code is checking or calculating.

---

### Inline Method

**When to Use**: When a method's body is just as clear as (or clearer than) its name.

**Goal**: Put the method's body into its callers and remove the method.

**When to Apply**: This is less common than Extract Method, but useful when:
- The method is trivially simple
- You have too much indirection
- The method name doesn't add clarity

**Example**:

```kotlin
/**
 * Example demonstrating Inline Method refactoring
 * Sometimes a method is so simple that inlining makes code clearer
 */

class ProductCatalog {
    private val products = listOf(
        "Laptop" to 999.99,
        "Mouse" to 29.99,
        "Keyboard" to 79.99
    )
    
    // BEFORE: Unnecessary method that doesn't add value
    fun isExpensiveBefore(price: Double): Boolean {
        return price > 100.0
    }
    
    fun getExpensiveProductsBefore(): List<String> {
        val expensive = mutableListOf<String>()
        for ((name, price) in products) {
            // Calling a method that just wraps a simple comparison
            if (isExpensiveBefore(price)) {
                expensive.add(name)
            }
        }
        return expensive
    }
    
    // AFTER: Inline the simple method for clarity
    fun getExpensiveProductsAfter(): List<String> {
        val expensive = mutableListOf<String>()
        for ((name, price) in products) {
            // Inlined the method - clearer without the indirection
            if (price > 100.0) {
                expensive.add(name)
            }
        }
        return expensive
    }
    
    // Or using Kotlin's functional approach (even better!)
    fun getExpensiveProductsKotlinWay(): List<String> {
        return products
            .filter { it.second > 100.0 }
            .map { it.first }
    }
}

fun main() {
    val catalog = ProductCatalog()
    
    println("=== BEFORE: Unnecessary indirection ===")
    val expensiveBefore = catalog.getExpensiveProductsBefore()
    println("Expensive products: $expensiveBefore")
    
    println("\n=== AFTER: Clearer with inlining ===")
    val expensiveAfter = catalog.getExpensiveProductsAfter()
    println("Expensive products: $expensiveAfter")
    
    println("\n=== BONUS: Kotlin functional style ===")
    val expensiveKotlin = catalog.getExpensiveProductsKotlinWay()
    println("Expensive products: $expensiveKotlin")
}
```

**Sample Output**:
```
=== BEFORE: Unnecessary indirection ===
Expensive products: [Laptop]

=== AFTER: Clearer with inlining ===
Expensive products: [Laptop]

=== BONUS: Kotlin functional style ===
Expensive products: [Laptop]
```

---

### Inline Temp

**When to Use**: When you have a temporary variable assigned once with a simple expression, and it's getting in the way of other refactorings.

**Goal**: Replace all references to the temporary variable with the expression itself.

**Steps**:
1. Check there are no side effects
2. Make the variable `val` (immutable) and compile to ensure it's only assigned once
3. Replace references with the expression

**Example**:

```kotlin
/**
 * Example demonstrating Inline Temp refactoring
 * Remove unnecessary temporary variables that don't add clarity
 */

class DiscountCalculator {
    // BEFORE: Unnecessary temporary variable
    fun calculateDiscountBefore(price: Double): Double {
        val basePrice = price  // Unnecessary temp variable
        return if (basePrice > 100) {
            basePrice * 0.9
        } else {
            basePrice * 0.95
        }
    }
    
    // AFTER: Inline the temp variable
    fun calculateDiscountAfter(price: Double): Double {
        // Directly use the parameter - clearer!
        return if (price > 100) {
            price * 0.9
        } else {
            price * 0.95
        }
    }
    
    // Another example - BEFORE
    fun isEligibleForFreeship pingBefore(total: Double): Boolean {
        val orderTotal = total
        val shippingThreshold = 50.0
        val isEligible = orderTotal >= shippingThreshold
        return isEligible
    }
    
    // Another example - AFTER
    fun isEligibleForFreeShippingAfter(total: Double): Boolean {
        // All those temp variables were just noise
        return total >= 50.0
    }
}

fun main() {
    val calculator = DiscountCalculator()
    
    println("=== Price Discounts ===")
    println("Before: £150 -> £${"%.2f".format(calculator.calculateDiscountBefore(150.0))}")
    println("After:  £150 -> £${"%.2f".format(calculator.calculateDiscountAfter(150.0))}")
    
    println("\n=== Free Shipping Eligibility ===")
    println("Before: £60 -> ${calculator.isEligibleForFreeShippingBefore(60.0)}")
    println("After:  £60 -> ${calculator.isEligibleForFreeShippingAfter(60.0)}")
}
```

**Sample Output**:
```
=== Price Discounts ===
Before: £150 -> £135.00
After:  £150 -> £135.00

=== Free Shipping Eligibility ===
Before: £60 -> true
After:  £60 -> true
```

**Important Note**: Only inline temps when they don't add clarity. If a temp variable has a meaningful name that explains a complex expression, keep it!

---

## Refactoring Techniques: Organizing Data

### Encapsulate Field

**When to Use**: When you have a public field that should be protected.

**Goal**: Make the field private and provide accessors (getters/setters).

**Benefits**:
- Control access to the field
- Add validation when setting values
- Change internal representation without affecting clients
- Add logging or other cross-cutting concerns

**Kotlin Note**: Kotlin properties automatically provide getters and setters, but you can still customize them for validation.

**Example**:

```kotlin
/**
 * Example demonstrating Encapsulate Field refactoring
 * Protecting fields with controlled access
 */

// BEFORE: Public field - anyone can modify it
class PersonBefore {
    var age: Int = 0  // Public field - no validation!
}

// AFTER: Encapsulated field with validation
class PersonAfter {
    // Private backing field
    private var _age: Int = 0
    
    // Public property with custom setter for validation
    var age: Int
        get() = _age
        set(value) {
            if (value < 0) {
                println("Error: Age cannot be negative. Setting to 0.")
                _age = 0
            } else if (value > 150) {
                println("Warning: Age seems unusually high. Please verify.")
                _age = value
            } else {
                _age = value
            }
        }
}

// More complex example with additional business logic
class BankAccountAfter {
    private var _balance: Double = 0.0
    private val transactionHistory = mutableListOf<String>()
    
    val balance: Double
        get() = _balance
    
    // Controlled access through methods instead of direct field access
    fun deposit(amount: Double): Boolean {
        if (amount <= 0) {
            println("Error: Deposit amount must be positive")
            return false
        }
        
        _balance += amount
        transactionHistory.add("Deposit: +£$amount")
        println("Deposited £$amount. New balance: £$_balance")
        return true
    }
    
    fun withdraw(amount: Double): Boolean {
        if (amount <= 0) {
            println("Error: Withdrawal amount must be positive")
            return false
        }
        
        if (amount > _balance) {
            println("Error: Insufficient funds")
            return false
        }
        
        _balance -= amount
        transactionHistory.add("Withdrawal: -£$amount")
        println("Withdrew £$amount. New balance: £$_balance")
        return true
    }
    
    fun getTransactionHistory(): List<String> {
        // Return a copy to prevent external modification
        return transactionHistory.toList()
    }
}

fun main() {
    println("=== BEFORE: No protection ===")
    val personBefore = PersonBefore()
    personBefore.age = -5  // This shouldn't be allowed!
    println("Age set to: ${personBefore.age}")
    
    println("\n=== AFTER: Protected with validation ===")
    val personAfter = PersonAfter()
    personAfter.age = -5  // Validation prevents invalid value
    println("Age is: ${personAfter.age}")
    personAfter.age = 25  // Valid value
    println("Age is: ${personAfter.age}")
    
    println("\n=== Bank Account Example ===")
    val account = BankAccountAfter()
    account.deposit(100.0)
    account.withdraw(30.0)
    account.withdraw(1000.0)  // Should fail
    
    println("\nTransaction History:")
    account.getTransactionHistory().forEach { println("  $it") }
}
```

**Sample Output**:
```
=== BEFORE: No protection ===
Age set to: -5

=== AFTER: Protected with validation ===
Error: Age cannot be negative. Setting to 0.
Age is: 0
Age is: 25

=== Bank Account Example ===
Deposited £100.0. New balance: £100.0
Withdrew £30.0. New balance: £70.0
Error: Insufficient funds

Transaction History:
  Deposit: +£100.0
  Withdrawal: -£30.0
```

---

### Encapsulate Collection

**When to Use**: When a method returns a collection that can be modified from outside the class.

**Goal**: Make the method return a read-only view and provide add/remove methods.

**Why It Matters**: Allowing direct access to a mutable collection breaks encapsulation. Clients can modify your internal state without your knowledge.

**Example**:

```kotlin
/**
 * Example demonstrating Encapsulate Collection refactoring
 * Protecting collections from unauthorized modification
 */

// BEFORE: Collection can be modified from outside
class CourseRosterBefore {
    val students = mutableListOf<String>()  // Exposed mutable list!
    
    fun printRoster() {
        println("Students enrolled: ${students.size}")
        students.forEach { println("  - $it") }
    }
}

// AFTER: Collection is encapsulated with controlled access
class CourseRosterAfter {
    // Private mutable collection
    private val _students = mutableListOf<String>()
    
    // Public read-only view
    val students: List<String>
        get() = _students.toList()  // Returns a copy
    
    // Controlled methods to modify the collection
    fun addStudent(name: String): Boolean {
        if (name.isBlank()) {
            println("Error: Student name cannot be blank")
            return false
        }
        
        if (_students.contains(name)) {
            println("Error: Student $name is already enrolled")
            return false
        }
        
        _students.add(name)
        println("Added student: $name")
        return true
    }
    
    fun removeStudent(name: String): Boolean {
        if (_students.remove(name)) {
            println("Removed student: $name")
            return true
        } else {
            println("Error: Student $name not found")
            return false
        }
    }
    
    fun getStudentCount(): Int = _students.size
    
    fun printRoster() {
        println("Students enrolled: ${_students.size}")
        _students.forEach { println("  - $it") }
    }
}

fun main() {
    println("=== BEFORE: Unprotected collection ===")
    val courseBefore = CourseRosterBefore()
    courseBefore.students.add("Alice")
    courseBefore.students.add("Bob")
    courseBefore.printRoster()
    
    // The problem: anyone can do this!
    courseBefore.students.clear()  // Oops, deleted all students!
    println("\nAfter external modification:")
    courseBefore.printRoster()
    
    println("\n=== AFTER: Protected collection ===")
    val courseAfter = CourseRosterAfter()
    courseAfter.addStudent("Alice")
    courseAfter.addStudent("Bob")
    courseAfter.addStudent("Alice")  // Duplicate - should be prevented
    courseAfter.printRoster()
    
    // Now this won't work - students returns a read-only List
    // courseAfter.students.clear()  // This would be a compile error!
    
    println("\nAttempting to remove:")
    courseAfter.removeStudent("Bob")
    courseAfter.removeStudent("Charlie")  // Not enrolled
    
    println("\nFinal roster:")
    courseAfter.printRoster()
}
```

**Sample Output**:
```
=== BEFORE: Unprotected collection ===
Students enrolled: 2
  - Alice
  - Bob

After external modification:
Students enrolled: 0

=== AFTER: Protected collection ===
Added student: Alice
Added student: Bob
Error: Student Alice is already enrolled
Students enrolled: 2
  - Alice
  - Bob

Attempting to remove:
Removed student: Bob
Error: Student Charlie not found

Final roster:
Students enrolled: 1
  - Alice
```

---

### Replace Type Code with Class

**When to Use**: When a class uses numeric type codes (like integers or strings) to represent types or categories.

**Goal**: Replace the primitive type code with a class (often an enum class in Kotlin).

**Benefits**:
- Type safety - compiler catches errors
- Self-documenting code
- Can add behavior to the type
- Easier to add new types

**Example**:

```kotlin
/**
 * Example demonstrating Replace Type Code with Class refactoring
 * Using type-safe enums instead of magic numbers
 */

// BEFORE: Using "magic numbers" for employee types
class EmployeeBefore(val name: String, val employeeType: Int) {
    companion object {
        const val ENGINEER = 0
        const val MANAGER = 1
        const val SALESPERSON = 2
    }
    
    fun getBonus(): Double {
        return when (employeeType) {
            ENGINEER -> 1000.0
            MANAGER -> 2000.0
            SALESPERSON -> 1500.0
            else -> 0.0  // Easy to forget cases!
        }
    }
    
    fun getTypeDescription(): String {
        return when (employeeType) {
            ENGINEER -> "Engineer"
            MANAGER -> "Manager"
            SALESPERSON -> "Salesperson"
            else -> "Unknown"
        }
    }
}

// AFTER: Using a type-safe enum
enum class EmployeeType(val description: String, val baseBonus: Double) {
    ENGINEER("Engineer", 1000.0),
    MANAGER("Manager", 2000.0),
    SALESPERSON("Salesperson", 1500.0);
    
    // Can add behavior to the enum
    fun getPerformanceMultiplier(performanceRating: Int): Double {
        return when (this) {
            ENGINEER -> if (performanceRating > 8) 1.5 else 1.0
            MANAGER -> if (performanceRating > 7) 1.8 else 1.0
            SALESPERSON -> if (performanceRating > 9) 2.0 else 1.0
        }
    }
}

class EmployeeAfter(val name: String, val employeeType: EmployeeType) {
    fun getBonus(performanceRating: Int): Double {
        val multiplier = employeeType.getPerformanceMultiplier(performanceRating)
        return employeeType.baseBonus * multiplier
    }
    
    fun getTypeDescription(): String {
        return employeeType.description
    }
}

fun main() {
    println("=== BEFORE: Magic numbers ===")
    val emp1 = EmployeeBefore("Alice", EmployeeBefore.ENGINEER)
    println("${emp1.name} (${emp1.getTypeDescription()}): Bonus = £${emp1.getBonus()}")
    
    // The problem: easy to pass invalid values
    val emp2 = EmployeeBefore("Bob", 99)  // Invalid type code!
    println("${emp2.name} (${emp2.getTypeDescription()}): Bonus = £${emp2.getBonus()}")
    
    println("\n=== AFTER: Type-safe enum ===")
    val emp3 = EmployeeAfter("Charlie", EmployeeType.ENGINEER)
    println("${emp3.name} (${emp3.getTypeDescription()}): " +
            "Bonus (rating 9) = £${emp3.getBonus(9)}")
    
    val emp4 = EmployeeAfter("Diana", EmployeeType.MANAGER)
    println("${emp4.name} (${emp4.getTypeDescription()}): " +
            "Bonus (rating 8) = £${emp4.getBonus(8)}")
    
    // This won't compile - type safety!
    // val emp5 = EmployeeAfter("Eve", 99)  // Compiler error!
}
```

**Sample Output**:
```
=== BEFORE: Magic numbers ===
Alice (Engineer): Bonus = £1000.0
Bob (Unknown): Bonus = £0.0

=== AFTER: Type-safe enum ===
Charlie (Engineer): Bonus (rating 9) = £1500.0
Diana (Manager): Bonus (rating 8) = £3600.0
```

---

## Refactoring Techniques: Making Method Calls Simpler

### Remove Setting Method

**When to Use**: When a field should be set at creation time and never changed afterward.

**Goal**: Remove any setter method for that field and make it immutable.

**Benefits**:
- Immutability prevents bugs
- Makes the object's lifecycle clearer
- Thread-safe
- Easier to reason about

**Example**:

```kotlin
/**
 * Example demonstrating Remove Setting Method refactoring
 * Making fields immutable when they shouldn't change after construction
 */

// BEFORE: Mutable ID that shouldn't be mutable
class DocumentBefore {
    var documentId: String = ""  // Shouldn't be changeable!
    var title: String = ""
    var content: String = ""
    var createdDate: String = ""
    
    fun display() {
        println("Document ID: $documentId")
        println("Title: $title")
        println("Created: $createdDate")
        println("Content: $content")
    }
}

// AFTER: ID is immutable
class DocumentAfter(
    val documentId: String,  // Immutable - set at construction
    var title: String,        // Still mutable - can be changed
    var content: String       // Still mutable - can be changed
) {
    val createdDate: String = java.time.LocalDateTime.now().toString()  // Set once
    
    // No setter for documentId or createdDate!
    // They can only be set during construction
    
    fun display() {
        println("Document ID: $documentId")
        println("Title: $title")
        println("Created: $createdDate")
        println("Content: $content")
    }
}

// Another example: Bank Account with immutable account number
class BankAccountAfter(
    val accountNumber: String,  // Immutable
    val accountHolder: String,  // Immutable
    initialBalance: Double = 0.0
) {
    private var _balance: Double = initialBalance
    val balance: Double
        get() = _balance
    
    fun deposit(amount: Double) {
        if (amount > 0) {
            _balance += amount
            println("Deposited £$amount")
        }
    }
}

fun main() {
    println("=== BEFORE: Dangerous mutability ===")
    val docBefore = DocumentBefore()
    docBefore.documentId = "DOC-001"
    docBefore.title = "Important Report"
    docBefore.content = "Confidential information"
    docBefore.display()
    
    println("\nThe problem - ID can be changed!")
    docBefore.documentId = "HACKED"  // This shouldn't be possible!
    docBefore.display()
    
    println("\n=== AFTER: Protected immutability ===")
    val docAfter = DocumentAfter(
        documentId = "DOC-002",
        title = "Important Report",
        content = "Confidential information"
    )
    docAfter.display()
    
    // This won't compile - documentId is immutable!
    // docAfter.documentId = "HACKED"  // Compiler error!
    
    // But we can still change content
    docAfter.title = "Updated Report Title"
    println("\nAfter updating title:")
    docAfter.display()
    
    println("\n=== Bank Account Example ===")
    val account = BankAccountAfter("ACC-12345", "Alice Smith", 100.0)
    println("Account: ${account.accountNumber}, Holder: ${account.accountHolder}")
    println("Balance: £${account.balance}")
    
    // account.accountNumber = "CHANGED"  // Compiler error - immutable!
}
```

**Sample Output**:
```
=== BEFORE: Dangerous mutability ===
Document ID: DOC-001
Title: Important Report
Created: 
Content: Confidential information

The problem - ID can be changed!
Document ID: HACKED
Title: Important Report
Created: 
Content: Confidential information

=== AFTER: Protected immutability ===
Document ID: DOC-002
Title: Important Report
Created: 2024-11-21T14:23:45.678
Content: Confidential information

After updating title:
Document ID: DOC-002
Title: Updated Report Title
Created: 2024-11-21T14:23:45.678
Content: Confidential information

=== Bank Account Example ===
Account: ACC-12345, Holder: Alice Smith
Balance: £100.0
```

---

### Hide Method

**When to Use**: When a method is not used by any other class.

**Goal**: Make the method private to reduce the public interface.

**Benefits**:
- Smaller public API is easier to understand
- More flexibility to change implementation
- Prevents misuse of internal methods
- Clearer class interface

**Example**:

```kotlin
/**
 * Example demonstrating Hide Method refactoring
 * Making internal helper methods private
 */

// BEFORE: Helper methods are public unnecessarily
class ReportGeneratorBefore {
    fun generateReport(data: List<Int>): String {
        val cleaned = cleanData(data)  // Helper method
        val analyzed = analyzeData(cleaned)  // Helper method
        return formatReport(analyzed)  // Helper method
    }
    
    // These are public but only used internally!
    fun cleanData(data: List<Int>): List<Int> {
        return data.filter { it >= 0 }
    }
    
    fun analyzeData(data: List<Int>): Map<String, Double> {
        return mapOf(
            "average" to data.average(),
            "max" to data.maxOrNull()?.toDouble() ?: 0.0
        )
    }
    
    fun formatReport(analysis: Map<String, Double>): String {
        return "Analysis Report\n" +
                "Average: ${analysis["average"]}\n" +
                "Maximum: ${analysis["max"]}"
    }
}

// AFTER: Helper methods are private
class ReportGeneratorAfter {
    // Main public method
    fun generateReport(data: List<Int>): String {
        val cleaned = cleanData(data)
        val analyzed = analyzeData(cleaned)
        return formatReport(analyzed)
    }
    
    // Private helper methods - not part of public API
    private fun cleanData(data: List<Int>): List<Int> {
        return data.filter { it >= 0 }
    }
    
    private fun analyzeData(data: List<Int>): Map<String, Double> {
        return mapOf(
            "average" to data.average(),
            "max" to data.maxOrNull()?.toDouble() ?: 0.0
        )
    }
    
    private fun formatReport(analysis: Map<String, Double>): String {
        return "Analysis Report\n" +
                "Average: ${analysis["average"]}\n" +
                "Maximum: ${analysis["max"]}"
    }
}

fun main() {
    val data = listOf(10, -5, 20, 30, -10, 40)
    
    println("=== BEFORE: Too many public methods ===")
    val generatorBefore = ReportGeneratorBefore()
    println("Public methods available:")
    println("- generateReport()")
    println("- cleanData()  <- Should be private!")
    println("- analyzeData()  <- Should be private!")
    println("- formatReport()  <- Should be private!")
    
    // The problem: clients can call internal methods
    val cleaned = generatorBefore.cleanData(data)
    println("\nCleaned data (direct access): $cleaned")
    
    val report = generatorBefore.generateReport(data)
    println("\n$report")
    
    println("\n=== AFTER: Clean public interface ===")
    val generatorAfter = ReportGeneratorAfter()
    println("Public methods available:")
    println("- generateReport()  <- Only this is public")
    
    // These won't compile - methods are private!
    // val cleaned2 = generatorAfter.cleanData(data)  // Compiler error!
    // val analyzed = generatorAfter.analyzeData(cleaned)  // Compiler error!
    
    val report2 = generatorAfter.generateReport(data)
    println("\n$report2")
}
```

**Sample Output**:
```
=== BEFORE: Too many public methods ===
Public methods available:
- generateReport()
- cleanData()  <- Should be private!
- analyzeData()  <- Should be private!
- formatReport()  <- Should be private!

Cleaned data (direct access): [10, 20, 30, 40]

Analysis Report
Average: 25.0
Maximum: 40.0

=== AFTER: Clean public interface ===
Public methods available:
- generateReport()  <- Only this is public

Analysis Report
Average: 25.0
Maximum: 40.0
```

---

### Rename Method

**When to Use**: When a method's name doesn't clearly reveal its purpose.

**Goal**: Change the method name to something that clearly describes what it does.

**Benefits**:
- Self-documenting code
- Easier to understand without reading implementation
- Reduces need for comments
- Makes the API more intuitive

**Important**: In Kotlin/Java, always check if the method is inherited or overrides a parent method before renaming.

**Example**:

```kotlin
/**
 * Example demonstrating Rename Method refactoring
 * Giving methods clear, descriptive names
 */

// BEFORE: Unclear method names
class ShoppingCartBefore {
    private val items = mutableListOf<Pair<String, Double>>()
    
    // What does 'add' add? Not clear!
    fun add(name: String, price: Double) {
        items.add(Pair(name, price))
    }
    
    // What does 'calc' calculate? Ambiguous!
    fun calc(): Double {
        return items.sumOf { it.second }
    }
    
    // 'get' is vague
    fun get(): Int {
        return items.size
    }
    
    // Single letter method name - terrible!
    fun c() {
        items.clear()
    }
}

// AFTER: Clear, descriptive method names
class ShoppingCartAfter {
    private val items = mutableListOf<Pair<String, Double>>()
    
    // Clear and descriptive
    fun addItem(name: String, price: Double) {
        items.add(Pair(name, price))
        println("Added: $name (£$price)")
    }
    
    // Exactly describes what it does
    fun calculateTotalPrice(): Double {
        return items.sumOf { it.second }
    }
    
    // Much clearer than 'get'
    fun getItemCount(): Int {
        return items.size
    }
    
    // Clearly states the action
    fun clearCart() {
        items.clear()
        println("Cart cleared")
    }
    
    // Bonus: descriptive name for a complex calculation
    fun calculateTotalWithTax(taxRate: Double): Double {
        val subtotal = calculateTotalPrice()
        val tax = subtotal * taxRate
        return subtotal + tax
    }
}

fun main() {
    println("=== BEFORE: Unclear names ===")
    val cartBefore = ShoppingCartBefore()
    cartBefore.add("Laptop", 999.99)  // What am I adding?
    cartBefore.add("Mouse", 29.99)
    println("Total: £${cartBefore.calc()}")  // Total what?
    println("Items: ${cartBefore.get()}")  // Get what?
    
    println("\n=== AFTER: Clear, descriptive names ===")
    val cartAfter = ShoppingCartAfter()
    cartAfter.addItem("Laptop", 999.99)  // Clear: adding an item
    cartAfter.addItem("Mouse", 29.99)
    
    val subtotal = cartAfter.calculateTotalPrice()  // Clear: calculating price
    println("Subtotal: £${"%.2f".format(subtotal)}")
    
    val totalWithTax = cartAfter.calculateTotalWithTax(0.20)  // Clear: price with tax
    println("Total with 20% tax: £${"%.2f".format(totalWithTax)}")
    
    println("Number of items: ${cartAfter.getItemCount()}")  // Clear: item count
}
```

**Sample Output**:
```
=== BEFORE: Unclear names ===
Total: £1029.98
Items: 2

=== AFTER: Clear, descriptive names ===
Added: Laptop (£999.99)
Added: Mouse (£29.99)
Subtotal: £1029.98
Total with 20% tax: £1235.98
Number of items: 2
```

**Naming Guidelines**:
- Use verbs for methods that perform actions: `calculateTotal`, `sendEmail`, `validateInput`
- Use `get` prefix for accessors: `getStudentCount`, `getUserName`
- Use `is`/`has` prefix for boolean methods: `isValid`, `hasPermission`
- Be specific: prefer `calculateNetPrice` over `calculate`

---

### Add Parameter and Remove Parameter

**When to Use**:
- **Add Parameter**: A method needs more information from its caller
- **Remove Parameter**: A parameter is no longer used by the method body

**Goal**: Keep method signatures lean and only include necessary parameters.

**Important**: Don't add a parameter if the information can be obtained from another existing parameter or from an instance variable.

**Example**:

```kotlin
/**
 * Example demonstrating Add Parameter and Remove Parameter refactoring
 * Adjusting method signatures to match actual needs
 */

// Scenario 1: REMOVE Parameter - parameter no longer needed
class EmailServiceBefore {
    private val companyName = "TechCorp"  // Instance variable
    
    // The companyName parameter is redundant!
    fun sendWelcomeEmail(userEmail: String, userName: String, companyName: String) {
        println("Sending to: $userEmail")
        println("Dear $userName,")
        println("Welcome to $companyName!")  // Uses parameter instead of field
        println("---")
    }
}

class EmailServiceAfter {
    private val companyName = "TechCorp"  // Instance variable
    
    // Removed redundant parameter - uses instance variable instead
    fun sendWelcomeEmail(userEmail: String, userName: String) {
        println("Sending to: $userEmail")
        println("Dear $userName,")
        println("Welcome to $companyName!")  // Uses instance variable
        println("---")
    }
}

// Scenario 2: ADD Parameter - method needs more information
class InvoiceGeneratorBefore {
    // Missing important information - no way to specify currency!
    fun generateInvoice(amount: Double, customerName: String): String {
        return """
            Invoice
            Customer: $customerName
            Amount: $amount
        """.trimIndent()
    }
}

class InvoiceGeneratorAfter {
    // Added currency parameter - now we have the needed information
    fun generateInvoice(
        amount: Double,
        customerName: String,
        currency: String = "GBP"  // Default value for convenience
    ): String {
        val symbol = when (currency) {
            "GBP" -> "£"
            "USD" -> "$"
            "EUR" -> "€"
            else -> currency
        }
        
        return """
            Invoice
            Customer: $customerName
            Amount: $symbol${"%.2f".format(amount)}
        """.trimIndent()
    }
}

fun main() {
    println("=== REMOVE Parameter Example ===")
    println("BEFORE: Redundant parameter")
    val emailBefore = EmailServiceBefore()
    emailBefore.sendWelcomeEmail("alice@email.com", "Alice", "TechCorp")
    
    println("\nAFTER: Parameter removed")
    val emailAfter = EmailServiceAfter()
    emailAfter.sendWelcomeEmail("alice@email.com", "Alice")
    
    println("\n=== ADD Parameter Example ===")
    println("BEFORE: Missing currency information")
    val invoiceBefore = InvoiceGeneratorBefore()
    println(invoiceBefore.generateInvoice(1000.00, "Bob Smith"))
    
    println("\nAFTER: Currency parameter added")
    val invoiceAfter = InvoiceGeneratorAfter()
    println(invoiceAfter.generateInvoice(1000.00, "Bob Smith", "USD"))
    println()
    println(invoiceAfter.generateInvoice(1500.00, "Charlie Brown"))  // Uses default GBP
}
```

**Sample Output**:
```
=== REMOVE Parameter Example ===
BEFORE: Redundant parameter
Sending to: alice@email.com
Dear Alice,
Welcome to TechCorp!
---

AFTER: Parameter removed
Sending to: alice@email.com
Dear Alice,
Welcome to TechCorp!
---

=== ADD Parameter Example ===
BEFORE: Missing currency information
Invoice
Customer: Bob Smith
Amount: 1000.0

AFTER: Currency parameter added
Invoice
Customer: Bob Smith
Amount: $1000.00

Invoice
Customer: Charlie Brown
Amount: £1500.00
```

**Guidelines for Parameters**:
- **Remove** if: parameter is never used, or can be derived from existing parameters, or is already an instance variable
- **Add** if: method needs information it doesn't have access to, but first check if it should be an instance variable instead
- Keep parameter lists short (ideally 3 or fewer)
- Consider using a data class for methods with many parameters

---

## Unit Testing and Refactoring

### The Relationship Between Testing and Refactoring

Unit testing and refactoring work hand-in-hand to ensure code quality:

**Key Principles**:
1. **Tests enable safe refactoring**: With a comprehensive test suite, you can refactor confidently knowing that if you break something, the tests will catch it
2. **Refactor to make code testable**: Sometimes code smells make testing difficult, and refactoring improves testability
3. **Tests may need updating**: When you refactor method signatures or extract classes, you may need to modify or create new tests

### The Refactoring Workflow

```
┌─────────────────┐
│  Write Tests    │
│  (if needed)    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Run Tests     │
│  (all pass)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Refactor      │
│    Code         │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Run Tests     │
│   Again         │
└────────┬────────┘
         │
    ┌────┴─────┐
    │          │
    ▼          ▼
┌───────┐  ┌──────┐
│ Pass  │  │ Fail │
│ ✓     │  │  ✗   │
└───┬───┘  └──┬───┘
    │         │
    │         ▼
    │    ┌────────────┐
    │    │ Fix Code   │
    │    │ or Tests   │
    │    └─────┬──────┘
    │          │
    └──────────┘
```

### Example with Unit Tests

```kotlin
/**
 * Example demonstrating refactoring with unit tests
 * Shows how tests ensure refactoring doesn't break functionality
 */

// Original implementation with code smell (long method)
class PasswordValidatorOriginal {
    fun validatePassword(password: String): Pair<Boolean, String> {
        // Long method with multiple responsibilities
        if (password.length < 8) {
            return Pair(false, "Password must be at least 8 characters")
        }
        
        var hasUpper = false
        var hasLower = false
        var hasDigit = false
        
        for (char in password) {
            if (char.isUpperCase()) hasUpper = true
            if (char.isLowerCase()) hasLower = true
            if (char.isDigit()) hasDigit = true
        }
        
        if (!hasUpper) {
            return Pair(false, "Password must contain uppercase letter")
        }
        if (!hasLower) {
            return Pair(false, "Password must contain lowercase letter")
        }
        if (!hasDigit) {
            return Pair(false, "Password must contain digit")
        }
        
        return Pair(true, "Password is valid")
    }
}

// Refactored implementation using Extract Method
class PasswordValidatorRefactored {
    fun validatePassword(password: String): Pair<Boolean, String> {
        // Much cleaner - each validation is extracted
        if (!hasMinimumLength(password)) {
            return Pair(false, "Password must be at least 8 characters")
        }
        if (!hasUpperCase(password)) {
            return Pair(false, "Password must contain uppercase letter")
        }
        if (!hasLowerCase(password)) {
            return Pair(false, "Password must contain lowercase letter")
        }
        if (!hasDigit(password)) {
            return Pair(false, "Password must contain digit")
        }
        
        return Pair(true, "Password is valid")
    }
    
    // Extracted methods - easier to test and understand
    private fun hasMinimumLength(password: String): Boolean {
        return password.length >= 8
    }
    
    private fun hasUpperCase(password: String): Boolean {
        return password.any { it.isUpperCase() }
    }
    
    private fun hasLowerCase(password: String): Boolean {
        return password.any { it.isLowerCase() }
    }
    
    private fun hasDigit(password: String): Boolean {
        return password.any { it.isDigit() }
    }
}

// Simple test runner
class PasswordValidatorTests {
    fun runAllTests() {
        println("=== Running Tests ===\n")
        
        testValidPassword()
        testTooShort()
        testNoUpperCase()
        testNoLowerCase()
        testNoDigit()
        
        println("\n✓ All tests passed!")
    }
    
    private fun testValidPassword() {
        val validator = PasswordValidatorRefactored()
        val result = validator.validatePassword("Password123")
        assert(result.first) { "Test failed: Valid password rejected" }
        println("✓ Test passed: Valid password accepted")
    }
    
    private fun testTooShort() {
        val validator = PasswordValidatorRefactored()
        val result = validator.validatePassword("Pass1")
        assert(!result.first) { "Test failed: Short password accepted" }
        assert(result.second.contains("8 characters")) {
            "Test failed: Wrong error message"
        }
        println("✓ Test passed: Short password rejected")
    }
    
    private fun testNoUpperCase() {
        val validator = PasswordValidatorRefactored()
        val result = validator.validatePassword("password123")
        assert(!result.first) { "Test failed: Password without uppercase accepted" }
        println("✓ Test passed: No uppercase rejected")
    }
    
    private fun testNoLowerCase() {
        val validator = PasswordValidatorRefactored()
        val result = validator.validatePassword("PASSWORD123")
        assert(!result.first) { "Test failed: Password without lowercase accepted" }
        println("✓ Test passed: No lowercase rejected")
    }
    
    private fun testNoDigit() {
        val validator = PasswordValidatorRefactored()
        val result = validator.validatePassword("Password")
        assert(!result.first) { "Test failed: Password without digit accepted" }
        println("✓ Test passed: No digit rejected")
    }
}

fun main() {
    // Demonstrate that both implementations work the same
    println("=== Original Implementation ===")
    val original = PasswordValidatorOriginal()
    println(original.validatePassword("Password123"))
    println(original.validatePassword("pass"))
    
    println("\n=== Refactored Implementation ===")
    val refactored = PasswordValidatorRefactored()
    println(refactored.validatePassword("Password123"))
    println(refactored.validatePassword("pass"))
    
    // Run tests to prove refactoring preserved behavior
    println()
    val tests = PasswordValidatorTests()
    tests.runAllTests()
}
```

**Sample Output**:
```
=== Original Implementation ===
(true, Password is valid)
(false, Password must be at least 8 characters)

=== Refactored Implementation ===
(true, Password is valid)
(false, Password must be at least 8 characters)

=== Running Tests ===

✓ Test passed: Valid password accepted
✓ Test passed: Short password rejected
✓ Test passed: No uppercase rejected
✓ Test passed: No lowercase rejected
✓ Test passed: No digit rejected

✓ All tests passed!
```

### When Tests Need to Change

Sometimes refactoring requires updating tests:

1. **Method signature changes** (Add/Remove Parameter): Update test calls
2. **Extract Class**: Create tests for the new class
3. **Hide Method**: Remove tests for now-private methods (or test through public interface)
4. **Encapsulate Collection**: Update tests that directly manipulated collections

**Important**: If refactoring breaks many tests, it might indicate the refactoring is too large. Consider smaller, incremental steps.

---

## Summary and Best Practices

### Key Takeaways

1. **Code Smells Are Warning Signs**
   - They indicate potential design problems
   - Not bugs, but maintenance risks
   - Address them before they become serious issues

2. **Refactoring Improves Internal Structure**
   - Changes how code works, not what it does
   - Should be done in small, safe steps
   - Always preserve external behavior

3. **Common Refactoring Patterns**
   - Extract Method: Break down long methods
   - Encapsulation: Protect data with controlled access
   - Type Safety: Replace magic numbers with enums
   - Simplification: Remove unnecessary complexity

4. **Testing Enables Safe Refactoring**
   - Write tests before refactoring when possible
   - Run tests after each refactoring step
   - Tests document expected behavior

### When to Refactor

**Good Times to Refactor**:
- When adding a new feature (refactor to make room)
- When fixing a bug (refactor to prevent recurrence)
- During code review (refactor based on feedback)
- When you notice a code smell (refactor immediately if time permits)

**When NOT to Refactor**:
- When code works and doesn't need to change
- When you're close to a deadline (unless absolutely necessary)
- When you don't have tests and can't write them easily
- When rewriting would be faster than refactoring

### The Boy Scout Rule

> "Leave the code cleaner than you found it."

Every time you touch code:
- Fix one small thing
- Extract one method
- Rename one variable
- Add one comment

Small improvements compound over time!

### Final Thoughts

Refactoring is not a luxury—it's essential for long-term project health. Code quality matters because:
- **Developers spend more time reading code than writing it**
- **Clean code has fewer bugs**
- **Maintainable code saves money**
- **Good code is professional code**

Remember: Perfect code doesn't exist, but continuously improved code does. Embrace refactoring as an ongoing practice, not a one-time event.

**Resources for Further Learning**:
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Martin, R. C. (2008). *Clean Code: A Handbook of Agile Software Craftsmanship*
- Kotlin official documentation on best practices
- IntelliJ IDEA refactoring tools (Ctrl+Alt+Shift+T for refactoring menu)

---

**End of Lecture**