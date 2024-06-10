---
title: Jump force
date: 2024-06-10 13:00:00 +09:00
categories: [Unity]
tags:
  [
    Unity, Game
  ]
---

How to make player jump at start?
----------------------------------
*****

If you want to actually control the movement and the Gravity of the player, and all of that, you actually need to use a new way to get a Component for your "Rigidbody".   
Because RidgidBodies aren/t something that are automatically on other GameObject versus somthing like a Transform, which is always on GameObject.    

So in your PlayerController Script, you need to create a variable.

> PlayerController.cs

```c#
private Rigidbody playerRb;
```

So this will be the Ridbody Component on our player.   

Now versus somthing like transform where we can already get access to our transform Component, we don't automatically get access to our Rigidbody Component.   
Because that's not something on every single gameObject.   
So in order to do that, you actually need to use something called ```GetComponent<>()```

**How can we use < > ?**   

Now you have to use these left and right carets, which is different that using parantheses to pass in parameters.   
Spectifically, the left and right carets actually get you a type of something.   
For example, in this case we're getting a Rigidbody, but you could use these carets to find to type of anything.   
So if you actually want to get a Collider, for example, that was on your player, you could do that too.

So the carets are specifically for finding different things like your components that are on your different object.   

```c#
private Rigidbody playerRb;

void Start()
{
  playerRb = GetComponent<Rigidbody>();
}
```

we're going to apply a force to our Rigidbody because we're using a Rigidbody Component.   
And it already has a method called ```AddForce()```   

And you can think of this similar as ```Transform.Translate()"```, but instead of moving something physically, just using positions, we can apply forces to things just like we would in the real world using physics to make something move.

In this case, we're going to make our player move up right?   
So I'm going to use "Vector3.up" and I'm going to amplify this by a lot.   
So I'll just multiply this by 100.   

```c#
private Rigidbody playerRb;

void Start()
{
  playerRb = GetComponent<Rigidbody>();
  playerRb.AddForce(Vector3.up * 10);
}
```

How to make player jump if spacebar pressed
---------------------------------------------
*****

I think it's easy for us now to make player jump if spacebar pressed.   
Let's check the code.   

```c#
void Update()
{
  if (Input.GetKeyDown(KeyCode.space))
  {

  }
}
```
Now we're going to take our AddForce, cut it out of Start.   
So that way the player doesn't jump when it starts, although we could do.   
And in our if() statement we'll make it so that the player jumps whenever we press space.   

Also I'm going to add in a ```ForceMode```, so I can actually apply different forces through this ForceMode.   
And then I'm actually going to apply an ```Impulse``` force.

**ForceMode** : it applies that force over time.   
**ForceMode.Impulse** : It immediately applies the force that I want to apply.   

```c#
void Update()
{
  if (Input.GetKeyDown(KeyCode.space))
  {
      playerRb.AddForce(Vector3.up * 10);
  }
}
```

How to tweak the jump gravity
------------------------------
*****

In our ```Start()``` method, you can actually change the gravity of your physics.   
```Physics``` will actually get all of the physics in our game in Unity.   
And we can actually get gravity by typing ```.gravity```.   
So it's a property of you Physics.

First, let's make a variable.

```c#
private Rigidbody playerRb;
public float jumpForce = 10;

```

And I'll type like this.

```c#
public float gravityModifier = 4;

void Start()
{
  playerRb = GetComponent<Rigidbody>();
  Physics.gravity *= gravityModifier;
}
```

Now we're done!   

How to prevent player from double-jumping
-------------------------------------------
*****

We have to use some of our coding skills to do so.   
So I'm just going to show you the code.

```c#
public bool isOnGround = true;

void Update()
{
  if (Input.GetKeydown(KeyCode.space) && isOntheGround)
  {
    playeRb.AddForce(Vector3.up * jumpForce * ForceMode.Impulse);
    isOntheGround = false;
  }
}

private void OnCollisionEnter(Collision collision) 
{
  isOnGround = true;
}
```

We need to use our Collisions.   
Since we have a Collider on the player, and we have Colliders on the ground, we know that when the player touches the ground that they've evtered the Collider Box for the ground.   

So I made one more method called OnCollisionEnter.   
So whenever the player enters a Collision with somthing now you can set your isOntheGround to true.

