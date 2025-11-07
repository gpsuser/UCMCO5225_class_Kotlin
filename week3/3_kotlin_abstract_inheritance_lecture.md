# Inheritance, Abstract Classes and Interfaces in Kotlin
## CO5225 - Lecture Session

---

## Table of Contents

| Section | Topic | Time Allocation |
|---------|-------|-----------------|
| 1 | Introduction and Overview | 3 minutes |
| 2 | Making Classes Inheritable in Kotlin | 8 minutes |
| 3 | Primary Constructors in Inheritance | 7 minutes |
| 4 | Overriding Methods | 7 minutes |
| 5 | Interfaces in Kotlin | 8 minutes |
| 6 | Abstract Classes | 7 minutes |
| 7 | Single Responsibility Principle | 5 minutes |
| 8 | Object Composition and Whole-Part Relationships | 5 minutes |
| 9 | Liskov Substitution Principle (LSP) | 5 minutes |
| **Total** | | **55 minutes** |

---

## Learning Objectives (SMART)

| Objective | Description |
|-----------|-------------|
| **Specific** | Students will understand and implement inheritance in Kotlin using the `open` keyword |
| **Measurable** | Students will create at least one working inheritance hierarchy with proper constructor calling |
| **Achievable** | Students will distinguish between abstract classes and interfaces and when to use each |
| **Relevant** | Students will apply the Liskov Substitution Principle to ensure proper inheritance design |
| **Time-bound** | By the end of this 55-minute session, students will demonstrate understanding through code examples |

---

## Learning Outcomes

| ID | Learning Outcome |
|----|------------------|
| **LO1** | Explain why Kotlin classes are final by default and use the `open` keyword to enable inheritance |
| **LO2** | Implement inheritance hierarchies with proper primary constructor calling |
| **LO3** | Override methods correctly using the `open` and `override` keywords |
| **LO4** | Create and implement interfaces with and without default implementations |
| **LO5** | Design abstract classes and understand their differences from interfaces |
| **LO6** | Apply the Single Responsibility Principle to class design |
| **LO7** | Distinguish between aggregation and composition in object relationships |
| **LO8** | Apply the Liskov Substitution Principle to ensure proper subtype behavior |

---

## Section 1: Introduction and Overview (3 minutes)

Welcome to today's lecture on Inheritance and the Liskov Substitution Principle in Kotlin. Today, we'll explore one of the fundamental concepts of Object-Oriented Programming: inheritance, and how Kotlin handles it differently from languages like Java.

**Why Inheritance Matters:**
- Enables code reuse through hierarchical relationships
- Allows us to model real-world "is-a" relationships
- Forms the foundation for polymorphism
- Critical for building maintainable, scalable applications

**Kotlin's Unique Approach:**
Unlike Java, where classes are open for inheritance by default, Kotlin takes a more restrictive approach. This design decision helps prevent fragile base class problems and encourages more intentional inheritance hierarchies.

---

## Section 2: Making Classes Inheritable in Kotlin (8 minutes)

### The Default: Final Classes

By default, **all classes in Kotlin are final** – they cannot be inherited. This is equivalent to explicitly marking every Java class with the `final` keyword.

**Why this design decision?**
- Prevents unintended inheritance
- Encourages composition over inheritance
- Reduces fragile base class problem
- Makes inheritance intentional, not accidental

### The `open` Keyword

To make a class inheritable, you must explicitly mark it with the `open` keyword:

```kotlin
// This class CANNOT be inherited (default behavior)
class FinalClass {
    val name = "I cannot be inherited"
}

// This class CAN be inherited
open class BaseClass {
    val name = "I can be inherited"
}

// This creates a valid inheritance hierarchy
class SubClass : BaseClass() {
    // Inherits 'name' property from BaseClass
}

fun main() {
    val obj = SubClass()
    println(obj.name)  // Output: I can be inherited
    
    // This would cause a compilation error:
    // class CannotInherit : FinalClass()  // ERROR!
}
```

**Output:**
```
I can be inherited
```

### Inheritance Chains Require `open`

**Important rule:** If a class is inherited, and you want to inherit from it further, it must also be declared as `open`.

```kotlin
// Base class - must be open
open class Animal {
    open fun makeSound() {
        println("Some generic animal sound")
    }
}

// Middle class - must be open if we want to inherit from it
open class Mammal : Animal() {
    override fun makeSound() {
        println("Mammal sound")
    }
}

// Final derived class - no need for 'open' if no further inheritance
class Dog : Mammal() {
    override fun makeSound() {
        println("Woof!")
    }
}

// This would not compile without 'open' on Mammal:
// class Cat : Mammal()  // Would fail if Mammal wasn't open

fun main() {
    val dog = Dog()
    dog.makeSound()  // Output: Woof!
}
```

