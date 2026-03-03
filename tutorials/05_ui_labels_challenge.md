# Lesson 5: UI & HUD Labels Challenge

## Level Up Your Game! 🎮

**Target:** Ages 12-15 | **Time:** 45-60 minutes | **Prerequisites:** Lessons 1-4

---

## What You'll Build Today

A heads-up display (HUD) that shows your player's health, mana, and score—just like in professional games! Your HUD will automatically update when values change, so players always know their stats.

**Success Criteria:**
- [ ] HUD displays Health, Mana, and Score labels
- [ ] Labels update automatically when game values change
- [ ] UI stays in the corner during gameplay
- [ ] Labels have good visibility (size, color, position)

---

## Part 1: Understanding CanvasLayer

### What is CanvasLayer?

Think of your game world as a movie set. Your player, enemies, and platforms are all actors on the set. But your health bar and score? Those are like the camera lens info overlay—they should stay on screen no matter where the camera moves.

**CanvasLayer** is Godot's way of creating UI that stays fixed on screen, ignoring the camera's position. It's perfect for:
- Health bars
- Score displays
- Pause menus
- Mini-maps

### How CanvasLayer Works

```
CanvasLayer (stays fixed on screen)
└── Control (container for UI elements)
    ├── Label (Health: 100/100)
    ├── Label (Mana: 50/100)
    └── Label (Score: 1250)
```

The **CanvasLayer** sits on top of everything and doesn't move. The **Control** node helps with layout and positioning.

---

## Part 2: Creating Your HUD Scene

### Step-by-Step Setup

1. **Create a new scene**
   - Right-click in FileSystem → New Scene
   - Name it `hud.tscn`

2. **Add CanvasLayer as root**
   - Click "Other Node"
   - Search for `CanvasLayer`
   - This is your HUD's foundation

3. **Add a Control node for layout**
   - Right-click CanvasLayer → Add Child Node
   - Search for `Control`
   - Name it `HUDContainer`
   - This will hold all your UI elements

4. **Set up the Control layout**
   - Select HUDContainer
   - In the Inspector, set **Layout Mode** to "Anchors"
   - Set **Anchor Preset** to "Top Left"
   - This keeps your HUD in the top-left corner

5. **Add Label nodes**
   - Right-click HUDContainer → Add Child Node
   - Search for `Label`
   - Name it `HealthLabel`
   - Repeat for `ManaLabel` and `ScoreLabel`

### Your Scene Tree Should Look Like:

```
CanvasLayer
└── HUDContainer (Control)
    ├── HealthLabel (Label)
    ├── ManaLabel (Label)
    └── ScoreLabel (Label)
```

---

## Part 3: Configuring Your Labels

### HealthLabel Setup

Select HealthLabel and configure these properties in the Inspector:

