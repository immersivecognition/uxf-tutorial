# Part 2 - Mouse control

Before we get to VR, we will start with a mouse controlled version as this is easier to demonstrate in a tutorial. 

## The Camera

First we need to set up the camera such to give us a view of the task. The camera is a GameObject that we can put in any position and orientation. What the camera sees is what we see in the Game view. Crucially in VR we use a special camera setup which is controlled by our head position, which we will get to later.

We want this simple mouse version to be a 2D task initially - so we will move the camera (called `Main Camera` by default) up above the scene (position = `[0, 10, 0]`) and pointing down (rotation = `[90, 0, 0]`). Now, notice the Camera component in the inspector when we click on our Main Camera. We will also change the projection to Orthographic to make the task flat and truly 2D (no perspective warping). (Read more about projection [here](https://answers.unity.com/questions/1218955/comparing-orthographic-and-perspective-cameras.html).) I then changed the Size to `0.5` to essentially zoom in and show the entire table. Switch to the Game view to preview what the camera sees.

[![orthographic-camera](/uxf-tutorial/images/orthographic-camera.png)](/uxf-tutorial/images/orthographic-camera.png)

## Fixing shadows

Before we continue developing the task we will fix the shadows. You'll notice above we get some banding effects in the shadows and we can't see any shadows for the cursor and target.
