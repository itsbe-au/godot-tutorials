# Godot 4.6 Top-Down RPG Tutorial Series

**Build Your Own RPG from Scratch!** 🎮

Welcome to the comprehensive tutorial series where you'll build a complete 2D top-down RPG game using Godot 4.6. By the end of this series, you'll have a fully playable game with a controllable character, enemies to fight, treasures to collect, and multiple levels to explore!

---

## What You'll Build

In this series, you'll create a complete RPG game featuring:

- 🎮 A controllable player character with smooth 8-directional movement
- 🗺️ Beautiful game worlds built with tilemaps
- 👾 Enemies that chase the player and deal damage
- ❤️ Health system with invulnerability frames and knockback
- 🪙 Collectible items (coins, potions) with visual effects
- 📊 Heads-up display (HUD) showing health, mana, and score
- 🎵 Sound effects and background music
- 🔄 Multiple scenes with smooth transitions (menu, pause, game over)
- 📦 Complete inventory system

**Perfect for:** Students aged 12-15, beginners to game development, and anyone wanting to learn Godot 4!

---

## Prerequisites

Before starting this tutorial series, make sure you have:

| Requirement | Details |
|-------------|---------|
| **Godot Engine 4.6** | Download from [godotengine.org](https://godotengine.org) |
| **Basic Computer Skills** | File management, typing, using applications |
| **No Prior Coding Required** | We teach GDScript from scratch! |
| **Recommended Age** | 12-15 years old (but suitable for all ages) |
| **Time Commitment** | Approximately 6-8 hours total (8 lessons × 45-60 min each) |

---

## Lesson Overview

### Lesson 1: Project Setup & Godot Basics
**Time:** 45-60 minutes | **Prerequisites:** None

Learn the Godot editor interface, create your first project, and get a sprite on screen. This lesson covers:
- Installing and launching Godot 4.6
- Understanding the editor layout
- Creating nodes and scenes
- Importing and displaying sprites

**First step on your game development journey!**

---

### Lesson 2: Player Movement with CharacterBody2D
**Time:** 45-60 minutes | **Prerequisites:** Lesson 1

Bring your character to life with smooth movement! You'll learn:
- Understanding Godot's physics system (CharacterBody2D, StaticBody2D)
- Writing your first GDScript
- Keyboard input handling
- Smooth acceleration and friction
- Collision detection and response

**By the end:** A character that moves with arrow keys or WASD!

---

### Lesson 3: TileMaps & World Building
**Time:** 45-60 minutes | **Prerequisites:** Lessons 1-2

Become a world architect! Build complete levels using:
- TileMap system for efficient level design
- Creating and configuring TileSets
- Painting terrain (grass, walls, paths)
- Physics layers for collision
- Auto-tiling for beautiful borders

**By the end:** A complete level your player can explore!

---

### Lesson 4: Game Flow Management
**Time:** 45-60 minutes | **Prerequisites:** Lessons 1-3

Learn to manage your game's state with a global manager:
- Autoload/Singletons in Godot
- Creating a GameFlow script
- Score tracking across scenes
- Pause functionality
- Managing game states (playing, paused, game_over)

**By the end:** A centralized system that coordinates your entire game!

---

### Lesson 5: UI & HUD Labels Challenge
**Time:** 45-60 minutes | **Prerequisites:** Lessons 1-4

Create a professional heads-up display:
- CanvasLayer for screen-fixed UI
- Labels for health, mana, and score
- Automatic UI updates using signals
- Positioning and styling UI elements
- Making UI visible and readable

**By the end:** A HUD that displays player stats in real-time!

---

### Lesson 6: Enemies & Basic Combat
**Time:** 45-60 minutes | **Prerequisites:** Lessons 1-5

Add danger to your game! Create enemies that:
- Chase the player using simple AI
- Deal damage on contact
- Implement health and damage systems
- Add invulnerability frames (i-frames)
- Create knockback effects
- Build an enemy spawner system

**By the end:** Enemies that make your game exciting and challenging!

---

### Lesson 7: Collectibles & Inventory
**Time:** 45-60 minutes | **Prerequisites:** Lessons 1-6

Make exploration rewarding with collectibles:
- Creating collectible items (coins, potions)
- Area2D for pickup detection
- Visual effects (animations, particles)
- Inventory management system
- Sound effects for pickups
- Score integration

**By the end:** Treasures scattered throughout your world!

---

### Lesson 8: Scene Transitions & Game States
**Time:** 45-60 minutes | **Prerequisites:** Lessons 1-7

Polish your game with professional features:
- Main Menu with Play/Quit buttons
- Pause menu functionality
- Game Over screen with score
- Multiple levels to progress through
- Smooth fade transitions between scenes
- Scene management best practices

**By the end:** A complete, polished game ready to share!

---

## Recommended Learning Path

```
Week 1: Foundations
├── Lesson 1: Project Setup (Day 1)
├── Lesson 2: Player Movement (Day 2-3)
└── Lesson 3: TileMaps & World Building (Day 4-5)

Week 2: Game Logic
├── Lesson 4: Game Flow Management (Day 1)
├── Lesson 5: UI & HUD (Day 2)
└── Lesson 6: Enemies & Combat (Day 3-4)

Week 3: Polish & Completion
├── Lesson 7: Collectibles (Day 1-2)
└── Lesson 8: Scene Transitions (Day 3-4)
    └── Playtest & Share! (Day 5)
```

### Tips for Success:
1. **Don't skip lessons** - Each builds on the previous
2. **Save often** - Use version control (Git) or frequent saves
3. **Experiment** - Try changing values to see what happens
4. **Take breaks** - Step away if stuck, then return with fresh eyes
5. **Ask questions** - No question is too small!

---

## Tips for Teachers/Instructors

### Before Class:
- **Pre-install Godot 4.6** on all computers
- **Test the lessons yourself** to understand timing
- **Prepare backup assets** in case downloads fail
- **Set up a shared folder** for student resources

### During Class:
- **Pair students** for peer learning and troubleshooting
- **Demonstrate first**, then have students follow along
- **Check progress regularly** - walk around the room
- **Encourage experimentation** after completing core requirements

### Adjusting for Skill Levels:

| Level | Adaptation |
|-------|------------|
| **Beginner (ages 10-12)** | Spend 2 sessions per lesson, focus on visual results |
| **Standard (ages 12-15)** | Follow the 45-60 min timeline as written |
| **Advanced (ages 15+)** | Add challenges, encourage custom features |

### Assessment Ideas:
- **Portfolio:** Students keep a screenshot journal of progress
- **Peer Review:** Students play each other's games
- **Extensions:** Create custom levels, enemies, or power-ups
- **Reflection:** Write about what was challenging and what was learned

---

## Common Troubleshooting

### Godot Won't Open / Crashes
- **Check version:** Make sure you're using Godot 4.6, not 3.x
- **Update drivers:** Update graphics drivers
- **Run as administrator:** On Windows, right-click and select "Run as administrator"

### Player Not Moving
- **Check script attachment:** Make sure the script is attached to the player node
- **Verify input map:** Go to Project → Project Settings → Input Map
- **Print debugging:** Add `print("Moving!")` to your code to test if it's running

### Collisions Not Working
- **Collision layers:** Check that layers and masks are set correctly
- **Collision shapes:** Ensure CollisionShape2D has a shape resource assigned
- **Physics body type:** Use CharacterBody2D for player, StaticBody2D for walls

### Sprites Not Appearing
- **Import settings:** Select the image in FileSystem, check Import tab
- **Visibility:** Check that the node is visible (eye icon in scene tree)
- **Z-index:** Higher Z-index draws on top of lower Z-index

### Signals Not Connecting
- **Verify signal exists:** Check the node actually emits that signal
- **Method name:** Ensure the connected method name is spelled exactly
- **Autoload issues:** Make sure autoload scripts are enabled in Project Settings

### "Node not found" Errors
- **Node paths:** Use relative paths (`$"../NodeName"`) or unique names
- **Tree structure:** Verify nodes are children of the expected parent
- **Loading order:** Some nodes might not exist yet when script runs

### Performance Issues
- **Too many nodes:** Use TileMaps instead of individual sprites
- **Physics:** Limit collision checks, use collision layers wisely
- **Particles:** Reduce particle count or disable when off-screen

---

## Additional Resources

### Official Godot Documentation
- [Godot 4.6 Documentation](https://docs.godotengine.org/en/4.6/)
- [GDScript Reference](https://docs.godotengine.org/en/4.6/tutorials/scripting/gdscript/index.html)
- [2D Game Development](https://docs.godotengine.org/en/4.6/tutorials/2d/index.html)

### Free Assets (for expanding your game)
- [OpenGameArt](https://opengameart.org) - Free sprites, sounds, music
- [Kenney.nl](https://kenney.nl/assets) - High-quality free game assets
- [Itch.io](https://itch.io/game-assets) - Free and paid assets
- [Godot Asset Library](https://godotengine.org/asset-library/asset) - Official asset store

### Learning Communities
- [Godot Discord](https://discord.gg/godotengine) - Active community help
- [r/godot](https://reddit.com/r/godot) - Reddit community
- [Godot Forums](https://godotforums.org) - Discussion and Q&A
- [GDQuest YouTube](https://youtube.com/c/GDQuest) - Video tutorials

### Next Steps After This Series
- **Animation:** Learn AnimationPlayer for cutscenes and complex animations
- **Shaders:** Create visual effects with Godot's shader language
- **Networking:** Add multiplayer to your game
- **Exporting:** Build for Windows, Mac, Linux, Android, iOS, or Web

---

## Ready to Start?

Click on **Lesson 1** above or open `01_project_setup.md` to begin your game development journey!

**Remember:** Every professional game developer started exactly where you are right now. Take your time, have fun, and don't be afraid to make mistakes - that's how we learn! 🚀

---

*Happy Game Making!* 🎮✨

*This tutorial series was created for educational purposes. Feel free to use, modify, and share these lessons in your classroom or learning environment.*
