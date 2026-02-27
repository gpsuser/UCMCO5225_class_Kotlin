# Worksheet

---

## Quiz Section

### Part A — Fill in the Blanks

For each question below, choose the correct word from the scrambled word bank to complete the sentence. Each word is used once.

---

**Question 1**

Word bank (scrambled): `CHANNEL` · `DRAWER` · `26` · `ID` · `IMMUTABLE`

Complete the following sentences about Android notifications:

a) Notifications are delivered to the notification _____________, which the user can access by swiping down from the top of the screen.

b) From Android O (API level _____________) onwards, every notification must be assigned to a Notification _____________.

c) When posting a notification, you must supply a unique integer _____________ so that the notification can be updated or cancelled later.

d) When creating a `PendingIntent` on Android 12 (API 31) and above, you must include the `FLAG_` _____________ flag to avoid a crash.

---

**Question 2**

Word bank (scrambled): `onStartCommand` · `null` · `START_STICKY` · `manifest` · `onBind`

Complete the following sentences about Started Services:

a) A Started Service is declared in the app's _____________.

b) For a Started Service, the `_____________()` method should return `_____________` because the service does not support binding.

c) Background work in a Started Service is placed inside the `_____________()` method.

d) Returning `_____________` from `onStartCommand()` tells Android to restart the service automatically if it is killed due to low memory.

---

**Question 3**

Word bank (scrambled): `postDelayed` · `Runnable` · `Handler` · `Looper` · `milliseconds`

Complete the following sentences about scheduling delayed execution:

a) A block of code that you want to run at a later time is wrapped in a _____________.

b) A _____________ is used to schedule that block of code on a thread's message queue.

c) The main thread's message queue is accessed via `_____________.getMainLooper()`.

d) The method `handler._____________(runner, delay)` schedules the code to run after the specified delay in _____________.

---

### Part B — Multiple Choice

Choose the **one best answer** for each question.

---

**Question 4**

Which of the following is the **correct** way to check and request the `POST_NOTIFICATIONS` permission on Android 13 (API 33) and above?

- A) Add `<uses-permission>` to the manifest — no further steps are needed.
- B) Call `requestPermissions()` from inside a button click handler, after the Activity has started.
- C) Register an `ActivityResultContracts.RequestPermission` launcher before `onCreate()` completes, then launch it if the permission has not yet been granted.
- D) Notifications do not require any permission on Android 13; channels alone are sufficient.

---

**Question 5**

A developer builds and posts a notification but it never appears on the device running Android O (API 26). The small icon has been set and the permission is granted. What is the **most likely** cause?

```
NotificationCompat.Builder(context, "alerts")
    .setSmallIcon(R.drawable.ic_bell)
    .setContentTitle("Update")
    .setContentText("A new update is ready.")
    .setPriority(NotificationCompat.PRIORITY_DEFAULT)
```

- A) `PRIORITY_DEFAULT` is too low; it must be `PRIORITY_HIGH`.
- B) A `NotificationChannel` with the ID `"alerts"` was never created and registered with the system.
- C) `setContentText()` must be called before `setContentTitle()`.
- D) `NotificationManagerCompat` cannot post notifications on API 26 — the platform `NotificationManager` must be used instead.

---

**Question 6**

Study the diagram below and identify what it represents:

```
Activity
   │
   │  startService(intent)
   ▼
ReminderService
   │
   ├─ onCreate()       → createNotificationChannel()
   │
   └─ onStartCommand() → handler.postDelayed(runner, 60_000)
                                   │
                              60 seconds
                                   │
                                   ▼
                            Runnable fires
                                   │
                                   ▼
                         Notification posted
                                   │
                             User taps it
                                   │
                                   ▼
                         ViewAlertActivity launched
```

Which statement **best** describes what this diagram shows?

