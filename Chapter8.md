#The Hero
Now that we can generate and render maps lets make our game a bit more interesting. We're going to add a hero! 

###New Project
Create a new project, call it **TheHero**. Make the standard window appear. Don't forget to set the working directory.

I'm going to be continuing from the **Game** class we built in the **Creating Tiles** chapter. You make a copy of that game file (**Copy** don't add a reference, we're going to be modifying this) and get to a point that the game can display this map:

![SAMPLE2](Images/sample2.PNG)

###Getting Started
I'm going to get started by giving the hero a spawn position. (Some code below is duplicated from the last example, just to give you an idea of where we are at)

```cs
namespace TheHero {
    class Game {
        protected Point SpawnTile = new Point(2, 1); // NEW

        protected Tile[][] map = null;
        protected int[][] mapLayout = new int[][] {
            new int[] { 1, 1, 1, 1, 1, 1, 1, 1 },
```

This is going to be the (zero based) tile that our hero spawns at. So, our hero should spawn in the upper left, right above that obstacle.

Now that we have a spawn position, how should we represent the Hero? We could make a bunch of variables in the ```Game``` class like _heroPosition_, _heroHealth_ and _heroSprite_ but as you can imagine this will get out of hand soon. A better approach is to make a ```Hero``` object, this way when we need to add new information to our hero we just add it to said object.

Let's make a new file, call it **Hero.cs** and start implementing our hero.

```cs
class Hero {
    public Point Position { get; private set; }
    public int Sprite { get; private set; }

    protected Rectangle facingUpFrame1 = new Rectangle();
    protected Rectangle facingDownFrame1 = new Rectangle();
    protected Rectangle facingLeftFrame1 = new Rectangle();
    protected Rectangle facingRightFrame1 = new Rectangle();

    protected Rectangle displaySourceRect = default(Rectangle);

    public Hero (string spriteSheet, Point startPos) {
        Position = startPos;
        Sprite = TextureManager.Instance.LoadTexture(spriteSheet);
        displaySourceRect = facingDownFrame1;
    }

    public void Destroy() {
        TextureManager.Instance.UnloadTexture(Sprite);
    }

    public void Render() {
        TextureManager.Instance.Draw(Sprite, Position, 1.0f, displaySourceRect);
    }
}
```