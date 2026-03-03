# Lesson 8: Scene Transitions & Game States

**Time:** 45-60 minutes  
**Target:** Ages 12-15  
**Prerequisites:** Lessons 1-7 (Complete platformer with player, enemies, collectibles, UI, audio)

---

## Lesson Overview

Welcome to the final lesson! In this chapter, you'll transform your single-scene game into a complete, polished experience with multiple scenes, smooth transitions, and proper game states. By the end, you'll have a professional game with:

- A Main Menu with Play and Quit buttons
- Multiple levels you can progress through
- A Pause menu to take breaks
- A Game Over screen with score display
- Smooth fade transitions between scenes

Let's make your game feel complete!

---

## Part 1: Why We Need Multiple Scenes

### The Problem: Everything in One Scene

Right now, your game starts immediately when you press Play. There's no menu, no way to restart, and no way to pause. If you die, the game just... stops. Not very user-friendly!

### The Solution: Scene Separation

Professional games split different parts into separate scenes:

| Scene | Purpose |
|-------|---------|
| **Main Menu** | First screen players see - has Play, Options, Quit buttons |
| **Level 1** | Your main game scene |
| **Level 2+** | Additional levels with increasing difficulty |
| **Pause Menu** | Overlay that pauses the game |
| **Game Over** | Shows when player dies - Retry/Menu options |
| **Victory Screen** | Shows when player beats all levels |

### Benefits of Multiple Scenes

1. **Organization**: Each scene has one job
2. **Memory Management**: Only load what's needed
3. **Teamwork**: Different people can work on different scenes
4. **Modularity**: Add/remove levels easily

---

## Part 2: Creating Game Scenes

Let's build the scenes you need. Open your Godot project from Lesson 7.

### Step 1: Create a Main Menu Scene

**Why first?** When players open your game, they should see a menu, not immediately start playing.

1. Click **Scene > New Scene**
2. Choose **User Interface** (Control node) as root
3. Rename root node to "MainMenu"
4. Save as `res://scenes/ui/main_menu.tscn`

#### Designing the Main Menu

Add these child nodes to MainMenu:

```
MainMenu (Control)
├── Background (ColorRect)
│   ├── Color: #1a1a2e (dark blue)
│   └── Anchors: Full Rect
├── TitleLabel (Label)
│   ├── Text: "SUPER PLATFORMER"
│   ├── Theme Overrides > Font Size: 64
│   ├── Horizontal Alignment: Center
│   └── Position: Top center
├── PlayButton (Button)
│   ├── Text: "PLAY"
│   ├── Size: (200, 60)
│   ├── Position: Center
│   └── Theme Overrides > Font Size: 32
├── OptionsButton (Button)
│   ├── Text: "OPTIONS"
│   ├── Size: (200, 60)
│   └── Position: Below Play
└── QuitButton (Button)
    ├── Text: "QUIT"
    ├── Size: (200, 60)
    └── Position: Below Options
```

#### Styling Your Buttons

Make your buttons look good:

1. Select a Button
2. In Inspector, find **Theme Overrides > Styles**
3. Add a **Normal** style box:
   - Create new StyleBoxFlat
   - BG Color: #4a4e69
   - Corner Radius: All 8
4. Add a **Hover** style box:
   - Create new StyleBoxFlat
   - BG Color: #6d7085 (lighter)
   - Corner Radius: All 8

**Copy this style** to all three buttons!

### Step 2: Create Level 2 Scene

Time to make a second level! This shows players their progress.

1. Click **Scene > New Scene**
2. Choose **2D Scene** (Node2D) as root
3. Rename to "Level2"
4. Save as `res://scenes/levels/level2.tscn`

#### Building Level 2

Add these nodes to Level2:

