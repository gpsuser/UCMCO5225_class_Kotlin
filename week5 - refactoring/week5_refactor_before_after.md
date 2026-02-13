# Code Smells: Before and After Refactoring Reference

This document provides side-by-side comparisons of common code smells and their refactored solutions in Kotlin. Each example shows the problematic code (BEFORE) and the improved version (AFTER).

---

## Table of Contents

1. [Duplicated Code](#1-duplicated-code)
2. [Long Method](#2-long-method)
3. [Large Class](#3-large-class)
4. [Long Parameter List](#4-long-parameter-list)
5. [Temporary Field](#5-temporary-field)
6. [Data Class](#6-data-class)

---

## 1. Duplicated Code

### The Problem
The same validation logic is repeated in multiple methods, making maintenance difficult and error-prone.

### BEFORE: Duplicated validation code

```kotlin
/**
 * BEFORE: Duplicated validation code
 * The validation logic appears in both registerUser and updateUser methods
 */
class UserRegistrationBefore {
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
    val registration = UserRegistrationBefore()
    
    // Test registration
    registration.registerUser("ab", "test@email.com", "pass123")
    registration.registerUser("validuser", "test@email.com", "pass123")
    
    // Test update
    registration.updateUser("ab", "newemail@test.com")
    registration.updateUser("validuser", "newemail@test.com")
}
```

### AFTER: Validation extracted into separate methods

```kotlin
/**
 * AFTER: Validation extracted into reusable methods
 * Using Extract Method refactoring to eliminate duplication
 */
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
    
    // Now the methods are much simpler and reuse validation logic
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
    val registration = UserRegistrationAfter()
    
    // Test registration
    registration.registerUser("ab", "test@email.com", "pass123")
    registration.registerUser("validuser", "test@email.com", "pass123")
    
    // Test update
    registration.updateUser("ab", "newemail@test.com")
    registration.updateUser("validuser", "newemail@test.com")
}
```

### What Changed
- ✅ Validation logic extracted into `isValidUsername()` and `isValidEmail()` methods
- ✅ Both methods now call the same validation functions
- ✅ Changes to validation rules only need to be made in one place
- ✅ Code is more maintainable and less error-prone

---

## 2. Long Method

### The Problem
A single method that does too many things: parsing, validation, calculation, and formatting. This makes it hard to understand, test, and maintain.

### BEFORE: One long method doing everything

```kotlin
/**
 * BEFORE: Long method that does too many things
 * This method handles parsing, validation, calculation, and formatting
 */
class OrderProcessorBefore {
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
    val processor = OrderProcessorBefore()
    val result = processor.processOrder("Laptop,12,500.00")
    println(result)
}
```

### AFTER: Extracted into smaller, focused methods

```kotlin
/**
 * AFTER: Long method broken down into focused methods
 * Each method has a single, clear responsibility
 */
class OrderProcessorAfter {
    // Main method coordinates the workflow
    fun processOrder(orderData: String): String {
        val order = parseOrderData(orderData)
        
        val validationError = validateOrder(order)
        if (validationError != null) {
            return validationError
        }
        
        val pricing = calculatePricing(order)
        return formatOrderSummary(order, pricing)
    }
    
    // Focused method for parsing
    private fun parseOrderData(orderData: String): OrderData {
        val parts = orderData.split(",")
        return OrderData(
            itemName = parts[0],
            quantity = parts[1].toInt(),
            unitPrice = parts[2].toDouble()
        )
    }
    
    // Focused method for validation
    private fun validateOrder(order: OrderData): String? {
        if (order.itemName.isEmpty()) {
            return "Error: Invalid item name"
        }
        if (order.quantity <= 0) {
            return "Error: Invalid quantity"
        }
        if (order.unitPrice < 0) {
            return "Error: Invalid price"
        }
        return null // No errors
    }
    
    // Focused method for calculations
    private fun calculatePricing(order: OrderData): PricingData {
        val subtotal = order.quantity * order.unitPrice
        val discount = calculateDiscount(order.quantity, subtotal)
        val tax = (subtotal - discount) * 0.2
        val total = subtotal - discount + tax
        
        return PricingData(subtotal, discount, tax, total)
    }
    
    // Helper method for discount calculation
    private fun calculateDiscount(quantity: Int, subtotal: Double): Double {
        return when {
            quantity > 10 -> subtotal * 0.1
            quantity > 5 -> subtotal * 0.05
            else -> 0.0
        }
    }
    
    // Focused method for formatting
    private fun formatOrderSummary(order: OrderData, pricing: PricingData): String {
        return buildString {
            append("Order Summary\n")
            append("=============\n")
            append("Item: ${order.itemName}\n")
            append("Quantity: ${order.quantity}\n")
            append("Unit Price: £${"%.2f".format(order.unitPrice)}\n")
            append("Subtotal: £${"%.2f".format(pricing.subtotal)}\n")
            append("Discount: £${"%.2f".format(pricing.discount)}\n")
            append("Tax: £${"%.2f".format(pricing.tax)}\n")
            append("Total: £${"%.2f".format(pricing.total)}\n")
        }
    }
    
    // Data classes for structure
    private data class OrderData(
        val itemName: String,
        val quantity: Int,
        val unitPrice: Double
    )
    
    private data class PricingData(
        val subtotal: Double,
        val discount: Double,
        val tax: Double,
        val total: Double
    )
}

fun main() {
    val processor = OrderProcessorAfter()
    val result = processor.processOrder("Laptop,12,500.00")
    println(result)
}
```

### What Changed
- ✅ Single long method split into 6 focused methods
- ✅ Each method has a single, clear responsibility
- ✅ Methods are easier to understand and test individually
- ✅ Data structures (OrderData, PricingData) make the code more organized
- ✅ Logic flow is clearer in the main `processOrder()` method

---

## 3. Large Class

### The Problem
A single class handling user management, email, logging, and database operations. This violates the Single Responsibility Principle.

### BEFORE: One class doing too many things

```kotlin
/**
 * BEFORE: Large class with too many responsibilities
 * This class handles user management, email, logging, and database operations
 */
class UserManagerBefore {
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
    val manager = UserManagerBefore()
    
    manager.addUser("alice", "alice@example.com")
    manager.sendEmail("alice", "Welcome", "Welcome to our service!")
    manager.printLogs()
}
```

### AFTER: Split into focused, single-responsibility classes

```kotlin
/**
 * AFTER: Split into multiple focused classes
 * Each class has a single, clear responsibility
 */

// Class 1: User Management
class UserRepository {
    private val users = mutableListOf<String>()
    private val emails = mutableMapOf<String, String>()
    
    fun addUser(username: String, email: String) {
        users.add(username)
        emails[username] = email
    }
    
    fun removeUser(username: String) {
        users.remove(username)
        emails.remove(username)
    }
    
    fun getEmail(username: String): String? {
        return emails[username]
    }
    
    fun getUsers(): List<String> {
        return users.toList()
    }
}

// Class 2: Email Service
class EmailService(private val serverAddress: String) {
    fun sendEmail(emailAddress: String, subject: String, body: String) {
        println("Connecting to $serverAddress...")
        println("Sending to: $emailAddress")
        println("Subject: $subject")
        println("Body: $body")
    }
}

// Class 3: Logger
class Logger {
    private val logMessages = mutableListOf<String>()
    
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
}

// Class 4: Database Access
class DatabaseService(private val connectionString: String) {
    fun saveUser(username: String, email: String) {
        println("Connecting to database: $connectionString")
        println("Saving user: $username")
    }
    
    fun deleteUser(username: String) {
        println("Connecting to database: $connectionString")
        println("Deleting user: $username")
    }
}

// Class 5: Coordinator (uses other classes)
class UserManagerAfter(
    private val userRepository: UserRepository,
    private val emailService: EmailService,
    private val logger: Logger,
    private val databaseService: DatabaseService
) {
    fun addUser(username: String, email: String) {
        userRepository.addUser(username, email)
        logger.log("User added: $username")
        databaseService.saveUser(username, email)
    }
    
    fun removeUser(username: String) {
        userRepository.removeUser(username)
        logger.log("User removed: $username")
        databaseService.deleteUser(username)
    }
    
    fun sendEmailToUser(username: String, subject: String, body: String) {
        val email = userRepository.getEmail(username) ?: return
        logger.log("Sending email to $email")
        emailService.sendEmail(email, subject, body)
    }
    
    fun printLogs() {
        logger.printLogs()
    }
}

fun main() {
    // Set up dependencies
    val userRepo = UserRepository()
    val emailService = EmailService("smtp.example.com")
    val logger = Logger()
    val database = DatabaseService("localhost:5432")
    
    // Create coordinator with dependencies
    val manager = UserManagerAfter(userRepo, emailService, logger, database)
    
    // Use the system
    manager.addUser("alice", "alice@example.com")
    manager.sendEmailToUser("alice", "Welcome", "Welcome to our service!")
    manager.printLogs()
}
```

### What Changed
- ✅ One large class split into 5 focused classes
- ✅ Each class has a single responsibility
- ✅ UserRepository: manages user data
- ✅ EmailService: handles email operations
- ✅ Logger: manages logging
- ✅ DatabaseService: handles database operations
- ✅ UserManagerAfter: coordinates between services
- ✅ Easier to test each component independently
- ✅ Can swap implementations (e.g., different database services)

---

## 4. Long Parameter List

### The Problem
A method with too many parameters is hard to use, easy to get wrong, and often indicates the method is trying to do too much.

### BEFORE: Too many parameters

```kotlin
/**
 * BEFORE: Long parameter list (12 parameters!)
 * Hard to use and easy to make mistakes
 */
class ReportGeneratorBefore {
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
    val generator = ReportGeneratorBefore()
    
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

### AFTER: Parameters grouped into configuration objects

```kotlin
/**
 * AFTER: Parameters grouped into logical data classes
 * Much easier to use and extend
 */

// Data classes to group related parameters
data class ReportMetadata(
    val title: String,
    val author: String,
    val date: String,
    val department: String,
    val category: String,
    val priority: Int
)

data class ReportOptions(
    val includeCharts: Boolean = false,
    val includeStatistics: Boolean = false
)

data class ReportFormatting(
    val fontSize: Int = 12,
    val fontFamily: String = "Arial",
    val pageSize: String = "A4",
    val orientation: String = "Portrait"
)

class ReportGeneratorAfter {
    // Much cleaner method signature with only 3 parameters!
    fun generateReport(
        metadata: ReportMetadata,
        options: ReportOptions = ReportOptions(),
        formatting: ReportFormatting = ReportFormatting()
    ): String {
        val report = StringBuilder()
        report.append("=== ${metadata.title} ===\n")
        report.append("Author: ${metadata.author}\n")
        report.append("Date: ${metadata.date}\n")
        report.append("Department: ${metadata.department}\n")
        report.append("Category: ${metadata.category}\n")
        report.append("Priority: ${metadata.priority}\n")
        report.append("Font: ${formatting.fontFamily}, ${formatting.fontSize}pt\n")
        report.append("Page: ${formatting.pageSize} (${formatting.orientation})\n")
        report.append("Charts: ${if (options.includeCharts) "Yes" else "No"}\n")
        report.append("Statistics: ${if (options.includeStatistics) "Yes" else "No"}\n")
        report.append("\n[Report content would go here]\n")
        return report.toString()
    }
}

fun main() {
    val generator = ReportGeneratorAfter()
    
    // Much clearer and easier to use!
    val metadata = ReportMetadata(
        title = "Monthly Sales Report",
        author = "John Smith",
        date = "2024-11-21",
        department = "Sales",
        category = "Financial",
        priority = 1
    )
    
    val options = ReportOptions(
        includeCharts = true,
        includeStatistics = true
    )
    
    // Can use defaults for formatting
    val report = generator.generateReport(metadata, options)
    println(report)
    
    // Or specify custom formatting
    val customFormatting = ReportFormatting(
        fontSize = 14,
        fontFamily = "Times New Roman"
    )
    val report2 = generator.generateReport(metadata, options, customFormatting)
}
```

### What Changed
- ✅ 12 parameters reduced to 3 parameter objects
- ✅ Related parameters grouped into logical data classes
- ✅ Named parameters make usage clear
- ✅ Default values provided for optional configuration
- ✅ Easy to add new parameters without changing method signature
- ✅ Much harder to pass parameters in wrong order

---

## 5. Temporary Field

### The Problem
An instance variable that's only used during certain operations and remains null most of the time, making the object's state confusing.

### BEFORE: Instance variable used temporarily

```kotlin
/**
 * BEFORE: Temporary field that's null most of the time
 * The calculationCache is only used during one method
 */
class DataProcessorBefore {
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
    val processor = DataProcessorBefore()
    
    processor.addData(10)
    processor.addData(20)
    processor.addData(30)
    
    println("Before calculation:")
    processor.printData()
    
    val stats = processor.calculateStatistics()
    println("\nStatistics: $stats")
    
    println("\nAfter calculation:")
    processor.printData()
}
```

### AFTER: Temporary field replaced with local variable

```kotlin
/**
 * AFTER: Temporary field removed - using local variable instead
 * Cleaner design with clearer object state
 */
class DataProcessorAfter {
    private val data = mutableListOf<Int>()
    
    // No more temporary field!
    
    fun addData(value: Int) {
        data.add(value)
    }
    
    fun calculateStatistics(): Map<String, Double> {
        // Use a local variable instead of an instance variable
        val statistics = mutableMapOf<String, Double>()
        
        val sum = data.sum().toDouble()
        val average = sum / data.size
        val max = data.maxOrNull()?.toDouble() ?: 0.0
        val min = data.minOrNull()?.toDouble() ?: 0.0
        
        statistics["sum"] = sum
        statistics["average"] = average
        statistics["max"] = max
        statistics["min"] = min
        
        return statistics.toMap()
    }
    
    // Or even better - extract into a separate class
    fun calculateStatisticsImproved(): Statistics {
        return Statistics(
            sum = data.sum().toDouble(),
            average = data.average(),
            max = data.maxOrNull()?.toDouble() ?: 0.0,
            min = data.minOrNull()?.toDouble() ?: 0.0,
            count = data.size
        )
    }
    
    fun printData() {
        println("Data: $data")
    }
    
    // New class to encapsulate statistics
    data class Statistics(
        val sum: Double,
        val average: Double,
        val max: Double,
        val min: Double,
        val count: Int
    )
}

fun main() {
    val processor = DataProcessorAfter()
    
    processor.addData(10)
    processor.addData(20)
    processor.addData(30)
    
    println("Data state:")
    processor.printData()
    
    val stats = processor.calculateStatistics()
    println("\nStatistics (as map): $stats")
    
    val statsObj = processor.calculateStatisticsImproved()
    println("\nStatistics (as object): $statsObj")
    println("Average: ${statsObj.average}")
}
```

### What Changed
- ✅ Temporary instance variable removed
- ✅ Replaced with local variable in the method
- ✅ Object state is now clearer (only contains actual data)
- ✅ Bonus: Created a Statistics data class for better structure
- ✅ No more nullable fields that are "sometimes set"
- ✅ Thread-safe (no shared mutable state)

---

## 6. Data Class

### The Problem
Classes that only expose data with getters/setters but have no behavior or validation. Business logic ends up scattered in other classes.

### BEFORE: Just data with no protection

```kotlin
/**
 * BEFORE: Data class smell (not Kotlin's data class feature!)
 * Exposes all data with no validation or behavior
 */

// This is the "smelly" version - just getters and setters
class BankAccountBefore {
    var accountNumber: String = ""
    var balance: Double = 0.0
    var accountHolder: String = ""
    var isActive: Boolean = true
    
    // No validation, no behavior, just data access
    // Anyone can set balance to negative, change account number, etc.
}

// Problem: Business logic is outside the class
class AccountManagerBefore {
    fun processWithdrawal(account: BankAccountBefore, amount: Double): Boolean {
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
    
    fun processDeposit(account: BankAccountBefore, amount: Double) {
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
    val account = BankAccountBefore()
    account.accountNumber = "12345"
    account.accountHolder = "Alice Smith"
    account.balance = 1000.0
    
    // The problem: anyone can do this!
    account.balance = -500.0  // No validation!
    println("Balance set to: £${account.balance}")
    
    // Business logic is scattered in other classes
    val manager = AccountManagerBefore()
    account.balance = 1000.0  // Reset for example
    manager.processWithdrawal(account, 200.0)
    manager.processDeposit(account, 500.0)
}
```

### AFTER: Encapsulated data with behavior

```kotlin
/**
 * AFTER: Proper encapsulation with behavior and validation
 * The class protects its data and contains its own business logic
 */
class BankAccountAfter(
    val accountNumber: String,  // Immutable
    val accountHolder: String,  // Immutable
    initialBalance: Double = 0.0
) {
    // Private mutable state
    private var _balance: Double = initialBalance
        set(value) {
            if (value < 0) {
                throw IllegalArgumentException("Balance cannot be negative")
            }
            field = value
        }
    
    // Public read-only access
    val balance: Double
        get() = _balance
    
    private var _isActive: Boolean = true
    val isActive: Boolean
        get() = _isActive
    
    // Transaction history for auditing
    private val transactions = mutableListOf<String>()
    
    // Business logic is IN the class where it belongs
    fun deposit(amount: Double): Boolean {
        if (amount <= 0) {
            println("Error: Deposit amount must be positive")
            return false
        }
        
        _balance += amount
        transactions.add("Deposit: +£$amount")
        println("Deposited £$amount. New balance: £$_balance")
        return true
    }
    
    fun withdraw(amount: Double): Boolean {
        if (!_isActive) {
            println("Error: Account is not active")
            return false
        }
        
        if (amount <= 0) {
            println("Error: Withdrawal amount must be positive")
            return false
        }
        
        if (amount > _balance) {
            println("Error: Insufficient funds. Balance: £$_balance")
            return false
        }
        
        _balance -= amount
        transactions.add("Withdrawal: -£$amount")
        println("Withdrew £$amount. New balance: £$_balance")
        return true
    }
    
    fun closeAccount() {
        if (_balance > 0) {
            println("Error: Cannot close account with remaining balance")
            return
        }
        _isActive = false
        transactions.add("Account closed")
        println("Account closed")
    }
    
    fun getTransactionHistory(): List<String> {
        // Return a copy to prevent external modification
        return transactions.toList()
    }
    
    fun printStatement() {
        println("\n=== Account Statement ===")
        println("Account: $accountNumber")
        println("Holder: $accountHolder")
        println("Balance: £$_balance")
        println("Status: ${if (_isActive) "Active" else "Closed"}")
        println("\nTransaction History:")
        transactions.forEach { println("  $it") }
        println("========================\n")
    }
}

fun main() {
    println("=== BEFORE: Unprotected data ===")
    val accountBefore = BankAccountBefore()
    accountBefore.accountNumber = "12345"
    accountBefore.balance = -500.0  // This shouldn't be allowed!
    println("Balance set to: £${accountBefore.balance}")
    
    println("\n=== AFTER: Protected with validation ===")
    val accountAfter = BankAccountAfter("ACC-67890", "Bob Jones", 1000.0)
    
    // These operations are now properly controlled
    accountAfter.deposit(500.0)
    accountAfter.withdraw(200.0)
    accountAfter.withdraw(2000.0)  // Should fail - insufficient funds
    
    // This won't compile - balance is read-only!
    // accountAfter.balance = -500.0  // Compiler error!
    
    // Can't modify account number either
    // accountAfter.accountNumber = "HACKED"  // Compiler error!
    
    accountAfter.printStatement()
}
```

### What Changed
- ✅ Data fields are now protected with proper encapsulation
- ✅ Business logic moved into the class where it belongs
- ✅ Validation happens at the point of modification
- ✅ Immutable fields (accountNumber, accountHolder) can't be changed
- ✅ Balance has controlled access through deposit() and withdraw()
- ✅ Transaction history tracks all operations
- ✅ Clear, intention-revealing methods
- ✅ Impossible to put the object in an invalid state

---

## Quick Reference: When to Apply Each Refactoring

| Code Smell | Primary Refactoring | When to Apply |
|-------------|-------------------|---------------|
| **Duplicated Code** | Extract Method | Same code appears 2+ times |
| **Long Method** | Extract Method | Method longer than one screen |
| **Large Class** | Extract Class | Class has 4+ distinct responsibilities |
| **Long Parameter List** | Introduce Parameter Object | More than 3-4 parameters |
| **Temporary Field** | Remove Field / Extract Class | Field is null most of the time |
| **Data Class** | Move Method / Encapsulate Field | Class has no behavior, just data |

---

## Key Principles for Clean Code

1. **Single Responsibility Principle**: Each class/method should do one thing well
2. **Don't Repeat Yourself (DRY)**: Extract common code into reusable methods
3. **Encapsulation**: Hide internal state and expose controlled interfaces
4. **Clarity Over Cleverness**: Make code easy to understand
5. **Small Methods**: Keep methods short and focused
6. **Meaningful Names**: Use descriptive names that reveal intent

---

## Testing After Refactoring

Always verify that refactoring hasn't changed behavior:

```kotlin
// Example test pattern
fun testRefactoring() {
    val before = OriginalClass()
    val after = RefactoredClass()
    
    // Test same inputs produce same outputs
    val input = "test data"
    assert(before.process(input) == after.process(input))
    
    println("✓ Refactoring preserved behavior")
}
```

---

**Remember**: The goal of refactoring is to make code easier to understand and maintain, not to show off clever tricks. Always prioritize clarity and simplicity!