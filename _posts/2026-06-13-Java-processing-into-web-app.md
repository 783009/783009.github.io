---
title: "Java processing into web app"
date: 2026-06-13
categories: [Devlog, Quantum Blink, projects, Documentation, Tutorial]
tags: [Processing, Game Dev, Computer Science, Portfolio, Tutorial]
---

# How to Put Your Processing (.pde) Game Online (From Scratch!)

If you have built a game inside the Processing desktop application on your computer and want to put it on a public website like GitHub Pages, this step-by-step guide will show you exactly how to do it. 

We will use a free library called **Processing.js**, which reads your Java-style game code and runs it directly inside any web browser. This guide assumes you are starting completely from scratch and know nothing about web development.

> 🎮 **Live Example:** You can see exactly how a finished project looks and runs by checking out the [Quantum Blink Live Game](https://783009.github.io/Quantum-Blink/). You can also view its full source code layout directly inside the [Quantum Blink GitHub Repository](https://github.com/783009/Quantum-Blink) to use as a reference while following this guide!

<div style="position: relative; width: 100%; padding-bottom: 66.66%; height: 0; overflow: hidden; border: 3px solid #00f0ff; border-radius: 12px; background: #0c141c; box-shadow: 0 0 20px rgba(0, 240, 255, 0.25); margin: 25px 0;">
  <iframe 
    src="https://783009.github.io/Quantum-Blink/" 
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: none;"
    allow="fullscreen">
  </iframe>
</div>

---

## Step 1: Locate Your Game Files on Your Computer
Before we build the website, you need to find where your game file (`.pde`) and your asset folders are saved on your computer.

1. Open your game inside the **Processing** desktop application.
2. In the top menu bar, click on **Sketch** and then click **Show Sketch Folder** (or press `Ctrl + K` on Windows / `Cmd + K` on Mac).
3. A file explorer window will pop up. Inside this folder, you will see your main game file (for example, `MyGame.pde`) and a folder named `data` containing your images and sounds. Keep this folder open!

---

## Step 2: Create Your Web Project Folder
Now, let's create a brand new workspace folder on your desktop where we will assemble the website version of your game.

1. Go to your computer's Desktop, right-click an empty space, select **New**, and choose **Folder**. Name this folder `web-game`.
2. Go back to the Processing sketch folder you opened in Step 1.
3. Copy your game file (e.g., `MyGame.pde`) and your entire `data` folder, then paste them directly into your new `web-game` folder.

---

## Step 3: Get the Processing.js Engine
Web browsers cannot read standard Java files on their own. They need a translator file called `processing.min.js`.

1. Open your web browser and search for a trusted download or CDN repository hosting the `processing.min.js` script file or you can download it [here](https://github.com/783009/Quantum-Blink/blob/main/processing.min.js).
2. Right-click the page or file link and choose **Save Link As...** or copy the raw script text into a blank text file.
3. Save this file inside your `web-game` folder and name it exactly: `processing.min.js`

Your `web-game` folder should now look exactly like this:

``` text
my-game-folder/
├── processing.min.js
├── MyGame.pde
└── data/
    ├── player.png
    └── jump.wav
```
---

## Step 4: Create the Webpage File (`index.html`)
An `index.html` file acts as the front door to your website. It tells the browser to load the Processing engine and run your game canvas.

1. Open a basic text editor on your computer (like **Notepad** on Windows or **TextEdit** on Mac set to plain text mode).
2. Paste the following web skeleton code into the blank document and make sure to change this line `<canvas data-processing-sources="MyGame.pde"></canvas>` to match your games actual filename:

``` html
<!DOCTYPE html>
<html>
<head>
    <title>My Processing Web Game</title>
    <style>
        body {
            background-color: #0c141c;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }
        canvas {
            outline: none;
        }
    </style>
    <script src="processing.min.js"></script>
</head>
<body>

    <canvas data-processing-sources="MyGame.pde"></canvas>

</body>
</html>
```

3. Click **File** -> **Save As...**
4. Navigate into your `web-game` folder.
5. Change the file type dropdown from *Text Documents (*.txt)* to *All Files* (if using Notepad).
6. Name the file exactly `index.html` and click **Save**.

---

## Step 5: Fix Naming Conflicts and Web Crashes
Desktop Processing allows a few coding structures that will completely freeze or crash when translated into a web browser environment. Open your `.pde` file in a text editor to verify these two critical areas:

### 1. Avoid Variable and Function Name Clashes
In standard desktop Java, you can sometimes get away with declaring a variable and a function with identical names. On the web, this will immediately break the compilation engine. Ensure you do not have overlapping names like this:

``` java
// ❌ BROKEN CODE (Will crash on the web):
int numbers;
void numbers() {
  // code
}

//  FIX: Give your variable a distinct name:
int numbers;
void countNumbers() {
  // code
}
```

### 2. Check image loading
Make sure that you check that all your images are loaded like this in the `steup()`.

``` java
void setup(){
  Menu = loadImage("data/Menu.png");
  logo = loadImage("data/Logo.png");
  MenuAsset = loadImage("data/MenuBackgroundAsset.png");
}
```


---
## Step 6: Create a GitHub Repository and Upload Your Game
Now that your game files are ready in your `web-game` folder, we need to put them on GitHub so the rest of the world can see and play your game. 

### 1. Create a GitHub Account
If you don't have one already, go to github.com and click the **Sign up** button in the top right corner. Follow the prompts to create your free account.

### 2. Create a New Repository
A repository (or "repo") is essentially a cloud folder where GitHub stores your website files.
1. Log into your GitHub account.
2. In the top-right corner of the screen, click the **+** (plus) icon and select **New repository**.
3. Name your repository something clean, like `my-processing-game`.
4. Leave the repository setting as **Public** (GitHub Pages will not work on free accounts if the project is private).
5. **Do not** check any of the boxes that say "Add a README file", "Add .gitignore", or "Choose a license". We want this repository completely blank.
6. Scroll down and click the green **Create repository** button.

### 3. Create the Asset Folder on GitHub (The Folder Trick)
You cannot drag and drop or upload an empty folder directly through the GitHub website interface. To get your `data` folder onto the web, you have to use a quick text trick to force GitHub to create it:

1. On the screen that appears after creating your repo, look closely at the top setup instructions and click the link that says **creating a new file**.
2. Look at the text box where it asks for your file name. Type the word `data` and then immediately type a forward slash `/` on your keyboard.
3. You will see the box instantly change, showing that you just created a folder named `data`!
4. Inside that file name box, right after the slash, type a placeholder file name like `placeholder.txt`.
5. Scroll up to the top of the page and click the green **Commit changes** button. 

### 4. Upload Your Files
Now that the structure is built, you can upload all your loose assets into their correct spots:

1. Click on the name of your repository at the top left to go back to your main project screen. You will see your `data` folder listed there.
2. Click the **Add file** button near the top right and select **Upload files**.
3. Open your computer's file manager, open your local `web-game` folder, and select your loose root files: `index.html`, `processing.min.js`, and your `.pde` game file. 
4. Drag and drop those files into the GitHub box, scroll down, and click **Commit changes**.
5. Go back to your main repository screen and click directly inside your online `data` folder.
6. Click **Add file** -> **Upload files** again while inside this folder.
7. Open your local `data` folder on your computer, select all your image and sound assets (like `player.png` and `jump.wav`), drag them into the browser, and click **Commit changes**.

---

## Step 7: Turn On GitHub Pages
GitHub Pages turns your repository folder into a live, interactive website.

1. At the top of your GitHub repository screen, look at the row of tabs under your project name and click on the **Settings** tab (the gear icon on the far right).
2. On the left-hand sidebar menu, scroll down to the "Code and automation" section and click on **Pages**.
3. Look for the **Build and deployment** section in the center of the screen. Under the "Source" header, make sure it says **Deploy from a branch**.
4. Right below that, locate the **Branch** dropdown menu (it will currently say `None`). Click it and change it to `main` (or `master`).
5. Leave the folder dropdown right next to it set to `/ (root)`.
6. Click the **Save** button.

### Where is My Website Link?
After you click save, GitHub needs about 1 to 2 minutes to compile your files into a real website. 
* To find your link, look at the top of that same **Pages** screen. Refresh the page after a minute, and you will see a box appear at the top that says: **"Your site is live at..."** followed by a URL link. 
* The link format will always look like this: `https://your-username.github.io/my-repository-name/`
* Click that link, and your game will load right up in your web browser!

---

## Step 8: Fixing File Saving and Loading (Web Storage)
On your desktop computer, Processing can easily save and load game files to your hard drive using commands like `createWriter()` and `createReader()`. However, once your game runs inside a web browser, security sandboxes completely block your code from reading or writing files on a player's machine.

To save player data (like high scores, current level progress, or item collections) on the web, you must use the web browser's built-in memory bank called **localStorage**. 

Open your `.pde` code file and apply these quick browser storage replacements:

### 1. How to Save Data to the Web Browser
Instead of writing text files to a hard drive, use `window.localStorage.setItem()` to store values directly in the user's browser memory. 

Pass your save slot identifier name first, followed by the string or number value you want to lock down:

``` java
// ❌ OLD DESKTOP WAY (Will crash on web):
PrintWriter write = createWriter("save.txt");
write.println(level);
write.close();

//  THE NEW WEB WAY:
window.localStorage.setItem("qb_currentLevel", str(currentLevel));
window.localStorage.setItem("qb_currentDeaths", str(deathsCount));
```

### 2. How to Load Saved Data on Startup
To read your progress data when your game reboots, use `window.localStorage.getItem()`. 

Because web storage stores everything as plain text strings, make sure to convert it back into integers or floats using functions like `int()` right after fetching it:

``` java
// ❌ OLD DESKTOP WAY (Will crash on web):
String line = "";
try {
  BufferedReader read = createReader("save.txt"); // Read the file
  if (read == null) {       // If the file doesn't exist
    throw new IOException("file does not exist");      // Prevents the NullPointerException error by throwing the error
  }
  line = read.readLine();        // Reads the file for the current level
  currentLevel = int(line);      // Sets the current level
  read.close();  
}
catch(IOException e) {
  // just doesnt do anything if there is an error
}

//  THE NEW WEB WAY:
Object savedLevel = window.localStorage.getItem("qb_currentLevel");
if (savedLevel != null) {
  currentLevel = int(savedLevel.toString());
} else {
  currentLevel = 1; // Default starting level if no save exists yet
}
```

---

## Step 9: Fixing Web Audio Crashes (The Final Step)
If your game uses standard desktop audio libraries like `processing.sound.*` or looks up absolute folders with `SoundFile()`, your project will instantly freeze or fail silently on a live web server. 

To give your game flawless, stutter-free sound effects on the web, follow these two final structural changes inside your `.pde` file:

### 1. Change your audio file data types
Go to the top of your file where you declare your sound variable names and replace the data type from `SoundFile` to `Object`:

``` java
// Desktop way
SoundFile woosh, hit, crawl, damage, walk, spit, fly, wall, music;

// Web way
Object woosh, hit, crawl, damage, walk, spit, fly, wall, music;
```


### 2. Clean Up Audio Loading
Go inside your `setup()` function. Ensure your audio tracking variables are configured to load as clean path strings:

``` java
// Fix: Assign paths as direct text entries
music = "data/background-Sci-Fi.mp3";
jump  = "data/jump.wav";
```
### 2. Paste the Web Audio Helper Functions
Scroll to the **absolute bottom** of your `.pde` game code file and paste this block of controller functions. This system bypasses Processing's heavy desktop compiler dependencies and uses the web browser's native HTML5 audio components directly:

```
// --- COMPILER-SAFE WEB AUDIO HELPERS ---
void playSound(Object sound) {
  if (sound == null) return;
  String url = (String) sound;
  String slot = getSoundSlot(url);
  window.eval(
    "if(!window." + slot + ") { window." + slot + " = new Audio('" + url + "'); }" +
    "window." + slot + ".currentTime = 0; window." + slot + ".volume = 0.5; window." + slot + ".play().catch(function(e){});"
  );
}

void loopSound(Object sound) {
  if (sound == null) return;
  String url = (String) sound;
  String slot = getSoundSlot(url);
  window.eval(
    "if(!window." + slot + ") { window." + slot + " = new Audio('" + url + "'); window." + slot + ".loop = true; }" +
    "window." + slot + ".volume = 0.4; window." + slot + ".play().catch(function(e){});"
  );
}

void loopSoundClean(Object sound) {
  if (sound == null) return;
  String url = (String) sound;
  String slot = getSoundSlot(url);
  window.eval(
    "if(!window." + slot + ") { window." + slot + " = new Audio('" + url + "'); }" +
    "if(window." + slot + ".paused || window." + slot + ".currentTime > 0.22) {" +
    "  window." + slot + ".currentTime = 0; window." + slot + ".volume = 0.3; window." + slot + ".play().catch(function(e){});" +
    "}"
  );
}

void stopSound(Object sound) {
  if (sound == null) return;
  String url = (String) sound;
  String slot = getSoundSlot(url);
  window.eval("if(window." + slot + ") { window." + slot + " = null; window." + slot + ".pause(); window." + slot + ".currentTime = 0; }");
}

boolean isSoundPlaying(Object sound) {
  if (sound == null) return false;
  String url = (String) sound;
  String slot = getSoundSlot(url);
  Object state = window.eval("window." + slot + " ? !window." + slot + ".paused : false;");
  return boolean("" + state);
}

String getSoundSlot(String url) {
  // Matches your unique filenames to a safe browser registration variable
  if (url.indexOf("jump") != -1) return "_sndJump";
  if (url.indexOf("Sci-Fi") != -1) return "_sndMusic";
  return "_sndGeneric";
}
```
*(Be sure to customize the keyword checks inside `getSoundSlot` to look for the real filenames inside your own game's data folder and replace it with the correct file names!)*

---

## Step 10: Crucial Web Deployment Rules
Once you push your updated script files over to GitHub, remember these final browser quirks to save yourself hours of troubleshooting:

* **Click the Webpage to Activate Audio:** Modern internet browsers purposefully block audio tracks from playing automatically until a user clicks on the website window. Always include a "Click to Start" menu screen or an initial button in your game loop so users activate the sound engine naturally!
* **The Hard Refresh Trick:** Web browsers love to cache (save) old web scripts locally on your machine to save download speeds. If you upload a quick fix to your code on GitHub but the website still displays your old code bugs, your browser is using its memory. Press **Ctrl + F5** on Windows (or **Cmd + Shift + R** on Mac) to completely flush out the cached storage and force your newest updates onto your display.