```
Level2 (Node2D)
├── TileMapLayer (ground)
├── TileMapLayer (background)
├── Player (CharacterBody2D)
│   └── [Instance your player scene from Lesson 4]
├── Enemies (Node2D)
│   ├── Slime1
│   └── [Add more enemies than Level 1!]
├── Collectibles (Node2D)
│   ├── Coin1
│   └── [Add coins in tricky places]
├── LevelEnd (Area2D)
│   └── [Signals level completion]
├── CanvasLayer (UI)
│   └── [Instance your HUD from Lesson 6]
└── AudioStreamPlayer2D
    └── [Instance your background music]
```

#### Make Level 2 Different

- **Harder platforming**: Add bigger gaps between platforms
- **More enemies**: Place them strategically
- **Collectible challenges**: Hide coins in tricky spots
- **Theme change**: Use different TileMap colors

### Step 3: Create Game Over Scene

1. Click **Scene > New Scene**
2. Choose **User Interface** (Control) as root
3. Rename to "GameOver"
4. Save as `res://scenes/ui/game_over.tscn`

#### Game Over Layout

```
GameOver (Control)
├── Background (ColorRect)
│   ├── Color: #000000
│   └── Modulate: Alpha 0.8 (transparent black)
├── TitleLabel (Label)
│   ├── Text: "GAME OVER"
│   ├── Theme Overrides > Font Size: 72
│   └── Theme Overrides > Font Color: #ff0000
├── ScoreLabel (Label)
│   ├── Text: "Score: 0"
│   ├── Theme Overrides > Font Size: 48
│   └── Name: "ScoreLabel" (important for code!)
├── RetryButton (Button)
│   ├── Text: "TRY AGAIN"
│   └── Size: (200, 60)
└── MenuButton (Button)
    ├── Text: "MAIN MENU"
    └── Size: (200, 60)
```

---

## Part 3: Scene Changing Basics

Now let's write code to switch between scenes.

### Understanding Scene Paths

Godot uses `res://` to mean "project folder":

```gdscript
# These paths point to your scenes:
"res://scenes/ui/main_menu.tscn"
"res://scenes/levels/level1.tscn"  
"res://scenes/levels/level2.tscn"
"res://scenes/ui/game_over.tscn"
```

### Basic Scene Change

Add this script to your **Main Menu**:

```gdscript
# main_menu.gd - Attach to MainMenu root node
extends Control

@onready var play_button = $PlayButton
@onready var quit_button = $QuitButton

func _ready():
    play_button.pressed.connect(_on_play_pressed)
    quit_button.pressed.connect(_on_quit_pressed)
    
    # Optional: Add sound effects
    play_button.mouse_entered.connect(_on_button_hover)

func _on_play_pressed():
    # Change to Level 1
    get_tree().change_scene_to_file("res://scenes/levels/level1.tscn")

func _on_quit_pressed():
    # Close the game
    get_tree().quit()

func _on_button_hover():
    # You could play a hover sound here
    pass
```

**Test it now!** Run the Main Menu scene and click Play. You should see your level!

### How `change_scene_to_file()` Works

```gdscript
get_tree().change_scene_to_file("path/to/scene.tscn")
```

What happens:
1. Current scene is freed from memory
2. New scene is loaded
3. New scene becomes the active scene
4. `_ready()` runs on the new scene

⚠️ **Important**: The old scene is completely gone! All scores, variables, etc. are lost.

---

## Part 4: Creating Smooth Transitions

Sudden scene changes feel jarring. Let's add fade transitions!

### Step 1: Create a Transition Scene

This scene will fade in/out between scene changes.

1. **Scene > New Scene** > User Interface
2. Rename root to "Transition"
3. Save as `res://scenes/ui/transition.tscn`

#### Transition Scene Structure

```
Transition (CanvasLayer)
├── ColorRect (full screen black overlay)
│   ├── Anchors: Full Rect
│   ├── Color: #000000 (black)
│   └── Initial Alpha: 0 (fully transparent)
└── AnimationPlayer
    └── [We'll add animations next]
```

### Step 2: Create Fade Animations

Select the AnimationPlayer and create two animations:

#### Animation 1: "fade_to_black"
- Length: 0.5 seconds
- Keyframes:
  - 0.0s: ColorRect modulate Alpha = 0
  - 0.5s: ColorRect modulate Alpha = 1

