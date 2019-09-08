# Part 15 - Making it look pretty

Here I'll just show some ideas & tips on how you can improve the graphics of the task to make it more widely appealing. Unity obviously has amazing graphics capabilities far beyond my abilities, so here are just some easy wins to make things look good without much effort.

## Ideas

* Use custom fonts in the UI. Here are some free [public domain fonts](https://www.dafont.com/top.php?l[]=10).
* Use particle effects
* Use custom shaders (many can be found on the asset store)
* Use the new [Post-Processing stack](https://docs.unity3d.com/Packages/com.unity.postprocessing@2.1/manual/index.html). Bloom with materials that have a HDR colour create a Beat Saber effect. (Make sure HDR is enabled on the CameraRig Camera.)
* Write simple scripts to animate objects, such as making something slowly rotate.
* Use an `AnimationCurve` to [make any linearly interpolated movements look smoother](https://www.loekvandenouweland.com/content/using-AnimationCurve-and-Vector3.Lerp-to-animate-an-object-in-unity.html).
* [Bake the lighting](https://docs.unity3d.com/Manual/LightMode-Baked.html) of any static objects in your scene.


I used [this custom shader](https://halisavakis.com/my-take-on-shaders-parallax-effect-part-ii/) for a cool parallax effect.

Also [this wireframe shader](https://github.com/Chaser324/unity-wireframe) for the particles.

---

* [*Go to Part 15*](/uxf-tutorial/part-15)