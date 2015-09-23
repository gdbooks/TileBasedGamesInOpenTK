#Scrolling with limits

The way we have scrolling implemented right now works fine, but there is one small thing that might (or might not) bother you about it. The edge of the scren! There are three ways games deal with this:

* Add obstacles to edge of screen so there is no black space
* Render black (or other solid color) in blank space (what we do now)
* Stop camera at edge of screen

In this section we are going to try to stop the camera at the edge of the screen. This means you will be able to walk from wall to wall, and see no background!

The implementation of this is decievingly simple, it's all done in ```Render``` method  **Game.cs** before anything is actually rendered! All we do is clamp the offsetPosition variable (camera space transformation) in four edge cases. When we calculate offet position:

* If the hero's world X position is less than 1/2 of the screen with
  * Clamp the xOFfset to 0
* If the hero's world Y position is les than 1/2 the screen height
  * Clamp the yOffset to 0
* If heros world X is greater than the world width minus 1/2 screen width
  * Clamp xOffset to world width minus screen width 
* If heros world Y is greater than the world width minus 1/2 screen height
  * Clamp yOffset to world height minus screen height

As you can see, all we're doing is not moving the offsetPosition after the hero passes a given point. Because offsetPosition is what transforms world space to camera space this essentially pegs the camera in spots so no corner of the map is off-screen. This is what my math looks like:

```cs
// If the hero is less than half the camera close to the left or top corner
if (hero.Position.X < 4 * tileSize) {
    offsetPosition.X = 0;
}
if (hero.Position.Y < 3 * tileSize) {
    offsetPosition.Y = 0;
}

// If the hero is less than half the camera close to the bottom or right corner
if (hero.Position.X > (currentMap[0].Length - 4) * tileSize) {
    offsetPosition.X = (currentMap[0].Length - 8) * tileSize;
}
if (hero.Position.Y > (currentMap.Length - 3) * tileSize) {
    offsetPosition.Y = (currentMap.Length - 6) * tileSize;
}
```

If you **run the game** there is going to be a visual error. When you are near the edge of the screen one or more of the tiles might be missing! Like this:

![ERROR](Images/visual_error.PNG)

This happens because of **Map.cs**, it limits which tiles are rendered and which are hidden. But it only lets 4 tiles to the left or right. We basically need to double the ammount of visible tiles, this is because we can now walk to the edge of the screen. So find the bit of code that hadnles how much is visible, this code:

```cs
// Find the visible corners of the screen in pixel position
int minX = (int)cameraCenter.X - 4 * 30 - 30;
int minY = (int)cameraCenter.Y - 3 * 30 - 30;
int maxX = (int)cameraCenter.X + 4 * 30 + 30;
int maxY = (int)cameraCenter.Y + 3 * 30 + 30;
```

And bump p the visible area to be the entire screen, like so:

```cs
// Find the visible corners of the screen in pixel position
int minX = (int)cameraCenter.X - 8 * 30 - 30;
int minY = (int)cameraCenter.Y - 6 * 30 - 30;
int maxX = (int)cameraCenter.X + 8 * 30 + 30;
int maxY = (int)cameraCenter.Y + 6 * 30 + 30;
```

**Run the game** now and you should be able to walk around flawlesly!