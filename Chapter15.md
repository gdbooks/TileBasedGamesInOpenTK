#Simple Enemy
Having a game where your hero is in an empty room is not much fun! Let's add some enemies to the game! For now they are going to be simple enemies, mario style. The enemy will spawn and start walking. It will walk until it hits a wall, then turn around. If at any point the enemy touches the player, the player dies. Just like mario.

###The Plan
How will this work? First off, we need to make an ```EnemyCharacter``` class. Games have multiple ```EnemyCharacter``` classes, one for each type of enemy. So for zelda you might have **MoblinEnemyCharacter**, **ChompChompEnemyCharacter**, **ChickenEnemyCharacter** and so on. We are only going to have one enemy class for now ```EnemyCharacter```. 

So, where do enemies go? Right now the ```Game``` owns the ```PlayerCharacter```. Can we just put an enemy array in ```Game```? Not really. Because our game has multiple rooms (or levels, whatever we are calling them), each room has it's own set of enemies. If you are in ```Room1``` then the enemies in ```Room2``` should not update or render! Therefore it makes sense to add a ```EnemyCharacter``` array inside of the ```Map``` class.

Now that map has a list of enemy characters it's going to need an update method. It needs this so the enemies can update themselves. It would also make sense to check for player collision in here! Instead of the player having to know about all the enemies, we just mape the map know about the player. Because map knows about the enemies and the player it's trivial to check for collision in there.

Finally, we need to add a game over state. This is going to be a simple bool in the ```Game``` class that controls what text we see on screen.

###New Project
Let's make a new project, call it **SimpleEnemy** and get this project up to par with the cleaned up version of the **OpenTheDoor** section of the writeup. We're going to work from here.

###Character refactor
Before creating the enemy class we need to refactor the ```Character``` and ```PlayerCharacter``` classes a bit. Right now all of the animation code is in ```PlayerCharacter``` seeing how both the player and the enemy will animate, we should move this functionality into the **Character** class.

The collision handling code is also all in ```PlayerCharacter```, and both player and enemy are going to collide with the wall, but for now we're going to duplicate the collision code across these two classes.

This refactor is super simple. Take the **Animate** method from ```PlayerCharacter``` and move it into ```Character```. Next take the **animFPS** and **animTimer** variables and also move them into ```Character```. That's it. The animation code is now shared for all characters!

**Run the game**, everything should work like before.

###Creating EnemyCharacter
The enemy characters are going to be simple. They are either going to move up or down. They are going to start walking and keep walking until they hit a wall. Once they hit a wall they are going to change direction. This is about as easy as you can get with an enemy, coincidentally most enemies in Super Mario work like this.

Add a new file, let's call it **EnemyCharacter.cs**, in it make the ```EnemyCharacter``` class a child class of ```Character``` (Just like PlayerCharacter is a subclass of Character). Add three variables to this new class:

* float **speed** = 60.0f;
  * Determines how fast an enemy walks. Measured in pixels per second. 
  * The player moves at 90, so the enemies are a little slower.
  * 60 pixels per second on a 30 pixel grid means enemies move 2 tiles / second
* bool **moveUpDown** = false;
  * If true, the enemy will move in an up and down direction
  * If false, the enemy will move in a left to right direction
* float **direction** = 1.0f;
  * Multiplies speed to determine which direction the enemy walks 
  * If positive the enemy will move down or right
  * If negative the enemy will move up or left
 
The **Constructor** for the enemy class is trivial, it does almost exactly what the Player constructor does. The only exception is the last argument that determines if the enemy is moving up/down or left/right. Also note, the sprite positions are different, i looked these values up using Paint.Net for you.

```cs
public EnemyCharacter(string spritePath, Point pos, bool movingUpDown) : base(spritePath, pos) {
    AddSprite("Down", new Rectangle(68, 112, 29, 30), new Rectangle(101, 112, 29, 30));
    AddSprite("Up", new Rectangle(134, 112, 30, 29), new Rectangle(167, 112, 30, 29));
    AddSprite("Left", new Rectangle(1, 113, 30, 30), new Rectangle(34, 112, 30, 30));
    AddSprite("Right", new Rectangle(201, 112, 30, 29), new Rectangle(234, 113, 30, 29));
    SetSprite("Down");

    moveUpDown = movingUpDown;
    if (!moveUpDown) {
        SetSprite("Right");
    }
}
```

The **Update** method for the enemy is very important. It animates the enemy, moves the enemys position and handles wall collisions. Let's write all of this in one go. I'm going to start you off with a template of the update function that animates and moves the enemy:

```cs
public void Update(float deltaTime) {
    Animate(deltaTime);

    if (moveUpDown) {
        // Direction is VERY important here. If it's positive the enemy moves up.
        // If its negative the player moves down. This happens because direction can
        // only be 1 or -1, so the code essentially translates to:
        // y += +(speed * delta) OR y += -(speed * delta)
        // depending on the value of direction. Nifty! 
        // The alternate to this is to add a bunch of bools (movingLeft, movingRight, etc...)
        // and flip them during collisions and add or subtract from y based on those bools
        // The bool approach is obviously aweful! Just multiplying a positive or -1 is very elegant.
        // Remember this trick, it's going to save you so many if statements down the line.
        Position.Y += direction * speed * deltaTime;
        
        // TODO: Handle wall collision (4 possible collisions)
    }
    else {
        Position.X += direction * speed * deltaTime;
        // TODO: Handle wall collision (4 possible collisions)
    }
}
```

This enemy is taking shape niceley! If we where to add him to the game right now it would just start walking in a direction and never stop. All we have to do is add wall collision and we are done with the enemy class.