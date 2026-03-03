# Lesson 2: Player Movement with CharacterBody2D
  
**Goal:** Create a controllable player character with smooth 8-directional movement

## Overview

In this lesson, you'll bring your game world to life by creating a controllable player character. We'll learn about Godot's physics system, write our first script, and create smooth 8-directional movement that feels great to play.

By the end of this lesson, you'll have:
- A player character that responds to keyboard input
- Smooth movement with acceleration and friction
- A walled area to test collisions
- Understanding of how game physics work in Godot

---

## Part 1: Understanding CharacterBody2D

### What Are Physics Bodies?

Godot provides three main types of physics bodies for 2D games:

| Body Type           | Use Case                     | Key Feature                        |
| ------------------- | ---------------------------- | ---------------------------------- |
| **StaticBody2D**    | Walls, floors, obstacles     | Never moves, just blocks           |
| **RigidBody2D**     | Falling rocks, pushed crates | Realistic physics simulation       |
| **CharacterBody2D** | Player characters, NPCs      | Controlled movement with collision |

### Why CharacterBody2D?

For player characters, we want **responsive control** rather than realistic physics. We want the player to stop immediately when we release a key, not slide around like they're on ice!

**Key features of CharacterBody2D:**
- 🎮 Full control over movement logic
- 🚫 Won't be pushed around by other objects
- ⚡ Perfect for platformers, top-down games, and RPGs
- 🔧 Easy to implement custom movement styles

---

## Part 2: Creating the Player Scene

### Step 1: Create the Player Scene File

1. In the **FileSystem** dock, right-click on your project folder
2. Select **Create Folder** and name it `player`
3. Right-click on the `player` folder
4. Select **New Scene** (or press `Ctrl+N`)
5. Click **Other Node** and search for `CharacterBody2D`
6. Name the root node `Player`
7. Save the scene: **Scene → Save Scene** (or `Ctrl+S`)
8. Save as `res://player/player.tscn`

### Step 2: Add Child Nodes

Your player needs two children:

**Sprite2D** - Displays the player image
**CollisionShape2D** - Defines the hitbox for collisions

Let's add them:

1. **Add Sprite2D:**
   2. With `Player` selected, click the **+** button (Add Child Node)
   3. Search for `Sprite2D` and add it
   4. Rename it to `Sprite2D` (keep default name)

