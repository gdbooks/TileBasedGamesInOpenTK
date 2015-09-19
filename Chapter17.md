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