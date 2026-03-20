---
marp: true
theme: default
paginate: true
footer: OOP Principles, SRP, LSP & Object Composition
style: |
  :root {
    --dark:   #1a2744;
    --accent: #028090;
    --light:  #f5f8fc;
    --mid:    #e0eaf4;
    --text:   #1a2744;
  }

  section {
    background-color: var(--light);
    color: var(--text);
    font-family: 'Trebuchet MS', 'Segoe UI', Arial, sans-serif;
    font-size: 16px;
    padding: 36px 52px 52px 52px;
  }

  /* ── Title slide ── */
  section.title {
    background-color: var(--dark);
    color: #ffffff;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    padding: 60px 80px;
  }
  section.title h1 {
    color: #ffffff;
    font-size: 42px;
    font-weight: 700;
    border-bottom: 4px solid var(--accent);
    padding-bottom: 20px;
    margin-bottom: 20px;
  }
  section.title p {
    color: #9ecfe8;
    font-size: 20px;
    margin: 0;
  }

  /* ── Dark section-divider slides ── */
  section.dark {
    background-color: var(--dark);
    color: #ffffff;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 60px 80px;
  }
  section.dark h1 {
    color: var(--accent);
    font-size: 46px;
    font-weight: 700;
    margin-bottom: 16px;
  }
  section.dark p {
    color: #c8ddf0;
    font-size: 18px;
    font-style: italic;
    margin: 0;
  }
  section.dark footer {
    color: #5a7a9a;
  }
  section.dark .marp-vt-mark,
  section.dark [data-marpit-pagination] {
    color: #5a7a9a;
  }

  /* ── Headings ── */
  h1 {
    color: var(--dark);
    font-size: 30px;
    margin-bottom: 12px;
  }
  h2 {
    color: var(--accent);
    font-size: 24px;
    margin-bottom: 10px;
  }
  h3 {
    color: var(--dark);
    font-size: 18px;
    margin-bottom: 6px;
  }

  /* ── Code ── */
  pre {
    background-color: #0f1e36;
    color: #b8d8f0;
    font-size: 11.5px;
    line-height: 1.55;
    border-radius: 6px;
    padding: 14px 18px;
    margin: 10px 0;
  }
  pre code {
    background: transparent;
    color: inherit;
    font-size: inherit;
    padding: 0;
  }
  code {
    background-color: #d6e8f4;
    color: var(--dark);
    font-size: 13px;
    border-radius: 3px;
    padding: 1px 5px;
  }

  /* ── Tables ── */
  table {
    border-collapse: collapse;
    width: 100%;
    font-size: 14px;
    margin: 10px 0;
  }
  th {
    background-color: var(--accent);
    color: #ffffff;
    padding: 8px 12px;
    text-align: left;
    font-weight: 600;
  }
  td {
    border: 1px solid #c0d0e0;
    padding: 7px 12px;
  }
  tr:nth-child(even) td {
    background-color: var(--mid);
  }

  /* ── Blockquote ── */
  blockquote {
    border-left: 4px solid var(--accent);
    background-color: var(--mid);
    color: var(--dark);
    padding: 10px 16px;
    margin: 14px 0;
    font-style: italic;
    border-radius: 0 4px 4px 0;
  }

  /* ── Bullets ── */
  ul, ol {
    margin: 8px 0;
    padding-left: 26px;
    line-height: 1.75;
  }
  li {
    margin-bottom: 3px;
  }

  /* ── Footer & pagination ── */
  footer {
    font-size: 11px;
    color: #8090a8;
  }
---

<!-- _class: title -->
<!-- _paginate: false -->
<!-- _footer: '' -->

# OOP Principles, SRP, LSP & Object Composition

**2026-03**

---

## Table of Contents

| Topic | Learning Outcome |
|---|---|
| Inheritance | LO1, LO2 |
| Abstraction | LO1, LO2 |
| Encapsulation | LO1, LO2 |
| Polymorphism | LO1, LO2 |
| Single Responsibility Principle | LO3 |
| Whole-Part Relationships & Composition | LO4 |
| Liskov Substitution Principle | LO5 |

---

## Objectives

By the end of this session you will have explored:

- The **four core principles** of Object-Oriented Programming — Inheritance, Abstraction, Encapsulation, and Polymorphism — and how to implement them in Kotlin
- How Kotlin keywords such as `open`, `abstract`, `override`, and visibility modifiers support OOP design
- The **Single Responsibility Principle (SRP)** and why it leads to better-structured classes
- The difference between **aggregation** and **composition** as models of object relationships
- The **Liskov Substitution Principle (LSP)**, what it guarantees, and how to spot violations

