#Hit The Wall
Having a hero who can walk is fun, but it would be even more fun if the hero could hit the brick wall... With his face! Handling collisions with walls is not that hard. We've done this in **WinFormGames** before. Given this situation:

![COLLISION1](Images/col1.PNG)

We first want to find the rectangle for both link (outlined blue) and the obstacle (outlined green):

![COLLISION2](Images/col2.png)

Using these two rectangles we want to see if they intersect. To do this we look for an intersection rectangle (outlined in red):

![COLLISION3](Images/col3.png)

If the intersection(red) rectangle has an area greater than 0 (That is, if a collision has happened) we must resolve the collision. We do this by moving the character to the left, right up or down by the width or height of the intersection.

###Intersection
From the above description it becomes clear that we need to be able to get an intersection rectangle between two rectangles. We're going to add this ability to a common helper class.

###A better way
Some of this code might seem a bit convoluted, expecially because we move link horizontally, resolve collisions, then move him vertically. It's a bit of a mess. But it's not a bad way to handle the situation. All mission critical code is local to the ```PlayerCharacter``` class. 

If it makes more sense to you, adding the player movement and collision resolution code directly into the ```Game``` class is also a valid option. It will bloat the size of the **Game.cs** file, but the update will be easyer to read. Something like this:

```
// PSEUDO CODE, NOT PRODUCTION!

void Game.Update(float dt) {
    InputManager.Update();
    
    // Update the hero
    if (GetKey(Keys.Right)) {
        hero.Animate(dt);
        
        hero.Position.X += speed * dt;
        if (IsCollidingWithMap(hero)) {
            hero.Position.X -= GetCollisionWidth(hero, GetCollisionTile(hero));
        }
    }
    else if (GetKey(Keys.Left)) {
        hero.Animate(dt);
        
        hero.Position.X -= speed * dt;
        if (IsCollidingWithMap(hero)) {
            hero.Position.X += GetCollisionWidth(hero, GetCollisionTile(hero));
        }
    }
    
    // Do the same for Up and Down
    hero.SetAnimStateToDefault();
    
    // TODO: Update all enemies and other objects
}
```

You can see how this **DEEPLY** couples the ```PlayerCharacter``` and ```Game``` classes together. This deep coupling is why we didn't do it like this in the tutorial. But this does reinforce the above statement about the nature of moving objects:

* First,move the Object
* Next, check if there is a collision
  * If there was resolve the collision

Collisions are usually resolved by moving the character back by some intersection amount.

Anyway, the way we did collision is one way. The way outlined in Pseudo Code above is another way. And there are many, many more ways of handling all of this. Once we get to 3D objects, we will see other ways of handling these situations.

For now spend some time thinking about how we are handling and resolving collisions. Are you happy with it? If you are we will move on. If you're not how would you make the situation better? Try to code it up, see if you can make your own version work!