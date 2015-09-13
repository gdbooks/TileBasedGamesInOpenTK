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
Door tiles will need to know two more pieces of information, where the door should take the player in pixels on screen and which room to take the player to. With that in mind, add these two variables to **Tile.cs**

* ```public Map DoorTarget = null;```
* ```public Point DoorLocation = new Point();```

Set them to good defaults (null, and empty Point). Next we're going to make a function that sets both of these, and the IsDoor variable. Lets call this function **MakeDoor** The function is super straight forward, this is what it looks like:

```cs
public void MakeDoor(Map target, Point location) {
    DoorTarget = target;
    DoorLocation = location;
    IsDoor = true;
}
```

That's it, for the moment the ```Tile``` class is done. We're not going to be touching it for the rest of this section.

###Map Refactor
Let's add a new method to the ```Map``` class. This is the method we're going to use to test if a door intersection has happened.Let's call that method ```ResolveDoors``` and give it the following signature:

```cs
public Map ResolveDoors(PlayerCharacter hero) {
```

Go ahead and **try to move** the door related update function out of **Game.cs** and into **Map.cs** I expect you will have a hard time with the code that decides that a collision has happened. Again, the code here is pretty straight forward, i'll include my version:

```cs
public Map ResolveDoors(PlayerCharacter hero) {
    Map result = this; // Return this map by default!

    for (int row = 0; row < tileMap.Length; ++row) {
        for (int col = 0; col < tileMap[row].Length; ++col) {
            if (tileMap[row][col].IsDoor) {
                Rectangle doorRect = new Rectangle(col * 30, row * 30, 30, 30);
                Rectangle playerCenter = new Rectangle((int)hero.Center.X - 2, (int)hero.Center.Y - 2, 4, 4);
                Rectangle intersection = Intersection.Rect(doorRect, playerCenter);

                // Intersection happens if the intersect rectangle has an area > 0
                if (intersection.Width * intersection.Height > 0) {
                    result = tileMap[row][col].DoorTarget;
                    hero.Position.X = tileMap[row][col].DoorLocation.X * 30;
                    hero.Position.Y = tileMap[row][col].DoorLocation.Y * 30;
                }
            }
        }
    }

    return result;
}
```

That's a short, good looking function! And with that we are done refactoring the ```Map``` class. We just need to fix up **Game.cs** now.

###Game Refactor
I order to utilize these shiny new features we've added we have to do some refactoring on **Game.cs**, luckly, it's just two things:

* In initialize, wehre we where setting ```IsDoor`` to true, don't do that
  * Instead call the ```MakeDoor``` function of ```Tile```
* In Update, tear out that big ugly nested loop ment for door checks.
  *  Replace it with: ```currentRoom = currentRoom.ResolveDoors(hero);``` 

And that's it! You should now be able to **Run Your Game**, and it should look exactly like our game did at the begening of the tutorial. The big difference? The code is now maintainable! In fact, the **Game.cs** class ended up so small, i'm going to include it verbatin:

```cs
using GameFramework;
using System.Drawing;
using Collision;

namespace OpenTheDoor {
    class Game {
        protected Point spawnTile = new Point(2, 1);
        protected PlayerCharacter hero = null;
        protected string heroSheet = "Assets/Link.png";
        public OpenTK.GameWindow Window = null;

        Map room1 = null;
        Map room2 = null;
        Map currentRoom = null;

        protected int[][] room1Layout = new int[][] {
            new int[] { 1, 1, 1, 1, 1, 1, 1, 1 },
            new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
            new int[] { 1, 0, 1, 0, 0, 0, 0, 1 },
            new int[] { 1, 0, 0, 0, 0, 1, 0, 1 },
            new int[] { 1, 0, 0, 0, 0, 0, 0, 2 },
            new int[] { 1, 1, 1, 1, 1, 1, 1, 1 }
        };

        protected int[][] room2Layout = new int[][] {
            new int[] { 1, 1, 1, 1, 1, 1, 1, 1 },
            new int[] { 2, 0, 0, 0, 1, 0, 0, 1 },
            new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
            new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
            new int[] { 1, 0, 0, 0, 1, 0, 0, 1 },
            new int[] { 1, 1, 1, 1, 1, 1, 1, 1 }
        };

        protected string spriteSheets = "Assets/HouseTiles.png";
        protected Rectangle[] spriteSources = new Rectangle[] {
            new Rectangle(466,32,30,30),
            new Rectangle(466,1,30,30),
            new Rectangle(32, 187, 30, 30),
            new Rectangle(32, 187, 30, 30)
        };
        
        public Tile GetTile(PointF pixelPoint) {
            return currentRoom[(int)pixelPoint.Y / 30][(int)pixelPoint.X / 30];
        }

        public Rectangle GetTileRect(PointF pixelPoint) {
            int xTile = (int)pixelPoint.X / 30;
            int yTile = (int)pixelPoint.Y / 30;
            Rectangle result = new Rectangle(xTile * 30, yTile * 30, 30, 30);
            return result;
        }

        private static Game instance = null;

        public static Game Instance {
            get {
                if (instance == null) {
                    instance = new Game();
                }
                return instance;
            }
        }

        protected Game() {

        }

        public void Initialize(OpenTK.GameWindow window) {
            Window = window;
            window.ClientSize = new Size(room1Layout[0].Length * 30, room1Layout.Length * 30);
            TextureManager.Instance.UseNearestFiltering = true;

            hero = new PlayerCharacter(heroSheet, new Point(spawnTile.X * 30, spawnTile.Y * 30));
            room1 = new Map(room1Layout, spriteSheets, spriteSources, 0, 2); // 0 and 2 ae walkable
            room2 = new Map(room2Layout, spriteSheets, spriteSources, 0, 2); // 0 and 2 are walkable
            currentRoom = room1;

            room1[4][7].MakeDoor(room2, new Point(1, 1)); // Go to room2, at tile 1, 1
            room2[1][0].MakeDoor(room1, new Point(6, 4)); // To to room1, at tile 6, 4
        }

        public void Update(float dt) {
            hero.Update(dt);
            currentRoom = currentRoom.ResolveDoors(hero);
        }

        public void Render() {
            currentRoom.Render();
            hero.Render();
        }

        public void Shutdown() {
            room1.Destroy();
            room2.Destroy();
            hero.Destroy();
        }
    }
}
```