# Worksheet: Software Testing

---

## Table of Contents

- [Quiz](#quiz)
- [Tasks](#tasks)

---

## Quiz

### Part A: Fill in the Blanks

For each question below, choose the correct word from the scrambled word bank to complete the sentence. Each word is used once.

---

**Question 1**

Word bank: `system` &nbsp; `isolation` &nbsp; `integration` &nbsp; `unit` &nbsp; `mocked`

> A ______ test targets a single, isolated piece of functionality and often uses ______ dependencies so that only the component under test is evaluated. When those components are combined and verified together, this is called ______ testing.

---

**Question 2**

Word bank: `requirements` &nbsp; `stub` &nbsp; `pyramid` &nbsp; `bottom-up` &nbsp; `deterministic`

> In the ______ integration approach, the lowest-level components with no dependencies are verified first, before progressively testing higher-level components. A ______ is a simplified stand-in used in place of a real dependency to keep a test ______.

---

**Question 3**

Word bank: `instrumented` &nbsp; `JVM` &nbsp; `androidTest` &nbsp; `emulator` &nbsp; `test`

> In Android Studio, unit tests that run on the local ______ are placed in the `______/` source set. Tests that need to run on a device or ______ are placed in the `______/` source set and are known as ______ tests.

---

### Part B: Multiple Choice

Circle or highlight the letter of the best answer for each question.

---

**Question 4**

Which of the following is the **best** reason to write integration tests in addition to unit tests?

- A) Integration tests are faster to run than unit tests  
- B) Unit tests can miss defects that occur when components interact with each other  
- C) Integration tests replace the need for a requirements specification  
- D) Unit tests can only be written after all components are complete  

---

**Question 5**

A team decides to integrate all components of their application at once and test them together. Which integration strategy does this describe?

- A) Bottom-up integration  
- B) Top-down integration  
- C) Big bang integration  
- D) Regression testing  

---

**Question 6**

Consider the testing pyramid below. Which statement correctly describes it?

```
        /\
       /  \
      / ST \
     /------\
    /   IT   \
   /----------\
  /    Unit    \
 /--------------\
```

- A) System tests should be the most numerous because they verify the most  
- B) Unit tests are at the base because they are fast and cheap, and there should be many of them  
- C) Integration tests sit at the top because they are the most important  
- D) The pyramid shows that system tests must be written before unit tests  

---

## Tasks

### Task 1: Library and Borrower Integration

A small library system has two classes: `Library` (which holds a collection of book titles) and `BorrowingRecord` (which tracks which books a member has borrowed). Your task is to complete the `Library` class and write an integration test that verifies both classes work together correctly.

**Scaffolding:**

```kotlin
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.Assertions.assertEquals
import org.junit.jupiter.api.Assertions.assertTrue

// Tracks which books a member has currently borrowed
class BorrowingRecord {
    private val borrowedBooks = mutableListOf<String>()

    // Adds a book title to the borrowed list
    fun borrow(title: String) {
        borrowedBooks.add(title)
    }

    // Removes a book title from the borrowed list
    fun returnBook(title: String) {
        borrowedBooks.remove(title)
    }

    // Returns the current number of borrowed books
    fun count(): Int = borrowedBooks.size

    // Returns true if the given title is currently borrowed
    fun hasBorrowed(title: String): Boolean = borrowedBooks.contains(title)
}

// Holds the library's available stock of books
class Library(private val stock: MutableList<String>) {

    // TODO: implement borrowBook(title: String, record: BorrowingRecord): Boolean
    //   - If the title is in stock, remove it from stock,
    //     add it to the BorrowingRecord, and return true
    //   - If the title is not in stock, return false

    // TODO: implement returnBook(title: String, record: BorrowingRecord)
    //   - Remove the title from the BorrowingRecord
    //     and add it back to stock

    // Returns the number of books currently in stock
    fun stockCount(): Int = stock.size
}

// Integration test — wire the real classes together
class LibraryIntegrationTest {

    @Test
    fun `borrowing a book reduces stock and updates record`() {
        val library = Library(mutableListOf("Kotlin in Action", "Clean Code", "The Pragmatic Programmer"))
        val record = BorrowingRecord()

        // TODO: borrow "Clean Code" and assert:
        //   - borrowBook returns true
        //   - record.hasBorrowed("Clean Code") is true
        //   - library.stockCount() is 2
    }

    @Test
    fun `returning a book restores stock and updates record`() {
        val library = Library(mutableListOf("Kotlin in Action"))
        val record = BorrowingRecord()

        // TODO: borrow then return "Kotlin in Action" and assert:
        //   - record.count() is 0
        //   - library.stockCount() is 1
    }
}
```

**Expected output when tests pass:**

```
Tests run: 2, Failures: 0, Errors: 0, Skipped: 0

LibraryIntegrationTest
  ✔ borrowing a book reduces stock and updates record
  ✔ returning a book restores stock and updates record
```

---

### Task 2: Temperature Logger

A `TemperatureLogger` records a series of temperature readings, and a `TemperatureAnalyser` computes statistics over those readings. Complete the `TemperatureAnalyser` class, then write an integration test verifying that the two classes work together.

**Scaffolding:**

