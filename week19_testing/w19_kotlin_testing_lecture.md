# Software Testing

---

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [1. Introduction to Software Testing](#1-introduction-to-software-testing)
- [2. Unit Testing Recap](#2-unit-testing-recap)
- [3. Integration Testing](#3-integration-testing)
- [4. Integration Testing Approaches](#4-integration-testing-approaches)
- [5. Writing Integration Tests in Kotlin](#5-writing-integration-tests-in-kotlin)
- [6. System Testing](#6-system-testing)
- [7. Testing in Android with Kotlin](#7-testing-in-android-with-kotlin)
- [8. Putting It All Together](#8-putting-it-all-together)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

1. Distinguish between unit testing, integration testing, and system testing
2. Explain the purpose of integration testing and why it matters
3. Describe bottom-up and big-bang integration testing strategies
4. Write integration tests in Kotlin using JUnit
5. Explain what system testing verifies and how it relates to requirements
6. Describe how testing fits into Android development with Kotlin

---

## 1. Introduction to Software Testing

Software rarely works perfectly the first time it is written. Even experienced developers introduce bugs, and as systems grow in size and complexity, the likelihood of unexpected behaviour increases. Testing is the discipline of systematically verifying that software does what it is supposed to do.

You are likely already familiar with the idea that code should be tested. What might be less clear is that testing exists at several distinct levels, each targeting a different scope of the software:

```
+---------------------------+
|      System Testing       |  <-- Whole system vs requirements
+---------------------------+
|   Integration Testing     |  <-- Units working together
+---------------------------+
|      Unit Testing         |  <-- Individual functions/classes
+---------------------------+
```

Each level builds on the one below it. You test individual pieces first, then test how those pieces interact, and finally test the system as a whole. This lecture focuses on **integration testing** and **system testing** — the two levels above unit testing.

[Back to Table of Contents](#table-of-contents)

---

## 2. Unit Testing Recap

Before moving on to integration testing, it is worth briefly recapping unit testing, since integration testing builds directly upon it.

A **unit test** targets a single, isolated piece of functionality — typically a single function or class method. When writing a unit test, you aim to test that function in isolation, often substituting any dependencies with fakes or mocks so that only the unit under test is being evaluated.

Here is a simple example of a unit test in Kotlin using JUnit 5:

```kotlin
// Import JUnit 5 testing annotations and assertion functions
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.Assertions.assertEquals

// A simple calculator class to be tested
class Calculator {
    // Adds two integers together
    fun add(a: Int, b: Int): Int = a + b

    // Subtracts b from a
    fun subtract(a: Int, b: Int): Int = a - b

    // Multiplies two integers
    fun multiply(a: Int, b: Int): Int = a * b

    // Divides a by b; throws an exception if b is zero
    fun divide(a: Int, b: Int): Int {
        if (b == 0) throw IllegalArgumentException("Cannot divide by zero")
        return a / b
    }
}

// Unit tests for the Calculator class
class CalculatorTest {

    // Create a single Calculator instance to use across tests
    private val calculator = Calculator()

    @Test
    fun `add returns correct sum`() {
        // Arrange: inputs are 3 and 4
        // Act: call the add method
        val result = calculator.add(3, 4)
        // Assert: result should be 7
        assertEquals(7, result)
    }

    @Test
    fun `subtract returns correct difference`() {
        val result = calculator.subtract(10, 4)
        assertEquals(6, result)
    }

    @Test
    fun `divide throws exception for zero divisor`() {
        // We expect an IllegalArgumentException to be thrown
        org.junit.jupiter.api.assertThrows<IllegalArgumentException> {
            calculator.divide(10, 0)
        }
    }
}
```

**Key point:** each test is independent — it tests one thing. The `Calculator` class has no external dependencies, so it is straightforward to test in isolation. Real-world classes, however, often depend on other classes, databases, or network services. That is where integration testing comes in.

[Back to Table of Contents](#table-of-contents)

---

## 3. Integration Testing

### What is Integration Testing?

Once individual units have been unit tested, the next step is to verify that they work correctly **when combined**. This is integration testing.

A widely used definition comes from Martin Fowler: the goal is to *"determine if independently developed units of software work together when they are connected to each other"* (Fowler, 2018).

Consider the analogy of building a car:

```
+------------+     +------------+     +------------------+
|   Engine   | --> | Gearbox    | --> | Drivetrain       |
| (unit test)|     | (unit test)|     | (unit test)      |
+------------+     +------------+     +------------------+
         \               |               /
          +------ Integration test -----+
                  (Do they work together?)
```

Each component might pass its own unit tests. However, the engine might produce output in a format the gearbox does not expect, or the gearbox might pass power to the drivetrain at incorrect intervals. Integration testing catches these kinds of problems.

### Why Integration Testing Matters

Unit tests, by their nature, test components in isolation. They often use mocked or stubbed dependencies. This means unit tests can pass even when:

- The interface contract between two components is misunderstood
- Data is serialised in one format by one unit and expected in a different format by another
- Timing or ordering assumptions differ between components

Integration tests expose these inter-component defects that unit tests simply cannot catch.

[Back to Table of Contents](#table-of-contents)

---

## 4. Integration Testing Approaches

There are several strategies for how you go about combining units and testing them together. Two of the most common are **bottom-up** and **big bang**.

### Bottom-Up Integration Testing

In a bottom-up approach, you start with the lowest-level components in your system — those that have no dependencies on other components — and test them in isolation first (unit tests). You then progressively integrate and test higher-level components that depend on the already-verified lower-level ones.

```
Level 3:  [AppController]           <-- tested last
                 |
Level 2:  [GameController]          <-- tested second
                 |
Level 1:  [NumberGenerator]         <-- tested first
```

**Advantages:**
- Defects are found early at the lower levels before higher levels are tested
- No need for stub components at lower levels since real components are used from the start

**Disadvantages:**
- High-level logic is tested last, meaning top-level integration problems are found late

### Big Bang Integration Testing

In the big bang approach, most or all components are integrated at once and then tested together.

```
+------------------+
| NumberGenerator  |---+
+------------------+   |
                        v
+------------------+  +------------------+
| ScoreTracker     |->| GuessingGame     | <-- all tested together
+------------------+  +------------------+
                        ^
+------------------+    |
| GameController   |----+
+------------------+
```

**Advantages:**
- Simpler to organise when the system is small
- All components are exercised together in one go

**Disadvantages:**
- When a test fails, it can be difficult to isolate which component caused the failure
- Defects may not be discovered until late in the process

[Back to Table of Contents](#table-of-contents)

---

## 5. Writing Integration Tests in Kotlin

Let us look at a practical example. We will build a small number-guessing game in Kotlin with multiple interacting components, then write an integration test that verifies they work together correctly.

### The Components

First, a `NumberGenerator` interface and a concrete implementation:

```kotlin
// A contract (interface) for generating a secret number
interface NumberGenerator {
    fun generate(): Int
}

// A fixed implementation used in testing — always returns the same number
// This makes the test predictable and repeatable
class FixedNumberGenerator(private val number: Int) : NumberGenerator {
    override fun generate(): Int = number
}

// The production implementation that uses actual randomness
class RandomNumberGenerator(private val max: Int = 100) : NumberGenerator {
    override fun generate(): Int = (1..max).random()
}
```

Next, a `ScoreTracker` that tracks the number of guesses:

```kotlin
// Tracks how many guesses the player has made
class ScoreTracker {
    private var guessCount = 0

    // Increments the guess counter
    fun recordGuess() {
        guessCount++
    }

    // Returns the total number of guesses recorded
    fun getScore(): Int = guessCount

    // Resets the counter back to zero
    fun reset() {
        guessCount = 0
    }
}
```

Now, the `GuessingGame` class that coordinates the two:

```kotlin
// Represents the result of a single guess
enum class GuessResult {
    TOO_LOW,
    TOO_HIGH,
    CORRECT
}

// The main game class — depends on both NumberGenerator and ScoreTracker
class GuessingGame(
    private val numberGenerator: NumberGenerator,
    private val scoreTracker: ScoreTracker
) {
    // Generate the secret number when the game is created
    private val secretNumber: Int = numberGenerator.generate()

    // Process a guess and return the result
    fun guess(number: Int): GuessResult {
        // Always record that a guess was made
        scoreTracker.recordGuess()

        return when {
            number < secretNumber -> GuessResult.TOO_LOW
            number > secretNumber -> GuessResult.TOO_HIGH
            else -> GuessResult.CORRECT
        }
    }

    // Returns the number of guesses made so far
    fun getGuessCount(): Int = scoreTracker.getScore()
}
```

### The Integration Test

Now we write an integration test that wires the real components together and verifies they interact correctly:

```kotlin
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.Assertions.assertEquals
import org.junit.jupiter.api.Assertions.assertTrue

// Integration test for GuessingGame + ScoreTracker + FixedNumberGenerator
class GuessingGameIntegrationTest {

    @Test
    fun `game correctly identifies winning guess and tracks score`() {
        // Arrange: wire up the components
        // We use FixedNumberGenerator so we know the secret number is 42
        val numberGenerator = FixedNumberGenerator(42)
        val scoreTracker = ScoreTracker()
        val game = GuessingGame(numberGenerator, scoreTracker)

        // Act: make some guesses
        val result1 = game.guess(20)  // too low
        val result2 = game.guess(60)  // too high
        val result3 = game.guess(42)  // correct

        // Assert: check the results are as expected
        assertEquals(GuessResult.TOO_LOW, result1)
        assertEquals(GuessResult.TOO_HIGH, result2)
        assertEquals(GuessResult.CORRECT, result3)

        // Also verify the ScoreTracker correctly counted three guesses
        assertEquals(3, game.getGuessCount())
    }

    @Test
    fun `player can win within a limited number of guesses`() {
        // Arrange: secret number is 7
        val game = GuessingGame(FixedNumberGenerator(7), ScoreTracker())

        // Act: simulate an automated player trying every number up to 1000
        val limit = 1000
        var result = GuessResult.TOO_LOW

        for (i in 1..limit) {
            result = game.guess(i)
            if (result == GuessResult.CORRECT) break
        }

        // Assert: the game should eventually be won
        assertEquals(GuessResult.CORRECT, result)

        // The player should have found 7 in exactly 7 guesses (guessing 1, 2, ..., 7)
        assertEquals(7, game.getGuessCount())
    }
}
```

**Sample output (when run in a test runner):**

```
Tests run: 2, Failures: 0, Errors: 0, Skipped: 0

GuessingGameIntegrationTest
  ✔ game correctly identifies winning guess and tracks score
  ✔ player can win within a limited number of guesses
```

Notice the key difference from a unit test: we are not mocking `ScoreTracker` — we are using the **real** `ScoreTracker`. The integration test verifies that `GuessingGame` and `ScoreTracker` work together correctly, not just that each one works in isolation.

### A Note on Test Doubles

In the example above, `FixedNumberGenerator` is what is known as a **stub** — a simplified stand-in that replaces a dependency for testing purposes. Using a stub for `NumberGenerator` makes our test **deterministic**: the secret number is always 42, so we know exactly what results to expect.

```
+-----------------------------+
|  GuessingGame (real)        |
|  ScoreTracker (real)        |  <-- Integration: real components
|  FixedNumberGenerator (stub)|  <-- Stubbed only to control input
+-----------------------------+
```

This is good integration test design. You use real components where possible but stub out any non-deterministic or external dependencies (random numbers, network calls, system clocks).

[Back to Table of Contents](#table-of-contents)

---

## 6. System Testing

### What is System Testing?

System testing follows integration testing. Where integration testing focuses on whether components work together correctly, system testing asks a broader question: **does the complete system meet its requirements?**

System testing is typically conducted against a formal specification — for example, a Functional Requirements Specification (FRS). The specification lists what the system is supposed to do, and the system test verifies that it actually does it.

```
+----------------------------------------------+
|           System Under Test                  |
|  (complete, deployed application)            |
+----------------------------------------------+
                     |
                     v
+----------------------------------------------+
| Does it satisfy the Functional Requirements  |
| Specification?                               |
+----------------------------------------------+
       |                           |
       v                           v
  Pass: system                Fail: raise defect
  meets requirement           report for fixing
```

### System vs Integration Testing

It is easy to confuse system testing with integration testing. The distinction is:

| Aspect | Integration Testing | System Testing |
|--------|-------------------|----------------|
| Scope | Specific component interactions | Entire system end-to-end |
| Reference | Developer expectations | Requirements specification |
| Testers | Often developers | Often independent testers |
| Environment | Test / dev environment | Production-like environment |
| Focus | Internal wiring | External behaviour |

### Requirements and System Tests

System test cases are derived from requirements. For each requirement in the specification, there should be one or more system tests that verify it.

For example, consider a simple requirement:

> *REQ-007: The system shall prevent a user from logging in with an incorrect password.*

A corresponding system test might be:

```
Test Case: TC-007-A
Requirement: REQ-007
Pre-condition: User account exists with password "securePass123"
Steps:
  1. Navigate to the login screen
  2. Enter the correct username
  3. Enter an incorrect password ("wrongPassword")
  4. Press the Login button
Expected Result: The system displays an error message and the user
                 is NOT logged in
Pass/Fail: ___
```

System tests are often described in prose and executed manually or with end-to-end testing tools (such as Espresso for Android UI testing). They validate the system from the perspective of the **user** or **external observer**, not the internal code structure.

[Back to Table of Contents](#table-of-contents)

---

## 7. Testing in Android with Kotlin

Android development has its own testing considerations because applications run on a device (or emulator) and interact with the Android framework. Kotlin is the primary language for Android development, and the Android testing ecosystem is well integrated with it.

### Test Source Sets in Android Projects

Android Studio organises tests into two source sets:

```
app/
+-- src/
    +-- main/        <-- production code
    |   +-- java/
    |   +-- res/
    +-- test/        <-- unit tests (run on the JVM, fast)
    |   +-- java/
    +-- androidTest/ <-- instrumented tests (run on device/emulator)
        +-- java/
```

- **Unit tests** (`test/`) run on the local JVM and are fast. They are ideal for testing pure Kotlin logic such as data models, view models, and utility classes.
- **Instrumented tests** (`androidTest/`) run on an Android device or emulator. They can interact with the Android framework and UI — making them suitable for integration and system-level tests.

### Example: Instrumented Integration Test with Espresso

Espresso is Android's UI testing framework. The following example demonstrates a simple instrumented test that verifies a login screen works end-to-end:

```kotlin
// Instrumented integration/system test using Espresso
// This runs on a physical device or emulator

import androidx.test.espresso.Espresso.onView
import androidx.test.espresso.action.ViewActions.*
import androidx.test.espresso.assertion.ViewAssertions.matches
import androidx.test.espresso.matcher.ViewMatchers.*
import androidx.test.ext.junit.rules.ActivityScenarioRule
import androidx.test.ext.junit.runners.AndroidJUnit4
import org.junit.Rule
import org.junit.Test
import org.junit.runner.RunWith

// Tells JUnit to use the Android test runner
@RunWith(AndroidJUnit4::class)
class LoginActivityTest {

    // Launches the LoginActivity before each test
    @get:Rule
    val activityRule = ActivityScenarioRule(LoginActivity::class.java)

    @Test
    fun enteringInvalidPassword_showsErrorMessage() {
        // Type a username into the username field (identified by its view ID)
        onView(withId(R.id.editTextUsername))
            .perform(typeText("testuser"), closeSoftKeyboard())

        // Type a wrong password into the password field
        onView(withId(R.id.editTextPassword))
            .perform(typeText("wrongPassword"), closeSoftKeyboard())

        // Tap the login button
        onView(withId(R.id.buttonLogin))
            .perform(click())

        // Assert that an error message is now displayed on screen
        onView(withId(R.id.textViewError))
            .check(matches(isDisplayed()))
    }

    @Test
    fun enteringValidCredentials_navigatesToHomeScreen() {
        // Use valid credentials
        onView(withId(R.id.editTextUsername))
            .perform(typeText("admin"), closeSoftKeyboard())

        onView(withId(R.id.editTextPassword))
            .perform(typeText("correctPassword"), closeSoftKeyboard())

        onView(withId(R.id.buttonLogin))
            .perform(click())

        // Assert that the home screen's welcome message is visible
        onView(withId(R.id.textViewWelcome))
            .check(matches(isDisplayed()))
    }
}
```

This is both an integration test (it exercises the login logic, the authentication service, and the UI together) and resembles a system test (it verifies a requirement: "users with valid credentials can log in").

### Example: ViewModel Unit Test with no Android Framework

A `ViewModel` in an Android MVVM architecture is a great candidate for pure unit or integration testing because it contains business logic without direct Android framework dependencies:

```kotlin
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.Assertions.assertEquals
import org.junit.jupiter.api.BeforeEach

// A simple ViewModel that manages a counter
class CounterViewModel {
    private var _count = 0

    // Returns the current count
    val count: Int get() = _count

    // Increments the count by 1
    fun increment() { _count++ }

    // Decrements the count, but not below zero
    fun decrement() { if (_count > 0) _count-- }

    // Resets the count to zero
    fun reset() { _count = 0 }
}

// Unit/integration test for CounterViewModel
class CounterViewModelTest {

    // Create a fresh ViewModel before each test
    private lateinit var viewModel: CounterViewModel

    @BeforeEach
    fun setUp() {
        viewModel = CounterViewModel()
    }

    @Test
    fun `initial count is zero`() {
        assertEquals(0, viewModel.count)
    }

    @Test
    fun `increment increases count by one`() {
        viewModel.increment()
        viewModel.increment()
        assertEquals(2, viewModel.count)
    }

    @Test
    fun `decrement does not go below zero`() {
        // Count starts at 0
        viewModel.decrement()
        // Should remain at 0, not go to -1
        assertEquals(0, viewModel.count)
    }

    @Test
    fun `reset returns count to zero after increments`() {
        viewModel.increment()
        viewModel.increment()
        viewModel.increment()
        viewModel.reset()
        assertEquals(0, viewModel.count)
    }
}
```

**Sample output:**

```
Tests run: 4, Failures: 0, Errors: 0, Skipped: 0

CounterViewModelTest
  ✔ initial count is zero
  ✔ increment increases count by one
  ✔ decrement does not go below zero
  ✔ reset returns count to zero after increments
```

[Back to Table of Contents](#table-of-contents)

---

## 8. Putting It All Together

Having looked at each level of testing in turn, it is worth stepping back and seeing how they relate to each other in practice.

### The Testing Pyramid

A well-tested application typically has many unit tests, fewer integration tests, and even fewer system tests. This is known as the **testing pyramid**:

```
          /\
         /  \
        / ST \        <-- Few system tests (slow, expensive)
       /------\
      /   IT   \      <-- Some integration tests
     /----------\
    /    Unit    \    <-- Many unit tests (fast, cheap)
   /--------------\
```

- **Unit tests** are fast and cheap to write. You should have lots of them.
- **Integration tests** are slower (they involve more components) and there are typically fewer of them.
- **System tests** are the slowest and most expensive to write and maintain. They cover the most important end-to-end user journeys.

### When to Write Each Type

| Situation | Test type |
|-----------|-----------|
| A new utility function is added | Unit test |
| Two services are connected for the first time | Integration test |
| A new user-facing feature is complete | System test |
| A bug is fixed | Unit test (to prevent regression) |
| The full application is deployed to staging | System test |

### Summary of Key Concepts

- **Unit testing** verifies individual components in isolation
- **Integration testing** verifies that independently developed units work correctly when combined — it catches interface mismatches and interaction bugs that unit tests miss
- **Bottom-up** integration starts with the lowest-level components; **big bang** integrates most components at once
- **System testing** validates the complete system against its formal requirements specification
- In Android, **unit tests** run on the JVM in the `test/` source set; **instrumented tests** run on a device or emulator in the `androidTest/` source set and support integration and system-level testing

[Back to Table of Contents](#table-of-contents)