2. **Add a texture:**
   2. In the Inspector, click the `[empty]` next to **Texture**
   3. Select **Load** and choose an image, OR
   4. Select **Quick Load** and type a built-in icon path
   5. Try: `res://icon.svg` (Godot's default icon)

3. **Add CollisionShape2D:**
   2. With `Player` selected, add another child node
   3. Search for `CollisionShape2D` and add it
   4. In the Inspector, click `[empty]` next to **Shape**
   5. Choose **New RectangleShape2D** (or CircleShape2D)
   6. Use the orange handles to resize the shape to match your sprite

### Your Scene Structure Should Look Like:

```
Player (CharacterBody2D)
├── Sprite2D
└── CollisionShape2D
```

### Organizing in the Editor

Make sure the CollisionShape2D outline is the same size as your sprite. This ensures collisions feel accurate to what players see!

## Part 3: Writing Your First GDScript

### What is GDScript?

GDScript is Godot's built-in programming language. It's designed to be:
- **Easy to learn** - Similar to Python
- **Fast to write** - Minimal boilerplate code
- **Integrated** - Built specifically for Godot

### Creating the Script

1. Click on the `Player` node (the root CharacterBody2D)
2. In the Inspector, click the script icon (paper with green +) next to **Script**
3. Click **Create**
4. In the dialog:
   5. **Template:** Node: Default (keep default)
   6. **Path:** Should already show `res://player/player.gd`
   7. **Class Name:** Leave empty
   8. Click **Create**

The script editor opens automatically!

### Understanding the Default Script

You'll see this code:

```gdscript
extends CharacterBody2D


const SPEED = 300.0
const JUMP_VELOCITY = -400.0


func _physics_process(delta: float) -> void:
    # Add the gravity.
    if not is_on_floor():
        velocity += get_gravity() * delta

    # Handle jump.
    if Input.is_action_just_pressed("ui_accept") and is_on_floor():
        velocity.y = JUMP_VELOCITY

    # Get the input direction and handle the movement/deceleration.
    # As good practice, you should replace UI actions with custom gameplay actions.
    var direction := Input.get_axis("ui_left", "ui_right")
    if direction:
        velocity.x = direction * SPEED
    else:
        velocity.x = move_toward(velocity.x, 0, SPEED)

    move_and_slide()
```

This is for a **platformer** (side-view with jumping). We'll modify it for **top-down** movement!

## Part 4: Basic Movement Code

### Modifying for Top-Down Movement

Replace the entire script with this:

```gdscript
extends CharacterBody2D

@export var speed: float = 200.0

func _physics_process(delta: float) -> void:
    # Get input vector from arrow keys or WASD
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    # Set velocity based on input
    velocity = input_vector * speed
    
    # Move the character
    move_and_slide()
```

### Breaking Down the Code

**Line 1: `extends CharacterBody2D`**
- Declares that this script extends (is based on) CharacterBody2D
- Gives us access to all CharacterBody2D features

**Line 3: `@export var speed: float = 200.0`**
- `@export` makes this variable editable in the Inspector
- `var` creates a variable
- `speed` is the variable name
- `: float` specifies the type (a decimal number)
- `= 200.0` sets the default value

**Line 5: `func _physics_process(delta: float) -> void:`**
- `func` defines a function
- `_physics_process` is a built-in Godot function that runs 60 times per second
- `delta` is the time between frames (useful for smooth movement)
- `-> void` means this function doesn't return anything

**Line 6: `var input_vector = Input.get_vector(...)`**
- Gets input as a 2D vector
- Returns values between -1 and 1 for X and Y axes
- Example: Holding right arrow returns (1, 0)

**Line 9: `velocity = input_vector * speed`**
- `velocity` is a built-in property of CharacterBody2D
- Multiplies direction by speed to get movement velocity

**Line 12: `move_and_slide()`**
- Built-in method that moves the character
- Handles collision detection automatically
- Slides along walls when hitting them

### Testing Your Movement

1. Click the **2D** tab to return to the scene editor
2. Open your MainScene (File → Open Recent, or double-click `main_scene.tscn`)
3. Drag your `player.tscn` from the FileSystem into the scene
4. Press **F6** (or click the play button) to run!

Try the arrow keys - your player should move!

## Part 5: Understanding Vectors and Process Functions

### What is a Vector?

A **vector** is like an arrow that has:
- **Direction** - Which way it points
- **Magnitude** - How long it is (strength)

In 2D games, we use **Vector2** which has X and Y components:

```
        (-1, -1)  (0, -1)  (1, -1)
              ↖    ↑    ↗
                \   |   /
        (-1, 0) ← (0, 0) → (1, 0)
                /   |   \
              ↙    ↓    ↘
        (-1, 1)   (0, 1)   (1, 1)
```

**Examples:**
- Moving right: Vector2(1, 0)
- Moving left: Vector2(-1, 0)
- Moving up: Vector2(0, -1) *(Y is negative UP in Godot)*
- Moving diagonally: Vector2(0.707, -0.707) *(normalized)*

### \_process vs \_physics\_process

Godot has two main update loops:

| Function                  | When It Runs                    | Use For                                |
| ------------------------- | ------------------------------- | -------------------------------------- |
| `_process(delta)`         | Every frame (varies by monitor) | Animations, UI updates, visual effects |
| `_physics_process(delta)` | Fixed 60 times/second           | Movement, collisions, physics          |

**Why separate them?**
- Physics needs to be consistent and predictable
- Frame rate varies (60Hz, 144Hz, 30Hz)
- Mixing them causes jittery movement or missed collisions

**Rule of thumb:**
- ✓ Movement, collisions → `_physics_process`
- ✓ Animations, particle effects → `_process`

## Part 6: Adding Acceleration and Friction

### Why Smooth Movement?

The basic code makes the player start and stop instantly. Real characters have:
- **Acceleration** - Time to reach full speed
- **Friction** - Time to slow down and stop

This makes movement feel more natural and satisfying!

### Updated Movement Code

Replace your script with this improved version:

```gdscript
extends CharacterBody2D

@export var max_speed: float = 200.0
@export var acceleration: float = 800.0
@export var friction: float = 1000.0

func _physics_process(delta: float) -> void:
    # Get input vector
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    if input_vector != Vector2.ZERO:
        # Accelerating - moving toward target speed
        velocity = velocity.move_toward(input_vector * max_speed, acceleration * delta)
    else:
        # Decelerating - moving toward zero
        velocity = velocity.move_toward(Vector2.ZERO, friction * delta)
    
    # Move with collision
    move_and_slide()
```

### Breaking Down the Changes

**New Variables:**
- `max_speed` - Top speed the player can reach
- `acceleration` - How quickly we speed up (higher = snappier)
- `friction` - How quickly we slow down (higher = stops faster)

**The `move_toward` Method:**
```gdscript
velocity.move_toward(target, max_delta)
```
- Moves `velocity` toward `target` value
- Won't exceed `max_delta` change per call
- Creates smooth transitions!

**The Logic:**
1. **If moving:** Gradually increase velocity toward max speed
2. **If stopped:** Gradually decrease velocity toward zero
3. **Delta multiplication:** Makes timing frame-rate independent

### Tuning the Feel

Try these values in the Inspector:

| Style            | Max Speed | Acceleration | Friction |
| ---------------- | --------- | ------------ | -------- |
| Tight/Responsive | 300       | 2000         | 2000     |
| Floaty/Ice       | 200       | 400          | 400      |
| Heavy            | 150       | 800          | 600      |
| Default          | 200       | 800          | 1000     |

Experiment! Good game feel takes tweaking.

## Part 7: Making the Player Face Movement Direction (Optional)

### Why Rotate the Sprite?

In many games, the player sprite should face the direction they're moving. This adds polish and helps players understand where they're going.

### Adding Sprite Rotation

Update your script:

```gdscript
extends CharacterBody2D

@export var max_speed: float = 200.0
@export var acceleration: float = 800.0
@export var friction: float = 1000.0

@onready var sprite = $Sprite2D

func _physics_process(delta: float) -> void:
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    if input_vector != Vector2.ZERO:
        velocity = velocity.move_toward(input_vector * max_speed, acceleration * delta)
        
        # Rotate sprite to face movement direction
        sprite.rotation = input_vector.angle()
    else:
        velocity = velocity.move_toward(Vector2.ZERO, friction * delta)
    
    move_and_slide()
```

### How It Works

**`@onready var sprite = $Sprite2D`**
- `@onready` runs when the node enters the scene
- `$Sprite2D` gets the child node named "Sprite2D"
- Now we can reference the sprite easily

**`sprite.rotation = input_vector.angle()`**
- `angle()` returns the direction of the vector in radians
- Rotates the sprite to face that direction
- 0 radians = right, PI/2 = down, PI = left, -PI/2 = up

**Note:** If your sprite faces the wrong direction initially, you can add an offset:
```gdscript
sprite.rotation = input_vector.angle() + PI/2  # Adjust as needed
```

## Part 8: Creating Walls with StaticBody2D

### Why We Need Walls

Right now, your player can walk off into the void! Let's create boundaries.

### Creating a Wall Scene

1. Right-click in FileSystem, create folder `environment`
2. Right-click `environment` folder → **New Scene**
3. Add root node: **StaticBody2D**
4. Rename to `Wall`
5. Add child: **Sprite2D**
   6. Add a texture or color rect
   7. Set **Modulate** color to gray/brown in Inspector
6. Add child: **CollisionShape2D**
   9. Add RectangleShape2D
   10. Match size to sprite
7. Save as `res://environment/wall.tscn`

### Placing Walls in MainScene

1. Open `main_scene.tscn`
2. Drag `wall.tscn` into the scene multiple times
3. Arrange walls to create boundaries:
   4. Top and bottom walls
   5. Left and right walls
   6. Maybe some obstacles in the middle

### Wall Tips

- **Snap to grid:** Enable snap (toolbar button) for easier placement
- **Duplicate:** Select a wall, press `Ctrl+D` to duplicate
- **Resize:** Scale walls in the Inspector for different sizes
- **Organize:** Group walls under a "Walls" node for organization

### Testing Collisions

Run the game (F6) and try walking into walls. Your player should stop or slide along them!

## Part 9: Practice Activities

### Activity 1: Adjust the Movement Feel

Experiment with different movement values:

1. Select your Player in MainScene
2. In the Inspector, find the exported variables
3. Try these combinations:
   4. **Speed demon:** Speed 400, Acceleration 1500
   5. **Slippery:** Speed 200, Friction 300
   6. **Tank:** Speed 100, Acceleration 500, Friction 2000

**Questions:**
- Which feels best for your game?
- How does changing just acceleration affect gameplay?
- What happens if friction is higher than acceleration?

### Activity 2: Add Sprinting

Let's add a sprint button (Shift key):

```gdscript
extends CharacterBody2D

@export var max_speed: float = 200.0
@export var sprint_speed: float = 350.0
@export var acceleration: float = 800.0
@export var friction: float = 1000.0

@onready var sprite = $Sprite2D

func _physics_process(delta: float) -> void:
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    # Check if sprinting
    var current_speed = max_speed
    if Input.is_action_pressed("ui_shift"):  # We'll set this up below
        current_speed = sprint_speed
    
    if input_vector != Vector2.ZERO:
        velocity = velocity.move_toward(input_vector * current_speed, acceleration * delta)
        sprite.rotation = input_vector.angle()
    else:
        velocity = velocity.move_toward(Vector2.ZERO, friction * delta)
    
    move_and_slide()
```

**Setting up the Sprint Input:**

1. Go to **Project → Project Settings**
2. Click the **Input Map** tab
3. Type "sprint" in the action name box
4. Click **Add**
5. Find "sprint" in the list, click the **+**
6. Press **Shift** key, click **OK**
7. Change your code to use `"sprint"` instead of `"ui_shift"`

### Activity 3: Add a Sprint Bar (UI)

Create a stamina system:

```gdscript
extends CharacterBody2D

@export var max_speed: float = 200.0
@export var sprint_speed: float = 350.0
@export var acceleration: float = 800.0
@export var friction: float = 1000.0

var stamina: float = 100.0
var max_stamina: float = 100.0
var is_sprinting: bool = false

@onready var sprite = $Sprite2D

func _physics_process(delta: float) -> void:
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    # Sprint logic
    is_sprinting = Input.is_action_pressed("sprint") and stamina > 0 and input_vector != Vector2.ZERO
    
    var current_speed = max_speed
    if is_sprinting:
        current_speed = sprint_speed
        stamina -= 30 * delta  # Drain stamina while sprinting
    else:
        stamina += 15 * delta  # Regenerate when not sprinting
    
    stamina = clamp(stamina, 0, max_stamina)  # Keep between 0 and 100
    
    if input_vector != Vector2.ZERO:
        velocity = velocity.move_toward(input_vector * current_speed, acceleration * delta)
        sprite.rotation = input_vector.angle()
    else:
        velocity = velocity.move_toward(Vector2.ZERO, friction * delta)
    
    move_and_slide()
```

**Challenge:** Add a visual stamina bar using a ProgressBar node!

## Part 10: Complete Code Reference

### Basic Movement
```gdscript
extends CharacterBody2D

@export var speed: float = 200.0

func _physics_process(delta: float) -> void:
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    velocity = input_vector * speed
    move_and_slide()
```

### Smooth Movement with Acceleration
```gdscript
extends CharacterBody2D

@export var max_speed: float = 200.0
@export var acceleration: float = 800.0
@export var friction: float = 1000.0

func _physics_process(delta: float) -> void:
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    if input_vector != Vector2.ZERO:
        velocity = velocity.move_toward(input_vector * max_speed, acceleration * delta)
    else:
        velocity = velocity.move_toward(Vector2.ZERO, friction * delta)
    
    move_and_slide()
```

### Full Movement with Rotation
```gdscript
extends CharacterBody2D

@export var max_speed: float = 200.0
@export var acceleration: float = 800.0
@export var friction: float = 1000.0

@onready var sprite = $Sprite2D

func _physics_process(delta: float) -> void:
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    if input_vector != Vector2.ZERO:
        velocity = velocity.move_toward(input_vector * max_speed, acceleration * delta)
        sprite.rotation = input_vector.angle()
    else:
        velocity = velocity.move_toward(Vector2.ZERO, friction * delta)
    
    move_and_slide()
```

### Movement with Sprint
```gdscript
extends CharacterBody2D

@export var max_speed: float = 200.0
@export var sprint_speed: float = 350.0
@export var acceleration: float = 800.0
@export var friction: float = 1000.0

@onready var sprite = $Sprite2D

func _physics_process(delta: float) -> void:
    var input_vector = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
    
    var current_speed = max_speed
    if Input.is_action_pressed("sprint"):
        current_speed = sprint_speed
    
    if input_vector != Vector2.ZERO:
        velocity = velocity.move_toward(input_vector * current_speed, acceleration * delta)
        sprite.rotation = input_vector.angle()
    else:
        velocity = velocity.move_toward(Vector2.ZERO, friction * delta)
    
    move_and_slide()
```


## Part 11: Troubleshooting

### Problem: Player doesn't move at all

**Possible causes:**
1. **Script not attached** - Check that `player.gd` appears in the Script property of Player node
2. **Wrong node type** - Ensure root node is CharacterBody2D, not Node2D
3. **No collision shape** - Player needs CollisionShape2D to work with physics
4. **Running wrong scene** - Make sure MainScene is set as main scene (Project → Project Settings → Run → Main Scene)

**Solutions:**
- Verify script attachment in Inspector
- Check node type in scene tree
- Add CollisionShape2D if missing
- Set MainScene in Project Settings

### Problem: Player moves but no collision with walls

**Possible causes:**
1. **Walls don't have collision** - StaticBody2D needs CollisionShape2D
2. **Collision shapes disabled** - Check if shapes are visible (orange outline)
3. **Wrong collision layers** - Both need to be on same physics layer

**Solutions:**
- Add CollisionShape2D to walls
- Ensure shapes are sized properly
- Check collision layers in Inspector (usually both on layer 1 is fine)

### Problem: Movement is jerky or stuttering

**Possible causes:**
1. **Using _process instead of _physics\_process** - Movement must be in \_physics\_process
2. **Missing delta multiplication** - Always multiply speeds by delta
3. **Sprite origin not centered** - Can cause visual jitter

**Solutions:**
- Move code to `_physics_process(delta)`
- Multiply acceleration and friction by delta
- Center sprite texture in image editor or offset in Godot

### Problem: Diagonal movement is faster

**This is actually expected!** When moving diagonally, input\_vector becomes (0.707, 0.707), which has a length of 1.0 (normalized).

If using basic movement, diagonal speed equals single-axis speed. This is correct!

If you WANT diagonal to be faster ( arcade-style), use unnormalized vectors.

### Problem: Player drifts after releasing keys

**Cause:** Friction too low

**Solution:** Increase friction value, or use this instant-stop code:

```gdscript
if input_vector != Vector2.ZERO:
    velocity = velocity.move_toward(input_vector * max_speed, acceleration * delta)
else:
    velocity = Vector2.ZERO  # Instant stop
```

### Problem: Sprite rotates wrong direction

**Cause:** Your sprite image faces a different default direction than Godot expects

**Solution:** Add rotation offset:
```gdscript
sprite.rotation = input_vector.angle() + deg_to_rad(90)  # Adjust 90 as needed
```

Or in the sprite's Inspector, set **Offset** or rotate the Sprite2D node itself 90 degrees in the editor.

## Part 12: Lesson Summary

### What You Learned

✅ **Physics Bodies** - When to use CharacterBody2D vs other types  
✅ **Scene Structure** - Creating nodes and building hierarchy  
✅ **GDScript Basics** - Variables, functions, and game logic  
✅ **Input Handling** - Reading keyboard input with Input.get\_vector()  
✅ **Movement** - Using velocity and move\_and\_slide()  
✅ **Smooth Movement** - Acceleration and friction for game feel  
✅ **Vectors** - Understanding 2D direction and magnitude  
✅ **Process Functions** - When to use _process vs _physics\_process  
✅ **Collisions** - Creating walls with StaticBody2D  

### Key Concepts to Remember

1. **CharacterBody2D** is for player-controlled characters
2. **Always use \_physics\_process** for movement and collision
3. **Velocity** determines movement direction and speed
4. **move\_toward** creates smooth acceleration/deceleration
5. **Input.get\_vector** handles 8-directional input easily
6. **move\_and\_slide()** handles collision detection automatically

### Vocabulary

| Term             | Definition                                  |
| ---------------- | ------------------------------------------- |
| **Vector2**      | A 2D direction with X and Y components      |
| **Velocity**     | Speed and direction of movement             |
| **Acceleration** | Rate of speed increase                      |
| **Friction**     | Rate of speed decrease                      |
| **Delta**        | Time between frames (makes movement smooth) |
| **Collision**    | Detection when two objects touch            |
| **Physics Body** | Node type that interacts with physics       |


## Quick Reference Card

```gdscript
# Get 8-directional input
var input = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")

# Smooth movement
if input != Vector2.ZERO:
    velocity = velocity.move_toward(input * max_speed, acceleration * delta)
else:
    velocity = velocity.move_toward(Vector2.ZERO, friction * delta)

# Apply movement
move_and_slide()
```

**Keyboard Shortcuts:**
- `F6` - Run current scene
- `Ctrl+S` - Save scene/script
- `Ctrl+D` - Duplicate selected node
- `F1` - Help/Documentation

