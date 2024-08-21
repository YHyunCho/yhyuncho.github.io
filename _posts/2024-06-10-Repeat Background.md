---
title: "[Prototype 3] Repeat Background and Game Over Trigger"
date: 2024-06-13 09:00:00 +09:00
categories: [Unity Learn, Practical Programming]
tags:
  [
    Unity, Game
  ]

---

> I need to repeat the background and move it left at the same speed as the obstacles, to make it look like the world is rushing by!

How to reset position of background?
-------------------------------------
*****

Let's creat a new script called RepeatBackground.cs and attach it to the Background Object.

I created a variable for the position.
> RepeatBackground.cs

```c#
private Vector3 startPos;
```

In this case, I was actually going to be getting the position that our Object starts at.   
Now, this would be very helpful, so that I know where I should reset our Background when it reaches the point that I need to put it back, and make it look like it's repeating.   

So I needed to set our "startPos" and I got all of the coordinates of my transform.position.

```c#
void Start()
{
  startPos = transform.position;
}

void Update()
{
  if (transform.position.x < startPos.x - 60)
  {
    transform.position = startPos;
  }
}
```

And since my Background was only moving on the X-axis, that was the only position I needed to check against.   
I wanted to see whenever my current position becomes less than my starting position.   
But specifically I wanted to make sure that our Background was farther down the line on my X-Axis before I reset the position of my Background to be the starting position again.   

How to fix background repeat with collider
------------------------------------------
*****

I had made it that my Background repeated every time it went back through but it did look a little bit weird.   
It wasn't exactly perfect.   
So in order to make my Background transition seamlessly back and forth, I used some new variables and Box Collider to be able to do.   

```c#
private float repeatWidth;
void Start()
{
  startPos = transform.position;
  repeatWidth = GetComponent<BoxCollider>();
}
```

And the Box Collider in the inspector, it actually has a "Size".   
So I didn't need to guess what the halfway point was of my Background.

```c#
private float repeatWidth;
void Start()
{
  startPos = transform.position;
  repeatWidth = GetComponent<BoxCollider>().size.x / 2;
}
```

Add a new game over trigger
---------------------------
*****

I created Tags for Ground and Obstacles.

> PlayerController.cs

```c#
public bool gameOver false;

private void OnCollisionEnter(Collision collision)
{
  if(collision.gameObject.CompareTag("Ground"))
  {
    isOnGround = true;
  } else if(collision.gameObject.CompareTag("Obstable"))
  {
    gameOver = true;
    Debug.Log("Game Over!");
  }
}
```

How to stop movement on gameOver
---------------------------------
*****

I already have my Movement.cs script. So I used it.    
What I needed to know was how exactly was I going to know what's happening in my PlayerController Script.   
First I created a variable.

> Movement.cs

```c#
private PlayerController playerControllerScript;
```

It's very similar to how I would just call GameObjects.   
And in my start() method, I've found the name of my object which in this case in my game is called "Player".   
After that I could actually get their Component, and I was looking for specitically their "PlayerController" object.   

```c#
void start
{
playerControllerScript = GameObject.Find("Player").GetComponent<PlayerController>();
}
```

Okay now I can actually stop things from moving whenever I collide with that player, and the game over condition happens.   
```c#
void start
{
playerControllerScript = GameObject.Find("Player").GetComponent<PlayerController>();
}

void update
{
  if (playerControllerScript.gameOver == false)
  {
    transform.Translate(Vector3.left * Time.deltaTime * speed);
  }
}
```

How to stop obstable spawning on gameOver
------------------------------------------
*****

I went to my SpawnManager Script and used the same thing that I just did in my Movement Script.   
I got another reference to my PlayerController Scipt.   

> SpawnManager.cs

```c#
private PlayerController playerControllerScript;
```

and I found in my scene which happens to be called player.   
Then in plyer I could find the Component which is my PlayerController.    

So in order to stop spawning the obstacles, I checked for when the game is over inside of my SpawnObstacle() method.   

```c#
private PlayerController playerControllerScript;

void Start()
{
  InvokeRepeating("SpawnObstacle", startDelay, repeatRate);
  playerControllerScript = GameObject.Find("Player").GetComponent<PlayerController>();
}

void SpqwnObstacle()
{
  if (playerControllerScript.game == false)
  {
    Instantiate(obstaclePrefab, sqawnPos, obstaclePrefab.transform.rotation);
  }
}
```

