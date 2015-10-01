#More Tiles
In all of our examples we have loaded tiles by hand. We did breafly look at how to use tools to generate some understandable data, but it only goes so far. Let's leave off with an easy to parse map format. In this chapter we're going to load maps.

###Define a format

I want you to come up with a map format! Each map will contain ONLY 1 room. Door tiles can go to other maps, how you represent those maps is up to you (By number, by string, or by whatever).

Here is what a format _might_ look like:

```
1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
1 0 3 3 3 0 0 0 0 0 0 0 0 0 1
1 0 3 3 3 0 3 3 3 3 3 3 3 0 1
1 0 0 0 0 0 3 3 0 0 0 0 3 0 1
1 0 3 3 3 0 3 3 3 0 3 3 3 0 1
1 0 3 3 3 0 0 0 0 0 0 0 0 0 2
1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

T Assets/TileSheet.png
R 0 23 87 98
R 1 23 87 98
R 2 23 87 98
R 3 23 87 98
W 0 2
D 2 Assets/NextRoom.txt
S 2 1
```

I assume NO HEIGHT in my map format. In this hypothetical format, blank lines are skipped. Any line that begins with a number records a row. Any line that starts with a letter starts some meta-data. Here is my meta data format:

* **T** [Path]
  * Specifies where the texture for this sheet is loaded
* **R** [index] [x] [y] [w] [h]
  * Specifies the source rectangle  
  * index - which tile are we specifying the source of (0, 1, 2...)
  * x, y, w, h - rectangle size
* **W** [index list]
  * Specifies which tiles are walkable
  * Takes a list of integers, any integer in the list is walkable
* **D** [Index] [Path]
  *  A door tile!
  *  The first argument is the index. In this map, tile 2 is a door
  *  The second argument is another map file! Load this map file when the door is entered
* **S** [x] [y]
  * Specifies the starting tile for this map.
  * When the map first loads, place the hero here.

There is no requirement for this. You can include as many or as few items in your map as you want (You don't _have_ to have doors unless you want to). Feel free to use my proposed format as a jumping off point.

###Loading the map

Right now we are hard coding the map layout into **Game.cs**, this needs to change. Change the constructor in **Map.cs** to take a single string for it's argument. This string should be the path of the map file to load. All map info needs to be in this file!

You should craft a map file by hand, then modify the constructor of ```Map``` and try loading that file. 