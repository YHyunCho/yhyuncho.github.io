---
title: "Project Optimization"
date: 2024-07-29 11:48:00 +09:00
categories: [Unity Learn, Conceptual Learning]
tags:
  [
    Unity, Game
  ]
---

Variable Attributes
--------------------
*****

**[SerializeField]**

Until now I've only ever used 'public' or 'private' variables.   
But you know, there are a lot of other variable attributes that we should be familiar with.   

I opened my own project, 'Kill Zombie', and I also opened up my PlayerController Script.   
Wow you can see I have a lot of private and public variables in my Script.

![Alt text](/assets/img/posts/Unity/pcScriptVariable.png)

And if you remember, private variables can only be used within this class, and they can't be seen inside of the Unity Editor. Like this, 

![Alt text](/assets/img/posts/Unity/VariableAttribute.png)

In Unity, they actually have a very helpful variable attribute that we can use to see any private variables that we want to interect with. So I went to where my "trunSpeed" variable is created,  and typed ```[SerializeField]```.

```c#
private float stairForce = 120;
private float jumpForce = 550;
private float gravityModifer = 2.5f;
private float speed = 250000;
[SerializeField] private float turnSpeed = 50;
```

When I open up Unity I can actually see that under my PlayerController Script with my player selected, the turnSpeed variable pops up.   

![Alt text](/assets/img/posts/Unity/AfterVariable.png)

```[SerializeField]``` is a really helpful way of defining which variables we want to use just in the Editor.   
But you don't want to ever use in a different class. For example, If I have another class, another Script that was using PlayerController, I want to be able to change the turnSpeed value in Unity. But We don't actually want to change that turnSpeed value in the script that's using PlayerController.

**protected**

We can also use the keyword 'protected', and 'protected' is a mix of private and public. So with 'private', we can use this variable in this Script, in this class, but we can also use this variable in any other Script that actually inherits from PlayerController.

For example, PlayerController inherits from MonoBehavior. And we can think of ```Start()``` and ```Update()``` both as protected methods allowing us to use them in our Script. However if we made a brand new Script that didn't inherit from MonoBehavior, we wouldn't be able to use these methods.

So 'protected' allows we to see variables or methods outside of current class. But only to any Script or class that inherits from this main class.

**const**

Other keywords that we can use as well are words like ```const```.
```c#
private const float speed = 250000;
```

And 'const' is just short for constant. So that means that we can never change the value of speed because of this keyword.

**readonly**

When we first ```Instantiate()``` an Object and use readonly keyword, we can set that variable when we create that Object but then can never change it ever again, versus const, is definded as soon as you create that variable.

**static**

If we want to have something like 'global' variable to use anywhere in your code at all times.

Unity Event Functions
----------------------
*****

Same as variable, I've only ever use the default ```Update()``` and ```Start()``` event functions. But there are others we might want to use in different circumstances.   

And this is the part of the Script of CameraManager and PlayerController.

> PlayerController.cs

```c#
void Update()
{
  if (gameOver)
  {
    playerRb.velocity = Vector3.zero;
  }
  else 
  {
    ...
  }
}
```

> CameraManager.cs

```c#
void Update()
{
  if (swichCameraScript.thirdView.enabled == true)
  {
    ...
  } else if (swichCameraScript.deathView.enabled == true)
  {
    ...
  }
}
```

When I press Play, I can see it's actually starting to jitter a little bit and that definitely doesn't look good.

![Alt text](/assets/img/posts/Unity/Animation/BeforeEventFuncion.gif)

I could probably actually reposition this camera but I'll keep it for now.

Now there are two event functions that I need.

**FixedUpdate()**

First one is ```FixedUpdate()``` event function. FixedUpdate is especially useful when we are trying to do movement and physics. FixedUpdate() is actualy called before Update() calls, and actually happens when our game is trying to calculate any kind of physics that is happening when we're playing it.

So I fixed my code in PlayerController Script like this

> PlayerController.cs

```c#
void FixedUpdate()
{
  ...
}
```

**LateUpdate()**

Now in CameraManager Script, I used ```LateUpdate()```. This is particularly useful when we're trying to calculate the camera's position, and so we typically want to use LateUpdate() when we have a camera that follows player.

That way our camera knows that all of the calculations for our player's movement is done, this is definitely where our camera should go as the game is updating.

> CameraManager.cs

```c#
void LateUpdate()
{
  ...
}
```

Now after I saved this I pressed Play again.

![Alt text](/assets/img/posts/Unity/Animation/AfterEventFuncion.gif)

It's not doing that shaky, It's a lot smoother.

**Awake()**

One last thing that we can do in our Start() method is we can use another method called ```Awake()``` in place of ```Start()```.

And Start() is useful when you have certain Objects that are supposed to do something as soon as the game starts. However, there may be certain Objects that you wnat to only do something when you want to use it.

For example, if you want a wall to move, you could use the Awake() method, so somebody presses a button, it will then call the Awake() method for that Object, that wall, and then it will do something.

So if there's certain values that you want to make sure are created when an Object wakes up versus when the game starts, you could use the Awake() method.