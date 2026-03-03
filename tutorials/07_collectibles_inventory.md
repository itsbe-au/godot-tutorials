# Lesson 7: Collectibles & Inventory

**Target Audience:** Middle high school students (ages 12-15)  
**Time:** 45-60 minutes  
**Prerequisites:** Lessons 1-6 (Basic Godot, player movement, scenes, signals, UI basics)

---

## What We'll Build Today

Every great game has collectibles that make exploration rewarding! Today you'll create:

- **Coins** - Increase your score
- **Health Potions** - Restore lost health  
- **Mana Potions** - Restore magical energy
- **Inventory System** - Track what you've collected
- **Juicy Effects** - Make pickups feel AMAZING!

By the end, your game world will be filled with treasures begging to be collected!

---

## 1. Understanding Collectibles

### Types of Collectibles We'll Create

| Collectible | Effect | Visual Style |
|-------------|--------|--------------|
| **Coin** | +10 score | Golden, spinning |
| **Health Potion** | +25 HP | Red liquid in bottle |
| **Mana Potion** | +25 MP | Blue liquid in bottle |

### Why Collectibles Matter

Collectibles:
- Reward exploration
- Guide players through levels
- Create satisfying feedback loops
- Encourage replayability

---

## 2. Creating the Base Collectible Scene

### Step 1: Set Up the Scene

1. Create a new scene: `res://collectibles/collectible_base.tscn`
2. Set the root node as **Area2D** (name it `Collectible`)
3. Add these child nodes:
   - **Sprite2D** - for the visual
   - **CollisionShape2D** - for detection (CircleShape2D works great)
   - **AnimationPlayer** - for bobbing/spinning

### Step 2: Scene Structure

```
Collectible (Area2D)
├── Sprite2D
├── CollisionShape2D
└── AnimationPlayer
```

### Step 3: Configure the Area2D

- **Collision Layer:** Set to "Collectibles" (create this in Project Settings)
- **Collision Mask:** Enable "Player" layer
- This ensures only the player can trigger collection

---

## 3. Creating the Collectible Script

Create `res://collectibles/collectible.gd`:

```gdscript
extends Area2D

@export var collectible_type: String = "coin"
@export var value: int = 10

@onready var sprite = $Sprite2D
@onready var anim_player = $AnimationPlayer

func _ready():
    # Connect the body entered signal
    body_entered.connect(_on_body_entered)
    
    # Start the idle animation
    if anim_player.has_animation("idle"):
        anim_player.play("idle")

func _on_body_entered(body: Node2D):
    # Check if it's the player
    if body.is_in_group("player"):
        collect(body)

func collect(player: Node2D):
    # Call GameFlow to update stats
    match collectible_type:
        "coin":
            GameFlow.add_score(value)
        "health_potion":
            GameFlow.heal_player(value)
        "mana_potion":
            GameFlow.restore_mana(value)
    
    # Spawn effects
    spawn_effects()
    
    # Remove from scene
    queue_free()

func spawn_effects():
    # We'll add particles and floating text here!
    pass
```

---

## 4. Creating Different Collectible Types

### Using @export for Flexibility

Instead of creating separate scenes, use **@export** variables:

```gdscript
@export_enum("coin", "health_potion", "mana_potion") var collectible_type: String = "coin"
@export var value: int = 10
@export var floating_text: String = "+10"
```

### Visual Differentiation

Add to your `_ready()` function:

```gdscript
func _ready():
    body_entered.connect(_on_body_entered)
    update_appearance()
    
    if anim_player.has_animation("idle"):
        anim_player.play("idle")

func update_appearance():
    match collectible_type:
        "coin":
            sprite.modulate = Color.GOLD
            floating_text = "+" + str(value)
        "health_potion":
            sprite.modulate = Color.RED
            floating_text = "+" + str(value) + " HP"
        "mana_potion":
            sprite.modulate = Color.BLUE
            floating_text = "+" + str(value) + " MP"
```

### Creating Variants

Now you can create different instances:

1. **Silver Coin:** `collectible_type = "coin"`, `value = 5`
2. **Gold Coin:** `collectible_type = "coin"`, `value = 10`
3. **Mega Coin:** `collectible_type = "coin"`, `value = 50`
4. **Small Potion:** `value = 25`
5. **Large Potion:** `value = 50`

