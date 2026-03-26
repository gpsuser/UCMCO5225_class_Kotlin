# Worksheet: Audio Recording, Playback and Touch Listeners

---

## Part A: Quiz

### Fill in the Blanks

*Use each word or phrase from the word bank to complete the statements. Each item is used exactly once.*

---

**Question 1**

**Word bank:** &nbsp; `audio encoder` &nbsp; `output format` &nbsp; `prepare()` &nbsp; `start()`

When configuring a `MediaRecorder`, the __________ must be set before the __________. Once all configuration is complete, call __________ to allocate system resources, then call __________ to begin capturing audio.

---

**Question 2**

**Word bank:** &nbsp; `ACTION_DOWN` &nbsp; `ACTION_MOVE` &nbsp; `ACTION_UP` &nbsp; `release()`

When the first finger makes contact with the screen, Android fires __________. While the finger is being dragged, Android continuously fires __________. When the last remaining finger lifts off the screen, Android fires __________. To free the hardware resources held by a `MediaPlayer` or `MediaRecorder`, you must call __________.

---

**Question 3**

**Word bank:** &nbsp; `ACTION_POINTER_DOWN` &nbsp; `actionMasked` &nbsp; `null` &nbsp; `pointer index`

When a second finger touches the screen while one is already held down, Android fires __________. Each active contact point is identified by a __________ (0, 1, 2…) that can shift as fingers are added or removed. When writing a multi-touch listener, you should read `event.__________` rather than `event.action` to get the correct action code. After calling `release()` on a `MediaRecorder`, the variable should be set to __________ to allow garbage collection.

---

### Multiple Choice

*Circle or mark the single best answer for each question.*

---

**Question 4**

A developer calls `setAudioEncoder()` before `setOutputFormat()` on a freshly created `MediaRecorder`. What is the result?

- A) Android silently reorders the calls into the correct sequence
- B) An `IllegalStateException` is thrown at runtime
- C) The recording proceeds but produces a corrupted output file
- D) The encoder is silently set to a default value and configuration continues

---

**Question 5**

In a multi-touch interaction, finger 1 is already on screen. Finger 2 now lifts off while finger 1 is still touching. Which event does Android fire for finger 2?

- A) `ACTION_UP`
- B) `ACTION_CANCEL`
- C) `ACTION_POINTER_DOWN`
- D) `ACTION_POINTER_UP`

---

**Question 6**

An `OnTouchListener` is attached to a `View`. The listener handles `ACTION_DOWN` and returns `false`. What is the effect of returning `false`?

- A) All future touch events on this view are ignored
- B) The event is treated as consumed and stops propagating
- C) The event is not consumed and continues up the view hierarchy
- D) The view's `OnClickListener` is automatically triggered instead

---

## Part B: Tasks

### Task 1 — Touch Duration Logger

Write a `TouchDurationLogger` class that measures how long the user holds a finger on screen for each individual press, then provides a summary. Complete the `TODO` sections in the scaffold below.

**Requirements:**
- Record the system time in milliseconds when `ACTION_DOWN` fires
- When `ACTION_UP` fires, calculate the held duration, print it, and store it
- `printSummary()` should print the total number of presses recorded and the average duration in milliseconds

```kotlin
import android.view.MotionEvent
import android.view.View

class TouchDurationLogger {

    private var touchStartTime: Long = 0L
    private val durations = mutableListOf<Long>()

    fun attach(view: View) {
        view.setOnTouchListener { _, event ->
            when (event.action) {

                MotionEvent.ACTION_DOWN -> {
                    // TODO: Store the current time in milliseconds
                    true
                }

                MotionEvent.ACTION_UP -> {
                    // TODO: Calculate duration since ACTION_DOWN
                    // TODO: Add the duration to durations
                    // TODO: Print "Touch held for X ms"
                    true
                }

                else -> false
            }
        }
    }

    fun printSummary() {
        // TODO: Print the count of recorded presses
        // TODO: Print the average duration (use integer division)
    }
}

// Simulated test — call the logic directly without a real View
fun main() {
    val logger = TouchDurationLogger()

    // Simulate two presses: 350 ms and 800 ms
    // Call logger.printSummary() after both presses

    // Expected output:
    // Touch held for 350 ms
    // Touch held for 800 ms
    // Summary: 2 press(es). Average: 575 ms
}
```

**Expected output:**
```
Touch held for 350 ms
Touch held for 800 ms
Summary: 2 press(es). Average: 575 ms
```

---

### Task 2 — Recorder Configuration Validator

