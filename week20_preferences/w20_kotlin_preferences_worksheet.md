# Worksheet: Android Preferences

---

## Quiz

### Part A — Fill in the Blanks

For each question, choose the correct word from the scrambled word bank to complete the sentence. Each word is used once.

---

**Question 1**

Preferences are defined in an XML resource file stored in the `res/___` directory. The root element of this file is `___`. Each preference element is given a unique `___` attribute, which is used to look up its stored value from Kotlin code.

**Word bank (scrambled):** &nbsp; `PreferenceScreen` &nbsp;&nbsp; `xml` &nbsp;&nbsp; `key`

---

**Question 2**

To access the `SharedPreferences` file written to by the Preferences framework, you call `PreferenceManager.___()`. When reading a preference value, you must always supply a `___` value in case the user has never explicitly set that preference. To persist a manually written change, `___` is the preferred method as it saves data on a background thread.

**Word bank (scrambled):** &nbsp; `apply()` &nbsp;&nbsp; `getDefaultSharedPreferences` &nbsp;&nbsp; `default`

---

**Question 3**

To respond to preference changes in real time, your class must implement `SharedPreferences.___`. The listener should be registered inside `___` and unregistered inside `___` to prevent memory leaks when the fragment is no longer visible.

**Word bank (scrambled):** &nbsp; `onPause()` &nbsp;&nbsp; `OnSharedPreferenceChangeListener` &nbsp;&nbsp; `onResume()`

---

### Part B — Multiple Choice

Circle or write the letter of the best answer.

---

**Question 4**

Which preference type should you use when the user must be able to select **multiple options** from a list, with all selections stored together?

- A) `ListPreference`
- B) `SwitchPreference`
- C) `MultiSelectListPreference`
- D) `EditTextPreference`

---

**Question 5**

In a `ListPreference`, what is the difference between the `entries` and `entryValues` attributes?

- A) `entries` hold the values stored in `SharedPreferences`; `entryValues` are the labels shown to the user
- B) `entries` are the human-readable labels shown to the user; `entryValues` are the values stored in `SharedPreferences`
- C) They are interchangeable — both are stored and displayed in the same way
- D) `entries` are used for single-choice lists; `entryValues` are used for multi-select lists

---

**Question 6**

Why should you **avoid** using an anonymous object (e.g. a lambda) when registering a `SharedPreferences.OnSharedPreferenceChangeListener`?

- A) Anonymous objects cannot implement interfaces in Kotlin
- B) `SharedPreferences` holds only a **weak reference** to listeners, so an anonymous object with no other reference may be garbage collected before it fires
- C) Lambdas are not permitted as arguments to `registerOnSharedPreferenceChangeListener`
- D) Anonymous objects cause an immediate `NullPointerException` at registration time

---

## Tasks

### Task 1 — Reading Fitness App Preferences

A fitness tracking app stores three user preferences:

| Key | Type | Default |
|-----|------|---------|
| `"daily_goal"` | `String` | `"10000"` |
| `"notifications_enabled"` | `Boolean` | `true` |
| `"active_days"` | `Set<String>` | `null` |

Complete the `FitnessActivity` below so that it reads all three preferences and logs a summary to Logcat. Fill in the four blanks marked with `___`.

**Expected output:**
```
D/Fitness: Daily step goal: 10000
D/Fitness: Notifications: enabled
D/Fitness: Active days: monday, wednesday, friday
```

**Scaffold:**

```kotlin
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import androidx.preference.PreferenceManager

class FitnessActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_fitness)

        val prefs = PreferenceManager.getDefaultSharedPreferences(this)

        // TODO: Read "daily_goal" as a String (default "10000")
        val dailyGoal: String? = ___

        // TODO: Read "notifications_enabled" as a Boolean (default true)
        val notificationsEnabled: Boolean = ___

        // TODO: Read "active_days" as a Set<String> (default null)
        val activeDays: Set<String>? = ___

        Log.d("Fitness", "Daily step goal: $dailyGoal")

        // TODO: Fill in the blank to use notificationsEnabled in the condition
        Log.d("Fitness", "Notifications: ${if (___) "enabled" else "disabled"}")

        Log.d("Fitness", "Active days: ${activeDays?.joinToString() ?: "None selected"}")
    }
}
```

