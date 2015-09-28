#Items and projectiles
Now that the player and enemy are both in isometric space, and 30 is no longer hard-coded anywhere in our code changing the items and the projectiles to be isometric as well is going to be super easy. You might even be able to do it without this guide.

First, we update the sprites to use isometric version. Next, we want to make sure that both projectiles and items work in our debug overhead view. Finally, we apply the isometric projection with the helper function that lives in **Map.cs**. And that's pretty much it.

Here is the updates isometric.png sheet that has items and bullets on it:

![ITEMS](Images/isometric_items.png)

###New projet?
You can either do this in a brand new project, or keep working int he lat isometric one. I'll leave it up to you.

###Updating tile sprites
The source rectangle for an item is defined in the constructor of the ```Item``` object. We make new items (call the constructor) in **Map.cs** in the ```AddItem``` function, which just forwards the ```source``` argument. The ```AddItem``` function is actually called in **Game.cs**. Find the following lines in **Game.cs**

```cs
room1.AddItem(spriteSheets, new Rectangle(350, 255, 16, 16), 10, new Point(4 * TILE_W + 7, 2 * TILE_H + 7));
room1.AddItem(spriteSheets, new Rectangle(381, 256, 13, 15), 20, new Point(5 * TILE_W + 7, 4 * TILE_H + 7));
room2.AddItem(spriteSheets, new Rectangle(412, 256, 16, 15), 30, new Point(4 * TILE_W + 7, 2 * TILE_H + 7));
```

Let's update the source rectangle, all items are going to use the same image. The new source rectangle is: **Rectangle(20, 198, 44, 49)**. While editing these, go ahead and remove all the **+ 7** parts. 7 is the number we hard coded to put each item in the middle of a tile, now that tile sizes are different, 7 holds no meaning.

Also, this line is not valid:

```cs
room1.AddItem(spriteSheets, new Rectangle(20, 198, 44, 49), 10, new Point(4 * TILE_W, 2 * TILE_H));
```

This is because i accidentally changed where one of the obstacles are. instead of the obstacle being at tile 5, 2 it's at 4, 2. Meaning this items is directly inside an obstacle! Update it's position to be at 3, 2.

Go int **Tile.cs** and let's add an if statement to the ```Render``` function to render the debug view of the item. Add an if statement, if ```Game.ViewWorldSpace``` is true we render a debug square, if it's false we render the texture like normal.

The debug rectangle should be at the position specified by the ```Position``` member variable. It should be of color ```Color.DarkSeaGreen```. As for size, both the width and the height are going to be **Source.Width / 2**. The size of the item works the same as the size of the player, so width is sprite width / 2 and height is width.

While we are in the render function, find where we offset the y pixel position of the tile: ```int yTile = (Position.Y - 1) / Game.TILE_H;```, get rid of the -1 offset. Because isometric tiles use a different registration point than cartesian tiles, we no longer need to apply this offset.

**Run the game**, you should now be seeing green item squares on the debug map. So long as your looking at the top down debug map you can even move around and collect the items!

###Adding a shooting sprite
Shooting already works in the debug view. Go ahead, try it. The thing is, bullets never had a sprite to begin with, so we must provide them with one!