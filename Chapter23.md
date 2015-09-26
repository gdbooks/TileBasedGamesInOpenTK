#Isometric View

Isometric worlds are great, they can add a lot of depth and visual appeal to a simple tile based game. Just look at Diablo 2, or Starcraft 1! 

There are some [great resources](http://gamedevelopment.tutsplus.com/tutorials/creating-isometric-worlds-a-primer-for-game-developers--gamedev-6511) online that really get into the nitty gritty of how isometric views work. They are worth a read.

Movement in an iso world doesn't have to be smooth. I think [This](http://www.gotoandplay.it/_articles/2007/03/isometric.php) demo fels awesome, and the character is snapped to a grid the whole time! Of course we are going to have smooth movement in our version.

Depth buffering is very important for an isometric scene. The draw order really, really matters! A Lot!

###New Project
Let's make a new project, call it **Isometric** and get this project up to par with the **DepthBuffer** section of the writeup. We're going to work from here.

###Tiles
So, how do we go from square tiles to isometric tiles? Simple, rotate by 45 degrees and scale height by 1/2 (0.5f). Like so:

![TUT16A](Images/tut16a.gif)

Take note that even tough the height of the tile changes, the width is untouched. We _could_ do this in code, but we _should not_. Instead rely on artists to provide art assets! 

Speaking of art assets, i made a new tile sheet, we are going to use this sheet for isometric tutorials. Save it to your "Assets" folder, make sure to call it **isometric.png**.

![ISO](Images/isometric.png)

The positioning of the tiles on the source image is very, very important!