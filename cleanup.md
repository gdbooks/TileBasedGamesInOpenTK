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

