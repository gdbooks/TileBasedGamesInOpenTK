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

Beacuse the ```PlayerCharacter``` is specific to the hero,  it's safe to put hero specific things in there. Take this bit of code from **Game.cs**:

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
Let's add code to actually move the character! In order to move the character we have to do a little bit of house cleaning first. 

Currently we keep track of the ```Character``` position in a **Point**. This won't work, in order to move around the screen we need to store values as floating points. Find the following line in **Character.cs** 

```cs
public Point Position { get; set; }
```

and change it to

```cs
public PointF Position { get; set; }
```

If you try to compile now, the compiler will throw an error. This is because our render method is trying to pass a **PointF** variable when a **Point** is expected. Still in **Character.cs**, find this bit of code:

```cs
public void Render() {
    TextureManager.Instance.Draw(Sprite, Position, 1.0f,spriteSource[currentSprite]);
}
```

and change it to:

```cs
public void Render() {
    TextureManager.Instance.Draw(Sprite, new Point((int)Position.X, (int)Position.Y), 1.0f,spriteSource[currentSprite]);
}
```

And you should be able to compile again! Back in **PlayerCharacter.cs** lets add a speed variable. This varaible is going to determine how fast the player moves. I'm going to set it to 90.0f, that is, move 90 pixels every second or 3 tiles in one second.

```cs
namespace KeysToMove {
    class PlayerCharacter : Character {
        float speed = 90.0f; // NEW

        public PlayerCharacter(string spriteSheet, Point startPos) : base(spriteSheet, startPos) {
```

Next let's add an ```Update``` method to the ```PlayerCharacter``` class. It should take one argument, ```deltaTime```.

```cs
public void Update(float deltaTime) {
    // TODO: Add move code
}
```

This method is going to be responsible for checking Keyboard Input. When the appropriate keys are pressed it's going to update the inherited ```Position``` property to make the character move (Remember, the character is rendered at ```Position```) Let's fill in the missing code:

```cs
public void Update(float deltaTime) {
    InputManager i = InputManager.Instance;

    PointF positionCpy = Position; // Don't forget to change to PointF in parent class
    // The Position getter is inherited from the Character class. 
    // We simply store a COPY of it here, so we can modify the 
    // X and Y properties indevidually. We also make 

    if (i.KeyDown(OpenTK.Input.Key.Left) || i.KeyDown(OpenTK.Input.Key.A)) {
        positionCpy.X -= speed * deltaTime;
    }
    else if (i.KeyDown(OpenTK.Input.Key.Right) || i.KeyDown(OpenTK.Input.Key.D)) {
        positionCpy.X += speed * deltaTime;
    }

    if (i.KeyDown(OpenTK.Input.Key.Up) || i.KeyDown(OpenTK.Input.Key.W)) {
        positionCpy.Y -= speed * deltaTime;
    }
    else if (i.KeyDown(OpenTK.Input.Key.Down) || i.KeyDown(OpenTK.Input.Key.S)) {
        positionCpy.Y += speed * deltaTime;
    }

    Position = positionCpy; // Move the copy we made back into the Postion variable
}
```

Running the game now, pressing the arrow keys.... Nothing moves! What gives? Well, we added an ```Update``` method to the ```PlayerCharacter``` class, but we are not calling this method anywhere! Let's go into **Game.cs** and add this ```Update``` call to the ```Game```s update method:

```cs
public void Update(float dt) {
    InputManager i = InputManager.Instance; //local ref to input manager
    //using i just saves time
    if (i.KeyDown(OpenTK.Input.Key.Left) || i.KeyDown(OpenTK.Input.Key.A)) {
        hero.SetSprite("Left");
    }
    if (i.KeyDown(OpenTK.Input.Key.Right) || i.KeyDown(OpenTK.Input.Key.D)){
        hero.SetSprite("Right");
    }
    if (i.KeyDown(OpenTK.Input.Key.Up) || i.KeyDown(OpenTK.Input.Key.W)) {
        hero.SetSprite("Up");
    }
    if (i.KeyDown(OpenTK.Input.Key.Down) || i.KeyDown(OpenTK.Input.Key.S)) {
        hero.SetSprite("Down");
    }
    
    hero.Update(dt);
}
```

Run the game now, and TADA! Your hero is moving! He's not colliding with any walls.... But we can fix that later. For now what matters is that the hero moved!

###Cleanup