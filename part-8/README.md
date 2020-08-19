# Part 8 - Visuomotor-rotation

## Multiple cursors

Finally we are now ready to implement the experiment manipulation. Here will will create a visuomotor rotation (VMR), which rotates the position (as seen in the headset) of the cursor a fixed angle about an origin. This will just require either some maths, or use of Unity's built in vector and transform functions.

To achieve this practically we will create a "Hidden Cursor", which is invisibly controlled by our mouse/hand, and then the standard Cursor follows it with a VMR.

I copy/pasted the Cursor to duplicate it and renamed it.

[![hierarchy-part-8](/uxf-tutorial/images/hierarchy-part-8.png)](/uxf-tutorial/images/hierarchy-part-8.png)

We need to remove the MouseController script from the actual cursor - because its position will be calculated by other means.

Then for the hidden cursor, I removed the MeshRenderer to make it invisible, and changed the value in the Object Name field in the UXF Position Rotation Tracker component.

[![hidden-cursor](/uxf-tutorial/images/hidden-cursor.png)](/uxf-tutorial/images/hidden-cursor.png)

This will make sure the position of this object is tracked by UXF but named correctly. We need to make sure both these objects are referenced in the TrackedObjects field in the UXF Session component.

[![tracked-object-update](/uxf-tutorial/images/tracked-object-update.png)](/uxf-tutorial/images/tracked-object-update.png)


## Visuomotor rotation script

Now we will write a script for the VMR and attach it to our visual cursor. It should, on the Update loop:

* Take the position of the real cursor.
* Rotate it around the start point (about the up axis) by an angle taken from a variable.

Then, we want a function which will run at the start of each trial to update our VMR angle amount - in case we want to change it trial to trial.

Try to write yourself before looking below.

<div style="border:1px solid #ff9a00;background-color:rgba(255, 154, 0, 0.3);padding:2em;">
NOTE: As of an update to UXF, you no longer need create a reference to the UXF session. You can simple write `Session.instance` to access the current session instance (e.g. `Session.instance.CurrentTrial.End()`. The old way still works too.
</div>

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

    float vmrAngle = 0f;

    void Update()
    {
        // update the position of this object
        transform.position = hiddenCursor.position;
        transform.RotateAround(startPoint.position, Vector3.up, vmrAngle);
    }

    // assign this function to run On Trial Begin
    public void UpdateSettings(Trial trial)
    {
        // use settings.Get*() to access settings (independent variables)
        vmrAngle = session.CurrentTrial.settings.GetFloat("vmr_angle");
    }
}
```

Notice here we are using [UXF settings](https://github.com/immersivecognition/unity-experiment-framework/wiki/Settings-system). These are independent variables we have control over. We can assign them at any point programmatically or using our experiment settings profile. We will do the latter - lets modify our settings file for this experiment (`Assets/StreamingAssets/reaching.json`) and add in this setting `vmr_angle`. This means all we have to do is modify this `.json` file (even after building) and we can change the VMR angle without modifying any of the actual code.

You can read about proper how to write `.json` online e.g. [here](https://www.tutorialspoint.com/json/json_syntax.htm).

```json
{
    "vmr_angle": 20
}
```

Now we can attach the `VisuoMotorRotation` script to our Visual Cursor and assign all the inspector references. Don't forget to assign the UpdateSettings script to run On Trial Begin

[![vmr](/uxf-tutorial/images/vmr.png)](/uxf-tutorial/images/vmr.png)

## Script execution order

One potential issue with this type of script is that it is undefined if this script executes before or after the MouseController script. If it executes after, that's fine as it will have the most recent hidden cursor position to apply the VMR to. If it executes before, it will essentially be applying the VMR to last frame's hidden cursor position. You can read about Unity's script execution order [here](https://docs.unity3d.com/Manual//class-MonoManager.html). 

Open the Script Execution Order settings in Edit -> Project Settings. We will put the VisuoMotorRotation at the bottom - that means it will execute after the rest of our scripts. (This won't affect latency in any way, these are all done within a single frame of rendering.)

[![script-execution-order](/uxf-tutorial/images/script-execution-order.png)](/uxf-tutorial/images/script-execution-order.png)

## Test it

The VMR should mean the cursor is rotated about the origin - see this video 

[![vmr-video](/uxf-tutorial/images/vmr-video.gif)](/uxf-tutorial/images/vmr-video.gif)


Because of the work we did with the UXF Tracked Objects before we now have the position of both the hidden cursor (i.e. the mouse/hand) as well as the actual cursor output as files.

[![vmr-files-output](/uxf-tutorial/images/vmr-files-output.png)](/uxf-tutorial/images/vmr-files-output.png)

---

* [*Go to Part 9*](/uxf-tutorial/part-9)
