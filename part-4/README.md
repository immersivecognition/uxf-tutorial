# Part 4 - Reaching for a target

## Setting up the target 

Now we can move the cursor around and have the start block acting how we want it, we can work on the Target GameObject. We will begin by applying the same physics settings to the Target that we did for the StartPoint. Add a Rigidbody, disable Gravity, and apply all constrains. Since we got our object from a 3D model rather than a built-in Unity Primitive, it doesn't have a collider attached. We could use a MeshCollider, which would create a collider that matches the icosahedron shape. However, MeshCollider are computationally expensive so best practise is to avoid them where possible. Also, we want our Target to act like a sphere for simplicity, the model is just for appearance. So instead we will add a SphereCollider with radius 0.5. If the 3D model scale factor is set up correctly this should match the size of the icosahedron.

(You may notice Target has a single child called Object001, this is just the default outcome of importing .obj files into Unity, as a single file can contain multiple objects.)

[![sphere-collider](/uxf-tutorial/images/sphere-collider.png)](/uxf-tutorial/images/sphere-collider.png)

## Audio

When we touch the Target, we will play a sound as positive feedback for the participant. We need an audio clip in our project - let's create a Sounds folder in our project and drag a sound in from a folder on our PC. I found and downloaded [this](https://opengameart.org/content/picked-coin-echo-2) 'ding!' sound. 

[![audio-clip](/uxf-tutorial/images/audio-clip.png)](/uxf-tutorial/images/audio-clip.png)

## Writing the TargetController script

Now we are going to write a simple script that makes the Target disappear and play the sound when we touch it. This is similar to the StartPointController script, which uses the `OnTriggerEnter` method. We also have have a field for the AudioClip we want to play. Go ahead and create a new C# script called TargetController, then add it to your Target! Don't forget to assign the Collect Sound field in the inspector to your audio clip.

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class TargetController : MonoBehaviour
{
    // reference to the AudioClip we want to play on trigger enter.
    public AudioClip collectSound;

    /// OnTriggerEnter is called when the Collider 'other' enters the trigger.
    void OnTriggerEnter(Collider other)
    {
        if (other.name == "Cursor")
        {
            // disable the whole GameObject
            gameObject.SetActive(false);

            // play the collect sound (at the same position as the target, 100% volume)
            AudioSource.PlayClipAtPoint(collectSound, transform.position, 1.0f);
        }
    }
}
```

*Why not use an AudioSource*? Because we can't play sound from a disabled GameObject using an AudioSource. If we wanted to use an AudioSource we would have to keep the target enabled for a short time before disabling it so the sound can play. `AudioSource.PlayClipAtPoint` just plays a sound at a point in the scene, and is unrelated to the GameObject we are using.

Note on audio: We can set up some complex spatial audio systems which better simulate 3D sound, making objects feel much more as if they were physically there. [Oculus Audio Spatializer](https://developer.oculus.com/documentation/audiosdk/latest/concepts/book-ospnative-unity/?locale=en_US) is useful here (even if not using an Oculus Headset) but fairly difficult to set up.

[![collect-sound](/uxf-tutorial/images/collect-sound.gif)](/uxf-tutorial/images/collect-sound.gif)

(No sound in gifs, but it did work!)

---

* [*Go to Part 5*](/uxf-tutorial/part-5)