#### Animation 2: "fade_from_black"
- Length: 0.5 seconds  
- Keyframes:
  - 0.0s: ColorRect modulate Alpha = 1
  - 0.5s: ColorRect modulate Alpha = 0

### Step 3: Transition Script

Add this script to the Transition node:

```gdscript
# transition.gd
extends CanvasLayer

signal transition_finished

@onready var anim_player = $AnimationPlayer

# Keep this scene alive when changing scenes!
func _ready():
    process_mode = Node.PROCESS_MODE_ALWAYS

func fade_to_black():
    anim_player.play("fade_to_black")
    await anim_player.animation_finished
    transition_finished.emit()

func fade_from_black():
    anim_player.play("fade_from_black")
```

### Step 4: Autoload the Transition

We want the transition available everywhere:

1. Go to **Project > Project Settings > Autoload**
2. Click folder icon, select `transition.tscn`
3. Name it "Transition"
4. Click **Add**

Now you can use `Transition.fade_to_black()` from any script!

### Step 5: Using Transitions

Update your Main Menu to use transitions:

```gdscript
# main_menu.gd
extends Control

func _on_play_pressed():
    # Fade to black
    await Transition.fade_to_black()
    
    # Change scene while black
    get_tree().change_scene_to_file("res://scenes/levels/level1.tscn")
    
    # Fade back in
    Transition.fade_from_black()
```

---

## Part 5: Game State Management

Games have different "states" - Menu, Playing, Paused, Game Over. Let's organize this!

### Creating a Game Manager

1. Create a new script: `res://scripts/autoload/game_manager.gd`
2. Go to **Project > Project Settings > Autoload**
3. Add this script as "GameManager"

```gdscript
# game_manager.gd
extends Node

# Game states as an enum (predefined list of values)
enum GameState {
    MENU,
    PLAYING,
    PAUSED,
    GAME_OVER
}

# Current state
var current_state: GameState = GameState.MENU

# Player data that persists between scenes
var current_level: int = 1
var total_score: int = 0
var lives: int = 3

func change_state(new_state: GameState):
    current_state = new_state
    
    match new_state:
        GameState.MENU:
            print("State: Menu")
            get_tree().paused = false
            
        GameState.PLAYING:
            print("State: Playing")
            get_tree().paused = false
            
        GameState.PAUSED:
            print("State: Paused")
            get_tree().paused = true
            
        GameState.GAME_OVER:
            print("State: Game Over")
            get_tree().paused = false

func reset_game():
    current_level = 1
    total_score = 0
    lives = 3
```

### Using Game States

In your Level script:

```gdscript
# level.gd
extends Node2D

func _ready():
    # Tell game manager we're playing
    GameManager.change_state(GameManager.GameState.PLAYING)
    
    # Setup level based on game state
    print("Starting level ", GameManager.current_level)

func _input(event):
    # Press ESC to pause
    if event.is_action_pressed("ui_cancel"):  # ESC key
        if GameManager.current_state == GameManager.GameState.PLAYING:
            pause_game()
        elif GameManager.current_state == GameManager.GameState.PAUSED:
            resume_game()

func pause_game():
    GameManager.change_state(GameManager.GameState.PAUSED)
    # Show pause menu (we'll create this next)
    $CanvasLayer/PauseMenu.show()

func resume_game():
    GameManager.change_state(GameManager.GameState.PLAYING)
    $CanvasLayer/PauseMenu.hide()
```

---

## Part 6: Main Menu Implementation

Let's complete the Main Menu with all functionality.

### Full Main Menu Script

