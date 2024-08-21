---
title: "[Prototype 3] Set Up Animations"
date: 2024-06-15 11:31:00 +09:00
categories: [Unity Learn, Practical Programming]
tags:
  [
    Unity, Game
  ]
---

How to open Animator Window
----------------------------
*****

1. After you click on the Player, apply an Animator Component in your Inspector.   
2. Attach your file for Controller and Avatar and double-click on that Controller box.   
3. You can see that it actually opens up a brand new window in your Scene for Animators.
4. If you want to move around in this view, you can use your mouse wheel.   

![Alt text](/assets/img/posts/Unity/Animator.png)

How to set up a jump animation
-------------------------------------------
*****

I wanted to do somethings to the jumping animation to really make it look real.   
So I opened my PlayerController Script, and got a reference to my Animator.   
It's really similar to how I got a reference to my Rigidbody.

> PlayerController.cs 

```c#
private Animator playerAnim;
```

Just like Rigidbody, I needed to get the actual Component on my Object.   

```c#
void Start()
{
  playerAnim = GetComponent<Animator>();
}
```

And I wanted to enter Update() function is actually where I wanted this animation to happen.   
But I had no idea what animation exactly were I trying to make happen.   
So I went to the Animator and found out player animator.   

I went to my Jumping layer and typed the code in script.   

```c#
private Animator playerAnim;

void Start()
{
  ...
  playerAnim = GetComponent<Animator>();
  ...
}

void Update()
{
  ...
  playerAnim.SetTrigger("Jump_trig");
}
```

How to set up a falling animation
---------------------------------
*****

I needed to add one more thing, which was whenever I collide with one of obstacles, I didn't want to keep running into one of obstacles.   
So I found from "Alive" to a "Death_01" in my Deaths Layer.

```c#
public bool gameOver = false;

private void ConCollisionEnter(Collision collision)
{
  if (collision.gameObject.CompareTag("Ground"))
  {
    isInGround = true;
  } else if(collision.gameObject.CompareTag("Obstacle"))
  {
    Debug.Log("Game Over!")
    gmaeOver = true;
    playerAnim.SetBool("Death_b", true);
    playerAnim.SetInteger("DeathType_int", 1);
  }
}
```
