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

