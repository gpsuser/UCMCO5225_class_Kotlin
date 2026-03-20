---
marp: true
theme: default
paginate: true
footer: "Android Preferences"
style: |
  /* ── Global ── */
  section {
    font-family: 'Trebuchet MS', 'Segoe UI', sans-serif;
    background: #f4f7f9;
    color: #1a2b3c;
    font-size: 18px;
    padding: 48px 60px 60px 60px;
  }
  h1 {
    font-family: 'Trebuchet MS', sans-serif;
    font-size: 40px;
    font-weight: 700;
    color: #028090;
    margin-bottom: 10px;
  }
  h2 {
    font-size: 24px;
    font-weight: 700;
    color: #028090;
    margin-top: 0;
    margin-bottom: 8px;
  }
  h3 {
    font-size: 20px;
    color: #02C39A;
    margin-top: 12px;
    margin-bottom: 4px;
  }
  code {
    background: #e2f0f5;
    color: #065A82;
    padding: 2px 6px;
    border-radius: 4px;
    font-size: 15px;
  }
  pre {
    background: #1a2b3c;
    color: #e0f4f8;
    border-radius: 8px;
    padding: 18px 20px;
    font-size: 13.5px;
    line-height: 1.5;
    overflow: hidden;
  }
  pre code {
    background: transparent;
    color: #e0f4f8;
    padding: 0;
    font-size: 13.5px;
  }
  table {
    border-collapse: collapse;
    width: 100%;
    font-size: 15px;
  }
  th {
    background: #028090;
    color: white;
    padding: 8px 12px;
    text-align: left;
  }
  td {
    padding: 7px 12px;
    border-bottom: 1px solid #cde3eb;
  }
  tr:nth-child(even) td { background: #e8f5f8; }
  ul, ol { padding-left: 24px; line-height: 1.8; }
  li { margin-bottom: 4px; }
  strong { color: #028090; }
  blockquote {
    border-left: 4px solid #02C39A;
    background: #e2f8f4;
    padding: 10px 16px;
    margin: 12px 0;
    border-radius: 0 6px 6px 0;
    font-style: normal;
    color: #1a2b3c;
  }

  /* ── Footer & Page Number ── */
  footer {
    font-size: 13px;
    color: #028090;
    font-weight: 600;
    letter-spacing: 0.5px;
    bottom: 20px;
    left: 60px;
  }
  section::after {
    font-size: 13px;
    color: #028090;
    font-weight: 600;
    bottom: 20px;
    right: 60px;
  }

  /* ── Title slide ── */
  section.title {
    background: #065A82;
    color: #e0f4f8;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 60px 72px;
  }
  section.title h1 {
    font-size: 54px;
    color: #ffffff;
    margin-bottom: 12px;
  }
  section.title h2 {
    font-size: 22px;
    color: #9adce8;
    font-weight: 400;
  }
  section.title footer { color: #9adce8; }
  section.title::after { color: #9adce8; }

  /* ── Section divider slides ── */
  section.divider {
    background: #028090;
    color: #ffffff;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 60px 72px;
  }
  section.divider h1 {
    font-size: 48px;
    color: #ffffff;
  }
  section.divider p {
    font-size: 20px;
    color: #c8eef4;
    margin-top: 8px;
  }
  section.divider footer { color: #c8eef4; }
  section.divider::after { color: #c8eef4; }

  /* ── Conclusion slide ── */
  section.conclusion {
    background: #1a2b3c;
    color: #e0f4f8;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 60px 72px;
  }
  section.conclusion h1 { font-size: 44px; color: #02C39A; }
  section.conclusion ul { color: #c8eef4; }
  section.conclusion footer { color: #9adce8; }
  section.conclusion::after { color: #9adce8; }

  /* ── Two-column helper ── */
  .cols {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 28px;
    margin-top: 12px;
  }
  .col {
    background: #e8f5f8;
    border-radius: 8px;
    padding: 16px 20px;
  }
  .col h3 { margin-top: 0; }

  /* ── Callout box ── */
  .callout {
    background: #02C39A;
    color: #fff;
    border-radius: 8px;
    padding: 14px 20px;
    margin-top: 14px;
    font-size: 16px;
    font-weight: 600;
  }

  /* ── Key-value badge ── */
  .badge {
    display: inline-block;
    background: #065A82;
    color: #fff;
    border-radius: 4px;
    padding: 2px 10px;
    font-size: 14px;
    font-family: monospace;
  }

  /* ── Intro box ── */
  .intro-box {
    background: #e2f0f5;
    border-radius: 10px;
    padding: 20px 28px;
    margin-top: 16px;
  }
---

<!-- _class: title -->

# Android Preferences

## 2026-03

---

<!-- Slide 2: Table of Contents -->

# Contents

| # | Topic | Learning Outcomes |
|---|-------|-------------------|
| 1 | Introduction | LO 1 |
| 2 | Defining Preferences in XML | LO 2 |
| 3 | Preference Entry Attributes | LO 2 |
| 4 | Displaying Preferences to the User | LO 3 |
| 5 | Reading Preferences in Code | LO 4 |
| 6 | Listening for Preference Changes | LO 5 |
| 7 | Handling Preference Changes | LO 6 |

---

<!-- Slide 3: Learning Outcomes -->

# Learning Outcomes

By the end of this lecture you will be able to:

1. Explain what Android's Preference framework is and why it is used
2. Define a `PreferenceScreen` in XML using a variety of preference types
3. Display a settings screen to users using `PreferenceFragmentCompat`
4. Read stored preference values using `SharedPreferences` from any part of an app
5. Register a listener to respond dynamically when the user changes a preference
6. Handle preference changes by retrieving updated values and updating app behaviour

---

<!-- Slide 4: Introduction -->

# Introduction

Almost every app has a settings screen — Android provides a dedicated framework to build one.

<div class="cols">
<div class="col">

### Without the Framework
- Build custom UI by hand
- Wire up checkboxes, inputs, radio buttons manually
- Write your own persistence logic

</div>
<div class="col">

### With the Framework
- **Declare** settings in XML
- **Display** them via a built-in fragment
- **Store / retrieve** values automatically via `SharedPreferences`

</div>
</div>

<div class="callout">
Consistent ✦ Accessible ✦ Maintainable — no boilerplate required
</div>

---

<!-- Slide 5: Preferences Framework Overview -->

# The Preferences Framework

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
|                                   |
|  [>] Display                      |
|      Dark Mode              [OFF] |
+-----------------------------------+
```

> **Key insight:** The Preference framework is built on top of `SharedPreferences` — a lightweight key-value store that persists data across app restarts. When the user changes a setting, `SharedPreferences` is updated **automatically**.

---

<!-- _class: divider -->

# Defining Preferences in XML

Declaring your settings as structured XML resources

---

<!-- Slide 7: Preference Types -->

# Preference Types

Preferences are declared in `res/xml/preferences.xml` — not a layout file.

The root element is `PreferenceScreen`. Inside it, place individual preference elements:

| Element | Purpose |
|---------|---------|
| `CheckBoxPreference` | On/off toggle — checkbox style |
| `SwitchPreference` | On/off toggle — material switch style |
| `ListPreference` | Single choice from a dialog list |
| `MultiSelectListPreference` | Multiple choices — stores a `Set<String>` |
| `EditTextPreference` | Free-text input shown in a dialog |

---

<!-- Slide 8: Grouping and Nesting -->

# Grouping and Nesting

<div class="cols">
<div class="col">

### PreferenceCategory
Groups related preferences under a visible heading

```xml
<PreferenceCategory
    android:title="Billing">

    <SwitchPreference ... />

</PreferenceCategory>
```

</div>
<div class="col">

### Nested PreferenceScreen
Links to a sub-screen of preferences

```xml
<androidx.preference
  .PreferenceScreen
    android:title="Advanced">

    <!-- sub-screen items -->

</androidx.preference
  .PreferenceScreen>
```

</div>
</div>

---

<!-- Slide 9: XML Example -->

# XML Preference File Example

```xml
<!-- res/xml/preferences.xml -->
<androidx.preference.PreferenceScreen
    xmlns:android="http://schemas.android.com/apk/res/android">

    <PreferenceCategory android:title="Billing">
        <SwitchPreference
            android:key="include_service"
            android:title="Include Service Charge"
            android:defaultValue="true" />
    </PreferenceCategory>

    <PreferenceCategory android:title="Dietary">
        <ListPreference
            android:key="dietary_preference"
            android:title="Dietary Preferences"
            android:entries="@array/diet_entries"
            android:entryValues="@array/diet_values"
            android:defaultValue="omnivore" />
    </PreferenceCategory>

</androidx.preference.PreferenceScreen>
```

> `entries` = human-readable labels shown to the user. `entryValues` = machine-readable values stored in `SharedPreferences`. They don't have to match.

---

<!-- _class: divider -->

# Preference Entry Attributes

The common attributes that control every preference element

---

<!-- Slide 11: Preference Entry Attributes -->

# Preference Entry Attributes

Every preference element shares these common attributes:

| Attribute | Purpose |
|-----------|---------|
| `android:key` | **Required.** Unique ID — used to retrieve the value in code |
| `android:title` | Main label shown to the user |
| `android:summary` | Subtitle giving more detail (shown below the title) |
| `android:defaultValue` | Value used when the preference has never been set |

> **Best practice:** Define `title` and `summary` as string resources (`@string/...`) rather than hardcoded strings — makes localisation straightforward.

---

<!-- Slide 12: Why Keys Matter -->

# Why Keys Matter

The `key` attribute is the bridge between your XML definition and your Kotlin code.

```
XML Definition                    Kotlin Code
--------------                    -----------
android:key="include_service"     prefs.getBoolean("include_service", false)
                                            ^
                                      same string key
```

### Example

```xml
<SwitchPreference
    android:key="include_service"
    android:title="Include Service Charge in Bill"
    android:defaultValue="true" />
```

- **Key:** `"include_service"` — used to read the value in Kotlin
- **Default value:** `true` — switch is on until the user turns it off

---

<!-- _class: divider -->

# Displaying Preferences to the User

Hosting your settings screen in a Fragment and Activity

---

<!-- Slide 14: 5-Step Overview -->

# Displaying Preferences — Overview

Five steps to get a working settings screen:

```
  Step 1            Step 2              Step 3
  -------           -------             -------
  Add the    -->   Create the   -->   Create the
  dependency       Fragment           Activity

  Step 4            Step 5
  -------           -------
  Declare in  -->  Launch from
  Manifest         Main Activity
```

---

<!-- Slide 15: Step 1 + Step 2 -->

# Steps 1 & 2 — Dependency & Fragment

### Step 1: Add the AndroidX Preference library

```kotlin
// build.gradle.kts
dependencies {
    implementation("androidx.preference:preference:1.2.1")
}
```

### Step 2: Create the PreferenceFragment

```kotlin
// PreferencesFragment.kt
class PreferencesFragment : PreferenceFragmentCompat() {

    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        // Load from res/xml/preferences.xml
        setPreferencesFromResource(R.xml.preferences, rootKey)
    }
}
```

No XML layout file needed — the framework renders everything automatically.

---

<!-- Slide 16: Step 3 — SettingsActivity -->

# Step 3 — SettingsActivity

Create an Activity to host the Fragment. No layout XML is needed — the Fragment fills `android.R.id.content`.

```kotlin
// SettingsActivity.kt
class SettingsActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Place PreferencesFragment into the default content area
        supportFragmentManager
            .beginTransaction()
            .replace(android.R.id.content, PreferencesFragment())
            .commit()
    }
}
```

---

<!-- Slide 17: Steps 4 & 5 — Manifest & Launch -->

# Steps 4 & 5 — Manifest & Launching

### Step 4: Declare in AndroidManifest.xml

```xml
<activity android:name=".SettingsActivity"
          android:label="Settings" />
```

### Step 5: Launch from MainActivity

```kotlin
val intent = Intent(this, SettingsActivity::class.java)
startActivity(intent)
```

<div class="callout">
The Preference framework handles all UI rendering, dialog presentation, and persistence — your code does none of that manually.
</div>

---

<!-- _class: divider -->

# Reading Preferences in Code

Retrieving stored values from anywhere in your app

---

<!-- Slide 19: Getting SharedPreferences -->

# Getting SharedPreferences

Use `PreferenceManager.getDefaultSharedPreferences()` to access the same file the Preference framework writes to.

```kotlin
import androidx.preference.PreferenceManager

// 'this' in an Activity, or requireContext() in a Fragment
val prefs = PreferenceManager.getDefaultSharedPreferences(context)
```

The values persist across app restarts — no database required.

```
+-----------------+        +------------------+        +---------------+
|  Preference     |        |  SharedPreferences|        |  Your Kotlin  |
|  XML / Widget   | -----> |  key-value store  | -----> |  Code         |
|                 |  saves |  (XML on device)  | reads  |               |
+-----------------+        +------------------+        +---------------+
```

---

<!-- Slide 20: Reading Values by Type -->

# Reading Values by Type

Call the typed getter, passing the key and a fallback default:

```kotlin
// Boolean  — SwitchPreference / CheckBoxPreference
val includeService: Boolean = prefs.getBoolean("include_service", false)

// String   — ListPreference / EditTextPreference
val diet: String? = prefs.getString("dietary_preference", "omnivore")

// Set<String> — MultiSelectListPreference
val allergies: Set<String>? = prefs.getStringSet("allergies", null)

// Int  (manually stored values)
val tipPercent: Int = prefs.getInt("tip_percentage", 15)
```

> The second argument is the **default value** returned if the key has never been set (e.g. user has not yet visited the settings screen).

---

<!-- Slide 21: Writing Programmatically -->

# Writing Preferences Programmatically

Useful for storing app-generated data (the Preference framework handles user-facing saves automatically).

```kotlin
val editor = prefs.edit()

editor.putBoolean("include_service", false)
editor.putString("dietary_preference", "vegan")

// apply() saves asynchronously (preferred)
editor.apply()
```

<div class="cols">
<div class="col">

### `apply()` ✅ Preferred
- Saves asynchronously on a background thread
- Returns immediately — does not block the UI

</div>
<div class="col">

### `commit()` — Use sparingly
- Saves synchronously — blocks the calling thread
- Returns `Boolean` — useful when you need confirmation

</div>
</div>

---

<!-- _class: divider -->

# Listening for Preference Changes

Responding in real time when the user updates a setting

---

<!-- Slide 23: OnSharedPreferenceChangeListener -->

# OnSharedPreferenceChangeListener

Reading preferences in `onCreate` handles initial setup. But what if the user changes a value and navigates back?

Android provides `SharedPreferences.OnSharedPreferenceChangeListener`:

```kotlin
override fun onSharedPreferenceChanged(
    sharedPreferences: SharedPreferences?,
    key: String?
) {
    // key tells you WHICH preference changed
    // sharedPreferences lets you read the NEW value
}
```

**Two implementation options:**

1. Have your `PreferencesFragment` **implement the interface** directly *(most common)*
2. Create a **separate listener object** and store it as a property

---

<!-- Slide 24: Weak References Warning -->

# ⚠️ Weak References — A Common Pitfall

`PreferenceManager` holds only a **weak reference** to listeners. An anonymous object with no other reference will be **garbage collected almost immediately**.

```kotlin
// WRONG — anonymous object is not retained
prefs.registerOnSharedPreferenceChangeListener { _, key ->
    // This may never be called!
}

// CORRECT — store the listener as a property
val listener = SharedPreferences.OnSharedPreferenceChangeListener { prefs, key ->
    // handle change
}
prefs.registerOnSharedPreferenceChangeListener(listener)
```

> Prefer having your Fragment implement the interface — `this` is always retained as long as the Fragment is alive.

---

<!-- Slide 25: Registering & Unregistering -->

# Registering & Unregistering

Register in `onResume()` and unregister in `onPause()` to match the Fragment's active visibility.

```kotlin
class PreferencesFragment : PreferenceFragmentCompat(),
    SharedPreferences.OnSharedPreferenceChangeListener {

    override fun onResume() {
        super.onResume()
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .registerOnSharedPreferenceChangeListener(this)
    }

    override fun onPause() {
        super.onPause()
        // Always unregister — prevents memory leaks
        PreferenceManager.getDefaultSharedPreferences(requireContext())
            .unregisterOnSharedPreferenceChangeListener(this)
    }
}
```

---

<!-- _class: divider -->

# Handling Preference Changes

Using the callback to react to updated values

---

<!-- Slide 27: Approach 1 — Read by Key -->

# Approach 1 — Read from SharedPreferences by Key

Use a `when` block on the key, then retrieve the new value directly:

```kotlin
override fun onSharedPreferenceChanged(
    sharedPreferences: SharedPreferences?, key: String?) {

    when (key) {
        "include_service" -> {
            val includeService = sharedPreferences?.getBoolean("include_service", false)
            if (includeService == true) {
                Log.d("Prefs", "Service charge enabled")
            }
        }
        "dietary_preference" -> {
            val diet = sharedPreferences?.getString("dietary_preference", "omnivore")
            Log.d("Prefs", "Dietary preference changed to: $diet")
        }
    }
}
```

> `sharedPreferences` is nullable — use `?.` safe-call operators throughout.

---

<!-- Slide 28: Approach 2 — findPreference -->

# Approach 2 — Access the Preference Widget Directly

Use `findPreference<T>()` to get the preference object itself — useful for updating the `summary` shown under the title.

```kotlin
override fun onSharedPreferenceChanged(
    sharedPreferences: SharedPreferences?, key: String?) {

    if (key == "include_service") {
        // Cast to the specific preference type
        val pref = findPreference<SwitchPreference>(key)
        pref?.summary = if (pref?.isChecked == true)
            "10% service charge will be added" else "No service charge"
    }

    if (key == "dietary_preference") {
        val listPref = findPreference<ListPreference>(key)
        // .entry gives the human-readable label (not the stored value)
        listPref?.summary = listPref?.entry ?: "Not set"
    }
}
```

> Only available inside `PreferenceFragmentCompat`. Use Approach 1 from other components.

---

<!-- Slide 29: Data Flow Summary -->

# Data Flow Summary

```
User interacts with      SharedPreferences      onSharedPreferenceChanged()
preference widget   -->  file updated       -->  callback fires in listener
      |                        |                          |
 (Switch toggled)        key="include_service"    key = "include_service"
                         value = true              read new value & react
```

**The key insight:**

> You never need to manually save the value — the Preference framework does that automatically when the user interacts with a preference widget.
> Your only job in `onSharedPreferenceChanged` is to **react** to the change.

---

<!-- _class: conclusion -->

# Summary

**Android Preferences framework provides a structured, consistent approach to app settings:**

- **XML** — declare your preference hierarchy in `res/xml/`
- **Fragment** — `PreferenceFragmentCompat` renders and persists settings automatically
- **SharedPreferences** — read values using typed getters and the preference key
- **Listener** — `OnSharedPreferenceChangeListener` fires when any preference changes
- **Callbacks** — use `when(key)` or `findPreference<T>()` to react to specific changes

> The framework handles persistence, UI rendering, and dialogs — keeping your code focused on *reacting* to user preferences, not managing them.