```kotlin
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.Assertions.assertEquals

// Stores a sequence of temperature readings in Celsius
class TemperatureLogger {
    private val readings = mutableListOf<Double>()

    // Adds a new temperature reading
    fun log(temp: Double) {
        readings.add(temp)
    }

    // Returns a read-only copy of all readings
    fun getReadings(): List<Double> = readings.toList()
}

// Computes statistics from a list of temperature readings
class TemperatureAnalyser(private val logger: TemperatureLogger) {

    // TODO: implement average(): Double
    //   - Return the mean of all readings
    //   - Return 0.0 if there are no readings

    // TODO: implement max(): Double
    //   - Return the highest reading
    //   - Throw IllegalStateException("No readings available") if the list is empty

    // TODO: implement min(): Double
    //   - Return the lowest reading
    //   - Throw IllegalStateException("No readings available") if the list is empty
}

class TemperatureIntegrationTest {

    @Test
    fun `analyser correctly computes stats from logged readings`() {
        val logger = TemperatureLogger()
        val analyser = TemperatureAnalyser(logger)

        logger.log(10.0)
        logger.log(20.0)
        logger.log(30.0)

        // TODO: assert that:
        //   - analyser.average() == 20.0
        //   - analyser.max() == 30.0
        //   - analyser.min() == 10.0
    }
}
```

**Expected output when tests pass:**

```
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

TemperatureIntegrationTest
  ✔ analyser correctly computes stats from logged readings
```

---

### Challenge Task: Shopping Basket System

Build a small shopping basket system with three interacting components — `ProductCatalogue`, `Basket`, and `DiscountEngine` — and write integration tests that verify the complete checkout flow.

**Requirements:**

- `ProductCatalogue` stores products (name → price in pence) and can look up a price by name
- `Basket` tracks items added by the user (a product name may be added more than once); it uses `ProductCatalogue` to compute the total cost
- `DiscountEngine` applies a percentage discount to a given total; if the total exceeds a threshold, a further fixed reduction is applied

**Scaffolding:**

```kotlin
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.Assertions.assertEquals

// Maps product names to their prices in pence
class ProductCatalogue(private val prices: Map<String, Int>) {

    // Returns the price of the named product in pence
    // Throws IllegalArgumentException if the product is not found
    fun priceOf(name: String): Int {
        return prices[name] ?: throw IllegalArgumentException("Product not found: $name")
    }
}

// Tracks the items a customer intends to buy
class Basket(private val catalogue: ProductCatalogue) {
    private val items = mutableListOf<String>()

    // Adds a product name to the basket
    fun add(productName: String) {
        items.add(productName)
    }

    // TODO: implement total(): Int
    //   - Sum the prices (from the catalogue) of every item in the basket
    //   - Return the total in pence

    // Returns the number of items in the basket
    fun itemCount(): Int = items.size
}

// Applies discounts to a basket total
class DiscountEngine(
    private val percentageOff: Int,      // e.g. 10 means 10% off
    private val thresholdPence: Int,     // e.g. 1000 means £10.00
    private val bonusReductionPence: Int // extra reduction if total exceeds threshold
) {

    // TODO: implement applyDiscount(totalPence: Int): Int
    //   Step 1 - apply the percentage discount to totalPence
    //   Step 2 - if the discounted total still exceeds thresholdPence,
    //            subtract bonusReductionPence as well
    //   Return the final discounted total (minimum 0)
}

class ShoppingBasketIntegrationTest {

    private val catalogue = ProductCatalogue(
        mapOf(
            "Apple" to 50,       // 50p
            "Bread" to 120,      // £1.20
            "Milk" to 90,        // 90p
            "Cheese" to 350      // £3.50
        )
    )

    @Test
    fun `basket total is calculated correctly across multiple items`() {
        val basket = Basket(catalogue)
        basket.add("Apple")
        basket.add("Bread")
        basket.add("Milk")

        // TODO: assert basket.total() == 260 (50 + 120 + 90)
    }

    @Test
    fun `discount engine applies percentage and bonus reduction`() {
        // 10% off, bonus 50p reduction if total still exceeds 200p after percentage off
        val engine = DiscountEngine(percentageOff = 10, thresholdPence = 200, bonusReductionPence = 50)

        // Total before discount: 50 + 120 + 90 + 350 = 610p
        // After 10% off: 610 - 61 = 549p
        // 549 > 200 threshold, so subtract 50p bonus: 549 - 50 = 499p

        val basket = Basket(catalogue)
        basket.add("Apple")
        basket.add("Bread")
        basket.add("Milk")
        basket.add("Cheese")

        // TODO: assert engine.applyDiscount(basket.total()) == 499
    }

    @Test
    fun `adding the same product twice doubles its contribution to total`() {
        val basket = Basket(catalogue)
        basket.add("Cheese")
        basket.add("Cheese")

        // TODO: assert basket.total() == 700 (350 * 2)
        //        and basket.itemCount() == 2
    }
}
```

**Expected output when tests pass:**

```
Tests run: 3, Failures: 0, Errors: 0, Skipped: 0

ShoppingBasketIntegrationTest
  ✔ basket total is calculated correctly across multiple items
  ✔ discount engine applies percentage and bonus reduction
  ✔ adding the same product twice doubles its contribution to total
```
