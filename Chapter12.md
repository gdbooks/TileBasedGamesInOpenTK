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
#Links
* http://excitemike.com/JumpingControlTester
* http://www.piratehearts.com/blog/2012/11/30/deriving-the-mathematics-of-jumping-physics-part-1-of/
* http://error454.com/2013/10/23/platformer-physics-101-and-the-3-fundamental-equations-of-platformers/
* http://higherorderfun.com/blog/2012/05/20/the-guide-to-implementing-2d-platformers/
* http://www.atomjack.net/blog/2014/12/9/dev-blog-designing-a-jump
* http://gamedev.stackexchange.com/questions/29617/how-to-make-a-character-jump#29618