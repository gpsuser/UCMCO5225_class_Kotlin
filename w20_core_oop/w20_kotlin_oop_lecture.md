# OOP Principles, SRP, LSP, and Object Composition

---

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [Introduction](#introduction)
- [Inheritance](#inheritance)
- [Abstraction](#abstraction)
- [Encapsulation](#encapsulation)
- [Polymorphism](#polymorphism)
- [Single Responsibility Principle](#single-responsibility-principle)
- [Whole-Part Relationships and Object Composition](#whole-part-relationships-and-object-composition)
- [Liskov Substitution Principle](#liskov-substitution-principle)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain and apply the four core principles of Object-Oriented Programming: **Inheritance**, **Abstraction**, **Encapsulation**, and **Polymorphism**
- Implement these principles using Kotlin, including the use of `open`, `abstract`, and visibility modifiers
- Apply the **Single Responsibility Principle (SRP)** when designing classes
- Distinguish between **aggregation** and **composition** as forms of whole-part relationships
- Explain and apply the **Liskov Substitution Principle (LSP)** and identify common violations

---

## Introduction

Object-Oriented Programming (OOP) is a programming paradigm built around the concept of **objects** — self-contained units that combine data (properties) and behaviour (methods). OOP gives us a powerful toolkit for designing software that is modular, reusable, and maintainable.

Kotlin is a modern, statically-typed language that fully supports OOP. In this lecture, we explore the four core principles of OOP — Inheritance, Abstraction, Encapsulation, and Polymorphism — and then extend our understanding to include two influential design principles: the **Single Responsibility Principle (SRP)** and the **Liskov Substitution Principle (LSP)**. We also examine how objects can be structured together through **whole-part relationships**.

Understanding these principles will help you write better-structured code that is easier to maintain, extend, and reason about.

[↑ Back to Table of Contents](#table-of-contents)

---

## Inheritance

### What Is Inheritance?

Inheritance is one of the foundational concepts of OOP. It allows a class to **inherit** properties and behaviour from another class. The class that is inherited from is called the **superclass** (or parent class), and the class that does the inheriting is called the **subclass** (or child class).

The key idea is:

- A **superclass** defines general properties and behaviour
- A **subclass** inherits those general characteristics and may add or override them with more specific ones

A classic way to think about this is the **IS-A** relationship. For example:
- A `Kettle` IS a `HomeAppliance`
- A `Dog` IS an `Animal`
- A `SavingsAccount` IS a `BankAccount`

Because a `Kettle` IS a `HomeAppliance`, it should have all the general attributes and behaviours of a `HomeAppliance` — such as being switched on or off, having a power rating, and so on — plus its own specific features like boiling water.

### Inheritance in Kotlin

In Kotlin, classes are **closed for inheritance by default**. This is a deliberate design decision — it prevents unintended subclassing and aligns with good software design practices. To allow a class to be subclassed, you must mark it with the `open` keyword.

```kotlin
// Superclass - marked 'open' to allow inheritance
open class HomeAppliance(val brand: String, val powerRatingWatts: Int) {

    // Open function so subclasses can override it
    open fun switchOn() {
        println("$brand appliance is now on. Power rating: ${powerRatingWatts}W")
    }

    fun switchOff() {
        println("$brand appliance switched off.")
    }
}

// Subclass - inherits from HomeAppliance using the colon (:) syntax
class Kettle(brand: String, powerRatingWatts: Int, val capacityLitres: Double)
    : HomeAppliance(brand, powerRatingWatts) {

    // Override the switchOn function with more specific behaviour
    override fun switchOn() {
        println("$brand kettle is now boiling ${capacityLitres}L of water. Power: ${powerRatingWatts}W")
    }

    fun boil() {
        println("$brand kettle reached boiling point. Click!")
    }
}

// Entry point
fun main() {
    val myKettle = Kettle(brand = "Breville", powerRatingWatts = 3000, capacityLitres = 1.7)

    myKettle.switchOn()   // Calls overridden version
    myKettle.boil()       // Specific to Kettle
    myKettle.switchOff()  // Inherited from HomeAppliance
}
```

**Sample Output:**
```
Breville kettle is now boiling 1.7L of water. Power: 3000W
Breville kettle reached boiling point. Click!
Breville appliance switched off.
```

### Key Points

- Use the `open` keyword on a class to allow it to be extended
- Use the `open` keyword on a function to allow subclasses to override it
- Use `override` in the subclass to replace a function's behaviour
- The subclass constructor calls the superclass constructor using the `: SuperclassName(args)` syntax

[↑ Back to Table of Contents](#table-of-contents)

---

## Abstraction

### The Concept of Abstraction

Abstraction means focusing on **what** something does rather than **how** it does it. It lets us model complex systems at a higher level, hiding unnecessary detail and presenting only what is relevant.

Think about driving a car. When you press the accelerator, you don't need to understand the combustion process, fuel injection, or crankshaft rotation. You simply know that pressing the pedal makes the car go faster. The car's internal complexity is **abstracted away**.

In programming terms, abstraction allows us to:
- Define the **structure** and **interface** of a class without committing to a concrete implementation
- Defer implementation details to subclasses

### Abstraction in Kotlin: Abstract Classes

An **abstract class** is a class that cannot be instantiated directly. It may contain abstract functions — functions that are declared but have no body — leaving it to subclasses to provide the implementation.

```kotlin
// Abstract class - cannot be instantiated directly
abstract class Shape(val colour: String) {

    // Abstract function - no body, must be implemented by subclasses
    abstract fun area(): Double

    // Concrete function - available to all subclasses
    fun describe() {
        println("This is a $colour shape with area: ${"%.2f".format(area())}")
    }
}

// Concrete subclass implementing the abstract function
class Circle(colour: String, val radius: Double) : Shape(colour) {
    override fun area(): Double = Math.PI * radius * radius
}

// Another concrete subclass
class Rectangle(colour: String, val width: Double, val height: Double) : Shape(colour) {
    override fun area(): Double = width * height
}

fun main() {
    // val s = Shape("red")  // ERROR - cannot instantiate abstract class

    val circle = Circle(colour = "red", radius = 5.0)
    val rect = Rectangle(colour = "blue", width = 4.0, height = 6.0)

    circle.describe()
    rect.describe()
}
```

**Sample Output:**
```
This is a red shape with area: 78.54
This is a blue shape with area: 24.00
```

### Abstraction in Kotlin: Interfaces

An **interface** defines a contract — it specifies **what** a class should be able to do, without saying **how**. Any class that implements an interface must provide implementations for its declared functions.

Interfaces differ from abstract classes in several important ways:
- A class can implement **multiple** interfaces, but only extend one class
- Interfaces cannot hold constructor parameters or state (though they can have default function implementations)

```kotlin
// Interface - defines a contract for "printable" things
interface Printable {
    fun printDetails()  // No implementation
}

// Another interface
interface Saveable {
    fun saveToFile(fileName: String)  // No implementation
}

// Class implementing two interfaces
data class StudentRecord(val name: String, val studentId: String, val grade: Double)
    : Printable, Saveable {

    // Must implement printDetails from Printable
    override fun printDetails() {
        println("Student: $name | ID: $studentId | Grade: $grade")
    }

    // Must implement saveToFile from Saveable
    override fun saveToFile(fileName: String) {
        println("Saving record for $name to file: $fileName")
    }
}

fun main() {
    val record = StudentRecord(name = "Alice", studentId = "CS1042", grade = 72.5)
    record.printDetails()
    record.saveToFile("records_2025.txt")
}
```

**Sample Output:**
```
Student: Alice | ID: CS1042 | Grade: 72.5
Saving record for Alice to file: records_2025.txt
```

### Summary: Abstract Class vs Interface

| Feature                  | Abstract Class         | Interface               |
|--------------------------|------------------------|-------------------------|
| Can hold state/fields    | Yes                    | Limited (no backing fields) |
| Multiple inheritance     | No (single only)       | Yes (multiple)          |
| Constructor parameters   | Yes                    | No                      |
| Default implementations  | Yes                    | Yes (since Kotlin 1.x)  |

[↑ Back to Table of Contents](#table-of-contents)

---

## Encapsulation

### The Concept of Encapsulation

While abstraction is about **what** you can see at a high level, encapsulation is about **controlling** what can be seen and changed. Encapsulation means bundling data and the methods that operate on it into a single unit (the class), and **hiding the internal details** from the outside world.

Think of a class as a **black box** — external code can interact with it through a defined public interface, but the internal workings are hidden. This offers several benefits:

- **Data integrity** — prevents external code from putting an object into an invalid state
- **Flexibility** — you can change the internal implementation without affecting code that uses the class
- **Maintainability** — the class is self-contained and easier to reason about

### Visibility Modifiers in Kotlin

Kotlin provides four visibility modifiers to control access to classes, properties, and functions:

| Modifier    | Visibility                                                    |
|-------------|---------------------------------------------------------------|
| `private`   | Only visible **inside the class**                             |
| `protected` | Visible inside the class and in **subclasses**                |
| `internal`  | Visible anywhere in the **same module** (compiled unit)       |
| `public`    | Visible by **anything** that can see the class (the default)  |

> **Note:** If no modifier is specified, Kotlin defaults to `public`.

### Implementing Encapsulation

```kotlin
// A BankAccount class demonstrating encapsulation
class BankAccount(owner: String, initialBalance: Double) {

    // Public property - safe to expose
    val owner: String = owner

    // Private property - hidden from outside; cannot be set directly
    private var balance: Double = initialBalance

    // Private helper - internal use only
    private fun logTransaction(type: String, amount: Double) {
        println("[$type] £${"%.2f".format(amount)} | New balance: £${"%.2f".format(balance)}")
    }

    // Public method - controlled way to deposit money
    fun deposit(amount: Double) {
        if (amount > 0) {
            balance += amount
            logTransaction("DEPOSIT", amount)
        } else {
            println("Deposit amount must be positive.")
        }
    }

    // Public method - controlled way to withdraw money
    fun withdraw(amount: Double) {
        if (amount > 0 && amount <= balance) {
            balance -= amount
            logTransaction("WITHDRAW", amount)
        } else {
            println("Invalid withdrawal amount or insufficient funds.")
        }
    }

    // Public method - read-only access to balance
    fun getBalance(): Double = balance
}

fun main() {
    val account = BankAccount(owner = "Bob", initialBalance = 500.00)

    account.deposit(150.00)
    account.withdraw(200.00)
    account.withdraw(600.00)  // Should fail

    // account.balance = 9999.00  // ERROR - balance is private
    println("Final balance for ${account.owner}: £${"%.2f".format(account.getBalance())}")
}
```

**Sample Output:**
```
[DEPOSIT] £150.00 | New balance: £650.00
[WITHDRAW] £200.00 | New balance: £450.00
Invalid withdrawal amount or insufficient funds.
Final balance for Bob: £450.00
```

In this example, `balance` is `private` — no external code can set it directly. The only way to change it is through `deposit()` and `withdraw()`, which apply validation logic. This protects the account from being put into an invalid state.

[↑ Back to Table of Contents](#table-of-contents)

---

## Polymorphism

### What Is Polymorphism?

The word **polymorphism** comes from Greek, meaning *"many forms"*. In OOP, polymorphism allows objects of different types to be treated as objects of a common supertype. More practically, it means that the same method call can produce **different behaviour** depending on the actual type of the object at runtime.

Polymorphism builds directly on inheritance. Because a subclass IS-A superclass, you can write code that works with the superclass type, and at runtime the correct subclass behaviour is invoked automatically. This is called **runtime polymorphism** (or **dynamic dispatch**).

### Polymorphism in Practice: Cooking with Kotlin

Let's explore polymorphism with a cooking-themed example. We define a general `Ingredient` superclass and several subclasses. A recipe-processing function works with `Ingredient` objects — it doesn't need to know the exact type until runtime.

```kotlin
import kotlin.math.roundToInt

// Open superclass representing a generic ingredient
open class Ingredient(val name: String, val weightGrams: Int) {

    // Open function - subclasses can override preparation behaviour
    open fun prepare(): String {
        return "Prepare $name ($weightGrams g)"
    }
}

// Subclass: Vegetable has a specific preparation method
class Vegetable(name: String, weightGrams: Int, val chopStyle: String) : Ingredient(name, weightGrams) {

    override fun prepare(): String {
        return "Chop $name ($weightGrams g) into $chopStyle pieces"
    }
}

// Subclass: Protein has a different preparation method
class Protein(name: String, weightGrams: Int, val cookingMethod: String) : Ingredient(name, weightGrams) {

    override fun prepare(): String {
        return "$cookingMethod $name ($weightGrams g) until thoroughly cooked"
    }
}

// Subclass: Spice has its own behaviour
class Spice(name: String, weightGrams: Int) : Ingredient(name, weightGrams) {

    override fun prepare(): String {
        return "Add a pinch of $name ($weightGrams g) to taste"
    }
}

// Function that works with any Ingredient - polymorphism in action
fun prepareRecipe(ingredients: List<Ingredient>) {
    println("=== Preparing Recipe ===")
    for (ingredient in ingredients) {
        // The correct 'prepare()' method is called at runtime based on actual type
        println("- ${ingredient.prepare()}")
    }
}

fun main() {
    // A list of different Ingredient subtypes
    val recipe: List<Ingredient> = listOf(
        Vegetable(name = "Onion", weightGrams = 150, chopStyle = "fine"),
        Vegetable(name = "Pepper", weightGrams = 120, chopStyle = "strips"),
        Protein(name = "Chicken", weightGrams = 400, cookingMethod = "Grill"),
        Spice(name = "Cumin", weightGrams = 5)
    )

    // prepareRecipe doesn't need to know the subtypes - polymorphism handles it
    prepareRecipe(recipe)
}
```

**Sample Output:**
```
=== Preparing Recipe ===
- Chop Onion (150 g) into fine pieces
- Chop Pepper (120 g) into strips
- Grill Chicken (400 g) until thoroughly cooked
- Add a pinch of Cumin (5 g) to taste
```

Notice that `prepareRecipe()` takes a `List<Ingredient>`. It doesn't know whether each item is a `Vegetable`, `Protein`, or `Spice` — and it doesn't need to. At runtime, Kotlin calls the correct `prepare()` method for each object's actual type. This is polymorphism.

### Why Is Polymorphism Valuable?

- You can write **general-purpose code** that works with entire families of types
- Adding new subtypes (e.g., a `Sauce` class) requires **no changes** to the existing processing logic
- It reduces `if/else` or `when` chains that check object types, making code cleaner

[↑ Back to Table of Contents](#table-of-contents)

---

## Single Responsibility Principle

### The Principle

The **Single Responsibility Principle (SRP)** is one of the SOLID principles of software design, originally articulated by Robert C. Martin. It states:

> *"A class should have only one reason to change."*

What this means in practice is that each class should be responsible for **one thing** — one area of functionality, one concern, one actor. If a class has multiple responsibilities, then there are multiple reasons it might need to change, which increases the risk of bugs and makes the code harder to maintain.

### A Concrete Analogy

Consider a **DVD-TV combo unit**. It plays DVDs and displays images on a screen. Sounds convenient — but what happens when:
- Optical media evolves and you need Blu-ray support? You must modify the device.
- TV standards change to support HD? You also must modify the device.

The device has **two reasons to change**, because it has two responsibilities. If those concerns were separate — a dedicated TV and a dedicated Blu-ray player — each device would only change for its own reason.

### SRP Violation

Here is an example of a class that violates SRP:

```kotlin
// BAD EXAMPLE - This class has too many responsibilities
class OrderProcessor {

    // Responsibility 1: business logic
    fun calculateTotal(items: Map<String, Double>): Double {
        return items.values.sum()
    }

    // Responsibility 2: database persistence
    fun saveOrderToDatabase(orderId: String, total: Double) {
        println("Saving order $orderId with total £$total to database...")
    }

    // Responsibility 3: sending notifications
    fun sendConfirmationEmail(customerEmail: String, orderId: String) {
        println("Sending confirmation email to $customerEmail for order $orderId...")
    }
}
```

This class has **three reasons to change**: the business logic for calculating totals, the database storage mechanism, and the notification system.

### SRP Applied

```kotlin
// GOOD EXAMPLE - Each class has a single responsibility

// Responsibility 1: business logic only
class OrderCalculator {
    fun calculateTotal(items: Map<String, Double>): Double {
        return items.values.sum()
    }
}

// Responsibility 2: persistence only
class OrderRepository {
    fun save(orderId: String, total: Double) {
        println("Saving order $orderId with total £${"%.2f".format(total)} to database...")
    }
}

// Responsibility 3: notification only
class OrderNotifier {
    fun sendConfirmation(customerEmail: String, orderId: String) {
        println("Sending confirmation to $customerEmail for order $orderId")
    }
}

// A coordinator brings them together, but each class has one job
fun main() {
    val items = mapOf("Laptop" to 999.99, "Mouse" to 29.99, "Keyboard" to 59.99)
    val orderId = "ORD-4821"
    val email = "student@university.ac.uk"

    val calculator = OrderCalculator()
    val repository = OrderRepository()
    val notifier = OrderNotifier()

    val total = calculator.calculateTotal(items)
    repository.save(orderId, total)
    notifier.sendConfirmation(email, orderId)
}
```

**Sample Output:**
```
Saving order ORD-4821 with total £1089.97 to database...
Sending confirmation to student@university.ac.uk for order ORD-4821
```

Now, if the database changes, only `OrderRepository` needs updating. If the email provider changes, only `OrderNotifier` changes. The business logic remains untouched. Each class has **one reason to change**.

> **Note:** SRP doesn't mean a class can only have one method. An `OrderCalculator` might legitimately have methods for calculating totals, applying discounts, and adding VAT — all of which belong to the same concern (order pricing logic).

[↑ Back to Table of Contents](#table-of-contents)

---

## Whole-Part Relationships and Object Composition

### The Idea

In OOP, objects don't have to exist in isolation. A class (the "whole") can be **composed of** other objects (the "parts"). This is the foundation of **object composition** — one of the most powerful tools in software design.

Even something as simple as a class with a `String` property is using composition — the `String` is an object that forms part of the containing class.

### Why Use Composition?

Consider an `Employee` class. An employee has an address. You could add address fields directly to `Employee`:

```
Employee
  - name
  - streetLine1
  - streetLine2
  - city
  - postcode
```

But this is messy. What if multiple employees live at the same address? What if you need to reuse address logic elsewhere? A better approach is to create an `Address` class and compose it into `Employee`.

```
Employee                Address
  - name                  - streetLine1
  - address  -------->    - streetLine2
  - contracts             - city
       |                  - postcode
       v
   Contract
   - startDate
   - role
```

### Two Types of Whole-Part Relationship

There are two distinct flavours of composition in OOP:

#### Aggregation ("Has-A")

In aggregation, the part **exists independently** of the whole. The whole uses the part, but does not own it. If the whole is destroyed, the parts survive.

Example: A `Car` has `Passengers`. If you destroy the car, the passengers still exist.

#### Composition ("Must-Have-A")

In composition (strict sense), the part **has no meaningful existence** without the whole. The whole owns its parts. If the whole is destroyed, so are the parts.

Example: A `Car` has a `Gearbox`. Destroy the car, destroy the gearbox — the gearbox is meaningless without the car it belongs to.

```
+-----------------------------+         +-----------------------------+
|        AGGREGATION          |         |         COMPOSITION          |
|-----------------------------|         |-----------------------------|
|                             |         |                             |
|   Car  <>------  Passenger  |         |   Car  *------  Gearbox    |
|                             |         |                             |
|   Parts exist independently |         |   Parts owned by the whole  |
|   Lifetime NOT tied         |         |   Lifetime tied together    |
+-----------------------------+         +-----------------------------+
```

### Composition in Kotlin

```kotlin
import java.time.LocalDate

// Address exists as an independent, reusable class
data class Address(
    val streetLine1: String,
    val city: String,
    val postcode: String
)

// Contract represents an employment contract
data class Contract(
    val role: String,
    val startDate: LocalDate,
    val salaryGBP: Double
)

// Employee is composed of Address and a list of Contract objects
class Employee(
    val name: String,
    val employeeId: String,
    val address: Address,                  // Composition / Aggregation of Address
    val contracts: MutableList<Contract>   // Composition of Contract history
) {

    fun addContract(contract: Contract) {
        contracts.add(contract)
        println("New contract added for $name: ${contract.role} starting ${contract.startDate}")
    }

    fun printSummary() {
        println("\n--- Employee Summary ---")
        println("Name: $name (ID: $employeeId)")
        println("Address: ${address.streetLine1}, ${address.city}, ${address.postcode}")
        println("Contract History:")
        contracts.forEach {
            println("  - ${it.role} | From: ${it.startDate} | Salary: £${"%.2f".format(it.salaryGBP)}")
        }
    }
}

fun main() {
    // Create an Address object
    val homeAddress = Address(
        streetLine1 = "12 Maple Street",
        city = "Birmingham",
        postcode = "B1 2AB"
    )

    // Create initial contract
    val firstContract = Contract(
        role = "Junior Developer",
        startDate = LocalDate.of(2022, 9, 1),
        salaryGBP = 28000.00
    )

    // Create Employee, composed of Address and initial Contract
    val employee = Employee(
        name = "Sarah Chen",
        employeeId = "EMP-1091",
        address = homeAddress,
        contracts = mutableListOf(firstContract)
    )

    // Add a new contract (promotion)
    val promotionContract = Contract(
        role = "Software Engineer",
        startDate = LocalDate.of(2024, 1, 15),
        salaryGBP = 42000.00
    )
    employee.addContract(promotionContract)

    employee.printSummary()
}
```

**Sample Output:**
```
New contract added for Sarah Chen: Software Engineer starting 2024-01-15

--- Employee Summary ---
Name: Sarah Chen (ID: EMP-1091)
Address: 12 Maple Street, Birmingham, B1 2AB
Contract History:
  - Junior Developer | From: 2022-09-01 | Salary: £28000.00
  - Software Engineer | From: 2024-01-15 | Salary: £42000.00
```

### Composition vs Inheritance

There is a well-known design principle: **"Favour composition over inheritance"**. Inheritance creates a tight coupling between parent and child classes, which can become fragile as a codebase grows. Composition, on the other hand, is more flexible because you can swap out composed parts without restructuring the class hierarchy. A good rule of thumb:

- Use **inheritance** when there is a genuine IS-A relationship
- Use **composition** when there is a HAS-A relationship

[↑ Back to Table of Contents](#table-of-contents)

---

## Liskov Substitution Principle

### The Principle

The **Liskov Substitution Principle (LSP)** was introduced by Barbara Liskov in her 1988 keynote address. It is the 'L' in SOLID, and it states:

> *"If S is a subtype of T, then objects of type T may be replaced by objects of type S without altering the correctness of the program."*

In simpler terms: **subtypes must be substitutable for their base types**. If you have code that works with a `Shape`, it must work correctly regardless of whether the `Shape` is a `Circle`, a `Rectangle`, or any other subtype. The behaviour should not break when you substitute a subclass for a parent class.

### LSP and Polymorphism

LSP extends polymorphism with a **behavioural contract**. Polymorphism tells us that we *can* use subclasses in place of a superclass. LSP tells us that we *must* ensure this substitution doesn't change the expected behaviour of the program.

### A Real Android Example

In Android, the `addView()` method of a `Layout` class expects a `View` parameter. However, you can pass a `Button`, a `TextView`, or any other widget — because `Button` inherits from `TextView`, which inherits from `View`. This is LSP in action: any subtype of `View` can be substituted safely.

```
View
 +-- TextView
 |    +-- Button
 |    +-- EditText
 +-- ImageView
 +-- RecyclerView
```

Any method that accepts a `View` will work correctly when passed a `Button`, an `EditText`, or an `ImageView`.

### Demonstrating LSP

Here is a correct, LSP-compliant class hierarchy:

```kotlin
// Base class: Area reporter for any shape
open class AreaReporter(val name: String) {
    open fun reportArea(): String {
        return "$name has an area (to be calculated by subclass)"
    }
}

// Circle correctly extends AreaReporter
class CircleReporter(val radius: Double) : AreaReporter("Circle") {
    override fun reportArea(): String {
        val area = Math.PI * radius * radius
        return "$name with radius $radius has area: ${"%.2f".format(area)}"
    }
}

// Square correctly extends AreaReporter
class SquareReporter(val side: Double) : AreaReporter("Square") {
    override fun reportArea(): String {
        val area = side * side
        return "$name with side $side has area: ${"%.2f".format(area)}"
    }
}

// Function that works with ANY AreaReporter subtype - LSP compliant
fun printAreaReport(reporter: AreaReporter) {
    println(reporter.reportArea())
}

fun main() {
    val shapes: List<AreaReporter> = listOf(
        CircleReporter(radius = 7.0),
        SquareReporter(side = 5.0),
        CircleReporter(radius = 3.5)
    )

    // Every subtype works correctly here - LSP is satisfied
    shapes.forEach { printAreaReport(it) }
}
```

**Sample Output:**
```
Circle with radius 7.0 has area: 153.94
Square with side 5.0 has area: 25.00
Circle with radius 3.5 has area: 38.48
```

### LSP Violation: The if/else Type Check

A **common way to violate LSP** is to use `if/else` (or Kotlin's `when`) to check the actual subtype inside a method, and then change behaviour based on it. If you find yourself writing this kind of code, it is a sign that your class hierarchy may not truly be substitutable.

```kotlin
// BAD EXAMPLE - LSP Violation

open class Bird(val name: String) {
    open fun move() {
        println("$name is moving")
    }
}

class Eagle(name: String) : Bird(name) {
    override fun move() {
        println("$name is soaring through the sky")
    }
}

class Penguin(name: String) : Bird(name) {
    // Penguins can't fly - this creates a behavioural mismatch
    override fun move() {
        println("$name is waddling along the ground")
    }
}

// VIOLATION: The function checks subtype to adjust behaviour
// This defeats the purpose of polymorphism and LSP
fun makeItFly(bird: Bird) {
    if (bird is Penguin) {
        println("${bird.name} cannot fly! LSP is being violated here.")
    } else {
        println("${bird.name} takes flight!")
    }
}

fun main() {
    val birds: List<Bird> = listOf(Eagle("Golden Eagle"), Penguin("Emperor Penguin"))
    birds.forEach { makeItFly(it) }
}
```

**Sample Output:**
```
Golden Eagle takes flight!
Emperor Penguin cannot fly! LSP is being violated here.
```

The `makeItFly()` function cannot be used with all `Bird` subtypes interchangeably — it breaks for `Penguin`. This is a **design smell**. The fix would be to reconsider the hierarchy — perhaps `Bird` should not have a `fly()` concept at all. Instead, you might use an interface `Flyable` that only flying birds implement.

### LSP vs Polymorphism: A Summary

| Aspect                      | Polymorphism                              | Liskov Substitution Principle           |
|-----------------------------|-------------------------------------------|-----------------------------------------|
| **Focus**                   | *Can* different types be used uniformly?  | *Should* they behave consistently?      |
| **Mechanism**                | Method overriding                         | Behavioural contracts                   |
| **Violation indicator**     | N/A                                       | `if/else` or `when` checks on subtype   |
| **Goal**                    | Code reuse and flexibility                | Correctness and reliability             |

### Ensuring LSP Compliance

- Subclasses should **strengthen, not weaken** the contract of the parent class
- Subclasses should not **throw exceptions** that the parent class does not
- If a subclass requires special handling, **reconsider the inheritance hierarchy** — perhaps an interface-based approach is more appropriate

[↑ Back to Table of Contents](#table-of-contents)

---
