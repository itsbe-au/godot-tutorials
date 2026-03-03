# Lesson 1: Project Setup & Godot Basics

**Welcome to Game Development!** 🎮

In this lesson, you'll set up your first Godot 4.6 project and learn the basics of the engine. By the end of this 45-60 minute lesson, you'll have a working game window with your first sprite on screen!

---

## What We're Building: The Full RPG Series

This is the **first lesson** in a series where you'll build a complete 2D RPG game from scratch! Here's what we'll create over the next few lessons:

| Lesson | Topic | What You'll Build |
|--------|-------|-------------------|
| **Lesson 1** | Project Setup & Godot Basics | Your first project, understanding the editor |
| **Lesson 2** | Player Character | A character that moves with keyboard input |
| **Lesson 3** | Tilemaps & World Building | Create game worlds with tiles |
| **Lesson 4** | Animations | Smooth character movement animations |
| **Lesson 5** | Enemies & Combat | Simple combat system |
| **Lesson 6** | UI & Game States | Menus, health bars, game over screens |
| **Lesson 7** | Items & Inventory | Collect items and manage inventory |
| **Lesson 8** | Sound & Polish | Add music and sound effects |
| **Lesson 9** | Exporting & Sharing | Build your game for others to play |

By the end of this series, you'll have a fully playable RPG game you can share with friends and family!

---

## Step 1: Installing Godot 4.6

### Download Godot

