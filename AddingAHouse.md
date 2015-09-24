#Visual Tweaks
Before moving on to the next section i feel like we need to do some house cleaning. There are a few things i want to change in the code in order to add houses that have depth to them visually. Also, while reviewing your code i found a bug that was made because i gave bad instructions in the last tutorial section.

Do all the work for this section in the **DepthBase** project from the last section.

Let's start off by updating our **HouseTiles.png** to the following image:

![MORE_TILES](Images/HouseTiles_Expanded.png)

Edit ```spriteSources``` in **Game.cs** to include some of the new tiles. I went ahead and measured these out for you:

```cs
protected Rectangle[] spriteSources = new Rectangle[] {
    /* 0*/new Rectangle(466,32,30,30),
    /* 1*/new Rectangle(466,1,30,30),
    /* 2*/new Rectangle(32,187,30,30),
    /* 3*/new Rectangle(32,280,30,40), // Top-left
    /* 4*/new Rectangle(63,280,30,40), // Top-middle
    /* 5*/new Rectangle(94,280,30,40), // Top-right
    /* 6*/new Rectangle(32,321,30,30), // Middle-left
    /* 7*/new Rectangle(63,321,30,30), // Middle-middle
    /* 8*/new Rectangle(94,321,30,30), // Middle-right
    /* 9*/new Rectangle(32,352,30,30),
    /*10*/new Rectangle(63,352,30,30)
};
```

Also, change the layout of room 1 to include the new building. Like so:

```cs
protected int[][] room1Layout = new int[][] {
    new int[] { 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 },
    new int[] { 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 3, 4, 5, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 6, 7, 8, 0, 1, 0, 0, 0, 0, 1, 0, 0, 1 },
    new int[] { 1, 9,10, 9, 0, 0, 0, 0, 0, 0, 1, 0, 0, 2 },
    new int[] { 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1 },
    new int[] { 1, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 }
};
```

After having made these changes **Run the game**, you will notice two errors. First, the bannana is stuck in a building wall. Second, the roof of the building is rendered wrong! This is what it looks like for me:

![BANANA](Images/building_banana.PNG)

The first one is easy, find where we add the bannana and move it's x index from 3 to 4. Fixing the rendering artifact is a little more involved as we have to build a mental model of what's wrong. First, open up **Tile.cs** and take a look at this section of the ```Render``` method:

```cs
Rectangle renderRect = new Rectangle(Source.Location, Source.Size);
if (renderRect.Height != 30) {
    int difference = renderRect.Height - 30;
    renderRect.Y -= difference; // This is a bug!
}
```

The line where we change renderRect.Y is inheritly wrong. This is my fault, i told you to change the wrong variable in the last tutorial. What you actually want to change is ```renderPos.Y```. It makes sense when we thing about it, renderRect is not the position on screen, it's the rectangle to use from the sprite sheet. We don't want to mess with the source sprite, only the render position!

Running the game now, it's still messed up. The height and y of the tile is just not set properly! So let's track down where the height and Y are configured, take a look at the ```constructor``` of **Map.cs**. The issue is in this block of code:

```cs
for (int j = 0; j < layout[i].Length; j++) {
    Rectangle source = sources[layout[i][j]];

    Point worldPosition = new Point();
    worldPosition.X = (int)(j * source.Width);
    worldPosition.Y = (int)(i * source.Height);
    result[i][j] = new Tile(sheets, source);
    result[i][j].Walkable = false;
    result[i][j].IsDoor = false;
    result[i][j].WorldPosition = worldPosition;
    result[i][j].Scale = scale;
    foreach (int w in walkable) {
        if (layout[i][j] == w) {
            tileMap[i][j].Walkable = true;
        }
    }
}
```

The problem is where we calculate worldPosition.X and worldPosition.Y. We multiply i and j by the source tile width and height. This of course assumes that all tiles are going to be uniform sized, an assumption we just broke! Instead we need to hard code the tile size, like so:

```cs
 Point worldPosition = new Point();
worldPosition.X = (int)(j * 30);
worldPosition.Y = (int)(i * 30);
```

There is one last think i'd like to address. If you collect an item and close the game you get a yellow warning in the console about an unreleased texture. This only happens when you collect an item. That tells me that at the point when we remove the item from the map, you don't release it's texture. Go ahead and fix this.

Now we're good to go to the next section! If you run the game you will be able to walk on the top 10 pixels of one of the houses, this is expected behaviour. In the next section we're going to add proper depth sorting to make it look like you are behind the house. Until then, this is what your game will probably look like at this point:

![FIDDLER](Images/fiddler_on_the_roof.PNG)