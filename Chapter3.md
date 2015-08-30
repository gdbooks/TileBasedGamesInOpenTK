#Map Format

###Two dimensional arrays
I'm going to talk about two dimensional arrays a LOT. You will quickly find that i don't mean proper C# 2D arrays. I don't EVER use those. 

When i talk about a 2D array i'm talking about a jagged array. An array of arrays, like so:

```
int[][] twoDim = new int[5];
for (int i = 0; i < twoDim.Length; ++i) {
    twoDim[i] = new int[5];
}
```

###Map data
Now that we know we're storing the map in a 2D array, the big question is what the data type of the array supposed to be?