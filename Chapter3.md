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

**Tile Object**: It only makes sense to create a tile object. This object could be responsible for 


**Integers**


#Map Manager

```
class MapManager {
    private List<int> sprites = null;
    
    public int AddTile() {
        sprites.Add(-1);
    }
    
    public void SetSprite(int tile, int spriteId) {
    
}
```