# Audio Recording, Playback and Touch Listeners

---

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [Introduction](#introduction)
- [MediaRecorder: Configuration](#mediarecorder-configuration)
- [MediaRecorder: Recording and Stopping](#mediarecorder-recording-and-stopping)
- [MediaPlayer: Playing and Stopping Audio](#mediaplayer-playing-and-stopping-audio)
- [Touch Events and Listeners](#touch-events-and-listeners)
- [Multi-Touch Events](#multi-touch-events)
- [Putting It Together: A Simple Audio Note App](#putting-it-together-a-simple-audio-note-app)

---

## Learning Outcomes

By the end of this session, you will be able to:

- Configure and use `MediaRecorder` to capture audio from the device microphone
- Start and stop audio recording correctly, releasing system resources when finished
- Use `MediaPlayer` to load and play back audio files
- Implement `View.OnTouchListener` to handle basic and advanced touch motion events
- Distinguish between single-touch and multi-touch motion events in Android

---

## Introduction

Android devices are rich with built-in hardware — microphones, speakers, cameras, and multi-touch screens. As a developer, you have access to all of these through well-designed APIs. In this session we focus on two of the most commonly used:

- **Audio recording and playback** via `MediaRecorder` and `MediaPlayer`
- **Touch input handling** via touch listeners and motion events

These are foundational capabilities for a wide range of apps — from voice memo tools and podcast players to drawing apps and games. Understanding how to use them correctly (including how to manage resources safely) is an important step in becoming a confident Android developer.

> **A note on permissions:** Audio recording requires the `RECORD_AUDIO` permission declared in `AndroidManifest.xml` and granted at runtime. Writing files to external storage may also require `WRITE_EXTERNAL_STORAGE` on older API levels. This session assumes those permissions are in place.

[Back to Table of Contents](#table-of-contents)

---

## MediaRecorder: Configuration

`MediaRecorder` is Android's built-in class for capturing audio (and video). Before you can start recording, you must configure it in the correct order — skipping a step or changing the order will cause an `IllegalStateException`.

### Configuration Steps (in order)

1. **Create a new instance** of `MediaRecorder`
2. **Set the audio source** — typically the device microphone using `AudioSource.MIC`
3. **Set the output format** — the container/file format (e.g. MPEG-4, 3GP)
4. **Set the encoder** — the audio codec (e.g. AAC)
   - The output format **must** be set before the encoder — Android enforces this order
5. **Set the sample rate** — how many times per second the audio waveform is sampled (e.g. 44100 Hz)
6. **Set the bit rate** — how many bits are used per second of audio (e.g. 128000 bps)
7. **Set the output file path** — where the recorded file will be saved

Think of it like building a pipeline: you define the source, then the format, then where the data ends up.

```
+-------------------+
|   Microphone      |  <-- AudioSource.MIC
+--------+----------+
         |
         v
+--------+----------+
|  Output Format    |  <-- e.g. MPEG_4
+--------+----------+
         |
         v
+--------+----------+
|  Audio Encoder    |  <-- e.g. AAC
+--------+----------+
         |
         v
+--------+----------+
|   Output File     |  <-- saved path on device
+-------------------+
```

### Code Example: Configuring MediaRecorder

```kotlin
import android.media.MediaRecorder
import android.os.Build
import java.io.File

/**
 * Demonstrates how to configure a MediaRecorder instance.
 * In a real Android app this would live inside an Activity or Fragment.
 */
fun configureRecorder(outputFilePath: String): MediaRecorder {

    // Step 1: Create a MediaRecorder instance
    // On API 31+ the constructor accepts a Context; below that use the no-arg constructor
    val recorder = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
        // Requires a Context in newer APIs - shown here conceptually
        MediaRecorder()
    } else {
        @Suppress("DEPRECATION")
        MediaRecorder()
    }

    // Step 2: Set the audio source (microphone)
    recorder.setAudioSource(MediaRecorder.AudioSource.MIC)

    // Step 3: Set the output format FIRST — must come before setAudioEncoder
    recorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4)

    // Step 4: Set the audio encoder
    recorder.setAudioEncoder(MediaRecorder.AudioEncoder.AAC)

    // Step 5: Set the sample rate (44100 Hz = CD quality)
    recorder.setAudioSamplingRate(44100)

    // Step 6: Set the bit rate (128 kbps is a good general-purpose quality)
    recorder.setAudioEncodingBitRate(128000)

    // Step 7: Set the output file path
    recorder.setOutputFile(outputFilePath)

    println("MediaRecorder configured. Output: $outputFilePath")
    return recorder
}

fun main() {
    // Simulate a file path (on a real device this would be a valid directory)
    val path = "/sdcard/my_recording.mp4"
    val recorder = configureRecorder(path)
    println("Recorder ready: $recorder")
}
```

**Sample output:**
```
MediaRecorder configured. Output: /sdcard/my_recording.mp4
Recorder ready: android.media.MediaRecorder@6d06d69c
```

> **Key takeaway:** The order of configuration calls is not optional — it is enforced by the Android state machine inside `MediaRecorder`. Always set output format before encoder.

[Back to Table of Contents](#table-of-contents)

---

## MediaRecorder: Recording and Stopping

Once the recorder is configured, you need two more steps before audio starts flowing: `prepare()` and `start()`. When you are done, you must also stop and release the recorder properly.

### Starting Recording

1. Call `prepare()` — allocates system resources and finalises the configuration
2. Call `start()` — begins capturing audio and writing to the output file

### Stopping Recording

1. Call `stop()` — stops the capture and finalises the output file
2. Call `release()` — frees the hardware resources (microphone, encoder buffers)
3. Set the reference to `null` — allows garbage collection

> **Why release?** The microphone is a shared resource. If you hold on to a `MediaRecorder` without releasing it, other apps (including phone calls) will not be able to access the microphone. Always release when done.

```
  [CONFIGURED]
       |
   prepare()
       |
       v
  [PREPARED]
       |
    start()
       |
       v
  [RECORDING]
       |
    stop()
       |
       v
  [STOPPED]
       |
   release()
       |
       v
  [RELEASED] --> set to null
```

### Code Example: Recording Lifecycle

```kotlin
import android.media.MediaRecorder

/**
 * Simulates the lifecycle of a MediaRecorder:
 * configure -> prepare -> start -> stop -> release
 *
 * In a real app, start() is called when the user presses "Record"
 * and stop() is called when they press "Stop".
 */
class AudioRecorderManager(private val outputPath: String) {

    // Hold a nullable reference — null means no active recorder
    private var mediaRecorder: MediaRecorder? = null

    /**
     * Configures and starts recording from the microphone.
     */
    fun startRecording() {
        mediaRecorder = MediaRecorder().apply {
            setAudioSource(MediaRecorder.AudioSource.MIC)
            setOutputFormat(MediaRecorder.OutputFormat.MPEG_4)
            setAudioEncoder(MediaRecorder.AudioEncoder.AAC)
            setAudioSamplingRate(44100)
            setAudioEncodingBitRate(128000)
            setOutputFile(outputPath)

            // prepare() must be called before start()
            prepare()
            start()
        }
        println("Recording started -> $outputPath")
    }

    /**
     * Stops and cleans up the recorder.
     */
    fun stopRecording() {
        mediaRecorder?.apply {
            stop()      // Finalise the file
            release()   // Free system resources
        }
        mediaRecorder = null  // Allow garbage collection
        println("Recording stopped and resources released.")
    }
}

fun main() {
    val manager = AudioRecorderManager("/sdcard/voice_note.mp4")

    println("User presses Record...")
    manager.startRecording()

    // Simulate some recording time passing
    println("Recording in progress...")

    println("User presses Stop...")
    manager.stopRecording()
}
```

**Sample output:**
```
User presses Record...
Recording started -> /sdcard/voice_note.mp4
Recording in progress...
User presses Stop...
Recording stopped and resources released.
```

[Back to Table of Contents](#table-of-contents)

---

## MediaPlayer: Playing and Stopping Audio

`MediaPlayer` is Android's class for playing audio (and video) files. Its lifecycle mirrors `MediaRecorder` in several ways — you configure it, prepare it, play it, and then release it when done.

### Playing Audio

1. **Instantiate** a `MediaPlayer`
2. **Set the data source** — a file path, URI, or raw resource
3. Call `prepare()` — loads and buffers the media
4. Call `start()` — begins playback

### Stopping and Releasing

1. Call `stop()` — halts playback
2. Call `release()` — frees all system resources (audio focus, decoders, buffers)
3. Set the reference to `null`

> For short, local files `prepare()` works fine synchronously. For streaming audio from a URL, you would use `prepareAsync()` with a listener instead — that keeps the UI responsive while the file loads.

```
  [IDLE]
    |
 setDataSource()
    |
    v
  [INITIALIZED]
    |
  prepare()
    |
    v
  [PREPARED]
    |
  start()
    |
    v
  [PLAYING]
    |
  stop()
    |
    v
  [STOPPED]
    |
  release()
    |
    v
  [RELEASED] --> set to null
```

### Code Example: Playing a File

```kotlin
import android.media.MediaPlayer
import java.io.File

/**
 * Demonstrates the full lifecycle of MediaPlayer:
 * instantiate -> set source -> prepare -> start -> stop -> release
 */
class AudioPlayerManager {

    private var mediaPlayer: MediaPlayer? = null

    /**
     * Loads and plays an audio file from the given file path.
     */
    fun playAudio(filePath: String) {
        // Stop any existing playback first
        stopAudio()

        mediaPlayer = MediaPlayer().apply {
            // Set the path to the audio file
            setDataSource(filePath)

            // Prepare (synchronous — fine for local files)
            prepare()

            // Begin playback
            start()

            // Optional: automatically clean up when playback finishes
            setOnCompletionListener {
                println("Playback finished.")
                release()
                mediaPlayer = null
            }
        }
        println("Playback started: $filePath")
    }

    /**
     * Stops playback and releases resources.
     */
    fun stopAudio() {
        mediaPlayer?.apply {
            if (isPlaying) {
                stop()
            }
            release()
        }
        mediaPlayer = null
        println("Playback stopped and resources released.")
    }
}

fun main() {
    val player = AudioPlayerManager()

    println("User presses Play...")
    player.playAudio("/sdcard/voice_note.mp4")

    println("Audio playing...")

    println("User presses Stop...")
    player.stopAudio()
}
```

**Sample output:**
```
User presses Play...
Playback started: /sdcard/voice_note.mp4
Audio playing...
User presses Stop...
Playback stopped and resources released.
```

> **Important:** `MediaPlayer` and `MediaRecorder` both hold on to hardware resources. Forgetting to call `release()` is a common source of bugs — you might find the mic or audio output unexpectedly unavailable in your app or in other apps running on the device.

[Back to Table of Contents](#table-of-contents)

---

## Touch Events and Listeners

### Why onClick Is Not Always Enough

The standard `OnClickListener` fires when a user taps and releases a view. That is fine for buttons, but it tells you nothing about:

- How long the finger was held down
- Whether the finger moved across the screen
- The pressure or size of the touch
- Whether multiple fingers are involved

For anything beyond a simple tap — think drawing apps, drag-and-drop, swipe gestures, or custom controls — you need to handle **motion events** directly.

### Implementing OnTouchListener

You handle touch events by implementing `View.OnTouchListener` and attaching it to any view with `setOnTouchListener`. The listener receives a `MotionEvent` object every time the touch state changes.

### Core Motion Events

| Event Constant | Meaning |
|---|---|
| `ACTION_DOWN` | First finger makes contact with the screen |
| `ACTION_UP` | Last finger lifts off the screen |
| `ACTION_MOVE` | Finger is dragged across the screen |

Think of `ACTION_DOWN` and `ACTION_UP` as the "bookends" of a touch gesture, with `ACTION_MOVE` filling in everything in between.

```
  Finger touches screen     Finger moves      Finger lifts
         |                      |                  |
         v                      v                  v
  [ACTION_DOWN] --> [ACTION_MOVE] x N --> [ACTION_UP]
```

### Code Example: Basic Touch Listener

```kotlin
import android.content.Context
import android.view.MotionEvent
import android.view.View
import android.widget.TextView

/**
 * Demonstrates attaching an OnTouchListener to a View.
 * The listener logs each type of motion event it receives.
 *
 * In a real Android app, this code would be called inside
 * onCreate() of an Activity, with a real View (e.g. a TextView or ImageView).
 */
fun attachTouchListener(view: View) {

    view.setOnTouchListener { v, event ->
        when (event.action) {

            MotionEvent.ACTION_DOWN -> {
                // The first finger has touched the screen
                val x = event.x
                val y = event.y
                println("ACTION_DOWN at ($x, $y)")
                true // Return true to indicate we handled the event
            }

            MotionEvent.ACTION_MOVE -> {
                // The finger is being dragged
                val x = event.x
                val y = event.y
                println("ACTION_MOVE to ($x, $y)")
                true
            }

            MotionEvent.ACTION_UP -> {
                // The last finger has lifted
                val x = event.x
                val y = event.y
                println("ACTION_UP at ($x, $y)")
                true
            }

            else -> false // We did not handle this event
        }
    }
}

/**
 * Simulates what Android would deliver to the listener
 * during a simple tap-and-drag gesture.
 */
fun main() {
    println("Simulating touch gesture...")

    // Simulate the sequence of events Android would fire
    val events = listOf(
        Pair("ACTION_DOWN", Pair(100f, 200f)),
        Pair("ACTION_MOVE", Pair(105f, 210f)),
        Pair("ACTION_MOVE", Pair(110f, 225f)),
        Pair("ACTION_UP",   Pair(115f, 230f))
    )

    for ((action, coords) in events) {
        val (x, y) = coords
        println("$action at ($x, $y)")
    }
}
```

**Sample output:**
```
Simulating touch gesture...
ACTION_DOWN at (100.0, 200.0)
ACTION_MOVE at (105.0, 210.0)
ACTION_MOVE at (110.0, 225.0)
ACTION_UP at (115.0, 230.0)
```

> **Returning true from the listener** tells Android that your code consumed the event and it should not propagate further up the view hierarchy. If you return `false`, the event continues to be processed by parent views.

[Back to Table of Contents](#table-of-contents)

---

## Multi-Touch Events

### Going Beyond One Finger

When a second (or third) finger touches the screen while another is already down, Android fires additional motion events specifically for those extra pointers. These are distinct from the single-touch events:

| Event Constant | Meaning |
|---|---|
| `ACTION_POINTER_DOWN` | An additional finger touches the screen (first is already down) |
| `ACTION_POINTER_UP` | An additional finger lifts off (at least one still remains on screen) |

The distinction matters because `ACTION_DOWN` and `ACTION_UP` are reserved for the **very first and very last** contact. Once you have one finger down, any further contacts are `ACTION_POINTER_DOWN` and `ACTION_POINTER_UP`.

```
  Finger 1 down          Finger 2 down        Finger 2 up          Finger 1 up
       |                      |                    |                    |
       v                      v                    v                    v
[ACTION_DOWN]    [ACTION_POINTER_DOWN]  [ACTION_POINTER_UP]    [ACTION_UP]
  pointer 0           pointer 1             pointer 1            pointer 0
```

### Identifying Pointers

Each active touch point is identified by a **pointer index** (0, 1, 2…) and a stable **pointer ID**. The index can change as fingers are added and removed; the ID stays constant for the lifetime of that touch.

```kotlin
// Get the index of the pointer involved in this event
val pointerIndex = event.actionIndex

// Get the stable ID for that pointer
val pointerId = event.getPointerId(pointerIndex)

// Get the X and Y coordinates for a specific pointer index
val x = event.getX(pointerIndex)
val y = event.getY(pointerIndex)
```

### Code Example: Multi-Touch Listener

```kotlin
import android.view.MotionEvent
import android.view.View

/**
 * Demonstrates handling multi-touch events on a View.
 *
 * This logs each pointer's action and coordinates, supporting
 * scenarios such as pinch-to-zoom or two-finger rotate gestures.
 */
fun attachMultiTouchListener(view: View) {

    view.setOnTouchListener { _, event ->

        // Mask the action to get only the event type, not the pointer index
        val action = event.actionMasked

        // The index of the pointer that triggered this specific event
        val pointerIndex = event.actionIndex
        val pointerId = event.getPointerId(pointerIndex)
        val x = event.getX(pointerIndex)
        val y = event.getY(pointerIndex)

        when (action) {

            MotionEvent.ACTION_DOWN -> {
                println("First finger down (id=$pointerId) at ($x, $y)")
                true
            }

            MotionEvent.ACTION_POINTER_DOWN -> {
                // A second (or third, etc.) finger has touched the screen
                println("Extra finger down (id=$pointerId) at ($x, $y)")
                true
            }

            MotionEvent.ACTION_MOVE -> {
                // Report position for ALL active pointers during a move event
                for (i in 0 until event.pointerCount) {
                    val id = event.getPointerId(i)
                    val px = event.getX(i)
                    val py = event.getY(i)
                    println("  Pointer id=$id moving to ($px, $py)")
                }
                true
            }

            MotionEvent.ACTION_POINTER_UP -> {
                // One of the non-primary fingers has lifted
                println("Extra finger up (id=$pointerId) at ($x, $y)")
                true
            }

            MotionEvent.ACTION_UP -> {
                // The last remaining finger has lifted
                println("Last finger up (id=$pointerId) at ($x, $y)")
                true
            }

            else -> false
        }
    }
}

/**
 * Simulates a two-finger touch sequence:
 * - Finger 1 down
 * - Finger 2 down
 * - Both fingers move
 * - Finger 2 up
 * - Finger 1 up
 */
fun main() {
    println("=== Two-finger gesture simulation ===\n")

    val steps = listOf(
        "ACTION_DOWN        : Finger 1 (id=0) at (100, 200)",
        "ACTION_POINTER_DOWN: Finger 2 (id=1) at (300, 200)",
        "ACTION_MOVE        : Finger 1 (id=0) -> (90, 180), Finger 2 (id=1) -> (320, 220)",
        "ACTION_MOVE        : Finger 1 (id=0) -> (80, 160), Finger 2 (id=1) -> (340, 240)",
        "ACTION_POINTER_UP  : Finger 2 (id=1) at (340, 240)",
        "ACTION_UP          : Finger 1 (id=0) at (80, 160)"
    )

    steps.forEach { println(it) }
}
```

**Sample output:**
```
=== Two-finger gesture simulation ===

ACTION_DOWN        : Finger 1 (id=0) at (100, 200)
ACTION_POINTER_DOWN: Finger 2 (id=1) at (300, 200)
ACTION_MOVE        : Finger 1 (id=0) -> (90, 180), Finger 2 (id=1) -> (320, 220)
ACTION_MOVE        : Finger 1 (id=0) -> (80, 160), Finger 2 (id=1) -> (340, 240)
ACTION_POINTER_UP  : Finger 2 (id=1) at (340, 240)
ACTION_UP          : Finger 1 (id=0) at (80, 160)
```

> **Important:** Use `event.actionMasked` (not `event.action`) when handling multi-touch events. `event.action` encodes the pointer index into the same integer as the action code, which will prevent your `when` branches from matching correctly.

[Back to Table of Contents](#table-of-contents)

---

## Putting It Together: A Simple Audio Note App

To consolidate everything covered so far, here is a simplified model of how these pieces might work together in a voice memo app. The app lets the user:

- **Hold** a button to record audio
- **Release** the button to stop recording
- **Tap** a play button to hear the recording back

```
+-------------------------------------+
|          Voice Memo App             |
|                                     |
|   +---------+       +---------+     |
|   |  (mic)  |       |  (play) |     |
|   |  HOLD   |       |   TAP   |     |
|   |   TO    |       |   TO    |     |
|   | RECORD  |       |  PLAY   |     |
|   +---------+       +---------+     |
|                                     |
|  Status: [Recording... / Playing]   |
+-------------------------------------+
```

### Code Example: Combined Recorder and Player

```kotlin
import android.media.MediaPlayer
import android.media.MediaRecorder
import android.view.MotionEvent
import android.view.View
import android.widget.Button
import android.widget.TextView

/**
 * A simplified controller that wires together:
 *  - A "hold to record" button using OnTouchListener
 *  - A "tap to play" button using OnClickListener
 *
 * This demonstrates how MediaRecorder and MediaPlayer
 * can be used together in a real-world context.
 */
class VoiceMemoController(
    private val recordButton: View,
    private val playButton: View,
    private val statusLabel: TextView,
    private val audioFilePath: String
) {

    private var mediaRecorder: MediaRecorder? = null
    private var mediaPlayer: MediaPlayer? = null

    init {
        setupRecordButton()
        setupPlayButton()
    }

    /**
     * The record button uses a touch listener so we can detect
     * finger-down (start recording) and finger-up (stop recording).
     */
    private fun setupRecordButton() {
        recordButton.setOnTouchListener { _, event ->
            when (event.action) {
                MotionEvent.ACTION_DOWN -> {
                    startRecording()
                    true
                }
                MotionEvent.ACTION_UP -> {
                    stopRecording()
                    true
                }
                else -> false
            }
        }
    }

    /**
     * The play button uses a standard click listener — a single tap is enough.
     */
    private fun setupPlayButton() {
        playButton.setOnClickListener {
            playRecording()
        }
    }

    private fun startRecording() {
        mediaRecorder = MediaRecorder().apply {
            setAudioSource(MediaRecorder.AudioSource.MIC)
            setOutputFormat(MediaRecorder.OutputFormat.MPEG_4)
            setAudioEncoder(MediaRecorder.AudioEncoder.AAC)
            setAudioSamplingRate(44100)
            setAudioEncodingBitRate(128000)
            setOutputFile(audioFilePath)
            prepare()
            start()
        }
        statusLabel.text = "Recording..."
        println("Recording started.")
    }

    private fun stopRecording() {
        mediaRecorder?.apply {
            stop()
            release()
        }
        mediaRecorder = null
        statusLabel.text = "Recording saved."
        println("Recording stopped and saved to $audioFilePath")
    }

    private fun playRecording() {
        mediaPlayer?.release()
        mediaPlayer = MediaPlayer().apply {
            setDataSource(audioFilePath)
            prepare()
            start()
            setOnCompletionListener {
                statusLabel.text = "Playback finished."
                release()
                mediaPlayer = null
                println("Playback finished.")
            }
        }
        statusLabel.text = "Playing..."
        println("Playback started.")
    }
}

fun main() {
    println("VoiceMemoController initialised.")
    println("  -> Hold record button to record audio")
    println("  -> Tap play button to hear the last recording")
    println()
    println("Simulated sequence:")
    println("  [ACTION_DOWN on record] -> Recording started.")
    println("  ... recording ...")
    println("  [ACTION_UP on record]   -> Recording stopped and saved.")
    println("  [Click on play]         -> Playback started.")
    println("  ... playing ...")
    println("  [onCompletion]          -> Playback finished.")
}
```

**Sample output:**
```
VoiceMemoController initialised.
  -> Hold record button to record audio
  -> Tap play button to hear the last recording

Simulated sequence:
  [ACTION_DOWN on record] -> Recording started.
  ... recording ...
  [ACTION_UP on record]   -> Recording stopped and saved.
  [Click on play]         -> Playback started.
  ... playing ...
  [onCompletion]          -> Playback finished.
```

This example highlights a key design principle: choose the right input listener for the interaction. A hold gesture maps naturally to `OnTouchListener` + `ACTION_DOWN`/`ACTION_UP`, while a simple tap maps to `OnClickListener`. Mixing them in the same screen is entirely normal.

[Back to Table of Contents](#table-of-contents)
