---
title: Basic of Pedal to the Metal and Camera
date: 2024-06-05 22:31:00 +09:00
categories: [Unity]
tags:
  [
    Unity, Game
  ]
---

How to give the object a forward motion
----------------------------------------
*****

Okay, Let's move the blue arrow in the scene.   
If you look in the Inspector on the right side, 
in our "Transform" Component, we can see that the Z is updating.

In the code, we can write like this.

> PlayerController.cs   

```c#
void Update()
{
  transform.Translate(0, 0, 1);
}
```

You can also change that position by the X, and by the Y.


How to use a Vector3 to move forward
-------------------------------------
*****

You can also write the code with Vector3

```c#
void Update() 
{
  transform.Translate(Vector3.forward);
}
```

A Vector3 is a representation of 3D vectors, and points.   
In fect, If we used "." and then "forward",   
you can see that ".forward" is actually just shorthand for writhing 0, 0, 1.


How to customize the object's speed
-----------------------------------
*****

We have a class already made to keep track of time.   
The class is "deltaTime" to get the change in time between all of our different frames.

```c#
void Update()
{
  transform.Translate(Vector3.forward * Time.deltaTime * 20);
}
```

I multiplied by say 20,   
So it should move about 20 meters every second now.

There's other way to wirte the same code.
We can create the variable and use it.

```c#
public float speed = 20;

void Update()
{
  transform.Translate(Vector3.forward * Time.deltaTime * speed);
}
```

How to make the camera following the object
-------------------------------------------
*****

Write the code like this.

> FollowPlayer.cs   

```c#
public gameObject player;

void Update()
{
  transform.position = player.transform.position;
}
```

and make sure you select Main Camera in the Hierarchy, in the inspector you can see on the camera scipt.   
There's a Player variable that has no GameObject attached.   
So we're goint to make sure left-click on the object in your Hierarchy, and drag it into that box.

Add and offset to the camera position
-------------------------------------
*****

```c#
public GameObject player;
private Vector3 offset = new Vector3(0, 5, -7);

void Update()
{
  transform.position = player.transform.position + offset;
}
```

How to make smooth the camera
------------------------------
*****

Change "Updete()" to "LateUpdate()" in camera script.

```c#
void LateUpdate()
{
  transform.position = player.transform.position + offset;
}
```