---

## Learning Outcomes

| # | You will be able to... |
|---|---|
| LO1 | Explain and apply Inheritance, Abstraction, Encapsulation, and Polymorphism |
| LO2 | Implement these principles in Kotlin using `open`, `abstract`, and visibility modifiers |
| LO3 | Apply the Single Responsibility Principle when designing classes |
| LO4 | Distinguish between aggregation and composition as forms of whole-part relationships |
| LO5 | Explain and apply the Liskov Substitution Principle and identify common violations |

---

## Introduction

OOP organises software around **objects** — self-contained units that combine data (**properties**) and behaviour (**methods**).

```
+---------------------------------------+
|               Object                  |
|---------------------------------------|
|   Properties (state)                  |
|   Methods    (behaviour)              |
+---------------------------------------+
```

Kotlin is a modern, statically-typed language with full OOP support. We explore the four pillars of OOP, then extend our understanding with two influential design principles:

- **SRP** — the Single Responsibility Principle
- **LSP** — the Liskov Substitution Principle

> Well-designed OOP code is **modular**, **maintainable**, and **extendable**.

---

<!-- _class: dark -->

# Inheritance

> *Modelling IS-A relationships between classes*

---

## Inheritance — The Concept

**Inheritance** allows a subclass to inherit properties and behaviour from a superclass.

```
HomeAppliance          <-- superclass (general)
    |
    +-- Kettle         <-- IS-A HomeAppliance
    +-- WashingMachine <-- IS-A HomeAppliance
    +-- Fridge         <-- IS-A HomeAppliance
```

Key ideas:
- The **superclass** defines general attributes and behaviour
- The **subclass** inherits those and can add or override specifics
- Kotlin classes are **closed for inheritance by default**
- Use `open` to permit subclassing; use `override` to replace behaviour

---

## Inheritance — Kotlin Example

```kotlin
open class HomeAppliance(val brand: String, val powerRatingWatts: Int) {
    open fun switchOn() {
        println("$brand appliance on. Power: ${powerRatingWatts}W")
    }
    fun switchOff() = println("$brand switched off.")
}

class Kettle(brand: String, powerRatingWatts: Int, val capacityLitres: Double)
    : HomeAppliance(brand, powerRatingWatts) {

    override fun switchOn() {
        println("$brand kettle boiling ${capacityLitres}L. Power: ${powerRatingWatts}W")
    }
}

fun main() {
    val k = Kettle("Breville", 3000, 1.7)
    k.switchOn()   // calls overridden version in Kettle
    k.switchOff()  // inherited from HomeAppliance
}
```

```
Breville kettle boiling 1.7L. Power: 3000W
Breville switched off.
```

---

<!-- _class: dark -->

# Abstraction

> *Focusing on **what** something does, not **how** it does it*

---

## Abstraction — Abstract Classes

An **abstract class** cannot be instantiated directly. It may declare **abstract functions** — declared but bodyless — which subclasses must implement.

```kotlin
abstract class Shape(val colour: String) {
    abstract fun area(): Double        // no body — subclasses must implement

    fun describe() {
        println("$colour shape, area: ${"%.2f".format(area())}")
    }
}

class Circle(colour: String, val radius: Double) : Shape(colour) {
    override fun area() = Math.PI * radius * radius
}

class Rectangle(colour: String, val w: Double, val h: Double) : Shape(colour) {
    override fun area() = w * h
}
```

- `Shape` defines the **what** (area must be calculable)
- `Circle` and `Rectangle` define the **how**

---

## Abstraction — Interfaces

An **interface** defines a contract — *what* a class can do — with no constructor parameters or backing fields.

```kotlin
interface Printable {
    fun printDetails()         // abstract by default
}

interface Saveable {
    fun saveToFile(fileName: String)
}

// A class can implement MULTIPLE interfaces
data class StudentRecord(val name: String, val grade: Double)
    : Printable, Saveable {

    override fun printDetails() = println("$name | Grade: $grade")
    override fun saveToFile(f: String) = println("Saving $name to $f")
}
```

> A class can implement **many interfaces**, but can only extend **one class**.

---

## Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---|---|---|
| Can hold state / fields | ✅ Yes | ❌ No backing fields |
| Multiple inheritance | ❌ Single only | ✅ Multiple |
| Constructor parameters | ✅ Yes | ❌ No |
| Default implementations | ✅ Yes | ✅ Yes (Kotlin 1.x+) |
| **Use when...** | Shared state or base behaviour | Defining a capability / contract |

