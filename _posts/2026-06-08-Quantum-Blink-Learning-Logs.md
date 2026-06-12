---
title: "Quantum Blink Learning Logs"
date: 2026-06-08
categories: [Devlog, Quantum Blink]
tags: [Processing, Game Dev, Computer Science, Portfolio]
math: true
---

# Quantum Blink - Final Project Learning Logs

This post compiles the 5 individual learning log entries required for the ICS3U Final Programming Project. Each entry focuses on a specific programming concept implemented during the development of *Quantum Blink*.

# Quantum Blink - Final Project Learning Logs

This post compiles the 5 individual learning log entries required for the ICS3U Final Programming Project.

---

## Play Quantum Blink Live

Test your reflexes and use your short-range teleporter to phase through contaminated sectors directly in the terminal below! 

* **Controls:** Use **WASD** or **Arrow Keys** to move/jump, and press **SPACE** or **Z** to blink through enemies and eliminate them.

<div style="position: relative; width: 100%; padding-bottom: 66.66%; height: 0; overflow: hidden; border: 3px solid #00f0ff; border-radius: 12px; background: #0c141c; box-shadow: 0 0 20px rgba(0, 240, 255, 0.25); margin: 25px 0;">
  <iframe 
    src="https://783009.github.io/Quantum-Blink/" 
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: none;"
    allow="fullscreen">
  </iframe>
</div>

### 🖥️ Full Screen Presentation Terminal
For the best visual scaling, zero input lag, and full-resolution presentation gameplay, launch the standalone application tab:

👉 **[Launch Quantum Blink in Full Screen Mode](https://783009.github.io/Quantum-Blink/)**

---


---

## Log 1: Variables & Data Tracking

### a) Concept Implemented

I learned that you can dynamically change which image is stored withing a PImage variable. Instead of permanently binding an image variable to a single static file, you can treat the variable as a dynamic pointer that re-routes to different sprite indices or array elements depending on active gameplay conditions.

---

``` java
PImage standing[] = new PImage[2];
PImage jumping[] = new PImage[2];
PImage falling[] = new PImage[2];
PImage walking[] = new PImage[6];
PImage afterImage;
// Save the current layer image to a different image variable
  if(touchingGround){
    if(lastClicked.equals("Right"))
      afterImage = walking[frame];
    else
      afterImage = walking[frame+3];
  } else {
    if(speedY > 3){
      if(lastClicked.equals("Right"))
        afterImage = falling[0];
      else
        afterImage = falling[1];
    } else {
      if(lastClicked.equals("Right"))
        afterImage = jumping[0];
      else
        afterImage = jumping[1];
    }
  }
```

### b) Application to Game
I implemented this variable logic because I needed to save a precise snapshot of the player's sprite the exact moment they die. This leaves behind a translucent, ghost-like "afterimage" at the point of failure, serving as a helpful visual marker that shows the player exactly where their last run ended. By using the selection logic above, the `afterImage` variable captures the exact frame state—whether the scientist was running left, jumping right, or falling down a pit—and preserves that specific frame independently on screen.

### c) Challenges & Fixes
Before implementing this setup, I ran into a major logic issue where the afterimage placeholder wouldn't freeze properly. I originally tried to just load a generic default sprite reference right when the death sequence triggered, but the player's post-death velocity and current movement keys were still actively bleeding into the state check. This caused the ghost image to change its facing direction or switch animations while fading out, entirely ruining the "frozen in time" effect.

I fixed this by using the conditional structure to evaluate the physics states (`touchingGround`, `speedY`, `lastClicked`) at the absolute instant of impact. Assigning that exact calculation directly to the `afterImage` variable safely isolated it from any subsequent player inputs or movement calculations.


## Log 2: Selection Structure

### a) Concept Implemented
I implemented a multi-branch conditional structure using nested `if`, `else if`, and sequential comparison statements. This setup allows the program to evaluate multiple true/false states in a specific execution order to handle physical space restrictions. I also combined these structures with Processing's built-in math functions to choose a reaction path based on changing coordinate parameters.

```java
// Draws a wall and handles all collisions
void Wall(int x1, int x2, int y1, int y2){
  rectMode(CORNERS);
  fill(0, 0, 10);
  noStroke();
  rect(x1, y1, x2, y2);
  // First check if the player teleported into the wall using the player's middle point
  if(checkCollision(50, 50, 75, 75, x1, x2, y1, y2)){
    reset();
  }
  // Check if inside the wall
  else if(checkCollision(100, 0, 150, 0, x1, x2, y1, y2)){
    // Check which side is closest to push the player to
    distX1 = SX+100-x1;
    distX2 = x2-SX;
    distY1 = SY+150-y1;
    distY2 = y2-SY;
    float[] array = {distX1, distX2, distY1, distY2};
    smallest = min(array);
    
    // Pushes the player to the closest side
    if(smallest == distY1){
      SY = y1 - 150;
      speedY = 0;
      canBlink = true;
      touchingGround = true;
    }
    else if(smallest == distY2){
      SY = y2;
      speedY = 0;
    }
    else if(smallest == distX1){
      SX = x1 - 100;
    }
    else if(smallest == distX2){
      SX = x2;
    }
  }
}
```

### b) Why I Did This
I implemented this selection structure to create a dynamic, axis-aligned collision response system for our platformer's terrain blocks. The primary goal was to ensure that whenever the scientist player overlaps with a solid wall object, the game engine can immediately determine which side of the wall was hit (top, bottom, left, or right) and cleanly eject the player. Furthermore, the selection structure handles environmental status changes depending on the intersection location: if the structure evaluates that the player landed on top of the wall (`smallest == distY1`), it triggers unique state changes like turning off falling physics (`speedY = 0`), registering that the player is stable (`touchingGround = true`), and completely recharging the teleport ability (`canBlink = true`).

### c) Challenges & fixes
Before writing this dynamic multi-branch setup, my collision logic was completely broken around the corners of platforms. Originally, I was trying to hardcode checks based on the player's position relative to the middle of the wall, using checks like `if(SX+100 >= x1 && SX+100 <= halfWidth)`. The issue with that hardcoded approach was that it failed completely at corner intersections. When jumping or moving diagonally straight into the corner edge of a solid tile, the code would encounter a logical stalemate where neither condition could cleanly solve the layout boundaries, causing the wall to do nothing at all. The player could walk or jump straight into the tiles, clipping deeply inside the blocks.

I fixed this by stripping out the fixed midpoint conditions and implementing an overlap ejection model. The new selection structure works by calculating the absolute penetration distances from all four outer boundaries into a array (`float[] array`) and using the min() function to find the absolute shortest exit path. The nested `if / else if` block then evaluates that single smallest value to push the player out along the cleanest edge, transforming the corner collision logic into a responsive and glitch-free boundary barrier.



## Log 3: Repetition Structure

### a) Concept Implemented
I implemented a series of counted repetition structures using standard `for` loops combined with structural null-checking restrictions. This setup enables the game to dynamically run iterative processing loops over dynamic collections of custom object references based on the sizing parameters of the target array, adjusting its execution cycles automatically at runtime.

``` java
// Enemies
if(Deepling != null){
  for(int i = 0; i < Deepling.length; i++){
    Deepling[i].move();
    Deepling[i].attack();
  }
}
if(Aspid != null){
  for(int i = 0; i < Aspid.length; i++){
    Aspid[i].attack();
  }
}
```
### b) Why I Did This
I implemented these repetition structures inside the core drawing loop to handle the rendering, movement tracking, and hit-box checking for all active hazards on a given map. Because our design scales up in difficulty, the total number of enemies changes drastically from room to room—Level 1 only features a single crawling hazard, while Level 7 instantiates five separate flying enemies simultaneously. Using a standard `for` loop combined with the `.length` property allows the engine to adapt automatically to whatever index is allocated for that specific level, ensuring every active enemy is properly updated and processed every frame without duplicating code manually for individual enemies.

### c) My Issues
Before introducing this looping structure alongside its tracking defenses, the game engine suffered from fatal stability crashes whenever changing maps. During development, if a room did not spawn any flying enemies (like Level 1, which only uses one crawling enemy), the engine would crash instantly with a `NullPointerException` the millisecond the drawing ran. This happened because the loop was trying to read the `.length` property of an empty, uninitialized array reference that didn't exist. 

I resolved this issue by wrapping each individual loop inside a structural safety net check using the syntax `if(Deepling != null)` and `if(Aspid != null)`. This acts as an explicit conditional guard that forces the repetition loops to execute only if the target arrays actually contain enemies, protecting the main program loop from crashing on single-enemy rooms.



## Log 4: Arrays & Data Structures

### a) Concept Implemented
I learned that arrays can be used to hold collections of complex custom object classes (like `Deepling[]` and `Aspid[]`) as well as lists of engine-specific structural object types such as `PImage` variables. 

```java
// Global declaration of image arrays
PImage standing[] = new PImage[2];
PImage jumping[] = new PImage[2];
PImage falling[] = new PImage[2];
PImage walking[] = new PImage[6];

void setup(){
  size(1200, 800);
  
  // Array assignments for right-facing movement frames
  standing[0] = loadImage("Standing.png");
  walking[0] = loadImage("Walking_1.png");
  walking[1] = loadImage("Walking_2.png");
  walking[2] = loadImage("Walking_3.png");
  jumping[0] = loadImage("Jumping.png");
  falling[0] = loadImage("Falling.png");
  
  // Array assignments for left-facing (Flipped) movement frames
  standing[1] = loadImage("StandingF.png");
  walking[3] = loadImage("Walking_1F.png");
  walking[4] = loadImage("Walking_2F.png");
  walking[5] = loadImage("Walking_3F.png");
  jumping[1] = loadImage("JumpingF.png");
  falling[1] = loadImage("FallingF.png");
}
```


### b) Why I Did This
I implemented arrays for two critical systems in *Quantum Blink*: tracking enemy groups dynamically and organizing player movement graphics. 

For our character system, I initialized several `PImage` arrays (`walking[]`, `standing[]`, `jumping[]`, `falling[]`) to act as indexed asset sheets. By utilizing arrays, I can link player animation states directly to an incrementing layout loop counter variable (`frame`). This allows the code to easily swap between active visual states every few ticks rather than calling heavy individual file load processes, keeping our game neat and organized.

### c) My Issues
While dealing with the tracking logic for the arrays, I ran into an irritating glitch where the scientist would face the wrong direction halfway through his stride. The first two frames of walking right looked fine, but on the third frame, he would suddenly turn around completely out of nowhere before swinging back forward. 

When looking back at my initialization code, I realized I accidentally flipped the asset names for the array paths: `walking[0]`, `walking[1]`, and `walking[2]` were mapped to my right-facing sprites, but I had accidentally mixed a left-facing sprite asset (`Walking_3F.png`) into a right-facing array index slot. This structural confusion caused the engine to grab and draw a backwards asset when checking sequential frames inside the `moveRight()` logic. 

I fixed this by properly separating my array elements so that elements `0-2` strictly contain right-facing assets while elements `3-5` hold the mirrored left-facing assets (`Walking_1F.png` through `Walking_3F.png`). I then updated the movement rendering lines using standard offset modifiers, calling `walking[frame]` for rightward steps and `walking[frame+3]` for leftward steps, which completely resolved the animation glitch.


## Log 5: Use of Custom Functions & Error Checking/Restrictions

### a) Concept Implemented
I learned how to write custom functions that break my code down into smaller, organized pieces. I also learned how to use a `try-catch` block along with error checking to handle files safely. This lets the game look for external files without completely breaking or freezing up if those files are missing.

``` java
// Read "save.txt" to get the saved game
String line = "";
try {
  BufferedReader read = createReader("save.txt"); // Read the file
  if (read == null) {       // If the file doesn't exist
    hasSave = false;
    throw new IOException("file does not exist");      // Prevents the NullPointerException error by throwing the error
  }
  hasSave = true;
  line = read.readLine();        // Reads the file for the current level
  currentLevel = int(line);      // Sets the current level
  line = read.readLine();        // Reads for the current deaths
  currentDeaths = int(line);     // Sets the current deaths
  line = read.readLine();        // Reads for the current time
  startElapsed = int(line);      // Sets the current time
  read.close();  
}
catch(IOException e) {
  // just doesnt do anything if there is an error
}
```

### b) Why I Did This
I used these functions to handle the save file system in *Quantum Blink*. Inside `setup()`, I used a `BufferedReader` to check `save.txt` right when the game starts. By adding the condition `if (read == null)`, the game can figure out if a player has played before. 

If it finds a file, it sets a variable called `hasSave` to true. This is super important because my main menu uses that variable to decide whether or not to show the "Continue" button. If it's true, you get to continue your game; if not, it stays hidden so you can only click "New Game."

### c) My Issues
The biggest headache I faced was that the game would instantly crash for any first-time player. If someone downloaded the game and didn't have a pre-existing `save.txt` file in their folder, the `BufferedReader` would search for it, find nothing, and throw a fatal `NullPointerException` that shut down the whole program.

I fixed this issue by wrapping the file loader inside a `try-catch` block and adding a strict restriction. Now, if `read == null` triggers, the code sets `hasSave = false` so the menu knows not to show the continue button, and then it manually throws an `IOException`. The `catch` block grabs that error and handles it quietly in the background instead of letting it crash the software. This keeps the game completely stable for new players while still loading up everything perfectly for returning players.