**Output:**
```
Woof!
```

### Visual Representation

```
    ┌──────────┐
    │  Animal  │  (open)
    │  (open)  │
    └────┬─────┘
         │
         │ inherits
         ▼
    ┌──────────┐
    │  Mammal  │  (open - allows further inheritance)
    │  (open)  │
    └────┬─────┘
         │
         │ inherits
         ▼
    ┌──────────┐
    │   Dog    │  (final - no further inheritance needed)
    └──────────┘
```

---

## Section 3: Primary Constructors in Inheritance (7 minutes)

### The Constructor Calling Rule

**Critical concept:** If a superclass has a primary constructor, it **must** be called when the subclass is created. This is done directly in the class header.

### Basic Example

```kotlin
// Base class with a primary constructor
open class BaseClass(val x: Int) {
    init {
        println("BaseClass initialized with x = $x")
    }
}

// Subclass must call the base constructor
class SubClass(x: Int) : BaseClass(x) {
    init {
        println("SubClass initialized")
    }
}

fun main() {
    val obj = SubClass(42)
    println("Value of x: ${obj.x}")
}
```

**Output:**
```
BaseClass initialized with x = 42
SubClass initialized
Value of x: 42
```

### More Complex Example: Adding Subclass Properties

```kotlin
// Vehicle base class
open class Vehicle(val make: String, val year: Int) {
    open fun displayInfo() {
        println("Vehicle: $make ($year)")
    }
}

// Car subclass with additional properties
class Car(
    make: String,           // Parameter to pass to superclass
    year: Int,              // Parameter to pass to superclass
    val doors: Int          // New property specific to Car
) : Vehicle(make, year) {   // Calling superclass constructor
    
    override fun displayInfo() {
        println("Car: $make ($year) with $doors doors")
    }
}

fun main() {
    val myCar = Car("Toyota", 2023, 4)
    myCar.displayInfo()
    
    // Can access inherited properties
    println("Make: ${myCar.make}")
    println("Year: ${myCar.year}")
    println("Doors: ${myCar.doors}")
}
```

**Output:**
```
Car: Toyota (2023) with 4 doors
Make: Toyota
Year: 2023
Doors: 4
```

### Using `val` or `var` in Subclass Constructor

```kotlin
open class Person(val name: String)

// Using 'val' in subclass - creates a NEW property
class Student(
    override val name: String,  // Overrides the property
    val studentId: String
) : Person(name)

fun main() {
    val student = Student("Alice", "S12345")
    println("Name: ${student.name}")
    println("ID: ${student.studentId}")
}
```

**Output:**
```
Name: Alice
ID: S12345
```

---

## Section 4: Overriding Methods (7 minutes)

### Methods Must Be Marked as `open`

Just like classes, methods in Kotlin are **final by default**. To allow overriding, mark them with `open`.

```kotlin
open class HomeAppliance {
    // This method CANNOT be overridden (default)
    fun turnOn() {
        println("Appliance is turning on...")
    }
    
    // This method CAN be overridden
    open fun operate() {
        println("Appliance is operating...")
    }
}

class Kettle : HomeAppliance() {
    // Override the open method
    override fun operate() {
        println("Kettle is boiling water...")
    }
    
    // Cannot override turnOn() - it's not open
    // override fun turnOn() { }  // ERROR!
}

fun main() {
    val kettle = Kettle()
    kettle.turnOn()     // Inherited method
    kettle.operate()    // Overridden method
}
```

**Output:**
```
Appliance is turning on...
Kettle is boiling water...
```

### Using `super` to Call Parent Methods

The `super` keyword allows you to call the parent class implementation:

```kotlin
open class Animal(val name: String) {
    open fun speak() {
        println("$name makes a sound")
    }
}

class Dog(name: String) : Animal(name) {
    override fun speak() {
        // Call parent implementation first
        super.speak()
        // Then add specific behavior
        println("$name barks: Woof!")
    }
}

class Cat(name: String) : Animal(name) {
    override fun speak() {
        // We can choose not to call super
        println("$name meows: Meow!")
    }
}

fun main() {
    val dog = Dog("Rex")
    val cat = Cat("Whiskers")
    
    println("Dog speaking:")
    dog.speak()
    
    println("\nCat speaking:")
    cat.speak()
}
```