```gdscript
# main_menu.gd
extends Control

@onready var play_button = $PlayButton
@onready var options_button = $OptionsButton
@onready var quit_button = $QuitButton

func _ready():
    # Set initial game state
    GameManager.change_state(GameManager.GameState.MENU)
    
    # Connect buttons
    play_button.pressed.connect(_on_play_pressed)
    options_button.pressed.connect(_on_options_pressed)
    quit_button.pressed.connect(_on_quit_pressed)
    
    # Focus the play button (for keyboard/controller)
    play_button.grab_focus()

func _on_play_pressed():
    # Reset game data for a fresh start
    GameManager.reset_game()
    
    # Transition to level 1
    await Transition.fade_to_black()
    get_tree().change_scene_to_file("res://scenes/levels/level1.tscn")
    Transition.fade_from_black()

func _on_options_pressed():
    # We'll create this in Practice Activities
    print("Options menu coming soon!")

func _on_quit_pressed():
    # Confirm quit (optional polish)
    var confirm = await show_quit_confirmation()
    if confirm:
        get_tree().quit()

func show_quit_confirmation() -> bool:
    # Simple confirmation - could be a popup dialog
    return true  # For now, just quit

func _input(event):
    # Allow ESC to quit from menu
    if event.is_action_pressed("ui_cancel"):
        _on_quit_pressed()
```

---

## Part 7: Pause Menu

A pause menu lets players take a break without quitting.

### Step 1: Create Pause Menu Scene

1. **Scene > New Scene** > User Interface
2. Rename to "PauseMenu"
3. Save as `res://scenes/ui/pause_menu.tscn`

#### Pause Menu Structure

```
PauseMenu (CanvasLayer)
├── Background (ColorRect)
│   ├── Anchors: Full Rect
│   ├── Color: #000000
│   └── Modulate: Alpha 0.5 (semi-transparent)
├── MenuPanel (Panel)
│   ├── Size: (400, 300)
│   ├── Position: Center
│   └── Style: Create StyleBoxFlat, dark color
├── TitleLabel (Label)
│   ├── Parent: MenuPanel
│   ├── Text: "PAUSED"
│   └── Theme Overrides > Font Size: 48
├── ResumeButton (Button)
│   ├── Parent: MenuPanel
│   ├── Text: "RESUME"
│   └── Size: (200, 50)
├── OptionsButton (Button)
│   ├── Parent: MenuPanel
│   ├── Text: "OPTIONS"
│   └── Size: (200, 50)
└── QuitToMenuButton (Button)
    ├── Parent: MenuPanel
    ├── Text: "QUIT TO MENU"
    └── Size: (200, 50)
```

### Step 2: Pause Menu Script

```gdscript
# pause_menu.gd
extends CanvasLayer

@onready var resume_button = $MenuPanel/ResumeButton
@onready var options_button = $MenuPanel/OptionsButton
@onready var quit_button = $MenuPanel/QuitToMenuButton

func _ready():
    # Connect buttons
    resume_button.pressed.connect(_on_resume_pressed)
    options_button.pressed.connect(_on_options_pressed)
    quit_button.pressed.connect(_on_quit_pressed)
    
    # Hide by default
    hide()

func _on_resume_pressed():
    # Hide menu
    hide()
    
    # Unpause the game
    GameManager.change_state(GameManager.GameState.PLAYING)

func _on_options_pressed():
    print("Options menu coming soon!")

func _on_quit_pressed():
    # Confirm before quitting
    hide()
    GameManager.change_state(GameManager.GameState.PLAYING)
    
    await Transition.fade_to_black()
    get_tree().change_scene_to_file("res://scenes/ui/main_menu.tscn")
    Transition.fade_from_black()
    
    GameManager.change_state(GameManager.GameState.MENU)
```

### Step 3: Add Pause Menu to Level

In your Level scene:

```
Level1 (Node2D)
├── ...
└── CanvasLayer (UI)
    ├── HUD (instance)
    └── PauseMenu (instance pause_menu.tscn)
```

Make sure PauseMenu is hidden by default in the editor!

---

## Part 8: Game Over Screen

When the player dies, show a nice Game Over screen with their score.

### Game Over Script

