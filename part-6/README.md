# Part 6 - Adding a miss outcome

## Creating a task area

Right now our task only has one trial outcome. We want to add the possibility of missing the target. This is probably going to mean a little bit of backtracking - and removing some of the code we have written. This is because we want our task to be all about moving to the correct angle. So we need an edge of a circle to define our the boundary of our task.

We will start by creating an empty GameObject that sits at the centre of our table; I called it TaskArea. Then, we will add a capsule collider to it of height 100 and radius 0.2, and set it to be a trigger. This capsule will define our movement area - move outside of this collider and we end the trial. We also need a RigidBody with the same settings as our other objects so that collisions are detected. (You can copy and paste the component from the Target or StartPoint.)

(At this point its useful to scale down the size of 3D icons in the Gizmos option in the Scene view.)

[![task-area](/uxf-tutorial/images/task-area.png)](/uxf-tutorial/images/task-area.png)

Its quite hard to see above, but essentially we have a collider of radius 0.2. I set the height to be huge making this effectively a 2D task. That means we can do something when our cursor moves outside of this area.

Now we are going to make another script, similar to the others, that ends the trial when we exit this trigger area. To stay consistent let's name it TaskAreaController. All it needs to do is end the trial when the cursor exits the trigger.

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UXF;

public class TaskAreaController : MonoBehaviour
{
    public Session session;

    void OnTriggerExit(Collider other)
    {
        if (other.name == "Cursor" & session.InTrial)
        {
            // end current trial
            session.EndCurrentTrial();
        }
    }
}
```

Attach it to our TaskArea GameObject, assign the session reference, and let's test it!

[![task-area-exit](/uxf-tutorial/images/task-area-exit.gif)](/uxf-tutorial/images/task-area-exit.gif)

You can see that the trial (seen in the Info Bar at the bottom) but nothing happens. At this point we are going to change the behaviour of the target. Instead of detecting collisions, we are just going to use the angle of the cursor relative to the start point to work out if the target was hit. The first step to do this is remove the TargetController script from the Target. We may re-use some of its code later.

We will work some more on the TaskAreaController. At the moment we exit the task area, we will calculate the angle of the cursor within the task area (imagine the hands of a clock face). This angle also one of the things we want UXF to store in our output for us. Now in our TaskAreaController OnTriggerExit method, we will:

* Get the position of the cursor
* Calculate the angle (relative to the forwards direction)
* Log the angle to the console for sanity checks
* Store this angle as a result for this trial (i.e. dependent variable)

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UXF;

public class TaskAreaController : MonoBehaviour
{
    public Session session;
    public Transform startPoint;

    void OnTriggerExit(Collider other)
    {
        if (other.name == "Cursor" & session.InTrial)
        {
            Vector3 cursorPos = other.transform.position;
            cursorPos.y = 0; // strip the y coordinate - we want to measure a 2D angle (XZ plane)
            Vector3 targetDir = Vector3.forward;

            // calculate the angle from the target (forward direction) to the cursor (about the up axis)
            float angle = Vector3.SignedAngle(targetDir, cursorPos, Vector3.up);
            Debug.Log(angle);

            // store the result in the current trial - this will then be saved in the trial_results csv
            session.CurrentTrial.result["angle"] = angle;

            // end current trial
            session.EndCurrentTrial();
        }
    }
}
```

Note: I used `Vector3.up` as the "about" axis as that's what the angle rotates around. You can imagine twisting a spinning top about the `up` (+y) axis. You can of course manually calculate the angle with trigonometry but Unity has lots of built in methods for vector mathematics. ([Some examples](https://docs.unity3d.com/ScriptReference/Vector3.html).)

Before we continue we need to make sure UXF is set up to understand the "angle" result we have collected. UXF needs to know these beforehand to set up a CSV file with the correct headers. To do this just type "angle" as one of the fields in the Custom Headers field in the Session inspector.

[![custom-header](/uxf-tutorial/images/custom-header.png)](/uxf-tutorial/images/custom-header.png)

### Test it

You can see the angle calculation looks correct! (Here positive is clockwise.)

[![angle-test](/uxf-tutorial/images/angle-test.gif)](/uxf-tutorial/images/angle-test.gif)

And now you can check the `trial_results.csv` output file. It should have stored the angle on each trial.

[![angle-output](/uxf-tutorial/images/angle-output.png)](/uxf-tutorial/images/angle-output.png)

## Detecting a hit or miss

Now we can get back to the problem of detecting a hit or a miss. There are lots of different ways to do this, such as using RayCasts to cast a ray to check if a straight line trace from the centre outwards would incur a hit on the target. However, here we will do a very simple method, where we just use an angle threshold. If the angle magnitude was below that threshold, we can say we hit the target, otherwise we missed.

Let's check the angle is within a threshold of 4 degrees. If it is, we will store in the results a for a variable named "outcome": "hit", else, store "miss". (Don't forget again to add the custom header "outcome".) Here are the changes needed for the TaskAreaController script.

```cs
        if (other.name == "Cursor" & session.InTrial)
        {
            Vector3 cursorPos = other.transform.position;
            cursorPos.y = 0; // strip the y coordinate - we want to measure a 2D angle (XZ plane)
            Vector3 targetDir = Vector3.forward;

            // calculate the angle from the target (forward direction) to the cursor (about the up axis)
            float angle = Vector3.SignedAngle(targetDir, cursorPos, Vector3.up);
            Debug.Log(angle);

            // store the result in the current trial - this will then be saved in the trial_results csv
            session.CurrentTrial.result["angle"] = angle;

            // hit if we are within the threshold
            float threshold = 4.0f;
            bool hit = Mathf.Abs(angle) < threshold;
            session.CurrentTrial.result["outcome"] = hit ? "hit" : "miss";

            // end current trial
            session.EndCurrentTrial();
        }
```

