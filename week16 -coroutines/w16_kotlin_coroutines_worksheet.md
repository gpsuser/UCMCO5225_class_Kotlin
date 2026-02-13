# Worksheet

## Quiz Section

### Question 1

Fill in the blanks using the words from the word bank below:

A __________ function is marked with the __________ keyword and can pause execution without __________ the underlying thread. When a suspend function encounters a suspension point, it saves its current __________ and releases the thread for other work. The function can later be __________ when ready.

**Word Bank:** blocking, suspend, state, resumed, suspendable

---

### Question 2

Fill in the blanks using the words from the word bank below:

The __________ coroutine builder returns a __________ object and is used for fire-and-forget operations, while the __________ coroutine builder returns a __________ object which represents a future value. To get the actual value from async, you must call the __________ function.

**Word Bank:** Deferred, async, await, Job, launch

---

### Question 3

Fill in the blanks using the words from the word bank below:

When a coroutine is cancelled, it throws a __________. Cancellation in coroutines is __________, meaning the coroutine must check __________ or call suspend functions. Use __________ blocks to ensure resources are cleaned up properly, even when a coroutine is cancelled.

**Word Bank:** cooperative, CancellationException, try-finally, isActive

---

### Question 4

Which of the following best describes the main advantage of using coroutines over traditional threads?

A) Coroutines are faster than threads in all scenarios  
B) Coroutines are lightweight and thousands can run with minimal overhead  
C) Coroutines automatically handle all exceptions without any code  
D) Coroutines can only run on a single thread at a time  

---

### Question 5

What does the `select` expression do in Kotlin coroutines?

A) Selects which thread a coroutine should run on  
B) Filters a list of coroutine results based on a condition  
C) Races multiple Deferred values and returns the result from whichever completes first  
D) Cancels all coroutines except the one you select  

---

### Question 6

When should you use `join()` on a Job?

A) When you need to get the return value from a coroutine  
B) When you need to wait for a coroutine to complete but don't need its return value  
C) When you want to cancel a coroutine  
D) When you want to check if a coroutine is still running  

---

## Task Section

### Task 1: Parallel Configuration Loader

Create a suspend function that loads three different configuration settings concurrently and combines them into a single configuration object. The three settings should be: `theme`, `language`, and `notifications`.

**Requirements:**
- Use `async` and `await` to fetch all three settings concurrently
- Each setting fetch should take different amounts of time (use `delay()`)
- Return a data class containing all three settings
- The function should complete in roughly the time of the slowest operation, not the sum of all operations

**Scaffolding:**

```kotlin
import kotlinx.coroutines.*

data class AppConfiguration(
    val theme: String,
    val language: String,
    val notifications: Boolean
)

fun main() = runBlocking {
    println("Loading configuration...")
    val startTime = System.currentTimeMillis()
    
    val config = loadConfiguration()
    
    val endTime = System.currentTimeMillis()
    println("Configuration loaded: $config")
    println("Time taken: ${endTime - startTime}ms")
}

suspend fun loadConfiguration(): AppConfiguration = coroutineScope {
    // TODO: Implement concurrent loading of all three settings
    // Use async for each setting and await to get results
}

suspend fun fetchThemeSetting(): String {
    // TODO: Add delay of 800ms and return "Dark"
}

suspend fun fetchLanguageSetting(): String {
    // TODO: Add delay of 500ms and return "English"
}

suspend fun fetchNotificationSetting(): Boolean {
    // TODO: Add delay of 1000ms and return true
}
```

**Expected Output:**
```
Loading configuration...
Configuration loaded: AppConfiguration(theme=Dark, language=English, notifications=true)
Time taken: ~1000ms
```

---

### Task 2: Download Manager with Cancellation

Create a file download manager that can cancel downloads if they take too long. The download should be cancelled if it doesn't complete within 2 seconds.

**Requirements:**
- Use a coroutine to simulate file download
- Implement a timeout of 2000ms
- If the download completes in time, return the success message
- If timeout occurs, cancel the download and return a timeout message
- Use proper exception handling for cancellation

**Scaffolding:**

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("Test 1: Fast download (should succeed)")
    println(downloadFileWithTimeout(1500))
    
    println("\nTest 2: Slow download (should timeout)")
    println(downloadFileWithTimeout(3000))
}

suspend fun downloadFileWithTimeout(downloadTime: Long): String = coroutineScope {
    // TODO: Implement download with timeout
    // - Start async download job
    // - Wait maximum 2000ms
    // - Cancel if timeout occurs
    // - Return appropriate success or timeout message
}

suspend fun downloadFile(timeRequired: Long): String {
    // TODO: Implement simulated download
    // - Print "Download started..."
    // - Delay for timeRequired milliseconds
    // - Print "Download complete!" if not cancelled
    // - Return "File downloaded successfully"
}
```

**Expected Output:**
```
Test 1: Fast download (should succeed)
Download started...
Download complete!
File downloaded successfully

Test 2: Slow download (should timeout)
Download started...
Download timeout - operation cancelled
```

---

### Task 3: Multi-Source Data Fetcher (Challenge)

Create a system that fetches user data from three different backup servers. The system should:
- Query all three servers simultaneously
- Return data from the fastest responding server
- Cancel requests to the other servers once one succeeds
- If all servers are too slow (> 2 seconds), return an error message

**Requirements:**
- Use `select` to race the three server queries
- Implement a timeout mechanism
- Use nested `select` if needed for the timeout
- Cancel unnecessary jobs to save resources
- Handle all edge cases properly

**Scaffolding:**

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.selects.select

data class UserData(val username: String, val email: String)

fun main() = runBlocking {
    println("Fetching user data from backup servers...\n")
    
    val result = fetchUserDataFromBackups()
    
    println("\nResult: $result")
}

suspend fun fetchUserDataFromBackups(): String = coroutineScope {
    // TODO: Implement multi-source fetching with select
    // - Start three async queries (server1, server2, server3)
    // - Start a timeout timer (2000ms)
    // - Use select to race all four operations
    // - Cancel remaining jobs when one succeeds
    // - Return appropriate success or timeout message
}

suspend fun queryServer1(): UserData {
    println("Querying Server 1...")
    delay(1800)  // Just under timeout
    println("Server 1 responded")
    return UserData("john_doe", "john@server1.com")
}

suspend fun queryServer2(): UserData {
    println("Querying Server 2...")
    delay(2500)  // Over timeout
    println("Server 2 responded")
    return UserData("john_doe", "john@server2.com")
}

suspend fun queryServer3(): UserData {
    println("Querying Server 3...")
    delay(3000)  // Over timeout
    println("Server 3 responded")
    return UserData("john_doe", "john@server3.com")
}
```

**Expected Output:**
```
Fetching user data from backup servers...

Querying Server 1...
Querying Server 2...
Querying Server 3...
Server 1 responded

Result: User data retrieved: UserData(username=john_doe, email=john@server1.com) from Server 1
```

**Hint:** You'll need to track which server responded so you can include it in your success message. Consider storing both the data and a server identifier together.

---

## Completion Checklist

- [ ] All quiz questions answered
- [ ] Task 1 completed and tested
- [ ] Task 2 completed and tested
- [ ] Challenge task attempted
- [ ] All code compiles without errors
- [ ] Sample outputs match expected results
