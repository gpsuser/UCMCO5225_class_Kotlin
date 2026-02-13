# Kotlin Coroutines: Asynchronous Programming Made Simple

## Table of Contents

1. [Introduction to Asynchronous Programming](#introduction-to-asynchronous-programming)
2. [Understanding Synchronous Code](#understanding-synchronous-code)
3. [Understanding Asynchronous Code](#understanding-asynchronous-code)
4. [Introduction to Coroutines](#introduction-to-coroutines)
5. [Setting Up Coroutines in Your Project](#setting-up-coroutines-in-your-project)
6. [Suspendable Functions](#suspendable-functions)
7. [Launching Coroutines with launch](#launching-coroutines-with-launch)
8. [Waiting for Jobs with join](#waiting-for-jobs-with-join)
9. [Returning Values with async and await](#returning-values-with-async-and-await)
10. [Grouping Asynchronous Functions](#grouping-asynchronous-functions)
11. [Cancelling Jobs](#cancelling-jobs)
12. [Using select for First Completion](#using-select-for-first-completion)
13. [Nesting select Expressions](#nesting-select-expressions)
14. [Handling Exceptions in Coroutines](#handling-exceptions-in-coroutines)

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain the difference between synchronous and asynchronous code execution
- Understand the advantages of using coroutines over traditional threading approaches
- Create and configure suspendable functions using the `suspend` keyword
- Launch coroutines using `launch` for fire-and-forget operations
- Use `join()` to wait for coroutine completion
- Return values from coroutines using `async` and `await`
- Group multiple asynchronous operations using `coroutineScope`
- Cancel running coroutines and handle cancellation gracefully
- Use `select` expressions to handle the first completing coroutine
- Handle exceptions that occur within coroutines

---

## Introduction to Asynchronous Programming

Modern applications need to perform multiple tasks simultaneously without freezing or becoming unresponsive. Think about a mobile app that needs to download data from the internet while still allowing the user to scroll through a list or tap buttons. This is where asynchronous programming becomes essential.

In traditional synchronous programming, tasks execute one after another, each waiting for the previous one to complete. This works fine for quick operations, but becomes problematic when dealing with time-consuming tasks like:

- Network requests to fetch data from APIs
- Database queries
- File I/O operations
- Complex calculations

Kotlin provides **coroutines** as its solution for asynchronous programming, offering a more intuitive and less error-prone approach compared to traditional threading or callback-based systems.

[↑ Back to Table of Contents](#table-of-contents)

---

## Understanding Synchronous Code

Synchronous code executes tasks sequentially, one after another. Each task must complete before the next one can begin.

### Characteristics of Synchronous Execution

- **Sequential execution**: Tasks run in order
- **Blocking behavior**: A long-running task blocks all subsequent tasks
- **Simple to reason about**: The flow of execution is straightforward
- **Poor user experience**: Long operations can freeze the application

### Example: Synchronous Code

```kotlin
import kotlin.system.measureTimeMillis

fun main() {
    println("Starting synchronous operations...")
    
    val totalTime = measureTimeMillis {
        // First task - simulates a network call
        val result1 = fetchDataFromServer1()
        println("Result 1: $result1")
        
        // Second task - must wait for first to complete
        val result2 = fetchDataFromServer2()
        println("Result 2: $result2")
        
        // Third task - must wait for second to complete
        val result3 = processData()
        println("Result 3: $result3")
    }
    
    println("Total time taken: ${totalTime}ms")
    println("All operations complete!")
}

// Simulates a slow network request (1 second)
fun fetchDataFromServer1(): String {
    Thread.sleep(1000)  // Blocks the thread for 1 second
    return "Data from Server 1"
}

// Simulates another slow network request (1.5 seconds)
fun fetchDataFromServer2(): String {
    Thread.sleep(1500)  // Blocks the thread for 1.5 seconds
    return "Data from Server 2"
}

// Simulates data processing (500ms)
fun processData(): String {
    Thread.sleep(500)  // Blocks the thread for 0.5 seconds
    return "Processed data"
}
```

**Sample Output:**
```
Starting synchronous operations...
Result 1: Data from Server 1
Result 2: Data from Server 2
Result 3: Processed data
Total time taken: 3002ms
All operations complete!
```

Notice that the total time is the sum of all individual operations (1000 + 1500 + 500 = 3000ms). Each task waits for the previous one to complete before starting.

[↑ Back to Table of Contents](#table-of-contents)

---

## Understanding Asynchronous Code

Asynchronous code allows multiple functions to execute simultaneously, improving application responsiveness and efficiency.

### Benefits of Asynchronous Execution

- **Non-blocking**: Long-running tasks don't prevent other tasks from executing
- **Better resource utilization**: CPU can work on other tasks while waiting for I/O
- **Improved user experience**: UI remains responsive during long operations
- **Parallel execution**: Independent tasks can run simultaneously

### Challenges of Asynchronous Code

- **Increased complexity**: Managing concurrent execution is more difficult
- **Handling return values**: Getting results from multiple concurrent operations requires careful coordination
- **Error handling**: Exceptions can occur in multiple places simultaneously

### Approaches to Asynchronous Programming

Different programming languages and platforms use various approaches:

1. **Threads**: Create separate execution threads for concurrent work
2. **Callbacks**: Functions that execute when asynchronous operations complete
3. **Futures/Promises**: Objects representing future values
4. **Reactive Extensions**: Observable streams for handling asynchronous data
5. **Coroutines**: Kotlin's lightweight approach (what we'll focus on)

[↑ Back to Table of Contents](#table-of-contents)

---

## Introduction to Coroutines

Coroutines are Kotlin's solution for asynchronous programming. They provide a way to write asynchronous code that looks and behaves like synchronous code, making it easier to understand and maintain.

### What Are Coroutines?

A coroutine is a lightweight thread-like construct that can:
- **Suspend**: Pause execution without blocking a thread
- **Resume**: Continue execution from where it was paused
- **Run concurrently**: Multiple coroutines can execute simultaneously

### Key Advantages

- **Lightweight**: You can run thousands of coroutines without the overhead of threads
- **Structured concurrency**: Built-in lifecycle management
- **Easier to read**: Code looks sequential even when it's asynchronous
- **Less error-prone**: Fewer race conditions and memory leaks than traditional threading

### Core Concepts

- **Suspend function**: A function that can pause and resume execution
- **Coroutine builder**: Functions like `launch` and `async` that start coroutines
- **Coroutine scope**: Defines the lifecycle and context of coroutines
- **Job**: Represents a cancellable coroutine
- **Dispatcher**: Determines which thread(s) the coroutine runs on

[↑ Back to Table of Contents](#table-of-contents)

---

## Setting Up Coroutines in Your Project

Kotlin provides minimal built-in APIs for coroutines. Most coroutine functionality comes from the `kotlinx.coroutines` library provided by JetBrains.

### Adding the Dependency

#### For Gradle Projects (Kotlin DSL)

```kotlin
dependencies {
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.4")
}
```

#### For Android Projects

You typically need the Android-specific coroutine library:

```kotlin
dependencies {
    // New approach using version catalogs
    implementation(libs.kotlinx.coroutines.android)
    
    // Or traditional approach
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.6.4")
}
```

The Android library includes the core library plus Android-specific dispatchers for working with the UI thread.

### Required Imports

Most coroutine programs will need these imports:

```kotlin
import kotlinx.coroutines.*
```

[↑ Back to Table of Contents](#table-of-contents)

---

## Suspendable Functions

Suspendable functions are the building blocks of coroutines. They're marked with the `suspend` keyword and can pause execution without blocking the underlying thread.

### The suspend Keyword

When you prefix a function with `suspend`, you're telling Kotlin that this function:
- Can be paused during execution
- Can be resumed later
- Must be called from a coroutine or another suspend function
- Doesn't block the thread while suspended

### Understanding Suspension

When a suspend function encounters a suspension point (like `delay()`), it:
1. Saves its current state
2. Releases the thread for other work
3. Waits for the suspension to complete
4. Resumes execution when ready

### Example: Basic Suspend Function

```kotlin
import kotlinx.coroutines.*

fun main() {
    println("Program started at ${System.currentTimeMillis()}")
    
    // runBlocking creates a coroutine scope and blocks until complete
    // This is primarily used in main() functions and tests
    runBlocking {
        println("Coroutine scope entered")
        
        // Launch a coroutine to run our suspend function
        launch {
            doSomeInitialSetup()
        }
        
        println("Setup job started at ${System.currentTimeMillis()}")
    }
    
    println("Program complete at ${System.currentTimeMillis()}")
}

// Suspend function that can pause execution
suspend fun doSomeInitialSetup() {
    println("Initial setup started")
    
    // delay() is a suspend function that pauses without blocking
    delay(1000)  // Waits 1 second
    println("Initial setup still in progress")
    
    delay(1000)  // Waits another second
    println("Initial setup complete")
}
```

**Sample Output:**
```
Program started at 1641234567890
Coroutine scope entered
Setup job started at 1641234567895
Initial setup started
Initial setup still in progress
Initial setup complete
Program complete at 1641234569898
```

### Understanding Jobs

A **Job** represents a cancellable coroutine. When you launch a coroutine, you get back a Job object that you can use to:
- Check if the coroutine is active
- Wait for completion
- Cancel the coroutine

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Launching job...")
    
    // launch returns a Job instance
    val job: Job = launch {
        doSomeInitialSetup()
    }
    
    println("Job is active: ${job.isActive}")
    println("Job is completed: ${job.isCompleted}")
    
    // Wait a bit
    delay(2500)
    
    println("Job is active: ${job.isActive}")
    println("Job is completed: ${job.isCompleted}")
}

suspend fun doSomeInitialSetup() {
    delay(2000)
    println("Setup complete")
}
```

**Sample Output:**
```
Launching job...
Job is active: true
Job is completed: false
Setup complete
Job is active: false
Job is completed: true
```

[↑ Back to Table of Contents](#table-of-contents)

---

## Launching Coroutines with launch

The `launch` function is a coroutine builder that starts a new coroutine without blocking the current thread. It's often described as "fire and forget" because it doesn't return a result to the caller.

### Characteristics of launch

- Returns a `Job` object (not the function's return value)
- Executes asynchronously
- Useful for operations that don't need to return values
- Multiple launches can run concurrently

### Example: Multiple Concurrent Coroutines

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Starting multiple coroutines...")
    
    // Launch first coroutine
    launch {
        functionA()
    }
    
    // Launch second coroutine - runs concurrently with first
    launch {
        functionB()
    }
    
    // Launch third coroutine
    launch {
        functionC()
    }
    
    println("All coroutines launched!")
    // runBlocking waits for all child coroutines to complete
}

suspend fun functionA() {
    println("Function A starting")
    delay(100)  // Short delay
    println("Function A complete")
}

suspend fun functionB() {
    println("Function B starting")
    delay(50)  // Very short delay
    println("Function B complete")
}

suspend fun functionC() {
    println("Function C starting")
    delay(150)  // Longer delay
    println("Function C complete")
}
```

**Sample Output:**
```
Starting multiple coroutines...
All coroutines launched!
Function A starting
Function B starting
Function C starting
Function B complete
Function A complete
Function C complete
```

Notice that:
- All three functions start almost simultaneously
- They complete in order based on their delay times, not launch order
- The "All coroutines launched!" message appears immediately

### Launch with Timing Example

```kotlin
import kotlinx.coroutines.*
import kotlin.system.measureTimeMillis

fun main() = runBlocking {
    println("Demonstrating concurrent execution...\n")
    
    val sequentialTime = measureTimeMillis {
        fetchUserData()
        fetchUserPosts()
        fetchUserComments()
    }
    println("\nSequential execution took: ${sequentialTime}ms\n")
    
    println("---\n")
    
    val concurrentTime = measureTimeMillis {
        launch { fetchUserData() }
        launch { fetchUserPosts() }
        launch { fetchUserComments() }
    }
    println("\nConcurrent execution took: ${concurrentTime}ms")
}

suspend fun fetchUserData() {
    println("Fetching user data...")
    delay(1000)
    println("User data retrieved")
}

suspend fun fetchUserPosts() {
    println("Fetching user posts...")
    delay(1500)
    println("User posts retrieved")
}

suspend fun fetchUserComments() {
    println("Fetching user comments...")
    delay(800)
    println("User comments retrieved")
}
```

**Sample Output:**
```
Demonstrating concurrent execution...

Fetching user data...
User data retrieved
Fetching user posts...
User posts retrieved
Fetching user comments...
User comments retrieved

Sequential execution took: 3305ms

---

Fetching user data...
Fetching user posts...
Fetching user comments...
User comments retrieved
User data retrieved
User posts retrieved

Concurrent execution took: 1503ms
```

The concurrent version is significantly faster because all three operations run simultaneously.

---

## Waiting for Jobs with join

Sometimes you need to wait for a coroutine to complete before proceeding, even if you don't need its return value. The `join()` function allows you to wait for a Job to finish.

### When to Use join

- You need to ensure a setup task completes before continuing
- You want to coordinate the order of operations
- You need to guarantee completion without needing a return value

### Example: Using join for Coordination

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Application starting...")
    
    // Launch initial setup as a separate coroutine
    val initialSetupJob = launch {
        performInitialSetup()
    }
    
    // Do some other work that doesn't depend on setup
    println("Performing other work...")
    someOtherWork()
    println("Other work complete")
    
    // Now we need the setup to be complete before proceeding
    println("Waiting for initial setup to complete...")
    initialSetupJob.join()  // Suspends until the job is complete
    
    println("Ready to go! All setup complete.")
    startMainApplication()
}

suspend fun performInitialSetup() {
    println("  [Setup] Loading configuration...")
    delay(1000)
    println("  [Setup] Configuration loaded")
    
    println("  [Setup] Initializing database...")
    delay(1500)
    println("  [Setup] Database initialized")
    
    println("  [Setup] Setup complete!")
}

suspend fun someOtherWork() {
    delay(500)
    println("  [Other] Work item 1 done")
    delay(500)
    println("  [Other] Work item 2 done")
}

fun startMainApplication() {
    println("Main application started!")
}
```

**Sample Output:**
```
Application starting...
Performing other work...
  [Setup] Loading configuration...
  [Other] Work item 1 done
  [Setup] Configuration loaded
  [Other] Work item 2 done
Other work complete
Waiting for initial setup to complete...
  [Setup] Initializing database...
  [Setup] Database initialized
  [Setup] Setup complete!
Ready to go! All setup complete.
Main application started!
```

### Example: Joining Multiple Jobs

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Starting parallel initialization...\n")
    
    // Launch multiple setup tasks
    val databaseJob = launch {
        initializeDatabase()
    }
    
    val cacheJob = launch {
        initializeCache()
    }
    
    val networkJob = launch {
        initializeNetwork()
    }
    
    println("All initialization jobs launched\n")
    
    // Wait for all jobs to complete
    databaseJob.join()
    cacheJob.join()
    networkJob.join()
    
    println("\nAll systems initialized. Application ready!")
}

suspend fun initializeDatabase() {
    println("Database: Starting initialization...")
    delay(2000)
    println("Database: Ready")
}

suspend fun initializeCache() {
    println("Cache: Starting initialization...")
    delay(1000)
    println("Cache: Ready")
}

suspend fun initializeNetwork() {
    println("Network: Starting initialization...")
    delay(1500)
    println("Network: Ready")
}
```

**Sample Output:**
```
Starting parallel initialization...

All initialization jobs launched

Database: Starting initialization...
Cache: Starting initialization...
Network: Starting initialization...
Cache: Ready
Network: Ready
Database: Ready

All systems initialized. Application ready!
```

All three initialization tasks run concurrently, but the program waits for all of them to complete before proceeding.

[↑ Back to Table of Contents](#table-of-contents)

---

## Returning Values with async and await

While `launch` is great for fire-and-forget operations, we often need to get results back from our asynchronous operations. This is where `async` and `await` come in.

### Understanding async

- `async` is a coroutine builder like `launch`
- It returns a `Deferred<T>` object instead of `Job`
- `Deferred<T>` is a promise of a future value of type T
- You can call `await()` on a Deferred to get the actual value

### Understanding await

- `await()` is a suspend function called on a Deferred object
- It suspends until the value is ready
- It returns the actual computed value
- Multiple coroutines can await the same Deferred

### Example: Basic async and await

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Starting async operations...\n")
    
    // Start two async operations
    val resultA: Deferred<String> = async {
        fetchDataA()
    }
    
    val resultB: Deferred<String> = async {
        fetchDataB()
    }
    
    // Both operations are now running concurrently
    println("Both operations started\n")
    
    // Await the results (suspends until both are ready)
    val dataA = resultA.await()
    val dataB = resultB.await()
    
    println("\nFinal result: $dataA, $dataB")
}

suspend fun fetchDataA(): String {
    println("Fetching data A...")
    delay(2000)  // Simulates 2-second operation
    println("Data A ready")
    return "Data A"
}

suspend fun fetchDataB(): String {
    println("Fetching data B...")
    delay(1000)  // Simulates 1-second operation
    println("Data B ready")
    return "Data B"
}
```

**Sample Output:**
```
Starting async operations...

Both operations started

Fetching data A...
Fetching data B...
Data B ready
Data A ready

Final result: Data A, Data B
```

### Example: Combining Results

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Fetching weather data from multiple sources...\n")
    
    val temperature = async { fetchTemperature() }
    val humidity = async { fetchHumidity() }
    val windSpeed = async { fetchWindSpeed() }
    
    // Combine all results
    val weatherReport = """
        Weather Report:
        Temperature: ${temperature.await()}°C
        Humidity: ${humidity.await()}%
        Wind Speed: ${windSpeed.await()} km/h
    """.trimIndent()
    
    println(weatherReport)
}

suspend fun fetchTemperature(): Int {
    println("Fetching temperature...")
    delay(1000)
    return 22
}

suspend fun fetchHumidity(): Int {
    println("Fetching humidity...")
    delay(800)
    return 65
}

suspend fun fetchWindSpeed(): Int {
    println("Fetching wind speed...")
    delay(1200)
    return 15
}
```

**Sample Output:**
```
Fetching weather data from multiple sources...

Fetching temperature...
Fetching humidity...
Fetching wind speed...
Weather Report:
Temperature: 22°C
Humidity: 65%
Wind Speed: 15 km/h
```

### Comparison with launch

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("=== Using launch (no return value) ===\n")
    
    // With launch, we can't access return values outside the scope
    launch {
        val result = computeValue()
        println("Result inside launch: $result")
        // result is not accessible outside this block
    }
    
    delay(1500)  // Wait for launch to complete
    
    println("\n=== Using async (returns value) ===\n")
    
    // With async, we can get the return value
    val deferred = async {
        computeValue()
    }
    
    val result = deferred.await()
    println("Result outside async: $result")
}

suspend fun computeValue(): Int {
    println("Computing value...")
    delay(1000)
    println("Computation complete")
    return 42
}
```

**Sample Output:**
```
=== Using launch (no return value) ===

Computing value...
Computation complete
Result inside launch: 42

=== Using async (returns value) ===

Computing value...
Computation complete
Result outside async: 42
```

[↑ Back to Table of Contents](#table-of-contents)

---

## Grouping Asynchronous Functions

Often you'll want to encapsulate multiple async operations into a single suspend function. The `coroutineScope` function allows you to create a new scope for launching coroutines within a suspend function.

### Using coroutineScope

- Creates a new coroutine scope
- Suspends until all child coroutines complete
- Allows you to group async operations logically
- Provides structured concurrency

### Example: Grouping with coroutineScope

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Getting combined data...\n")
    
    val result = getCombinedData()
    
    println("\nFinal result: $result")
}

// Groups multiple async operations into a single function
suspend fun getCombinedData(): String = coroutineScope {
    println("Starting data collection...")
    
    // Launch multiple async operations
    val userInfo = async { fetchUserInfo() }
    val userPosts = async { fetchUserPosts() }
    val userFriends = async { fetchUserFriends() }
    
    // Combine results - note: no 'return' keyword needed with expression body
    "${userInfo.await()}, ${userPosts.await()}, ${userFriends.await()}"
}

suspend fun fetchUserInfo(): String {
    println("  Fetching user info...")
    delay(1000)
    return "UserInfo"
}

suspend fun fetchUserPosts(): String {
    println("  Fetching posts...")
    delay(800)
    return "UserPosts"
}

suspend fun fetchUserFriends(): String {
    println("  Fetching friends...")
    delay(1200)
    return "UserFriends"
}
```

**Sample Output:**
```
Getting combined data...

Starting data collection...
  Fetching user info...
  Fetching posts...
  Fetching friends...
Final result: UserInfo, UserPosts, UserFriends
```

### Example: More Complex Grouping

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Processing user dashboard data...\n")
    
    val dashboard = getUserDashboard("user123")
    
    println(dashboard)
}

// Represents a user's dashboard data
data class Dashboard(
    val userName: String,
    val messageCount: Int,
    val notificationCount: Int,
    val unreadCount: Int
)

// Groups multiple data fetches for a complete dashboard
suspend fun getUserDashboard(userId: String): Dashboard = coroutineScope {
    println("Loading dashboard for $userId...")
    
    // Launch all data fetches concurrently
    val name = async { fetchUserName(userId) }
    val messages = async { fetchMessageCount(userId) }
    val notifications = async { fetchNotificationCount(userId) }
    val unread = async { fetchUnreadCount(userId) }
    
    // Wait for all and construct dashboard
    Dashboard(
        userName = name.await(),
        messageCount = messages.await(),
        notificationCount = notifications.await(),
        unreadCount = unread.await()
    )
}

suspend fun fetchUserName(userId: String): String {
    delay(500)
    return "John Doe"
}

suspend fun fetchMessageCount(userId: String): Int {
    delay(700)
    return 15
}

suspend fun fetchNotificationCount(userId: String): Int {
    delay(400)
    return 3
}

suspend fun fetchUnreadCount(userId: String): Int {
    delay(600)
    return 8
}
```

**Sample Output:**
```
Processing user dashboard data...

Loading dashboard for user123...
Dashboard(userName=John Doe, messageCount=15, notificationCount=3, unreadCount=8)
```

### Benefits of Grouping

- **Encapsulation**: Hide implementation details
- **Reusability**: Use the grouped function in multiple places
- **Clarity**: Clear separation of concerns
- **Maintainability**: Easier to modify grouped operations

[↑ Back to Table of Contents](#table-of-contents)

---

## Cancelling Jobs

Sometimes you need to cancel a coroutine that's taking too long or is no longer needed. Coroutines support cooperative cancellation through the Job interface.

### Why Cancel Coroutines?

- Operation is taking too long (timeout)
- User navigated away (Android: user left the screen)
- Result is no longer needed
- Resource cleanup

### Cancellation Basics

- Call `cancel()` on a Job or Deferred
- Check `isCompleted` to see if work finished
- Use `isActive` to check if still running
- Handle results conditionally

### Example: Cancelling Slow Operations

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Starting operations with timeout...\n")
    
    val result = getDataWithTimeout()
    
    println("\nFinal result: $result")
}

suspend fun getDataWithTimeout(): String = coroutineScope {
    // Start two async operations
    val dataA = async { fetchSlowDataA() }
    val dataB = async { fetchSlowDataB() }
    
    // Wait maximum 500ms
    delay(500)
    println("\nTimeout reached! Checking completion status...")
    
    // Check what completed and cancel what didn't
    val resultA = if (dataA.isCompleted) {
        println("Data A completed in time")
        dataA.await()
    } else {
        dataA.cancel()
        println("Cancelling Data A - too slow")
        "A unavailable"
    }
    
    val resultB = if (dataB.isCompleted) {
        println("Data B completed in time")
        dataB.await()
    } else {
        dataB.cancel()
        println("Cancelling Data B - too slow")
        "B unavailable"
    }
    
    "$resultA, $resultB"
}

suspend fun fetchSlowDataA(): String {
    println("Fetching Data A (will take 1000ms)...")
    delay(1000)
    println("Data A ready")
    return "Data A"
}

suspend fun fetchSlowDataB(): String {
    println("Fetching Data B (will take 300ms)...")
    delay(300)
    println("Data B ready")
    return "Data B"
}
```

**Sample Output:**
```
Starting operations with timeout...

Fetching Data A (will take 1000ms)...
Fetching Data B (will take 300ms)...
Data B ready

Timeout reached! Checking completion status...
Cancelling Data A - too slow
Data B completed in time

Final result: A unavailable, Data B
```

### Example: Cancelling User Operations

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Starting file download...\n")
    
    val downloadJob = launch {
        downloadLargeFile()
    }
    
    // Simulate user clicking "Cancel" after 2 seconds
    delay(2000)
    println("\n[User clicked Cancel button]")
    downloadJob.cancel()
    
    delay(500)
    println("\nDownload status: ${if (downloadJob.isCancelled) "Cancelled" else "Active"}")
}

suspend fun downloadLargeFile() {
    println("Download started...")
    
    try {
        for (i in 1..10) {
            // Check if coroutine is still active
            if (!isActive) {
                println("Download cancelled at ${i * 10}%")
                break
            }
            delay(500)
            println("Downloaded: ${i * 10}%")
        }
        println("Download complete!")
    } catch (e: CancellationException) {
        println("Download was cancelled")
        throw e  // Re-throw to properly propagate cancellation
    }
}
```

**Sample Output:**
```
Starting file download...

Download started...
Downloaded: 10%
Downloaded: 20%
Downloaded: 30%
Downloaded: 40%

[User clicked Cancel button]
Download cancelled at 50%

Download status: Cancelled
```

### Important Cancellation Notes

1. **Cancellation is cooperative**: The coroutine must check `isActive` or call suspend functions
2. **Suspension points**: Functions like `delay()` check for cancellation automatically
3. **CancellationException**: Thrown when a suspended function is called on a cancelled coroutine
4. **Resource cleanup**: Use try-finally blocks to clean up resources

[↑ Back to Table of Contents](#table-of-contents)

---

## Using select for First Completion

Sometimes you only care about whichever operation completes first. The `select` expression lets you race multiple Deferred values and take the first result.

### When to Use select

- You need a result quickly and don't care which source provides it
- You're querying multiple redundant data sources
- You want to implement timeout behavior
- You're racing multiple algorithms to find the fastest

### Basic select Syntax

```kotlin
select<T> {
    deferred1.onAwait { result ->
        // Handle result from deferred1
        result
    }
    deferred2.onAwait { result ->
        // Handle result from deferred2
        result
    }
}
```

### Example: Getting First Available Result

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.selects.select

fun main() = runBlocking {
    println("Querying multiple servers for fastest response...\n")
    
    val result = getFirstAvailableData()
    
    println("\nReceived: $result")
}

suspend fun getFirstAvailableData(): String = coroutineScope {
    // Query two different servers
    val serverA = async { queryServerA() }
    val serverB = async { queryServerB() }
    
    // Take whichever responds first
    val firstResult = select<String> {
        serverA.onAwait { result ->
            println("Server A won the race!")
            serverB.cancel()
            println("Cancelled Server B query")
            result
        }
        serverB.onAwait { result ->
            println("Server B won the race!")
            serverA.cancel()
            println("Cancelled Server A query")
            result
        }
    }
    
    firstResult
}

suspend fun queryServerA(): String {
    println("Querying Server A...")
    delay(1500)  // Slow server
    println("Server A responded")
    return "Data from Server A"
}

suspend fun queryServerB(): String {
    println("Querying Server B...")
    delay(800)  // Fast server
    println("Server B responded")
    return "Data from Server B"
}
```

**Sample Output:**
```
Querying multiple servers for fastest response...

Querying Server A...
Querying Server B...
Server B responded
Server B won the race!
Cancelled Server A query

Received: Data from Server B
```

### Example: Implementing Timeout with select

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.selects.select

fun main() = runBlocking {
    println("Fetching data with timeout...\n")
    
    val result = getDataOrTimeout()
    
    println(result)
}

suspend fun getDataOrTimeout(): String = coroutineScope {
    // Start the data fetch
    val dataFetch = async { fetchImportantData() }
    
    // Start a timer
    val timer = async {
        delay(2000)  // 2-second timeout
        "TIMEOUT"
    }
    
    // Race them
    val result = select<String> {
        dataFetch.onAwait { data ->
            timer.cancel()
            println("Data received before timeout")
            data
        }
        timer.onAwait { timeout ->
            dataFetch.cancel()
            println("Timeout occurred!")
            timeout
        }
    }
    
    result
}

suspend fun fetchImportantData(): String {
    println("Fetching important data...")
    delay(3000)  // Takes 3 seconds (will timeout)
    return "Important Data"
}
```

**Sample Output:**
```
Fetching data with timeout...

Fetching important data...
Timeout occurred!
TIMEOUT
```

### Example: Racing Multiple Algorithms

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.selects.select
import kotlin.random.Random

fun main() = runBlocking {
    println("Finding solution using multiple algorithms...\n")
    
    val solution = findSolutionFastest()
    
    println("\n$solution")
}

suspend fun findSolutionFastest(): String = coroutineScope {
    // Run three different algorithms
    val algorithm1 = async { runAlgorithm1() }
    val algorithm2 = async { runAlgorithm2() }
    val algorithm3 = async { runAlgorithm3() }
    
    // Take whichever finishes first
    val winner = select<String> {
        algorithm1.onAwait { result ->
            algorithm2.cancel()
            algorithm3.cancel()
            println("Algorithm 1 won!")
            result
        }
        algorithm2.onAwait { result ->
            algorithm1.cancel()
            algorithm3.cancel()
            println("Algorithm 2 won!")
            result
        }
        algorithm3.onAwait { result ->
            algorithm1.cancel()
            algorithm2.cancel()
            println("Algorithm 3 won!")
            result
        }
    }
    
    winner
}

suspend fun runAlgorithm1(): String {
    println("Algorithm 1 started")
    delay(Random.nextLong(1000, 2000))
    return "Solution from Algorithm 1"
}

suspend fun runAlgorithm2(): String {
    println("Algorithm 2 started")
    delay(Random.nextLong(1000, 2000))
    return "Solution from Algorithm 2"
}

suspend fun runAlgorithm3(): String {
    println("Algorithm 3 started")
    delay(Random.nextLong(1000, 2000))
    return "Solution from Algorithm 3"
}
```

**Sample Output** (varies due to randomness):
```
Finding solution using multiple algorithms...

Algorithm 1 started
Algorithm 2 started
Algorithm 3 started
Algorithm 3 won!

Solution from Algorithm 3
```

[↑ Back to Table of Contents](#table-of-contents)

---

## Nesting select Expressions

For more complex scenarios, you can nest `select` expressions to handle multiple stages of racing operations.

### Use Cases for Nested select

- Wait for timeout OR both operations complete
- Progressive fallback strategies
- Complex racing scenarios with multiple outcomes

### Example: Nested select for Optimal Waiting

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.selects.select

fun main() = runBlocking {
    println("Fetching data with smart timeout...\n")
    
    val result = getDataWithSmartTimeout()
    
    println("\nFinal result: $result")
}

suspend fun getDataWithSmartTimeout(): String = coroutineScope {
    val dataA = async { fetchDataA() }
    val dataB = async { fetchDataB() }
    val timer = async {
        delay(500)
        println("Timer expired")
    }
    
    // Outer select: race timer against data fetches
    val result = select<String> {
        timer.onAwait {
            // Timer finished first - cancel both
            dataA.cancel()
            dataB.cancel()
            println("Timeout - cancelled both operations")
            "Both unavailable (timeout)"
        }
        
        dataA.onAwait { resultA ->
            // Data A finished first
            println("Data A finished first")
            
            // Inner select: race Data B against timer
            select<String> {
                dataB.onAwait { resultB ->
                    // Both completed before timer
                    timer.cancel()
                    println("Data B also completed - got both results")
                    "$resultA, $resultB"
                }
                timer.onAwait {
                    // Timer finished before Data B
                    dataB.cancel()
                    println("Timer expired waiting for Data B")
                    "$resultA, B unavailable"
                }
            }
        }
        
        dataB.onAwait { resultB ->
            // Data B finished first
            println("Data B finished first")
            
            // Inner select: race Data A against timer
            select<String> {
                dataA.onAwait { resultA ->
                    // Both completed before timer
                    timer.cancel()
                    println("Data A also completed - got both results")
                    "$resultA, $resultB"
                }
                timer.onAwait {
                    // Timer finished before Data A
                    dataA.cancel()
                    println("Timer expired waiting for Data A")
                    "A unavailable, $resultB"
                }
            }
        }
    }
    
    result
}

suspend fun fetchDataA(): String {
    println("Fetching Data A...")
    delay(300)  // Quick
    println("Data A ready")
    return "Data A"
}

suspend fun fetchDataB(): String {
    println("Fetching Data B...")
    delay(400)  // Also quick enough
    println("Data B ready")
    return "Data B"
}
```

**Sample Output:**
```
Fetching data with smart timeout...

Fetching Data A...
Fetching Data B...
Data A ready
Data A finished first
Data B ready
Data B also completed - got both results

Final result: Data A, Data B
```

### Example: Slow Operations with Nested select

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.selects.select

fun main() = runBlocking {
    println("Fetching data with timeout (slow operations)...\n")
    
    val result = getSlowDataWithTimeout()
    
    println("\nFinal result: $result")
}

suspend fun getSlowDataWithTimeout(): String = coroutineScope {
    val dataA = async { fetchSlowDataA() }
    val dataB = async { fetchSlowDataB() }
    val timer = async {
        delay(500)
        println("Timer expired")
    }
    
    select<String> {
        timer.onAwait {
            dataA.cancel()
            dataB.cancel()
            println("Timeout - cancelled both")
            "Both unavailable"
        }
        
        dataA.onAwait { resultA ->
            println("Data A completed")
            select<String> {
                dataB.onAwait { resultB ->
                    timer.cancel()
                    "$resultA, $resultB"
                }
                timer.onAwait {
                    dataB.cancel()
                    println("Timer expired waiting for B")
                    "$resultA, B unavailable"
                }
            }
        }
    }
}

suspend fun fetchSlowDataA(): String {
    println("Fetching Data A...")
    delay(1000)  // Too slow
    return "Data A"
}

suspend fun fetchSlowDataB(): String {
    println("Fetching Data B...")
    delay(1500)  // Too slow
    return "Data B"
}
```

**Sample Output:**
```
Fetching data with timeout (slow operations)...

Fetching Data A...
Fetching Data B...
Timer expired
Timeout - cancelled both

Final result: Both unavailable
```

[↑ Back to Table of Contents](#table-of-contents)

---

## Handling Exceptions in Coroutines

Exception handling in coroutines requires special attention because exceptions can occur in multiple concurrent operations.

### Cancellation Exception

When a coroutine is cancelled, it throws a `CancellationException`. This is a normal part of coroutine lifecycle and usually doesn't need special handling.

### Try-Catch in Coroutines

You can use traditional try-catch blocks within coroutines, but remember:
- Each coroutine can have its own exception
- Exceptions in child coroutines propagate to parent
- Cancellation throws CancellationException

### Example: Handling Cancellation with Exceptions

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Starting operations with exception handling...\n")
    
    val result = getDataWithExceptionHandling()
    
    println("\nFinal result: $result")
}

suspend fun getDataWithExceptionHandling(): String = coroutineScope {
    // Start async operations
    val dataA = async { fetchDataA() }
    val dataB = async { fetchDataB() }
    
    // Start timer
    val timer = async {
        delay(500)
        println("Time is up!")
        // Cancel both operations
        dataA.cancel()
        dataB.cancel()
    }
    
    // Try to get result from A, handle cancellation
    val resultA = try {
        dataA.await()
    } catch (e: CancellationException) {
        println("Data A was cancelled")
        "A unavailable"
    }
    
    // Try to get result from B, handle cancellation
    val resultB = try {
        dataB.await()
    } catch (e: CancellationException) {
        println("Data B was cancelled")
        "B unavailable"
    }
    
    // Cancel timer if it's still active
    if (timer.isActive) {
        timer.cancel()
        println("Timer cancelled (operations completed in time)")
    }
    
    "$resultA, $resultB"
}

suspend fun fetchDataA(): String {
    println("Fetching Data A...")
    delay(300)
    println("Data A ready")
    return "Data A"
}

suspend fun fetchDataB(): String {
    println("Fetching Data B...")
    delay(400)
    println("Data B ready")
    return "Data B"
}
```

**Sample Output:**
```
Starting operations with exception handling...

Fetching Data A...
Fetching Data B...
Data A ready
Data B ready
Timer cancelled (operations completed in time)

Final result: Data A, Data B
```

### Example: Handling Actual Errors

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Processing multiple tasks with error handling...\n")
    
    val results = processMultipleTasks()
    
    println("\nResults:")
    results.forEach { println("  $it") }
}

suspend fun processMultipleTasks(): List<String> = coroutineScope {
    val task1 = async { riskyTask("Task 1", shouldFail = false) }
    val task2 = async { riskyTask("Task 2", shouldFail = true) }
    val task3 = async { riskyTask("Task 3", shouldFail = false) }
    
    // Collect results, handling failures
    val results = mutableListOf<String>()
    
    // Task 1
    try {
        results.add(task1.await())
    } catch (e: Exception) {
        results.add("Task 1 failed: ${e.message}")
    }
    
    // Task 2
    try {
        results.add(task2.await())
    } catch (e: Exception) {
        results.add("Task 2 failed: ${e.message}")
    }
    
    // Task 3
    try {
        results.add(task3.await())
    } catch (e: Exception) {
        results.add("Task 3 failed: ${e.message}")
    }
    
    results
}

suspend fun riskyTask(name: String, shouldFail: Boolean): String {
    println("Starting $name")
    delay(500)
    
    if (shouldFail) {
        throw Exception("Simulated failure in $name")
    }
    
    println("$name completed successfully")
    return "$name result"
}
```

**Sample Output:**
```
Processing multiple tasks with error handling...

Starting Task 1
Starting Task 2
Starting Task 3
Task 1 completed successfully
Task 3 completed successfully

Results:
  Task 1 result
  Task 2 failed: Simulated failure in Task 2
  Task 3 result
```

### Example: Timeout with Exception Handling

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Fetching with timeout and proper cleanup...\n")
    
    try {
        val result = withTimeout(2000) {
            fetchVerySlowData()
        }
        println("Success: $result")
    } catch (e: TimeoutCancellationException) {
        println("Operation timed out!")
        println("Cleanup completed")
    }
}

suspend fun fetchVerySlowData(): String {
    println("Starting slow operation...")
    try {
        delay(5000)  // Will timeout after 2 seconds
        return "Data"
    } finally {
        // Cleanup code runs even when cancelled
        println("Cleaning up resources...")
    }
}
```

**Sample Output:**
```
Fetching with timeout and proper cleanup...

Starting slow operation...
Cleaning up resources...
Operation timed out!
Cleanup completed
```

### Best Practices for Exception Handling

1. **Use try-finally for cleanup**: Ensure resources are released
2. **Handle CancellationException properly**: Don't suppress it
3. **Consider using supervisorScope**: For independent failure handling
4. **Use withTimeout for time limits**: Built-in timeout functionality
5. **Log exceptions appropriately**: Help with debugging

[↑ Back to Table of Contents](#table-of-contents)

---

## Summary

In this lecture, we've explored Kotlin coroutines as a powerful tool for asynchronous programming:

**Key Concepts Covered:**

- **Synchronous vs Asynchronous**: Understanding the difference and when each is appropriate
- **Coroutines Fundamentals**: Lightweight, suspendable units of work
- **Suspend Functions**: Functions that can pause and resume without blocking threads
- **Launch**: Fire-and-forget coroutine execution
- **Join**: Waiting for coroutines to complete
- **Async/Await**: Getting return values from asynchronous operations
- **CoroutineScope**: Grouping related async operations
- **Cancellation**: Stopping unnecessary work and managing timeouts
- **Select**: Racing operations and handling the first completion
- **Exception Handling**: Properly managing errors in concurrent code

Coroutines provide a cleaner, more intuitive way to write asynchronous code compared to traditional threading or callback approaches. They're essential for modern Android development and server-side Kotlin applications.

The key advantages of coroutines include:
- **Readability**: Async code looks like synchronous code
- **Efficiency**: Thousands of coroutines can run with minimal overhead
- **Safety**: Structured concurrency prevents common concurrency bugs
- **Flexibility**: Powerful primitives for complex async patterns

As you continue working with Kotlin, you'll find coroutines becoming an indispensable tool for building responsive, efficient applications.

