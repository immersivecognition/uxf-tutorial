# Unity Experiment Framework tutorial

This tutorial will walk you through building a simple virtual reality experiment using [UXF](https://github.com/immersivecognition/unity-experiment-framework). UXF is a package for Unity that allows users to more easily develop human behaviour experiments. Crucially, it will not be a general Unity tutorial (there are thousands already available!) but it will focus on how to make best use of UXF features. Here we will be creating a virtual reality visuomotor-rotation experiment. In these experiments, the user must reach towards an object under a rotated coordinate system until they adapt to the manipulation. Then, the visuomotor-rotation is removed, and the user must de-adapt to the manipulation.

The tutorial will begin by creating a simple keyboard/mouse version of the experiment. Then we will introduce UXF and begin collecting data and manipulating the task. Finally, we will make a few changes to make the project compatible with VR headsets. Here's a video of the final result:

<p align="center">
    <a href="http://immersivecognition.github.io/uxf-tutorial/videos/pretty.mp4">
        <img src="http://immersivecognition.github.io/uxf-tutorial/videos/pretty.png" width="300"/>
    </a>
</p>

If you want you can download the final project at [github.com/immersivecognition/uxf-tutorial-project](http://github.com/immersivecognition/uxf-tutorial-project). See Appendix 1 below for downloading or viewing the files at each part of the tutorial.

Note: This tutorial was written in 2019 and UXF has recieved updates since then, so things may look a little different but almost everything should work the same. If you encounter any problems [create an issue](https://github.com/immersivecognition/unity-experiment-framework/issues).

* [Part 0 - Introduction to Unity & UXF](http://immersivecognition.github.io/uxf-tutorial/part-0)
* [Part 1 - Setting up the scene](http://immersivecognition.github.io/uxf-tutorial/part-1)
* [Part 2 - Mouse control](http://immersivecognition.github.io/uxf-tutorial/part-2)
* [Part 3 - Creating a start point](http://immersivecognition.github.io/uxf-tutorial/part-3)
* [Part 4 - Reaching for a target](http://immersivecognition.github.io/uxf-tutorial/part-4)
* [Part 5 - Implementing UXF](http://immersivecognition.github.io/uxf-tutorial/part-5)
* [Part 6 - Adding a miss outcome](http://immersivecognition.github.io/uxf-tutorial/part-6)
* [Part 7 - Adding a timeout outcome](http://immersivecognition.github.io/uxf-tutorial/part-7)
* [Part 8 - Visuomotor-rotation](http://immersivecognition.github.io/uxf-tutorial/part-8)
* [Part 9 - Error clamp](http://immersivecognition.github.io/uxf-tutorial/part-9)
* [Part 10 - More settings](http://immersivecognition.github.io/uxf-tutorial/part-10)
* [Part 11 - Instructions](http://immersivecognition.github.io/uxf-tutorial/part-11)
* [Part 12 - Adding a VR SDK](http://immersivecognition.github.io/uxf-tutorial/part-12)
* [Part 13 - VR controls](http://immersivecognition.github.io/uxf-tutorial/part-13)
* [Part 14 - Table height adjustment](http://immersivecognition.github.io/uxf-tutorial/part-14)
* [Part 15 - Making it look pretty](http://immersivecognition.github.io/uxf-tutorial/part-15)
* [Part 16 - Minor experiment additions](http://immersivecognition.github.io/uxf-tutorial/part-16)

---

* [Appendix 1 - Using GitHub to download the project files for this tutorial](http://immersivecognition.github.io/uxf-tutorial/appendix-1)
* [Appendix 2 - Links to resources for your projects (Models/Sounds/Textures etc)](https://github.com/immersivecognition/unity-experiment-framework/wiki/Useful-asset-links)
* [Appendix 3 - Links to useful Unity tutorials](https://github.com/immersivecognition/unity-experiment-framework/wiki/Unity-tutorial-links)
* [Appendix 4 - Useful scripts/snippets for your projects](https://github.com/immersivecognition/unity-experiment-framework/wiki/Useful-code-snippets)
* [Appendix 5 - Example R script for processing UXF data](https://github.com/immersivecognition/unity-experiment-framework/wiki/Example-R-script-for-processing-UXF-data)

---

Initially written by [Jack Brookes](https://twitter.com/jackbrookes) in 2019.
