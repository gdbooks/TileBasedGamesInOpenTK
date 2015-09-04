#Starting Out


###Setting up a solution
Make a new visual studio solution, i called mine "TileBasedGames"

![STEP1](Images/step1.PNG)

Notice that this created a new solution called TileBasedGames as well as a new project called TileBasedGames. Go ahead and delete the project. Right click the project and select Remove. Click "OK" on the confirm dialog.

![STEP2](Images/step2.PNG)

If you look at the "TileBasedGames" folder there are two things in there, a solution named "TileBasedGames" and a folder named "TileBasedGames". The folder contains the project we just deleted. Go ahead and delete that folder.

![STEP3](Images/step3.PNG)

Before we do anything else, add a .gitignore file. Use the [Visual Studio](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore) template. 

Lets also add an Assets folder next to the solution. This is where we are going to keep all shared game assets. All in all your folder structre should look like this:

![STEP4](Images/step4.PNG)

###Setting up a project
Let's go trough setting up a project together. While you won't use this specific project, all subsequent tutorials will have the same setup. Ideally you want to have one solution with many projects inside it.

Let's add a new project called **Skeleton**. Right click on your solution and add a new project.

![STEP5](Images/step5.png)

I'm going to go ahead and call mine **Skeleton**

![STEP6](Images/step6.PNG)

Link in System.Drawing. Right click on references and select "Add Reference"

![STEP7](Images/step7.png)

Type *System.Drawing* in the search box, check the appropriate package

![STEP8](Images/step8.PNG)

Next we're going to link in two NuGet packages, [OpenTK](http://www.opentk.com/) and (https://naudio.codeplex.com/)

Don't forget to set your working directory (MAKE SURE TO EXPLAIN HOW THIS IS SHARED)
Make sure you have latest!
GABOR TODO: Submit nearest neighbor changelist

Go ahead and commit this, we now have a repo to work out of.


#TODO
Creating tiles: Upload final zip!