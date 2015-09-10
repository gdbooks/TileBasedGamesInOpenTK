#Hit The Wall
Having a hero who can walk is fun, but it would be even more fun if the hero could hit the brick wall... With his face! Handling collisions with walls is not that hard. We've done this in **WinFormGames** before. Given this situation:

![COLLISION1](Images/col1.PNG)

We first want to find the rectangle for both link (outlined blue) and the obstacle (outlined green):

![COLLISION2](Images/col2.png)

Using these two rectangles we want to see if they intersect. To do this we look for an intersection rectangle (outlined in red):

![COLLISION3](Images/col3.png)
