#Map Format
Knowing why we're going to use tiles, let's talk about how we are going to be treating them. One of the most important things in any good tile based game is the map. The map contains our tile data, the levels layout as well as information about each tile. Let's explore different ways we can represent this map.

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
    private List<bool> walkable = null;
    // One list for each property
    
    // Singleton code & constructor that creates lists
    
    public int AddTile() {
        sprites.Add(-1);
        walkable.Add(false);
    }
    
    public void SetWalkable(int tile, bool canWalk) {
        walkable[tile] = canWalk;
    }
    
    public bool GetWalkable(int tile) {
        return walkable[tile];
    }
    
    // Getters / setters for other properties
}
```

Using this method we would find out if a tile is walkable like so:

```
if (TileManager.Instance.GetWalkable(game_map[tileX][tileY])) {
    DoWalk();
}
```

As you can see, the code has become a LOT more verbose. At runtime we need to do a considerable amount of typing to achieve the same thing as we did with objects. So, is there an upside to this method? Yes, writing the map to a file and reading it back becomes dead simple! We write a few lists, and a two dimensional array to our saved map file. It's quick and easy!

##Next Steps
Having some context on the two main methods of storing tile info, i want you to take a few minutes and think about how you would do things. Which method makes more sense to you? What's more important, having clear code and messy data or having messy code and clear data? There is no silver bullet for anything in programming, everything is a compromise, it's your job as a programmer to find the best option.