# Lesson 4: Game Flow Management with GameFlow

**Prerequisites:** Lessons 1-3 (Godot basics, scripting fundamentals, player movement)



## 🎯 Learning Objectives

By the end of this lesson, you will:
- Understand what Autoload/Singletons are and why we need them
- Create a `game_flow.gd` script to manage global game state
- Implement score tracking, pause functionality, and level management
- Connect your player to the global game manager
- Create game states (playing, paused, game\_over)



## 🤔 Why Do We Need Game Flow Management?

Imagine you're building a game with multiple scenes: a main menu, several levels, and a game over screen. Each scene needs to know:

- What's the current score?
- How much health does the player have?
- Is the game paused?
- Which level are we on?

**The Problem:** Without a central manager, you'd have to pass this data between every scene manually. If the player dies in Level 3, how does the Game Over screen know what score to display?

**The Solution:** A **Game Manager** (or Game Flow Controller) - a special script that exists across ALL scenes and keeps track of everything!



## 📦 Understanding Autoload/Singletons in Godot

### What is a Singleton?

A **Singleton** is like the "command center" of your game. It's a script that:
- Loads automatically when your game starts
- Never gets deleted when you change scenes
- Can be accessed from ANY other script
- Stores data that needs to persist (like score, health, settings)

### Visual Diagram: Without vs. With Singleton

```
WITHOUT SINGLETON (Data gets lost between scenes):
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Level 1   │────→│   Level 2   │────→│  Game Over  │
│  Score: 100 │     │  Score: ??? │     │  Score: ??? │
└─────────────┘     └─────────────┘     └─────────────┘
    (Score is lost!)

WITH SINGLETON (Data persists everywhere):
                    ┌─────────────┐
                    │   GameFlow  │
                    │  Score: 100 │
                    │  Health: 3  │
                    └──────┬──────┘
                           │
           ┌───────────────┼───────────────┐
           │               │               │
    ┌──────▼──────┐ ┌──────▼──────┐ ┌──────▼──────┐
    │   Level 1   │ │   Level 2   │ │  Game Over  │
    │  Score: 100 │ │  Score: 100 │ │  Score: 100 │
    └─────────────┘ └─────────────┘ └─────────────┘
    
    All scenes access the SAME GameFlow!
```

### Godot's Autoload System

Godot has a built-in way to create singletons called **Autoload**. When you add a script to Autoload:

1. It automatically creates a node when your game starts
2. It stays alive even when you change scenes
3. You can access it from any script using its name

Think of it like your backpack in a video game - you carry it everywhere, and everything inside stays with you!



## 📝 Creating game\_flow.gd

### Step 1: Create the Script File

1. In the **FileSystem** panel, right-click on your project folder
2. Select **New Script**
3. Name it: `game_flow.gd`
4. Click **Create**

### Step 2: Write the GameFlow Script

Open `game_flow.gd` and add this code:

```gdscript
extends Node

# ============================================
# GAME STATE MANAGEMENT
# ============================================

# Game states - these control what the player can do
enum GameState {
	PLAYING,      # Normal gameplay
	PAUSED,       # Game is paused
	GAME_OVER,    # Player died
	VICTORY       # Player won!
}

# Current state of the game
var current_state: GameState = GameState.PLAYING

# Track if game is paused (convenience variable)
var is_paused: bool = false

# ============================================
# LEVEL MANAGEMENT
# ============================================

# Current level number
var current_level: int = 1

# Total number of levels in your game
var total_levels: int = 3

# Path to level scenes (you'll add your actual level paths here)
var level_paths: Dictionary = {
	1: "res://levels/level_01.tscn",
	2: "res://levels/level_02.tscn",
	3: "res://levels/level_03.tscn"
}

# ============================================
# PLAYER STATS
# ============================================

# Health system
var max_health: int = 3
var current_health: int = 3

# Score tracking
var score: int = 0
var high_score: int = 0

# Player progression
var lives: int = 3

# ============================================
# GAME SETTINGS
# ============================================

# Game speed modifier (useful for slow-motion effects)
var game_speed: float = 1.0

# Debug mode (show extra info while developing)
var debug_mode: bool = true

# ============================================
# INITIALIZATION
# ============================================

func _ready():
	print("GameFlow initialized! Welcome to the game!")
	reset_game()

# Reset everything to starting values
func reset_game():
	current_state = GameState.PLAYING
	is_paused = false
	current_level = 1
	current_health = max_health
	score = 0
	lives = 3
	game_speed = 1.0
	print("Game reset! Fresh start!")

# ============================================
# GAME STATE FUNCTIONS
# ============================================

# Pause the game
func pause_game():
	if current_state == GameState.PLAYING:
		current_state = GameState.PAUSED
		is_paused = true
		get_tree().paused = true
		print("Game paused!")

# Resume the game
func resume_game():
	if current_state == GameState.PAUSED:
		current_state = GameState.PLAYING
		is_paused = false
		get_tree().paused = false
		print("Game resumed!")

# Toggle pause on/off
func toggle_pause():
	if is_paused:
		resume_game()
	else:
		pause_game()

# Game over - player ran out of health or lives
func game_over():
	current_state = GameState.GAME_OVER
	is_paused = false
	get_tree().paused = false  # Don't pause so UI still works
	
	# Check for high score
	if score > high_score:
		high_score = score
		print("NEW HIGH SCORE: " + str(high_score) + "!")
	
	print("Game Over! Final score: " + str(score))

# Victory - player completed all levels!
func victory():
	current_state = GameState.VICTORY
	is_paused = false
	get_tree().paused = false
	
	if score > high_score:
		high_score = score
	
	print("VICTORY! You beat the game! Score: " + str(score))

# ============================================
# SCORE FUNCTIONS
# ============================================

# Add points to the score
func add_score(points: int):
	score += points
	print("Score: " + str(score) + " (" + str(points) + " points)")

# Get current score
func get_score() -> int:
	return score

# Get high score
func get_high_score() -> int:
	return high_score

# ============================================
# HEALTH FUNCTIONS
# ============================================

# Player takes damage
func take_damage(amount: int = 1):
	current_health -= amount
	print("Ouch! Health: " + str(current_health) + "/" + str(max_health))
	
	if current_health <= 0:
		lives -= 1
		print("Life lost! Lives remaining: " + str(lives))
		
		if lives <= 0:
			game_over()
		else:
			# Respawn with full health
			current_health = max_health
			respawn_player()

# Heal the player
func heal(amount: int = 1):
	current_health += amount
	if current_health > max_health:
		current_health = max_health
	print("Healed! Health: " + str(current_health) + "/" + str(max_health))

# Reset health to max
func reset_health():
	current_health = max_health

# Get current health
func get_health() -> int:
	return current_health

# Get max health
func get_max_health() -> int:
	return max_health

# ============================================
# LEVEL MANAGEMENT
# ============================================

# Load a specific level
func load_level(level_number: int):
	if level_paths.has(level_number):
		current_level = level_number
		print("Loading Level " + str(level_number) + "...")
		
		# Change to the level scene
		get_tree().change_scene_to_file(level_paths[level_number])
	else:
		print("ERROR: Level " + str(level_number) + " not found!")

# Go to next level
func next_level():
	var next = current_level + 1
	
	if next > total_levels:
		# Completed all levels!
		victory()
	else:
		load_level(next)

# Restart current level
func restart_level():
	load_level(current_level)

# Go back to main menu
func return_to_menu():
	print("Returning to main menu...")
	get_tree().change_scene_to_file("res://scenes/main_menu.tscn")

# ============================================
# PLAYER RESPAWN
# ============================================

func respawn_player():
	print("Respawning player...")
	# You can emit a signal here to notify the level to reset player position
	# Or restart the current level
	restart_level()

# ============================================
# UTILITY FUNCTIONS
# ============================================

# Check if game is currently playing
func is_playing() -> bool:
	return current_state == GameState.PLAYING

# Check if game is paused
func is_game_paused() -> bool:
	return is_paused

# Check if game is over
func is_game_over() -> bool:
	return current_state == GameState.GAME_OVER

# Get current level number
func get_current_level() -> int:
	return current_level

# Get total levels
func get_total_levels() -> int:
	return total_levels
```



## ⚙️ Setting Up the Autoload

Now we need to tell Godot to automatically load this script when the game starts.

### Step-by-Step Instructions:

