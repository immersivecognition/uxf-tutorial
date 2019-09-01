# Part 13 - VR controls

We will now make our cursor be controlled by the participant's hand. This will be quite simple and will just require a single script. 

## Inspecting the CameraRig

Take note of the CameraRig GameObject (provided by SteamVR) we have in our scene. Expanding the hierarchy reveals GameObjects for each controller and the "Camera". (The "Camera" GameObject here represents the imaginary "centre eye" between our two eyes, based on headset position.) We could very easily track the position of the controllers or the head by placing the UXF PositionRotationTracker script on these and referencing them in the Session inspector (as we did in Part 5).

---

* [*Go to Part 14*](/uxf-tutorial/part-14)