**Output:**
```
Dog speaking:
Rex makes a sound
Rex barks: Woof!

Cat speaking:
Whiskers meows: Meow!
```

### Complete Example: Shape Hierarchy

```kotlin
import kotlin.math.PI

open class Shape(val name: String) {
    open fun area(): Double {
        return 0.0
    }
    
    open fun describe() {
        println("This is a $name with area: ${area()}")
    }
}

class Circle(val radius: Double) : Shape("Circle") {
    override fun area(): Double {
        return PI * radius * radius
    }
}

class Rectangle(val width: Double, val height: Double) : Shape("Rectangle") {
    override fun area(): Double {
        return width * height
    }
    
    override fun describe() {
        super.describe()  // Call parent implementation
        println("Dimensions: ${width} x ${height}")
    }
}

fun main() {
    val circle = Circle(5.0)
    val rectangle = Rectangle(4.0, 6.0)
    
    circle.describe()
    println()
    rectangle.describe()
}
```

**Output:**
```
This is a Circle with area: 78.53981633974483

This is a Rectangle with area: 24.0
Dimensions: 4.0 x 6.0
```

---

## Section 5: Interfaces in Kotlin (8 minutes)

### What is an Interface?

An interface is essentially a **contract** – a set of function signatures that any implementing class must provide. Think of it as a blueprint for behavior.

**Key characteristics:**
- No constructors
- Cannot have backing fields for properties
- Can have multiple implementations per class
- Can provide default implementations
- Can inherit from other interfaces

### Basic Interface Example

```kotlin
// Define an interface
interface Drivable {
    // Abstract method - must be implemented
    fun drive()
    
    // Abstract property - must be implemented
    val maxSpeed: Int
}

class Car : Drivable {
    override val maxSpeed: Int = 200
    
    override fun drive() {
        println("Car is driving at max speed: $maxSpeed km/h")
    }
}

class Bicycle : Drivable {
    override val maxSpeed: Int = 30
    
    override fun drive() {
        println("Bicycle is pedaling at max speed: $maxSpeed km/h")
    }
}

fun main() {
    val vehicles: List<Drivable> = listOf(Car(), Bicycle())
    
    for (vehicle in vehicles) {
        vehicle.drive()
    }
}
```

**Output:**
```
Car is driving at max speed: 200 km/h
Bicycle is pedaling at max speed: 30 km/h
```

### Interfaces with Default Implementations

```kotlin
interface Printable {
    val content: String
    
    // Method WITHOUT default implementation
    fun print()
    
    // Method WITH default implementation
    fun printWithBorder() {
        println("=" * 40)
        print()
        println("=" * 40)
    }
}

class Document(override val content: String) : Printable {
    override fun print() {
        println("Document: $content")
    }
    
    // printWithBorder() is inherited with default implementation
}

class Photo(override val content: String, val resolution: String) : Printable {
    override fun print() {
        println("Photo: $content [$resolution]")
    }
    
    // Override the default implementation
    override fun printWithBorder() {
        println("╔" + "═" * 38 + "╗")
        print()
        println("╚" + "═" * 38 + "╝")
    }
}

// Extension function to repeat string (for demonstration)
private operator fun String.times(count: Int) = this.repeat(count)

fun main() {
    val doc = Document("Important memo")
    val photo = Photo("Sunset.jpg", "1920x1080")
    
    println("Document with default border:")
    doc.printWithBorder()
    
    println("\nPhoto with custom border:")
    photo.printWithBorder()
}
```

**Output:**
```
Document with default border:
========================================
Document: Important memo
========================================

Photo with custom border:
╔══════════════════════════════════════╗
Photo: Sunset.jpg [1920x1080]
╚══════════════════════════════════════╝
```

### Multiple Interfaces

```kotlin
interface Flyable {
    fun fly()
}

interface Swimmable {
    fun swim()
}

// Class implementing multiple interfaces
class Duck : Flyable, Swimmable {
    override fun fly() {
        println("Duck is flying")
    }
    
    override fun swim() {
        println("Duck is swimming")
    }
}

class Airplane : Flyable {
    override fun fly() {
        println("Airplane is flying")
    }
}

fun main() {
    val duck = Duck()
    duck.fly()
    duck.swim()
    
    val plane = Airplane()
    plane.fly()
}
```

**Output:**
```
Duck is flying
Duck is swimming
Airplane is flying
```

