# Part 11 - Instructions

## Create a canvas

Here we will create a way of displaying simple text instructions to the participant. This will be the first time we have used Unity's [UI system](https://docs.unity3d.com/Manual/UISystem.html).

A UI can be placed directly on the screen - or placed in 3D space in our scene like a regular object. We want it in 3D space since we will later add VR support, which only works with this type of UI. First let's create a Canvas. A canvas is a rectangular GameObject that hold our UI elements.

[![create-canvas](/uxf-tutorial/images/create-canvas.png)](/uxf-tutorial/images/create-canvas.png)

I renamed it InstructionsCanvas. Crucially we will set the canvas's render mode to World (meaning it sits in 3D space). Then we move and resize it to be just below our start point, facing up (take note of the values in RectTransform).

[![instructions-canvas](/uxf-tutorial/images/instructions-canvas.png)](/uxf-tutorial/images/instructions-canvas.png)

## Add text

Now right click on the InstructionsCanvas and add a new Text GameObject - configure it to look as you want it.

[![ui-text](/uxf-tutorial/images/ui-text.png)](/uxf-tutorial/images/ui-text.png)

(I increased the quality by changing the Dynamic Pixels Per Unit in the Canvas Scaler component in the Canvas)

## Instructions controller script

Now we just need a simple script to update the instructions based on what we have set in our settings. We will make this run at session begin.

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UXF;

// important! UI requires adding this namespace
using UnityEngine.UI;

public class InstructionsController : MonoBehaviour
{   
    public Text instructions;

    void Awake()
    {
        instructions.text = ""; // clear instructions until we start the session
    }

    // assign to On Session Begin event
    public void Present(Session session)
    {
        instructions.text = session.settings.GetString("instruction");
    }
}
```
Make sure you reference the Text component you created in your `instructions` field! Also add the Present method On Session Begin.

[![present-instructions](/uxf-tutorial/images/present-instructions.png)](/uxf-tutorial/images/present-instructions.png)

## Test it

[![instructions-video](/uxf-tutorial/images/instructions-video.gif)](/uxf-tutorial/images/instructions-video.gif)
---

* [*Go to Part 12*](/uxf-tutorial/part-12)