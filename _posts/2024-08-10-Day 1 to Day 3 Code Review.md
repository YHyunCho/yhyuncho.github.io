---
title: "Day 1 to Day 3 Code Review"
date: 2024-08-10 20:20:00 +09:00
categories: [Unity Project, Flyin' Fish 'n' Fowl]
tags:
  [
    Unity, Game Programming, Project
  ]
---

Hey everyone!

Wow, it’s been two months since I started learning Unity. Time flies, doesn’t it?

I had a great opportunity to work on a small Unity project through learn.unity.com. Previously, I tried creating an FPS game called 'Kill Zombie' (you can check out the code on my GitHub if you're interested). However, as you might expect, programming of FPS game can be quite challenging for beginners, so unfortunately, that project is still ongoing😞

I was feeling quite stressed due to the 'Kill Zombie' project, but since this game is very simple, working on it felt like a refreshing break.  

You can play my project using this link. Please note that it’s only playable on a computer.

[Go to play Flyin' Fish 'n' Fowl](https://play.unity.com/en/games/30480856-c2c6-417d-9c81-09af4b489ca0/flyin-fish-n-fowl)

![Alt text](/assets/img/posts/UnityProject/FlyingFish/썸네일.png)

Now, let me explain how I programmed it.

## Aug 2, 2024
*****

### 1) Downloaded and Checked the Provided Assets

>The scene contains a simple UI counter, a box, and several spheres placed above it. All of the spheres have Rigidbody components attached. When you play the scene, the spheres drop, and the UI counter updates to show how many of them landed in the box.   
>
>A script named Counter.cs is applied to the box mesh, which has a Box Collider set as a trigger. This trigger is scaled to the exact size of the interior of the box. Counter.cs has a public text variable for the UI counter.

![Alt text](/assets/img/posts/UnityProject/FlyingFish/CountingExample.gif)

> Counter.cs

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class Counter : MonoBehaviour
{
    public Text CounterText;

    private int Count = 0;

    private void Start()
    {
        Count = 0;
    }

    private void OnTriggerEnter(Collider other)
    {
        Count += 1;
        CounterText.text = "Count : " + Count;
    }
}
```

### 2) Create a Design Document

<details>
<summary>Mission</summary>
<div markdown="1">

The project provided to you is very basic, but there are a lot of opportunities to turn it into a variety of interesting prototypes! Take some time to think about what kind of application you’d like to make and write up a design document describing the application’s functionality. This concept phase is part of the overall exercise, so spend as much time as you need to come up with something interesting. 

There are only two requirements for your new prototype:
* The prototype must still count something.
* There must be a UI element displaying that count.

Everything else is up to you! Here are a few suggestions for inspiration: 
* A hoops style game: Toss a basketball into the net as many times as possible in the allotted time.
* A pachinko game: Drop several small balls down a pegboard and have each section at the bottom have a different point value.
* A stock counter: Different objects get sorted into their own spaces and then the total count is displayed for each.
* A neighborhood planner: Items found in a neighborhood, such as streets, sidewalks, houses, and streetlights, are contained in drag-and-drop style UI boxes. Users can drag out the items and plan their neighborhood design, and the UI tracks how many of each item they’ll need to build it in the real world.

</div>
</details>

*****

To be honest, I had no idea what to do at first. The song "It’s Raining Men" kept playing in my head LOL So, I called my friend for her opinion, and she suggested a game concept involving squirrels dropping nuts. I thought it was a great idea, so I started designing my game based on her suggestion.

Thanks to SeongEun😘

It’s quite a simple concept, right?

![Alt text](/assets/img/posts/UnityProject/FlyingFish/DesignDocument.jpg)

### 3) Make the Player Movement

The first thing I did was implement the box movement. I created a new script, PlayerController.cs, and used Rigidbody because I needed physics for the box. I wanted the box to spill over when it was full, and using Rigidbody allowed me to detect collisions between the spheres and the inside of the box.

> PlayerController.cs

```c#
public class PlayerController : MonoBehaviour
{
    private Rigidbody playerRb;
    public float speed;

    void Start()
    {
        playerRb = GetComponent<Rigidbody>();   
    }

    void Update()
    {
        float horizontalInput = Input.GetAxis("Horizontal");

        playerRb.AddForce(Vector3.right * horizontalInput * speed);
    }
}
```

However, I encountered a problem: when the box caught a dropped sphere, its weight increased, causing the box’s movement to slow down. I considered fixing this by adjusting gravity calculations, but I was concerned about memory usage and didn’t think it was a crucial feature for this game. So, I decided to use ```transform.Translate()``` instead of Rigidbody for the box’s movement. 

> PlayerController.cs

```c#
public class PlayerController : MonoBehaviour
{
    public float speed;

    void Update()
    {
        float horizontalInput = Input.GetAxis("Horizontal");

        transform.Translate(Vector3.forward * horizontalInput * speed * Time.deltaTime);
    }
}
```

### 4) Create a Game Manager and Destroy the Sphere that Collides with the Box

A Game Manager is essential when creating a game because it manages all of the game's resources. However, at this stage, I haven't implemented much functionality yet. For now, in the GameManager script, I've included the same code as in the Counter.cs script provided earlier.

```c#
public class GameManager : MonoBehaviour
{
    public Text CounterText;
    private int Count = 0;

    private void Start()
    {
        Count = 0;
    }

    public void UpdateScore()
    {
        Count += 1;
        CounterText.text = "Count : " + Count;
    }
}
```

I also used the ```OnTriggerEnter()``` method in the PlayerController script to destroy the sphere when it collides with the box. The reason I chose ```OnTriggerEnter()``` instead of ```OnCollisionEnter()``` is that I don't need any physics calculations for this interaction.

After destroying the sphere, the score should be updated because the box caught the sphere, right? So I called the GameManager script in the PlayerController script and used the ```UpdateScore()``` method of GameManager.cs

```c#
public class PlayerController : MonoBehaviour
{
    private GameManager gameManager;
    public float speed;

    void Start()
    {
        gameManager = GameObject.Find("GameManager").GetComponent<GameManager>();
    }

    void Update()
    {
        float horizontalInput = Input.GetAxis("Horizontal");

        transform.Translate(Vector3.forward * horizontalInput * speed * Time.deltaTime);
    }

    private void OnTriggerEnter(Collider other)
    {
        gameManager.UpdateScore();
        Destroy(other.gameObject);
    }
}
```


## Aug 5, 2024
*****

### 1) Spawn the Dropped Objects at Random Places

In the scene you saw earlier, the ball was initially positioned at a specific location. However, I wanted the ball to spawn at random places to increase the challenge for the player. This was straightforward to implement. I created a new script, SpawnManager.cs, and also set up a new GameObject named Spawn Manager in the Hierarchy.

> SpawnManager.cs

```c#
public class SpawnManager : MonoBehaviour
{
    public GameObject dropPrefab;
    public float spawnRange = 19;
    private float startDelay = 3;
    private float spawnInterval = 2;

    void Start()
    {
        InvokeRepeating("SpawnObject", startDelay, spawnInterval);
    }
    private void SpawnObject()
    {
        Instantiate(dropPrefab, GenerateSpawnPosition(), dropPrefab.transform.rotation);
    }

    private Vector3 GenerateSpawnPosition()
    {
        float spawnPosZ = Random.Range(-spawnRange, spawnRange);

        Vector3 randomPos = new Vector3(0, 14, spawnPosZ);

        return randomPos;
    }
}
```

### 2) Destroy the Object Upon Collision with the Ground

I created a new tag for the ground and wrote a new script, FallingObjectController.cs. Here’s the initial code I wrote:

```c#
public class FallingObjectController : MonoBehaviour
{
    private void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            Destroy(gameObject);
        }
    }
}
```

I was frustrated because the OnCollisionEnter method wasn’t working for the objects that were spawned, meaning it wasn’t being triggered for prefabs when they were instantiated. However, it worked for objects that were already in the Hierarchy. Also instead of being destroyed, the objects seemed to disappear completely upon collision with the ground.

After several hours of searching Google, I found that other people had encountered the same issue, and many had not found a solution. I eventually came across a comment in the Unity forums suggesting the use of ```OnTriggerEnter()``` instead. I replaced ```OnCollisionEnter()``` with ```OnTriggerEnter()```, and surprisingly, it worked as intended.

```c#
public class FallingObjectController : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        if (other.gameObject.CompareTag("Ground"))
        {
            Destroy(gameObject);
        }
    }
}
```

And I still don't know why.

### 3) Asset Changes

At this point, the game allows me to spawn spheres at random positions, move a box left and right, and destroy spheres that collide with the ground or the box. These three features are central to the gameplay.

I started searching for assets, and literally **I hate this time**.

I needed to find free assets (~~because I'm poor~~), but most of the assets I wanted were NOT available for free. As a result, the game differs from my initial design document😇. I couldn't find assets for branches, squirrels and nuts, so I had to make some adjustments to the game design.

So yeah, here are the assets I ended up using.

> 3D Props

[3D model Pack Baskets](https://assetstore.unity.com/packages/3d/props/interior/3d-model-pack-baskets-157025) : Basket/Box(Apply)   
[RPG Food & Drinks Pack](https://assetstore.unity.com/packages/3d/props/food/rpg-food-drinks-pack-121067) : Fishes/Spheres ~~should've been nuts~~(Apply)   

> 3D Environments

[LoyPoly Water](https://assetstore.unity.com/packages/tools/particles-effects/lowpoly-water-107563) : Ocean(Apply)     
[Super Beach Pack](https://assetstore.unity.com/packages/3d/props/exterior/super-beach-pack-39084)  : Island(Apply)

> 3D Characters

[Living Birds](https://assetstore.unity.com/packages/3d/characters/animals/birds/living-birds-15649) : Birds ~~should've been squirrels~~(Not Yet)

> 2D GUI Tools

[Simple Heart Health System](https://assetstore.unity.com/packages/tools/gui/simple-heart-health-system-120676) : Player Lives(Not Yet)   
[Fantasy Wooden GUI : Free](https://assetstore.unity.com/packages/2d/gui/fantasy-wooden-gui-free-103811) : Title and GameOver etc(Not Yet)


## Aug 6, 2024
*****

### 1) Removed Spawn Manager and Added Random Bird Spawning

Since I already have a Game Manager, so technically I didn't need a separate Spawn Manager. I removed the Spawn Manager and integrated the bird spawning functionality into the GameManager script. 

My goal was to spawn birds on either the left or right side randomly. If a bird spawns on the right side, it needs to move to the left. I configured the regular bird to start on the left side, facing right. However, when a bird spawns on the right side, it rotates to move left.

Fortunately, the bird asset pack I'm using includes six different bird types, so I implemented random selection for spawning different kinds of birds.

```c#
public class GameManager : MonoBehaviour
{
  ...
    public GameObject[] birds;

