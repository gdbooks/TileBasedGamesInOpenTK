#Seperating Collision & Rendering
The first thing we need to do in order to add depth to our game is to seperate the collision rectangle of characters and tiles from their rendering. This will give us the unsorted illusion of depth. By the end of this chapter we want to achieve this effect (Note how links head is above the tile):

![DEPTH](Images/depth_effect.PNG)

###New Project
Let's make a new project, call it **DepthBase** and get this project up to par with the **LimitedScrolling** section of the writeup. We're going to work from here.

###Character
We're going to start by changing the scrolling behaviour of the character class. Starting with the character is nice, we get the effects of it applied to both the player character and enemy character! We're only going to add depth on the Y axis for now, it's rare to see depth on the X axis.

We are going to hard code the height of the character to some constant number (In this example 20px). Whenever the corners or bounding rectangle of the character is requested we're going to change the return to have 20 hard coded to it. Then we're going to change the render function, we're going to change it to render the sprite at Position.Y - 20 instead of Position.Y.

These changes will cause a 20px gap at the top of our characters. This part of the sprite will still be drawn, but it won't be taken into collision calculations. I made a graphic atempting to describe what we will be doing:

![DEPTH_EXPLAINED](Images/depth_explained.png)

###Debug Rendering
// TODO: Debug

###Applying Depth
First, we have to add a new member variable to the ```Character``` class. Make it a protected **float** and call it ```Height```. Set it to **-1** by default. If the new variable is less than 0 we will not apply the depth effect to the character, if it is greater than 0 we will apply a depth effect.

Next, change the constructor of character, it should now take in a third argument, a float called height. Set the member variable accordingly. Since we changed the constructor, we need to change where the constructor gets called. Find the following code in both ```PlayerCharacter``` and ```EnemyCharacter```:

```cs
: base(spritePath, pos) {
```

In ```PlayerCharacter``` pass **20** as the third argument, in ```EnemyCharacter``` pass **-1** as the third argument. By using constants as the last argument of the base constructor we can avoid changing the constructor of ```PlayerCharacter``` and ```EnemyCharacter```. Needless to say, the player will have a depth effect, while the enemy will not.

Change the **Rect** getter in the ```Character``` class. If the new ```Height``` variable is >= 0, set the returned rectangles height to the varaible.

Change the **Corners** getter. Right now it uses this bit of code to add height to corners ```SpriteSources[currentSprite][currentFrame].Height```, if the new ```Height``` variable is >= 0, use the ```Height``` variable, if it's less than 0 keep using what was already there.

Finally, change the **Render** function. After subtracting ```offsetPosition``` from ```renderPosition```, if the new ```Height``` variable is >= 0 find the height difference of the frame height and the hard coded height:

```cs
int difference = SpriteSources[currentSprite][currentFrame].Height - Height;
```

and subtract it from from ```renderPosition.Y```.