---

## 5. Creating Bobbing Animation

### Simple Bobbing Effect

Open your **AnimationPlayer** and create an "idle" animation:

1. Add a track for `Sprite2D:position`
2. Set keyframes:
   - Time 0.0s: position.y = -5
   - Time 0.5s: position.y = 5
   - Time 1.0s: position.y = -5
3. Set loop mode to **Linear Loop**

### Add a Spin Effect

Add another track:
- `Sprite2D:rotation`
- Time 0.0s: rotation = 0
- Time 2.0s: rotation = 360 (or 6.28 radians)

Your collectible now floats and spins - irresistible!

---

## 6. Simple Inventory System

### Step 1: Update GameFlow

Add to your `res://autoloads/game_flow.gd`:

```gdscript
extends Node

# Existing stats
var score: int = 0
var player_health: int = 100
var player_mana: int = 100

# NEW: Inventory system
var inventory: Dictionary = {
    "coins": 0,
    "health_potions": 0,
    "mana_potions": 0,
    "keys": 0
}

# Signals
signal score_changed(new_score)
signal health_changed(new_health)
signal mana_changed(new_mana)
signal inventory_changed(item_type, new_count)

func add_to_inventory(item: String, amount: int = 1):
    if inventory.has(item):
        inventory[item] += amount
    else:
        inventory[item] = amount
    
    inventory_changed.emit(item, inventory[item])
    print("Added ", amount, " ", item, " to inventory")

func remove_from_inventory(item: String, amount: int = 1) -> bool:
    if inventory.has(item) and inventory[item] >= amount:
        inventory[item] -= amount
        inventory_changed.emit(item, inventory[item])
        return true
    return false

func has_item(item: String, amount: int = 1) -> bool:
    return inventory.has(item) and inventory[item] >= amount

func add_score(amount: int):
    score += amount
    score_changed.emit(score)
    add_to_inventory("coins", amount)

func heal_player(amount: int):
    player_health = min(player_health + amount, 100)
    health_changed.emit(player_health)
    add_to_inventory("health_potions", 1)

func restore_mana(amount: int):
    player_mana = min(player_mana + amount, 100)
    mana_changed.emit(player_mana)
    add_to_inventory("mana_potions", 1)

func use_health_potion() -> bool:
    if remove_from_inventory("health_potions", 1):
        heal_player(50)
        return true
    return false

func use_mana_potion() -> bool:
    if remove_from_inventory("mana_potions", 1):
        restore_mana(50)
        return true
    return false
```

### Step 2: Display Inventory in UI

Create a simple inventory display:

```gdscript
# In your UI script (res://ui/inventory_display.gd)
extends Control

@onready var coin_label = $CoinLabel
@onready var health_potion_label = $HealthPotionLabel
@onready var mana_potion_label = $ManaPotionLabel

func _ready():
    GameFlow.inventory_changed.connect(_on_inventory_changed)
    update_display()

func _on_inventory_changed(item_type, count):
    update_display()

func update_display():
    coin_label.text = "Coins: " + str(GameFlow.inventory["coins"])
    health_potion_label.text = "HP Potions: " + str(GameFlow.inventory["health_potions"])
    mana_potion_label.text = "MP Potions: " + str(GameFlow.inventory["mana_potions"])
```

---

## 7. Creating Pickup Effects

### Floating Text Effect

Create `res://effects/floating_text.tscn`:

```gdscript
# res://effects/floating_text.gd
extends Label

@export var float_speed: float = 50.0
@export var fade_speed: float = 1.0

func _ready():
    # Start slightly above
    position.y -= 20
    
    # Create tween for floating and fading
    var tween = create_tween()
    tween.set_parallel(true)
    
    # Float up
    tween.tween_property(self, "position:y", position.y - 30, 1.0)
    
    # Fade out
    tween.tween_property(self, "modulate:a", 0.0, 1.0)
    
    # Remove when done
    tween.tween_callback(queue_free).set_delay(1.0)
```

### Update Collectible Script

