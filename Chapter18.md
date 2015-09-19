#Getting Items
We can walk around, switch rooms and even kill enemies. It's almost like we have a game starting to shape up! In the last section we added bullets, now let's add the opposite. Items! Our items are going to be simple. The player will have a running score, collecting an item will add some number (item dependant) to that score.

Why do i call items the opposite of bullets? Because they don't interesect the enemies, they intersect the player! You will find much of the code is going to be similar to how bullets work. I still think an item is a non moving anti-bullet.

###New Project
Let's make a new project, call it **GettingItems** and get this project up to par with the **Shooting** section of the writeup. We're going to work from here.

###Getting Started
Let's get started by updating the **HouseTiles.png** texture atlas with a new version that i've added some items to:

![ATLAS](Images/HouseTiles_Items.png)

###Item class
Let's go ahead an create a new class for items. If this was a real game,we might create a class called Item, then a subclass that into various other subclasses like powerup, weapon, secret, and tose further into specific subclasses like sword, potion, map, etc... But this is just a simple demonstration of how to pick items up, so we will only have a simple Item class.

This class will have 4 member variables:
* int **Sprite**
  * Which sprite sheet id is the sprite on 
* Rectangle **Source**
  * What part of the sprite sheet to draw 
* int **Value**
  * How many points the item is worth
* Point **Position**
  * Where on screen to draw the sprite of the item

The constructor will take 1 argument for each of these, and simply set the appropriate values. We will have a **Render** function that just draws the subsprite to screen. Because this class holds on to a texture reference we must add a **Destroy** function, in which the textur emanager unloads textures. For good measure we also implement a Rect getter, the X and Y are provided by Position while the width and height are provided by Source. Try to implement the class yourself first, here is my implementation:

```cs
class Item {
    protected int Sprite;
    protected Rectangle Source;
    public int Value { get; private set; }
    public Point Position;
    
    public Rectangle Rect {
        get {
            return new Rectangle(Position.X, Position.Y, Source.Width, Source.Height);
        }
    }
    
    public Item(string spriteSheet, Rectangle sourceRect, int value, Point position) {
        Sprite = TextureManager.Instance.LoadTexture(spriteSheet);
        Source = sourceRect;
        Value = value;
        Position = position;
    }

    public void Render() {
        TextureManager.Instance.Draw(Sprite, Position, 1.0f, Source);
    }

    public void Destroy() {
        TextureManager.Instance.UnloadTexture(Sprite);
    }
}
```

###Refactoring Map
Just like we have unique enemies in each room, we have unique items in each room. Therefore it makes sense to make the ```Map``` class be the owner of the items list. We need a list because we don't know how many items we're going to have in advance, but more importantly because we're going to remove items as they are collected.

Make a new **List** of type **Item** in **Map.cs**, i call mine ```items```. Make sure to initialize this list.

In **Render**, loop trough all of the items and call Render on each one.

In **Destroy**, loop trough all of the items and call Destroy on each one.

Inside the **Update** function, AFTER the loop that goes trough all the enemies, add a loop that loops trough all the items. We're going to potentially delete stuff from the list, so loop appropriately. Loop trough every item, and check for collision against the hero. If a collision occurs, remove the item. (At this point we just want to remove it, we're not keeping track of score just yet.

When you remove an item from the list it will no longer be referencable. Therefore it will no longer be auto unloaded by Destroy. Make sure to call the items ```Destroy`` function before reoving it from the list.

Now, when we created enemies, we added a very helpful **AddEnemy** function. Do the same for items. This function should of course take the same arguments as an item constructor. Call this new function **AddItem**.

###Refactoring Game
In **Game.cs** find where we added the enemies to the rooms. Use the following code to add items:

```cs
room1.AddItem(spriteSheets, new Rectangle(350, 255, 16, 16), 10, new Point(3 * 30 + 7, 2 * 30 + 7));
room1.AddItem(spriteSheets, new Rectangle(381, 256, 13, 15), 20, new Point(5 * 30 + 7, 4 * 30 + 7));
room2.AddItem(spriteSheets, new Rectangle(412, 256, 16, 15), 30, new Point(4 * 30 + 7, 2 * 30 + 7));
```

**Run the game** you should have items in the roomw. You should also be able to walk around and pick up said items! Here is what the first map looks like:

![MAP](Images/fmap_item.PNG)

Lets add the ability to view a score that increases when items are piced up. Add a public integer to the ```Game``` class, call it **Score**. Add the follwoing at the end of the **Render** function (outside any if statements) to display the new score:

```cs
GraphicsManager.Instance.DrawRect(new Rectangle(150, 0, 90, 20), Color.CadetBlue);
GraphicsManager.Instance.DrawString("Score:" + Score, new PointF(155, 3), Color.Black);
GraphicsManager.Instance.DrawString("Score:" + Score, new PointF(154, 2), Color.White);
```

###Adding up the score
All that's left is to add up the score. Go back into **Map.cs**, find where we Destory and Remove items on collision with the player. In this same block add one to the ```Score``` variable of game. You can access the Score variable using the games singleton instance.

**Run the game** you should now be able to collect items for points! That's all there is to it!