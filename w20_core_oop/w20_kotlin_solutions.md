# Answer Key: OOP Principles, SRP, LSP, and Object Composition

---

## Quiz Answers

### Fill in the Blanks

---

**Question 1 — Answers**

> In Kotlin, classes are **closed** for inheritance by default. To permit subclassing, the class must be marked with the **open** keyword. When a subclass replaces a function defined in the parent, the **override** keyword must appear before the function in the subclass. A subclass is also called a **child** class.

| Blank | Answer |
|-------|--------|
| 1 | `closed` |
| 2 | `open` |
| 3 | `override` |
| 4 | `child` |

**Explanation:** Kotlin deliberately closes classes by default to prevent unintended subclassing — this encourages deliberate, thoughtful inheritance hierarchies. The `open` keyword must appear at both the class level and the individual function level to enable overriding; forgetting to mark a function as `open` will result in a compile-time error when a subclass attempts to `override` it.

**Common mistakes:**
- Confusing `open` (allows extension) with `override` (replaces a function in a subclass)
- Assuming classes are open by default as they are in Java

---

**Question 2 — Answers**

> Encapsulation means hiding **implementation** details and exposing only a well-defined interface. In Kotlin, a property declared as **private** can only be accessed from within the class that owns it. The **internal** modifier makes a declaration visible anywhere within the same compiled unit. A class that enforces validation inside its methods helps maintain data **integrity**.

| Blank | Answer |
|-------|--------|
| 1 | `implementation` |
| 2 | `private` |
| 3 | `internal` |
| 4 | `integrity` |

**Explanation:** Encapsulation is not just about using `private` — it is about deliberately designing the boundary between a class and the rest of the codebase. `internal` is particularly useful in multi-file Kotlin projects where you want to share access within a module without exposing a class to external consumers. Data integrity is the practical benefit: validation in setter methods or init blocks ensures an object can never enter an invalid state.

**Common mistakes:**
- Confusing `internal` (module-level) with `protected` (subclass-level)
- Thinking `public` must be explicitly written — in Kotlin it is the default

---

**Question 3 — Answers**

> The Liskov Substitution Principle states that objects of a subtype must be **substitutable** for their base type without altering programme correctness. A common indicator of an LSP violation is the use of **if** or **when** expressions to branch on a specific subtype inside a general-purpose function. LSP is represented by the letter **L** in the SOLID acronym.

| Blank | Answer |
|-------|--------|
| 1 | `substitutable` |
| 2 | `if` / `when` (either order is acceptable) |
| 3 | `when` / `if` |
| 4 | `L` |

**Explanation:** LSP goes beyond syntactic polymorphism — it requires that substituting a subtype does not break the **expected behaviour** of the calling code. The need to type-check (`is Penguin`, `is Truck`) inside a function that should work generically is a design smell: it means the hierarchy does not truly honour the contract of the base class. The fix is often to reconsider the hierarchy or use interfaces to separate capabilities.

**Common mistakes:**
- Confusing LSP with the Open/Closed Principle (the 'O' in SOLID)
- Thinking that valid polymorphism automatically guarantees LSP compliance

---

### Multiple Choice

---

**Question 4 — Answer: B**

> B) A class should have only one reason to change

**Explanation:** The Single Responsibility Principle, coined by Robert C. Martin, is about *reasons to change*, not about the number of fields or methods. A well-designed class may have several methods — `OrderCalculator` might have `calculateSubtotal()`, `applyDiscount()`, and `addVAT()` — but they all serve one concern (pricing logic). The moment a class has to change because the email provider changed *and* because the database schema changed, it has two responsibilities.

**Why the others are wrong:**
- A: No constraint exists on field count
- C: A class may implement as many interfaces as makes sense
- D: Classes can absolutely be subclassed; LSP concerns how they should behave when substituted

---

**Question 5 — Answer: C**

> C) Aggregation

**Explanation:** In **aggregation**, the part has an independent lifetime from the whole. A `Car` has `Passengers`, but the passengers exist before they get in and continue to exist after they get out. In **composition** (strict sense), the part is owned by the whole and has no meaningful existence independently — a `Car`'s `Gearbox` has no purpose outside the car it belongs to. The diagram labelled "Type X" (diamond `<>`) represents aggregation; "Type Y" (filled `*`) represents composition.

```
Aggregation    Car  <>------  Passenger  (part outlives whole)
Composition    Car   *------  Gearbox    (part dies with whole)
```

**Why the others are wrong:**
- A: Inheritance is an IS-A relationship, not a whole-part relationship
- B: Composition is the opposite — parts are owned and their lifetime is tied to the whole
- D: Polymorphism is about behaviour, not ownership

---

**Question 6 — Answer: B**