```gdscript
# game_over.gd
extends Control

@onready var score_label = $ScoreLabel
@onready var retry_button = $RetryButton
@onready var menu_button = $MenuButton

func _ready():
    GameManager.change_state(GameManager.GameState.GAME_OVER)
    
    # Display the final score
    score_label.text = "Final Score: " + str(GameManager.total_score)
    
    # Connect buttons
    retry_button.pressed.connect(_on_retry_pressed)
    menu_button.pressed.connect(_on_menu_pressed)
    
    # Focus retry button
    retry_button.grab_focus()

func _on_retry_pressed():
    # Retry current level
    await Transition.fade_to_black()
    
    var level_path = "res://scenes/levels/level" + str(GameManager.current_level) + ".tscn"
    get_tree().change_scene_to_file(level_path)
    
    Transition.fade_from_black()
    GameManager.change_state(GameManager.GameState.PLAYING)

func _on_menu_pressed():
    await Transition.fade_to_black()
    get_tree().change_scene_to_file("res://scenes/ui/main_menu.tscn")
    Transition.fade_from_black()
    GameManager.change_state(GameManager.GameState.MENU)
```

### Triggering Game Over

In your Player script (from Lesson 4), update the death handling:

```gdscript
# player.gd - modify the _on_death() function

func _on_death():
    # Play death animation/sound
    animation_player.play("death")
    
    # Wait a moment
    await get_tree().create_timer(1.0).timeout
    
    # Lose a life
    GameManager.lives -= 1
    
    if GameManager.lives <= 0:
        # Game over - show game over screen
        await Transition.fade_to_black()
        get_tree().change_scene_to_file("res://scenes/ui/game_over.tscn")
        Transition.fade_from_black()
    else:
        # Retry current level
        await Transition.fade_to_black()
        var level_path = "res://scenes/levels/level" + str(GameManager.current_level) + ".tscn"
        get_tree().change_scene_to_file(level_path)
        Transition.fade_from_black()
```

---

## Part 9: Level Progression

Let's add a way to complete levels and move to the next one!

### Step 1: Create Level End Area

In your TileMap, create a special tile or object that marks the end of the level.

Or add an Area2D:

```
LevelEnd (Area2D)
├── CollisionShape2D
│   └── Shape: RectangleShape2D (size 32x64)
└── Sprite2D (optional visual)
    └── Texture: Flag or portal image
```

### Step 2: Level End Script

```gdscript
# level_end.gd
extends Area2D

@export var next_level_path: String = ""

func _ready():
    body_entered.connect(_on_body_entered)

func _on_body_entered(body):
    if body.is_in_group("player"):
        level_complete()

func level_complete():
    print("Level Complete!")
    
    # Save current score
    # (It's already in GameManager!)
    
    # Increment level
    GameManager.current_level += 1
    
    # Check if there's a next level
    if next_level_path.is_empty():
        # No more levels - victory!
        await Transition.fade_to_black()
        get_tree().change_scene_to_file("res://scenes/ui/victory_screen.tscn")
        Transition.fade_from_black()
    else:
        # Go to next level
        await Transition.fade_to_black()
        get_tree().change_scene_to_file(next_level_path)
        Transition.fade_from_black()
```

### Step 3: Set Up Level Progression

In Level 1 scene:
- Select LevelEnd node
- In Inspector, set **Next Level Path** to: `res://scenes/levels/level2.tscn`

In Level 2 scene:
- Select LevelEnd node
- Leave **Next Level Path** empty (triggers victory)

---

## Part 10: Practice Activities

You've learned the basics! Now extend your game with these challenges.

### Activity 1: Loading Screen

Create a loading screen for slower computers:

```gdscript
# loading_screen.gd
extends Control

@onready var progress_bar = $ProgressBar
@onready var status_label = $StatusLabel

var scene_to_load: String = ""

func load_scene(path: String):
    scene_to_load = path
    show()
    
    # Start loading in background
    ResourceLoader.load_threaded_request(path)
    
    # Poll loading progress
    while ResourceLoader.load_threaded_get_status(path) == ResourceLoader.THREAD_LOAD_IN_PROGRESS:
        var progress = ResourceLoader.load_threaded_get_stage(path)
        progress_bar.value = progress * 100
        await get_tree().process_frame
    
    # Loading complete
    var resource = ResourceLoader.load_threaded_get(path)
    get_tree().change_scene_to_packed(resource)
    hide()
```