    private Vector3[] birdPos = { new Vector3(13.3f, 22.62f, -13.49f), new Vector3(13.3f, 18.91f, 32.75f) };
    private Quaternion birdRotation;

    private int[] direction = { 0, 180 };
    private float birdSpawnInterval = 4;
  ...

    private void Start()
    {
      ...
        StartCoroutine(SpawnRandomBirds());
    }

    IEnumerator SpawnRandomBirds()
    {
        yield return new WaitForSeconds(birdSpawnInterval);

        int birdIndex = Random.Range(0, birds.Length);
        int birdDir = Random.Range(0, direction.Length);

        birdRotation = Quaternion.Euler(0, direction[birdDir], 0);

        Instantiate(birds[birdIndex], birdPos[birdDir], birdRotation);
    }
    ...
}
```

I just realized that I should have used a Dictionary instead of an array. It would have made things much easier😂

Anyway in the previous Spawn Manager, I used ```InvokeRepeating()```, but this time I switched to using coroutines. The reason for this change is that coroutines allow me to save the current state of the game, which is important for future updates. Although there’s nothing to save at the moment, I plan to add code like ```while (isGameActive)``` later on.

For example, if you use ```while (isGameActive)``` inside InvokeRepeating(), and ```bool isGameActive``` remains true, the loop will run indefinitely because ```InvokeRepeating()``` can't handle state changes effectively. Coroutines, on the other hand, can manage this situation more gracefully.

### 2) Give the bird a forward motion

I created a new Script for the bird called FlightOfBird. Very kindly, the bird asset includes all the necessary animations, so I utilized those directly. I also made the bird to move forward and to destroy the bird object when it leaves the screen.

```c#
public class FlightOfBird : MonoBehaviour
{
    private Animator birdAni;
    public float speed;

