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