1. Go to the official Godot website: **https://godotengine.org/**
2. Click the big "Download" button
3. Look for **Godot 4.6** (or the latest stable version)
4. Download the **Standard version** (NOT the .NET version unless you know C#)

### What's in the Download?

Godot is **portable** - it doesn't need installation! You'll get:
- A single executable file (on Windows: `Godot_v4.6.exe`, on Mac: `Godot.app`)
- That's it! No complex installation required

### Opening Godot for the First Time

**Windows:**
- Double-click the `.exe` file

**Mac:**
- Right-click the `.app` file and select "Open"
- If you get a security warning, go to **System Preferences > Security & Privacy** and click "Open Anyway"

**Linux:**
- Make the file executable: `chmod +x Godot_v4.6.x86_64`
- Run it: `./Godot_v4.6.x86_64`

---

## Step 2: Creating Your First Project

When Godot opens, you'll see the **Project Manager**. This is where you create and open projects.

### Creating a New Project

1. Click the **"New Project"** button
2. In the dialog that appears:
   - **Project Name:** `MyFirstRPG` (or any name you like!)
   - **Project Path:** Choose where to save your project (like `Documents/GodotProjects/`)
   - **Renderer:** Keep it on "Forward+" (this is for 3D, but works great for 2D too)
   - **Version Control Metadata:** Check "Git" (we'll use this later!)
3. Click **"Create & Edit"**

### Understanding the Project Structure

Godot creates a folder for your project containing:
```
MyFirstRPG/
├── project.godot          # Main project file (don't delete this!)
├── icon.svg               # Project icon
├── .godot/                # Internal engine files (don't touch!)
└── (your files go here)
```

---

## Step 3: Understanding the Godot Editor Interface

Welcome to the **Godot Editor**! Let's break down what you're seeing:

### Main Areas

```
┌─────────────────────────────────────────────────────────┐
│  Scene      Inspector                                   │
│  ┌─────┐   ┌─────────────────┐                         │
│  │     │   │                 │                         │
│  │     │   │    VIEWPORT     │  2D/3D/Script/AssetLib  │
│  │     │   │   (Your Game)   │                         │
│  │     │   │                 │                         │
│  │     │   └─────────────────┘                         │
│  └─────┘   ┌─────────────────┐                         │
│            │   FileSystem    │                         │
│            │   (Your Files)  │                         │
│            └─────────────────┘                         │
└─────────────────────────────────────────────────────────┘
```

### Panel Breakdown

| Panel | Location | What It Does |
|-------|----------|--------------|
| **Scene Dock** | Top-Left | Shows the node tree of your current scene |
| **FileSystem Dock** | Bottom-Left | Browse your project files |
| **Inspector** | Right Side | Edit properties of selected nodes |
| **Viewport** | Center | See and edit your game |
| **Bottom Panel** | Bottom | Output, Debugger, Animation, etc. |

### Toolbar Buttons

- **2D/3D/Script/AssetLib** - Switch between editing modes
- **Play Buttons** (top-right) - Run your game
  - ▶️ Play (F5) - Run the main scene
  - ▶️ Play Scene (F6) - Run current scene only
  - ⏹️ Stop (F8) - Stop the game

---

## Step 4: Creating Your First Scene

In Godot, everything is organized into **scenes**. Think of a scene like a level, a menu, or a character.

### Creating a Scene

1. Make sure you're in **2D mode** (click the "2D" button in the toolbar)
2. Look at the **Scene dock** (top-left)
3. Click the **"2D Scene"** button (or go to Scene > New Scene)

### Adding Your First Node

A scene needs at least one **root node**. Let's add a Node2D:

1. In the Scene dock, you'll see "Create Root Node:"
2. Click **"2D Scene"** (this creates a Node2D)
3. **Rename it:** Double-click "Node2D" and type "Main"

### Understanding Nodes

Nodes are the building blocks of your game. Each node has:
- A **type** (what it does)
- **Properties** (settings you can change)
- **Children** (other nodes attached to it)

Common node types:
- **Node2D** - Basic 2D object (position, rotation, scale)
- **Sprite2D** - Displays an image
- **CharacterBody2D** - A character that can move and collide
- **Camera2D** - Controls what the player sees

### Adding a Sprite

Let's add an image to your scene:

1. **Download a sprite:**
   - Go to **https://opengameart.org/** or use the provided `assets/player.png`
   - Save it to your project folder

2. **Add a Sprite2D node:**
   - With "Main" selected, click the **"+"** button (or Ctrl+A)
   - Search for "Sprite2D"
   - Click "Create"
   - Rename it to "PlayerSprite"

3. **Add the texture:**
   - With PlayerSprite selected, look at the **Inspector** (right side)
   - Find the **Texture** property
   - Click "<empty>" and select "Load"
   - Choose your image file

### Positioning Your Sprite

In the **Viewport** (center), you can:
- **Click and drag** the sprite to move it
- **Click the green circle** and drag to rotate
- **Hold Shift** while dragging to snap to grid

The blue cross in the center is the **origin** (0,0 position).

---

## Step 5: Running Your Game

Time to see your creation in action!

### Saving Your Scene

1. Press **Ctrl+S** (or Cmd+S on Mac)
2. Name it `main.tscn`
3. Save it in the project root

### Setting the Main Scene

Before running, tell Godot which scene to start with:

1. Go to **Project > Project Settings**
2. Click on **Application > Run**
3. Next to "Main Scene", click the folder icon
4. Select `main.tscn`
5. Click "Open"
6. Close the settings window

### Run Your Game!

Click the **Play button** (▶️) in the top-right or press **F5**

A new window should open showing your sprite! 🎉

### Stopping the Game

- Click the window's X button, OR
- Press **F8**, OR
- Click the **Stop button** (⏹️) in the editor

---

## Step 6: Understanding the Node Tree

The **Scene dock** shows your scene's node tree. This is crucial to understand!

### Node Tree Basics

```
Main (Node2D)           ← Root node
└── PlayerSprite (Sprite2D)  ← Child node
```

- **Root node:** The top-most node (every scene has one)
- **Child nodes:** Attached to other nodes
- **Parent-child relationship:** Moving the parent moves all children!

### The Scene Tree Concept

Think of nodes like folders on your computer:
- A folder can contain other folders
- If you move a folder, everything inside moves too
- Each folder has its own properties

### Why This Matters

**Parent-child inheritance:**
- If you move "Main", "PlayerSprite" moves with it
- If you rotate "Main", "PlayerSprite" rotates too
- This makes organizing complex objects easier!

### Try It Yourself

1. Select "Main" in the Scene dock
2. In the Inspector, change **Position** to (100, 50)
3. Notice how PlayerSprite moves too!
4. Undo with **Ctrl+Z**

---

## Step 7: Project Settings Configuration

Let's configure some important project settings.

### Opening Project Settings

Go to **Project > Project Settings**

### Display Settings

**Window Size:**
1. Navigate to **Display > Window**
2. Under "Size":
   - **Viewport Width:** `1280`
   - **Viewport Height:** `720`
3. This sets your game resolution

**Stretch Mode (Important for pixel art!):**
1. Under "Stretch":
   - **Mode:** `canvas_items` (scales everything nicely)
   - **Aspect:** `expand` (fills the screen)

### Application Settings

**Game Information:**
1. Navigate to **Application > Config**
2. Set:
   - **Name:** `My First RPG`
   - **Description:** `A learning project`
   - **Version:** `0.1`

### Input Settings

We'll set up input actions so your player can move!

1. Navigate to **Input Map** (at the top)
2. We'll add four actions: `move_left`, `move_right`, `move_up`, `move_down`

### Adding move_left

1. In "Action" field, type: `move_left`
2. Click **"Add"**
3. Click the **"+"** next to move_left
4. Select **"Key"**
5. Press the **Left Arrow** key on your keyboard
6. Click "OK"

### Adding move_right

1. Type: `move_right`
2. Click "Add"
3. Click "+" next to move_right
4. Select "Key"
5. Press **Right Arrow**
6. Click "OK"

### Adding move_up

1. Type: `move_up`
2. Click "Add"
3. Click "+" next to move_up
4. Select "Key"
5. Press **Up Arrow**
6. Click "OK"

### Adding move_down

1. Type: `move_down`
2. Click "Add"
3. Click "+" next to move_down
4. Select "Key"
5. Press **Down Arrow**
6. Click "OK"

### Alternative Controls (Optional)

You can add multiple keys for each action! Let's add WASD:

1. Find `move_left` in the list
2. Click "+" again
3. Select "Key"
4. Press **A**
5. Repeat for other directions:
   - `move_right`: Add **D**
   - `move_up`: Add **W**
   - `move_down`: Add **S**

Now players can use arrow keys OR WASD!

### Save Settings

Just close the Project Settings window - changes are saved automatically.

---

## Step 8: Project Folder Organization

Keeping your project organized from the start saves headaches later!

### Recommended Folder Structure

Create these folders in your project:

```
MyFirstRPG/
├── project.godot          # Main project file
├── icon.svg               # Project icon
├── main.tscn              # Your main scene
│
├── assets/                # All game art and sounds
│   ├── sprites/           # Character and object images
│   ├── tiles/             # Tilemap images
│   ├── backgrounds/       # Background images
│   └── audio/             # Music and sound effects
│
├── scenes/                # Scene files (.tscn)
│   ├── player.tscn
│   ├── enemy.tscn
│   └── level1.tscn
│
├── scripts/               # Code files (.gd)
│   ├── player.gd
│   └── enemy.gd
│
└── resources/             # Other resources
    └── materials/
```

### Creating Folders

1. Look at the **FileSystem dock** (bottom-left)
2. Right-click in the empty space
3. Select **"New Folder"**
4. Name it (e.g., "assets")
5. Repeat for other folders

### Moving Files

To move files into folders:

1. In FileSystem, drag and drop files
2. Or right-click a file > "Move To"
3. Godot will ask if you want to update references - say **Yes**!

### Pro Tips

- **Use lowercase names** for folders (prevents issues on some systems)
- **No spaces** in folder/file names (use underscores: `my_file.png`)
- **Group by type** (all sprites together, all scripts together)
- **Name descriptively** (`player_idle.png` not `img1.png`)

---

## Step 9: Practice Activities

Test your knowledge with these activities!

### Activity 1: Create Multiple Scenes (15 min)

1. Create a new scene called `menu.tscn`
2. Add a Node2D root named "MainMenu"
3. Add 3 Sprite2D nodes as children
4. Position them in different places
5. Save and run it

### Activity 2: Organize Your Project (10 min)

1. Create the folder structure from Step 8
2. Move your sprite image to `assets/sprites/`
3. Godot will ask about updating references - click "Fix Dependencies"

### Activity 3: Custom Input (10 min)

1. Open Project Settings > Input Map
2. Add a new action called `jump`
3. Assign it to the Spacebar
4. Assign it to the Up Arrow as well
5. Think about: What game could use a jump action?

### Activity 4: Experiment with Properties (15 min)

1. Select your Sprite2D
2. In the Inspector, try changing these:
   - **Modulate** (color tint)
   - **Scale** (size - try 2,2 for double size!)
   - **Rotation** (angle in degrees)
   - **Z Index** (layer order)
3. Run the game to see the changes

---

## Troubleshooting

### Common Problems and Solutions

#### "My game window is black/blank!"

**Problem:** Nothing appears when you run the game

**Solutions:**
- Check that your scene is saved as `main.tscn`
- Verify the Main Scene is set in Project Settings
- Make sure your sprite has a texture assigned
- Check that the sprite's position isn't off-screen

#### "Godot won't open my project!"

**Problem:** Error when trying to open project

**Solutions:**
- Make sure `project.godot` file exists
- Check that you're using Godot 4.x (not 3.x)
- Try opening the project folder directly

#### "My sprite looks blurry/pixelated!"

**Problem:** Pixel art looks bad

**Solutions:**
1. Select your sprite in the FileSystem
2. In Import tab (next to Scene dock), change:
   - **Filter:** Nearest (for pixel art)
3. Click "Reimport"

#### "I can't find my files!"

**Problem:** Files don't appear in FileSystem

**Solutions:**
- Click the **"Refresh"** button (circular arrow) in FileSystem
- Make sure files are in the project folder (not Desktop/Downloads)
- Check that file extensions are correct (.png, .jpg, etc.)

#### "My changes don't save!"

**Problem:** Work is lost when closing Godot

**Solutions:**
- Save scenes with **Ctrl+S** frequently!
- Watch for asterisk (*) next to scene name - means unsaved changes
- Enable "Auto Save" in Editor Settings if you want

#### "The game crashes when I run it!"

**Problem:** Game window opens then closes

**Solutions:**
- Check the **Output panel** (bottom) for error messages
- Make sure you don't have infinite loops in scripts
- Verify all file paths are correct
- Try running a simpler scene first

### Getting More Help

If you're stuck:

1. **Check the Output panel** - it shows error messages
2. **Read the error carefully** - Godot errors are usually descriptive
3. **Search the error message** online
4. **Ask for help** - teachers, forums, or Discord
5. **Take a break** - sometimes stepping away helps!

---

## Preview of Next Lesson

Great job completing Lesson 1! 🎉

### What You've Learned

- ✅ Installed and opened Godot 4.6
- ✅ Created a new project
- ✅ Navigated the Godot Editor
- ✅ Created scenes with nodes
- ✅ Added and positioned sprites
- ✅ Configured project settings
- ✅ Set up input actions
- ✅ Organized your project

### What's Next: Lesson 2 - Player Character

In the next lesson, you'll:

1. **Create a player character** using CharacterBody2D
2. **Write your first code** in GDScript
3. **Make the player move** with keyboard input
4. **Add collision** so the player doesn't go through walls
5. **Set up a camera** that follows the player

### Homework

Before next class:

1. **Find or create a player sprite** (32x32 or 64x64 pixels works great)
2. **Organize your project folders** as shown in this lesson
3. **Experiment** with different node types in Godot
4. **Think about** what kind of RPG you want to make (fantasy? sci-fi? modern?)

### Resources to Explore

- **Godot Docs:** https://docs.godotengine.org/
- **GDQuest (YouTube):** Great Godot tutorials
- **KidsCanCode:** Beginner-friendly Godot content
- **OpenGameArt:** Free game assets
- **itch.io:** Free assets and inspiration

---

## Quick Reference Card

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| **F5** | Run game |
| **F6** | Run current scene |
| **F8** | Stop game |
| **Ctrl+S** | Save scene |
| **Ctrl+Shift+S** | Save all scenes |
| **Ctrl+A** | Add child node |
| **Ctrl+Z** | Undo |
| **Ctrl+Shift+Z** | Redo |
| **Delete** | Delete selected node |

### Important File Extensions

| Extension | Type |
|-----------|------|
| `.godot` | Project file |
| `.tscn` | Scene file |
| `.gd` | GDScript file |
| `.tres` | Resource file |

### Key Concepts

- **Scene:** A self-contained game object or level
- **Node:** Building block of scenes
- **Node Tree:** Hierarchical structure of nodes
- **Root Node:** Top-most node in a scene
- **Script:** Code that controls node behavior
- **Viewport:** The game view/camera area

---

## Glossary

| Term | Definition |
|------|------------|
| **Node** | A building block in Godot. Every object in your game is a node. |
| **Scene** | A collection of nodes saved as a file. Can be loaded and used multiple times. |
| **Sprite** | A 2D image displayed in the game. |
| **Viewport** | The visible area of your game. |
| **Inspector** | The panel where you edit node properties. |
| **GDScript** | Godot's programming language (similar to Python). |
| **Texture** | An image file used by sprites and other nodes. |
| **Resolution** | The size of your game window in pixels (e.g., 1280x720). |
| **Input Map** | Where you define what keys/buttons trigger game actions. |
| **Parent/Child** | Relationship where child nodes follow their parent's transformations. |

---

## Congratulations!

You've completed **Lesson 1: Project Setup & Godot Basics**! 

You now have:
- ✅ A working Godot 4.6 installation
- ✅ Your first project created and configured
- ✅ Understanding of the Godot Editor
- ✅ A scene with a sprite
- ✅ Working input actions
- ✅ An organized project structure

**Next up:** Lesson 2 - Player Character (coming soon!)

Keep practicing, stay curious, and happy game developing! 🎮✨

---

*Last updated: 2024*
*For Godot 4.6*
*Target audience: Ages 12-15*