### Interface Inheritance

```kotlin
interface Vehicle {
    fun move()
}

interface MotorizedVehicle : Vehicle {
    val fuelType: String
    fun refuel()
}

interface ElectricVehicle : Vehicle {
    val batteryCapacity: Int
    fun charge()
}

class GasCar(override val fuelType: String) : MotorizedVehicle {
    override fun move() {
        println("Gas car moving using $fuelType")
    }
    
    override fun refuel() {
        println("Refueling with $fuelType")
    }
}

class ElectricCar(override val batteryCapacity: Int) : ElectricVehicle {
    override fun move() {
        println("Electric car moving silently")
    }
    
    override fun charge() {
        println("Charging battery ($batteryCapacity kWh)")
    }
}

fun main() {
    val gasCar = GasCar("Petrol")
    gasCar.move()
    gasCar.refuel()
    
    println()
    
    val electricCar = ElectricCar(75)
    electricCar.move()
    electricCar.charge()
}
```

**Output:**
```
Gas car moving using Petrol
Refueling with Petrol

Electric car moving silently
Charging battery (75 kWh)
```

---

## Section 6: Abstract Classes (7 minutes)

### What is an Abstract Class?

An abstract class is a class that **cannot be instantiated** directly. It may contain:
- Abstract methods (no implementation)
- Concrete methods (with implementation)
- Properties with backing fields
- Constructors

**Key differences from interfaces:**
- Can have state (backing fields)
- Can have constructors
- A class can inherit from only ONE abstract class
- Open by definition (no need for `open` keyword)

### Basic Abstract Class

```kotlin
// Abstract class - cannot be instantiated
abstract class Employee(val name: String, val id: String) {
    // Concrete property
    var yearsOfService: Int = 0
    
    // Abstract method - must be implemented
    abstract fun calculateSalary(): Double
    
    // Concrete method - can be used as-is or overridden
    fun displayInfo() {
        println("Employee: $name (ID: $id)")
        println("Years of service: $yearsOfService")
        println("Salary: $${calculateSalary()}")
    }
}

class FullTimeEmployee(
    name: String,
    id: String,
    private val annualSalary: Double
) : Employee(name, id) {
    
    override fun calculateSalary(): Double {
        return annualSalary / 12  // Monthly salary
    }
}

class ContractEmployee(
    name: String,
    id: String,
    private val hourlyRate: Double,
    private val hoursWorked: Int
) : Employee(name, id) {
    
    override fun calculateSalary(): Double {
        return hourlyRate * hoursWorked
    }
}

fun main() {
    val fullTime = FullTimeEmployee("Alice Johnson", "FT001", 60000.0)
    fullTime.yearsOfService = 3
    
    val contractor = ContractEmployee("Bob Smith", "CT001", 50.0, 160)
    contractor.yearsOfService = 1
    
    fullTime.displayInfo()
    println()
    contractor.displayInfo()
    
    // This would cause an error:
    // val emp = Employee("Test", "T001")  // ERROR: Cannot instantiate abstract class
}
```

**Output:**
```
Employee: Alice Johnson (ID: FT001)
Years of service: 3
Salary: $5000.0

Employee: Bob Smith (ID: CT001)
Years of service: 1
Salary: $8000.0
```

### Abstract Class vs Interface

```kotlin
// Abstract class - can have state and constructors
abstract class DatabaseConnection(val connectionString: String) {
    // Concrete property with backing field
    protected var isConnected: Boolean = false
    
    // Abstract method
    abstract fun connect()
    abstract fun disconnect()
    
    // Concrete method
    fun getStatus(): String {
        return if (isConnected) "Connected" else "Disconnected"
    }
}

// Interface - no state or constructors
interface Queryable {
    fun executeQuery(query: String): List<String>
}

// Class can inherit ONE abstract class and MULTIPLE interfaces
class MySQLConnection(connectionString: String) : DatabaseConnection(connectionString), Queryable {
    
    override fun connect() {
        println("Connecting to MySQL: $connectionString")
        isConnected = true
    }
    
    override fun disconnect() {
        println("Disconnecting from MySQL")
        isConnected = false
    }
    
    override fun executeQuery(query: String): List<String> {
        if (!isConnected) {
            println("Error: Not connected to database")
            return emptyList()
        }
        println("Executing query: $query")
        return listOf("Result 1", "Result 2", "Result 3")
    }
}

fun main() {
    val db = MySQLConnection("localhost:3306/mydb")
    
    println("Status: ${db.getStatus()}")
    
    db.connect()
    println("Status: ${db.getStatus()}")
    
    val results = db.executeQuery("SELECT * FROM users")
    println("Results: $results")
    
    db.disconnect()
    println("Status: ${db.getStatus()}")
}
```

