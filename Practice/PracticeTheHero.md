#On Your Own
The Character class we created in **The Hero** section works ok for what we need, but it's very static. By this i mean there are only ever 4 images, and even then the one that is to be displayed needs to be hard coded. Let's fix this. In this section we're going to make the hero more dynamic.

###Think about it
How would you solve this problem? I'm going to present my solution below, but it's by no means the best or only way to tackle the problem. Before reading my answer think about how you would solve this problem. Maybe try to implement it! If you do come up with your own implementation, i want to see it! You can also use my implementation as a guide, and not follow it verbatim. Or you can do what  i do, this is a  learning experience!

###Changing the hero
Any time you hear the word dynamic, you should probably start thinking about a [Dictionary](https://goo.gl/BCUxAT) (Hash table). Dictionary's allow us to get super customizable by having dynamic string keys. We're going to re-do the **Character** class with dictionary support.

First, remove the following variables from the character class:
* facingDownFrame1
* facingLeftFrame1
* facingRightFrame1
* displaySourceRect

Add a new dictionary, the key should be a string, the value should be a rectangle. I would call this dictionary **spriteSources**, but the name is up to you. 