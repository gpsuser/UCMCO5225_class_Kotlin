# Answer Key: Android Preferences

---

## Quiz Answers

### Part A — Fill in the Blanks

---

**Question 1**

> Preferences are defined in an XML resource file stored in the `res/**xml**` directory. The root element of this file is `**PreferenceScreen**`. Each preference element is given a unique `**key**` attribute, which is used to look up its stored value from Kotlin code.

**Answers:** `xml` · `PreferenceScreen` · `key`

**Explanation:**
- `res/xml/` is the dedicated directory for preference XML files — it is separate from `res/layout/`.
- `PreferenceScreen` is the required root element of any preference XML file.
- The `key` attribute is the bridge between the XML definition and Kotlin code — it is the string identifier used with `getBoolean()`, `getString()`, etc.

**Common mistake:** Students sometimes try to place preference XML inside `res/layout/`. This will not work — the file must live in `res/xml/`.

---

**Question 2**

> To access the `SharedPreferences` file written to by the Preferences framework, you call `PreferenceManager.**getDefaultSharedPreferences**()`  . When reading a preference value, you must always supply a `**default**` value in case the user has never explicitly set that preference. To persist a manually written change, `**apply()**` is the preferred method as it saves data on a background thread.

**Answers:** `getDefaultSharedPreferences` · `default` · `apply()`

**Explanation:**
- `PreferenceManager.getDefaultSharedPreferences(context)` returns the same `SharedPreferences` instance that the Preference framework writes to — it is important to use this method rather than creating a named preferences file, which would not contain the values set by the UI.
- A default value is required because the preference may never have been set (e.g. first launch before the user visits Settings).
- `apply()` is asynchronous and does not block the main thread. `commit()` is synchronous and returns a `Boolean` indicating success, but should only be used when you need to confirm the write immediately.

**Common mistake:** Using `commit()` unnecessarily, or forgetting to supply a default value (which causes a compilation error since the parameter is not optional).

---

**Question 3**

> To respond to preference changes in real time, your class must implement `SharedPreferences.**OnSharedPreferenceChangeListener**`. The listener should be registered inside `**onResume()**` and unregistered inside `**onPause()**` to prevent memory leaks when the fragment is no longer visible.

**Answers:** `OnSharedPreferenceChangeListener` · `onResume()` · `onPause()`

**Explanation:**
- `OnSharedPreferenceChangeListener` is a functional interface with a single method: `onSharedPreferenceChanged(sharedPreferences, key)`.
- Registering in `onResume()` and unregistering in `onPause()` ensures the listener is only active while the fragment is visible. Failing to unregister causes a memory leak because `SharedPreferences` retains a reference to the listener.

**Common mistake:** Registering in `onCreate()` but forgetting to unregister, or unregistering in `onDestroy()` instead of `onPause()` (which still allows leaks if the fragment pauses without being destroyed).

---

### Part B — Multiple Choice

---

**Question 4 — Answer: C) `MultiSelectListPreference`**

`MultiSelectListPreference` allows the user to tick multiple items from a list. The selected values are stored together as a `Set<String>` in `SharedPreferences`.

- `ListPreference` — single selection only.
- `SwitchPreference` — a simple on/off boolean toggle.
- `EditTextPreference` — free-form text input via a dialog.

---

**Question 5 — Answer: B) `entries` are the human-readable labels; `entryValues` are what is stored**

`entries` are the display strings shown in the dialog (e.g. `"Vegetarian"`). `entryValues` are the corresponding machine-readable values written to `SharedPreferences` (e.g. `"vegetarian"`). These two arrays must be the same length and correspond index-by-index.

**Common mistake:** Assuming `entries` and `entryValues` must contain the same strings. They can differ — and often should, so that the stored value is suitable for use in code without being tied to the display language.

---

**Question 6 — Answer: B) `SharedPreferences` holds only a weak reference; an anonymous object may be garbage collected**

`SharedPreferences` stores listener references weakly, meaning the garbage collector can reclaim an anonymous object if nothing else holds a strong reference to it. The result is that `onSharedPreferenceChanged()` silently stops firing. The fix is to store the listener as a property of the class (or to have the class implement the interface directly, as shown in the lecture).

---

## Task Answers

### Task 1 — Reading Fitness App Preferences

