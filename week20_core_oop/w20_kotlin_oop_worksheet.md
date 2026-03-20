# Worksheet: OOP Principles, SRP, LSP, and Object Composition

---

## Quiz

### Fill in the Blanks

Complete each sentence by choosing the correct word from the word bank beneath it. Each word is used exactly once.

---

**Question 1**

In Kotlin, classes are __________ for inheritance by default. To permit subclassing, the class must be marked with the __________ keyword. When a subclass replaces a function defined in the parent, the __________ keyword must appear before the function in the subclass. A subclass is also called a __________ class.

**Word bank (scrambled):** `child` &nbsp;|&nbsp; `override` &nbsp;|&nbsp; `open` &nbsp;|&nbsp; `closed`

---

**Question 2**

Encapsulation means hiding __________ details and exposing only a well-defined interface. In Kotlin, a property declared as __________ can only be accessed from within the class that owns it. The __________ modifier makes a declaration visible anywhere within the same compiled unit. A class that enforces validation inside its methods helps maintain data __________.

**Word bank (scrambled):** `integrity` &nbsp;|&nbsp; `internal` &nbsp;|&nbsp; `implementation` &nbsp;|&nbsp; `private`

---

**Question 3**

The Liskov Substitution Principle states that objects of a subtype must be __________ for their base type without altering programme correctness. A common indicator of an LSP violation is the use of __________ or __________ expressions to branch on a specific subtype inside a general-purpose function. LSP is represented by the letter __________ in the SOLID acronym.

**Word bank (scrambled):** `L` &nbsp;|&nbsp; `when` &nbsp;|&nbsp; `substitutable` &nbsp;|&nbsp; `if`

---

### Multiple Choice

Circle or state the letter of the best answer for each question.

---

**Question 4**

Which statement most accurately describes the Single Responsibility Principle?

- A) A class should contain only one field  
- B) A class should have only one reason to change  
- C) A class should implement only one interface  
- D) A class should never be subclassed  

---

**Question 5**

In a whole-part relationship, which type allows the part to **exist independently** of the whole?

```
+-------------------+       +-------------------+
|   Type X          |       |   Type Y          |
|-------------------|       |-------------------|
|  Whole <>----Part |       |  Whole *-----Part |
|  Part outlives    |       |  Part destroyed   |
|  the whole        |       |  with the whole   |
+-------------------+       +-------------------+
```

- A) Inheritance  
- B) Composition  
- C) Aggregation  
- D) Polymorphism  

---

**Question 6**

Which of the following is a key difference between an **abstract class** and an **interface** in Kotlin?

- A) Abstract classes cannot contain concrete (non-abstract) functions  
- B) A class can implement multiple interfaces but extend only one abstract class  
- C) Interfaces can declare constructor parameters; abstract classes cannot  
- D) Abstract functions may only appear inside interfaces, not abstract classes  

---

## Tasks

### Task 1 — Vehicle Fleet Reporter

A transport company needs to print a preparation summary for vehicles in its fleet. Your job is to complete the class hierarchy below so that `printFleet()` calls the correct `describe()` method for each vehicle type without needing to check which type it is.

**Requirements**

- `Vehicle` is the base class with properties `make: String` and `fuelType: String`, and an open function `describe()` that returns a basic description string
- `Car` extends `Vehicle` and adds `numDoors: Int`; its `describe()` should mention the number of doors
- `Motorcycle` extends `Vehicle` and adds `hasSidecar: Boolean`; its `describe()` should note whether a sidecar is fitted
- `printFleet()` accepts a `List<Vehicle>` and prints the result of `describe()` for every vehicle

**Scaffolding**

```kotlin
// TODO: mark this class appropriately for inheritance
class Vehicle(val make: String, val fuelType: String) {

    // TODO: add describe() — returns "$make ($fuelType)"
}

// TODO: extend Vehicle
class Car(make: String, fuelType: String, val numDoors: Int) {

    // TODO: override describe() — return "$make ($fuelType) - $numDoors doors"
}

// TODO: extend Vehicle
class Motorcycle(make: String, fuelType: String, val hasSidecar: Boolean) {

    // TODO: override describe() — return "$make ($fuelType) - sidecar: $hasSidecar"
}

fun printFleet(vehicles: List<Vehicle>) {
    println("=== Fleet Summary ===")
    for (vehicle in vehicles) {
        // TODO: call describe() and print the result
    }
}

fun main() {
    val fleet: List<Vehicle> = listOf(
        Car(make = "Ford", fuelType = "Petrol", numDoors = 4),
        Motorcycle(make = "Honda", fuelType = "Petrol", hasSidecar = false),
        Car(make = "Tesla", fuelType = "Electric", numDoors = 4),
        Motorcycle(make = "Ural", fuelType = "Petrol", hasSidecar = true)
    )
    printFleet(fleet)
}
```

**Expected Output**

```
=== Fleet Summary ===
Ford (Petrol) - 4 doors
Honda (Petrol) - sidecar: false
Tesla (Electric) - 4 doors
Ural (Petrol) - sidecar: true
```

---

### Task 2 — Encapsulated Temperature Reading

A weather station stores temperature readings. The internal value must not be modified directly — it should only be updated through a validated setter method.

**Requirements**

