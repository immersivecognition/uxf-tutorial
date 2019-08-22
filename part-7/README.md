# Part 7

In this task, we want people to move quickly with ballistic movements. We will ensure this happens by adding a fail condition if the participant moves too slow (over 600ms). We can do this with a simple script that launches a coroutine and fails this trial if they move too slowly.

We will also play a sound when we move too slowly. I used [this negative beep sound](https://freesound.org/people/themusicalnomad/sounds/253886/) that I found (login required). 

I made a script called TooSlowCheck that will perform these functions. Here's a screenshot of all the script I have so far if you're following along.

[![scripts-part-7](/uxf-tutorial/images/scripts-part-7.png)](/uxf-tutorial/images/scripts-part-7.png)

And the script (hopefully pretty self explanatory as previous parts have covered everything here).

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UXF;

public class TooSlowCheck : MonoBehaviour
{
    public AudioClip failSound;
    public Session session;
    
    public void BeginCountdown()
    {
        StartCoroutine(Countdown());
    }

    public void StopCountdown()
    {
        StopAllCoroutines();
    }

    IEnumerator Countdown()
    {
        yield return new WaitForSeconds(0.6f);

        // if we got to this stage, that means we moved too slow
        session.CurrentTrial.result["outcome"] = "tooslow";
        session.EndCurrentTrial();

        // we will play a clip at position above origin, 100% volume
        AudioSource.PlayClipAtPoint(failSound, new Vector3(0, 1.3f, 0), 1.0f);
    }
}
```

Again, this script is attached to my Experiment object. Here are the assigned references...

[![too-slow-refs](/uxf-tutorial/images/too-slow-refs.png)](/uxf-tutorial/images/too-slow-refs.png)

And we want to call our BeginCountdown on trial begin, and StopCountdown on trial end.

[![too-slow-events](/uxf-tutorial/images/too-slow-events.png)](/uxf-tutorial/images/too-slow-events.png)

## Fixing the feedback

Right now the feedback (the Cursor Copy) is still going to try to display even if we move too slow. That is a problem because we don't have an "angle" measured. So lets add a condition where that doesn't happen in our FeedbackController script. I modified the first part of our Present() method that returns early if the outcome isn't a hit or miss:

```cs
...
    public void Present(Trial endedTrial)
    {
        // get the results for this trial
        // we have to cast to types (using the name of the type in brackets)
        string outcome = (string) endedTrial.result["outcome"];
        if (outcome != "hit" & outcome != "miss") return; // early exit, dont do anything
        float angle = (float) endedTrial.result["angle"];     
...
```

Later we will add additional text feedback for the different outcomes. 


## Test it

No sound here but you can see the trial ending when the movement isn't quick enough.

[![too-slow-test](/uxf-tutorial/images/too-slow-test.gif)](/uxf-tutorial/images/too-slow-test.gif)

---

* [*Go to Part 8*](/uxf-tutorial/part-8)