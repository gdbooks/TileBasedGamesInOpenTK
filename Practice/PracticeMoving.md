#On Your Own
We now have a way to make the character walk on screen! Huzza! One small problem we're having here is that the hero is just kind of sliding around on the screen! While animations are not required to make a decent game demo, they help. A lot. So in this section we're going to add animations to our Hero!

###New Project
Let's make a new project, call it **MovingPractice** and get this project up to par with the **KeysToMove** section of the writeup.

###Getting started
We will have to change code in the ```Character``` class, and add new code to the ```PlayerCharacter``` class to support animations. Keep in mind, i'm only going to give you guidance to how i would implement animation. You can follow my lead, or come up with your own way of doing things. There is no right or wrong way. So long as it looks right and runs fast it's good!

The basic concept is simple, when we add a sprite i don't want to just add a single rectangle. I want to add a variable number of rectangles to the sprite name. I also want to add an animation flag. If animation is false, show only the current (first?) rectangle of the sprite name. If animation is true, flip trough all of the rectangles in the sprite name at a set framerate.

###Refactoring Character
First things first, let's refactor the ```Character``` class. In **Character.cs** we have the following function:

```cs
public void AddSprite(string name,Rectangle source) {
```

I want to change this function to support multiple rectangles. Instead of taking a list, take a params array:

```cs
public void AddSprite(string name, params Rectangle[] source) {
```

This will allow the function to be called with a variable number of rectangles seperated by only commas. Of course this is going to break a few things. This bit of code is no longer valid:

```cs
private Dictionary<string, Rectangle> spriteSources = null;
```

That however is an easy fix, just change the value type to be a Rectangle array

```cs
private Dictionary<string, Rectangle[]> spriteSources = null;
```

Now the ```AddSprite``` method works again, but the ```Render``` method is broken!

There are a few ways to fix this. You could always render frame 0, but that's a bad idea! You could add an integer to the ```Character``` class and call it ```currentFrame```. This int would represent what frame to display. Adding an integer sounds like a good idea, it's certainly intuitive. But it does have a serious flaw: not every animation is going to have the same number of frames.

Because each animation can have a variable number of frames, we need to add house-keeping. Whenever a new animation is played, the currentFrame has to be reset to 0 to avoid going out of bounds. But then switching back to the old animation, you can't just resume where you left off. This may or may not be the behaviour you want. Is there an alternative?

Yes, yes there is. You could keep a parallel array. One who's key is a ```string``` and value is an ```int```. So it would know the walk animation is on frame 5, while the idle animation is on frame 2. In this case you would also have to add a new string, and call it currentAnimation. This would be used to index into the dictionary. Wow, thats a lot of house keeping! I'm already getting confused!

So, how should we handle this?!?!?! Well, experience tells me having an integer called ```currentFrame``` is the best approach. It's easy to read and requires the least amount of house keeping. It also makes the most sense. So i'm just going to add a new integer to the ```Character``` class:

```cs
namespace TheHero {
    class Character {
        public PointF Position { get; protected set; }
        public int Sprite { get; private set; }
        
        public Dictionary<string, Rectangle> spriteSource { get; private set; }
        public string currentSprite { get; private set; }
        protected int currentFrame = 0; // NEW
        // ^ protected means any children of this class can modify the variable
```

and let's not forget to fix the ```Render``` method:

```cs
public void Render() {
    TextureManager.Instance.Draw(Sprite, new Point((int)Position.X, (int)Position.Y), 1.0f, spriteSources[currentSprite][currentFrame]);
}
```

Running the game at this point, you should be able to walk around with link. From the players point of view nothing has changed! But from the programmers point of view, we have laid the groundwork for animating the character.

###Refactoring Player Character
At this point, whatever the ```currentFrame``` variable is set to will be displayed. All you need to do is add some more frames. I'll spare you having to look up the frame positions manually, here are the walking frames for link:

```cs
public PlayerCharacter(string spriteSheet, Point startPos) : base(spriteSheet, startPos) {
    // ^ The Constructor which take a string and a Point of the base class (Character)
    // is called using the : notation.

    AddSprite("Down", new Rectangle(59, 1, 24, 30), new Rectangle(87, 1, 24, 30));
    AddSprite("Up", new Rectangle(115, 3, 22, 28), new Rectangle(141, 3, 22, 28));
    AddSprite("Left", new Rectangle(1, 1, 26, 30), new Rectangle(31, 1, 26, 31));
    AddSprite("Right", new Rectangle(195, 1, 26, 30), new Rectangle(167, 1, 26, 29));
}
```

Animation is as simple as setting ```currentFrame```. I'll let you figure out how that should be done. I'll include some hints as to how i did it below.

###Animation, my method
I added three variables to the ```PlayerCharacter``` class to support animation:

* bool animating = false
* float animFPS = 1.0f / 9.0f;
* float timeTimer = 0.0f

The **animating** boolean is self explanatory. If it's set to true the animation is playing. If it's set to false then no animation plays. This is how i make link no animate if no button is pressed. That boolean is only true while one of the movement buttons is held down.

The **animFPS** float isn't as straight forward. Link animates at 9 frames / second. This means in one second you see 9 frames of animation. So, why _1.0f / 9.0f_? Simple, we are going to have a counter. That counter is going to count upwards from 0. If we want to display 9 frames every second, that means we want to show ONE new frame every 0.11 seconds. _1.0f / 9.0f_ equals 0.11.

The **timeTimer** float is pretty simple. This is the counter variable that counts upwards. If **timeTimer** is greater than **animFPS** then we will flip one frame!

So how is this all implemented? In each of the if statements that checks if a key is down (and moves the position and sets the sprite if it is) i also set animating to true. After the if blocks that handle input i check if animating is true. If it is, i add deltaTime to animTimer. 

Still inside the ```if(animating) {``` block, i put anoter nested if. If the animTimer is greater than animFPS, i add one to the currentFrame. I also subtract animFPS from animTimer, this gives a smooth animation. Time for yet another nested if, if the currentFrame is greater than the Length of the current spriteSources array then i reset currentFrame to 0 (to keep from going out of bounds). 

Finally outside of any if statements, after the ```if(animating) {``` block i set animating back to false. It's the default state of the animating variable. This way, if no key is pressed no animation happens.

###Check in
Skype or text me if you have any questions. Turning the ```PlayerCharacter``` class into a flip book is not easy, but i think you can do it!

If you finish this section skype or text me. I want to look over your work before you move on to the next section!