**Output:**
```
Status: Disconnected
Connecting to MySQL: localhost:3306/mydb
Status: Connected
Executing query: SELECT * FROM users
Results: [Result 1, Result 2, Result 3]
Disconnecting from MySQL
Status: Disconnected
```

### When to Use Each?

```
┌─────────────────────────────────────────────────────────────┐
│                    ABSTRACT CLASS                            │
├─────────────────────────────────────────────────────────────┤
│ ✓ Need state (fields with backing storage)                  │
│ ✓ Need constructors                                          │
│ ✓ Need to share common implementation                        │
│ ✓ Modeling "is-a" relationship                               │
│ ✓ Single inheritance hierarchy                               │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                      INTERFACE                               │
├─────────────────────────────────────────────────────────────┤
│ ✓ Defining a contract/capability                            │
│ ✓ Need multiple inheritance                                  │
│ ✓ Unrelated classes share behavior                          │
│ ✓ Modeling "can-do" relationship                             │
│ ✓ No state needed                                            │
└─────────────────────────────────────────────────────────────┘
```

---

## Section 7: Single Responsibility Principle (5 minutes)

### The Principle

**"A class should have only one reason to change"**

This means each class should have a **single responsibility** or job. If a class has multiple responsibilities, changes to one responsibility might affect the other.

### Bad Example: Multiple Responsibilities

```kotlin
// BAD: This class has TOO MANY responsibilities
class Employee {
    var name: String = ""
    var id: String = ""
    var salary: Double = 0.0
    
    // Responsibility 1: Calculate pay
    fun calculatePay(): Double {
        return salary * 1.1  // With 10% bonus
    }
    
    // Responsibility 2: Database operations
    fun saveToDatabase() {
        println("Saving employee to database...")
        // Database logic here
    }
    
    // Responsibility 3: Report generation
    fun generateReport(): String {
        return """
            Employee Report
            Name: $name
            ID: $id
            Salary: $salary
        """.trimIndent()
    }
}

// Multiple reasons to change:
// 1. Pay calculation logic changes
// 2. Database technology changes
// 3. Report format changes
```

### Good Example: Single Responsibilities

```kotlin
// GOOD: Each class has ONE responsibility

// Responsibility: Hold employee data
data class Employee(
    val name: String,
    val id: String,
    val salary: Double
)

// Responsibility: Calculate payroll
class PayrollCalculator {
    fun calculatePay(employee: Employee): Double {
        return employee.salary * 1.1  // With 10% bonus
    }
}

// Responsibility: Database operations
class EmployeeRepository {
    fun save(employee: Employee) {
        println("Saving ${employee.name} to database...")
        // Database logic here
    }
    
    fun findById(id: String): Employee? {
        println("Finding employee with ID: $id")
        // Database query logic
        return null
    }
}

// Responsibility: Generate reports
class ReportGenerator {
    fun generateEmployeeReport(employee: Employee): String {
        return """
            ═══════════════════════════
            Employee Report
            ═══════════════════════════
            Name: ${employee.name}
            ID: ${employee.id}
            Salary: $${employee.salary}
        """.trimIndent()
    }
}

fun main() {
    val employee = Employee("Alice Johnson", "E001", 50000.0)
    
    val payroll = PayrollCalculator()
    val repository = EmployeeRepository()
    val reportGen = ReportGenerator()
    
    println("Monthly pay: $${payroll.calculatePay(employee)}")
    repository.save(employee)
    println(reportGen.generateEmployeeReport(employee))
}
```

**Output:**
```
Monthly pay: $55000.0
Saving Alice Johnson to database...
═══════════════════════════
Employee Report
═══════════════════════════
Name: Alice Johnson
ID: E001
Salary: $50000.0
```

**Benefits:**
- Each class can change independently
- Easier to test
- Easier to understand
- More maintainable

---

## Section 8: Object Composition and Whole-Part Relationships (5 minutes)

### The Concept

Objects can **contain other objects**. This creates whole-part relationships where complex objects are built from simpler ones.

### Two Types of Relationships

#### 1. Aggregation (Has-a / Usage)
- Parts exist **independently** of the whole
- Parts can be **shared**
- Destroying the whole does NOT destroy the parts