---

### Task 2 — Live Summary Updates

When a user picks a new option from a `ListPreference`, the summary line beneath its title should update to show their current selection.

The preference XML below defines a `ListPreference` for measurement units:

```xml
<ListPreference
    android:key="measurement_unit"
    android:title="Measurement Unit"
    android:summary="Select your preferred unit"
    android:entries="@array/unit_entries"
    android:entryValues="@array/unit_values"
    android:defaultValue="km" />
```

The `unit_entries` array contains: `Kilometres`, `Miles`, `Steps`.  
The `unit_values` array contains: `km`, `miles`, `steps`.

Complete the `PreferencesFragment` so that whenever `"measurement_unit"` changes, the summary updates to show the human-readable label (e.g. `"Miles"` rather than `"miles"`). Fill in the four blanks marked with `___`.

**Expected behaviour:**
```
Before change: summary displays "Select your preferred unit"
After user selects "Miles": summary displays "Miles"
```

**Scaffold:**

```kotlin
import android.content.SharedPreferences
import android.os.Bundle
import androidx.preference.ListPreference
import androidx.preference.PreferenceFragmentCompat
import androidx.preference.PreferenceManager

class PreferencesFragment : PreferenceFragmentCompat(),
    SharedPreferences.OnSharedPreferenceChangeListener {

    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        setPreferencesFromResource(R.xml.preferences, rootKey)
    }

    override fun onResume() {
        super.onResume()
        // TODO: Register this fragment as a preference change listener
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .___(this)
    }

    override fun onPause() {
        super.onPause()
        // TODO: Unregister this fragment as a preference change listener
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .___(this)
    }

    override fun onSharedPreferenceChanged(sharedPreferences: SharedPreferences?, key: String?) {
        if (key == "measurement_unit") {
            // TODO: Retrieve the ListPreference using its key
            val pref = findPreference<___>("measurement_unit")

            // TODO: Update the summary to show the human-readable entry label
            pref?.summary = ___
        }
    }
}
```

---

### Challenge Task — Programmatic Preference Reset

A **Reset to Defaults** button in `MainActivity` should programmatically restore three preferences to their original default values — without requiring the user to navigate to the settings screen.

The three preferences and their defaults are:

| Key | Type | Default Value |
|-----|------|---------------|
| `"theme"` | `String` | `"light"` |
| `"font_size"` | `String` | `"medium"` |
| `"show_tips"` | `Boolean` | `true` |

After resetting, the function should read each value back from `SharedPreferences` and log it to confirm the change was applied.

Complete the `resetPreferences()` function and the button click handler below. Fill in all blanks marked with `___`.

**Expected output after the button is tapped:**
```
D/Reset: theme reset to: light
D/Reset: font_size reset to: medium
D/Reset: show_tips reset to: true
```

**Scaffold:**

```kotlin
import android.os.Bundle
import android.util.Log
import android.widget.Button
import androidx.appcompat.app.AppCompatActivity
import androidx.preference.PreferenceManager

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val resetButton = findViewById<Button>(R.id.btnReset)
        resetButton.setOnClickListener {
            // TODO: Call the reset function when the button is clicked
            ___
        }
    }

    private fun resetPreferences() {
        val prefs = PreferenceManager.getDefaultSharedPreferences(this)

        // TODO: Obtain a SharedPreferences editor
        val editor = ___

        // TODO: Write each preference back to its default value
        editor.___(  "theme",      ___ )
        editor.___(  "font_size",  ___ )
        editor.___(  "show_tips",  ___ )

        // TODO: Persist the changes asynchronously
        ___

        // Read back each value and log it to confirm
        val theme    = prefs.getString("theme", "light")
        val fontSize = prefs.getString("font_size", "medium")
        val showTips = prefs.getBoolean("show_tips", true)

        Log.d("Reset", "theme reset to: $theme")
        Log.d("Reset", "font_size reset to: $fontSize")
        Log.d("Reset", "show_tips reset to: $showTips")
    }
}
```

> **Hint:** Use `prefs.edit()` to get an editor. Choose `putString()` or `putBoolean()` depending on the type, then call `apply()` to save asynchronously.
