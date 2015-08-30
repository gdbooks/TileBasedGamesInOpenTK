#Creating Tiles
The last section left off with an open ended question. What's more important clean code and messy data, or messy code and clean data? Seeing how we're making a game, not a tool i would say having clean code take priority. Games are complicated beasts, it's hard enough to make a game when you write good code, let's not over complicate the code needed for the sake of data.

###Tile Object
The first thing we need is a tile object. For now each object will have two properties, a sprite to display and a boolean to dictate if we can walk on it or not. In the future we will add properties to this tile definition as needed (eq: IsDestructable, IsHidden, etc...). The name of this class is going to simply be ```Tile```:

```
class Tile {
    public int Sprite { get; private set; }
    public bool Walkable { get; set; }
    
    public Tile(string spritePath) {
        Sprite = TextureManager.Instance.LoadTexture(Sprite);
    }
}
```

###Game map
So now that we have the definition of what an indevidual tile is, let's see if we can make a map. The ```map``` variable lives inside your ```Game``` class, *NOT* inside of the ```Tile``` class.

```
protected Tile[][] map = null;
protected Size mapSize = new Size(8, 6);
protected int[][] mapLayout = new int[
    new int[],
    new int[],
    new int[],
    new int[],
    new int[],
    new int[]
];

public void Initialize() {
    map = new Tile[mapSize.Width][];
    for (int i = 0; i < mapSize.Width; ++i) {
        map[i] = new Tile[mapSize.Height];
    }
}

```