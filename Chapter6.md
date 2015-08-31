#Creating Tiles
The last section left off with an open ended question. What's more important clean code and messy data, or messy code and clean data? Seeing how we're making a game, not a tool i would say having clean code take priority. Games are complicated beasts, it's hard enough to make a game when you write good code, let's not over complicate the code needed for the sake of data.

https://dl.dropboxusercontent.com/u/48598159/ZeldaTiles.zip

###Tile Object
The first thing we need is a tile object. For now each object will have two properties, a sprite to display and a boolean to dictate if we can walk on it or not. In the future we will add properties to this tile definition as needed (eq: IsDestructable, IsHidden, etc...). The name of this class is going to simply be ```Tile```:

```
class Tile {
    public int Sprite { get; private set; }
    public Rectangle Source { get; private set; }
    public Point WorldPosition { get; set; }
    public bool Walkable { get; set; }
    public float Scale { get; set; }

    public Tile(string spritePath, Rectangle sourceRect) {
        Sprite = TextureManager.Instance.LoadTexture(spritePath);
        Source = sourceRect;
        Walkable = false;
        WorldPosition = new Point(0, 0);
        Scale = 1.0f;
    }

    public void Render(int x, int y) {
        TextureManager.Instance.Draw(Sprite, new Point(x, y), Scale, Source);
    }
}
```

// TODO: Talk about constructor & render logic

###Game map
So now that we have the definition of what an individual tile is, let's see if we can make a map. The ```map``` should be in a game class, you should be able to figure out how to add a game instance to the window.

```
class Game {
    protected Tile[][] map = null;
    protected int[][] mapLayout = new int[][] {
        new int[] { 1, 1, 1, 1, 1, 1, 1, 1 },
        new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
        new int[] { 1, 0, 1, 0, 0, 0, 0, 1 },
        new int[] { 1, 0, 0, 0, 0, 1, 0, 1 },
        new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
        new int[] { 1, 1, 1, 1, 1, 1, 1, 1 }
    };
}
```

* **map**: a 2D array to hold runtime map information
* **mapLayout**: Meta-data to hold an easily configurable map. 0's are walkable tiles, 1 are not.

As your game gets more and more complicated it may not be possible to store data in a simple representation like the one given above. I'm going to try my best to keep things as simple as possible for the tutorial. 

Now that we know what the map is going to look like, the next step is to actually load the map in! In order to create a tile object we need to know two things, the sprite sheet it's going to be on and the rectangle on that sheet. Before going any further, lets also add this meta-data to the game class

```
class Game {
    protected Tile[][] map = null;
    protected int[][] mapLayout = new int[][] {
        new int[] { 1, 1, 1, 1, 1, 1, 1, 1 },
        new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
        new int[] { 1, 0, 1, 0, 0, 0, 0, 1 },
        new int[] { 1, 0, 0, 0, 0, 1, 0, 1 },
        new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
        new int[] { 1, 1, 1, 1, 1, 1, 1, 1 }
    };
    
    protected string[] spriteSheets = new string[] {
        "Assets/HouseTiles.png",
        "Assets/HouseTiles.png"
    };
    protected Rectangle[] spriteSources = new Rectangle[] {
        new Rectangle(120,1,16,16),
        new Rectangle(120,86,16,16)
    };
}
```

Why did we add two arrays? Well, map layout refers to tiles by numbers, each number coresponds to an element in one of the new arrays we just created. Do we need the ```spriteSheets``` array? Not really. If we have a "world map" atlas that contains EVERY tile in the game, then the ```spriteSheets``` array is a lot of extra work. But what if we want tiles with a number **5** and above to come from an alternate sprite sheet? The ```spriteSheets``` array lets us do just that.

We now have all the information we need to build the map array. Let's create a helper function and call it in initialize.

```
Tile[][] GenerateMap(int[][] layout, string[] sheets, Rectangle[] sources) {
    Tile[][] result = new Tile[layout.Length][];
    float scale = 50.0f;

    for (int i = 0; i < layout.Length; ++i) {
        result[i] = new Tile[layout[i].Length];

        for (int j = 0; j < layout[i].Length; ++j) {
            string sheet = sheets[layout[i][j]];
            Rectangle source = sources[layout[i][j]];
            Point worldPosition = new Point();
            worldPosition.X = (int)(i * source.Width * scale);
            worldPosition.Y = (int)(j * source.Height * scale);

            result[i][j] = new Tile(sheet, source);
            result[i][j].Walkable = layout[i][j] == 0;
            result[i][j].WorldPosition = worldPosition;
            result[i][j].Scale = scale;
        }
    }

    return result;
}

public void Initialize() {
    map = GenerateMap(mapLayout, spriteSheets, spriteSources);
}
```
With ```GenerateMap``` containing all of the logic for initializing tiles you can imagine that it's going to get more complicated as your tiles get more complicated. This is the expected behaviour, it's just something to keep in mind. 

With all of that in place, i think we are ready to render our tiles! Because the tile class has a ```Render``` method that takes care of it's own rendering, this is going to be super simple. Inside the **Game** classes ```Render``` function just call ```Render``` on every tile:

```
public void Render() {
    for (int i = 0; i < map.Length; ++i) {
        for (int j = 0; j < map[i].Length; ++j) {
            float scale = map[i][j].Scale;
            map[i][j].Render((int)(i * scale), (int)(j * scale));
        }
    }
}
```

// TODO: Wrap up & Screen Shot