- `TemperatureReading` holds a private `temperatureCelsius: Double`
- `getCelsius()` returns the stored value
- `getFahrenheit()` converts and returns the value using the formula: `(C × 9/5) + 32`
- `setTemperature(value: Double)` updates the stored value **only** if it is above absolute zero (`-273.15`); otherwise, print `"Error: temperature below absolute zero."` and leave the value unchanged

**Scaffolding**

```kotlin
class TemperatureReading(initialTemp: Double) {

    // TODO: store the initial temperature as a private property
    // Hint: validate on construction too — reject values below -273.15

    fun getCelsius(): Double {
        // TODO: return the stored temperature
    }

    fun getFahrenheit(): Double {
        // TODO: convert and return (C * 9.0/5.0) + 32.0
    }

    fun setTemperature(value: Double) {
        // TODO: validate and either update or print the error message
    }
}

fun main() {
    val reading = TemperatureReading(20.0)
    println("Celsius: ${reading.getCelsius()}")
    println("Fahrenheit: ${"%.1f".format(reading.getFahrenheit())}")

    reading.setTemperature(100.0)
    println("Updated Celsius: ${reading.getCelsius()}")

    reading.setTemperature(-300.0)  // Should be rejected
    println("After invalid update: ${reading.getCelsius()}")
}
```

**Expected Output**

```
Celsius: 20.0
Fahrenheit: 68.0
Updated Celsius: 100.0
Error: temperature below absolute zero.
After invalid update: 100.0
```

---

### Challenge Task — Library System with Single Responsibility

A small library system needs to manage its book catalogue, track active loans, and produce reports. Currently all logic sits in one bloated class. Your task is to redesign it using the Single Responsibility Principle.

**Requirements**

- `Book` is a `data class` with `isbn: String`, `title: String`, and `author: String`
- `BookCatalogue` is responsible only for storing books — implement `addBook(book: Book)` and `findByIsbn(isbn: String): Book?`
- `LoanManager` is responsible only for tracking loans — implement `checkOut(isbn: String)` (adds to a `MutableSet<String>` of loaned ISBNs), `returnBook(isbn: String)`, and `isOnLoan(isbn: String): Boolean`
- `LibraryReporter` is responsible only for printing — implement `printCatalogueReport(catalogue: BookCatalogue, loanManager: LoanManager)` which lists every book and shows `[ON LOAN]` or `[AVAILABLE]` next to each title
- Wire all three together in `main()` using the sample data below

**Scaffolding**

```kotlin
data class Book(val isbn: String, val title: String, val author: String)

class BookCatalogue {
    private val books = mutableListOf<Book>()

    fun addBook(book: Book) {
        // TODO: add book to the list and print "Added: ${book.title}"
    }

    fun findByIsbn(isbn: String): Book? {
        // TODO: return the first book with a matching isbn, or null
    }
}

class LoanManager {
    private val loanedIsbns = mutableSetOf<String>()

    fun checkOut(isbn: String) {
        // TODO: add isbn to the set and print "Checked out: $isbn"
    }

    fun returnBook(isbn: String) {
        // TODO: remove isbn from the set and print "Returned: $isbn"
    }

    fun isOnLoan(isbn: String): Boolean {
        // TODO: return true if the isbn is in the set
    }
}

class LibraryReporter {
    fun printCatalogueReport(catalogue: BookCatalogue, loanManager: LoanManager) {
        // TODO: iterate over all books in the catalogue and print:
        // "${book.title} by ${book.author} — [ON LOAN] or [AVAILABLE]"
        // Hint: you will need to expose the book list somehow
    }
}

fun main() {
    val catalogue = BookCatalogue()
    val loanManager = LoanManager()
    val reporter = LibraryReporter()

    catalogue.addBook(Book("978-0-13-110362-7", "The C Programming Language", "Kernighan & Ritchie"))
    catalogue.addBook(Book("978-0-13-468599-1", "The Pragmatic Programmer", "Hunt & Thomas"))
    catalogue.addBook(Book("978-0-20-163361-5", "Design Patterns", "Gang of Four"))

    loanManager.checkOut("978-0-13-468599-1")
    loanManager.checkOut("978-0-20-63361-5")

    println("\n--- Library Report ---")
    reporter.printCatalogueReport(catalogue, loanManager)

    loanManager.returnBook("978-0-13-468599-1")
    println("\n--- Updated Report ---")
    reporter.printCatalogueReport(catalogue, loanManager)
}
```

**Expected Output**

```
Added: The C Programming Language
Added: The Pragmatic Programmer
Added: Design Patterns
Checked out: 978-0-13-468599-1
Checked out: 978-0-20-63361-5

--- Library Report ---
The C Programming Language by Kernighan & Ritchie — [AVAILABLE]
The Pragmatic Programmer by Hunt & Thomas — [ON LOAN]
Design Patterns by Gang of Four — [AVAILABLE]
Returned: 978-0-13-468599-1

--- Updated Report ---
The C Programming Language by Kernighan & Ritchie — [AVAILABLE]
The Pragmatic Programmer by Hunt & Thomas — [AVAILABLE]
Design Patterns by Gang of Four — [AVAILABLE]
```

> **Hint:** To allow `LibraryReporter` to iterate over books without giving it direct access to `BookCatalogue`'s private list, consider adding a `getAllBooks(): List<Book>` method to `BookCatalogue`.
