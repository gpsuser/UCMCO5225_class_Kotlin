# Lecture 3: Kotlin Worksheet

---

## Part 1: Quiz Section

### Question 1: Fill in the Blanks

Complete the following paragraph by filling in the blanks with the appropriate words from the word bank below.

In Kotlin, all classes are __________ by default, which means they cannot be inherited unless explicitly marked with the __________ keyword. When a subclass inherits from a superclass with a primary constructor, it must __________ the parent constructor directly in the class header. Similarly, methods are also final by default and must be marked as __________ if you want to allow subclasses to override them.

**Word Bank:** `call`, `override`, `final`, `open`, `abstract`, `sealed`

---

### Question 2: Fill in the Blanks

Complete the following paragraph by filling in the blanks with the appropriate words from the word bank below.

An __________ class cannot be instantiated directly and may contain both abstract and concrete methods. It can also have __________ with backing fields and constructors. In contrast, an __________ defines a contract without state and a class can implement multiple of them. The Single Responsibility Principle states that a class should have only one __________ to change.

**Word Bank:** `properties`, `reason`, `interface`, `abstract`, `method`, `constructor`

---

### Question 3: Multiple Choice

Which of the following statements about inheritance in Kotlin is **correct**?

A) A class can inherit from multiple abstract classes simultaneously  
B) Interfaces cannot have default method implementations in Kotlin  
C) A subclass can override a method without the parent method being marked as `open`  
D) If you want to inherit from a class further down the hierarchy, intermediate classes must also be marked as `open`

---

### Question 4: Multiple Choice

Which of the following scenarios represents a **violation** of the Liskov Substitution Principle (LSP)?

A) A `Dog` class that overrides the `makeSound()` method from `Animal` to print "Woof!"  
B) A `Square` class that inherits from `Rectangle` but forces width and height to always be equal  
C) A `Circle` class that implements the `area()` method from the abstract `Shape` class  
D) A `Penguin` class that overrides the `move()` method from `Bird` to waddle instead of fly

---

## Part 2: Task Section

### Task 1: Library Management System

Create a simple library management system using inheritance and interfaces.

**Requirements:**
- Create an abstract class `LibraryItem` with properties for `title` and `itemId`
- Create an interface `Borrowable` with methods `checkOut()` and `returnItem()`
- Implement two concrete classes: `Book` and `DVD`
- `Book` should have an additional property `author`
- `DVD` should have an additional property `duration` (in minutes)
- Both should implement the `Borrowable` interface

**Code Scaffolding:**

```kotlin
// Define your abstract class here
abstract class LibraryItem(val title: String, val itemId: String) {
    // Add an abstract method to display item details
}

// Define your interface here
interface Borrowable {
    // Add methods for checkout and return
}

// Implement Book class
class Book(
    // Add parameters
) : LibraryItem(/* ... */), Borrowable {
    // Implement required methods
}

// Implement DVD class
class DVD(
    // Add parameters
) : LibraryItem(/* ... */), Borrowable {
    // Implement required methods
}

fun main() {
    val book = Book("1984", "B001", "George Orwell")
    val dvd = DVD("Inception", "D001", 148)
    
    book.displayDetails()
    book.checkOut()
    book.returnItem()
    
    println()
    
    dvd.displayDetails()
    dvd.checkOut()
    dvd.returnItem()
}
```

**Expected Output:**
```
Book: 1984 (ID: B001)
Author: George Orwell
Checking out: 1984
Returning: 1984

DVD: Inception (ID: D001)
Duration: 148 minutes
Checking out: Inception
Returning: Inception
```

---

### Task 2: Payment Processing System

Create a payment processing system that demonstrates composition and polymorphism.

**Requirements:**
- Create an interface `PaymentMethod` with a method `processPayment(amount: Double)`
- Create classes `CreditCard`, `PayPal`, and `BankTransfer` that implement `PaymentMethod`
- Create a `PaymentProcessor` class that uses composition to hold a `PaymentMethod`
- The `PaymentProcessor` should be able to process payments using any payment method
- Each payment method should print a specific message when processing

**Code Scaffolding:**

```kotlin
// Define PaymentMethod interface
interface PaymentMethod {
    // Add processPayment method
}

// Implement CreditCard class
class CreditCard(private val cardNumber: String) : PaymentMethod {
    override fun processPayment(amount: Double) {
        // Implement credit card payment processing
    }
}

// Implement PayPal class
class PayPal(private val email: String) : PaymentMethod {
    override fun processPayment(amount: Double) {
        // Implement PayPal payment processing
    }
}

// Implement BankTransfer class
class BankTransfer(private val accountNumber: String) : PaymentMethod {
    override fun processPayment(amount: Double) {
        // Implement bank transfer processing
    }
}

// Implement PaymentProcessor using composition
class PaymentProcessor(private val paymentMethod: PaymentMethod) {
    fun executePayment(amount: Double) {
        // Process the payment using the composed payment method
    }
}

fun main() {
    val creditCard = CreditCard("1234-5678-9012-3456")
    val paypal = PayPal("user@example.com")
    val bankTransfer = BankTransfer("ACC123456789")
    
    val processor1 = PaymentProcessor(creditCard)
    processor1.executePayment(150.00)
    
    val processor2 = PaymentProcessor(paypal)
    processor2.executePayment(75.50)
    
    val processor3 = PaymentProcessor(bankTransfer)
    processor3.executePayment(200.00)
}
```

**Expected Output:**
```
Processing payment of $150.0
Credit Card payment: Charged $150.0 to card ****3456

Processing payment of $75.5
PayPal payment: Transferred $75.5 from user@example.com

Processing payment of $200.0
Bank Transfer payment: Transferred $200.0 from account ACC123456789
```

---

**End of Worksheet**