```gdscript
func spawn_effects():
    # Spawn floating text
    var text = preload("res://effects/floating_text.tscn").instantiate()
    text.text = floating_text
    text.position = global_position
    get_tree().current_scene.add_child(text)
    
    # Play sound (if you have AudioManager)
    # AudioManager.play_sfx("collect")
```

### Particle Effects

Add **CPUParticles2D** to your collectible:

1. Add CPUParticles2D as child of Collectible
2. Configure:
   - **Emitting:** OFF (we'll trigger it)
   - **Amount:** 10
   - **Lifetime:** 0.5
   - **Explosiveness:** 0.8
   - **Direction:** (0, -1) - upward
   - **Spread:** 45
   - **Gravity:** (0, 200)
   - **Initial Velocity:** 100-200
   - **Scale:** 2-4
   - **Color:** Match collectible type

Update your `spawn_effects()`:

```gdscript
@onready var particles = $CPUParticles2D

func spawn_effects():
    # Detach particles so they stay after collectible is freed
    var pos = global_position
    remove_child(particles)
    get_tree().current_scene.add_child(particles)
    particles.global_position = pos
    particles.emitting = true
    
    # Spawn floating text
    var text = preload("res://effects/floating_text.tscn").instantiate()
    text.text = floating_text
    text.position = global_position
    get_tree().current_scene.add_child(text)
    
    # Remove particles after they finish
    await get_tree().create_timer(1.0).timeout
    particles.queue_free()
```

---

## 8. Placing Collectibles in the World

### Method 1: Manual Placement

1. Drag your `collectible_base.tscn` into the level
2. Select it in the scene tree
3. In the Inspector, set:
   - Collectible Type: "coin" (or potion type)
   - Value: Your desired amount
4. Position where desired

### Method 2: Random Spawning (Optional)

Create a spawner:

```gdscript
# res://collectibles/coin_spawner.gd
extends Node2D

@export var spawn_radius: float = 200.0
@export var coin_count: int = 5
@export var collectible_scene: PackedScene

func _ready():
    spawn_coins()

func spawn_coins():
    for i in coin_count:
        var coin = collectible_scene.instantiate()
        
        # Random position within radius
        var angle = randf() * TAU  # TAU = 2 * PI
        var distance = randf() * spawn_radius
        var offset = Vector2(cos(angle), sin(angle)) * distance
        
        coin.position = position + offset
        add_child(coin)
```

---

## 9. Practice Activities

### Activity 1: Create a Treasure Chest

**Challenge:** Create a chest that gives multiple items when opened!

```gdscript
# res://collectibles/treasure_chest.gd
extends Area2D

@export var rewards: Dictionary = {
    "coins": 50,
    "health_potions": 2,
    "mana_potions": 1
}

var opened: bool = false

func _on_body_entered(body):
    if body.is_in_group("player") and not opened:
        open_chest()

func open_chest():
    opened = true
    
    # Give rewards
    for item in rewards:
        GameFlow.add_to_inventory(item, rewards[item])
    
    # Change sprite to open chest
    $Sprite2D.texture = preload("res://assets/chest_open.png")
    
    # Spawn confetti particles!
    spawn_confetti()
    
    # Show "Treasure Found!" text
    show_treasure_text()
```

### Activity 2: Add Key Items

Create a key that unlocks doors:

```gdscript
# res://objects/locked_door.gd
extends StaticBody2D

@export var required_key: String = "dungeon_key"
@export var unlock_message: String = "Door Unlocked!"

func interact(player):
    if GameFlow.has_item(required_key):
        GameFlow.remove_from_inventory(required_key, 1)
        unlock()
    else:
        show_message("Locked! Need: " + required_key)

func unlock():
    # Open door animation
    $AnimationPlayer.play("open")
    await $AnimationPlayer.animation_finished
    queue_free()
```

### Activity 3: Inventory UI Display

**Challenge:** Create a visual inventory grid:

1. Create a new scene: `res://ui/inventory_slot.tscn`
   - TextureRect for item icon
   - Label for count

2. Create `res://ui/inventory_grid.gd`:

```gdscript
extends GridContainer

@export var slot_scene: PackedScene
@export var columns: int = 4

var slots = []

func _ready():
    GameFlow.inventory_changed.connect(update_inventory)
    create_slots()

func create_slots():
    for i in range(12):  # 12 inventory slots
        var slot = slot_scene.instantiate()
        add_child(slot)
        slots.append(slot)

func update_inventory(item_type, count):
    # Find slot for this item or empty slot
    for slot in slots:
        if slot.item_type == item_type or slot.item_type == "":
            slot.set_item(item_type, count)
            break
```

### Activity 4: Item Rarity System

Add rarity colors to your collectibles:

```gdscript
enum Rarity { COMMON, UNCOMMON, RARE, EPIC, LEGENDARY }

@export var rarity: Rarity = Rarity.COMMON

var rarity_colors = {
    Rarity.COMMON: Color.WHITE,
    Rarity.UNCOMMON: Color.GREEN,
    Rarity.RARE: Color.BLUE,
    Rarity.EPIC: Color.PURPLE,
    Rarity.LEGENDARY: Color.ORANGE
}

func _ready():
    sprite.modulate = rarity_colors[rarity]
    
    # Legendary items get a special glow!
    if rarity == Rarity.LEGENDARY:
        add_glow_effect()
```

---

## 10. Troubleshooting

### Common Issues & Solutions

**Problem:** Collectible doesn't disappear when collected

**Solution:** 
- Check if collision layers/masks are set correctly
- Verify the player is in the "player" group
- Add print statements to debug:
```gdscript
func _on_body_entered(body):
    print("Body entered: ", body.name)
    if body.is_in_group("player"):
        print("It's the player! Collecting...")
        collect(body)
```

**Problem:** Particles disappear immediately

**Solution:** Detach particles from collectible before freeing it:
```gdscript
func spawn_effects():
    remove_child(particles)
    get_parent().add_child(particles)
    particles.global_position = global_position
    particles.emitting = true
```

**Problem:** Inventory not updating in UI

**Solution:** 
- Verify the signal is connected:
```gdscript
func _ready():
    if not GameFlow.inventory_changed.is_connected(_on_inventory_changed):
        GameFlow.inventory_changed.connect(_on_inventory_changed)
```
- Check if GameFlow is an autoload in Project Settings

**Problem:** Floating text doesn't appear

**Solution:** 
- Ensure text is added to current_scene, not the collectible
- Check that the floating_text scene path is correct
- Verify the label has visible text and color

**Problem:** Collectibles spawn in walls

**Solution:** Use raycasting to check for valid spawn positions:
```gdscript
func get_valid_spawn_position():
    var space_state = get_world_2d().direct_space_state
    for attempt in 10:
        var pos = get_random_position()
        var query = PhysicsPointQueryParameters2D.new()
        query.position = pos
        var result = space_state.intersect_point(query)
        if result.is_empty():
            return pos
    return position  # Fallback to spawner position
```

---

## 11. Preview of Next Lesson

**Lesson 8: Enemies & Combat**

In the next lesson, you'll:
- Create enemy AI that chases the player
- Implement a simple combat system
- Add health mechanics and damage
- Create different enemy types
- Design combat encounters

Get ready to defend your treasure!

---

## Quick Reference

### Essential Functions

```gdscript
# Check if player has item
if GameFlow.has_item("key"):
    open_door()

# Add item to inventory
GameFlow.add_to_inventory("coins", 10)

# Remove item from inventory
if GameFlow.remove_from_inventory("health_potions", 1):
    heal_player()

# Connect to inventory changes
GameFlow.inventory_changed.connect(func(item, count):
    print(item, " count is now ", count)
)
```

### Visual Polish Checklist

- [ ] Bobbing/floating animation
- [ ] Spinning or pulsing effect
- [ ] Particle burst on collect
- [ ] Floating text showing value
- [ ] Sound effect (optional)
- [ ] Different colors for different types
- [ ] Glow effect for rare items

---

## Congratulations! 

Your game world is now filled with treasures! Players will love exploring every corner to find coins, potions, and special items.

**Next Steps:**
1. Place collectibles throughout your level
2. Balance the economy (how many coins feel right?)
3. Add visual variety with different sprites
4. Consider adding secret/hidden collectibles
5. Test that everything feels fun to collect!

Happy collecting! 🎮✨