    void Start()
    {
        birdAni = GetComponent<Animator>();
        birdAni.SetBool("flying", true);
    }

    void Update()
    {
        transform.Translate(Vector3.forward * speed * Time.deltaTime);
        if (transform.position.z > 55 || transform.position.z < -33)
        {
            Destroy(gameObject);
        }
    }
}
```

### 3) Make the Fish Drop from the Bird

I needed the bird to drop fish, so I added the fish spawning functionality directly into the FlightOfBird script, not the Game Manager.

This is where using a coroutine instead of ```InvokeRepeating()``` becomes important. Since the fish's position needs to be relative to the bird's current position, it's crucial to save the bird's position every frame. Coroutines are ideal for this as they allow for continuous updates. 

```c#
public class FlightOfBird : MonoBehaviour
{
  ...
    public GameObject[] fishes;
    private GameManager gameManager;

    void Start()
    {
      ...
        gameManager = GameObject.Find("GameManager").GetComponent<GameManager>();
      ...
        StartCoroutine(SpawnRandomFishes());
    }

    IEnumerator SpawnRandomFishes()
    {
        yield return new WaitForSeconds(fishSpawnInterval);

        int fishIndex = Random.Range(0, fishes.Length);

        Vector3 fishPos = new Vector3(13.3f, transform.position.y, transform.position.z);

        Instantiate(fishes[fishIndex], fishPos, fishes[fishIndex].transform.rotation);
  }
}
```

I have two types of fish: fresh fish and leftover fish, which is why I used an array to manage them. 

In the next post, I’ll explain how to use that array for this game.