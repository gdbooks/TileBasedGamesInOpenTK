#Visual Tweaks
Before adding depth, it would be nice if we could render some buildings which have depth.

TODO

Edit ```spriteSources``` in **Game.cs** to include some of the new tiles. I went ahead and measured these out for you:

```cs
protected Rectangle[] spriteSources = new Rectangle[] {
    /* 0*/new Rectangle(466,32,30,30),
    /* 1*/new Rectangle(466,1,30,30),
    /* 2*/new Rectangle(32,187,30,30),
    /* 3*/new Rectangle(32,280,30,40), // Top-left
    /* 4*/new Rectangle(63,280,30,40), // Top-middle
    /* 5*/new Rectangle(94,280,30,40), // Top-right
    /* 6*/new Rectangle(32,321,30,30), // Middle-left
    /* 7*/new Rectangle(63,321,30,30), // Middle-middle
    /* 8*/new Rectangle(94,321,30,30), // Middle-right
    /* 9*/new Rectangle(32,352,30,30),
    /*10*/new Rectangle(63,352,30,30)
};
```

Also, change the layout of room 1 to include the new building. Like so:

```cs
protected int[][] room1Layout = new int[][] {
    new int[] { 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 },
    new int[] { 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 3, 4, 5, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 6, 7, 8, 0, 1, 0, 0, 0, 0, 1, 0, 0, 1 },
    new int[] { 1, 9,10, 9, 0, 0, 0, 0, 0, 0, 1, 0, 0, 2 },
    new int[] { 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1 },
    new int[] { 1, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1 },
    new int[] { 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 }
};
```