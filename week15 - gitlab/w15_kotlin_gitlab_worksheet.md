# Worksheet

## Quiz Section

### Question 1

Complete the following statement by filling in the blanks:

A __________ is a snapshot of your project at a specific point in time, while a __________ is a version of your repository hosted on a server. When you want to upload your local commits to the remote repository, you perform a __________ operation.

**Word Bank:** remote, push, commit, clone, pull, fetch

---

### Question 2

Complete the following statement by filling in the blanks:

The hidden __________ directory contains the complete history of all commits and should never be manually modified. When initializing a Git repository in Android Studio, you should select the __________ folder of your project to ensure all project files are tracked.

**Word Bank:** .gitignore, root, .git, build, source, local

---

### Question 3

Complete the following statement by filling in the blanks:

Git commit messages should use __________ tense imperative mood and always start with a __________. A good commit message explains the __________ of the change, not just the how.

**Word Bank:** what, verb, past, present, adjective, why

---

### Question 4

What is the primary purpose of a `.gitignore` file in an Android Studio project?

A) To hide files from the Android Studio interface  
B) To specify which files Git should not track or commit  
C) To prevent other developers from accessing certain files  
D) To compress files before committing them to save space

---

### Question 5

When should you pull changes from a remote repository?

A) Only at the end of each day before closing Android Studio  
B) Before starting new work and before pushing your commits  
C) Only when you receive an error message  
D) After making all your local commits but before testing

---

### Question 6

What happens when you clone a repository?

A) Only the current files are copied, without any history  
B) A complete local copy is created including all commits, branches, and history  
C) The remote repository is deleted and moved to your computer  
D) A link is created to the remote repository without downloading any files

---

## Task Section

### Task 1: Commit Message Evaluation

You are reviewing commit messages from a team member who is new to Git. For each commit message below, identify whether it is **Good** or **Bad**, and if it's bad, rewrite it following Git conventions.

```
1. "fixed stuff"

2. "Add input validation to registration form"

3. "Updated the code and changed some files"

4. "Fix NullPointerException in MainActivity.onCreate() when user data is null"

5. "I added a new feature for the login screen and also fixed a bug 
   and updated the theme colors"
```

**Your task:**
- Classify each message as Good or Bad
- Rewrite the bad messages following proper conventions (present tense imperative, starts with verb, specific and concise)

---

### Task 2: Merge Conflict Resolution

You're working on a team project and encounter a merge conflict in the following Kotlin file. Both you and your teammate modified the `validateEmail` function.

**Your Version (Local):**
```kotlin
class UserValidator {
    fun validateEmail(email: String): Boolean {
        // Check if email is empty
        if (email.isEmpty()) {
            return false
        }
        return email.contains("@")
    }
}
```

**Their Version (Remote):**
```kotlin
class UserValidator {
    fun validateEmail(email: String): Boolean {
        // Validate email format with regex
        val emailPattern = "[a-zA-Z0-9._-]+@[a-z]+\\.+[a-z]+"
        return email.matches(emailPattern.toRegex())
    }
}
```

**Your task:**
Write the merged version that combines both approaches:
- Keep the empty check from your version
- Use the regex validation from their version
- Add appropriate comments explaining each validation step
- Ensure the function returns `false` if either check fails

**Scaffolding:**
```kotlin
class UserValidator {
    fun validateEmail(email: String): Boolean {
        // TODO: Add empty check first
        
        
        // TODO: Add regex validation
        val emailPattern = "[a-zA-Z0-9._-]+@[a-z]+\\.+[a-z]+"
        
        
    }
}
```

**Test your solution with these cases:**
```kotlin
// Should return false
validateEmail("")           
validateEmail("notanemail")  

// Should return true
validateEmail("user@example.com")
```

---

### Task 3: Version Control Workflow (Challenge)

You're working on an Android project with a team. Below is a sequence of Git operations that led to a problem. Analyze the workflow and answer the questions.

**Monday 9:00 AM:**
```
Developer A: Clones repository
Developer B: Clones repository
```

**Monday 10:00 AM:**
```
Developer A: Modifies MainActivity.kt (adds login button)
Developer A: Commits locally: "Add login button"
Developer A: Pushes to remote
```

**Monday 11:00 AM:**
```
Developer B: Modifies MainActivity.kt (adds signup button)
Developer B: Commits locally: "Add signup button"
Developer B: Tries to push to remote -> ERROR!
```

**Part A:** Explain why Developer B received an error when trying to push.

**Part B:** Write the exact sequence of Git operations (with Android Studio menu paths) that Developer B should perform to resolve this issue and successfully push their changes.

**Part C:** Developer B successfully merged and pushed their changes. Now Developer A wants to get the latest code including the signup button. What should Developer A do?

**Part D:** Both developers modified the same file (MainActivity.kt) but in different places - Developer A added code for a login button, Developer B added code for a signup button. When Developer B pulled the changes, would this cause a merge conflict? Explain your reasoning.

**Part E:** Create a Kotlin code snippet showing what the merged MainActivity.kt might look like after both developers' changes are integrated. Include:
- Both login and signup buttons
- Appropriate click listeners for each
- Toast messages for user feedback

**Scaffolding:**
```kotlin
package com.example.teamapp

import android.os.Bundle
import android.widget.Button
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // TODO: Set up login button click listener
        
        
        // TODO: Set up signup button click listener
        
        
    }
}
```

---
