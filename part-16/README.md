# Part 16 - Minor experiment additions

At this stage I wanted to add a few features that I forgot about earlier. These are:

* Record the actual hand angle (measured by the HiddenCursor position) in the trial results. This is because in the case of the error clamp, the angle we output is the angle of the visible cursor (therefore fixed to the VMR angle we specify). This is easily done by calculating the angle and storing it in `trial.result["hand_angle"]`. This just required some minor changes to the TaskAreaController script. 
* Have an option for playing a (different) sound when completing the movement regardless of hitting the target or not. This is closer to the Ivry lab version.
* Make sure the target disappears at the end of feedback. Here we just disable the target in the SetupNextTrial.SetupSequence method.
* Lower the target distance to 0.15m.
* Add re-centre position/direction ability with keyboard press.
* Add arrow sprite facing direction reach.
* Remove the extra time given on practice trials. Turns out it's not needed and could interfere with the adaptation.
* Remove the "jerk" experienced at the initial part of the reach. This is because the VMR is turned on instantaneously. We will try to fix this by hiding the cursor when the trial begins up until we leave the start point.
* Other minor tweaks.

(See the Git commit changelog for the full code change details.)


---

* [*Go to Part 17*](/uxf-tutorial/part-17)