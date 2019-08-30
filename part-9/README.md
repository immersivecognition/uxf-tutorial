# Part 9 - Error clamp

We want to add a few more conditions. Some work has been done where the error is always clamped at a certain angle no matter where the participant moves. We can achieve this with some small changes to our code. Additionally, we want the option to remove on-line feedback, and hiding position of the cursor all together. Read more about the clamped error work in [Morehead, et al. 2017](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5505262/).

## Adding settings

To accommodate these changes, we will add two new settings to our json file.

```json
{
    "vmr_angle": 20,
    "clamped_error": true,
    "online_feedback": false
}
```

We can change these values at any time to manipulate the experiment.

Now we will use these settings to modify our scene. For the clamped error, we just need some changes to the VMR script, that clamps the error to a static amount rather than the cursor position. (This will need to change if we ever plan on moving the target.) For the online feedback, we can just enable/disable the MeshRenderer as needed at the start of the trial. Note here I used GetComponent in Awake() to get the reference to the mesh renderer component. This is just one of the ways of getting references to components in Unity - I prefer to do this when the component is attached to the same object I am working with as it leaves less room for error in forgetting to assign the reference in the inspector. 

I also added an if statement that means it will always show veridical feedback when outside of a trial. 

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UXF;

public class VisuoMotorRotation : MonoBehaviour
{
    public Session session;
    public Transform hiddenCursor;
    public Transform startPoint;
    
    MeshRenderer cursorMR;
    float vmrAngle = 0f;
    bool clamped = false;

    void Awake()
    {
        // get the mesh renderer attached to this component
        // could also be done with public variable
        cursorMR = GetComponent<MeshRenderer>();
    }

    void Update()
    {
        // we don't want to have any intervention when moving back to the start point
        // so if we aren't in a trial let's just show the cursor normally
        if (!session.hasInitialised | !session.InTrial)
        {
            transform.position = hiddenCursor.position;
            return; // exit the function early
        }

        if (clamped)
        {
            // calculate distance from center
            float dist = (hiddenCursor.position - startPoint.position).magnitude;
            // move towards target with calculated distance (i.e. with 0 error)
            transform.position = startPoint.position + Vector3.forward * dist;
        }
        else
        {
            // update the position of this object
            transform.position = hiddenCursor.position;
        }

        transform.RotateAround(startPoint.position, Vector3.up, vmrAngle);
    }

    // assign this function to run On Trial Begin
    public void UpdateSettings(Trial trial)
    {
        // use settings.Get*() to access settings (independent variables)
        vmrAngle = session.CurrentTrial.settings.GetFloat("vmr_angle");
        clamped = session.CurrentTrial.settings.GetBool("clamped_error");

        // enable/disable cursor mesh renderer as needed
        cursorMR.enabled = session.CurrentTrial.settings.GetBool("online_feedback");
    }
}
```

## Test it

Here's it with `"error_clamp": true, "online_feedback": false`:

[![vmr-clamp-novision](/uxf-tutorial/images/vmr-clamp-novision.gif)](/uxf-tutorial/images/vmr-clamp-novision.gif)

Here's it with `"error_clamp": true, "online_feedback": true`:

[![vmr-clamp-vision](/uxf-tutorial/images/vmr-clamp-vision.gif)](/uxf-tutorial/images/vmr-clamp-vision.gif)

Hopefully you can now appreciate the settings system. We can specify, even on a trial level, if we want any of these conditions to be on or off.

## Feedback fixes

In part 6 we used the On Trial Begin event to hide the cursor during the trial - make sure this event is removed since it will conflict with this script. I also added an event to disable the cursor at the end of the trial - this means the cursor won't be visible at the same time that the cursor copy (the feedback) is.

[![events-part-9](/uxf-tutorial/images/events-part-9.png)](/uxf-tutorial/images/events-part-9.png)

---

* [*Go to Part 10*](/uxf-tutorial/part-10)