#### 2. Composition (Must-have / Ownership)
- Parts have **no meaning** without the whole
- Parts are NOT shared
- **Lifetime dependency**: Destroying the whole destroys the parts

### Visual Representation

```
AGGREGATION                       COMPOSITION
(Has-a)                          (Must-have)

   ┌──────┐                         ┌──────┐
   │  Car │◇────── Passengers       │  Car │◆────── Engine
   └──────┘                         └──────┘
      │                                │
      │ Uses                           │ Owns
      ▼                                ▼
  ┌─────────┐                      ┌────────┐
  │Passenger│                      │ Engine │
  │ (exists │                      │(depends│
  │indepen- │                      │   on   │
  │ dently) │                      │  Car)  │
  └─────────┘                      └────────┘
```

### Code Example

```kotlin
// AGGREGATION Example: Passengers in a Car

class Passenger(val name: String, val seatNumber: Int) {
    fun boardVehicle() {
        println("$name is boarding (Seat $seatNumber)")
    }
}

class Car(val model: String) {
    // Aggregation: Car HAS passengers, but doesn't own them
    private val passengers = mutableListOf<Passenger>()
    
    fun addPassenger(passenger: Passenger) {
        passengers.add(passenger)
        passenger.boardVehicle()
    }
    
    fun displayPassengers() {
        println("$model passengers:")
        passengers.forEach { println("  - ${it.name}") }
    }
}

// COMPOSITION Example: Engine in a Car

class Engine(private val type: String, private val horsepower: Int) {
    fun start() {
        println("$type engine ($horsepower HP) starting...")
    }
    
    fun stop() {
        println("Engine stopping...")
    }
}

class Vehicle(val brand: String, engineType: String, engineHP: Int) {
    // Composition: Vehicle OWNS the engine
    // Engine is created with the vehicle and destroyed with it
    private val engine = Engine(engineType, engineHP)
    
    fun start() {
        println("$brand starting...")
        engine.start()
    }
    
    fun stop() {
        println("$brand stopping...")
        engine.stop()
    }
}

fun main() {
    println("=== AGGREGATION EXAMPLE ===")
    // Passengers exist independently
    val alice = Passenger("Alice", 1)
    val bob = Passenger("Bob", 2)
    
    val car1 = Car("Toyota Camry")
    car1.addPassenger(alice)
    car1.addPassenger(bob)
    car1.displayPassengers()
    
    // Passengers can switch cars
    val car2 = Car("Honda Accord")
    car2.addPassenger(alice)  // Alice can be in different car
    
    println("\n=== COMPOSITION EXAMPLE ===")
    val vehicle = Vehicle("Tesla Model 3", "Electric", 283)
    vehicle.start()
    vehicle.stop()
    // When vehicle is destroyed, its engine is also destroyed
}
```

**Output:**
```
=== AGGREGATION EXAMPLE ===
Alice is boarding (Seat 1)
Bob is boarding (Seat 2)
Toyota Camry passengers:
  - Alice
  - Bob
Alice is boarding (Seat 1)

=== COMPOSITION EXAMPLE ===
Tesla Model 3 starting...
Electric engine (283 HP) starting...
Tesla Model 3 stopping...
Engine stopping...
```

### Practical Example: Address

```kotlin
// Address exists independently - AGGREGATION
data class Address(
    val street: String,
    val city: String,
    val postalCode: String
)

// Contract exists only for employee - COMPOSITION
data class Contract(
    val startDate: String,
    val position: String,
    val salary: Double
)

class Employee(
    val name: String,
    val address: Address,  // Aggregation: address exists independently
) {
    // Composition: contracts are owned by employee
    private val contracts = mutableListOf<Contract>()
    
    fun addContract(contract: Contract) {
        contracts.add(contract)
    }
    
    fun displayInfo() {
        println("Employee: $name")
        println("Address: ${address.street}, ${address.city}")
        println("Contracts:")
        contracts.forEach {
            println("  - ${it.position}: $${it.salary} (from ${it.startDate})")
        }
    }
}

fun main() {
    val address = Address("123 Main St", "London", "SW1A 1AA")
    
    // Multiple employees can share the same address
    val emp1 = Employee("John Doe", address)
    val emp2 = Employee("Jane Smith", address)  // Same address object
    
    emp1.addContract(Contract("2020-01-01", "Junior Developer", 40000.0))
    emp1.addContract(Contract("2022-01-01", "Senior Developer", 60000.0))
    
    emp1.displayInfo()
}
```

