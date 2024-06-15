---
title: Take Control of the Object Speed and Movement
date: 2024-06-06 06:31:00 +09:00
categories: [Unity]
tags:
  [
    Unity, Game
  ]
---

How to allow the object to move left/right
------------------------------------------
*****

If you open your Script, we're going to need a way to control the object to move left or right,   
so we should probably just use a variable to do that.

We're goint to make this a public variable so that we can control it just like we did with the speed.

> PlayerController.cs

```c#
public float turnSpeed;

void Update()
{
  transform.Translate(Vector3.forward * Time.deltaTime * speed);
  transform.Trnaslate(Vector3.right * Time.deltaTime * turnspeed);
}
```

We can actually use right so this'll move the object in the right direction.   
And don't forget "Time.deltaTime",   
because we want to be able to update this position over time instead of once every frame.

And go back to your game, press Play.   
if you click on Turn speed and drag it to the right, it'll actually start turning right.

How to make left/right movement on input
----------------------------------------
*****

If you go to the top left under Edit, or if you're on Mac, under Unity,   
if you go down to Project Settings, and click on that, there's all these options for Project Setting.

But what we really care about actually our input.
![Alt text](/assets/img/posts/Unity/inputManager.png)

This is actually "Input Manager" in Unity, and they have helpfully already created a way,    
when you do something, it's already mapped to be able to change that value.

And You can see Horizontal and Vertical axes.   
If you want it to go left or if you want it go right, using these Horizontal Axes.   
Knowing this, you can do something really easy to be able to control the player's movement.

First we're goint to creat a public variable.   
So we can actually watch and see what it's doing
```c#
public float horizontalInput;
```

And now, if you remember that Input Manager,   
that is actually a class and a method that we can call just for that input manager.

And then, we were looking for that axis before, so we can ues our GetAxis() method.
```c#
horizontalInput = Input.GetAxis("Horizontal");
```
This will give you the values for when we press the left or right key on that axis.

But we also need to tell it which axis we want to use,   
and in this case it's asking for the name of our axis: specifically a string.
For this we typy in the word "Horizontal",   
because in Unity, in our input Manager, the name of our Horizontal axis is just Horezontal.   

Now we're getting the Input for whenever a player presses the left or right key.   
You need to go into Translate() and multiply this by our horizontalInput.
```c#
transform.Trnaslate(Vector3.right * Time.deltaTime * turnspeed * horizontalInput);
```

So now, you can see what is happening on the Horizontal Input.


```c#
public float horizontalInput;

void Update()
{
  horizontalInput = Input.GetAxis("Horizontal");

  transform.Translate(Vector3.forward * Time.deltaTime * speed);
  transform.Trnaslate(Vector3.right * Time.deltaTime * turnspeed * horizontalInput);
}
```

How to take control of the object speed
----------------------------------------
*****

If you take a look at our Input Manager again, you can see the Vertical axis here.
You can do same thing that we did with our Horizontal Input and do that with our Vertical Input. 

```c#
public float horizontalInput;
public float forwardInput;

void Update()
{
  horizontalInput = Input.GetAxis("Horizontal");
  forwardInput = Input.GetAxis("Vertical");

  transform.Translate(Vector3.forward * Time.deltaTime * speed * forwardInput);
  transform.Trnaslate(Vector3.right * Time.deltaTime * turnspeed * horizontalInput);
}
```

How to make object rotate instead of slide
------------------------------------------
*****

Luckily our IDE, there is method called Rotate().    
It'll actually rotate it at an angle.

And we're going to use Vactor3.
Well you know that the Axis is that Y-Axis, that Vector3 up.

```c#
public float horizontalInput;
public float forwardInput;

void Update()
{
  horizontalInput = Input.GetAxis("Horizontal");
  forwardInput = Input.GetAxis("Vertical");

  transform.Translate(Vector3.forward * Time.deltaTime * speed * forwardInput);
  transform.Rotate(Vactor3.up, turnspeed * horizontalInput * Time.daltaTime);
}
```

