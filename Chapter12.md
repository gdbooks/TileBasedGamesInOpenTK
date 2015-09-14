#Jumping
Tile games don't al have to be top down. We can change perspectives pretty easily. In this chapter we are going to do just that. Instead of the game playing top-down we're going to make it play from a side view. Mario style! The gameboy zelda games do include some side scrolling sections:

![SIZE_SCROLL](Images/link_side_scroll.png)

###New Project

Let's make a new project, call it **Jumping** and get this project up to par with the cleaned up OpenTheDoor section of the writeup. We're going to work from here.

###PlayerCharacter refactor
One of the most important things in turning our tile engine from top down into a side scroller is to not allow horizontal movement! Let's add a new ```#define``` the top of the **PlayerCharacter.cs**

```
//#define ENABLE_VERTICAL_MOVEMENT
```

Have the new define be commented out as we're not going to be using it! Now go to the ```Update``` function, and find the if-elseif block that handles up and down movement. Put this into a ```#if``` block, add a ```#else``` block, we're going to be working in the ```#else``` block.

```
#if ENABLE_VERTICAL_MOVEMENT
    // Vertical movement code
#else if
    // We're going to be writing the code for this section in here.
#endif
```

###Gravity
Adding gravity is going to be pretty straight forward, first let's declare a new member variable and call it ```gravity```. This veriable represents how many _pixels_ the character is going to fall per second. So, if we wanted him to fall 2 tiles we would have to set gravity equal to 2 * 30.

```cs
protected float gravity = 7 * 30; // Fall 7 tiles / second
```

Now go to the ```#else``` block and add gravity times delta time to the players y position every frame. If you **run the game** the player character will simply fall down, your application might or might not crash. Let's fix this by adding some collision detection.