> B) A class can implement multiple interfaces but extend only one abstract class

**Explanation:** This is the defining practical constraint. Kotlin (like Java) does not support multiple class inheritance — a class can have only one superclass. However, a class may implement any number of interfaces. This is why "favour interfaces over abstract classes" is common advice when designing a flexible type hierarchy — interfaces allow different class trees to share behavioural contracts without forcing them into a single inheritance chain.

**Why the others are wrong:**
- A: Abstract classes *can* contain concrete (non-abstract) functions — that is one of their distinguishing features vs interfaces
- C: It is the reverse — interfaces *cannot* have constructor parameters; abstract classes *can*
- D: Abstract functions are the defining feature of abstract classes and can appear in either

---

## Task Answers

### Task 1 — Vehicle Fleet Reporter

```kotlin
// Superclass marked open to allow inheritance
open class Vehicle(val make: String, val fuelType: String) {

    // Open so subclasses can override
    open fun describe(): String {
        return "$make ($fuelType)"
    }
}

// Subclass extends Vehicle using colon syntax
class Car(make: String, fuelType: String, val numDoors: Int) : Vehicle(make, fuelType) {

    override fun describe(): String {
        return "$make ($fuelType) - $numDoors doors"
    }
}

class Motorcycle(make: String, fuelType: String, val hasSidecar: Boolean) : Vehicle(make, fuelType) {

    override fun describe(): String {
        return "$make ($fuelType) - sidecar: $hasSidecar"
    }
}

fun printFleet(vehicles: List<Vehicle>) {
    println("=== Fleet Summary ===")
    for (vehicle in vehicles) {
        println(vehicle.describe())
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

**Output:**
```
=== Fleet Summary ===
Ford (Petrol) - 4 doors
Honda (Petrol) - sidecar: false
Tesla (Electric) - 4 doors
Ural (Petrol) - sidecar: true
```

**Key points:**
- `Vehicle` must be `open` and `describe()` must be `open` — forgetting either causes a compile error when subclasses try to override
- The `Car` and `Motorcycle` constructors pass `make` and `fuelType` up to the superclass via `: Vehicle(make, fuelType)`
- `printFleet()` is deliberately typed as `List<Vehicle>` — it works with any subtype. This is polymorphism at work: the correct `describe()` is resolved at runtime via dynamic dispatch
- No `if/else` or `when` type-checking is needed inside `printFleet()` — adding a new vehicle type later requires no changes to that function

**Common mistakes:**
- Forgetting `open` on the class or the function — results in a compile error
- Forgetting to pass constructor parameters up to the superclass

---

### Task 2 — Encapsulated Temperature Reading

```kotlin
class TemperatureReading(initialTemp: Double) {

    // Private backing property — cannot be accessed or modified directly
    private var temperatureCelsius: Double

    // Validate on construction
    init {
        temperatureCelsius = if (initialTemp > -273.15) initialTemp else {
            println("Error: temperature below absolute zero.")
            0.0  // Safe default
        }
    }

    fun getCelsius(): Double {
        return temperatureCelsius
    }

    fun getFahrenheit(): Double {
        return (temperatureCelsius * 9.0 / 5.0) + 32.0
    }

    fun setTemperature(value: Double) {
        if (value > -273.15) {
            temperatureCelsius = value
        } else {
            println("Error: temperature below absolute zero.")
        }
    }
}

fun main() {
    val reading = TemperatureReading(20.0)
    println("Celsius: ${reading.getCelsius()}")
    println("Fahrenheit: ${"%.1f".format(reading.getFahrenheit())}")

    reading.setTemperature(100.0)
    println("Updated Celsius: ${reading.getCelsius()}")

    reading.setTemperature(-300.0)
    println("After invalid update: ${reading.getCelsius()}")
}
```

**Output:**
```
Celsius: 20.0
Fahrenheit: 68.0
Updated Celsius: 100.0
Error: temperature below absolute zero.
After invalid update: 100.0
```

**Key points:**
- `temperatureCelsius` is `private var` — `var` is required because `setTemperature()` needs to reassign it; `val` would be immutable
- The `init` block handles construction-time validation — this ensures the object cannot be created in an invalid state even if a bad value is passed to the constructor
- The rejected value `-300.0` prints the error and leaves the stored temperature unchanged at `100.0` — this demonstrates that encapsulation preserves data integrity
- External code can never write `reading.temperatureCelsius = -999.0` — the compiler will reject it

**Common mistakes:**
- Using `val` for `temperatureCelsius` — it cannot be reassigned inside `setTemperature()`
- Forgetting the `init` block — construction-time validation is part of good encapsulation
- Using `>= -273.15` instead of `> -273.15` — absolute zero itself (`-273.15`) is theoretically unreachable but this depends on how strictly you interpret the requirement; either is defensible as long as it is consistent

---

### Challenge Task — Library System with Single Responsibility

```kotlin
data class Book(val isbn: String, val title: String, val author: String)

