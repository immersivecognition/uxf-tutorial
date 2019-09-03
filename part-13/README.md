# Part 13 - VR controls

We will now make our cursor be controlled by the participant's hand. This will be quite simple and will just require a single script. 

## Inspecting the CameraRig

Take note of the CameraRig GameObject (provided by SteamVR) we have in our scene. Expanding the hierarchy reveals GameObjects for each controller and the "Camera". (The "Camera" GameObject here represents the imaginary "centre eye" between our two eyes, based on headset position.) We could very easily track the position of the controllers or the head by placing the UXF PositionRotationTracker script on these and referencing them in the Session inspector (as we did in Part 5).

[![camera-rig-gameobject](/uxf-tutorial/images/camera-rig-gameobject.png)](/uxf-tutorial/images/camera-rig-gameobject.png)

We can use the hand GameObjects' positions to update the position of the cursor.

## HandController script

We will make a new script called HandController that (on each frame) takes the position of the `Controller (right)` GameObject and updates the position of the attached GameObject (but maintaining the Y position, locking it to 2D). Updating the position of the HiddenCursor will mean we can leave all of our VMR code, etc untouched. The scripts we wrote before don't care if it is controlled by mouse or hand.

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class HandController : MonoBehaviour
{
    public Transform rightHand;

    void Update()
    {
        Vector3 handPos = rightHand.position;
        handPos.y = transform.position.y; // lock to current Y position
        transform.position = handPos;
    }
}
```

Attached to the HiddenCursor, and an assigned reference to `rightHand` (to the SteamVR `Controller (right)`) in the inspector, it should work!

[![Video of hand-control](/uxf-tutorial/videos/hand-control.png)](/uxf-tutorial/videos/hand-control.mp4)

Obviously we don't want the controllers visible, but we can fix that easily later!

It was at this stage that I saw a mistake with the error clamp code - the height of the cursor isn't right. A simple fix maintains the y position of the cursor.

```cs
        if (clamped)
        {
            // calculate distance from center
            float dist = (hiddenCursor.position - startPoint.position).magnitude;
            // move towards target with calculated distance (i.e. with 0 error)
            Vector3 newPos = startPoint.position + Vector3.forward * dist;
            newPos.y = transform.position.y; // lock to 2D
            transform.position = newPos;
        }
```

## Left/right hand choice

One extension readers could try is have a UXF participant details entry that allows participants to select left or right handed - then using (e.g.) an `if` statement in the HandController script to reference the required controller GameObject.

## Testing without clamped error

I then tested the code with the `clamped_error` setting set to `false` in the settings profile. Here's what it looks like!

[![Video of vr-vmr](/uxf-tutorial/videos/vr-vmr.png)](/uxf-tutorial/videos/vr-vmr.mp4)

---

* [*Go to Part 14*](/uxf-tutorial/part-14)