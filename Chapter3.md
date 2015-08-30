#Map Format

###Two dimensional arrays
I'm going to talk about two dimensional arrays a LOT. You will quickly find that i don't mean proper C# 2D arrays. I don't EVER use those. 

When i talk about a 2D array i'm talking about a jagged array. An array of arrays, like so:

```
int[][] twoDim = new int[5];
for (int i = 0; i < twoDim.Length; ++i) {
    twoDim[i] = new int[5];
}
```

###Map data
Now that we know we're storing the map in a 2D array, the big question is what the data type of the array supposed to be? With the map we want to have tiles have properties, these should denote things like Is the tile walkable, what sprite should the tile use? etc... We have two options to storing map data:

**Tile Object**: It only makes sense to create a tile object. This object could have member variables denoting IsWalkable, RenderSprite, etc... Tile objects have the advantage of being easy to use. Want to check if an object is walkable? It's as simple as:

```
if (game_map[tileX][tileY].IsWalkable) {
    DoWalk();
}
```

However there is a downside to this method of doing things. It becomes pretty complicated to save the map data. You can't really serialize a two dimensional array of objects to a text file cleanly.


**Integers**: The other option is to create a two dimensional array of integers. Each integer represents a tile type. How would you figure out if a given tile is walkable or not, how would you get the sprite of a tile if we just stored a 2D array of integers? Well, you would need to create a TileManager (Singleton), something like this:

```
class TileManager {
    private List<int> sprites = null;
    
    public int AddTile();
    public void SetSprite(int tile, int spriteId);
}
```

