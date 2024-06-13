---
title: Spawn Random Object and Collision Decision
date: 2024-06-07 11:54:00 +09:00
categories: [Unity]
tags:
  [
    Unity, Game
  ]
---

How to spawn random objects?
----------------------------
*****

First, we need to create empty GameObject into our project hierarchy.   
this is what we'll be using to manage all of the spawning of all of objects on the screen.   

And we also need to create an actual script to be able to spawn all the objects in random locations in our scene.  
After that, attach your new script to your new GameObject.

In script, I used an array to store all of my objects that I've created.

> SpawnManager.cs

```c#
public GameObject[] animalPrefabs;
```

Let's make random objects spawn from array.   
I already wrote the code to make the objects move when I press 'S'.   

```c#
public GameObject[] animalPrefabs;

void Update()
{
  SpawnRandomAnimal();
}

void SpawnRandomAnimal()
{
  if (Input.GetKeyDown(KeyCode.S))
  {
    int animalIndex = Random.Range(0, animalPrefabs.Length);
    Instantiate(animalPrefabs[animalIndex], new Vector3(0, 0, 20), animalPrefabs[animalIndex].transform.rotation);
  }
}
```

If you want to randomize the spawn location, you can use Random.Range() again.   
```c#
public GameObject[] animalPrefabs;

void Update()
{
  SpawnRandomAnimal();
}

void SpawnRandomAnimal()
{
  if (Input.GetKeyDown(KeyCode.S))
  {
    int animalIndex = Random.Range(0, animalPrefabs.Length);
    Vector3 spawnPos = new Vector3(Random.Range(-2, 20), 0, 20);

    Instantiate(animalPrefabs[animalIndex], spawnPos, animalPrefabs[animalIndex].transform.rotation);
  }
}
```

How to make the objects spawn at timed intervals
--------------------------------------------------
*****

I don't want to press anymore, so I'm going to make my objects spawn on a timer.   
I'm going to use a new method called ```InvokeRepeating()```

If I put it inside of the Start() method, it will actually take a method that I want to call.

```c#
public GameObject[] animalPrefabs;
private float startDelay = 2;
private float spawnInterval = 1.5f;

void Start()
{
  InvokeRepeating("SpawnRandomAnimal", startDelay, spawnInterval);
}

void Update(){}

void SpawnRandomAnimal()
{
  if (Input.GetKeyDown(KeyCode.S))
  {
    int animalIndex = Random.Range(0, animalPrefabs.Length);
    Vector3 spawnPos = new Vector3(Random.Range(-2, 20), 0, 20);

    Instantiate(animalPrefabs[animalIndex], spawnPos, animalPrefabs[animalIndex].transform.rotation);
  }
}
```


Collision Decisions
--------------------
*****

You shoud do something before you write the code.    
1. Add a Box Collider component to every object you want to destroy after a collision.
2. Click Edit Collider, then drag the collider handles to encompass the object.
3. Check the “Is Trigger” checkbox.

If you're done, create new script and attach to your objects you want destroy.

And now I'm going to override a function that already exists in MonoBehaviour class.   
it's called ```OnTriggerEnter()```   

```c#
private void OnTriggerEnter(Collider other)
```

We can see when it auto completed that it already made this method private, so that it's only available within this class.   
And then, it also automatically gave us a parameter that we have to fill.    
it's because of the way that this method is setup, it actually is already pulling all of the information in from MonoBehaviour.   

So what happens is, in Unity, whenever one of the Colliders collides with one another it'll actually know, from this script, that they've collided and it should do something.   

Anyway I'm going to type in Destroy().

```c#
private void OnTriggerEnter(Collider other)
    {
        Destroy(gameObject);
        Destroy(other.gameObject);
    }
```

Now in your game, when your object collided with oher object, it desroyed itself.