**Output:**
```
Employee: John Doe
Address: 123 Main St, London
Contracts:
  - Junior Developer: $40000.0 (from 2020-01-01)
  - Senior Developer: $60000.0 (from 2022-01-01)
```

---

## Section 9: Liskov Substitution Principle (LSP) (5 minutes)

### The Principle

**"If S is a subtype of T, then objects of type T may be replaced by objects of type S without altering the correctness of the program"**

Or more simply: **"Subtypes must be substitutable for their base types"**

### What This Means

If you have a function that accepts a `Vehicle`, it should work correctly with ANY subtype of `Vehicle` (like `Car`, `Truck`, `Motorcycle`) without needing to know which specific type it is.

**The behavior must remain consistent!**

### LSP in Action: Correct Example

```kotlin
open class Bird(val name: String) {
    open fun move() {
        println("$name is moving")
    }
}

class Sparrow(name: String) : Bird(name) {
    override fun move() {
        println("$name is flying")
    }
}

class Penguin(name: String) : Bird(name) {
    override fun move() {
        println("$name is waddling")
    }
}

// This function expects a Bird and works with ANY Bird subtype
fun makeB​irdMove(bird: Bird) {
    bird.move()  // Works correctly for any Bird subtype
}

fun main() {
    val sparrow = Sparrow("Tweety")
    val penguin = Penguin("Pingu")
    
    // LSP: Both subtypes can substitute the base type
    makeBirdMove(sparrow)  // Works!
    makeBirdMove(penguin)  // Works!
    
    // We can use Bird type for any subtype
    val birds: List<Bird> = listOf(sparrow, penguin)
    birds.forEach { it.move() }
}
```

**Output:**
```
Tweety is flying
Pingu is waddling
Tweety is flying
Pingu is waddling
```

### LSP Violation: Bad Example

```kotlin
// BAD EXAMPLE: Violates LSP

open class Rectangle(open val width: Double, open val height: Double) {
    open fun area(): Double = width * height
    
    open fun setDimensions(w: Double, h: Double) {
        println("Setting rectangle: width=$w, height=$h")
    }
}

// Square violates LSP because it changes behavior
class Square(size: Double) : Rectangle(size, size) {
    override var width: Double = size
        set(value) {
            field = value
            height = value  // Forces height to change!
        }
    
    override var height: Double = size
        set(value) {
            field = value
            width = value  // Forces width to change!
        }
    
    override fun setDimensions(w: Double, h: Double) {
        // Violates expected behavior: can't set different dimensions
        val size = maxOf(w, h)
        width = size
        height = size
        println("Setting square: size=$size")
    }
}

// This function expects Rectangle behavior
fun processRectangle(rect: Rectangle) {
    println("\nProcessing rectangle:")
    rect.setDimensions(4.0, 5.0)
    println("Expected area for 4x5: 20.0")
    println("Actual area: ${rect.area()}")
    
    // With Rectangle: area = 20.0 ✓
    // With Square: area = 25.0 ✗ (VIOLATION!)
}

fun main() {
    val rectangle = Rectangle(0.0, 0.0)
    val square = Square(0.0)
    
    processRectangle(rectangle)  // Works as expected
    processRectangle(square)     // Violates LSP!
}
```

**Output:**
```
Processing rectangle:
Setting rectangle: width=4.0, height=5.0
Expected area for 4x5: 20.0
Actual area: 20.0

Processing rectangle:
Setting square: size=5.0
Expected area for 4x5: 20.0
Actual area: 25.0
```

**Problem:** The `Square` changed the expected behavior! Code that works with `Rectangle` breaks with `Square`.

### Common LSP Violations

```kotlin
// VIOLATION: Using type checks
fun processShape(shape: Shape) {
    if (shape is Circle) {
        // Special handling for Circle - LSP VIOLATION!
        println("This is a circle, handling differently...")
    } else if (shape is Rectangle) {
        // Special handling for Rectangle - LSP VIOLATION!
        println("This is a rectangle, handling differently...")
    }
    // If you need type checks, your hierarchy is likely wrong!
}
```

### Correct LSP Design