- A) A Bound Service that connects to an Activity and streams live data.
- B) A Started Service that schedules a delayed notification; tapping the notification launches a new Activity via a `PendingIntent`.
- C) A `BroadcastReceiver` that listens for a system alarm and posts a notification every 60 seconds on a loop.
- D) An `AsyncTask` that posts a notification after performing a network request in the background.

---

## Task Section

### Task 1 — Priority Notification Dispatcher (5–10 minutes)

You are building a simple notification dispatcher. Complete the function below so that it sends **two different notifications** with the same channel but **different priorities**: one using `PRIORITY_HIGH` (for urgent alerts) and one using `PRIORITY_LOW` (for informational messages).

**Your task:** Fill in the `TODO` sections so that:
- The urgent notification uses `PRIORITY_HIGH` and the title `"Urgent Alert"`
- The info notification uses `PRIORITY_LOW` and the title `"Info"`
- Each notification has a unique ID derived from its title using `.hashCode()`

**Scaffolding:**

```kotlin
import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.Context
import android.os.Build
import androidx.core.app.NotificationCompat
import androidx.core.app.NotificationManagerCompat

fun dispatchNotifications(context: Context) {

    val channelId = "Dispatcher"

    // Step 1: Create the notification channel (API 26+)
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        val channel = NotificationChannel(
            channelId,
            "Dispatcher",
            NotificationManager.IMPORTANCE_DEFAULT
        )
        val manager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        manager.createNotificationChannel(channel)
    }

    // Step 2: Build the urgent notification
    val urgentTitle = "Urgent Alert"
    val urgentNotification = NotificationCompat.Builder(context, channelId)
        .setSmallIcon(android.R.drawable.ic_dialog_alert)
        .setContentTitle(urgentTitle)
        .setContentText("This requires your immediate attention.")
        .setPriority(/* TODO: set high priority */)
        .build()

    // Step 3: Build the info notification
    val infoTitle = "Info"
    val infoNotification = NotificationCompat.Builder(context, channelId)
        .setSmallIcon(android.R.drawable.ic_dialog_info)
        .setContentTitle(infoTitle)
        .setContentText("Nothing urgent — just keeping you informed.")
        .setPriority(/* TODO: set low priority */)
        .build()

    // Step 4: Post both notifications using unique IDs
    with(NotificationManagerCompat.from(context)) {
        notify(/* TODO: unique ID for urgent */, urgentNotification)
        notify(/* TODO: unique ID for info   */, infoNotification)
    }
}

fun main() {
    println("dispatchNotifications() complete.")
    println("Urgent ID  : ${"Urgent Alert".hashCode()}")
    println("Info ID    : ${"Info".hashCode()}")
}
```