(The `condition ? valueIfTrue : valueIfFalse` statement is called a [conditional operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-operator) in C#.)

## Feedback

We will now add some feedback to the end of the trial. We want to show the position at which the cursor left the task area. We'll do this by placing a copy of the cursor at the position in which we left the ring. First lets duplicate our Cursor GameObject, rename it CursorCopy, and making sure to delete the SphereCollider, MouseController and PositionRotationTracker components from it. We should also disable the object, as we only want to enable it for a short time after a trial completed to show the user how close they were.

Here's what my hierarchy looks like at this point:

[![hierarchy](/uxf-tutorial/images/hierarchy.png)](/uxf-tutorial/images/hierarchy.png)

We will now make another script that handles feedback called FeedbackController. We will attach it to our Experiment GameObject, since feedback won't involve one single object in our scene. It will have a function called `Present()` which simply shows the relevant feedback based on the trial outcomes. It will:

* Calculate the intersection point of leaving the task area using the "angle" result. (We could use the cursor's position, but this way is more precise and consistent.)
* Move the CursorImage to that point, and enable it
* Play the "ding" sound if we hit the target

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UXF;

public class FeedbackController : MonoBehaviour
{
    // we don't need a reference to the Session here because we are supplied a reference to the trial by the event
    // we only need a reference if we are doing something TO the session, rather than reacting to an event

    // we do need a reference to the cursor copy since we have to enable and move it.
    public Transform cursorCopy;

    // reference to the AudioClip we want to play on hit.
    public AudioClip collectSound;


    // method to show the feedback. the On Trial End event will pass us the reference to the trial that has just completed
    public void Present(Trial endedTrial)
    {
        // get the results for this trial
        // we have to cast to types (using the name of the type in brackets)
        float angle = (float) endedTrial.result["angle"];
        string outcome = (string) endedTrial.result["outcome"];

        // calculate new position of cursor copy by rotating "angle" degrees about the y axis
        // relative to the forward position, and using radius of 0.2
        Vector3 newPosition = Quaternion.Euler(0, angle, 0) * (Vector3.forward * 0.2f);
        
        // don't change the height. 2D task
        newPosition.y = cursorCopy.position.y;

        // enable cursor copy and set its position
        cursorCopy.gameObject.SetActive(true);
        cursorCopy.position = newPosition;

        // if we hit, play our "collect" audio clip (copied code from TargetController)
        if (outcome == "hit")
        {
            // we will play it at the new cursorCopy location, 100% volume
            AudioSource.PlayClipAtPoint(collectSound, newPosition, 1.0f);
        }
    }   
}

```

Add the script to your Experiment GameObject and assign all the references.

[![feedback-controller-references](/uxf-tutorial/images/feedback-controller-references.png)](/uxf-tutorial/images/feedback-controller-references.png)

Finally, this `Present()` method needs to be called On Trial End - so add it to the UXF On Trial End event. Here's what my events look like at this stage.

[![session-events](/uxf-tutorial/images/session-events.png)](/uxf-tutorial/images/session-events.png)

## Test it

[![cursor-copy](/uxf-tutorial/images/cursor-copy.gif)](/uxf-tutorial/images/cursor-copy.gif)

## Hiding the Cursors at the right time

In the experiment, we don't want view of our cursor during the trial, and we want the cursor copy (the feedback) to display only for a short time after the trial ends. For the former, we will simply disable the MeshRenderer component of our Cursor at the start of the trial by adding an event under On Trial Begin which turns `enabled` to false.

[![disable-meshrenderer](/uxf-tutorial/images/disable-meshrenderer.png)](/uxf-tutorial/images/disable-meshrenderer.png)

A short time (here 500ms) after the trial ends, we will re-enable the cursor and also hide the cursor copy - the script is a very simple launch of a coroutine. I called SetupNextTrial since it cleans up the feedback and sets things up for the next trial.

We are also going to set the StartPoint GameObject to inactive at the end of a trial and re-enable it when the feedback has finished.

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UXF;

public class SetupNextTrial : MonoBehaviour
{
    public MeshRenderer cursorMR;
    public GameObject cursorCopy;
    public GameObject startPoint;

    public void DelayedSetup()
    {
        StartCoroutine(SetupSequence());
    }

    IEnumerator SetupSequence()
    {
        yield return new WaitForSeconds(0.5f);

        // note: .enabled is a property of *components*
        //       .SetActive() is a method of GameObjects

        cursorMR.enabled = true;
        cursorCopy.SetActive(false);
        startPoint.SetActive(true);
    }
}
```

Now add the component to the Experiment GameObject, assign the references in the inspector, and finally make sure DelayedSetup is called On Trial End in the `UXF_Rig` (along with SetActive(false) of the StartPoint).

[![setup-next-trial](/uxf-tutorial/images/setup-next-trial.png)](/uxf-tutorial/images/setup-next-trial.png)

[![on-trial-end-events](/uxf-tutorial/images/on-trial-end-events.png)](/uxf-tutorial/images/on-trial-end-events.png)

## Test it.. again

It should look like this:

[![trial-timings](/uxf-tutorial/images/trial-timings.gif)](/uxf-tutorial/images/trial-timings.gif)

---

* [*Go to Part 7*](/uxf-tutorial/part-7)