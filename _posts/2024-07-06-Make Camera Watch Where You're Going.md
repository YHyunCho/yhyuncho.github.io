---
title: "[Prototype 4] Make Camera Watch Where You're Going"
date: 2024-07-06 18:30:00 +09:00
categories: [Unity Learn, Practical Programming]
tags:
  [
    Unity, Game
  ]
---

Long time no see everyone LOL

How to create a focal point for the camera
-------------------------------------------
*****

Okay, So for this game, my entire platform is the center point of my entire Player view.   
So I used the camera to be able to do so.   

First, I created an empty GameObject in my Hierachy(Make sure its position is center!).   
And I named this the "Focal Point".   
In this case, this GameObject is the center point of my entire scene, literally just be a position.   

Second, Drag and drop the Main Camera onto the Focal Point GameObject.   
Now the Main Camera is child object of the Focal Point.   
The Main Camera is always tied to the location of the Focal Point by doing this.

And I made a new C# Script, Rotate Camera, onto the Focal Point.   
![Alt text](/assets/img/posts/Unity/FocalPoint.png)

Perfect!   
And let's rotate the focal point by user input. It's easy peasy for us now you know.   

> RotateCamera.cs

```c#
public float rotationSpeed;

    void Update()
    {
        float horizontalInput = Input.GetAxis("Horizontal");

        transform.Rotate(Vector3.up, horizontalInput * rotationSpeed * Time.deltaTime);
    }
```

How to move in direction of focal point
----------------------------------------
*****

First I needed to add forward force to the player.   
I don't think I need to explain right? Just check my code here!

> PlayerController.cs

```c#
private Rigidbody playerRb;
public float speed = 5.0f

void start()
{
  playerRb = GetComponent<Rigidbody>();
}

void update() 
{
  float forwardInput = Input.GetAxis("Vertical");
  playerRb.AddForce(Vector3.Forward * speed * forwardInput);
}
```

I actually wanted to push the player in the direction that the Camera is facing.   
For example, if I'm facing right side, I wanted my player to go directly up in my game view.   

**Global and Local**

Now an intersting thing is this button for Global or Local.

![Alt text](/assets/img/posts/Unity/GlobalAndLocal.png)

We can see when we change it the positions of those arrow actually change.   
If we take a look at the Focal Point, we can actually see which way is currently the front of the Focal Point!   
So when I rotate my Camera, and I watch these axes, I can actually see the Z-axis pointing in the direction that my Camera was pointing.   
My point is the Camera is following those Local coordinates.   

Hmm so when do we use Global coordinate?   
When we're actually trying to setup our scene, and setup our environment, and we want everything to be in the same place, using Global coordinate system is a lot better.

So using Global, and using Local are helpful for different situations.

Anyway to move in direction of focal point, I got a reference to the Focal Point.

> PlayerController.cs

```c#
private GameObject focalPoint;
...

void start()
{
  playerRb = GetComponent<Rigidbody>();
  focalPoint = GameObject.Find("Focal Point");
}
```

Instead of moving the player in the forward direction of my world, I made it the foward direction that my Camera's pointing in.

> PlayerController.cs

```c#
...

void update() 
{
  float forwardInput = Input.GetAxis("Vertical");
  playerRb.AddForce(focalPoint.transform.forward * speed * forwardInput);
}
```

And now I can see when I rotate the Camera and move forward, it goes in that direction.