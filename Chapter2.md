#Why Tiles

Before delving into how to make tile based games, why do we use tiles to being with? Are they easier to make than their free form counterparts? Do they run faster? Is C# even a good candidate to make tile based games?

Tile based games came about back in the olden days of computing. When processor speeds where counted in megahertz, not gigahertz. When memory was kilobytes not megabytes. The limitation of memory and processing power kept programmers from being able to display a 512 x 512 sprite while keeping framerates reasonable. 

Without being able to display large textures how can one draw a nice background? Slice the picture up into tiles! You don't need to waste memory on areas of the background that look the same.

![SCREEN1](Images/tut00_1.gif)