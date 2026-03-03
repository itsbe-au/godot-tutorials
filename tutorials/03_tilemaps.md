# Lesson 3: TileMaps & World Building

**Goal:** Build beautiful game worlds using Godot's TileMap system

## Welcome to World Building! 🎮

In the last two lessons, you learned how to make a player move around. But so far, your player has been walking on... nothing! Just empty space.

Today, we're going to become **world architects**! You'll learn how to build entire levels, landscapes, and game worlds using something called **TileMaps**. By the end of this lesson, you'll have created a complete level with grass, walls, and decorations that your player can explore!

## 1. What is a TileMap and Why Use It?

Imagine you're building a house with LEGO bricks. Each LEGO piece is the same size, and you snap them together to create walls, floors, and buildings. **TileMaps work the same way!**

A **TileMap** is a grid system where each square (called a "tile") can display an image. You arrange these tiles to build your game world.

### Why TileMaps Are Awesome:

| Without TileMaps                         | With TileMaps                     |
| ---------------------------------------- | --------------------------------- |
| Place 1000 individual sprites manually   | Paint tiles in seconds            |
| Each sprite uses memory                  | One image file, reused many times |
| Walls don't have collision automatically | Built-in physics support          |
| Hard to edit later                       | Easy to change anytime            |

**Real games that use tiles:**
- Stardew Valley (farming fields)
- Hollow Knight (cave rooms)
- Terraria (dirt and stone)
- Pokémon (routes and cities)

### The TileMap Family:

	TileMap (the main container)
	├── TileMapLayer (floor/grass layer)
	├── TileMapLayer (walls layer)
	├── TileMapLayer (decorations layer)
	└── TileMapLayer (foreground layer)

Each layer is like a transparent sheet you can draw on. You can have grass on one layer, walls on another, and they stack together!

## 2. Creating a TileMap Node

Let's start building! First, we need to add a TileMap to our scene.

### Step-by-Step:

1. **Open your player scene** from Lesson 2 (or create a new scene)

2. **Add a TileMap node:**
   2. Right-click on the root node
   3. Select "Add Child Node"
   4. Search for "TileMap"
   5. Click "Create"

3. **Rename it** to something meaningful like "World" or "Level"

4. **Save your scene** (Ctrl+S)

You should now see a TileMap node in your scene tree, and the editor view will show a grid overlay.

### Understanding the Grid:

The grid you see represents where tiles will go. Each square in the grid is one "tile cell." By default, tiles are 16x16 pixels, but you can change this to any size you want!

**Try it:** Look at the grid overlay. Can you count how many squares fit across your screen?

## 3. Understanding TileSets

Now we have a TileMap, but it's empty! We need to tell it what images to use. This is where **TileSets** come in.

A **TileSet** is like a paint palette - it's a collection of all the tiles you can use in your world.

### Creating Your First TileSet:

1. **With the TileMap selected**, look at the Inspector panel on the right

2. Find the property called **"Tile Set"**

3. Click where it says "[empty]" and select **"New TileSet"**

4. You'll see a TileSet panel appear at the bottom of the screen!

### The TileSet Panel:

The TileSet panel has three main sections:

| Section     | What It Does                          |
| ----------- | ------------------------------------- |
| **Sources** | Add your image files here             |
| **Tiles**   | Configure individual tiles            |
| **Layers**  | Set up collision and other properties |

### 3.1 Atlas Textures

An **Atlas** is a single image file that contains many tiles arranged in a grid. It's like a sprite sheet!

**Why use atlases?**
- One file instead of hundreds
- Easier to manage
- Better for game performance

**Example atlas layout:**
	┌─────┬─────┬─────┬─────┐
	│grass│grass│grass│grass│  <- Row 0: Different grass variations
	├─────┼─────┼─────┼─────┤
	│dirt │dirt │dirt │dirt │  <- Row 1: Different dirt variations
	├─────┼─────┼─────┼─────┤
	│wall │wall │wall │wall │  <- Row 2: Different wall styles
	└─────┴─────┴─────┴─────┘