Write a `validateConfig()` function that checks a `RecorderConfig` before it is applied to a `MediaRecorder`. Complete the `TODO` sections below.

**Validation rules:**
- `filePath` must end with `".mp4"` — if not, print `"Error: File path must end with .mp4"` and return `false`
- `bitRate` must be between `64000` and `320000` inclusive — if not, print `"Error: Bit rate must be between 64000 and 320000 bps"` and return `false`
- `sampleRate` must be one of `22050`, `44100`, or `48000` — if not, print `"Error: Sample rate must be 22050, 44100, or 48000 Hz"` and return `false`
- If all checks pass, print `"Config valid."` and return `true`

```kotlin
data class RecorderConfig(
    val filePath: String,
    val bitRate: Int,
    val sampleRate: Int
)

fun validateConfig(config: RecorderConfig): Boolean {
    // TODO: Check filePath ends with ".mp4"

    // TODO: Check bitRate is in range 64000..320000

    // TODO: Check sampleRate is one of the accepted values

    // TODO: Print "Config valid." and return true if all checks passed
}

fun main() {
    val configs = listOf(
        RecorderConfig("/sdcard/note.mp4", 128000, 44100),  // valid
        RecorderConfig("/sdcard/note.mp3", 128000, 44100),  // bad extension
        RecorderConfig("/sdcard/note.mp4", 16000,  44100),  // bad bit rate
        RecorderConfig("/sdcard/note.mp4", 128000, 8000)    // bad sample rate
    )
    configs.forEach { config ->
        println(validateConfig(config))
        println()
    }
}
```

**Expected output:**
```
Config valid.
true

Error: File path must end with .mp4
false

Error: Bit rate must be between 64000 and 320000 bps
false

Error: Sample rate must be 22050, 44100, or 48000 Hz
false
```

---

### Challenge Task — Multi-Touch Finger Tracker

Implement a `MultiTouchTracker` class that processes a sequence of touch events and produces a report. Complete the `TODO` sections below.

**Requirements:**
- Track which pointer IDs are currently active (on screen)
- Track all pointer IDs seen across the entire session
- Record the peak (maximum) number of simultaneous fingers at any moment
- `printReport()` should print the peak finger count and a sorted list of all pointer IDs observed

```kotlin
class MultiTouchTracker {

    private val activePointers  = mutableSetOf<Int>()
    private val allPointersSeen = mutableSetOf<Int>()
    private var peakFingerCount = 0

    /**
     * Process one simulated touch event.
     *
     * @param action    One of: "DOWN", "POINTER_DOWN", "POINTER_UP", "UP"
     * @param pointerId The stable identifier for the pointer involved
     */
    fun processEvent(action: String, pointerId: Int) {
        when (action) {
            "DOWN", "POINTER_DOWN" -> {
                // TODO: Add pointerId to activePointers and allPointersSeen
                // TODO: Update peakFingerCount if current active count exceeds it
                println("Finger $pointerId touched.  Active fingers: ${activePointers.size}")
            }
            "POINTER_UP", "UP" -> {
                // TODO: Remove pointerId from activePointers
                println("Finger $pointerId lifted.  Active fingers: ${activePointers.size}")
            }
        }
    }

    fun printReport() {
        // TODO: Print peakFingerCount
        // TODO: Print allPointersSeen sorted in ascending order
    }
}

fun main() {
    val tracker = MultiTouchTracker()

    tracker.processEvent("DOWN",         0)
    tracker.processEvent("POINTER_DOWN", 1)
    tracker.processEvent("POINTER_DOWN", 2)
    tracker.processEvent("POINTER_UP",   1)
    tracker.processEvent("POINTER_DOWN", 3)
    tracker.processEvent("POINTER_UP",   3)
    tracker.processEvent("POINTER_UP",   2)
    tracker.processEvent("UP",           0)

    println()
    tracker.printReport()
}
```

**Expected output:**
```
Finger 0 touched.  Active fingers: 1
Finger 1 touched.  Active fingers: 2
Finger 2 touched.  Active fingers: 3
Finger 1 lifted.  Active fingers: 2
Finger 3 touched.  Active fingers: 3
Finger 3 lifted.  Active fingers: 2
Finger 2 lifted.  Active fingers: 1
Finger 0 lifted.  Active fingers: 0

Peak simultaneous fingers: 3
All pointer IDs seen: [0, 1, 2, 3]
```

> **Hint:** Think carefully about *when* you update `peakFingerCount` — it must reflect the count *after* the new pointer has been added.
