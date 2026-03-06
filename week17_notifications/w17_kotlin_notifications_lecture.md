# Android Notifications and Started Services in Kotlin

---

## Table of Contents

1. [Learning Outcomes](#learning-outcomes)
2. [Introduction to Android Notifications](#introduction-to-android-notifications)
3. [Notification Permissions](#notification-permissions)
4. [Properties of a Notification](#properties-of-a-notification)
5. [Notification Channels](#notification-channels)
6. [Sending a Notification](#sending-a-notification)
7. [Making Notifications Interactive with PendingIntent](#making-notifications-interactive-with-pendingintent)
8. [Started Services](#started-services)
9. [Scheduled Execution with a Started Service](#scheduled-execution-with-a-started-service)
10. [Putting It All Together](#putting-it-all-together)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain what Android notifications are and when they should be used
- Request the `POST_NOTIFICATIONS` runtime permission correctly on API level 33+
- Construct a notification using `NotificationCompat.Builder` with the required properties
- Create and register a `NotificationChannel` for Android O (API 26) and above
- Send a notification with a unique ID using `NotificationManagerCompat`
- Attach a `PendingIntent` to a notification so that tapping it launches an Activity
- Describe the purpose of a Started Service and implement one by extending the `Service` class
- Use a `Handler` and `Runnable` inside `onStartCommand` to schedule repeated or delayed task execution

---

## Introduction to Android Notifications

[↑ Back to Table of Contents](#table-of-contents)

### What Are Notifications?

Notifications are one of the most important communication tools in Android. They allow your application to alert the user to something important — even when your app is **not currently visible on screen** or is completely closed.

You've almost certainly seen notifications before: a messaging app alerting you to a new message, a calendar app reminding you about an upcoming meeting, or a music player showing playback controls in the notification drawer. All of these are built using Android's notification system.

```
┌─────────────────────────────┐
│  Android Device             │
│  ┌───────────────────────┐  │
│  │ ████ Status Bar ████  │  │
│  └───────────────────────┘  │
│                             │
│  ← swipe down              │
│                             │
│  ┌───────────────────────┐  │
│  │  Notification Drawer  │  │
│  │ ┌───────────────────┐ │  │
│  │ │ 🔔 MyApp • now    │ │  │
│  │ │ You have a        │ │  │
│  │ │ new message!      │ │  │
│  │ └───────────────────┘ │  │
│  │ ┌───────────────────┐ │  │
│  │ │ 📅 Calendar • now │ │  │
│  │ │ Meeting in 10 min │ │  │
│  │ └───────────────────┘ │  │
│  └───────────────────────┘  │
└─────────────────────────────┘
```

### Key Characteristics

- Notifications are delivered to the **notification drawer** (accessible by swiping down from the top of the screen)
- They can be tapped to **launch an Activity** within your app
- They can carry **action buttons** (e.g., Reply, Dismiss)
- They can be **updated** or **removed** programmatically using a unique ID
- From **Android O (API 26)** onwards, they must be assigned to a **Notification Channel**
- From **Android 13 / Tiramisu (API 33)** onwards, you must request **runtime permission** before sending them

---

## Notification Permissions

[↑ Back to Table of Contents](#table-of-contents)

### Why Is Permission Required?

Prior to Android 13 (API level 33), notifications did not require an explicit runtime permission — apps could post notifications freely. However, this led to apps spamming users with unwanted notifications. From API 33 onwards, Android requires that users explicitly grant the `POST_NOTIFICATIONS` permission.

There are **two steps** to implementing this correctly:

1. Declare the permission in your `AndroidManifest.xml`
2. Request the permission at runtime in your Activity (or wherever you have a `Context`)

### Step 1 — Manifest Declaration

In your `AndroidManifest.xml`, add the following line inside the `<manifest>` element (outside `<application>`):

```xml
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
```

This declaration tells the Android system (and the Play Store) that your app *intends* to use this permission. It does not automatically grant it — the user must still approve it at runtime.

### Step 2 — Runtime Permission Request

In your `Activity`, you use the Activity Result API to register a launcher that handles the user's response to the permission dialog. You then check whether the permission is already granted; if not (and if the device is running API 33+), you launch the request.

```kotlin
import android.Manifest
import android.content.pm.PackageManager
import android.os.Build
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity
import androidx.core.content.ContextCompat

class MainActivity : AppCompatActivity() {

    // Step 1: Register a launcher BEFORE the activity is started (not inside onClick etc.)
    // This launcher will be called with the user's permission decision (true = granted)
    private val requestPermissionLauncher =
        registerForActivityResult(ActivityResultContracts.RequestPermission()) { permitted: Boolean ->
            if (!permitted) {
                // The user denied the permission
                // You should inform the user why the feature is unavailable
                // or gracefully disable notification-related functionality
            }
        }

    override fun onCreate(savedInstanceState: android.os.Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Step 2: Check if the permission is already granted
        if (ContextCompat.checkSelfPermission(
                this,
                Manifest.permission.POST_NOTIFICATIONS
            ) != PackageManager.PERMISSION_GRANTED
        ) {
            // Permission not yet granted — only request it on API 33+
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
                requestPermissionLauncher.launch(Manifest.permission.POST_NOTIFICATIONS)
            }
        }
    }
}
```

> **Important:** You must call `registerForActivityResult(...)` before `onCreate()` completes. If you register it too late (e.g., inside a button click handler), the app will crash with an `IllegalStateException`.

---

## Properties of a Notification

[↑ Back to Table of Contents](#table-of-contents)

### Building a Notification with `NotificationCompat.Builder`

Notifications are built using the **Builder pattern** — you create a `NotificationCompat.Builder`, set the properties you want, then call `.build()` to get the final `Notification` object.

The key properties are:

| Property | Method | Description |
|---|---|---|
| Small icon | `setSmallIcon(...)` | Required. Shown in the status bar. |
| Large icon | `setLargeIcon(...)` | Optional. Shown in the expanded notification. |
| Title | `setContentTitle(...)` | Bold heading text |
| Content | `setContentText(...)` | Body text of the notification |
| Priority | `priority = ...` | Controls how prominently the notification is displayed |
| Channel | Constructor parameter | The channel this notification belongs to (API 26+) |

> **Note:** `NotificationCompat` (from the `androidx.core` library) is used instead of the platform's `Notification.Builder` because it provides backwards compatibility across older API levels.

```kotlin
import android.app.NotificationManager
import android.content.Context
import androidx.core.app.NotificationCompat

// This function demonstrates building a notification object.
// In a real app, 'context' would be your Activity or Application context.
fun buildBasicNotification(context: Context): android.app.Notification {

    // The channelName string is typically stored in res/values/strings.xml
    // Here we use a plain string for clarity
    val channelName = "Reminders"

    // Create the builder, passing the context and the channel ID
    val builder = NotificationCompat.Builder(context, channelName)

    // Set the small icon — this MUST be set or the notification will not appear
    // Use an existing drawable resource from your project
    builder.setSmallIcon(android.R.drawable.ic_dialog_info)

    // Set the title (bold heading text)
    builder.setContentTitle("Read me")

    // Set the body text
    builder.setContentText("You have been notified of something")

    // Set the priority — PRIORITY_DEFAULT is suitable for most notifications
    // Other options: PRIORITY_HIGH (makes a sound/pops up), PRIORITY_LOW, PRIORITY_MIN
    builder.priority = NotificationCompat.PRIORITY_DEFAULT

    // Build and return the final Notification object
    val notification = builder.build()
    return notification
}

// Entry point to demonstrate the builder output description
fun main() {
    println("NotificationCompat.Builder creates a Notification object.")
    println("Key properties set:")
    println("  - Small icon: ic_dialog_info")
    println("  - Title: 'Read me'")
    println("  - Content: 'You have been notified of something'")
    println("  - Priority: PRIORITY_DEFAULT")
    println("  - Channel: 'Reminders'")
}
```

**Sample Output:**
```
NotificationCompat.Builder creates a Notification object.
Key properties set:
  - Small icon: ic_dialog_info
  - Title: 'Read me'
  - Content: 'You have been notified of something'
  - Priority: PRIORITY_DEFAULT
  - Channel: 'Reminders'
```

---

## Notification Channels

[↑ Back to Table of Contents](#table-of-contents)

### What Are Notification Channels?

From **Android O (API level 26)** onwards, every notification must be assigned to a **Notification Channel**. A channel represents a *category* of notifications from your app. For example:

```
MyApp Notifications
├── Channel: "Reminders"        (low importance, no sound)
├── Channel: "Messages"         (high importance, plays sound)
└── Channel: "Promotions"       (low importance, silent)
```

The crucial point is that **users can configure each channel independently** in the system settings — they can silence one channel without disabling all your app's notifications. This gives users fine-grained control and is why Google made channels mandatory.

### Why Group Similar Notifications?

You should create one channel per *type* of notification, not one channel per notification. For instance, all reminder notifications should share a single "Reminders" channel. This keeps the settings screen tidy and makes sense to users.

### Creating a Notification Channel

```kotlin
import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.Context
import android.os.Build

// Call this function early in your app lifecycle — typically in onCreate()
// of your Activity or Application class. It is safe to call repeatedly;
// Android will simply update the channel if it already exists.
fun createNotificationChannel(context: Context) {

    // Notification channels only exist from API 26 onwards
    // On older devices this block is skipped entirely
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {

        // Human-readable name shown to the user in Settings
        val channelName = "Reminders"

        // Longer description explaining what this channel is for
        val channelDescription = "Reminders you've added"

        // The channel ID is used to reference this channel when building notifications.
        // It must be unique within your app. Here we reuse channelName for simplicity.
        val channelId = channelName

        // Importance controls default behaviour: sound, pop-up, etc.
        // IMPORTANCE_DEFAULT: makes a sound but does not visually intrude
        // IMPORTANCE_HIGH: pops up on screen (heads-up notification)
        // IMPORTANCE_LOW: no sound or visual interruption
        val importance = NotificationManager.IMPORTANCE_DEFAULT

        // Create the channel object
        val channel = NotificationChannel(channelId, channelName, importance).apply {
            // 'apply' lets us configure extra properties on the same object
            description = channelDescription
        }

        // Register the channel with the system via NotificationManager
        val notificationManager: NotificationManager =
            context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

        notificationManager.createNotificationChannel(channel)

        println("Notification channel '$channelName' created successfully.")
    }
}

fun main() {
    println("createNotificationChannel() should be called in Activity.onCreate()")
    println("It is safe to call multiple times — Android won't duplicate channels.")
    println()
    println("Channel properties:")
    println("  ID          : 'Reminders'")
    println("  Name        : 'Reminders'")
    println("  Description : 'Reminders you've added'")
    println("  Importance  : IMPORTANCE_DEFAULT")
}
```

**Sample Output:**
```
createNotificationChannel() should be called in Activity.onCreate()
It is safe to call multiple times — Android won't duplicate channels.

Channel properties:
  ID          : 'Reminders'
  Name        : 'Reminders'
  Description : 'Reminders you've added'
  Importance  : IMPORTANCE_DEFAULT
```

> **Design tip:** Create your channels when the app first launches. The `createNotificationChannel()` method is idempotent — calling it more than once with the same channel ID is completely safe.

---

## Sending a Notification

[↑ Back to Table of Contents](#table-of-contents)

### Unique Notification IDs

Every notification you post must have an **integer ID** that is unique *within your app*. This ID serves two purposes:

- **Updating a notification:** calling `notify()` with the same ID replaces the existing notification with the new one (useful for progress updates)
- **Cancelling a notification:** calling `cancel(id)` removes it from the drawer

A common approach for generating an ID that is likely to be unique is to use `.hashCode()` on a meaningful string (such as the notification title). This is not guaranteed to be collision-free, but works well in practice for most apps.

### Posting with `NotificationManagerCompat`

```kotlin
import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.Context
import android.os.Build
import androidx.core.app.NotificationCompat
import androidx.core.app.NotificationManagerCompat

// A self-contained function that creates the channel (if needed) and posts a notification.
// In a real Activity you would call this from a button click or similar event.
fun sendNotification(context: Context, title: String, message: String) {

    val channelId = "Reminders"

    // ----- 1. Ensure the channel exists (API 26+) -----
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        val channel = NotificationChannel(
            channelId,
            "Reminders",
            NotificationManager.IMPORTANCE_DEFAULT
        ).apply {
            description = "Reminders you've added"
        }
        val manager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        manager.createNotificationChannel(channel)
    }

    // ----- 2. Build the notification -----
    val builder = NotificationCompat.Builder(context, channelId)
        .setSmallIcon(android.R.drawable.ic_dialog_info)
        .setContentTitle(title)
        .setContentText(message)
        .setPriority(NotificationCompat.PRIORITY_DEFAULT)

    val notification = builder.build()

    // ----- 3. Post the notification with a unique ID -----
    // Using title.hashCode() gives us a reasonably unique integer ID
    // so that different titles produce different notifications
    with(NotificationManagerCompat.from(context)) {
        val notificationId = title.hashCode() // "pretty much unique"
        notify(notificationId, notification)
        println("Notification posted with ID: $notificationId")
    }
}

fun main() {
    println("sendNotification() flow:")
    println("  1. Create NotificationChannel (skipped on API < 26)")
    println("  2. Build Notification using NotificationCompat.Builder")
    println("  3. Post using NotificationManagerCompat.notify(id, notification)")
    println()

    // Demonstrate how hashCode generates an ID from a title string
    val exampleTitle = "Meeting Reminder"
    println("Example title: \"$exampleTitle\"")
    println("Generated ID (hashCode): ${exampleTitle.hashCode()}")
}
```

**Sample Output:**
```
sendNotification() flow:
  1. Create NotificationChannel (skipped on API < 26)
  2. Build Notification using NotificationCompat.Builder
  3. Post using NotificationManagerCompat.notify(id, notification)

Example title: "Meeting Reminder"
Generated ID (hashCode): -1528471135
```

> **Permissions check:** On API 33+ you must also verify that `POST_NOTIFICATIONS` permission is granted before calling `notify()`. The `NotificationManagerCompat` will silently drop the notification if permission is missing.

---

## Making Notifications Interactive with PendingIntent

[↑ Back to Table of Contents](#table-of-contents)

### What Is a PendingIntent?

A **PendingIntent** is a token that wraps an `Intent` and grants another process (in this case, the Android system) the right to execute that Intent on your app's behalf — even when your app is not running.

When the user taps a notification, Android uses the `PendingIntent` you attached to it to start the appropriate Activity in your app.

### The Process

1. Create a regular `Intent` pointing to the `Activity` you want to launch
2. Add any extras the Activity needs (e.g., which item was tapped)
3. Wrap it in a `PendingIntent` using `PendingIntent.getActivity(...)`
4. Attach the `PendingIntent` to your notification builder using `.setContentIntent(...)`

```kotlin
import android.app.NotificationChannel
import android.app.NotificationManager
import android.app.PendingIntent
import android.content.Context
import android.content.Intent
import android.os.Build
import androidx.core.app.NotificationCompat
import androidx.core.app.NotificationManagerCompat

// Hypothetical target Activity — would exist in your project
// class ViewAlertActivity : AppCompatActivity() { ... }

// Sends a notification that launches ViewAlertActivity when tapped
fun sendInteractiveNotification(context: Context, title: String, message: String) {

    val channelId = "Reminders"

    // ----- 1. Create channel if needed -----
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        val channel = NotificationChannel(
            channelId,
            "Reminders",
            NotificationManager.IMPORTANCE_DEFAULT
        ).apply { description = "Reminders you've added" }

        val manager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        manager.createNotificationChannel(channel)
    }

    // ----- 2. Create the Intent for the Activity to launch -----
    // FLAG_ACTIVITY_NEW_TASK is required when starting an Activity from a non-Activity context
    val intent = Intent(context, /* ViewAlertActivity::class.java */ Context::class.java).apply {
        flags = Intent.FLAG_ACTIVITY_NEW_TASK
    }

    // Attach any data the target Activity will need
    intent.putExtra("title", title)

    // ----- 3. Wrap the Intent in a PendingIntent -----
    // Parameters:
    //   context      — used to construct the intent
    //   requestCode  — used to distinguish multiple PendingIntents; hashCode works well here
    //   intent       — the actual Intent to fire
    //   flags        — FLAG_UPDATE_CURRENT updates the extras if the PendingIntent already exists
    val pendingIntent = PendingIntent.getActivity(
        context,
        title.hashCode(),       // request code — doubles as our unique identifier
        intent,
        PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
        // FLAG_IMMUTABLE is required on API 31+ for security
    )

    // ----- 4. Build the notification and attach the PendingIntent -----
    val builder = NotificationCompat.Builder(context, channelId)
        .setSmallIcon(android.R.drawable.ic_dialog_info)
        .setContentTitle(title)
        .setContentText(message)
        .setPriority(NotificationCompat.PRIORITY_DEFAULT)
        .setContentIntent(pendingIntent)   // <-- attach the PendingIntent here
        .setAutoCancel(true)               // dismiss notification when user taps it

    // ----- 5. Post the notification -----
    with(NotificationManagerCompat.from(context)) {
        notify(title.hashCode(), builder.build())
        println("Interactive notification posted. Tapping it will launch ViewAlertActivity.")
    }
}

fun main() {
    println("PendingIntent flow for interactive notifications:")
    println()
    println("  Intent → wrap in PendingIntent → attach to NotificationBuilder")
    println()
    println("  intent = Intent(context, ViewAlertActivity::class.java)")
    println("  intent.flags = FLAG_ACTIVITY_NEW_TASK")
    println("  intent.putExtra(\"title\", title)")
    println()
    println("  pendingIntent = PendingIntent.getActivity(")
    println("      context, requestCode, intent, FLAG_UPDATE_CURRENT or FLAG_IMMUTABLE)")
    println()
    println("  builder.setContentIntent(pendingIntent)")
}
```

**Sample Output:**
```
PendingIntent flow for interactive notifications:

  Intent → wrap in PendingIntent → attach to NotificationBuilder

  intent = Intent(context, ViewAlertActivity::class.java)
  intent.flags = FLAG_ACTIVITY_NEW_TASK
  intent.putExtra("title", title)

  pendingIntent = PendingIntent.getActivity(
      context, requestCode, intent, FLAG_UPDATE_CURRENT or FLAG_IMMUTABLE)

  builder.setContentIntent(pendingIntent)
```

> **API 31+ note:** You must include `PendingIntent.FLAG_IMMUTABLE` (or `FLAG_MUTABLE` if the PendingIntent must be modified by another process) when creating `PendingIntent` objects on Android 12 and above. Omitting this flag will cause a crash.

---

## Started Services

[↑ Back to Table of Contents](#table-of-contents)

### What Is a Service?

An **Android Service** is an application component that can perform long-running operations **in the background**. Unlike an Activity, a Service has **no user interface**. This makes Services ideal for tasks such as:

- Periodically checking for new data (e.g., polling a server)
- Playing audio in the background
- Performing file I/O or network operations that should continue when the user navigates away

```
┌──────────────────────────────────────────┐
│           Android App Process            │
│                                          │
│  ┌─────────────┐    ┌─────────────────┐  │
│  │  Activity   │    │    Service      │  │
│  │  (has UI)   │    │  (no UI)        │  │
│  │             │───▶│                 │  │
│  │  User taps  │    │  Runs even      │  │
│  │  a button   │    │  when Activity  │  │
│  └─────────────┘    │  is closed      │  │
│                     └─────────────────┘  │
└──────────────────────────────────────────┘
```

### Types of Service

There are two main types of Service in Android:

- **Started Service** — started by calling `startService()` and runs until it stops itself or is stopped explicitly. This is what we will implement today.
- **Bound Service** — binds to a component (like an Activity) and runs only while that component is connected to it. This is more complex and worth exploring in directed study.

### Implementing a Started Service

To create a Started Service:

1. Create a class that **extends `Service`**
2. Override `onBind()` and return `null` (since we are not binding)
3. Override `onStartCommand()` and put your background logic there
4. Declare the Service in `AndroidManifest.xml`

```kotlin
import android.app.Service
import android.content.Intent
import android.os.IBinder
import android.util.Log

// A basic Started Service that logs a message when started
// In a real app, this file would be in your app's package, e.g.:
// com.example.myapp/MyStartedService.kt
class MyStartedService : Service() {

    companion object {
        private const val TAG = "MyStartedService"
    }

    // onBind() must be overridden as Service is abstract.
    // For a Started Service we do not support binding, so we return null.
    override fun onBind(intent: Intent?): IBinder? {
        return null
    }

    // onStartCommand() is called each time startService() is invoked.
    // This is where you place the background work your service needs to do.
    // Parameters:
    //   intent    — the Intent that was used to start this service (may carry extras)
    //   flags     — additional data about the start request
    //   startId   — unique ID representing this specific start request
    // Returns:
    //   START_STICKY — if the service is killed by the OS, restart it (with a null intent)
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        Log.d(TAG, "Service started — performing background work")
        println("Service onStartCommand() called. Background work begins here.")
        return START_STICKY
    }

    // Called when the service is being destroyed
    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, "Service destroyed")
        println("Service onDestroy() called.")
    }
}

// -----------------------------------------------------------------------
// In AndroidManifest.xml, declare the service inside <application>:
//
// <service android:name=".MyStartedService" />
//
// To start it from an Activity:
// val intent = Intent(this, MyStartedService::class.java)
// startService(intent)
// -----------------------------------------------------------------------

fun main() {
    println("Started Service lifecycle:")
    println("  startService(intent)  →  onStartCommand() called")
    println("  stopService(intent)   →  onDestroy() called")
    println()
    println("Return value from onStartCommand:")
    println("  START_STICKY        — restart if killed, null intent")
    println("  START_NOT_STICKY    — do not restart if killed")
    println("  START_REDELIVER_INTENT — restart and re-deliver original intent")
}
```

**Sample Output:**
```
Started Service lifecycle:
  startService(intent)  →  onStartCommand() called
  stopService(intent)   →  onDestroy() called

Return value from onStartCommand:
  START_STICKY        — restart if killed, null intent
  START_NOT_STICKY    — do not restart if killed
  START_REDELIVER_INTENT — restart and re-deliver original intent
```

---

## Scheduled Execution with a Started Service

[↑ Back to Table of Contents](#table-of-contents)

### Running Code After a Delay

A common use of a Started Service is to execute a piece of code **after a delay** — for example, sending a reminder notification one minute after the user creates a reminder.

This is achieved using a **`Handler`** and a **`Runnable`**:

- A `Runnable` is a block of code (a lambda) you want to run
- A `Handler` is a mechanism for scheduling that code to run on a particular thread's message queue
- `handler.postDelayed(runnable, delayMs)` schedules the runnable to run after `delayMs` milliseconds

```
onStartCommand() called
        │
        ▼
   Create Runnable ──── { code to run later }
        │
        ▼
   Create Handler (on Main Looper)
        │
        ▼
   handler.postDelayed(runner, 60_000)
        │
        │   ... 60 seconds pass ...
        │
        ▼
   Runnable executes (e.g., sends notification)
```

### A Service That Sends a Delayed Notification

```kotlin
import android.app.NotificationChannel
import android.app.NotificationManager
import android.app.Service
import android.content.Context
import android.content.Intent
import android.os.Build
import android.os.Handler
import android.os.IBinder
import android.os.Looper
import androidx.core.app.NotificationCompat
import androidx.core.app.NotificationManagerCompat

class ReminderService : Service() {

    companion object {
        private const val CHANNEL_ID = "Reminders"
        private const val TAG = "ReminderService"
    }

    // Called once when the service is first created — good place to set up the channel
    override fun onCreate() {
        super.onCreate()
        createNotificationChannel(this)
        println("ReminderService created — notification channel ready.")
    }

    // Return null because this is a Started (not Bound) Service
    override fun onBind(intent: Intent?): IBinder? = null

    // Called each time startService() is invoked
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {

        // Retrieve the reminder title passed from the calling Activity (if any)
        val reminderTitle = intent?.getStringExtra("title") ?: "Reminder"
        val reminderMessage = intent?.getStringExtra("message") ?: "You have a reminder"

        // --- Step 1: Define what should happen after the delay ---
        val runner = Runnable {
            // This code will run after the delay has elapsed
            println("Runnable executing — sending notification for: $reminderTitle")
            sendReminderNotification(this, reminderTitle, reminderMessage)
        }

        // --- Step 2: Create a Handler tied to the main thread's Looper ---
        // Looper.getMainLooper() gives us the main UI thread's message queue.
        // This is important because some UI-related operations must run on the main thread.
        val handler = Handler(Looper.getMainLooper())

        // --- Step 3: Schedule the Runnable to execute after a 1-minute delay ---
        // delayMillis: 1000ms × 60 = 60,000ms = 1 minute
        handler.postDelayed(runner, 1000 * 60)
        println("Reminder scheduled for 60 seconds from now.")

        // --- Step 4: Return START_STICKY ---
        // This tells Android: "If you have to kill this service due to low memory,
        // please restart it when resources become available again."
        return START_STICKY
    }

    // Clean up when the service is stopped
    override fun onDestroy() {
        super.onDestroy()
        println("ReminderService destroyed.")
    }

    // ---- Helper: create the notification channel ----
    private fun createNotificationChannel(context: Context) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                CHANNEL_ID,
                "Reminders",
                NotificationManager.IMPORTANCE_DEFAULT
            ).apply {
                description = "Reminders you've added"
            }
            val manager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
            manager.createNotificationChannel(channel)
        }
    }

    // ---- Helper: build and post the notification ----
    private fun sendReminderNotification(context: Context, title: String, message: String) {
        val builder = NotificationCompat.Builder(context, CHANNEL_ID)
            .setSmallIcon(android.R.drawable.ic_dialog_info)
            .setContentTitle(title)
            .setContentText(message)
            .setPriority(NotificationCompat.PRIORITY_DEFAULT)
            .setAutoCancel(true)

        with(NotificationManagerCompat.from(context)) {
            notify(title.hashCode(), builder.build())
        }
    }
}

// -----------------------------------------------------------------------
// To start this service from an Activity:
//
// val intent = Intent(this, ReminderService::class.java).apply {
//     putExtra("title", "Meeting")
//     putExtra("message", "Your meeting starts in 1 minute!")
// }
// startService(intent)
// -----------------------------------------------------------------------

fun main() {
    println("ReminderService simulation:")
    println()
    println("[onCreate]      → createNotificationChannel()")
    println("[onStartCommand]→ Runnable defined")
    println("[onStartCommand]→ Handler(Looper.getMainLooper()) created")
    println("[onStartCommand]→ handler.postDelayed(runner, 60000) called")
    println("[onStartCommand]→ returns START_STICKY")
    println()
    println("... 60 seconds later ...")
    println()
    println("[Runnable runs] → sendReminderNotification() called")
    println("[Notification]  → Posted to the notification drawer")
}
```

**Sample Output:**
```
ReminderService simulation:

[onCreate]      → createNotificationChannel()
[onStartCommand]→ Runnable defined
[onStartCommand]→ Handler(Looper.getMainLooper()) created
[onStartCommand]→ handler.postDelayed(runner, 60000) called
[onStartCommand]→ returns START_STICKY

... 60 seconds later ...

[Runnable runs] → sendReminderNotification() called
[Notification]  → Posted to the notification drawer
```

### A Note on `START_STICKY`

Returning `START_STICKY` from `onStartCommand` is important for long-running or repeated services. It signals to the Android OS that this service should be **restarted automatically** if it is killed to free up memory. The restarted service will receive a `null` Intent, so always null-check the `intent` parameter in `onStartCommand`.

---

## Putting It All Together

[↑ Back to Table of Contents](#table-of-contents)

### The Full Notification + Service Architecture

Here is how all the pieces fit together in a real "reminder" application:

```
┌──────────────────────────────────────────────────────┐
│                    Activity                          │
│                                                      │
│  [User sets a reminder and taps "Save"]              │
│           │                                          │
│           │  startService(intent with title/message) │
│           ▼                                          │
│  ┌─────────────────────┐                             │
│  │   ReminderService   │                             │
│  │                     │                             │
│  │  onCreate()         │ ← create channel            │
│  │  onStartCommand()   │ ← schedule with Handler     │
│  │      │              │                             │
│  │      │ 60 seconds   │                             │
│  │      ▼              │                             │
│  │  Runnable fires     │ ← send notification         │
│  └─────────────────────┘                             │
│           │                                          │
│           ▼                                          │
│  ┌─────────────────────────────────────┐             │
│  │      Notification Drawer            │             │
│  │  🔔 Meeting • now                   │             │
│  │  Your meeting starts in 1 minute!   │             │
│  └──────────────────┬──────────────────┘             │
│                     │ user taps                      │
│                     ▼                                │
│             ViewAlertActivity                        │
│             (launched via PendingIntent)             │
└──────────────────────────────────────────────────────┘
```

### Summary of Key Classes and Concepts

| Concept | Class / Method | Purpose |
|---|---|---|
| Build a notification | `NotificationCompat.Builder` | Assembles the notification |
| Request permission | `registerForActivityResult` | API 33+ runtime permission |
| Create a channel | `NotificationChannel` | Group notifications (API 26+) |
| Post a notification | `NotificationManagerCompat.notify()` | Delivers notification to drawer |
| Tap action | `PendingIntent.getActivity()` | Launches an Activity on tap |
| Background work | `Service.onStartCommand()` | Runs code outside the UI |
| Schedule delayed task | `Handler.postDelayed()` | Executes code after a delay |
| Keep service alive | `START_STICKY` | Auto-restarts if OS kills service |

### Common Pitfalls to Avoid

- **Forgetting the small icon** — `setSmallIcon()` is mandatory. A notification without one will not appear.
- **Missing channel on API 26+** — building a notification with a channel ID that was never registered will silently prevent the notification from displaying.
- **Forgetting `FLAG_IMMUTABLE` on API 31+** — creating a `PendingIntent` without this flag will crash on Android 12 and above.
- **Not checking permission on API 33+** — calling `notify()` without the `POST_NOTIFICATIONS` permission will fail silently.
- **Not declaring the Service in the manifest** — a Service that is not declared in `AndroidManifest.xml` cannot be started and will throw a `ServiceNotFoundException`.

```xml
<!-- Required in AndroidManifest.xml -->
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

<application ...>
    <activity android:name=".MainActivity" ... />
    <activity android:name=".ViewAlertActivity" />
    <service android:name=".ReminderService" />
</application>
```

---

*End of Lecture*

[↑ Back to Table of Contents](#table-of-contents)