class BookCatalogue {
    // Private list — only BookCatalogue manages it
    private val books = mutableListOf<Book>()

    fun addBook(book: Book) {
        books.add(book)
        println("Added: ${book.title}")
    }

    fun findByIsbn(isbn: String): Book? {
        return books.firstOrNull { it.isbn == isbn }
    }

    // Expose a read-only view for reporting purposes
    fun getAllBooks(): List<Book> = books.toList()
}

class LoanManager {
    private val loanedIsbns = mutableSetOf<String>()

    fun checkOut(isbn: String) {
        loanedIsbns.add(isbn)
        println("Checked out: $isbn")
    }

    fun returnBook(isbn: String) {
        loanedIsbns.remove(isbn)
        println("Returned: $isbn")
    }

    fun isOnLoan(isbn: String): Boolean {
        return isbn in loanedIsbns
    }
}

class LibraryReporter {
    fun printCatalogueReport(catalogue: BookCatalogue, loanManager: LoanManager) {
        for (book in catalogue.getAllBooks()) {
            val status = if (loanManager.isOnLoan(book.isbn)) "[ON LOAN]" else "[AVAILABLE]"
            println("${book.title} by ${book.author} — $status")
        }
    }
}

fun main() {
    val catalogue = BookCatalogue()
    val loanManager = LoanManager()
    val reporter = LibraryReporter()

    catalogue.addBook(Book("978-0-13-110362-7", "The C Programming Language", "Kernighan & Ritchie"))
    catalogue.addBook(Book("978-0-13-468599-1", "The Pragmatic Programmer", "Hunt & Thomas"))
    catalogue.addBook(Book("978-0-20-63361-5", "Design Patterns", "Gang of Four"))

    loanManager.checkOut("978-0-13-468599-1")
    loanManager.checkOut("978-0-20-63361-5")

    println("\n--- Library Report ---")
    reporter.printCatalogueReport(catalogue, loanManager)

    loanManager.returnBook("978-0-13-468599-1")
    println("\n--- Updated Report ---")
    reporter.printCatalogueReport(catalogue, loanManager)
}
```

**Output:**
```
Added: The C Programming Language
Added: The Pragmatic Programmer
Added: Design Patterns
Checked out: 978-0-13-468599-1
Checked out: 978-0-20-63361-5

--- Library Report ---
The C Programming Language by Kernighan & Ritchie — [AVAILABLE]
The Pragmatic Programmer by Hunt & Thomas — [ON LOAN]
Design Patterns by Gang of Four — [ON LOAN]
Returned: 978-0-13-468599-1

--- Updated Report ---
The C Programming Language by Kernighan & Ritchie — [AVAILABLE]
The Pragmatic Programmer by Hunt & Thomas — [AVAILABLE]
Design Patterns by Gang of Four — [AVAILABLE]
```

**Key points about SRP in this solution:**

| Class | Single responsibility | Reason to change |
|---|---|---|
| `BookCatalogue` | Store and retrieve books | Only if the catalogue data structure changes |
| `LoanManager` | Track which ISBNs are on loan | Only if loan tracking logic changes |
| `LibraryReporter` | Format and print output | Only if the report format changes |

- `getAllBooks()` returns `books.toList()` — a defensive copy. This exposes the data for reporting without giving `LibraryReporter` the ability to modify the private list directly
- `LoanManager` uses a `MutableSet` rather than a `MutableList` — an ISBN should only appear once in the loaned set; a Set enforces this automatically
- `LibraryReporter` depends on the public interfaces of both `BookCatalogue` and `LoanManager` but owns no data itself — if the storage mechanism changes from a list to a database, only `BookCatalogue` needs updating
- Notice that `main()` acts as a simple coordinator, wiring the three components together — this mirrors how a controller or service layer functions in a real application

**Common mistakes:**
- Putting report formatting logic inside `BookCatalogue` or `LoanManager`
- Giving `LibraryReporter` direct access to the private `books` list rather than using `getAllBooks()`
- Using a `MutableList` instead of `MutableSet` for `loanedIsbns` — allows the same book to appear as checked out multiple times

**Extension ideas:**
- Add a `LoanHistory` class that logs every checkout and return event with a timestamp — note that this is a *fourth* responsibility and should be its own class, not added to `LoanManager`
- Make `BookCatalogue` searchable by title or author using `filter`
- Modify `LoanManager` to track *who* has borrowed a book using a `Map<String, String>` (isbn → borrowerName)