**Completed code:**

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

        // Read "daily_goal" as a String with default "10000"
        val dailyGoal: String? = prefs.getString("daily_goal", "10000")

        // Read "notifications_enabled" as a Boolean with default true
        val notificationsEnabled: Boolean = prefs.getBoolean("notifications_enabled", true)

        // Read "active_days" as a Set<String> with default null
        val activeDays: Set<String>? = prefs.getStringSet("active_days", null)

        Log.d("Fitness", "Daily step goal: $dailyGoal")
        Log.d("Fitness", "Notifications: ${if (notificationsEnabled) "enabled" else "disabled"}")
        Log.d("Fitness", "Active days: ${activeDays?.joinToString() ?: "None selected"}")
    }
}
```

**Blank answers (in order):**

| Blank | Answer |
|-------|--------|
| 1 | `prefs.getString("daily_goal", "10000")` |
| 2 | `prefs.getBoolean("notifications_enabled", true)` |
| 3 | `prefs.getStringSet("active_days", null)` |
| 4 | `notificationsEnabled` |

**Explanation:**
- Each getter method corresponds to the type stored: `getString`, `getBoolean`, `getStringSet`. Using the wrong getter (e.g. `getString` for a boolean key) will return the default value rather than causing a crash, which can introduce subtle bugs.
- `getStringSet` returns `null` when the key is absent if `null` is given as the default — this is a valid approach for "nothing selected yet."
- The `if (notificationsEnabled)` expression evaluates the `Boolean` directly (not as `Boolean?`) because `getBoolean` returns a non-nullable `Boolean`.

**Common mistake:** Using `prefs.getBoolean("notifications_enabled", false)` with the wrong default. Since the intended default is `true`, using `false` here would change the app's behaviour on first launch.

---

### Task 2 — Live Summary Updates

**Completed code:**

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
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .registerOnSharedPreferenceChangeListener(this)
    }

    override fun onPause() {
        super.onPause()
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .unregisterOnSharedPreferenceChangeListener(this)
    }

    override fun onSharedPreferenceChanged(sharedPreferences: SharedPreferences?, key: String?) {
        if (key == "measurement_unit") {
            val pref = findPreference<ListPreference>("measurement_unit")
            pref?.summary = pref?.entry
        }
    }
}
```

**Blank answers (in order):**

| Blank | Answer |
|-------|--------|
| 1 | `registerOnSharedPreferenceChangeListener` |
| 2 | `unregisterOnSharedPreferenceChangeListener` |
| 3 | `ListPreference` |
| 4 | `pref?.entry` |

**Explanation:**
- `findPreference<ListPreference>("measurement_unit")` returns the `ListPreference` object from the preference hierarchy. The type parameter (`ListPreference`) is required so the result is correctly typed — without it, the return type is the base `Preference` class, which has no `.entry` property.
- `pref?.entry` returns the human-readable display label corresponding to the currently stored value. This is the key difference from `pref?.value`, which returns the raw stored value (e.g. `"miles"` vs. `"Miles"`).
- The safe-call operator (`?.`) is required throughout because `findPreference` returns a nullable type.

**Common mistake:** Using `pref?.value` instead of `pref?.entry`. The `value` property holds the stored machine-readable string (e.g. `"km"`), not the label shown to the user (e.g. `"Kilometres"`). Using `value` for the summary technically works but shows the raw stored value rather than the friendly label.

---

### Challenge Task — Programmatic Preference Reset

**Completed code:**

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
            resetPreferences()
        }
    }

    private fun resetPreferences() {
        val prefs = PreferenceManager.getDefaultSharedPreferences(this)

        // Obtain a SharedPreferences editor
        val editor = prefs.edit()

        // Write each preference back to its default value
        editor.putString("theme",     "light")
        editor.putString("font_size", "medium")
        editor.putBoolean("show_tips", true)

        // Persist the changes asynchronously
        editor.apply()

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

**Blank answers (in order):**

| Blank | Answer |
|-------|--------|
| 1 | `resetPreferences()` |
| 2 | `prefs.edit()` |
| 3 | `putString(` |
| 4 | `"light"` |
| 5 | `putString(` |
| 6 | `"medium"` |
| 7 | `putBoolean(` |
| 8 | `true` |
| 9 | `editor.apply()` |

**Explanation:**
- `prefs.edit()` returns a `SharedPreferences.Editor` object. All `put...()` calls are made on the editor — they do not take effect until `apply()` or `commit()` is called.
- The method called to write the value must match the type: `putString()` for `String` and `putBoolean()` for `Boolean`. Mixing them causes a `ClassCastException` when the value is later read back.
- Calling `apply()` queues the write on a background thread and returns immediately. The subsequent `prefs.getString(...)` calls read from the in-memory cache, which is updated before `apply()` returns, so the logged values are correct even though the disk write may still be in progress.
- Calling `resetPreferences()` from the click listener (rather than inlining the logic) keeps `onCreate` clean and makes the reset logic easy to test or reuse.

**Common mistake:** Forgetting to call `apply()` or `commit()` after the `put...()` calls. Without this, the editor's changes are discarded and the preferences remain unchanged.

**Extension idea:** Modify `resetPreferences()` to accept a `Map<String, Any>` of key–default pairs, making it reusable for resetting any combination of preferences without duplicating the editor logic.
