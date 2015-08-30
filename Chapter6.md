#Creating Tiles
The last section left off with an open ended question. What's more important clean code and messy data, or messy code and clean data? Seeing how we're making a game, not a tool i would say having clean code take priority. Games are complicated beasts, it's hard enough to make a game when you write good code, let's not over complicate the code needed for the sake of data.

#Tile Object
The first thing we need is a tile object. For now each object will have two properties, a sprite to display and a boolean to dictate if we can walk on it or not. In the future we will add properties to this tile definition as needed (eq: IsDestructable, IsHidden, etc...). The name of this class is going to simply be ```Tile```:

```
class Tile {
    private int sprite = -1;
    
    public bool Walkable { get; set; }
    
    public int Sprite {
        get {
            return sprite;
        }
        set {
        
        }
    }
}
```