#On Your Own
The Character class we created in **The Hero** section works ok for what we need, but it's very static. By this i mean there are only ever 4 images, and even then the one that is to be displayed needs to be hard coded. Let's fix this. In this section we're going to make the hero more dynamic.

###Think about it
How would you solve this problem? I'm going to present my solution below, but it's by no means the best or only way to tackle the problem. Before reading my answer think about how you would solve this problem. Maybe try to implement it! If you do come up with your own implementation, i want to see it! You can also use my implementation as a guide, and not follow it verbatim. Or you can do what  i do, this is a  learning experience!

###New Project
Create a new project, call it **HeroPractice**. We will be continuing from the **The Hero** section, make sure that the new project is up to date / par with that.

###Changing the hero
Any time you hear the word dynamic, you should probably start thinking about a [Dictionary](https://goo.gl/BCUxAT) (Hash table). Dictionary's allow us to get super customizable by having dynamic string keys. We're going to re-do the **Character** class with dictionary support.

First, remove the following variables from the character class:
* facingDownFrame1
* facingLeftFrame1
* facingRightFrame1
* displaySourceRect

Add a new dictionary, the key should be a string, the value should be a rectangle. I would call this dictionary **spriteSources**, but the name is up to you. Also, add a string. Again what you call the string is up to you, i call mine **currentSprite**.

The idea here is pretty simple, we will be able to add any number of sprites to the **Character** object, and reference them by strings thanks to our new dictionary. Only one of these sprites will display at a time, and it will be the one that has the same key as **currenSprite**.

We don't want these variables to be just randomly set, make sure they are private. Next, make the following helper metod in the **Character** class:

```cs
public void AddSprite(string name, Rectangle source) {
```

This method should add a new entry into the **spriteSources** dictionary. If **currentSprite** has not been set yet (If it's null or empty), set **currentSprite** to be the **name** argument. This way if at least one sprite is added it will be displayed by default no matter what.

Also add this helper method:

```cs
public void SetSprite(string name) {
```

**SetSprite** should check if the **name** argument exists as a key inside the **spriteSources** dictionary. If it does set the **currentSprite** variable equal to the **name** argument. If not, do nothing.

Last, update your **Render** function. It should display a sprite out of the **spriteSources** dictionary. IF the **currentSprite** string has not been set (If it's null or empty), early out and draw nothing (as the dictionary access would trigger an NRE).

###End result
The **Initialize** method of my **Progra.cs** looks like this:
```cs
public void Initialize() {
    TextureManager.Instance.UseNearestFiltering = true;
    map = GenerateMap(mapLayout, spriteSheets, spriteSources);
    hero = new Character(heroSheet, new Point(SpawnTile.X * 30, SpawnTile.Y * 30));

    hero.AddSprite("Down", new Rectangle(59, 1, 24, 30));
    hero.AddSprite("Up", new Rectangle(115, 3, 22, 28));
    hero.AddSprite("Left", new Rectangle(1, 1, 26, 30));
    hero.AddSprite("Right", new Rectangle(195, 1, 26, 30));
}
```

And i added this to **Update** so i could test:

```cs
public void Update(float dt) {
    InputManager i = InputManager.Instance; // Local reference to input manager
    // ^ I only use the reference to save on space below

    if (i.KeyDown(OpenTK.Input.Key.Left) || i.KeyDown(OpenTK.Input.Key.A)) {
        hero.SetSprite("Left");
    }
    if (i.KeyDown(OpenTK.Input.Key.Right) || i.KeyDown(OpenTK.Input.Key.D)) {
        hero.SetSprite("Right");
    }
    if (i.KeyDown(OpenTK.Input.Key.Up) || i.KeyDown(OpenTK.Input.Key.W)) {
        hero.SetSprite("Up");
    }
    if (i.KeyDown(OpenTK.Input.Key.Down) || i.KeyDown(OpenTK.Input.Key.S)) {
        hero.SetSprite("Down");
    }
}
```

Even tough link still doesn't move the arrow keys (and WASD) now make him turn in different directions :)