In the commented out (by the ```#if``` section of code we have some code to handle the case of link trying to walk down and off screen. We're going to copy **ONLY** the collision handling part of that code, and paste it after updating the players position by gravity. This is the bit of code i'm talking about:

```cs
if (!Game.Instance.GetTile(Corners[CORNER_BOTTOM_LEFT]).Walkable) {
    Rectangle intersection = Intersection.Rect(Rect, Game.Instance.GetTileRect(Corners[CORNER_BOTTOM_LEFT]));
    if (intersection.Width * intersection.Height > 0) {
        Position.Y = intersection.Top - Rect.Height;
    }
}
if (!Game.Instance.GetTile(Corners[CORNER_BOTTOM_RIGHT]).Walkable) {
    Rectangle intersection = Intersection.Rect(Rect, Game.Instance.GetTileRect(Corners[CORNER_BOTTOM_RIGHT]));
    if (intersection.Width * intersection.Height > 0) {
        Position.Y = intersection.Top - Rect.Height;
    }
}
```

**Run the game** again and you should now be colliding with tiles. Link starts off on a platform and can walk left or right to fall. You can even enter the other room!

###Jumping Theory
If you are trying to figure out how to make a 2D character jump, google will sooner or later take you to [this stack overflow answer](http://gamedev.stackexchange.com/questions/29617/how-to-make-a-character-jump#29618). The answer gives the standard jump parabola, to jump you need two forces:

* **Gravity** - always pulls the player down
* **Velocity** - can push player up or down

We're going to use these two forces, but they will not both effect the player. Gravity is going to be constantly pulling velocity down. Velocity will be moving the player. We will also introduce a third force, **Impulse**. 

* **Impulse** - force exerted by character to change velocity

Gravity will always be pulling velocity down, and velocity will move the character. So how can velocity ever push the character up? In order to do so we have to apply another force to velocity, this force is impulse. While gravity is constantly pulling down, impulse will suddenly push up.

**Example 1:** Character is at (30, 60). Our velocity is 0. Gavity is 2.
* Frame 1: _Character in initial position_
  * Character is stationary velocity is 0, character at y pixel 60
  * Velocity is pulled down by gravity (2) it's now 2
* Frame 2: _Character starts falling_
  * Character is moved down by velocity (2) to y pixel (58)
  * Velocity is pulled down by gravity (2) it's now 4
* Frame 3:
  * Character is moved down by velocity (4) to y pixel (54)
  * Velocity is pulled down by gravity (2) it's now 6
* Frame 4:
  * Character is moved down by velocity (6) to y pixel (48)
  * Velocity is pulled down by gravity (2) it's now 8
* Frame 5:
  * Character is moved down by velocity (8) to y pixel (40)
  * Velocity is pulled down by gravity (2) it's now 10

As you can see from this 5 frame example, the simple model outlined above has one simple flaw, velocity is constantly increasing! The longer the character falls, the faster he will fall! We're going to fix this by clamping velocity to gravity: ```velocity = Math.Min(velocity, gravity```. I don't think i need to include an example for the clamped velocity. But let's see an example with it in place where we apply an impulse!

**Example 2:** Character is at (30, 60). Our velocity is 0. Gavity is 2. Impulse is -6
* Frame 1: _Character in initial position_
  * Character is stationary velocity is 0, character at y pixel 60
  * Velocity is pulled down by gravity (2) it's now 2
* Frame 2: _Character starts falling_
  * Character is moved down by velocity (2) to y pixel (58)
  * Velocity is pulled down by gravity (2) it's 4, it gets clamped back to 2 (the value of gravity)
* Frame 3:
  * Character is moved down by velocity (2) to y pixel (56)
  * Velocity is pulled down by gravity (2) it's 4, it gets clamped back to 2 (the value of gravity)
* Frame 4:
  * Character is moved down by velocity (2) to y pixel (54)
  * Velocity is pulled down by gravity (2) it's 4, it gets clamped back to 2 (the value of gravity)
* Frame 5:
  * Character is moved down by velocity (2) to y pixel (52)
  * Velocity is pulled down by gravity (2) it's 4, it gets clamped back to 2 (the value of gravity)
* Frame 6: **Impulse is added**
  * Character is moved down by velocity (2) to y pixel (50)
  * Velocity is pulled down by gravity (2) it's 4, it gets clamped back to 2 (the value of gravity)
  * Impulse (-6) is added to velocity (2), velocity is now -4
* Frame 7: _Character is moving up_ 
  * Character is pushed up by velocity (-4) to y pixel (56)
  * Velocity is pulled down by gravity (2) it's now -2  
* Frame 8:
  * Character is pushed up by velocity (-2) to y pixel (54)
  * Velocity is pulled down by gravity (2) it's now 0
* Frame 9: _nothings happening_
  * Character is stationary velocity is 0, character at y pixel 60
  * Velocity is pulled down by gravity (2) it's now 2
* Frame 10: _falling down again_
  * Character is moved down by velocity (2) to y pixel (56)
  * Velocity is pulled down by gravity (2) it's now 2

I feel like a companion gif would be really helpful, but i have no idea how to make proper gif images. If the above doesn't make sense give me a call and i will try my best to explain it!

###Jumping implementation
Let's start by adding our new variables. Gravity and impulse are constant forces, they don't change frame over frame. But velocity does:

```cs
float gravity = 210.0f; // Fall 7 tiles / second, constant
float impulse = -180.0f; // Randomly chosen, constant
// Impulse is negative because the force is going up!
float velocity = 0.0f; // Changes every frame
```

Next, we need to change the update method. 

* Instead of adding ```gravity * deltaTime``` to ```Position.Y``` add ```velocity * deltaTime```
* Above that, add ```gravity * deltaTime``` to velocity! This moves velocity down.
* Don't forget to clamp ```velocity```, it can never be greater than gravity!

**Run the game** now, and nothing has changed! If you did everything correctly you should be able to walk around, fall off the platform, all kinds of fun! Let's add the code to actually jump. Before applying gravity to velocity, check is the space bar is pressed. If it is, **set velocity equal to impulse**. This is how we apply the impulse force. **Run the game now** and you should be able to jump after having fallen off the platform the character starts on.

#Links
* http://excitemike.com/JumpingControlTester
* http://www.piratehearts.com/blog/2012/11/30/deriving-the-mathematics-of-jumping-physics-part-1-of/
* http://error454.com/2013/10/23/platformer-physics-101-and-the-3-fundamental-equations-of-platformers/
* http://higherorderfun.com/blog/2012/05/20/the-guide-to-implementing-2d-platformers/
* http://www.atomjack.net/blog/2014/12/9/dev-blog-designing-a-jump