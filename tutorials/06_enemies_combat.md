# Lesson 6: Enemies & Basic Combat

**Time:** 45-60 minutes  
**Prerequisites:** Lessons 1-5 (Player movement, animations, game flow)  
**Goal:** Create enemies that chase the player and implement basic combat with health and damage

---

## 🎯 What You'll Learn

By the end of this lesson, you'll have:
- Enemies that chase the player around the level
- A working health and damage system
- Invulnerability frames (so you don't die instantly!)
- Knockback when taking damage
- Enemies that can be defeated
- A spawner system for endless action

---

## 🧠 Planning Our Enemy

Before we start coding, let's think about what our enemy should do:

1. **See the player** - The enemy needs to know where the player is
2. **Chase the player** - Move toward the player's position
3. **Attack on contact** - Deal damage when touching the player
4. **Take damage** - Die when hit enough times (optional: player attacks)
5. **Spawn randomly** - Appear at different locations

We'll use a simple **state machine** - think of it like the enemy's brain switching between "idle" and "chase" modes!

---

## 🏗️ Creating the Enemy Scene

### Step 1: Create the Scene File

1. Right-click in the **FileSystem** dock
2. Select **Create Folder** and name it `enemies`
3. Right-click the `enemies` folder → **New Scene**
4. Save it as `enemies/enemy.tscn`

### Step 2: Set Up the Scene Tree

Build this node structure:

```
Enemy (CharacterBody2D)
├── Sprite2D
├── CollisionShape2D
└── AttackArea (Area2D)
    └── CollisionShape2D
```

**Why these nodes?**
- **CharacterBody2D**: Handles movement and physics
- **Sprite2D**: Shows the enemy's appearance
- **CollisionShape2D** (under Enemy): For physics collision with walls/player
- **Area2D** (AttackArea): Detects when enemy touches player to deal damage

### Step 3: Configure Each Node

**Enemy (CharacterBody2D):**
- In the Inspector, add a new script: `enemies/enemy.gd`

**Sprite2D:**
- Drag a texture to the **Texture** property, OR
- Use a simple colored rectangle:
  - Click the Sprite2D
  - In the toolbar, click **2D** → **Create** → **Rectangle**
  - Set color to red (classic enemy color!)

**CollisionShape2D (physics):**
- Click it
- In the toolbar, click **2D** → **Create** → **RectangleShape2D**
- Resize to match your sprite

**AttackArea (Area2D):**
- Add a CollisionShape2D child
- Create a shape slightly LARGER than the enemy sprite
- This creates the "attack zone"

### Step 4: Add to Groups

Click the **Enemy** node, go to the **Node** tab (next to Inspector), click **Groups**, and add:
- `enemy` - So we can find all enemies easily

---

## 💻 Writing the Enemy AI Script

Open `enemies/enemy.gd` and let's code!

### Basic Setup

```gdscript
extends CharacterBody2D

@export var speed: float = 100.0
@export var health: int = 3

var player: Node2D = null
var is_chasing: bool = false

func _ready():
    # Find the player in the scene
    player = get_tree().get_first_node_in_group("player")
```

**What this does:**
- `@export` lets us change values in the editor
- We look for the player using the group we created in earlier lessons
- `is_chasing` tracks if the enemy should move

### The Chase Logic

```gdscript
func _physics_process(delta):
    if player == null:
        return
    
    # Get direction to player
    var direction = (player.global_position - global_position).normalized()
    
    # Set velocity toward player
    velocity = direction * speed
    
    # Flip sprite based on direction
    if direction.x != 0:
        $Sprite2D.flip_h = direction.x < 0
    
    move_and_slide()
```

**How it works:**
1. Calculate direction vector (player position minus our position)
2. `normalized()` makes it a length of 1 (just direction, no distance)
3. Multiply by speed to get velocity
4. `flip_h` makes enemy face left or right

### Adding a State Machine

Let's make the enemy smarter with states:

```gdscript
enum State { IDLE, CHASE }
var current_state = State.IDLE

@export var detection_range: float = 300.0

func _physics_process(delta):
    if player == null:
        return
    
    match current_state:
        State.IDLE:
            velocity = Vector2.ZERO
            check_detection()
        State.CHASE:
            chase_player()
    
    move_and_slide()

func check_detection():
    var distance = global_position.distance_to(player.global_position)
    if distance < detection_range:
        current_state = State.CHASE

func chase_player():
    var direction = (player.global_position - global_position).normalized()
    velocity = direction * speed
    
    if direction.x != 0:
        $Sprite2D.flip_h = direction.x < 0
```

**What's happening:**
- `enum` creates named states (like IDLE = 0, CHASE = 1)
- `match` statement is like multiple if-else checks
- Enemy only chases when player is close enough

---

## ⚔️ Implementing Combat

### Making the Enemy Deal Damage

Add this to the enemy script:

```gdscript
func _ready():
    player = get_tree().get_first_node_in_group("player")
    
    # Connect the Area2D signal
    $AttackArea.body_entered.connect(_on_attack_area_body_entered)

func _on_attack_area_body_entered(body):
    if body.is_in_group("player"):
        # Tell the player to take damage
        if body.has_method("take_damage"):
            body.take_damage(1)
            
            # Optional: knockback the player
            var knockback_dir = (body.global_position - global_position).normalized()
            body.apply_knockback(knockback_dir * 200)
```

### Creating the Player's Health System

Open your player script (probably `player/player.gd`) and add:

```gdscript
@export var max_health: int = 3
var current_health: int
var is_invulnerable: bool = false

func _ready():
    current_health = max_health

func take_damage(amount: int):
    if is_invulnerable:
        return  # Can't take damage while invulnerable
    
    current_health -= amount
    print("Player health: ", current_health)
    
    # Visual feedback
    $Sprite2D.modulate = Color.RED
    
    # Start invulnerability
    is_invulnerable = true
    
    # Flash red for 0.5 seconds
    var timer = get_tree().create_timer(0.5)
    timer.timeout.connect(_on_invulnerability_end)
    
    # Check for death
    if current_health <= 0:
        die()

func _on_invulnerability_end():
    is_invulnerable = false
    $Sprite2D.modulate = Color.WHITE  # Return to normal color

func apply_knockback(force: Vector2):
    velocity += force

func die():
    print("Game Over!")
    # We'll handle game over in GameFlow
    get_tree().reload_current_scene()
```

**Key concepts:**
- **Invulnerability frames**: Brief period where you can't take damage again
- **Visual feedback**: Sprite turns red when hit
- **Knockback**: Pushes player away from enemy

### Testing Combat

1. Add your enemy to the main scene
2. Run the game
3. Walk into the enemy - you should see:
   - Health goes down
   - Sprite flashes red
   - Player gets knocked back

---

## 🎮 Updating GameFlow

Let's make GameFlow handle player damage better. Open `autoloads/game_flow.gd`:

```gdscript
extends Node

var score: int = 0
var player_health: int = 3
var max_health: int = 3

signal health_changed(new_health)
signal score_changed(new_score)
signal game_over

func _ready():
    reset_game()

func reset_game():
    score = 0
    player_health = max_health
    health_changed.emit(player_health)

func damage_player(amount: int = 1):
    player_health -= amount
    health_changed.emit(player_health)
    
    if player_health <= 0:
        game_over.emit()
        # Optional: show game over screen instead of immediate restart

func add_score(points: int):
    score += points
    score_changed.emit(score)
```

Update your player to use GameFlow:

```gdscript
func take_damage(amount: int):
    if is_invulnerable:
        return
    
    GameFlow.damage_player(amount)
    
    # Visual effects and invulnerability...
    is_invulnerable = true
    $Sprite2D.modulate = Color.RED
    
    var timer = get_tree().create_timer(0.5)
    timer.timeout.connect(_on_invulnerability_end)

func _on_invulnerability_end():
    is_invulnerable = false
    $Sprite2D.modulate = Color.WHITE
```

---

## 💀 Enemy Health and Death

### Making Enemies Take Damage

Add to `enemy.gd`:

```gdscript
func take_damage(amount: int):
    health -= amount
    print("Enemy health: ", health)
    
    # Flash white when hit
    $Sprite2D.modulate = Color.WHITE
    var timer = get_tree().create_timer(0.1)
    timer.timeout.connect(func(): $Sprite2D.modulate = Color.RED)
    
    if health <= 0:
        die()

func die():
    # Optional: death animation
    # $AnimationPlayer.play("death")
    # await $AnimationPlayer.animation_finished
    
    queue_free()  # Remove from scene
```

### Optional: Player Attack

If you want the player to attack enemies, add this to player script:

```gdscript
func _input(event):
    if event.is_action_pressed("attack"):
        attack()

func attack():
    # Create a hitbox (you'd add this to your player scene)
    # This is a simple example using raycast or area
    var attack_area = $AttackArea
    for body in attack_area.get_overlapping_bodies():
        if body.is_in_group("enemy") and body.has_method("take_damage"):
            body.take_damage(1)
```

---

## 🏭 Spawning Enemies

### Creating an Enemy Spawner

1. Create a new scene: `enemies/enemy_spawner.tscn`
2. Root node: **Node2D**
3. Add script: `enemies/enemy_spawner.gd`

```gdscript
extends Node2D

@export var enemy_scene: PackedScene
@export var spawn_interval: float = 3.0
@export var max_enemies: int = 5

var spawned_enemies: int = 0

func _ready():
    # Start spawning
    spawn_enemy()
    
    # Set up timer for continuous spawning
    var timer = Timer.new()
    timer.wait_time = spawn_interval
    timer.timeout.connect(_on_spawn_timer)
    add_child(timer)
    timer.start()

func _on_spawn_timer():
    if spawned_enemies < max_enemies:
        spawn_enemy()

func spawn_enemy():
    var enemy = enemy_scene.instantiate()
    
    # Spawn at random offset from spawner
    var random_offset = Vector2(
        randf_range(-50, 50),
        randf_range(-50, 50)
    )
    enemy.global_position = global_position + random_offset
    
    get_parent().add_child(enemy)
    spawned_enemies += 1
    
    # Connect to enemy death to track count
    enemy.tree_exited.connect(_on_enemy_died)

func _on_enemy_died():
    spawned_enemies -= 1
```

### Using the Spawner

1. Add the spawner to your main level scene
2. In the Inspector:
   - Set **Enemy Scene** to your `enemy.tscn` file
   - Adjust **Spawn Interval** (seconds between spawns)
   - Set **Max Enemies** (limit how many exist at once)

3. **Optional: Multiple Spawn Points**
   - Create several spawners at different locations
   - Or use a single spawner that moves to random positions

---

## 🎨 Practice Activities

### Activity 1: Create Different Enemy Types

Make two variants by duplicating `enemy.tscn`:

**Fast but Weak (FastEnemy):**
```gdscript
@export var speed: float = 200.0
@export var health: int = 1
@export var damage: int = 1
```

**Slow but Strong (TankEnemy):**
```gdscript
@export var speed: float = 50.0
@export var health: int = 5
@export var damage: int = 2
```

### Activity 2: Add Attack Cooldown

Prevent enemies from dealing damage every frame:

```gdscript
var can_attack: bool = true
@export var attack_cooldown: float = 1.0

func _on_attack_area_body_entered(body):
    if body.is_in_group("player") and can_attack:
        if body.has_method("take_damage"):
            body.take_damage(1)
            can_attack = false
            
            # Start cooldown
            var timer = get_tree().create_timer(attack_cooldown)
            timer.timeout.connect(func(): can_attack = true)
```

### Activity 3: Enemy Health Bar

Add a simple health bar above enemies:

1. Add to enemy scene:
```
Enemy
├── Sprite2D
├── CollisionShape2D
├── AttackArea
└── HealthBar (ProgressBar)
```

2. Update `enemy.gd`:
```gdscript
@onready var health_bar = $HealthBar

func _ready():
    health_bar.max_value = health
    health_bar.value = health
    # Hide when full for cleaner look
    health_bar.visible = false

func take_damage(amount):
    health -= amount
    health_bar.visible = true
    health_bar.value = health
    # ... rest of damage code
```

### Activity 4: Score on Kill

Update the enemy's die function:

```gdscript
func die():
    GameFlow.add_score(10)  # Give 10 points
    queue_free()
```

---

## 🔧 Troubleshooting

### Enemy doesn't move
- Check: Is the player in the "player" group?
- Check: Did you call `move_and_slide()`?
- Check: Is `speed` set to a value greater than 0?

### Enemy doesn't deal damage
- Check: Is the AttackArea's collision layer/mask correct?
- Check: Is the player body entering the Area2D?
- Add debug print: `print("Hit player!")` in the collision function

### Player takes damage too fast
- Make sure invulnerability frames are working
- Check that `is_invulnerable` is set to true
- Verify the timer is connected properly

### Enemies spawn inside walls
- Adjust the random offset range
- Use a `RayCast2D` to check for walls before spawning
- Or manually place spawn points away from obstacles

### Enemy flips constantly when directly above/below player
- Add a minimum distance check before flipping:
```gdscript
if abs(direction.x) > 0.1:
    $Sprite2D.flip_h = direction.x < 0
```

---

## ✅ Lesson Checklist

You've completed this lesson when:
- [ ] Enemy chases the player around the level
- [ ] Player takes damage on contact
- [ ] Invulnerability frames prevent instant death
- [ ] Visual feedback (flashing) shows damage taken
- [ ] Enemy can be defeated (dies when health reaches 0)
- [ ] Enemies spawn using the spawner system
- [ ] GameFlow tracks player health

---

## 🚀 Next Lesson Preview: Collectibles

In **Lesson 7**, we'll add:
- Coins, power-ups, and health pickups
- Collection animations and effects
- Score multipliers
- Inventory system basics

You'll make your game more rewarding and give players goals to achieve!

---

## 💡 Pro Tips

1. **Balance is key**: Don't make enemies too fast or too strong at first
2. **Visual clarity**: Use different colors for different enemy types
3. **Playtest often**: Try your combat immediately after coding
4. **Keep it fair**: Give the player enough time to react (invulnerability frames!)
5. **Have fun**: Add silly enemy designs - maybe a grumpy square or an angry circle!

---

## 📝 Key Terms

- **State Machine**: A system where an object switches between different behaviors (states)
- **Normalized Vector**: A direction vector with length 1 (pure direction, no distance)
- **Invulnerability Frames**: Brief period where a character can't take damage
- **Knockback**: Force that pushes a character away from impact
- **Collision Layer/Mask**: Determines what objects can collide with each other
- **PackedScene**: A saved scene file that can be instantiated multiple times

---

Happy coding! May your enemies be challenging but fair! 🎮
