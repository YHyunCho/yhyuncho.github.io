---
title: "Data Persistence between Scenes"
date: 2024-08-19 11:40:00 +09:00
categories: [Unity Learn, Conceptual Learning]
tags:
  [
    Data Persistence
  ]
---

__*The written content was created by referencing materials from the Unity Learn website.*__   

<details>
<summary>What is data persistence?</summary>
<div markdown="1">
Data persistence means making data last longer than the process used to create that data.

Some examples of data persistence are : 

- The specific player icon you select in a one-off multiplayer game (for example, a quiz party game)   
- Your name input at the start of an application and then displayed throughout the session (for example, in a polling tool)
- Your progress in ongoing long-form games (for example, an RPG game on a console)
- Your work in a word processing application

</div>
</details>

## 0. Review the brief
*****

The brief for the resource management simulation project includes making it possible to select colors in the initial menu (Menu scene) and apply them to the forklifts in the warehouse (Main scene).

At the moment, the buttons exist, because we've provided a custom script for this. Now let's implement date persistence between scenes by making the selected color data available in the Main scece.

To achieve this, we will use : 

- **DonDestroyOnLoad** :   
A method in Unity that marks a GameObject to be saved in memory even when loading or unloading a new scene.

- **Static classes** and **class members** :   
Static class members can be accessed from anywhere without having to reference a specific object. We may already have used some of these, such as ```Time.deltaTime``` or ```Vector3.forward```. Those aren't a specific time object or a specific vector3 - the format for static class members is ```ClassName.memberName```.

## 1. Review the code of Main Manager Script
*****

> MainManager.cs

```c#
public class MainManager : MonoBehaviour
{
    public static MainManager Instance; 

    public Color TeamColor; 
    
    private void Awake()
    {
        if (Instance != null)
        {
            Destroy(gameObject);
            return;
        }

        Instance = this;
        DontDestroyOnLoad(gameObject);
    }
}
```

### Class member declaration

```c#
public static MainManager Instance;
```

This means that the values stored in this class member will be shared by all the instances of that class.

For example, if there were then instances of MainManager in my scene, they would all share the same value stored in Instance. If any of those 10 MainManagers changed the value in it, it sould also be changed for the other nine.

### Awake method code

Awake method is called as soon as the object is created. Before we check the ```if()``` statement, let's look at the other code first.

```c#
Instance = this;
DontDestroyOnLoad(gameObject);
```

This first line of code stores **"this"** in the class member Instance - the current instance of MainManager. We can now call ```MainManager.Instance``` from any other script (for example, Unity script) and get a link to that specific instance of it. We don't need to have a reference to it, like we do when you assign GameObjects to script properties in the Inspector.

The second line of code marks the MainManager GameObject attached to this script not to be destroyed when the scene changes.

This code enables us to access the MainManager object from any other script. BUT, when you reload the scece there are going to be two MainManager GameObjects in our Hierarchy. And this is why we need the ```if()``` statement.

```c#
if (Instance != null)
{
  Destroy(gameObject);
  return;
}
```

The very first time that we launch the Menu scene, no MainManager will have filled the Instance variable. This means it will be null, so the condition will not be met, and the script will continue as you previously wrote it.

However, if we load the Menu scene again later, there will be already be one MainManager in existence, so Instance will not be null. In this case, the condition is met : the extra MainManager is destroyed and the script exits there.

> This pattern is called a **singleton**. We use it to ensure that only **single instance** of the MainManager can ever exist, so it acts as a cental point of access.

## 2. Store and pass the selected color

There's another class member in the Awake method : 

> MainManager.cs

```c#
public Color TeamColor;
```

Let's open the MenuUIHandler.cs script in our IDE. In this script, there's a method called ```NewColorSelected```, which is called when the user picks a new color.

> MenuUIHandler.cs

```c#
public void NewColorSelected(Color color)
{
  MainManager.Instance.TeamColor = color;
}
```

The color that the user selects is now stored in MainManager, so you can access the TeamColor variable from the Main scene to retrieve it.

Now we just need to appl it to the forklift in the warehouse.

> Unit.cs

```c#
private void Start()
{
  if (MainManager.Instance != null)
  {
    SetColor(MainManager.Instance.TeamColor);
  }
}
```

```SetColor``` is a method 'Unity learn' has written for us, to set a color to the unit(the forklift)

The reason we typed ```if (MainManager.Instance != null)``` is to prevent an error. If we want to test quickly then you may want to start the Main scene in the Editor directly, without passing by the Menu scene. In that case, the ```MainManager``` won't exist, and ```MainManager.Instance``` will be null. An error in case the programmer runs the Main scene by itself without pssing by the menu, making it easier to test things quickly.