| Property | Value | What It Does |
|----------|-------|--------------|
| Text | "Health: 100/100" | Default text (we'll update this in code) |
| Layout/Anchor | Left, Top | Stays in top-left |
| Position | X: 20, Y: 20 | Slight padding from corner |
| Custom Minimum Size | X: 200, Y: 30 | Ensures label has room |

### Theme Customization (Optional but Cool!)

Let's make your text bigger and more readable:

1. Select a Label
2. In Inspector, look for **Theme Overrides**
3. Expand **Font Sizes**
4. Set **Font Size** to `24`
5. Expand **Colors**
6. Set **Font Color** to white (#FFFFFF)

**Pro Tip:** To make text visible over any background, add a **Font Outline**:
- In Theme Overrides, expand **Constants**
- Set **Outline Size** to `2`
- Set **Font Outline Color** to black (#000000)

### Repeat for All Labels

Set up ManaLabel and ScoreLabel the same way, but position them below each other:
- ManaLabel: Position Y: 50
- ScoreLabel: Position Y: 80

Or try a horizontal layout:
- ManaLabel: Position X: 240, Y: 20
- ScoreLabel: Position X: 460, Y: 20

---

## Part 4: The Challenge - Wiring Labels to Variables

### What You Need to Do

You have labels on screen, but they're just showing static text. The challenge is to make them display live values from your `GameFlow` autoload.

### Understanding the Problem

Your `game_flow.gd` (from Lesson 4) has variables like:
```gdscript
var current_health = 100
var max_health = 100
var current_mana = 50
var max_mana = 100
var score = 0
```

When these change, your labels should automatically update. But how?

### The Solution: The _process() Function

Godot calls `_process(delta)` every single frame (60+ times per second). This is perfect for updating UI!

**Here's your mission:**
1. Create a script for your HUD
2. Get references to your labels
3. In `_process()`, read GameFlow variables and update label text

---

## Part 5: Step-by-Step Solution

### Step 1: Create the HUD Script

1. Select your CanvasLayer root node
2. Click the script icon (paper with green "+")
3. Name it `hud.gd`
4. Click Create

### Step 2: Access Your Labels

Add these at the top of your script:

```gdscript
extends CanvasLayer

@onready var health_label = $HUDContainer/HealthLabel
@onready var mana_label = $HUDContainer/ManaLabel
@onready var score_label = $HUDContainer/ScoreLabel
```

**What does @onready do?** It waits until the scene is fully loaded before getting the nodes. This prevents errors!

### Step 3: Update Labels Every Frame

Add the `_process()` function:

```gdscript
func _process(delta):
    health_label.text = "Health: %d/%d" % [GameFlow.current_health, GameFlow.max_health]
    mana_label.text = "Mana: %d/%d" % [GameFlow.current_mana, GameFlow.max_mana]
    score_label.text = "Score: %d" % GameFlow.score
```

### Understanding String Formatting

The `%` symbol is string formatting in GDScript:
- `%d` = insert a number (integer)
- `%s` = insert text (string)
- `%f` = insert a decimal number (float)

`% [value1, value2]` puts the values into the `%d` placeholders in order.

### Complete HUD Script

```gdscript
extends CanvasLayer

@onready var health_label = $HUDContainer/HealthLabel
@onready var mana_label = $HUDContainer/ManaLabel
@onready var score_label = $HUDContainer/ScoreLabel

func _process(delta):
    health_label.text = "Health: %d/%d" % [GameFlow.current_health, GameFlow.max_health]
    mana_label.text = "Mana: %d/%d" % [GameFlow.current_mana, GameFlow.max_mana]
    score_label.text = "Score: %d" % GameFlow.score
```

### Step 4: Add HUD to Your Main Scene

1. Open your main game scene (or create `main.tscn`)
2. Right-click the root node → Instance Child Scene
3. Select `hud.tscn`
4. The HUD will appear in your game!

---

## Part 6: Testing Your HUD

### Method 1: Test Buttons (Easiest)

Add temporary buttons to change values:

1. In your main scene, add three Button nodes
2. Name them: `DamageButton`, `HealButton`, `AddScoreButton`
3. Add this script to your main scene:

```gdscript
extends Node2D

func _on_damage_button_pressed():
    GameFlow.current_health -= 10
    if GameFlow.current_health < 0:
        GameFlow.current_health = 0

func _on_heal_button_pressed():
    GameFlow.current_health += 10
    if GameFlow.current_health > GameFlow.max_health:
        GameFlow.current_health = GameFlow.max_health

func _on_add_score_button_pressed():
    GameFlow.score += 100
```

4. Connect the button signals to these functions

### Method 2: Automatic Test (For Debugging)

Add this to your HUD script temporarily:

```gdscript
func _ready():
    # Set up test values
    GameFlow.current_health = 100
    GameFlow.max_health = 100
    GameFlow.current_mana = 50
    GameFlow.max_mana = 100
    GameFlow.score = 0

func _process(delta):
    # ... your label updates ...
    
    # Auto-change values for testing
    if Input.is_action_just_pressed("ui_accept"):  # Spacebar
        GameFlow.current_health -= 5
        GameFlow.score += 50
```

Press Spacebar to see the HUD update!

### What You Should See

- Health label updates when you press buttons
- Score increases when you earn points
- Numbers never go below 0 (add that check!)
- UI stays in corner even when player moves

---

## Part 7: Making It Look Good

### Using Themes for Consistency

Instead of styling each label individually, create a Theme:

1. In FileSystem, right-click → New Resource
2. Select `Theme`
3. Name it `hud_theme.tres`

4. Double-click to edit
5. Add Label type overrides:
   - Font Size: 24
   - Font Color: White
   - Outline Size: 2
   - Outline Color: Black

6. Save the theme
7. Select your CanvasLayer
8. In Inspector, drag `hud_theme.tres` into the **Theme** property

Now all labels automatically use these settings!

### Adding Backgrounds (Bonus)

Make a backdrop for your stats:

1. Add a `ColorRect` node as a child of HUDContainer
2. Position it behind your labels
3. Set Color to semi-transparent black (e.g., #000000 with Alpha: 0.7)
4. Resize to cover all your labels
5. Right-click ColorRect → Move Up (to put it behind labels)

### Progress Bars (Super Bonus)

Instead of just numbers, add visual bars:

1. Add `ProgressBar` nodes for health and mana
2. In your HUD script:

```gdscript
@onready var health_bar = $HUDContainer/HealthBar

func _process(delta):
    # ... label updates ...
    health_bar.value = GameFlow.current_health
    health_bar.max_value = GameFlow.max_health
```

---

## Part 8: Challenge Variations

Pick at least one to try:

### Variation A: Animated Changes
Make numbers count up/down instead of jumping:

```gdscript
var display_health = 100

func _process(delta):
    # Smoothly animate to actual health
    display_health = lerp(display_health, GameFlow.current_health, delta * 5)
    health_label.text = "Health: %d/%d" % [int(display_health), GameFlow.max_health]
```

### Variation B: Color Warnings
Change color when health is low:

```gdscript
func _process(delta):
    health_label.text = "Health: %d/%d" % [GameFlow.current_health, GameFlow.max_health]
    
    # Change color based on health percentage
    var health_percent = float(GameFlow.current_health) / GameFlow.max_health
    if health_percent < 0.3:
        health_label.add_theme_color_override("font_color", Color.RED)
    else:
        health_label.add_theme_color_override("font_color", Color.WHITE)
```

### Variation C: Different Layouts
Try these arrangements:

**Vertical Stack:**
```
Health: 100/100
Mana: 50/100
Score: 1250
```

**Horizontal Bar:**
```
HP: 100 | MP: 50 | Score: 1250
```

**Corner Display:**
```
Score: 1250

Health: ████████░░
Mana:   █████░░░░░
```

### Variation D: Damage Feedback
Show damage numbers floating up:

1. Create a scene `damage_number.tscn` with a Label
2. When health decreases, instance it and animate upward
3. Delete after animation finishes

---

## Part 9: Troubleshooting

### Problem: Labels show "null" or empty
**Solution:** Check that:
- `@onready` variables match your node names exactly
- GameFlow is set as an autoload (Project → Project Settings → Autoload)
- GameFlow variables are initialized (not null)

### Problem: Labels don't update
**Solution:** Check that:
- The `_process()` function exists in your HUD script
- GameFlow variables are actually changing (add print statements to check)
- The HUD scene is added to your main scene

### Problem: Text is cut off or overlapping
**Solution:**
- Increase Custom Minimum Size on labels
- Check font size isn't too large
- Use outline to make text readable on any background

### Problem: UI moves with camera
**Solution:**
- Make sure root node is CanvasLayer, not Node2D
- CanvasLayer should NOT be a child of anything that moves

### Problem: "Invalid get index 'current_health'" error
**Solution:**
- GameFlow might not be loaded. Check Project Settings → Autoload
- Variable name might have a typo
- Make sure you're using `GameFlow.` (capital G, capital F)

---

## Part 10: Next Lesson Preview

**Lesson 6: Signals & Event System**

You'll learn:
- How to use Godot's signal system for cleaner code
- Connecting buttons to actions the right way
- Broadcasting events ("Player died!", "Level completed!")
- Decoupling your HUD from GameFlow using signals

**Why it matters:** Instead of checking every frame, signals let your code react only when things actually happen—more efficient and organized!

---

## Quick Reference

### Common Label Properties
| Property | Description |
|----------|-------------|
| text | The text displayed |
| horizontal_alignment | LEFT, CENTER, RIGHT |
| vertical_alignment | TOP, CENTER, BOTTOM |
| autowrap_mode | Wrap long text to multiple lines |
| clip_text | Cut off text that doesn't fit |

### String Formatting
```gdscript
"Health: %d/%d" % [current, max]      # Numbers
"Score: %d, Time: %.1f" % [score, time]  # Integer and decimal
"Player: %s" % player_name              # Text
```

### Node Paths
```gdscript
$NodeName                    # Direct child
$Parent/Child/Grandchild     # Path through hierarchy
%UniqueNodeName              # Using unique names (Godot 4)
```

---

## Check Your Understanding

Before moving on, make sure you can answer:

1. Why do we use CanvasLayer instead of Node2D for UI?
2. What does `@onready` do?
3. When does `_process()` run?
4. How do you format a string with multiple numbers?
5. Why might we use a Theme instead of styling each label?

---

**Congratulations!** You now have a working HUD that displays live game data. This is a fundamental skill for any game developer. Keep experimenting with different layouts and styles to make your game unique! 🎉

*Remember: Great UI is invisible—it just works without players thinking about it.*
