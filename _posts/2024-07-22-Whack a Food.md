---
title: "[Challenge 5] Whack-a-Food"
date: 2024-07-22 12:12:00 +09:00
categories: [Unity]
tags:
  [
    Unity, Game
  ]
---

## 0. The difficulty buttons look messy

* Center the text on the buttons horizontally and vertically

![Alt text](/assets/img/posts/Unity/OrganizeText.png)

## 1. The food is being destroyed too soon

* The food should only be destroyed when the player clicks on it, not when the mouse touches it

> Before TargetX.cs

```c#
private void OnMouseEnter()
{
  ...           
}
```

> After TargetX.cs

```c#
private void OnMouseDown()
{
  ...           
}
```

## 2. The Score is being replaced by the word “score”

* It should always say, “Score: __“ with the value displayed after “Score:”

> Before GameManagerX.cs

```c#
public void UpdateScore(int scoreToAdd)
{
  score += scoreToAdd;
  scoreText.text = "score";
}
```

> After GameManagerX.cs

```c#
public void UpdateScore(int scoreToAdd)
{
  score += scoreToAdd;
  scoreText.text = "Score : " + score;
}
```

## 3. When you lose, there’s no way to Restart

* Make the Restart button appear on the game over screen

> Before GameManagerX.cs

```c#
public void GameOver()
{
  gameOverText.gameObject.SetActive(true);
  restartButton.gameObject.SetActive(false);
  isGameActive = false;
}
```

> After GameManagerX.cs

```c#
public void GameOver()
{
  gameOverText.gameObject.SetActive(true);
  restartButton.gameObject.SetActive(true);
  isGameActive = false;
}
```

## 4. The difficulty buttons don’t change the difficulty

* The spawnRate is always way too fast. When you click Easy, the spawnRate should be slower - if you click Hard, the spawnRate should be faster.

> Before GameManagerX.cs

```c#
public void StartGame()
{
  spawnRate /= 5;
  ...
}
```

> After GameManagerX.cs

```c#
public void StartGame(int difficulty)
{
  spawnRate /= difficulty;
  ...
}
```

> Before DifficultyButtonX.cs

```c#
void SetDifficulty()
{
  Debug.Log(button.gameObject.name + " was clicked");
  gameManagerX.StartGame();
}
```

> After DifficultyButtonX.cs

```c#
void SetDifficulty()
{
  Debug.Log(button.gameObject.name + " was clicked");
  gameManagerX.StartGame(difficulty);
}
```

## 5. Bonus: The game can go on forever

* Add a “Time: __” display that counts down from 60 in whole numbers (i.e. 59, 58, 57, etc) and triggers the game over sequence when it reaches 0.

First, Make new 'Time' text.   

![Alt text](/assets/img/posts/Unity/NewTimeText.png)

Second, type c# code in GameManager Script like this.

```c#
...
public TextMeshProUGUI timeText;
private int time = 60;
private int second = 1;

public void StartGame(int difficulty)
{
  ...
  StartCoroutine(CountsDown());
  ...
}

IEnumerator CountsDown()
{
  while (isGameActive)
  {
    yield return new WaitForSeconds(second);

    if (isGameActive)
    {
      time -= second;
      timeText.text = "Time : " + time;

      if (time == 0)
      {
        GameOver();
      }
    }
  }
}
...
```