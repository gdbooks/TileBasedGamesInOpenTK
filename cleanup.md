#Cleaning Up
We can now open doors to multiple rooms in the game. Very cool! But our ```Game``` class got very mangled in the process. There is now considetably more code in there! You can imagint the situation will quickly get out of hand as we add more rooms.

Each room makes for a pretty good object. In this section we're going to clean the code up a bit by refactoring the rooms into objects, and hopefully getting the ```Game``` class back under control.

We're going to call this new object that represent a room ```Map```.

###Tile Refactor
Before we create the new ```Map``` class, lets make a quick change to the ```Tile``` class. In **Tile.cs** add a public bool to this class, call it **IsDoor**. It should be false by default.

Now, inside **Game.cs**, in the ```GenerateMap``` function where we set the ```Walkable``` variable, also set the IsDoor variable. It's going to be true if the layout tile is 2, false otherwise. 

Finally, in **Game.cs** let's change the ```Update``` method. The part where it loops trough the ```currentLayout``` variable and checks the layout index if it's a door or not, this part:

```cs
for (int row = 0; row < currentLayout.Length; ++row) {
    for (int col = 0; col < currentLayout[row].Length; ++col) {
        // The tile is a door if it had a 2 in the layout!
        if (currentLayout[row][col] == 2) {
```

Let's change that to loop trough the tiles (```currentRoom```) and check the new bool we added. The refactored code looks like this:

```cs
for (int row = 0; row < currentRoom.Length; ++row) {
    for (int col = 0; col < currentRoom[row].Length; ++col) {
        // The tile is a door if it had a 2 in the layout!
        if (currentRoom[row][col].IsDoor) {
```

Go ahead, **Run the game**. It plays like nothing has happened. If there is no visual difference, why did we make this change? Simple, this loop and tile check was the only reason we needed to keep the ```currentLayout``` variable around. Now, when we refactor ```Map`` to be it's own object, it won't need to track layout.

Given that there are no more practical references to ```currentLayout``` anymore, go ahead and remove the variable. Delete it's definition and any lingering assignments. Get the code to compile without it.

This isn't the last refactor we're going to make to the Tile class in this section, but let's hold off on the other refactors until they make sense.

###Map class

Go ahead and create a new file, let's call it **Map.cs**. We're not going to add a lot of new code to this class, or the most part we're just going to move code out of **Game.cs** and into here.

A map is just a 2D array of ```Tile``` objects, so lets add a protected 2D ```Tile``` array to this class and call it **tileMap**. If we expose the same API as an array being used, then the ```Map``` class and a 2D int array will behave the same (inside of **Game.cs**). So what do we need to mimic an array API? We need a bracket accessor and a .Length property.

So far our class looks like this:

```cs
using System.Drawing;

namespace OpenTheDoor {
    class Map {
        protected Tile[][] tileMap = null;

        // Accessor to the tileMap variable. With this we can index the map object
        // without directly exposing the underlying tileMap. EX:
        // Map myMap = new Map();
        // myMap[row][col] = new Tile(row, col);
        public Tile[] this[int i] {
            get { return tileMap[i]; }
        }

        // We may need to know how many rows / columns there are in the map, that's what
        // this function is great for!
        public int Length {
            get {
                return tileMap.Length;
            }
        }
    }
}
```

Next, let's add a constructor:

```cs
public Map(int[][] layout, string sheets, Rectangle[] sources, params int[] walkable) {
```

If you notice the constructor here has a very similar signature to the ```GenerateMap``` function inside of **Game.cs**. That's because it essentially does the same thing. Go ahead and migrate the ```GenerateMap``` function into the map constructor.

Take these lines:

```cs
result[i][j].Walkable = layout[i][j] == 0 || layout[i][j] == 2;
result[i][j].IsDoor = layout[i][j] == 2;
```

And replace them with sane defaults (don't worry, we will change these later):

```cs
result[i][j].Walkable = false;
result[i][j].IsDoor = false;
```

After setting all defaults, we want to loop trough the params argument at the end. If the value of the tile we just created is in the walkable array, then we should mark the tile as walkable.  Because an array doesn't have a search function, we will just do a linear search manually:

```cs
foreach (int w in walkable) {
    if (layout[i][j] == w) {
        tileMap[i][j].Walkable = true;
    }
}
```

It makes no sense for the ```Game``` class to have to loop torugh a room and destroy all textures indevidually. Same thing with rendering, why is the ```Game``` class looping? Let's fix that by moving the loops into the ```Map``` class like so:

```cs
public void Render() {
    for (int h = 0; h < tileMap.Length; h++) {
        for (int w = 0; w < tileMap[h].Length; w++) {
            tileMap[h][w].Render();
        }
    }
}

public void Destroy() {
    for (int h = 0; h < tileMap.Length; h++) {
        for (int w = 0; w < tileMap[h].Length; w++) {
            tileMap[h][w].Destroy();
        }
    }
}
```

That's our first round on the ```Map``` object, let's see what we need to do to **Game.cs** to make it work:

###Game refactor
We have to do a little bit of re-factoring in the ```Game``` class to make it work with our new ```Map``` class. Here is a pretty comprehensive list of what needs to happen

* Change ```Tile[][] room1 = null;``` to ```Map room1 = null```
* Repeat the above for room2
* Repeat the above for currentRoom
* Change the ```Initialize``` function to create a new ```Map```
  * Last argument (params) is going to be 2 and 0 as they are walkable
* Inside ```Initialize``` still, map 1: manually set tile 4, 7 IsDoor to true
* Inside ```Initialize``` still, map 2: manually set tile 0, 2 IsDoor to true
* Delet the ```GenerateMap``` function from **Game.cs**
* In the **Game.cs**  ```Destory``` function, don't loop torugh maps. 
  * Call that map object's ```Destroy``` function instead
* In the **Game.cs** ```Render``` function, don't loop trough the currentMap.
  * Instead just call ```currentMap.Render();```

If you need a reference, at this point my **Game.cs** looks like [This](https://gist.github.com/gszauer/e4564c7a53582ecfeedf). 

**Run the game**, at this point your game should run like if nothing had changed.

###Whats next?
The project is a little bit better orgonized now. We removed some code from ***Game.cs**, but not enough. I think we should be able to make the ```Update``` function into a 2 line function. I want it to work like this

```
// PSEUDOCODE, NOT FOR PRODUCTION
public void Update(float dt) {
    hero.Update(dt);
    currentMap = currentMap.ResolveDoors(hero);
```

If the player leaves the room, ```ResolveDoors``` will return the new room the player is in, and move the player to the correct new location. In order to do this the map class will need to be able to check for doors, and the doors will need to know where to take the player. 


###Tile refactor
