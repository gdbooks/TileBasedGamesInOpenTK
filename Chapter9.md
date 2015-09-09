#Keys To Move
We have a hero on the screen! But he's not very mighty... He can't even move! Let's fix that. In this section we're going to add the ability to walk around the map.

###New Project
Make a new project, call it **KeysToMove**, we're going to be starting off from the **On Your Own** section of **The Hero**. Meaning your new project should pop up a window, the window has a room, link is in the room and pressing the directional buttons moves link around.

###Player character
In **The Hero** section, we made a ```Character``` class. This class contains the bare minimum information needed to display a character. We could add code to this class for handling input and moving around but that would get messy, after all not every ```Character``` instance will be controlled by the player! We want enemies to be ```Character```s as well.

The best way to solve this delima is to create a new class, we're going to call ```PlayerCharacter```, this new class will extend ```Character``` and add player specific functionality to it! Let's get started. Make a new file, call it **PlayerCharacter.cs** and add the following code:

```cs
using System.Drawing; // For Point
using TheHero; // For Character

namespace KeysToMove {
    class PlayerCharacter : Character {

        public PlayerCharacter(string spriteSheet, Point startPos) : base(spriteSheet, startPos) { 
            // ^ The Constructor which take a string and a Point of the base class (Character)
            // is called using the : notation.
        }
    }
}
```

I don't know if we have used constructor chaining before. Constructor chaining is when a base class does not contain a default constructor, so the child class must call it's parents non-default constructor. This is done by adding ```: base(/*args*/)``` after the consturctor. In our example, we pass the arguments from the ```PlayerCharacter``` constructor to the ```Character``` constructor (refered to as ```base```). 

When chaining constructors you don't always have to forward arguments, the following code would have also been perfectly valid:

```cs
public PlayerCharacter() : base("Default string", new Point(-2, 5)) { 
```

But we want the classes to work the same, so instead we take two arguments and forward them to the base class.

###Updating the character
The ```PlayerCharacter``` and ```Character``` classes at this point are interchangable. Go into your **Game.cs** file and change this line:

```cs
protected Character hero = null;
```

To this:

```cs
protected PlayerCharacter hero = null;
```

Also find this line:

```cs
hero = new Character(heroSheet, new Point(SpawnTile.X * 30, SpawnTile.Y * 30));
```

And change it to this:

```cs
hero = new PlayerCharacter(heroSheet, new Point(SpawnTile.X * 30, SpawnTile.Y * 30));
```

Beacuse the ```PlayerCharacter``` is specific to the hero,  it's safe to put hero specific things in there. Take this bit of code from **Program.cs**:

```cs
hero.AddSprite("Down",new Rectangle(59,1,24,30));
hero.AddSprite("Up", new Rectangle(115,3,22,28));
hero.AddSprite("Left",new Rectangle(1,1,26,30));
hero.AddSprite("Right", new Rectangle(195, 1, 26, 30));
hero.SetSprite("Down");
```

And move it into the constructor of of the ```PlayerCharacter``` class:

```cs
public PlayerCharacter(string spriteSheet, Point startPos) : base(spriteSheet, startPos) {
    // ^ The Constructor which take a string and a Point of the base class (Character)
    // is called using the : notation.

    AddSprite("Down", new Rectangle(59, 1, 24, 30));
    AddSprite("Up", new Rectangle(115, 3, 22, 28));
    AddSprite("Left", new Rectangle(1, 1, 26, 30));
    AddSprite("Right", new Rectangle(195, 1, 26, 30));
    SetSprite("Down");
}
```

At this point you should be able to run the game. The game should display, and link should rotate when you press keys like nothing has happened.

###Moving
Let's add code to actually move the character!



Add an ```Update``` method to the ```PlayerCharacter``` class. It should take one argument, ```deltaTime```.

```cs
public void Update(float deltaTime) {
    // TODO: Add move code
}
```