1. Click on **Project** in the top menu bar
2. Select **Project Settings** (or press `Cmd/Ctrl + Shift + S`)
3. Click on the **Autoload** tab
4. Click the folder icon next to the Path field
5. Navigate to and select your `game_flow.gd` file
6. In the **Node Name** field, type: `GameFlow` (this is how you'll access it!)
7. Click **Add**
8. Click **Close**

### Visual Guide:

```
Project Settings Window:
┌─────────────────────────────────────────────┐
│  General  │  Input Map  │  Autoload  │     │  ← Click "Autoload"
├─────────────────────────────────────────────┤
│                                             │
│  Path: [res://game_flow.gd]  [📁]           │  ← Select your script
│                                             │
│  Node Name: [GameFlow        ]              │  ← Name it "GameFlow"
│                                             │
│           [      Add      ]                 │  ← Click Add
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │  ✓ GameFlow  res://game_flow.gd  [X]  │  │  ← Should appear here
│  └───────────────────────────────────────┘  │
│                                             │
└─────────────────────────────────────────────┘
```

### 🎉 Success Check

Run your game and check the **Output** panel at the bottom. You should see:
```
GameFlow initialized! Welcome to the game!
Game reset! Fresh start!
```

If you see this, your Autoload is working!



## 🔗 Using GameFlow from Other Scripts

Now that GameFlow is set up, you can access it from ANY script in your game!

### Basic Access Pattern

```gdscript
# Access GameFlow using its name
GameFlow.variable_name
GameFlow.function_name()
```

### Example 1: Updating Score from Player Script

Add this to your player's script when they collect a coin:

```gdscript
# In your player script (player.gd)

func _on_coin_collected():
	# Add 10 points to the global score
	GameFlow.add_score(10)
	
	# Play a sound
	$CoinSound.play()
```

### Example 2: Taking Damage

When the player hits an enemy:

```gdscript
# In your player script

func _on_body_entered(body):
	if body.is_in_group("enemies"):
		# Player takes damage through GameFlow
		GameFlow.take_damage(1)
		
		# Knockback effect
		velocity = (global_position - body.global_position).normalized() * 200
		
		# Flash the player sprite red
		$Sprite2D.modulate = Color.RED
		await get_tree().create_timer(0.2).timeout
		$Sprite2D.modulate = Color.WHITE
```

### Example 3: Checking Game State

In your enemy script, only chase player if game is active:

```gdscript
# In your enemy script (enemy.gd)

func _process(delta):
	# Only move if game is being played (not paused or game over)
	if GameFlow.is_playing():
		chase_player()
	else:
		# Stop moving when paused
		velocity = Vector2.ZERO
```

### Example 4: Pause Input

Add pause functionality to any script:

```gdscript
# Can be in player script, UI script, or anywhere!

func _input(event):
	# Press ESC or P to pause/unpause
	if event.is_action_pressed("ui_cancel") or event.is_action_pressed("pause"):
		GameFlow.toggle_pause()
		
		# Show/hide pause menu (if you have one)
		$PauseMenu.visible = GameFlow.is_paused
```



## 🎮 Creating Game States

Our GameFlow uses an **enum** to track the current state. Here's how to use each state:

### State Diagram

```
                    ┌───────────┐
         ┌─────────│  PLAYING  │──────────┐
         │         └─────┬─────┘          │
    Player dies          │          All levels
    or ESC pressed       │          completed
         │               │               │
         ▼               ▼               ▼
   ┌───────────┐   ┌───────────┐   ┌───────────┐
   │ GAME_OVER │   │  PAUSED   │   │  VICTORY  │
   └─────┬─────┘   └─────┬─────┘   └─────┬─────┘
         │               │               │
    Restart or      Resume or       Return to
    Main Menu       Main Menu       Main Menu
         │               │               │
         └───────────────┴───────────────┘
                         │
                         ▼
                  ┌───────────┐
                  │  RESTART  │
                  └───────────┘
```

### Using States in Your Code

```gdscript
# Check specific states
if GameFlow.current_state == GameFlow.GameState.PLAYING:
	# Do something only when playing
	pass

# Use the helper functions (recommended)
if GameFlow.is_playing():
	# Only run game logic when playing
	update_enemies()
	move_player()
	check_collisions()
```



## ⏸️ Implementing Pause Functionality

### How Pausing Works in Godot

Godot has a built-in pause system. When you set `get_tree().paused = true`:
- `_process()` and `_physics_process()` stop running
- Input events still work
- Animation stops
- Physics stops

But we want our pause menu UI to still work! Here's how to handle it:

### Creating a Pause Menu

Create a new scene called `pause_menu.tscn`:

1. Create a **CanvasLayer** (so it stays on top)
2. Add a **ColorRect** as a child (for dark background)
   3. Color: Black with alpha 0.5 (semi-transparent)
3. Add a **VBoxContainer** for buttons
4. Add **Button** nodes for:
   6. Resume
   7. Restart Level
   8. Main Menu
   9. Quit

### Pause Menu Script

```gdscript
extends CanvasLayer

@onready var resume_button = $VBoxContainer/ResumeButton
@onready var restart_button = $VBoxContainer/RestartButton
@onready var menu_button = $VBoxContainer/MenuButton
@onready var quit_button = $VBoxContainer/QuitButton

func _ready():
	# Connect button signals
	resume_button.pressed.connect(_on_resume_pressed)
	restart_button.pressed.connect(_on_restart_pressed)
	menu_button.pressed.connect(_on_menu_pressed)
	quit_button.pressed.connect(_on_quit_pressed)
	
	# Hide by default
	visible = false

func _input(event):
	if event.is_action_pressed("pause"):
		toggle_pause_menu()

func toggle_pause_menu():
	GameFlow.toggle_pause()
	visible = GameFlow.is_paused

func _on_resume_pressed():
	GameFlow.resume_game()
	visible = false

func _on_restart_pressed():
	GameFlow.resume_game()
	visible = false
	GameFlow.restart_level()

func _on_menu_pressed():
	GameFlow.resume_game()
	visible = false
	GameFlow.return_to_menu()

func _on_quit_pressed():
	get_tree().quit()
```

### Important: Process Mode for UI

Make sure your pause menu keeps working when paused:

1. Select your PauseMenu root node
2. In the Inspector, find **Process → Mode**
3. Set it to: **When Paused**

```
Inspector Panel:
┌─────────────────────────────┐
│ Process                     │
│   Mode: [When Paused ▼]     │  ← Change this!
└─────────────────────────────┘
```

This tells Godot: "Keep running this node even when the game is paused!"



## 💖 Connecting Player Health to GameFlow

### Health System Overview

```
Player collides with enemy
         │
         ▼
  ┌─────────────┐
  │ Enemy calls │
  │  GameFlow.  │
  │ take_damage │
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │  GameFlow   │
  │   reduces   │
  │   health    │
  └──────┬──────┘
         │
    ┌────┴────┐
    │         │
 Health>0?  Health<=0?
    │         │
    ▼         ▼
Keep    Lose a life
Playing    │
           ▼
      Lives>0?
      ┌────┴────┐
      │         │
  Respawn   Game Over
```

### Visual Health Bar (UI)

Create a health bar that displays GameFlow's health:

```gdscript
# health_bar.gd - Attach to a CanvasLayer

extends CanvasLayer

@onready var health_label = $HealthLabel
@onready var lives_label = $LivesLabel

func _ready():
	update_health_display()

func _process(delta):
	# Update every frame to stay in sync
	update_health_display()

func update_health_display():
	var health = GameFlow.get_health()
	var max_health = GameFlow.get_max_health()
	var lives = GameFlow.lives
	
	health_label.text = "Health: " + str(health) + "/" + str(max_health)
	lives_label.text = "Lives: " + str(lives)
	
	# Change color when low on health
	if health == 1:
		health_label.modulate = Color.RED
	else:
		health_label.modulate = Color.WHITE
```

### Making Player Invulnerable After Hit

Add invulnerability frames so the player doesn't take damage every frame:

```gdscript
# In your player script

var is_invulnerable: bool = false
@export var invulnerability_time: float = 1.0

func take_damage(amount: int = 1):
	if is_invulnerable:
		return  # Can't take damage right now
	
	# Tell GameFlow to handle the damage
	GameFlow.take_damage(amount)
	
	# Become invulnerable temporarily
	is_invulnerable = true
	$Sprite2D.modulate = Color(1, 1, 1, 0.5)  # Semi-transparent
	
	# Wait before becoming vulnerable again
	await get_tree().create_timer(invulnerability_time).timeout
	
	is_invulnerable = false
	$Sprite2D.modulate = Color(1, 1, 1, 1)  # Back to normal
```



## 🏆 Practice Activities

Now it's your turn! Complete these challenges to master GameFlow:

### Activity 1: Add Level Progression (15 minutes)

**Goal:** Create a system to move between levels when the player reaches the end.

**Steps:**
1. Create a new Area2D node called "LevelExit" at the end of your level
2. Add a CollisionShape2D (make it a rectangle at the exit)
3. Attach this script:

```gdscript
# level_exit.gd

extends Area2D

@export var next_level_number: int = 2

func _on_body_entered(body):
	if body.name == "Player":
		print("Level complete!")
		GameFlow.next_level()
```

4. Create at least 2 different level scenes
5. Update the `level_paths` dictionary in GameFlow with your actual level paths
6. Test: Can you walk to the exit and load the next level?

**Bonus:** Add a "Level Complete!" popup that shows before loading the next level.



### Activity 2: Create a Win Condition (15 minutes)

**Goal:** Show a victory screen when the player beats all levels.

**Steps:**
1. Create a new scene called `victory_screen.tscn`:
   2. Add a CanvasLayer
   3. Add a Label with text "YOU WIN!"
   4. Add a Label showing the final score
   5. Add buttons: "Play Again" and "Main Menu"

2. Create `victory_screen.gd`:

```gdscript
extends CanvasLayer

@onready var score_label = $ScoreLabel

func _ready():
	# Show final score
	score_label.text = "Final Score: " + str(GameFlow.get_score())
	
	# Check for high score
	if GameFlow.get_score() >= GameFlow.get_high_score():
		$HighScoreLabel.visible = true

func _on_play_again_pressed():
	GameFlow.reset_game()
	GameFlow.load_level(1)

func _on_main_menu_pressed():
	GameFlow.reset_game()
	GameFlow.return_to_menu()
```

3. In GameFlow, modify the `victory()` function to load your victory screen:

```gdscript
func victory():
	current_state = GameState.VICTORY
	is_paused = false
	get_tree().paused = false
	
	if score > high_score:
		high_score = score
	
	print("VICTORY! You beat the game!")
	
	# Load victory screen
	get_tree().change_scene_to_file("res://scenes/victory_screen.tscn")
```

4. Test: Beat all levels and see your victory screen!

**Bonus:** Add confetti particles to the victory screen!



### Activity 3: Collectibles & Score Multiplier (15 minutes)

**Goal:** Create different types of collectibles with different point values.

**Steps:**
1. Create an enum in GameFlow for collectible types:

```gdscript
# Add this to GameFlow
enum CollectibleType {
	COIN = 10,
	GEM = 50,
	STAR = 100,
	HEART = 0  # Gives health instead
}

var score_multiplier: float = 1.0
```

2. Create a collectible script:

```gdscript
# collectible.gd

extends Area2D

@export var type: GameFlow.CollectibleType = GameFlow.CollectibleType.COIN
@export var value: int = 10

func _on_body_entered(body):
	if body.name == "Player":
		collect()

func collect():
	if type == GameFlow.CollectibleType.HEART:
		GameFlow.heal(1)
	else:
		var points = int(value * GameFlow.score_multiplier)
		GameFlow.add_score(points)
	
	# Animation and destroy
	$AnimationPlayer.play("collect")
	await $AnimationPlayer.animation_finished
	queue_free()
```

3. Create 4 different collectible sprites (coin, gem, star, heart)
4. Place them in your level and test!

**Bonus:** Add a combo system - collect 5 items in a row for a 2x multiplier!



### Activity 4: Create a High Score System (Advanced, 15 minutes)

**Goal:** Save the high score so it persists even when you close the game.

**Steps:**
1. Learn about Godot's `FileAccess` class for saving data
2. Modify GameFlow to save/load high score:

```gdscript
# Add to GameFlow

const SAVE_PATH = "user://highscore.save"

func save_high_score():
	var file = FileAccess.open(SAVE_PATH, FileAccess.WRITE)
	file.store_var(high_score)
	file.close()
	print("High score saved!")

func load_high_score():
	if FileAccess.file_exists(SAVE_PATH):
		var file = FileAccess.open(SAVE_PATH, FileAccess.READ)
		high_score = file.get_var()
		file.close()
		print("High score loaded: " + str(high_score))

# Call load_high_score() in _ready()
# Call save_high_score() when setting a new high score
```

**Challenge:** Can you also save the player's name with their high score?



## 🧩 Common Patterns for Game Managers

Here are some patterns professional game developers use:

### Pattern 1: Signal-Based Communication

Instead of directly calling GameFlow functions, use signals for loose coupling:

```gdscript
# In GameFlow
signal score_changed(new_score)
signal health_changed(new_health)
signal game_over_triggered()

func add_score(points: int):
	score += points
	score_changed.emit(score)  # Notify everyone listening!
```

```gdscript
# In UI script
func _ready():
	GameFlow.score_changed.connect(_on_score_changed)

func _on_score_changed(new_score):
	score_label.text = "Score: " + str(new_score)
```

### Pattern 2: Multiple Autoloads

For larger games, split responsibilities:

```
GameFlow (main manager)
├── GameState (playing, paused, etc.)
└── Level management

PlayerData (player-specific)
├── Health, lives, upgrades
└── Inventory

AudioManager (sound)
├── Music volume
├── Sound effects
└── Ambient sounds

SaveManager (data persistence)
├── Save game
├── Load game
└── Settings
```

### Pattern 3: State Machine Pattern

For complex games, use a proper state machine:

```gdscript
# More advanced state management
var states = {
	"menu": MenuState.new(),
	"playing": PlayingState.new(),
	"paused": PausedState.new(),
	"game_over": GameOverState.new()
}

var current_state_obj = null

func change_state(state_name: String):
	if current_state_obj:
		current_state_obj.exit()
	
	current_state_obj = states[state_name]
	current_state_obj.enter()
```



## 🐛 Troubleshooting

### Problem: "GameFlow is not defined" error

**Solution:**
- Make sure you added `game_flow.gd` to Project Settings → Autoload
- Check that the Node Name is exactly "GameFlow" (case-sensitive!)
- Restart Godot after adding Autoload

### Problem: Score/health resets when changing levels

**Solution:**
- Make sure you're accessing `GameFlow.score` not a local variable
- Don't create a new GameFlow node in your scene - Godot does this automatically
- Check that you're not calling `reset_game()` when you don't mean to

### Problem: Pause menu doesn't work when paused

**Solution:**
- Set the pause menu's Process Mode to "When Paused"
- Make sure pause menu is a CanvasLayer or Control node
- Check that `get_tree().paused = true` is being called

### Problem: Can't access GameFlow from other scripts

**Solution:**
- Autoload singletons are available immediately when the game starts
- If getting errors, add a small delay:

```gdscript
func _ready():
	await get_tree().create_timer(0.1).timeout
	# Now access GameFlow
	print(GameFlow.score)
```

### Problem: Game freezes completely when paused

**Solution:**
- Some nodes might be processing when they shouldn't
- Check Process Mode on:
  - UI elements (should be "When Paused")
  - Game world (should be "Pausable" or "Inherit")
- Make sure to unpause before changing scenes



## 🎓 Key Takeaways

1. **Autoload/Singletons** stay alive across all scenes and store global data
2. **GameFlow** is your command center for game state, score, and level management
3. Use **enums** for game states to make your code readable and organized
4. **get\_tree().paused** is Godot's built-in pause system
5. Set Process Mode to **"When Paused"** for UI that needs to work while paused
6. Always access game data through **GameFlow** to keep everything synchronized



## 🔮 Preview of Next Lesson

In **Lesson 5: UI & HUD Design**, you'll learn to:
- Create professional health bars, score displays, and progress meters
- Design responsive UI that works on different screen sizes
- Add animated UI elements (damage numbers, combo counters)
- Build a complete in-game HUD using Godot's Control nodes
- Create main menus and settings screens

Your GameFlow will work hand-in-hand with the UI system to display all that game data!



## 📚 Additional Resources

- **Godot Documentation:** [Singletons (AutoLoad)][1]
- **Godot Documentation:** [Pausing Games][2]
- **Practice:** Try creating additional managers: AudioManager, SettingsManager, AchievementManager



## ✅ Lesson Checklist

Before moving to Lesson 5, make sure you can:

- [ ]() Explain what an Autoload/Singleton is
- [ ]() Created `game_flow.gd` with game states, score, and health
- [ ]() Added GameFlow to Project Settings → Autoload
- [ ]() Accessed GameFlow from player and enemy scripts
- [ ]() Implemented pause functionality with a working pause menu
- [ ]() Connected player health to GameFlow's health system
- [ ]() Created level progression (can move between levels)
- [ ]() Implemented at least one win/lose condition
- [ ]() Completed at least 2 practice activities

**Congratulations!** You now have a solid game management system. Your games can now track score, manage health, pause, and move between levels like professional games!



## 📝 Glossary

- **Autoload:** Godot's system for automatically loading scripts as singletons
- **Singleton:** A design pattern where only one instance of a class exists
- **Global Variable:** A variable accessible from anywhere in the program
- **Enum:** A data type with a fixed set of named values (like PLAYING, PAUSED)
- **State Machine:** A system that manages different states and transitions between them
- **Process Mode:** Godot setting that controls if a node runs when paused



*Happy Coding! Your games are getting more professional with every lesson! 🎮✨*

[1]:	https://docs.godotengine.org/en/stable/tutorials/scripting/singletons_autoload.html
[2]:	https://docs.godotengine.org/en/stable/tutorials/scripting/pausing_games.html
