#Shooting
The enemies can kill the player, but the player can't kill the enemies. Doesn't seem very fair does it? Let's add a simple way for the player to kill an enemy. If during gameplay the player presses the sapce bar he will shoot a bullet that can kill the enemy.

The way to achieve this is actually pretty simple. We're going to implement a bullet class. Whenever space is pressed a bullet will be added to a list. Every frame we loop trough the list. If a bullet has hit a wall we remove it from the list. If a bullet has hit an enemy, we remove the bullet AND the enemy. Simple.

###New Project
Let's make a new project, call it **Shoting** and get this project up to par with the **SimpleEnemy** section of the writeup. We're going to work from here.

###Bullet class
Let's start by adding the new bullet class. This should be an easy one. Bullet has only two members, position and velocity. The update method of the class should apply the velocity to position over time. The render method should just draw a square around the point at which the bullet is. IF you want try to write this class without looking at the below code:

```cs
class Bullet {
    public PointF Position = new PointF(0.0f, 0.0f);
    public PointF Velocity = new PointF(0.0f, 0.0f);

    public Bullet(PointF pos, PointF vel) {
        Position = pos;
        Velocity = vel;
    }

    public void Update(float deltaTime) {
        Position.X += Velocity.X * deltaTime;
        Position.Y += Velocity.Y * deltaTime;
    }

    public void Render() {
        Rectangle renderRect = new Rectangle(0, 0, 10, 10);
        renderRect.X = (int)(Position.X - 5f);
        renderRect.Y = (int)(Position.Y - 5f);

        GraphicsManager.Instance.DrawRect(renderRect, Color.Red);
    }
}
```

###Game Refactor
To actually shoot / use bullets we need to refactor the **Game** class a little bit. First thing is first, add a new member variable, let's call it **projectiles** its type is going to be ```List<Bullet>```, don't forget to initialize this new list.

In the **Render** function, after rendering the hero, but before rendering the game over screen, loop trough every projectile in projectiles and call render on all of them.

In the **Update** method, after updating the hero, but before updating the map, loop trough all theprojectiles and call ```Update``` on each of them, passing in deltaTime. 

At this point we still can't see anything on screen. We need to add some code to actually shoot the projectile. Above the loop that updates all the projectiles, add an if statement. In this if statement check if the space key was pressed. If it was, use the hero objects ```currentSprite``` variable to determine which direction the velocity needs to face. Velocity should be either 90, or -90, that is 3 tiles per second. 

Once you know which way the velocity goes, add a new bullet at the center of the player. Like so:

```cs
if (InputManager.Instance.KeyPressed(OpenTK.Input.Key.Space)3) {
    PointF velocity = new PointF(0.0f, 0.0f);
    if (hero.currentSprite == "down") {
        velocity.Y = 100;
    }
    else if (hero.currentSprite == "up") {
        velocity.Y = -100;
    }
    if (hero.currentSprite == "left") {
        velocity.X = -100;
    }
    else if (hero.currentSprite == "right") {
        velocity.X = 100;
    }
    // projectiles is the name of my member variable that is of type List<Bullet>
    projectiles.Add(new Bullet(hero.Center, velocity));
}
```

**Run the game**, every time you press space there shoudl be a new projectile. Each projectile shoots, and eventually makes it off-screen. You may have notices that we don't have a Destroy function for the projectiles, that's because they don't need a Destroy function. The projectiles just draw squares, if no texture / resources are being held onto then a resource class is useless.

###Map Refactor
Becuse the **Map** class knows about both walkable / non-walkable tiles AND enemies, it seems as good a place as any to add logic that will make bullets dissapear. There are two instances where bullets will dissapear, if they hit a non walkable tile; or if they hit an enemy.

Inside the **Update**method of the ``map``` class. Add a new artument. A ```List<Bullet>```, call this argument ```projectiles```. You want to loop trough this array before looping trough the enemies array. Remember, you are going to be removing items for the array, loop accordinly.

Inside this loop:
* Find the xTile of the projectile
* Fint the yTile of the projectile
* If the xTile is out of bounds
  * remove the loop index (i?) from the projectiles array.
* ELSE If the yTile is out of bounds
  * remove the loop index (i?) from the projectiles array.
* ELSE If the tile at [yTile][xTile] is not Walkable
  * remove the loop index (i?) from the projectiles array. 

Remember, you can use the ```RemoveAt``` function of a vector to remove an item at a specific location. Next, let's update the way you check for enemu collision. Right now you have a loop counting up. Make this loop count down, we are going to potentially be deleting specific enemies from the ```enemies``` array.