#Open The Door
In the last chapter we explored how to add collision to the walls of our room. This is aweome! But Link is feeling claustrophobic! He is an epic hero, confined to a tiny room... In this chapter we are going to double the size of Link's world by adding a second room to it! Here is the layout of what this is going to look like:

![ROOMS](Images/multiroom.png)

Of course only one of these rooms is going to be visible at a time, link's going to have to go trough the green door to visit the adjacent room. 

The nice thing here, none of the code for this is going to have to go inside the ```PlayerCharacter``` class, as none of this is specific to the player. Instead we're going to be writing most of our code inside of **Game.cs**

###New Project
Let's make a new project, call it **OpenTheDoor** and get this project up to par with the **HitTheWall** section of the writeup. We're going to work from here.

###The door tile
First thing is first, let's add our door tile that will lead into the second room. We're going to be working in **Game.cs**. Right now we define the map layout in the ```mapLayout``` variable where tile 1 is a wall and tile 0 is walkable. Let's add tile 2, which will be the door. 

* In the ```mapLayout``` varibale, change the appropriate tile to be a door
  * Look at the above screnshot, it's the tile in the lower right
* Add ```new Rectangle(32, 187, 30, 30)``` to the ```spriteSources``` array
* Make tile 2 walkable. So tiles 0 and 2 should both be walkable.

Be careful when walking around! While you should be able to walk a little bit into the door, if you walk too far into the new door tile the game will crash. This is expected. After the above changes, running the game should look like this:

![DOOR1](Images/door1.PNG)

###The second room
Sweet we now have a door (well, visually at least), but it doesn't lead anywhere! We're going to add a second map. Still in **Game.cs**, refactor the name of the ```map``` variable to be ```room1```. To do this, right click on ```map``` and select **Rename**

![RENAME](Images/rename_map.png)

This will cause the ```map``` variable to highlight in some strange green color, just start typing and the name of the variable will change. When you are done, hit enter and all instances of ```map``` will be autmatically renamed for you.

![GREEN](Images/green_map.PNG)

Now rename the ```mapLayout``` variable to be named ```room1Layout```. 

**Run The Game** at this point it's important for you to run the game and make sure it still works correctly!

Next, let's add the definition of ```room2``` and ```room2Layout```. Seeing how this is mostly a copy / paste job of ```room1``` with minor alterations, use this clode:

```
protected Tile[][] room2 = null;

protected int[][] room2Layout = new int[][] {
    new int[] { 1, 1, 1, 1, 1, 1, 1, 1 },
    new int[] { 2, 0, 0, 0, 1, 0, 0, 1 },
    new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 0, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 0, 0, 0, 1, 0, 0, 1 },
    new int[] { 1, 1, 1, 1, 1, 1, 1, 1 }
};
```

Inside of the **Initialize** function, make sure that ```room2``` is created!

###Walking trough the door

###Going back

#Cleaning Up
Follow along to the code in the **Cleaning** Up subsection inside of this (OpenTheDoor) project. The **Cleaning Up** section just gives us an easy way to store room's as objects, instead of cluttering up the **Game.cs** file.