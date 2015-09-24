
###Existing Depth Buffer
The graphics frameworks is already using buffers under the hood. When you call ```GraphicsManager.Instance.ClearScreen(System.Drawing.Color.CadetBlue);``` in **Program.cs** the depth buffer is cleared to 0 (Pure white). 

Every sprite you draw renders as a quad. Each time you call ```GraphicsManager.Instance.Draw[String/Square/Line]``` or ```TextureManager.Instance.Draw```  0.0005f is added to the depth, and the quad is rendered into the depth buffer with the new depth. The closer we get to 1, the darker the depth buffer.

The depth test passes, and the colors of the quad are drawn to screen.

Right now the depth test will always pass! This is becuase each time a new quad is rendered it is rendered with a z valu 0.0005f higher than the last one. That is, nothing in the depth buffer will have a higher value (be darker) than the current thing being drawn.

Here is a screenshot of what our z buffer looks like. Notice how the tiles get darker not only going from top to bottom, but also left to right.

![BUFFERS](Images/buffers.PNG)

Just for fun, if you want to see the z-buffer for your self, you can add this code to **Game.cs**

```cs
if (InputManager.Instance.KeyPressed(OpenTK.Input.Key.Y)) {
    Image depthBuffer = GraphicsManager.Instance.GetDepthBuffer();
    depthBuffer.Save("depth_buffer.png", System.Drawing.Imaging.ImageFormat.Png);
}
```

With this code in place, every time you press the **Y** button the depth buffer will be saved next to the Asets directory and solution file.

###Explicit Depth Buffer

The task at hand is to figure out how to sort tiles, enemies, characters and items; then to apply Z values directly. Right now we don't have controll of the z buffer directly, which is why i updated the Graphics manager with the following functions

```cs
public void SetDepthRange(float near, float far)
public void IncreaseDepth()
public void IncreaseDepth(float step)
public void SetDepth(float depth)
```

* **SetDepthRange** - Unlike the X and Y axis, our depth is not infinite! We need to define a range for it
  * Takes a near value and a far value. Configures depth buffer to use said values
* **IncreaseDepth** - This is what GraphicsManager has been using internally this whole time.
  * Increases current depth by 0.0005f 
* **IncreaseDepth(float)** - The stock increase might not be enough. We may want to increase depth in smaller steps
  * Increases depth by the value of the argument provided 
* **SetDepth** - Increase depth can only go forward, we may need to set depth to 3 after it was 5
  * Allows us to hard code the depth value for the next draw call

Out of all these functions, we only need SetDepth. Before rendering a tile or the character or anything we're going to explicitly set it's z-depth by calling **SetDepth**. This way we can be in total controll! The only question is, how to we figure out the depth of a thing (tile, character, item, etc...)??? Oh, and what resolution depth buffer do we need?

The image of the depth buffer we saw in the last section on this page actually gives us a pretty decent strategy for dealing with depth. The further down something is the higher it's depth. The further right something is, the higher it's depth! Here is a pretty good demonstration of what i mean:

