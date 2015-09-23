#Depth

We want to add some depth to our game. Right now, having a purley top-down game depth isn't all that important. But as we experiment with different perspectives (Including isometric) depth is about to become very improtant.

As you will see, there are two ways of handling depth, with a z buffer or with the painters algorithm. Neither method is better or worse, for the most part which one you use is your preference. 

I prefer to use z buffers myself, they requie a lot less code to write and is the method used when dealing with 3D graphics. 

Adding depth to the tile based framework we have set up is going to be the hardest thing we've done so far (In my opinion) as such we're going to have to break this task into three sections. Each section will have it's own project.

**First** we're going to seperate out collision rectangles from rendering rectangles. This will allow us to draw character and tile sprites outside of their respective bounding boxes. If a tile is 45 pixels tall (instead of 30), the character will be able to walk behind the top 15 pixels.

**Second** we're going to implement sprite sorting using z buffers. This will require an update to the ```GraphicsManager``` class. z buffering is pretty simple to set up and takes very little code, this should be a quick fun exercise.

**Third** we're goin to implement the painters algorithm. This is going to build on the first section (Not the z buffered section). Implementing the painters algorithm is a bit difficult becuase it requires a shift in paradime for our code.

###Moving forward

After having read all three of the sub sections for depth, which method you use moving forward (For things like isometric tiles) is going to be compleatly up to you. 

Ideally, once sorting is set up the code shouldn't need to be touched so the mthod you choose should not have too much of an impact moving forward.

I would urge you to use z buffering for the simple reason that it requires a LOT less code than the painters algorightm. In my experience less code means easyer debugging.