### Adding an Atlas to Your TileSet:

1. In the **TileSet panel**, click the "Atlas" button (or drag and drop an image)

2. **Select an image file** to use as your atlas. You can:
   2. Use Godot's built-in default tiles
   3. Download free tilesets from sites like OpenGameArt
   4. Create your own in any image editor

3. **Configure the atlas:**
   2. Set "Tile Size" to match your tile dimensions (usually 16x16 or 32x32)
   3. Set "Separation" if there's space between tiles in your image
   4. Set "Margins" if the first tile doesn't start at the edge

4. **Click "Create Tiles"** - Godot will automatically detect all the individual tiles!

**Tip:** Most free tilesets online are 16x16 or 32x32 pixels per tile. If you're not sure, try 16x16 first!

### 3.2 Physics Layers for Collision

We need walls to stop the player from walking through them! This is where **Physics Layers** come in.

**Think of it this way:**
- Visual tiles = what you see (art)
- Physics layers = what you can touch (collision)

### Adding Collision to Tiles:

1. In the TileSet panel, click on the **"Physics Layers"** tab

2. Click **"Add Element"** to create a new physics layer

3. Give it a name like "Solid" or "Walls"

4. **Now select a tile** in the atlas that should be solid (like a wall)

5. In the "Paint Properties" section, make sure "Physics Layer" is selected

6. **Draw a collision shape** over the tile:
   2. Click and drag to create a rectangle
   3. This rectangle is where the player will collide
   4. Usually, you want the collision to cover the entire tile

7. **Repeat for all wall tiles!**

**Visual example:**
	Tile with collision:          Tile without collision:
	┌─────────────┐              ┌─────────────┐
	│  ████████   │              │             │
	│  █ wall █   │  <- solid    │   grass     │  <- walkable
	│  ████████   │              │             │
	└─────────────┘              └─────────────┘

**Important:** Floor tiles (grass, dirt, water) usually DON'T need collision. Only walls, obstacles, and things that should block movement need collision shapes.

### 3.3 Creating Tile Sources

You can have multiple sources in one TileSet! This is great for organizing different types of tiles.

**Example setup:**
	TileSet
	├── Atlas: "Nature" (grass, dirt, flowers)
	├── Atlas: "Buildings" (walls, roofs, doors)
	├── Atlas: "Decorations" (rocks, trees, signs)
	└── Single Tile: "Water animated"

### Adding Multiple Sources:

1. In the TileSet panel, click **"Atlas"** again

2. Add another image

3. Configure it the same way

4. You can now switch between sources when painting!

**Pro tip:** Keep related tiles together in one atlas for easier management.

## 4. Setting Up a Basic Tileset

Let's create a practical example with floor tiles and wall tiles with collision.

### What You'll Need:

**Option 1: Use Godot's built-in tiles**
- Godot 4 includes a default tileset you can use for testing

**Option 2: Download free tiles**
- Visit [OpenGameArt.org][1]
- Search for "16x16 tileset" or "32x32 tileset"
- Download a simple set with grass and walls

**Option 3: Make your own (super easy!)**
- Open any image editor (Paint, GIMP, Photoshop)
- Create a new image that's 64x32 pixels
- Draw 4 grass tiles (16x16 each) in the top row
- Draw 4 wall tiles (16x16 each) in the bottom row
- Save as PNG

### Setting Up Grass/Floor Tiles:

1. Add your atlas to the TileSet

2. **Select all the grass/dirt tiles**

3. Check that they DON'T have collision (no physics layer)

4. These are your walkable floor tiles!

### Setting Up Wall Tiles:

1. **Select a wall tile** from your atlas

2. Go to the Physics Layers tab

3. Make sure your collision layer is active

4. **Paint collision** over the entire tile
   2. Click and drag to draw a rectangle
   3. It should fill most of the tile