```kotlin
// CORRECT: Polymorphic behavior without type checks

abstract class Shape {
    abstract fun area(): Double
    abstract fun draw()
}

class Circle(private val radius: Double) : Shape() {
    override fun area(): Double = Math.PI * radius * radius
    
    override fun draw() {
        println("Drawing a circle with radius $radius")
    }
}

class Rectangle(private val width: Double, private val height: Double) : Shape() {
    override fun area(): Double = width * height
    
    override fun draw() {
        println("Drawing a rectangle ${width}x$height")
    }
}

// This works with ANY Shape without knowing the specific type
fun processShape(shape: Shape) {
    shape.draw()
    println("Area: ${shape.area()}")
}

fun main() {
    val shapes: List<Shape> = listOf(
        Circle(5.0),
        Rectangle(4.0, 6.0)
    )
    
    // LSP satisfied: All shapes work identically through base type
    shapes.forEach { processShape(it) }
}
```

**Output:**
```
Drawing a circle with radius 5.0
Area: 78.53981633974483
Drawing a rectangle 4.0x6.0
Area: 24.0
```

### Real-World Android Example

```kotlin
import android.view.View
import android.widget.Button
import android.widget.TextView

// In Android, the addView() method expects a View
class MyLayout {
    private val views = mutableListOf<View>()
    
    // Accepts View (base type)
    fun addView(view: View) {
        views.add(view)
        println("Added view: ${view::class.simpleName}")
    }
}

fun main() {
    val layout = MyLayout()
    
    // Button inherits from TextView which inherits from View
    // LSP allows us to pass Button where View is expected
    
    // layout.addView(Button())    // Would work in Android
    // layout.addView(TextView())  // Would work in Android
    
    // This demonstrates LSP: subtypes (Button, TextView) can
    // substitute their base type (View) without issues
    
    println("LSP allows Button to be used where View is expected")
    println("because Button IS-A TextView which IS-A View")
}
```

**Key Takeaway:** Design your class hierarchies so that derived classes can be used anywhere the base class is expected without breaking functionality!

---

## Time Allocation Summary

| Section | Topic | Time | Learning Outcomes |
|---------|-------|------|-------------------|
| 1 | Introduction | 3 min | - |
| 2 | Making Classes Inheritable | 8 min | LO1 |
| 3 | Primary Constructors in Inheritance | 7 min | LO2 |
| 4 | Overriding Methods | 7 min | LO3 |
| 5 | Interfaces | 8 min | LO4 |
| 6 | Abstract Classes | 7 min | LO5 |
| 7 | Single Responsibility Principle | 5 min | LO6 |
| 8 | Object Composition | 5 min | LO7 |
| 9 | Liskov Substitution Principle | 5 min | LO8 |
| **Total** | | **55 min** | |

---

## Further Resources

### Official Documentation
- **Kotlin Inheritance:** https://kotlinlang.org/docs/inheritance.html
- **Kotlin Interfaces:** https://kotlinlang.org/docs/interfaces.html
- **Kotlin Visibility Modifiers:** https://kotlinlang.org/docs/visibility-modifiers.html

### Books
- **Martin, R.C. (2012).** *Agile Software Development: Principles, Patterns, and Practices.* Upper Saddle River, NJ: Pearson. Pages 95-98 & 111-126
- **Liskov, B. (1988).** Keynote address-data abstraction and hierarchy. *ACM Sigplan Notices, 23(5),* 17-34.
- **Jemerov, D., & Isakova, S. (2017).** *Kotlin in Action.* Manning Publications.

### Online Tutorials
- **Kotlin by Example - Inheritance:** https://play.kotlinlang.org/byExample/01_introduction/07_Inheritance
- **Kotlin Koans - Object-Oriented Programming:** https://play.kotlinlang.org/koans
- **JetBrains Academy - OOP in Kotlin:** https://hyperskill.org/tracks/18

### Practice Platforms
- **Exercism Kotlin Track:** https://exercism.org/tracks/kotlin
- **HackerRank Kotlin:** https://www.hackerrank.com/domains/kotlin
- **LeetCode (select Kotlin as language):** https://leetcode.com/

### Video Resources
- **Kotlin Programming for Beginners - FreeCodeCamp:** YouTube
- **Kotlin Design Patterns - Coding in Flow:** YouTube channel

### Design Principles
- **SOLID Principles Explained:** https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design
- **Composition vs Inheritance:** https://www.thoughtworks.com/insights/blog/composition-vs-inheritance-how-choose

---

**End of Lecture**

Remember: **Inheritance is powerful but should be used thoughtfully.** Always ask yourself:
1. Is there a true "is-a" relationship?
2. Does the subtype truly substitute the base type (LSP)?
3. Could composition solve this better?

Happy coding! 🚀