**Expected output (console):**
```
dispatchNotifications() complete.
Urgent ID  : <integer>
Info ID    : <integer>
```
*(The exact integers will depend on your platform's `hashCode()` implementation.)*

---

### Task 2 — Cancellable Notification (5–10 minutes)

Sometimes you need to **remove** a notification programmatically — for example, when the user completes the action the notification was asking them to do.

**Your task:** Complete the two functions below:
- `showTaskNotification(context)` — posts a notification with the title `"Task Pending"` and returns the notification ID
- `dismissNotification(context, id)` — cancels the notification with the given ID

**Scaffolding:**

```kotlin
import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.Context
import android.os.Build
import androidx.core.app.NotificationCompat
import androidx.core.app.NotificationManagerCompat

fun showTaskNotification(context: Context): Int {
    val channelId = "Tasks"
    val title = "Task Pending"

    // TODO: Create a NotificationChannel if needed (API 26+)

    // TODO: Build a notification with:
    //       - small icon : android.R.drawable.ic_menu_agenda
    //       - title      : title
    //       - content    : "You have an outstanding task."
    //       - priority   : PRIORITY_DEFAULT

    val notificationId = title.hashCode()

    // TODO: Post the notification using NotificationManagerCompat

    println("Notification posted with ID: $notificationId")
    return notificationId
}

fun dismissNotification(context: Context, id: Int) {
    // TODO: Use NotificationManagerCompat to cancel the notification with the given id
    println("Notification with ID $id dismissed.")
}

fun main() {
    println("--- Showing notification ---")
    // In a real app you would pass a real Context; here we just simulate the flow.
    println("showTaskNotification() would post notification ID: ${"Task Pending".hashCode()}")
    println()
    println("--- Dismissing notification ---")
    println("dismissNotification() would cancel ID: ${"Task Pending".hashCode()}")
}
```

**Expected output (console):**
```
--- Showing notification ---
showTaskNotification() would post notification ID: <integer>

--- Dismissing notification ---
dismissNotification() would cancel ID: <integer>
```

---

### Challenge Task — Self-Stopping Reminder Service (10–15 minutes)

A common real-world pattern is a service that does its work **once** and then **stops itself** rather than staying alive indefinitely.

**Your task:** Complete the `SingleShotReminderService` below so that it:

1. Reads a `"title"` and `"message"` string extra from the incoming `Intent`
2. Uses a `Handler` to post a notification after a **30-second** delay
3. Calls `stopSelf()` **after** the notification has been sent (inside the `Runnable`, not before)
4. Returns `START_NOT_STICKY` — because once the notification has fired, there is no need to restart the service

**Scaffolding:**

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

class SingleShotReminderService : Service() {

    companion object {
        private const val CHANNEL_ID = "SingleShot"
        private const val DELAY_MS = 30_000L  // 30 seconds
    }

    override fun onCreate() {
        super.onCreate()
        // TODO: Create the notification channel here
    }

    override fun onBind(intent: Intent?): IBinder? = null

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {

        // TODO: Read "title" and "message" from intent extras
        //       Use fallback values if they are null

        // TODO: Create a Runnable that:
        //         1. Posts the notification using a helper function or inline code
        //         2. Calls stopSelf() to shut the service down

        // TODO: Create a Handler on the main Looper and postDelayed the Runnable by DELAY_MS

        println("SingleShotReminderService started — notification due in ${DELAY_MS / 1000}s")

        // TODO: Return the correct constant so the service does NOT restart if killed
        return TODO()
    }

    override fun onDestroy() {
        super.onDestroy()
        println("SingleShotReminderService destroyed.")
    }

    // Helper — feel free to expand this
    private fun postNotification(title: String, message: String) {
        val builder = NotificationCompat.Builder(this, CHANNEL_ID)
            .setSmallIcon(android.R.drawable.ic_dialog_info)
            .setContentTitle(title)
            .setContentText(message)
            .setPriority(NotificationCompat.PRIORITY_DEFAULT)
            .setAutoCancel(true)

        with(NotificationManagerCompat.from(this)) {
            notify(title.hashCode(), builder.build())
            println("Notification posted: \"$title\"")
        }
    }
}

// -----------------------------------------------------------------------
// To test, start from an Activity:
//
// val intent = Intent(this, SingleShotReminderService::class.java).apply {
//     putExtra("title", "Quick Reminder")
//     putExtra("message", "Don't forget to review your notes!")
// }
// startService(intent)
// -----------------------------------------------------------------------

fun main() {
    println("Expected console output when the service runs:")
    println()
    println("SingleShotReminderService started — notification due in 30s")
    println("... 30 seconds later ...")
    println("Notification posted: \"Quick Reminder\"")
    println("SingleShotReminderService destroyed.")
}
```

**Expected output (console):**
```
SingleShotReminderService started — notification due in 30s
... 30 seconds later ...
Notification posted: "Quick Reminder"
SingleShotReminderService destroyed.
```

**Hints:**
- You can call `stopSelf()` from inside a `Runnable` — the service will not stop until the `Runnable` finishes executing.
- `START_NOT_STICKY` tells Android not to restart the service if it is killed, which is appropriate here because the one-shot work would need to be re-triggered by the user anyway.
- Remember to declare `SingleShotReminderService` in `AndroidManifest.xml` under `<application>`.
