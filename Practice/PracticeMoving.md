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