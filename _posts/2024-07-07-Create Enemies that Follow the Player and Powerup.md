---
title: "[Prototype 4] Create Enemies that Follow the Player and Powerup"
date: 2024-07-07 16:15:00 +09:00
categories: [Unity Learn, Practical Programming]
tags:
  [
    Unity, Game
  ]
---

How to make enemy follow the player
------------------------------------
*****

I created a new game object, Enemy. And I wanted to make it that can actually chase the Player around the Scene.   
So the first thing I did, of course created a new c# script.   

In script, I made some public and private to the things I wanted to do.

> Enemy.cs

```c#
public float speed = 3.0f;
public GameObject player;
private Rigidbody enemyRb;

void Start()
{
  enemyRb = GetComponent<Rigidbody>();
}
```

And I needed to apply a "Force" in the direction of the Player.   
How I added this Force was I took the Player's position, like I got the transform to get their current position.   
It gives me the Vector3 coordinates that I need to go towards at all time.

In this case, I apply the speed so that way I know how fast the enemy shoud be moving.   

> Enemy.cs

```c#
void Update()
{
  Vector3 lookDirection = player.transform.position - transform.position;

  enemyRb.AddForce(lookDirection * speed);
}
```

After I saved this, I saw the enemy was very aggressively rolling towards the Player.   
The reason that happened is because as the Player got farther away from the enemy, the length of that actual vector, the distance between those two positions becomes larger.
So what it actually did, was it tried to multiply over a very large amount of distance. So it actually increased in force over time.   

Fortunately we can actually use some code to prevent that form happening.   
And that is ```.normalized```   
After this Vector3, this normalize the magnitude of this vector, so it doesn't matter how close or how far the enemy is from the Player. It's always coming at the same speed.

```c#
void Update()
{
  Vector3 lookDirection = (player.transform.position - transform.position).normalized;

  enemyRb.AddForce(lookDirection * speed);
}
```

Plus, I created a spawn manager for the enemy to randomly generate spawn positions.

> SpawnManager.cs

```c#
public GameObject enemyPrefab;
private float spawnRange = 9;

void Start()
{
  Instantiate(powerupPrefab, GenerateSpawnPosition(), powerupPrefab.transform.rotation);
}

private Vector3 GenerateSpawnPosition()
{
  float spawnPosX = Random.Range(-spawnRange, spawnRange);
  float spawnPosZ = Random.Range(-spawnRange, spawnRange);

  Vector3 randomPos = new Vector3(spawnPosX, 0, spawnPosZ);

  return randomPos;
}
```

How to add Powerup
------------------
*****

Most of games have a "Powerup" to give the Player some temporary superpowers. Let's add it!   
I created new Game Object named "Powerup" and added a Box Collider in the Powerup Inspector.   
And I checked "Is Trigger" box. Also I added a Tag to the Powerup.   
Make sure that Game Object is ans original Prefab.   

![Alt text](/assets/img/posts/Unity/Powerup.png)

The first step I took to getting the Powerup to work, was I made it disappear when the Player hits it.   
In PlayerController Script, I added another method for OnTriggerEnter().

> PlayerController.cs

```c#
public bool hasPowerup; 

private void OnTriggerEnter(Collider other)
{
  if (other.CompareTag("Powerup"))
  {
    hasPowerup = true;

    Destroy(other.gameObject);
  }
}
```

How to apply extra knockback with powerup
-----------------------------------------
*****

Okay In PlayerController Script, I created a new method underneath ```OntriggerEnter()```.   
It's ```OnCollisionEnter()``` method.   

Now the reason we want to use ```OnCollisionEnter()``` vs ```OnTriggerEnter()``` is because ```OnTriggerEnter()``` is useful when we are just trying to understand triggers between Colliders, but if we are trying to do something with Physics, we use ```OnCollisionEnter()``` instead.

Anyway in that method I used an if() statement to check condition.

> PlayerController.cs

```c#
private void OnCollisionEnter(Collision collision)
{
  if(collision.gameObject.CompareTag("Enemy") && hasPowerup)
  {

  }
}
```

And in the if() statement, I set Rigidbody for the enemy that way I can actually apply force to it.   
In this case I need the Rigidbody because I should calculate the direction that I'm trying to go away from the Player.   
So I used a Vector3 for that direction. And to calculate this, I took the enemy's current position and subtract it by the current position.   

> PlayerController.cs

```c#
private void OnCollisionEnter(Collision collision)
{
  if(collision.gameObject.CompareTag("Enemy") && hasPowerup)
  {
    Rigidbody enemyRigidbody = collision.gameObject.GetComponent<Rigidbody>();
    Vector3 awayFromPlayer = collision.gameObject.transform.position - transform.position;
  }
}
```

Through this code, now I know what direction the enemy's coming in and what direction I should send it back.   

The next step I did was I added a Force. The direction I send it to is in the direction of the Vector3 that I set up away from the Player. I multiplied it by 15.0f, I call it the "powerupStrength", and I made this an instant interaction so I actually used a ForceMode. And used Impulse so that it applies the force immediately. 

> PlayerController.cs

```c#
private float powerupStrength = 15.0f;

private void OnCollisionEnter(Collision collision)
{
  if(collision.gameObject.CompareTag("Enemy") && hasPowerup)
  {
    Rigidbody enemyRigidbody = collision.gameObject.GetComponent<Rigidbody>();
    Vector3 awayFromPlayer = collision.gameObject.transform.position - transform.position;

    enemyRigidbody.AddForce(awayFromPlayer * powerupStrength, ForceMode.Impulse);
  }
}
```

How to create countdown routine for powerup
-------------------------------------------
*****

The problem was the Powerup basically lasts forever.   
So I added a timer in, so that I could remove this Powerup at a certain time.   

In the Player Controller script, I created a little bit strange new method.   
It's called an ```IEnumerator()``` 

> PlayerController.cs

```c#
IEnumerator PowerupCountdownRoutine()
{
  
}
```

An ```IEnumerator()``` is something in programming and C# known as an interface. In this case we don't have to understand to go into how it works or what is does, but it is very helpful to be able to do certain things like enable a countdown timer outside of the ```Update()``` loop.

The way we're able to use this countdown timer outside of the ```Update()``` loop is through use of something called ```Coroutines()``` that is able to run with a function called ```WaitForSeconds()```.

To be able to work, I used a keyword called "yield", and this enable me to run this timer.
And I put 7 just as a random value.

> PlayerController.cs

```c#
IEnumerator PowerupCountdownRoutine()
{
  yield return new WaitForSeconds(7);
  hasPowerup = false;
  powerupIndicator.gameObject.SetActive(false);
}
```

And then I used another method whick I put inside of the ```OnTriggerEnter()``` in the ```if()``` statement.   
When the player's picked up the Powerup, It makes the ```Coroutine()``` start.

> PlayerController.cs

```c#
public bool hasPowerup; 

private void OnTriggerEnter(Collider other)
{
  if (other.CompareTag("Powerup"))
  {
    hasPowerup = true;

    Destroy(other.gameObject);

    StartCoroutine(PowerupCountdownRoutine());
  }
}
```

So now when my player picks up the powerup evemy goes away, and eventually has powerup should turn off!