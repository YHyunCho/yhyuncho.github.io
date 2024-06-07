---
title: Keep the objects inbounds, remove the objects offscreen, and prefabs
date: 2024-06-06 11:54:00 +09:00
categories: [Unity]
tags:
  [
    Unity, Game
  ]
---

How to keep the player inbounds
--------------------------------
*****

We're going to set a boundary so that the player cannot leave this area.

In my game, the player is moving left or right on their X position.    
So I'm going to get the X value specifically.   
And I want to make sure that it's lessthan -10.

> PlayerController.cs

```c#
if (transform.position.x < -10)
```

And now we're going to get their position and set it to a position that we want them to go to.
So we can use a "new Vector3".

I want to keep them at -10 whenever they try, so I'll put -10 for the X value.   
But their Y and Z positions never change.

```c#
void Update() 
{
  if (transform.position.x < -10) 
  {
    transform.position = new Vector3(-10, transform.position.y, transform.position.z);
  }
}
```

That code was only for keeping the object on the leftside.   
My player can still leave the right side.   
So I fixed some code line.   

```c#
void Update() 
{
  if (transform.position.x < -10) 
  {
    transform.position = new Vector3(-10, transform.position.y, transform.position.z);
  }
  if (transform.position.x > 10)
  {
    transform.position = new Vactor3(10, transform.position.y, transform.position.z);
  }
}
```

You can also write the code like this

```c#
public float xRange = 10;
void Update() 
{
  if (transform.position.x < -10) 
  {
    transform.position = new Vector3(-xRange, transform.position.y, transform.position.z);
  }
  if (transform.position.x > 10)
  {
    transform.position = new Vactor3(xRange, transform.position.y, transform.position.z);
  }
}
```

Launch projectile on spacebar press
------------------------------------
*****

Specifically, they're these series of functions called GetKey, GetKeyDown, GetKeyUp.   
And so these are all related to your keyboard keys.

If you GetKey, it gets whenever the key is pressed.   
If you GetKeyDown, it's whenever the key is pressed down specifically.   
And KeyUp is whenever you actually let go of that key, it will do that thing.   

So for now, I'll do GetKeyDown so that way it's really instant.

```c#
  if (Input.GetKeyDown(KeyCode.Space))
```

Now, whenever the space key is pressed down, something will happen.   
I'm going to use a new method. So this one's called ```"Instantiate()"```

So what Instantiate() does it,  it's another way of saying that we're going to Create an Object,   
but specifically in my case, Instantiate() is for creating copies of Objects that already exist.   

And this method is what allows we to create a copy of that object.

```c#
void Update() 
{
  if (Input.GetKeyDown(KeyCode.Space))
  {
    Instantiate(projectilePrefab, transform.position, projectilePrefab.transform.rotation);
  }
}
```
And now when we press the Play button, we can see the object flies off.


How to destroy objects offscreen
---------------------------------
*****

One of the problems that I have is, I have all things that are able to march off into eternity.    
And we can even generate all different objects and this is actually going to cause a huge problem for our game later down the line, as I hava all different objects in my scene that are constantly flying away, constantly being updated.

So I created new script, it's called "DestroyOutOfBounds".

```c#
private float topBound = 30;

void Update() 
{
  if (transform.position.z > topBound)
  {
    Destroy(gameObject);
  }
}
```
There's a really helpful method that we can use in C# called ```"Destroy()"```

Now Destroy actually enables us to remove a GameObject, we can remove components, we can remove assets and so we'll use Destroy to remove the GameObjects on the screen that are no longer actually within view of our Player.

You can add a conditional statement to this code.
```c#
private float topBound = 30;
private float lowerBound = -10;

void Update() 
{
  if (transform.position.z > topBound)
  {
    Destroy(gameObject);
  } else if(transform.position.z < lowerBound)
  {
    Destroy(gameObject);
  }
}
```

What is the Prefab?
--------------------
*****

A Prefab is that we can keep reusing it anytime, anywhere, and it will always be the exact same,    
and it'll have everything that it should be doing.

If you make a Prefab, you can keep reusing the object over and over again.    
And it will always have the Script applied to it.    
If you change the size of the objects, it'll always be that size whenever you bring it from your projects into your Project Hierarchy.