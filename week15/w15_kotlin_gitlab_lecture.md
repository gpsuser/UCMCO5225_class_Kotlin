# Version Control with Android Studio

## Table of Contents

- [Learning Outcomes](#learning-outcomes)
- [Introduction to Version Control](#introduction-to-version-control)
- [Understanding Git Fundamentals](#understanding-git-fundamentals)
- [Creating a Git Repository in Android Studio](#creating-a-git-repository-in-android-studio)
- [Making Commits](#making-commits)
- [Working with Remote Repositories](#working-with-remote-repositories)
- [Creating and Configuring a Remote Repository on GitLab](#creating-and-configuring-a-remote-repository-on-gitlab)
- [Pushing Changes to Remote](#pushing-changes-to-remote)
- [Pulling Changes from Remote](#pulling-changes-from-remote)
- [Understanding and Resolving Conflicts](#understanding-and-resolving-conflicts)
- [Cloning an Existing Repository](#cloning-an-existing-repository)
- [Best Practices for Version Control](#best-practices-for-version-control)

---

## Learning Outcomes

By the end of this lecture, you will be able to:

- Explain the purpose and benefits of version control in software development
- Create and initialize a Git repository for an Android Studio project
- Make meaningful commits with clear descriptions following Git conventions
- Configure and connect to remote repositories on GitLab
- Push local changes to and pull updates from remote repositories
- Identify and resolve merge conflicts using Android Studio's built-in tools
- Clone existing repositories and set up projects from remote sources
- Apply version control best practices to manage your Android development projects effectively

---

## Introduction to Version Control

### What is Version Control?

Version control is a system that records changes to files over time so that you can recall specific versions later. Think of it as a detailed "save game" system for your code, where every meaningful change is recorded and can be revisited if needed.

**Why is Version Control Essential?**

1. **History Tracking**: Every change is recorded with information about who made it, when, and why
2. **Collaboration**: Multiple developers can work on the same project without overwriting each other's work
3. **Backup**: Your code is safely stored in multiple locations
4. **Experimentation**: You can try new features without fear of breaking existing code
5. **Recovery**: Easily revert to previous versions if something goes wrong

### Version Control in Professional Development

In industry, version control is not optional—it's a fundamental skill expected of every developer. Whether you're working on a team of 2 or 200, version control systems like Git enable:

- **Code Reviews**: Team members can review changes before they're integrated
- **Parallel Development**: Different features can be developed simultaneously
- **Deployment Management**: Specific versions can be deployed to production environments
- **Documentation**: The commit history serves as a log of project evolution

---

## Understanding Git Fundamentals

### What is Git?

Git is a distributed version control system created by Linus Torvalds in 2005. Unlike older centralized systems, Git allows every developer to have a complete copy of the entire project history on their local machine.

**Key Git Concepts:**

1. **Repository (Repo)**: A directory that Git tracks, containing your project files and the complete history of changes
2. **Commit**: A snapshot of your project at a specific point in time
3. **Branch**: An independent line of development (we'll cover this in depth in future sessions)
4. **Remote**: A version of your repository hosted on a server (like GitLab or GitHub)
5. **Clone**: Creating a local copy of a remote repository
6. **Push**: Sending your local commits to a remote repository
7. **Pull**: Retrieving changes from a remote repository to your local machine

### The Git Workflow

The basic Git workflow follows this pattern:

```
1. Modify files in your working directory
2. Stage the changes you want to commit
3. Commit the staged changes with a descriptive message
4. Push commits to a remote repository (optional but recommended)
```

This workflow ensures that changes are deliberate and well-documented.

---

## Creating a Git Repository in Android Studio

### Initial Setup Considerations

When you create a new Android Studio project, unlike some other IDEs, Android Studio does not automatically create a Git repository. This is actually beneficial as it gives you control over when and how to initialize version control for your projects.

### Step-by-Step: Initializing a Git Repository

**Step 1: Create or Open Your Android Studio Project**

First, ensure you have an Android Studio project open that you want to track with version control.

**Step 2: Access the VCS Menu**

Android Studio provides comprehensive Git integration through the VCS (Version Control System) menu. All Git operations can be accessed from here.

**Step 3: Create the Git Repository**

1. Navigate to: **VCS > Import into Version Control > Create Git Repository...**
2. A dialog box will appear asking you to select a directory
3. **Critical**: Select the root folder of your project (not a subfolder)
4. Click "OK"

**Why the Root Folder?**

The root folder contains all project files, including:
- Source code directories (`app/src/`)
- Build configuration files (`build.gradle`)
- Android manifest (`AndroidManifest.xml`)
- Resource files
- Gradle wrapper files

Initializing the repository at the root ensures all these important files are tracked.

### What Happens Behind the Scenes?

When you create a Git repository, Git creates a hidden `.git` directory in your project folder. This directory contains:

- The complete history of all commits
- Configuration settings
- Information about branches and remotes
- Staged changes

**Important**: Never manually modify or delete the `.git` directory—this is where Git stores all version control information.

### Understanding .gitignore

Android Studio projects contain many files that shouldn't be tracked by Git:

- Build outputs (`.apk` files, compiled classes)
- IDE-specific settings (some `.idea` folder contents)
- Local configuration files
- Generated files

Android Studio automatically creates a `.gitignore` file that specifies which files Git should ignore. This keeps your repository clean and focused on source code.

**Common .gitignore Patterns:**

```
*.iml
.gradle
/local.properties
/.idea/workspace.xml
/.idea/libraries
.DS_Store
/build
/captures
```

---

## Making Commits

### Understanding Commits

A commit is like taking a snapshot of your project. Each commit represents a specific state of your codebase and should contain related changes that serve a single purpose.

### Good Commit Practices

**1. Commit Frequently**
- Make commits after completing a logical unit of work
- Don't wait until the end of the day to commit everything
- Smaller, focused commits are easier to understand and review

**2. Commit Related Changes Together**
- Group changes that serve the same purpose
- For example: "Add login button" should include the button XML, the click handler, and related styling

**3. Don't Commit Half-Done Work**
- Only commit when a feature or fix is complete and functional
- Use Git branches for work-in-progress (covered in future sessions)

### The Commit Process in Android Studio

**Step 1: Access the Commit Dialog**

Navigate to: **VCS > Commit...** (or press `Cmd+K` on Mac / `Ctrl+K` on Windows)

**Step 2: Review Changed Files**

Android Studio shows all files that have been modified, added, or deleted. You'll see:

- A list of changed files with color coding:
  - **Blue**: Modified files
  - **Green**: New files
  - **Red**: Deleted files
- A diff viewer showing exactly what changed in each file

**Step 3: Select Files to Include**

Not all changed files need to be in every commit. Use the checkboxes to select which files to include.

**Important Note About .idea Folder:**
- The `.idea` folder contains IDE-specific settings
- Most `.idea` contents shouldn't be committed (they're user-specific)
- Android Studio's default `.gitignore` handles this automatically
- If you see `.idea` files in your commit, they're usually workspace settings that differ per developer

**Step 4: Write a Commit Message**

This is where many developers struggle, but good commit messages are crucial.

### Commit Message Conventions

**The Golden Rules:**

1. **Use Present Tense Imperative Mood**
   - ✅ "Add user authentication feature"
   - ✅ "Fix crash on empty input"
   - ✅ "Refactor database helper class"
   - ❌ "Added user authentication"
   - ❌ "Fixed crash"
   - ❌ "Refactored database"

2. **Start with a Verb**
   - Add, Fix, Update, Remove, Refactor, Implement, etc.

3. **Be Specific but Concise**
   - ✅ "Fix NullPointerException in MainActivity.onCreate()"
   - ❌ "Fix bug"
   - ❌ "Update code"

4. **Explain What and Why, Not How**
   - The code shows how; the message explains what changed and why

**Example Commit Messages:**

```
Good Examples:
- "Add input validation to registration form"
- "Fix memory leak in image loading"
- "Refactor database queries for better performance"
- "Update app theme to Material Design 3"
- "Remove deprecated API calls"

Poor Examples:
- "Changed stuff"
- "Fixed it"
- "Updates"
- "Work in progress"
- "asdfasdf"
```

### The Commit Dialog Interface

Here's what you'll see in the commit dialog:

```
┌─────────────────────────────────────────────┐
│ Commit Message                               │
│ ┌─────────────────────────────────────────┐ │
│ │ Add login button to main activity       │ │
│ └─────────────────────────────────────────┘ │
│                                              │
│ Changed Files:                               │
│ ☑ app/src/main/res/layout/activity_main.xml │
│ ☑ app/src/main/java/.../MainActivity.kt     │
│ ☐ .idea/workspace.xml                       │
│                                              │
│ [Commit]  [Commit and Push...]              │
└─────────────────────────────────────────────┘
```

**Step 5: Commit Your Changes**

Click the "Commit" button to save your changes to the local repository.

### Example: Tracking a Simple Kotlin Change

Let's see what a commit looks like with actual code changes:

**Before Commit - Original MainActivity.kt:**

```kotlin
package com.example.myapp

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}
```

**After Changes - Modified MainActivity.kt:**

```kotlin
package com.example.myapp

import android.os.Bundle
import android.widget.Button
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Set up click listener for login button
        val loginButton = findViewById<Button>(R.id.loginButton)
        loginButton.setOnClickListener {
            Toast.makeText(this, "Login clicked", Toast.LENGTH_SHORT).show()
        }
    }
}
```

**Good Commit Message:**
```
Add click listener to login button

Implement basic Toast notification when login button is pressed
to provide user feedback. Full authentication will be added later.
```

This commit message clearly states what was added and provides context about future work.

---

## Working with Remote Repositories

### Local vs. Remote Repositories

So far, we've been working with a **local repository**—Git storage on your own computer. While this provides version control benefits, it doesn't offer:

- **Backup**: If your computer fails, you lose everything
- **Collaboration**: Others can't access your code
- **Accessibility**: You can't access your code from other machines

This is where **remote repositories** come in.

### What is a Remote Repository?

A remote repository is a version of your project hosted on a server. Popular platforms include:

- **GitLab**: Open-source platform, often used in universities and enterprises
- **GitHub**: Widely used in open-source and professional development
- **Bitbucket**: Popular in corporate environments

For your coursework, we'll be using GitLab, specifically a university-hosted instance.

### The Local-Remote Relationship

Think of the relationship like this:

```
Your Computer (Local Repository)
         ↕ 
   Network Transfer
         ↕
GitLab Server (Remote Repository)
```

- **Push**: Send your local commits to the remote
- **Pull**: Retrieve commits from the remote to your local machine
- **Fetch**: Download remote changes without integrating them

### Benefits of Using Remotes

1. **Automatic Backup**: Your code is stored securely on a server
2. **Access from Anywhere**: Work on different computers
3. **Collaboration**: Share code with team members or instructors
4. **Code Review**: Others can review your changes
5. **Submission**: For assignments, remotes provide an easy submission method

---

## Creating and Configuring a Remote Repository on GitLab

### Step-by-Step: Creating a GitLab Repository

**Step 1: Navigate to GitLab**

Open your web browser and go to: `https://chester.networked` (or your institution's GitLab URL)

**Step 2: Create a New Project**

1. Click the "New Project" button (usually a plus icon or "+" symbol)
2. Select "Create blank project"

**Step 3: Configure Project Settings**

You'll see several options:

**Project Name**: Give your repository a meaningful name
- ✅ "AndroidDevelopmentAssignment1"
- ✅ "WeatherApp"
- ❌ "test"
- ❌ "project"

**Project URL**: This is automatically generated from your project name

**Visibility Level**: Choose who can see your repository
- **Private**: Only you and people you invite (recommended for assignments)
- **Internal**: Anyone logged into the GitLab instance
- **Public**: Anyone on the internet

**⚠️ FOR ASSIGNMENTS**: Set visibility to **Private**! You can invite your instructors or teammates if needed.

**Initialize repository with a README**: **UNCHECK THIS BOX**
- We're pushing an existing project, not creating a new one
- If checked, GitLab creates an initial commit that will conflict with your local repository

**Step 4: Create the Project**

Click "Create project" and GitLab will generate your remote repository.

### Setting Up Authentication Tokens

Git needs a way to verify your identity when pushing code. GitLab uses **personal access tokens** for this.

**Why Not Use Your Password?**

- Tokens can have limited permissions (more secure)
- Tokens can be revoked without changing your password
- Different tokens can be created for different machines
- Tokens can have expiration dates for added security

**Creating an Access Token:**

1. Go to **Settings > Access Tokens** in GitLab
2. Click "Add new token"
3. Configure the token:

**Token Name**: Identify which computer this token is for
- ✅ "Home-Laptop-Token"
- ✅ "University-Lab-PC"
- ❌ "Token1"

**Expiration Date**: 
- For coursework: Set to expire after the semester/course ends
- For personal projects: You can set "No expiration" (though this is less secure)

**Scopes**: Select permissions
- At minimum, select `write_repository` (allows push/pull)
- For full functionality, select `api` and `read_repository` as well

4. Click "Create personal access token"

**⚠️ CRITICAL**: Copy the token immediately!
GitLab shows the token only once. If you lose it, you'll need to create a new one.

**Best Practices for Tokens:**

- Create a separate token for each computer you use
- Store tokens securely (use a password manager)
- Revoke tokens you're no longer using
- Never share tokens or commit them to code
- Treat tokens like passwords—they provide full access to your account

### Connecting Your Local Repository to GitLab

Now we'll link your local Android Studio project to the GitLab remote repository.

**Step 1: Copy Your Repository URL**

On your GitLab project page, find the repository URL. It looks like:
```
https://git.chester.network/your-username/your-project.git
```

**Step 2: Add the Remote in Android Studio**

1. In Android Studio, go to **VCS > Git > Remotes...**
2. Click the "+" (Add) button
3. Enter the remote details:

**Name**: `origin` (this is the conventional name for the primary remote)

**URL**: Paste your GitLab repository URL

4. Click "OK"

**Step 3: Authenticate**

Android Studio will prompt you to log in:

- **Username**: Your GitLab username
- **Password**: **Paste your access token** (NOT your GitLab password!)

Android Studio will save these credentials (in a secure credential store) so you don't need to enter them every time.

### Understanding the "origin" Remote

The name "origin" is a Git convention for your primary remote repository. Think of it as a nickname:

```
origin = https://git.chester.network/username/project.git
```

When you run Git commands, you can use "origin" instead of the full URL:
- `git push origin main` pushes to your GitLab repository
- `git pull origin main` pulls from your GitLab repository

---

## Pushing Changes to Remote

### What is Pushing?

Pushing uploads your local commits to the remote repository. Think of it as syncing your local work with the server.

**Important**: Push only uploads commits, not uncommitted changes. Always commit before pushing!

### The Push Process

**Step 1: Ensure You Have Commits to Push**

Before pushing, make sure you've committed your changes locally. You can't push uncommitted work.

**Step 2: Access the Push Dialog**

Navigate to: **VCS > Git > Push...** (or press `Cmd+Shift+K` on Mac / `Ctrl+Shift+K` on Windows)

**Step 3: Review Commits to Push**

Android Studio shows you:
- Which commits will be pushed
- Which files are in each commit
- The branch you're pushing to (usually `main` or `master`)

**Step 4: Push**

Click the "Push" button. Android Studio will:
1. Connect to the remote repository
2. Upload your commits
3. Update the remote to match your local repository

**Confirmation**: You'll see a success notification: "Pushed successfully"

### Commit and Push Simultaneously

Android Studio offers a convenient shortcut: you can commit and push in one operation.

In the commit dialog, instead of clicking "Commit", click "Commit and Push..."

This is useful for:
- Quick updates
- When you're certain the commit is ready to share
- End-of-day work sessions (to ensure your code is backed up)

**When to Use Commit vs. Commit and Push:**

- **Commit only**: When you want to make several related commits before pushing
- **Commit and Push**: When each commit should immediately be shared/backed up

### Understanding Push Output

When you push, Git provides feedback:

```
Pushing to https://git.chester.network/username/project.git
   abc1234..def5678  main -> main
   
Successfully pushed 3 commits to origin/main
```

This tells you:
- Where you pushed to
- Which branch was updated
- How many commits were uploaded

---

## Pulling Changes from Remote

### What is Pulling?

Pulling downloads commits from the remote repository and integrates them into your local repository. This is essential when:

- Working on multiple computers
- Collaborating with others
- Someone else has pushed changes you need

### Understanding Pull vs. Fetch

**Fetch**: Downloads commits but doesn't integrate them
**Pull**: Downloads commits AND integrates them automatically

For most scenarios, pull is what you want.

### The Pull Process

**Step 1: Access the Pull Dialog**

Navigate to: **VCS > Git > Pull...** (or press `Cmd+T` on Mac / `Ctrl+T` on Windows)

**Step 2: Review Pull Settings**

The dialog shows:
- Which remote you're pulling from (`origin`)
- Which branch you're pulling into (usually `main`)

**Step 3: Pull Changes**

Click "OK" and Git will:
1. Download new commits from the remote
2. Integrate them into your local branch
3. Update your working files

**Success Scenario**: 
```
Pulling from https://git.chester.network/username/project.git
   Updating abc1234..def5678
   Fast-forward
   MainActivity.kt | 10 ++++++++--
   1 file changed, 8 insertions(+), 2 deletions(-)
```

### When Should You Pull?

**Always pull before starting new work!**

This ensures you're working with the most recent version and prevents conflicts.

**Typical Workflow:**
```
1. Open project
2. Pull latest changes
3. Make your changes
4. Commit
5. Pull again (in case others pushed)
6. Push your commits
```

### Pull Conflicts (Preview)

Sometimes pulling causes conflicts (we'll cover this in detail next). This happens when:
- You modified a file locally
- Someone else modified the same file remotely
- Git can't automatically merge the changes

Don't worry—Android Studio has excellent tools to resolve these!

---

## Understanding and Resolving Conflicts

### What Causes Conflicts?

Imagine this scenario:

**Monday 9 AM**: You pull the latest code. MainActivity.kt has this:

```kotlin
fun calculateTotal(price: Double): Double {
    return price
}
```

**Monday 10 AM**: Your teammate pushes a change:

```kotlin
fun calculateTotal(price: Double, tax: Double): Double {
    return price + tax
}
```

**Monday 11 AM**: You modify your local copy:

```kotlin
fun calculateTotal(price: Double, discount: Double): Double {
    return price - discount
}
```

**Monday 12 PM**: You try to pull or push...

### Conflict! 

Git can't automatically merge these changes because both versions modified the same function in different ways. Git doesn't know which version is correct, so it asks you to decide.

### Identifying Conflicts

When a conflict occurs, Android Studio will show:
- A notification: "Conflicts detected"
- A list of files with conflicts
- Options to resolve the conflicts

**VCS > Git > Resolve Conflicts** shows all conflicted files.

### The Merge Conflict Interface

Android Studio provides a powerful three-way merge tool:

```
┌─────────────────────────────────────────────────┐
│  Your Version  │   Result   │  Their Version   │
│   (Local)      │  (Merged)  │   (Remote)       │
├────────────────┼────────────┼──────────────────┤
│                │            │                  │
│  Your changes  │  What will │  Remote changes  │
│  that conflict │  be saved  │  that conflict   │
│                │            │                  │
└────────────────┴────────────┴──────────────────┘
    [Accept Left]  [Accept Right]  [Apply]
```

**Three Panes:**

1. **Left (Yours)**: Your local changes
2. **Middle (Result)**: The final merged version
3. **Right (Theirs)**: Changes from the remote repository

### Resolving Conflicts: Step by Step

**Step 1: Understand Both Changes**

Read both versions carefully. What was each developer trying to accomplish?

**Step 2: Choose a Resolution Strategy**

You have several options:

**Option A: Accept Yours**
- Click "Accept Left" for your changes
- Use when your changes are correct

**Option B: Accept Theirs**
- Click "Accept Right" for remote changes
- Use when remote changes are correct

**Option C: Manual Merge**
- Manually edit the middle pane
- Combine both sets of changes
- Use when both changes should be kept

**Option D: Rewrite Completely**
- Write a new version that incorporates both intents
- Use when neither version is ideal

### Example Conflict Resolution

**Your Version (Local):**
```kotlin
// Add error handling to user input
fun processUserInput(input: String): Boolean {
    if (input.isEmpty()) {
        Log.e(TAG, "Empty input provided")
        return false
    }
    return true
}
```

**Their Version (Remote):**
```kotlin
// Add input length validation
fun processUserInput(input: String): Boolean {
    if (input.length < 3) {
        return false
    }
    return true
}
```

**Good Manual Merge:**
```kotlin
// Add comprehensive input validation with error handling
fun processUserInput(input: String): Boolean {
    // Check for empty input
    if (input.isEmpty()) {
        Log.e(TAG, "Empty input provided")
        return false
    }
    
    // Check minimum length requirement
    if (input.length < 3) {
        Log.e(TAG, "Input too short (minimum 3 characters)")
        return false
    }
    
    return true
}
```

This combines both intentions: logging errors AND validating length.

### After Resolving Conflicts

**Step 3: Mark as Resolved**

Once you've edited the middle pane to your satisfaction:
1. Click "Apply" to accept the merged version
2. The file is removed from the conflicts list

**Step 4: Complete the Merge**

After resolving all conflicts:
1. Review your merged code
2. Test that it works correctly
3. Commit the merge:

```
Merge commit message:
"Merge remote changes with local input validation"
```

**Step 5: Push the Resolution**

Push your merged changes so others benefit from the resolution.

### Preventing Conflicts

While conflicts are normal, you can minimize them:

1. **Pull Frequently**: Get others' changes early
2. **Commit and Push Regularly**: Share your changes often
3. **Communicate**: Coordinate who's working on which files
4. **Small Commits**: Smaller changes are easier to merge
5. **Code Reviews**: Catch potential conflicts before merging

---

## Cloning an Existing Repository

### What is Cloning?

Cloning creates a complete local copy of a remote repository. This is useful for:

- Starting work on an existing project
- Working on a different computer
- Collaborating on team projects
- Accessing instructor-provided starter code

### The Clone Process

**Step 1: Get the Repository URL**

On GitLab, navigate to the repository and find the "Clone" button. You'll see:

```
Clone with HTTPS:
https://somehost.com/somerepo.git
```

Copy this URL.

**Step 2: Clone in Android Studio**

**Option A: From Welcome Screen**
1. On the Android Studio welcome screen
2. Click "Get from VCS" (Version Control System)

**Option B: From an Open Project**
3. Go to **VCS > Get from Version Control...**

**Step 3: Enter Clone Details**

```
┌────────────────────────────────────────┐
│ Clone Repository                       │
│                                        │
│ URL: https://somehost.com/somerepo.git │
│                                        │
│ Directory:                             │
│ /Users/yourname/Documents/AS Projects/ │
│ /NewProject                            │
│                                        │
│       [Test]         [Clone]           │
└────────────────────────────────────────┘
```

**URL**: Paste the repository URL

**Directory**: Choose where to save the project locally
- Pick a meaningful location
- Android Studio will create a folder with the project name

**Step 4: Clone**

Click "Clone" and Android Studio will:
1. Download the entire repository
2. Set up the remote connection automatically
3. Create a local working copy
4. Open the project

### Authentication During Clone

If the repository is private, you'll be prompted to authenticate:

- **Username**: Your GitLab username
- **Password**: Your access token (not your GitLab password!)

### After Cloning

**Step 5: Project Setup**

Android Studio may display dialogs asking you to:
- Trust the project source
- Import Gradle settings
- Configure the JDK
- Sync Gradle files

**Important**: Click "Trust Project" and follow the prompts. Android Studio needs to configure the project's build system.

**Typical Prompts:**
```
"Trust Project?"
→ Click "Trust Project"

"Gradle Sync"
→ Click "OK" or "Sync Now"

"Configure SDK"
→ Follow the prompts to set up the Android SDK
```

**Step 6: Verify the Clone**

Once setup completes, you should see:
- The full project structure in the Project panel
- All source files accessible
- The ability to build and run the project

### Cloning Creates Everything

When you clone, you get:
- All source code files
- Complete commit history
- All branches
- Remote repository configuration
- Project build files

**What You Don't Get:**
- Build outputs (these are generated locally)
- IDE-specific settings from other users
- Large binary files (if excluded via .gitignore)

### Your Relationship with the Remote

After cloning, your local repository is linked to the remote:

```
Your Clone (Local)  ←→  Original Repo (Remote: origin)
```

You can:
- Pull to get updates from the original
- Push your changes back (if you have permission)
- Work independently and sync when ready

---

## Best Practices for Version Control

### Commit Best Practices

**1. Commit Often, But Meaningfully**
```
✅ After fixing a bug
✅ After adding a feature
✅ After refactoring a class
❌ Every time you save a file
❌ Only at the end of the day
```

**2. One Commit = One Purpose**

Bad commit:
```
"Fix login bug, add new activity, update dependencies, 
 refactor database, update README"
```

Good commits:
```
Commit 1: "Fix NullPointerException in login validation"
Commit 2: "Add ProfileActivity with basic layout"
Commit 3: "Update AndroidX dependencies to latest stable versions"
Commit 4: "Refactor DatabaseHelper to use Room"
Commit 5: "Update README with setup instructions"
```

**3. Test Before You Commit**

Always ensure your code compiles and runs before committing:
```
1. Make changes
2. Test thoroughly
3. Fix any issues
4. Then commit
```

### Push/Pull Best Practices

**Daily Workflow:**

```
Morning:
1. Open Android Studio
2. Pull latest changes
3. Start work

Throughout Day:
4. Make changes
5. Commit locally
6. Continue working

End of Day:
7. Pull again (in case others pushed)
8. Resolve any conflicts
9. Push your commits
10. Verify push succeeded
```

**Before Major Changes:**
Always pull before:
- Starting a new feature
- Refactoring large sections of code
- Merging branches (advanced topic)

### GitLab Best Practices for Assignments

**1. Always Use Private Repositories**
Academic integrity requires your assignment code remains private.

**2. Invite Your Instructor**
Most courses require you to:
- Invite your instructor to the repository
- Grant "Reporter" level permissions (minimum)
- This allows them to view code and provide help

**How to Invite:**
```
GitLab Project > Settings > Members > Invite Member
- Enter instructor's username/email
- Select role: "Reporter" or "Developer"
- Click "Invite"
```

**3. Regular Pushes**
Push your assignment progress regularly:
- Provides backup if something goes wrong
- Shows your work progression (helpful for academic integrity)
- Allows instructor to help with issues

**4. Clear Commit Messages**
Helps instructors understand your development process:
```
✅ "Implement database schema for user accounts"
✅ "Fix crash when loading large images"
❌ "Changes"
❌ "Stuff"
```

### File Management Best Practices

**What to Commit:**
```
✅ Source code (.kt, .java)
✅ Layout files (.xml)
✅ Resource files (strings, drawables)
✅ Build configuration (build.gradle)
✅ Manifest file
✅ README and documentation
```

**What Not to Commit:**
```
❌ Build outputs (/build, .apk files)
❌ User-specific IDE settings (workspace.xml)
❌ Compiled files (.class)
❌ Local configuration (local.properties)
❌ Temporary files
❌ API keys and secrets
```

Android Studio's default `.gitignore` handles most of this automatically.

### Security Best Practices

**1. Never Commit Sensitive Information**
```
❌ API keys
❌ Passwords
❌ Database credentials
❌ Access tokens
❌ Private keys
```

**2. Use Configuration Files**
Store sensitive data in files excluded from Git:
```kotlin
// local.properties (not committed)
api.key=your-secret-key-here

// Build.gradle.kts reads from local.properties
android {
    buildTypes {
        all {
            buildConfigField(
                "String", 
                "API_KEY", 
                properties["api.key"].toString()
            )
        }
    }
}
```

**3. Revoke Compromised Tokens**
If you accidentally commit a token:
1. Immediately revoke it on GitLab
2. Create a new token
3. Remove the token from Git history (advanced operation)

### Collaboration Best Practices

**Communication:**
- Discuss who's working on which files
- Notify team members before major refactoring
- Use descriptive commit messages so others understand changes

**Code Reviews:**
- Review each other's commits
- Provide constructive feedback
- Learn from others' approaches

**Conflict Prevention:**
- Pull before you push
- Commit and push regularly
- Coordinate changes to shared files

---

## Summary

Today we've covered the essential skills for version control with Git and Android Studio:

- **Git Fundamentals**: Understanding repositories, commits, and remotes
- **Local Version Control**: Creating repositories and making commits
- **Remote Repositories**: Setting up GitLab, creating access tokens, and connecting remotes
- **Synchronization**: Pushing local changes and pulling remote updates
- **Conflict Resolution**: Understanding and resolving merge conflicts
- **Cloning**: Setting up projects from existing repositories
- **Best Practices**: Professional approaches to version control

Version control is fundamental to modern software development. As you continue your development journey, these skills will serve you in every project, whether working solo or in teams.

Remember: version control is about more than backing up code—it's about tracking your project's evolution, collaborating effectively, and maintaining a clear history of your development decisions.

Make frequent, meaningful commits with clear messages, push regularly to back up your work, and pull before starting new work to stay synchronized with your team.

---