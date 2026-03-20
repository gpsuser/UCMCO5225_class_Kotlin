# Android Preferences

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [Overview](#overview)
- [Defining Preferences in XML](#defining-preferences-in-xml)
- [Preference Entry Attributes](#preference-entry-attributes)
- [Displaying Preferences to the User](#displaying-preferences-to-the-user)
- [Reading Preferences in Code](#reading-preferences-in-code)
- [Listening for Preference Changes](#listening-for-preference-changes)
- [Handling Preference Changes](#handling-preference-changes)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

1. Explain what Android's Preference framework is and why it is used.
2. Define a `PreferenceScreen` in XML using a variety of preference types.
3. Display a settings screen to users using `PreferenceFragmentCompat`.
4. Read stored preference values using `SharedPreferences` from any part of an app.
5. Register a listener to respond dynamically when the user changes a preference.
6. Handle preference changes by retrieving updated values and updating app behaviour accordingly.

---

## Overview

[↑ Back to Table of Contents](#table-of-contents)

Almost every app you use on your phone has a settings screen — a place where the user can configure how the app behaves. Android provides a dedicated framework to build exactly this kind of screen: the **Preferences API**.

Rather than building a custom UI for settings from scratch (with checkboxes, text fields, and radio buttons wired up manually), Android lets you:

- **Declare** what settings exist in an XML file.
- **Display** them automatically using a built-in fragment.
- **Store** and **retrieve** the values via `SharedPreferences` — a lightweight key-value store that persists across app restarts.

This approach keeps settings screens consistent, accessible, and easy to maintain.

```
+-----------------------------------+
|        App Settings Screen        |
|-----------------------------------|
|  [>] Notifications                |
|      Enable push alerts     [ON]  |
|                                   |
|  [>] Dietary Preferences          |
|      Omnivore            (o)      |
|      Vegetarian          ( )      |
|      Vegan               ( )      |
|                                   |
|  [>] Display                      |
|      Dark Mode              [OFF] |
+-----------------------------------+
```

The Preferences framework is built on top of `SharedPreferences`, which stores simple key-value pairs (booleans, strings, integers, etc.) in an XML file on the device. When the user changes a setting, `SharedPreferences` is updated automatically — no additional code required for saving.

---

## Defining Preferences in XML

[↑ Back to Table of Contents](#table-of-contents)

Preferences are defined in an XML resource file, not a layout file. This file lives in `res/xml/` (you may need to create this directory in your project).

The root element is `PreferenceScreen`, and inside it you place individual preference elements.

### Available Preference Types

| Preference Element    | Purpose                                              |
|-----------------------|------------------------------------------------------|
| `CheckBoxPreference`  | A simple on/off toggle using a checkbox              |
| `SwitchPreference`    | An on/off toggle using a material switch             |
| `ListPreference`      | A single-choice list shown in a dialog               |
| `MultiSelectListPreference` | A multi-choice list (stores a `Set<String>`) |
| `EditTextPreference`  | A free-text input shown in a dialog                  |

### Grouping and Nesting

- Use `<PreferenceCategory>` to visually group related preferences under a heading.
- Use a nested `<androidx.preference.PreferenceScreen>` element to link to a sub-screen of preferences.

### Example XML Preference File

Below is a complete example of `res/xml/preferences.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.preference.PreferenceScreen
    xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- Category groups related preferences under a label -->
    <PreferenceCategory android:title="Billing">

        <!-- SwitchPreference: toggles service charge on/off -->
        <SwitchPreference
            android:key="include_service"
            android:title="Include Service Charge in Bill"
            android:summary="Adds a 10% service charge to the total"
            android:defaultValue="true" />

    </PreferenceCategory>

    <PreferenceCategory android:title="Dietary">

        <!-- ListPreference: single choice from a list of options -->
        <ListPreference
            android:key="dietary_preference"
            android:title="Dietary preferences"
            android:summary="Select your preferred diet choice"
            android:entries="@array/diet_entries"
            android:entryValues="@array/diet_values"
            android:defaultValue="omnivore" />

        <!-- MultiSelectListPreference: multiple choices (stored as Set<String>) -->
        <MultiSelectListPreference
            android:key="allergies"
            android:title="Allergies"
            android:summary="Select any allergies that apply"
            android:entries="@array/allergy_entries"
            android:entryValues="@array/allergy_values" />

    </PreferenceCategory>

    <PreferenceCategory android:title="Display">

        <!-- CheckBoxPreference: another style of boolean toggle -->
        <CheckBoxPreference
            android:key="dark_mode"
            android:title="Dark Mode"
            android:summary="Use dark theme throughout the app"
            android:defaultValue="false" />

        <!-- EditTextPreference: free text input -->
        <EditTextPreference
            android:key="username"
            android:title="Display Name"
            android:summary="Set your name as shown in the app" />

    </PreferenceCategory>

</androidx.preference.PreferenceScreen>
```

The `entries` and `entryValues` for `ListPreference` and `MultiSelectListPreference` come from string arrays defined in `res/values/arrays.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="diet_entries">
        <item>Omnivore</item>
        <item>Pescatarian</item>
        <item>Vegetarian</item>
        <item>Vegan</item>
    </string-array>

    <string-array name="diet_values">
        <item>omnivore</item>
        <item>pescatarian</item>
        <item>vegetarian</item>
        <item>vegan</item>
    </string-array>

    <string-array name="allergy_entries">
        <item>Gluten</item>
        <item>Nuts</item>
        <item>Dairy</item>
    </string-array>

    <string-array name="allergy_values">
        <item>gluten</item>
        <item>nuts</item>
        <item>dairy</item>
    </string-array>
</resources>
```

> **Key point:** `entries` are the human-readable labels shown to the user. `entryValues` are the machine-readable values actually stored in `SharedPreferences`. These do not have to match.

---

## Preference Entry Attributes

[↑ Back to Table of Contents](#table-of-contents)

Every preference element shares a common set of attributes that control its behaviour and appearance.

| Attribute              | Purpose                                                                 |
|------------------------|-------------------------------------------------------------------------|
| `android:key`          | **Required.** A unique string identifier used to retrieve the value in code. |
| `android:title`        | The main label displayed to the user (should be a string resource).     |
| `android:summary`      | A subtitle giving more detail (shown below the title).                  |
| `android:defaultValue` | The value used when the preference has never been set by the user.      |

### Why Keys Matter

The `key` attribute is the bridge between your XML definition and your Kotlin code. When you want to read or respond to a preference, you look it up by its key — just like looking up a value in a `Map`.

```
XML Definition                  Kotlin Code
--------------                  -----------
android:key="include_service"   prefs.getBoolean("include_service", false)
                                          ^
                                    same string
```

### Example: A Single SwitchPreference

```xml
<SwitchPreference
    android:key="include_service"
    android:title="Include Service charge in bill"
    android:defaultValue="true" />
```

- **Key:** `"include_service"` — used when reading the value in Kotlin.
- **Default value:** `true` — the switch is on until the user turns it off.
- **No summary** is set here, but it is recommended for clarity.

> **Best practice:** Define `title` and `summary` as string resources (`@string/...`) rather than hardcoded strings. This makes it easy to support multiple languages later.

---

## Displaying Preferences to the User

[↑ Back to Table of Contents](#table-of-contents)

To display your XML preferences on screen, you need two things:

1. A **Fragment** that extends `PreferenceFragmentCompat` and loads the XML.
2. An **Activity** that hosts and displays that Fragment.

### Step 1 — Add the Dependency

First, add the AndroidX Preference library to your module's `build.gradle` file:

```groovy
// In app/build.gradle (Groovy DSL)
dependencies {
    implementation 'androidx.preference:preference:1.2.1'
}
```

Or in `build.gradle.kts` (Kotlin DSL):

```kotlin
dependencies {
    implementation("androidx.preference:preference:1.2.1")
}
```

### Step 2 — Create the PreferenceFragment

Create a Kotlin class (no XML layout needed) that extends `PreferenceFragmentCompat` and overrides `onCreatePreferences` to load your XML:

```kotlin
// PreferencesFragment.kt

import android.os.Bundle
import androidx.preference.PreferenceFragmentCompat

// Extends PreferenceFragmentCompat - handles rendering the preferences for us
class PreferencesFragment : PreferenceFragmentCompat() {

    // Called when the fragment needs to load its preferences
    // savedInstanceState: any saved state from a previous session
    // rootKey: used when navigating sub-screens (null for the root screen)
    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        // Load the preference hierarchy from the XML file
        // R.xml.preferences refers to res/xml/preferences.xml
        setPreferencesFromResource(R.xml.preferences, rootKey)
    }
}
```

### Step 3 — Create the SettingsActivity

Create an Activity to host the Fragment. No XML layout file is needed — the Fragment fills the standard `android.R.id.content` container:

```kotlin
// SettingsActivity.kt

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class SettingsActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Use the Fragment Manager to place our PreferencesFragment
        // into the default content area of the Activity
        supportFragmentManager
            .beginTransaction()
            .replace(android.R.id.content, PreferencesFragment())
            .commit()
    }
}
```

### Step 4 — Declare the Activity in AndroidManifest.xml

```xml
<activity android:name=".SettingsActivity"
          android:label="Settings" />
```

### Step 5 — Launch the Settings Screen

From your main Activity, launch `SettingsActivity` like any other:

```kotlin
// From MainActivity.kt
val intent = Intent(this, SettingsActivity::class.java)
startActivity(intent)
```

### What it Looks Like

```
+-----------------------------------+
|  <-- Settings                     |
|-----------------------------------|
|  BILLING                          |
|                                   |
|  Include Service Charge in Bill   |
|  Adds a 10% service charge        |  [ON]
|                                   |
|  DIETARY                          |
|                                   |
|  Dietary preferences              |
|  Select your preferred diet       |
|                                   |
|  Allergies                        |
|  Select any allergies             |
|                                   |
|  DISPLAY                          |
|                                   |
|  Dark Mode                        |
|  Use dark theme throughout   [  ] |
+-----------------------------------+
```

The Preference framework takes care of all the UI rendering, dialog presentation, and persistence automatically.

---

## Reading Preferences in Code

[↑ Back to Table of Contents](#table-of-contents)

Once the user has set their preferences, you can read those values from anywhere in your app using `SharedPreferences`. The values persist even after the app is closed and reopened.

### Getting the SharedPreferences Instance

Use `PreferenceManager.getDefaultSharedPreferences()` to get a reference to the same `SharedPreferences` file that the Preference framework writes to:

```kotlin
import android.content.Context
import androidx.preference.PreferenceManager

// context is typically 'this' from an Activity, or requireContext() from a Fragment
val prefs = PreferenceManager.getDefaultSharedPreferences(context)
```

### Reading Values

Call the appropriate typed getter, passing in the key and a fallback default value:

```kotlin
// Read a Boolean (e.g. from a SwitchPreference or CheckBoxPreference)
val includeService: Boolean = prefs.getBoolean("include_service", false)

// Read a String (e.g. from a ListPreference or EditTextPreference)
val diet: String? = prefs.getString("dietary_preference", "omnivore")

// Read a Set<String> (e.g. from a MultiSelectListPreference)
val allergies: Set<String>? = prefs.getStringSet("allergies", null)

// Read an Int (if you stored one manually)
val tipPercent: Int = prefs.getInt("tip_percentage", 15)
```

The second argument is the **default value** returned if the key has never been set (e.g. the user has not visited the settings screen yet).

### Complete Example — Reading Preferences in an Activity

```kotlin
// BillingActivity.kt

import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import androidx.preference.PreferenceManager

class BillingActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_billing)

        // Retrieve the SharedPreferences instance
        val prefs = PreferenceManager.getDefaultSharedPreferences(this)

        // Read the service charge toggle
        val includeService: Boolean = prefs.getBoolean("include_service", false)

        // Read the selected dietary preference
        val diet: String? = prefs.getString("dietary_preference", "omnivore")

        // Read the set of selected allergies (null if never set)
        val allergies: Set<String>? = prefs.getStringSet("allergies", null)

        // Use the values to drive app behaviour
        if (includeService) {
            Log.d("Billing", "Service charge will be added to the bill")
            // e.g. billTotal *= 1.10
        }

        Log.d("Billing", "Dietary preference: $diet")
        Log.d("Billing", "Allergies: ${allergies?.joinToString() ?: "None"}")
    }
}
```

**Sample output (Logcat):**
```
D/Billing: Service charge will be added to the bill
D/Billing: Dietary preference: vegetarian
D/Billing: Allergies: gluten, dairy
```

### Writing Preferences Programmatically

You can also write values to `SharedPreferences` manually using an editor. This is less common for user-facing settings (since the Preference framework handles that), but useful for storing app-generated data:

```kotlin
// Get the editor from the SharedPreferences instance
val editor = prefs.edit()

// Put values by key
editor.putBoolean("include_service", false)
editor.putString("dietary_preference", "vegan")

// apply() saves asynchronously (preferred over commit() for performance)
editor.apply()
```

> **`apply()` vs `commit()`:** Use `apply()` in most cases — it saves asynchronously on a background thread. Use `commit()` only when you need to know immediately whether the save succeeded (it returns a `Boolean` and blocks the calling thread).

---

## Listening for Preference Changes

[↑ Back to Table of Contents](#table-of-contents)

Reading preferences in `onCreate` is fine for initial setup, but what if the user navigates to Settings, changes a value, and then navigates back? You need a way to be notified in real time.

Android provides `SharedPreferences.OnSharedPreferenceChangeListener` for exactly this purpose.

### How it Works

When any preference value changes, the listener's `onSharedPreferenceChanged()` callback is invoked with:
- The `SharedPreferences` object containing the updated data.
- The `key` of the preference that changed.

### Two Ways to Implement the Listener

**Option A — Make Your Fragment Implement the Interface**

The most common approach is to have your `PreferencesFragment` implement the listener directly. This keeps the logic close to where preferences are displayed.

```
PreferencesFragment
       |
       |-- implements --> SharedPreferences.OnSharedPreferenceChangeListener
       |                         |
       |                   onSharedPreferenceChanged(prefs, key)
```

**Option B — Create a Separate Listener Object**

You can create a dedicated class that implements the interface. This is useful if you want to observe preference changes from an Activity or other component.

> **Important:** Do **not** use an anonymous object (a lambda or object expression) as your listener. `PreferenceManager` holds only a weak reference to listeners, so an anonymous object with no other reference will be garbage collected almost immediately and your callbacks will stop firing.

```kotlin
// WRONG - anonymous object is not retained
prefs.registerOnSharedPreferenceChangeListener { _, key ->
    // This may never be called!
}

// CORRECT - store the listener as a property so it is retained
val listener = SharedPreferences.OnSharedPreferenceChangeListener { prefs, key ->
    // handle change
}
prefs.registerOnSharedPreferenceChangeListener(listener)
```

### Registering and Unregistering

You must **register** the listener when the component becomes active and **unregister** it when it becomes inactive. In a Fragment, the right lifecycle methods are `onResume()` and `onPause()`:

```kotlin
// PreferencesFragment.kt (with listener registration)

import android.content.SharedPreferences
import android.os.Bundle
import androidx.preference.PreferenceFragmentCompat
import androidx.preference.PreferenceManager

class PreferencesFragment : PreferenceFragmentCompat(),
    SharedPreferences.OnSharedPreferenceChangeListener {

    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        setPreferencesFromResource(R.xml.preferences, rootKey)
    }

    override fun onResume() {
        super.onResume()
        // 'this' Fragment implements the listener interface - safe to register
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .registerOnSharedPreferenceChangeListener(this)
    }

    override fun onPause() {
        super.onPause()
        // Always unregister to avoid memory leaks
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .unregisterOnSharedPreferenceChangeListener(this)
    }

    // Called whenever any preference value changes
    override fun onSharedPreferenceChanged(sharedPreferences: SharedPreferences?, key: String?) {
        // Handle the change — see next section
    }
}
```

---

## Handling Preference Changes

[↑ Back to Table of Contents](#table-of-contents)

Once your listener is registered, `onSharedPreferenceChanged()` will be called each time a preference value changes. Inside this callback, you use the `key` parameter to identify *which* preference changed, then retrieve its new value.

### Approach 1 — Read from SharedPreferences by Key

The most straightforward approach: use the key to look up the new value directly from the `SharedPreferences` object passed into the callback.

```kotlin
override fun onSharedPreferenceChanged(sharedPreferences: SharedPreferences?, key: String?) {

    when (key) {

        "include_service" -> {
            // sharedPreferences is nullable, so use safe-call ?.
            val includeService = sharedPreferences?.getBoolean("include_service", false)

            // includeService is nullable (Boolean?) due to safe-call
            // Use equality check rather than direct if(includeService)
            if (includeService == true) {
                // Apply service charge to bill total
                Log.d("Prefs", "Service charge enabled")
            } else {
                Log.d("Prefs", "Service charge disabled")
            }
        }

        "dietary_preference" -> {
            val diet = sharedPreferences?.getString("dietary_preference", "omnivore")
            Log.d("Prefs", "Dietary preference changed to: $diet")
        }

        "allergies" -> {
            val allergies = sharedPreferences?.getStringSet("allergies", null)
            Log.d("Prefs", "Allergies updated: ${allergies?.joinToString() ?: "None"}")
        }
    }
}
```

**Sample output (Logcat) when user toggles service charge on:**
```
D/Prefs: Service charge enabled
```

### Approach 2 — Access the Preference Object Directly with findPreference()

If you need to interact with the preference widget itself (e.g. to update its summary, or read its state), use `findPreference()` from within your `PreferenceFragmentCompat`. The result must be cast to the appropriate preference type.

```kotlin
override fun onSharedPreferenceChanged(sharedPreferences: SharedPreferences?, key: String?) {

    if (key == "include_service") {
        // findPreference returns Preference? - cast to SwitchPreference
        val pref = findPreference<SwitchPreference>(key)

        if (pref != null && pref.isChecked) {
            // The switch is currently on - apply service charge
            pref.summary = "Service charge of 10% will be added"
            // ... update bill total
        } else {
            pref?.summary = "No service charge applied"
        }
    }

    if (key == "dietary_preference") {
        // Cast to ListPreference to access its entry (the human-readable label)
        val listPref = findPreference<ListPreference>(key)
        // Update the summary to reflect the current selection
        listPref?.summary = listPref?.entry  // e.g. "Vegetarian"
    }
}
```

### Complete Example — Preferences Fragment with Full Change Handling

```kotlin
// PreferencesFragment.kt

import android.content.SharedPreferences
import android.os.Bundle
import android.util.Log
import androidx.preference.ListPreference
import androidx.preference.PreferenceFragmentCompat
import androidx.preference.PreferenceManager
import androidx.preference.SwitchPreference

class PreferencesFragment : PreferenceFragmentCompat(),
    SharedPreferences.OnSharedPreferenceChangeListener {

    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        // Load the preference hierarchy from res/xml/preferences.xml
        setPreferencesFromResource(R.xml.preferences, rootKey)
    }

    override fun onResume() {
        super.onResume()
        // Register this fragment as a listener when it is visible
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .registerOnSharedPreferenceChangeListener(this)
    }

    override fun onPause() {
        super.onPause()
        // Unregister when the fragment is no longer visible to avoid leaks
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .unregisterOnSharedPreferenceChangeListener(this)
    }

    // Invoked automatically whenever any preference value changes
    override fun onSharedPreferenceChanged(sharedPreferences: SharedPreferences?, key: String?) {

        when (key) {

            // Handle service charge toggle
            "include_service" -> {
                val switchPref = findPreference<SwitchPreference>("include_service")
                if (switchPref?.isChecked == true) {
                    Log.d("Prefs", "Service charge: ENABLED")
                    switchPref.summary = "10% service charge will be added"
                } else {
                    Log.d("Prefs", "Service charge: DISABLED")
                    switchPref?.summary = "No service charge"
                }
            }

            // Handle dietary preference selection
            "dietary_preference" -> {
                // ListPreference stores the value, but .entry gives the display label
                val listPref = findPreference<ListPreference>("dietary_preference")
                Log.d("Prefs", "Diet changed to: ${listPref?.value}")
                // Reflect the current choice in the summary line
                listPref?.summary = listPref?.entry ?: "Not set"
            }

            // Handle allergy selection (multi-select returns a Set)
            "allergies" -> {
                val selected = sharedPreferences?.getStringSet("allergies", emptySet())
                Log.d("Prefs", "Allergies: ${selected?.joinToString() ?: "None"}")
            }
        }
    }
}
```

**Sample Logcat output as the user interacts with settings:**
```
D/Prefs: Service charge: ENABLED
D/Prefs: Diet changed to: vegetarian
D/Prefs: Allergies: gluten, nuts
D/Prefs: Service charge: DISABLED
```

### Data Flow Summary

```
User interacts with        SharedPreferences     onSharedPreferenceChanged()
preference widget      --> file updated       --> callback fires in listener
      |                         |                          |
 (Switch toggled)        key="include_service"      key = "include_service"
                         value=true                  read new value & react
```

The key insight is that **you never need to manually save the value** — the Preference framework does that for you when the user interacts with a preference widget. Your job in `onSharedPreferenceChanged` is simply to *react* to the change.