**Challenge**: Add fun loading tips that rotate while waiting!

### Activity 2: Options Menu

Create an options menu with:
- **Music Volume slider** (0-100%)
- **SFX Volume slider** (0-100%)
- **Fullscreen toggle** checkbox
- **Back button**

```gdscript
# options_menu.gd
extends Control

@onready var music_slider = $MusicSlider
@onready var sfx_slider = $SFXSlider
@onready var fullscreen_check = $FullscreenCheck

func _ready():
    music_slider.value_changed.connect(_on_music_changed)
    sfx_slider.value_changed.connect(_on_sfx_changed)
    fullscreen_check.toggled.connect(_on_fullscreen_toggled)

func _on_music_changed(value):
    AudioServer.set_bus_volume_db(AudioServer.get_bus_index("Music"), linear_to_db(value / 100.0))

func _on_sfx_changed(value):
    AudioServer.set_bus_volume_db(AudioServer.get_bus_index("SFX"), linear_to_db(value / 100.0))

func _on_fullscreen_toggled(button_pressed):
    if button_pressed:
        DisplayServer.window_set_mode(DisplayServer.WINDOW_MODE_FULLSCREEN)
    else:
        DisplayServer.window_set_mode(DisplayServer.WINDOW_MODE_WINDOWED)
```

### Activity 3: Victory Screen

Create a victory screen that shows:
- "YOU WIN!" message
- Final score
- Time taken (if you track it)
- Return to menu button
- "Play Again" button (restarts from level 1)

Add some confetti particles for extra celebration!

### Activity 4: Save/Load System (Advanced)

Save player progress so they can quit and come back later:

```gdscript
# save_manager.gd (Autoload)
extends Node

const SAVE_FILE = "user://savegame.save"

func save_game():
    var save_data = {
        "current_level": GameManager.current_level,
        "total_score": GameManager.total_score,
        "lives": GameManager.lives
    }
    
    var file = FileAccess.open(SAVE_FILE, FileAccess.WRITE)
    file.store_var(save_data)
    file.close()
    
    print("Game saved!")

func load_game() -> bool:
    if not FileAccess.file_exists(SAVE_FILE):
        return false
    
    var file = FileAccess.open(SAVE_FILE, FileAccess.READ)
    var save_data = file.get_var()
    file.close()
    
    GameManager.current_level = save_data.get("current_level", 1)
    GameManager.total_score = save_data.get("total_score", 0)
    GameManager.lives = save_data.get("lives", 3)
    
    print("Game loaded!")
    return true

func has_save() -> bool:
    return FileAccess.file_exists(SAVE_FILE)
```

Use this in your Main Menu:
- Add "Continue" button if `SaveManager.has_save()`
- Call `SaveManager.save_game()` after completing each level
- Call `SaveManager.load_game()` when clicking Continue

---

## Part 11: Troubleshooting

### Common Issues

**Problem**: Scene changes but screen stays black

**Solution**: 
- Check that Transition.fade_from_black() is being called
- Make sure the new scene has a visible background or Camera2D
- Verify the scene path is correct

**Problem**: Buttons don't work

**Solution**:
- Check that signals are properly connected
- Verify the button isn't covered by another Control node
- Make sure process_mode isn't set to DISABLED

**Problem**: Pause menu doesn't pause the game

**Solution**:
- Ensure `get_tree().paused = true` is being called
- Check that your PauseMenu has `process_mode = PROCESS_MODE_ALWAYS` or `PROCESS_MODE_WHEN_PAUSED`
- Verify GameState is being set to PAUSED

**Problem**: Score resets between levels

**Solution**:
- Use GameManager.total_score instead of local variables
- Don't call GameManager.reset_game() between levels
- Only reset on "New Game", not on "Retry"

