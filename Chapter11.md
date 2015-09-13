#Open The Door
In the last chapter we explored how to add collision to the walls of our room. This is aweome! But Link is feeling claustrophobic! He is an epic hero, confined to a tiny room... In this chapter we are going to double the size of Link's world by adding a second room to it! Here is the layout of what this is going to look like:

![ROOMS](Images/multiroom.png)

Of course only one of these rooms is going to be visible at a time, link's going to have to go trough the green door to visit the adjacent room. 

The nice thing here, none of the code for this is going to have to go inside the ```PlayerCharacter``` class, as none of this is specific to the player. Instead we're going to be writing most of our code inside of **Game.cs**

###New Project
Let's make a new project, call it **OpenTheDoor** and get this project up to par with the **HitTheWall** section of the writeup. We're going to work from here.

###The door tile
First thing is first, let's add our door tile that will lead into the second room.

#Cleaning Up
Follow along to the code in the **Cleaning** Up subsection inside of this (OpenTheDoor) project. The **Cleaning Up** section just gives us an easy way to store room's as objects, instead of cluttering up the **Game.cs** file.