5. **Test it:** The tile should now show a red outline when selected (that's the collision!)

### Configuring Tile Properties:

You can also set other properties:

- **Z Index:** Makes tiles appear above or below others (good for bridges)
- **Occlusion:** Creates shadows/lighting (advanced)
- **Navigation:** For pathfinding (if you add AI later)

For now, just focus on collision for walls!

## 5. Painting Tiles in the Editor

Now for the fun part - actually building your world!

### Switching to TileMap Mode:

1. **Select your TileMap node** in the scene tree

2. Look at the **toolbar at the top** of the 2D editor

3. You'll see tilemap painting tools appear:
   2. **Paint Tool** (brush icon) - Place tiles
   3. **Line Tool** - Draw straight lines
   4. **Rectangle Tool** - Draw filled rectangles
   5. **Bucket Fill** - Fill an area
   6. **Eraser** - Remove tiles

### Basic Painting:

1. **Select a tile** from the TileSet panel at the bottom

2. **Click in the editor** to place it!

3. **Hold click and drag** to paint multiple tiles

4. **Use the eraser** to fix mistakes

### Painting Tips:

**Tip 1: Right-click to pick tiles**
- Right-click on any tile in the world to select that tile type
- Super fast way to switch between tiles!

**Tip 2: Use the grid**
- Tiles snap to the grid automatically
- Make sure your grid is visible (View menu → Show Grid)

**Tip 3: Zoom in/out**
- Use Ctrl+Mouse wheel to zoom
- See individual tiles up close or view the whole level

**Tip 4: Undo is your friend**
- Ctrl+Z to undo mistakes
- Ctrl+Shift+Z to redo

## 6. Using TileMapLayer Nodes

Remember how I mentioned layers earlier? Let's use them!

### Why Use Layers?

Layers let you organize your world like this:
	Layer 4: Foreground (things in front of player)
	Layer 3: Player and enemies
	Layer 2: Decorations (flowers, rocks)
	Layer 1: Walls (solid collision)
	Layer 0: Ground (grass, dirt)

### Creating Layers:

1. **Select your TileMap**

2. In the Inspector, find **"Layers"**

3. Click to add layers:
   2. Layer 0: "Ground"
   3. Layer 1: "Walls"
   4. Layer 2: "Decorations"

### Painting on Different Layers:

In the TileSet panel at the bottom, you'll see a **layer selector** dropdown.

1. **Select "Ground" layer** - Paint all your floor tiles
2. **Select "Walls" layer** - Paint walls on top
3. **Select "Decorations" layer** - Add flowers, rocks, etc.

### Z-Index and Ordering:

Each layer can have a different **Z-Index**:

- **Ground layer:** Z-Index 0 (bottom)
- **Walls layer:** Z-Index 1
- **Decorations layer:** Z-Index 2
- **Foreground layer:** Z-Index 10 (above player)

**Why this matters:** If the player walks "behind" a tree, you want the tree to be on a higher layer so it covers the player!

### Organizing Your Scene:

Good practice:
	MainScene
	├── Player (your character)
	├── TileMap (world)
	│   ├── Layer 0: Ground
	│   ├── Layer 1: Walls
	│   └── Layer 2: Decorations
	└── Camera2D (follows player)

## 7. Setting Up Autotiling (Optional but Cool!)

Autotiling is like **magic for level building!** It automatically chooses the right tile variation based on what's around it.

### What is Autotiling?

Imagine you're building a path. Without autotiling:
	grass grass grass grass
	grass PATH  PATH  grass
	grass PATH  PATH  grass
	grass grass grass grass

With autotiling:
	grass grass grass grass
	grass ┌───┐ ┌───┐ grass
	grass │path│ │path│ grass  <- Corners, edges, centers all different!
	grass └───┘ └───┘ grass
	grass grass grass grass

Autotiling automatically picks the right tile so paths connect smoothly!

### Setting Up Autotiling in Godot 4:

1. **In your TileSet**, select the tiles you want to autotile

2. **Create a "Terrain Set"** (new in Godot 4):
   2. Go to the "Terrains" tab
   3. Click "Add Terrain Set"
   4. Give it a name like "Ground"
   5. Choose the mode: "Connect" is best for most cases

3. **Paint terrain bits on each tile**:
   2. Each tile can have different "terrain bits" (corners, edges, centers)
   3. This tells Godot how tiles connect to each other
   4. It's like giving each tile instructions: "I'm a corner piece" or "I'm an edge piece"

4. **Enable terrain painting**:
   2. In the editor toolbar, switch to "Terrain" mode
   3. Select your terrain set
   4. Start painting!

### Autotiling Tips:

**You need special tilesets** for autotiling to work well. The tileset must include:
- Corner pieces
- Edge pieces  
- Center pieces
- All the variations!

**Where to find autotile-ready tilesets:**
- Search for "autotile" or "terrain" on OpenGameArt
- Many modern tilesets include autotile layouts
- Godot's built-in tilesets often support autotiling

**Don't worry if this seems complex!** It's an advanced feature. You can build great levels without it. Come back to autotiling once you're comfortable with basic tile painting.

## 8. Creating a Simple Level

Let's put it all together and build a complete small level!

### Step 1: Plan Your Level

Sketch out a simple design on paper first:

	####################
	#..................#
	#..........######..#
	#..........#....#..#
	#..######..#....#..#
	#..#....#..#....#..#
	#..#....#..######..#
	#..######..........#
	#..................#
	####################
	
	# = walls
	. = floor/empty

### Step 2: Paint the Ground

1. **Select your Ground layer**

2. Choose a grass or floor tile

3. **Paint the entire area** where you want the player to walk

4. **Use the rectangle tool** for large areas - much faster!

### Step 3: Add Walls

1. **Switch to Walls layer**

2. Select a wall tile (with collision!)

3. **Paint walls around the edges** and any internal rooms

4. Leave openings for doorways

### Step 4: Add Decorations

1. **Switch to Decorations layer**

2. Add variety:
   2. Some flowers on the grass
   3. A rock or two
   4. Maybe a signpost

3. **Don't overdo it** - leave space for the player to move!

### Step 5: Test Your Level

1. **Press F6** (or the play button) to run the game

2. **Move your player around**

3. **Check that:**
   2. Walls block movement
   3. You can walk on grass/floor
   4. The level feels fun to explore

### Level Building Tips:

**Start small:** Build one room first, get it working, then expand

**Test often:** Run the game every few minutes to check collision

**Use variety:** Mix 2-3 different grass tiles to make it look natural

**Guide the player:** Use walls to create paths and guide where players go



## 9. Connecting Walls to Player Collision

Your walls are set up, but we need to make sure the player actually collides with them!

### Step 1: Check Your Player Setup

Your player from Lesson 2 should have:
- A CharacterBody2D node (or similar)
- A Sprite2D for the visual
- A CollisionShape2D for physics

### Step 2: Verify Collision Layers

1. **Select your player**

2. Look at the Inspector → "Collision" section

3. Check the **Collision Layer** and **Collision Mask**:
   2. Layer 1 should be on (this is the player)
   3. Mask should include Layer 1 (so player detects walls)

### Step 3: Check TileMap Collision

1. **Select your TileMap**

2. Check that wall tiles have collision shapes (red outline)

3. Verify they're on the correct physics layer

### Step 4: Test Collision

Run the game and try walking into walls:
- **Player should stop** when hitting walls
- **No jittering or glitching** through walls
- **Feels solid and responsive**

### Troubleshooting Collision:

**Problem:** Player walks through walls  
**Solution:** Check that wall tiles have collision shapes in the TileSet

**Problem:** Player gets stuck  
**Solution:** Make sure collision shapes aren't too large. Leave a tiny gap.

**Problem:** Collision feels "mushy"  
**Solution:** Check that both player and walls are using the same collision layer/mask



## 10. Practice Activities

Now it's your turn! Try these challenges:

### Activity 1: Create Different Biomes (15 minutes)

Create three different 10x10 tile areas:

**Forest Biome:**
- Dark green grass
- Tree wall tiles
- Flower decorations
- A path through the middle

**Desert Biome:**
- Sand-colored ground
- Cactus decorations
- Rocky walls
- Maybe some bones or ruins

**Cave Biome:**
- Dark gray stone floors
- Cave walls
- Crystal decorations
- Torches on the walls

### Activity 2: Build a Simple Dungeon (20 minutes)

Create a small dungeon with:
- An entrance room
- A hallway
- A treasure room
- A monster room
- Connect them all with corridors

**Requirements:**
- All walls must have collision
- Player must be able to walk through all rooms
- Use at least 2 different floor tiles
- Add decorations to at least 50% of rooms

### Activity 3: Design a Mini Adventure (20+ minutes)

Create a level that tells a story:
- Start area with a sign saying "Enter the Cave"
- A winding path through the woods
- A cave entrance
- Inside: treasure, danger, mystery!

**Challenge:** Can you guide the player using just walls and floor layouts? Make the path obvious without any text except the starting sign.

### Activity 4: Multi-Level Building (Advanced)

Try using different Z-Indexes:
- Ground floor (Z-Index 0)
- Upper floor/bridge (Z-Index 5)
- Make the player go "under" a bridge and "over" a different part

This requires understanding how Godot's Y-sort works with TileMaps!

### Bonus: Find a Custom Tileset

1. Go to [OpenGameArt.org][2]
2. Download a free tileset that looks cool
3. Import it into Godot
4. Build a level with it
5. Share with your classmates!



## 11. Troubleshooting

### Common Problems and Solutions:

**Problem:** Tiles look blurry or stretched  
**Solution:** Check "Filter" in the import settings. Turn it OFF for pixel art.

**Problem:** Tiles don't line up with the grid  
**Solution:** Check your tile size in the TileSet. It must match your image.

**Problem:** Can't see the grid  
**Solution:** Go to View menu → Show Grid, or press the grid icon in the toolbar.

**Problem:** Player falls through the floor  
**Solution:** Make sure floor tiles are on a separate layer from walls, and walls have collision.

**Problem:** Can't paint tiles  
**Solution:** Make sure you've selected a tile from the TileSet panel first!

**Problem:** Tiles disappear when running the game  
**Solution:** Check that the TileMap node is visible (eye icon in scene tree).

**Problem:** Collision doesn't work in one direction  
**Solution:** Check that wall collision shapes are centered on the tile, not offset.

**Problem:** Game runs slowly with many tiles  
**Solution:** Use larger tile sizes (32x32 or 64x64), or enable "Quadrant Size" optimization in TileMap settings.

## Quick Reference Card

**TileMap Keyboard Shortcuts:**
- `Ctrl + Z` - Undo
- `Ctrl + Shift + Z` - Redo
- `B` - Paint tool
- `L` - Line tool
- `R` - Rectangle tool
- `F` - Bucket fill
- `Shift + Click` - Draw straight lines
- `Right-click` - Pick tile
- `Ctrl + Scroll` - Zoom

**TileMap Concepts:**
- **Tile:** One square in the grid
- **Atlas:** Image with multiple tiles
- **TileSet:** Collection of all your tiles
- **Physics Layer:** Collision information
- **Layer:** Different levels (ground, walls, decorations)

**Important Reminders:**
- Always set collision on wall tiles!
- Use layers to organize your world
- Test collision frequently
- Save your work often
- Have fun building!



## Glossary

| Term              | Definition                                  |
| ----------------- | ------------------------------------------- |
| **Tile**          | A single square image in a grid             |
| **TileMap**       | A node that displays a grid of tiles        |
| **TileSet**       | A collection of tiles and their properties  |
| **Atlas**         | An image containing multiple tiles          |
| **Collision**     | Physics detection that stops movement       |
| **Physics Layer** | A layer that defines collision areas        |
| **Z-Index**       | Determines drawing order (higher = on top)  |
| **Autotiling**    | Automatic tile selection based on neighbors |
| **Terrain**       | A set of tiles that blend together          |




[1]:	https://opengameart.org
[2]:	https://opengameart.org