---

<!-- _class: dark -->

# Encapsulation

> *Controlling what the outside world can see and change*

---

## Encapsulation — Concept & Visibility Modifiers

Encapsulation **bundles** data with the methods that operate on it, and **hides** internal details behind a public interface.

| Modifier | Visibility |
|---|---|
| `private` | Inside the class only |
| `protected` | Class and subclasses |
| `internal` | Same module (compiled unit) |
| `public` | Anywhere — the **default** |

```kotlin
class BankAccount(val owner: String, initialBalance: Double) {
    private var balance = initialBalance   // hidden: cannot be set directly

    fun deposit(amount: Double) {
        if (amount > 0) balance += amount  // controlled access
    }
    fun getBalance() = balance             // read-only view
}
// account.balance = 9999  --> ERROR: balance is private
```

---

## Encapsulation — Why It Matters

```
External Code           BankAccount
+------------+         +-------------------------------+
|            |  -----> |  + deposit(amount)            |
|  Client    |  -----> |  + withdraw(amount)    PUBLIC |
|            |  <----- |  + getBalance()               |
+------------+         |-------------------------------|
                       |  - balance         PRIVATE    |
                       |  - logTransaction()           |
                       +-------------------------------+
```

Benefits of a well-encapsulated class:
- **Data integrity** — external code cannot put the object in an invalid state
- **Flexibility** — internal implementation can change without affecting clients
- **Maintainability** — the class is self-contained and easier to reason about

---

<!-- _class: dark -->

# Polymorphism

> *One interface — many forms at runtime*

---

## Polymorphism — The Concept

**Polymorphism** allows objects of different types to be treated through a **common supertype**, with the correct subclass behaviour invoked at **runtime** (dynamic dispatch).

```
List<Ingredient>
    |
    +-- Vegetable --> prepare(): "Chop Onion into fine pieces"
    +-- Protein   --> prepare(): "Grill Chicken until cooked"
    +-- Spice     --> prepare(): "Add a pinch of Cumin"
         ^
         All called through the same Ingredient reference
```

The `prepareRecipe()` function accepts `List<Ingredient>` — it does **not** need to know whether each item is a `Vegetable`, `Protein`, or `Spice`.

> Adding a new subtype (e.g. `Sauce`) requires **no changes** to existing processing logic.

---

## Polymorphism — Code Example

```kotlin
open class Ingredient(val name: String) {
    open fun prepare() = "Prepare $name"
}

class Vegetable(name: String, val style: String) : Ingredient(name) {
    override fun prepare() = "Chop $name into $style pieces"
}

class Protein(name: String, val method: String) : Ingredient(name) {
    override fun prepare() = "$method $name until cooked"
}

fun prepareRecipe(items: List<Ingredient>) {
    items.forEach { println(it.prepare()) }   // correct method called at runtime
}

fun main() {
    prepareRecipe(listOf(
        Vegetable("Onion", "fine"),
        Protein("Chicken", "Grill")
    ))
}
```

```
Chop Onion into fine pieces
Grill Chicken until cooked
```

---

<!-- _class: dark -->

# Single Responsibility Principle

> *"A class should have only one reason to change."*
> — Robert C. Martin (SOLID)

---

## SRP — The Principle

Each class should be responsible for **exactly one concern** — one area of functionality, one actor.

**DVD-TV Combo Analogy:**
If the same device handles both TV display and disc playback, it has two reasons to change — any update to either technology requires modifying the whole device.

**Signs of SRP violation:**

```
OrderProcessor                     <-- 3 responsibilities
    |
    +-- calculateTotal()           Reason to change: pricing logic
    +-- saveOrderToDatabase()      Reason to change: storage layer
    +-- sendConfirmationEmail()    Reason to change: email provider
```

> If you can describe a class with "and", it probably violates SRP.

---

## SRP — Refactored

```kotlin
class OrderCalculator {
    fun calculateTotal(items: Map<String, Double>) = items.values.sum()
}

class OrderRepository {
    fun save(orderId: String, total: Double) {
        println("Saving order $orderId: £${"%.2f".format(total)}")
    }
}

class OrderNotifier {
    fun sendConfirmation(email: String, orderId: String) {
        println("Confirmation sent to $email for order $orderId")
    }
}
```

Each class now has a **single reason to change**:
- Database changes? → Only `OrderRepository`
- Email provider changes? → Only `OrderNotifier`
- Pricing logic changes? → Only `OrderCalculator`