**Problem**: Game crashes when changing scenes

**Solution**:
- Check for null reference errors
- Make sure all nodes exist before accessing them
- Use `await` properly with transitions
- Check Output panel for error messages

### Debug Checklist

Before asking for help, check:
- [ ] All scene paths are correct (use FileSystem dock to verify)
- [ ] Signals are connected (check Node tab)
- [ ] Autoloads are properly set up in Project Settings
- [ ] No error messages in Output panel
- [ ] Scene runs without errors when played individually

---

## Part 12: Course Wrap-Up and Next Steps

### Congratulations! 🎉

You've completed the entire course! Let's review what you built:

#### What You've Learned

1. **Godot Basics** - Nodes, scenes, and the editor
2. **Player Movement** - Physics, input, animation
3. **Level Design** - TileMaps, platforms, hazards
4. **Enemies** - AI, collisions, combat
5. **Collectibles** - Coins, scoring, UI
6. **Audio** - Music, SFX, mixing
7. **Scene Transitions** - Multiple scenes, smooth fades
8. **Game States** - Menus, pause, game over, victory

#### Your Complete Game Includes

✅ Main Menu with Play/Quit  
✅ Multiple playable levels  
✅ Smooth scene transitions  
✅ Pause functionality  
✅ Game Over screen  
✅ Level progression system  
✅ Persistent score tracking  
✅ Professional polish

### Share Your Game!

Export your game and share it:

1. **Project > Export**
2. Add preset for your platform (Windows, Mac, Linux, Web)
3. Click **Export Project**
4. Share the exported file with friends!

### Next Steps

Keep improving your game:

**Immediate Extensions:**
- Add more levels (Level 3, 4, 5...)
- Create different enemy types
- Add power-ups (invincibility, speed boost)
- Implement boss battles

**Polish Ideas:**
- Add particle effects everywhere
- Create a proper Options menu
- Add more sound effects
- Design better UI graphics

**Advanced Features:**
- Level select screen
- High score table
- Achievement system
- Cutscenes between levels

**Learn More:**
- **Godot Documentation**: docs.godotengine.org
- **GDQuest YouTube**: Excellent Godot tutorials
- **GameDev.tv**: Complete Godot courses
- **itch.io**: Share your game and play others

### Remember

Game development is a journey. Every professional started where you are now. Keep making games, keep learning, and most importantly - **have fun!**

The skills you've learned in this course apply to:
- 2D platformers (like you built!)
- Top-down RPGs
- Puzzle games
- Shooters
- And much more!

### Final Challenge

Take your game from this course and make it truly yours:
1. Design 5 unique levels
2. Create your own art style
3. Write your own music or find free assets
4. Add a unique mechanic (wall-jump, double-jump, etc.)
5. Share it online!

You now have all the tools you need to create amazing games. Go build something awesome! 🚀

---

## Quick Reference Card

### Scene Changing
```gdscript
# Basic scene change
get_tree().change_scene_to_file("res://path/to/scene.tscn")

# With transition
await Transition.fade_to_black()
get_tree().change_scene_to_file("res://path/to/scene.tscn")
Transition.fade_from_black()

# Quit game
get_tree().quit()

# Pause/Unpause
get_tree().paused = true   # Pause
get_tree().paused = false  # Unpause
```

### Game States
```gdscript
# Change state
GameManager.change_state(GameManager.GameState.PLAYING)
GameManager.change_state(GameManager.GameState.PAUSED)
GameManager.change_state(GameManager.GameState.GAME_OVER)

# Check state
if GameManager.current_state == GameManager.GameState.PAUSED:
    pass
```

### Persistent Data
```gdscript
# Store data in GameManager (autoload)
GameManager.total_score += 10
GameManager.current_level = 2
GameManager.lives -= 1

# Access from any scene
print(GameManager.total_score)
```

---

**End of Lesson 8 - Complete Course**

*Happy Game Developing!* 🎮
