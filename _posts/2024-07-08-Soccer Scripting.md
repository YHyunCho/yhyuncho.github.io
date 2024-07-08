---
title: "[Challenge 4] Soccer Scripting"
date: 2024-07-08 12:05:00 +09:00
categories: [Unity]
tags:
  [
    Unity, Game
  ]
---

## 0. Fix error before we start challenge
![Alt text](/assets/img/posts/Unity/Chanllenge4Error.png)

> Original EnemyX.cs

```c#
void Update()
{
  enemyRb = GetComponent<Rigidbody>();
}
```

> Modified EnemyX. cs

```c#
void Update()
{
  enemyRb = GetComponent<Rigidbody>();
  playerGoal = GameObject.Find("Player");
}
```

## 1. Hitting an enemy sends it back towards you

* When you hit an enemy, it should send it away from the player

> Original PlayerControllerX.cs

```c#
private void OnCollisionEnter(Collision other)
{
  if (other.gameObject.CompareTag("Enemy"))
  {
    Rigidbody enemyRigidbody = other.gameObject.GetComponent<Rigidbody>();
    Vector3 awayFromPlayer =  transform.position - other.gameObject.transform.position; 
     ...
  }
}
```

> Modified Script

```c#
private void OnCollisionEnter(Collision other)
{
  if (other.gameObject.CompareTag("Enemy"))
  {
    Rigidbody enemyRigidbody = other.gameObject.GetComponent<Rigidbody>();
    Vector3 awayFromPlayer =  other.gameObject.transform.position - transform.position; 
     ...
  }
}
```

## 2. A new wave spawns when the player gets a powerup

* A new wave should spawn when all enemy balls have been removed

> Original SpawnManagerX.cs

```c#
void Update()
{
  enemyCount = GameObject.FindGameObjectsWithTag("Powerup").Length;
  ...
}
```

> Modified Script

```c#
void Update()
{
  enemyCount = GameObject.FindGameObjectsWithTag("Enemy").Length;
  ...
}
```

## 3. The powerup never goes away

* The powerup should only last for a certain duration, then disappear

> Original PlayerControllerX.cs

```c#
private void OnTriggerEnter(Collider other)
{
  if (other.gameObject.CompareTag("Powerup"))
  {
    Destroy(other.gameObject);
    hasPowerup = true;
    powerupIndicator.SetActive(true);
  }
}
```

> Modified Script

```c#
private void OnTriggerEnter(Collider other)
{
  if (other.gameObject.CompareTag("Powerup"))
  {
    Destroy(other.gameObject);
    hasPowerup = true;
    powerupIndicator.SetActive(true);
    StartCoroutine(PowerupCooldown());
  }
}
```

## 4. 2 enemies are spawned in every wave

* One enemy should be spawned in wave 1, two in wave 2, three in wave 3, etc

> Original SpawnManagerX.cs

```c#
void SpawnEnemyWave(int enemiesToSpawn)
{
  ...
  // Spawn number of enemy balls based on wave number
  for (int i = 0; i < 2; i++)
  {
    Instantiate(enemyPrefab, GenerateSpawnPosition(), enemyPrefab.transform.rotation);
  }
  ...
}
```

> Modified Script

```c#
void SpawnEnemyWave(int enemiesToSpawn)
{
  ...
  // Spawn number of enemy balls based on wave number
  for (int i = 0; i < enemiesToSpawn; i++)
  {
    Instantiate(enemyPrefab, GenerateSpawnPosition(), enemyPrefab.transform.rotation);
  }
  ...
}
```

## 5. The enemy balls are not moving anywhere

* The enemy balls should go towards the “Player Goal” object

> Original EnemyX.cs

```c#
public float speed;
```

> Modified Script

```c#
private float speed = 20.0f;
```

## 6. Bonus: The player needs a turbo boost

* The player should get a speed boost whenever the player presses spacebar - and a particle effect should appear when they use it 

> Original PlayerControllerX.cs

```c#
void Update()
{
  // Add force to player in direction of the focal point (and camera)
  float verticalInput = Input.GetAxis("Vertical");

  playerRb.AddForce(focalPoint.transform.forward * verticalInput * speed * Time.deltaTime); 

  // Set powerup indicator position to beneath player
  powerupIndicator.transform.position = transform.position + new Vector3(0, -0.6f, 0);
}
```

> Modified Script

```c#
void Update()
{
  // Add force to player in direction of the focal point (and camera)
  float verticalInput = Input.GetAxis("Vertical");

  if (Input.GetKey(KeyCode.Space))
  {
    speed = 800;
    smokeParticle.Play();
  } else
  {
    speed = 500;
    smokeParticle.Stop();
  }

  playerRb.AddForce(focalPoint.transform.forward * verticalInput * speed * Time.deltaTime); 

  // Set powerup indicator position to beneath player
  powerupIndicator.transform.position = transform.position + new Vector3(0, -0.6f, 0);
}
```

> Hierarchy
![Alt text](/assets/img/posts/Unity/challenge4Particle.png)

## 7. Bonus: The enemies never get more difficult

* The enemies’ speed should increase in speed by a small amount with every new wave

> Modified Enemy.cs

```c#
...
private SpawnManagerX smScript;

void Start()
{
  ...
  smScript = GameObject.Find("Spawn Manager").GetComponent<SpawnManagerX>();
}

void Update()
{
  // Set enemy direction towards player goal and move there
  Vector3 lookDirection = (playerGoal.transform.position - transform.position).normalized;
  enemyRb.AddForce(lookDirection * speed * Time.deltaTime);

  if (smScript.enemyCount == 0)
  {
    speed += 10.0f;
  }
}
```