---

<!-- _class: dark -->

# Whole-Part Relationships & Object Composition

> *Building objects from other objects*

---

## Aggregation vs Composition

Two types of whole-part relationship:

```
+-------------------------------+    +--------------------------------+
|        AGGREGATION            |    |         COMPOSITION            |
|-------------------------------|    |--------------------------------|
|                               |    |                                |
|   Car  <>-------  Passenger   |    |   Car  *--------  Gearbox     |
|                               |    |                                |
|   Parts exist independently   |    |   Parts owned by the whole    |
|   Lifetime NOT tied together  |    |   Lifetime tied together      |
+-------------------------------+    +--------------------------------+
```

| Relationship | Destroy the whole... | Example |
|---|---|---|
| **Aggregation** | Parts survive | Car & Passengers |
| **Composition** | Parts are destroyed too | Car & Gearbox |

---

## Composition in Kotlin

```kotlin
data class Address(val city: String, val postcode: String)

data class Contract(val role: String, val salaryGBP: Double)

class Employee(
    val name: String,
    val address: Address,                  // Employee HAS-A Address
    val contracts: MutableList<Contract>   // Employee HAS-A list of Contracts
) {
    fun addContract(c: Contract) {
        contracts.add(c)
        println("New role for $name: ${c.role} at £${c.salaryGBP}")
    }
}
```

```
Employee
  - name
  - address   -----> Address  { city, postcode }
  - contracts -----> [ Contract { role, salary }, ... ]
```

---

## Composition vs Inheritance

| Approach | Use When | Coupling |
|---|---|---|
| **Inheritance** | Genuine IS-A relationship | Tight |
| **Composition** | HAS-A relationship | Loose and flexible |

> **"Favour composition over inheritance"**

- Composition lets you **swap out parts** without restructuring the class hierarchy
- Inheritance creates a tight parent-child coupling that can become fragile as codebases grow

```
Inheritance:  Kettle    extends HomeAppliance  (IS-A)
Composition:  Employee  has an Address         (HAS-A)
              Employee  has Contracts          (HAS-A)
```

---

<!-- _class: dark -->

# Liskov Substitution Principle

> *"Subtypes must be substitutable for their base types."*
> — Barbara Liskov, 1988 (SOLID)

---

## LSP — The Principle

If `S` is a subtype of `T`, objects of type `T` can be **replaced** with objects of type `S` **without altering program correctness**.

**Android example — `View` hierarchy:**

```
View
 +-- TextView
 |    +-- Button
 |    +-- EditText
 +-- ImageView
 +-- RecyclerView
```

`addView()` accepts a `View`. You can safely pass a `Button`, `TextView`, or `ImageView`. Any `View` subtype substitutes correctly — this **is** LSP.

LSP extends polymorphism with a **behavioural contract**: substitution must not break expected behaviour. Any code that works with a `Shape` must work correctly with a `Circle` or `Rectangle`.

---

## LSP — Violation: The Type-Check Smell

A common violation: using `is` or `when` to check subtype and adjust behaviour.

```kotlin
open class Bird(val name: String) {
    open fun move() = println("$name is moving")
}

class Eagle(name: String)  : Bird(name) {
    override fun move() = println("$name soars through the sky")
}

class Penguin(name: String) : Bird(name) {
    override fun move() = println("$name waddles on the ground")
}

// VIOLATION: not all Birds can be treated identically
fun makeItFly(bird: Bird) {
    if (bird is Penguin) println("${bird.name} cannot fly!")  // broken
    else println("${bird.name} takes flight!")
}
```

**Fix:** Introduce a `Flyable` interface. `Eagle` implements `Flyable`; `Penguin` does not. The hierarchy now reflects reality, and `makeItFly` works on `Flyable` — not all `Bird` subtypes.

---

## Conclusion

The seven principles covered today form the foundation of good OOP design:

| Principle | Key Idea |
|---|---|
| **Inheritance** | Subclasses extend superclass behaviour (IS-A) |
| **Abstraction** | Define *what*, let subclasses define *how* |
| **Encapsulation** | Hide internals; expose a controlled interface |
| **Polymorphism** | One interface, many runtime behaviours |
| **SRP** | One class — one reason to change |
| **Composition** | Build complex objects from simpler parts (HAS-A) |
| **LSP** | Subtypes must behave correctly in place of their base type |

> Mastering these principles allows you to write code that is **modular**, **maintainable**, and **built to last**.
