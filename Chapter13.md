#Clouds
A cloud is a pretty simple construct in a platformer. You can walk trough a clouds left or right side. You can jump from below the clowd over it. So, there is no collision on it's bottom, left or right. However, you can land on a cloud. So it does have collision on the top. In this section, we're going to build a simple cloud. Things like breakable bricks or enemies in super mario are similar, they do one thing from one direction and another thing from another direction.

###New Project
Let's make a new project, call it **Clouds** and get this project up to par with the **Configurable Jumping** section of the writeup. We're going to work from here.

###Updated tile sheet
I've updated **HouseTiles.png** to be a new image. Right click on the below image, save as and overwrite your old one. This tile sheet is still shared between projects, i didn't touch any of the existing tiles, instead the sheet is now larger at 512x512 pixels.

![TILES](Images/HouseTiles_revision1.png)

###Updated rooms
In **Game.cs** replace the **SpriteSources** array with this revised version:

```cs
protected Rectangle[] spriteSources = new Rectangle[] {
    // OLD
    /* 0  */new Rectangle(466,32,30,30),
    /* 1  */new Rectangle(466,1,30,30),
    /* 2  */new Rectangle(32,187,30,30),
    // NEW
    /* 3  */new Rectangle(466, 125,30,30), // blue border
    /* 4  */new Rectangle(311, 249,30,30), // blackness
    /* 5  */new Rectangle(466, 63,30,30), // ground layer
    /* 6  */new Rectangle(63, 218,30,30), // blank ladder
    /* 7  */new Rectangle(156, 218,30,30), // ground ladder
    /* 8  */new Rectangle(63, 249,30,30),  // skele 1
    /* 9  */new Rectangle(94, 249,30,30), // slele 2
    /* 10 */new Rectangle(125, 249,30,30), // skele 3
    /* 11 */new Rectangle(156, 249,30,30), // cloud 1
    /* 12 */new Rectangle(187, 249,30,30), // cloud 2
};
```

Next update room 1 layout and room 2 layout

```cs
protected int[][] room1Layout = new int[][] {
    new int[] { 3, 3, 3, 3, 3, 3, 3, 3 },
    new int[] { 3, 4, 4, 4, 4, 4, 4, 3 },
    new int[] { 3, 4, 4, 4, 4, 4, 4, 3 },
    new int[] { 3, 4,11,12, 4, 4, 4, 3 },
    new int[] { 3, 4, 4, 4, 4, 4, 4, 2 },
    new int[] { 3, 5, 5, 5, 5, 5, 5, 5 }
};
protected int[][] room2Layout = new int[][] {
    new int[] { 3, 3, 3, 3, 3, 3, 3, 3 },
    new int[] { 3, 4, 4, 4, 4, 4, 8, 3 },
    new int[] { 3, 4, 4, 4, 4, 4, 9, 3 },
    new int[] { 3, 4, 4, 4, 4, 4,10, 3 },
    new int[] { 2, 4, 4, 4, 4, 4, 4, 3 },
    new int[] { 5, 5, 5, 5, 5, 5, 5, 3 }
};
```

Find where we make the rooms, and add 4, 8, 9 and 10 as walkable tiles:
```cs
room1 = new Map(room1Layout, spriteSheets, spriteSources, 2, 0, 4, 8, 9, 10);
room2 = new Map(room2Layout, spriteSheets, spriteSources, 0, 2, 4, 8, 9, 10);
```

The new rooms have adjacent doors. **On your own, update the door tiles**.

**Running the game**, you should see these two rooms:

![ROOMS](Images/sidebyside.png)

You should be able to walk around without crashing. Confirm that you can walk from room 1 to room 2 and from room 2 from room one. You are ready to proceed when it works.

###Refactoring Tile
Go into **Tile.cs** and add a new public boolean to the ```Tile``` class. Call this bool ```IsCloud``` and give it a default vlaue of false!

###Refactoring Game
Back in **Game.cs** find the code that makes doors. Immediateley after that set the stone platform (11 and 12) to be clouds. Do this by indexing the map array and setting the ```.IsCloud``` bool in the tiles to true. So far nothing has really changed, if you try to run the game the platform is still solid from all sides.

###Refactoring PlayerCharacter
In the Left / Right movement, find where we check if the tile is walkable, it looks something like this:

```cs
if (!Game.Instance.GetTile(Corners[CORNER_TOP_LEFT]).Walkable) {
```
We have to edit this check, the new check should be **If corner is not walkable AND corner is not cloud**. Remember, both left and right have two checks one for the upper corner and one for the lower corner.

Finally, find where the upper collision does that same check. Modify the if statement in the same way.

**Run the game**, you should be able to do everything you have been able to do before. The big difference is now you can walk under the platform and jump up. It will land you on the platform.

###Smooth landing
The only problem we have at this point is when the player passed trough the platform he was snapped to it. This is a jarring action that we want to avoid. Instead we want to go ahead and let him finish the jump.

The key to finishing the jump is going to be in velocity. Where we check if the feet of the player collide with the top of the platform, we are also going to check the platform type. If the platform type is cloud AND the players velocity is negative then we will not clamp.

Find this bit of code:

```cs
if (!Game.Instance.GetTile(Corners[CORNER_BOTTOM_LEFT]).Walkable) {
    Rectangle intersection = Intersections.Rect(Rect, Game.Instance.GetTileRect(Corners[CORNER_BOTTOM_LEFT]));
    if (intersection.Width * intersection.Height > 0) {
        // Ground Player
```

and change it according to the above description. Here is a pseudo code guide to help you get started:

```cs
if (!Game.Instance.GetTile(Corners[CORNER_BOTTOM_LEFT]).Walkable) {
    Rectangle intersection = Intersections.Rect(Rect, Game.Instance.GetTileRect(Corners[CORNER_BOTTOM_LEFT]));
    if (intersection.Width * intersection.Height > 0) {
        if ( [Not a cloud] OR [Velocity greater than 0]) {
            // Ground Player
```

All we do is wrap the grounding code in another if statement. Remember, the grounding code exists twice, once for the bottom left and once for the bottom right corner. 

What's up with that if statement!?!?! **Not cloud || height > 0** ??? I tought we wanted to check **is cloud && height < 0**!! The thing is, those if statements are logically the same. The logic on each side of the operator is flipped, and so is the operator. This works fine in this case.

**Run the game** standing under the platform, you should now be able to jump trough it, complete the jump and land!

#Done
This was a fairly quick lesson, this is because adding special case tiles is easy. If you need to create something like a brick for super mario, or a bush for the legend of zelda follow the steps we just took. 

* First, you update the tile to contain the new variable. 
* Then you update Game to set the new variable. 
* Last you update player (or enemy, or game) to handle the new special case tile.