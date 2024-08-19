---
title: "Data Persistence between Sessions"
date: 2024-08-19 15:39:00 +09:00
categories: [Unity Learn, Conceptual Learning]
tags:
  [
    Data Persistence
  ]
---

__*The written content was created by referencing materials from the Unity Learn website.*__ 

[Go To Previous Post](https://yehyuncho.github.io/posts/Data-Persistence-between-Scenes/)

In this post, we're going to write the color selected by the user to a file. Then, we're going to configure the MainManager to check whether that file exists when the application starts. If the file does exist, then the MainManager will read the color stored to it and set the color picker to that color.

## 1. How can data persist between sessions?
*****

For data to persist between sessions, it needs to be stored in some way. In our case, we'll need to convert the color our user selects into a format that can be stored and then read when they load the application again. 

**Serialization**   
The process of converting complex data into a format in which it can be stored. 

**Deserialization**   
When you're ready to access the data again, the process of converting is back.

![Alt text](/assets/img/posts/Unity/datapersist.jpg)

There are different formats that you can use to store data, depending on what the data is and what you want to do with it. In this case, we're going to use the **JSON** format.

## 2. What is JSON?
*****

JSON is a text format used to store data and exchange it between platforms. It was first developed for the web, and its full name is **J**ava**S**cript **O**bject **N**otation. It's based on JavaScript but is language independent - we can use it whether we're writing C# code or using any other programming language.

The JSON format stores data in the form of a **key:value** pair. The key is a string, and the value can be :   
- a number   
- a string   
- a Boolean (true/false)   
- an array of values
- Another JSON object

### How does this format work?

The following JSON string encodes an object storing basic information about a person : 

```javascript
{
  "name" : "Dayomi",
  "age" : 24, 
  "friend" : ["Denise", "YeHyun"]
  "skill" : {
    "Language" : "Java",
    "Web" : "Spring",
    "App" : "Android Studio"
  }
}
```

Les's break down the example :   
- Each object is surrounded by curly braces(**{}**).   
- Each entry is in the form of a key:value pair, seperated by a comma.   
- The entry "friend" is an array of strings - array values are listed between square brakets(**[]**)   
- The value associated with the entry "skill" is between curly braces too - this is because it is another JSON object.

## 3. How JSON works for both serializing and deserializing data.
*****

JSON was developed to exchange data between very different systems, which makes it a good fit for saving data in our application. Unity has a helper class called **JsonUtility**, which allows we to take a serializable class and transfrom it into its JSON representation. 

### Serializing Data

Consider the following DayomiData class : 

```c#
[Serializable]
public class DayomiData
{
  public int githubContribution;
  public string[] usedLanguage;
  public string projectName;
  public Vector3 position;
}
```
Imagine that you want to pass this class to JsonUtility with the following values :

```c#
DayomiData herData = new DayomiData();
herData.githubContribution = 400;
herData.projectName = "Survive from programming";
herData.position = new Vector3(99, 12, 4);
```

Calling ```string json = JsonUtility.ToJson(herData);``` will result in the following JSON string : 

```javascript
{
  "githubContribution" : 400,
  "projectName" : "Survive from programming",
  "position" : {
    "x" : 99,
    "y" : 12, 
    "z" : 4 }
}
```

Note that since **position** is a vector3, it is encoded as a new JSON object with three keys : **x**, **y** and **z**. This is because the Vector3 class(extremely simplified) is : 

```c#
[Serializable]
public class Vector3
{
  public float x;
  public float y;
  public float z;
}
```

### Deserializing Data

JsonUtiliy also has a method that does the inverse of the process we've just seen: ```FromJson<T>```.

This method will that a string containing some JSON data and create an instance of the object with its fields filled in. It uses a template argument - as type of that data is not stored in the JSON file. Unity needs the template argument to read the right value in the right field.

To extend the example, 

```c#
DayomiData herData = JsonUtility.FromJson<DayomiData>(json);
```

this code will fill ```herData``` with the values in the JSON string.

### Limitations of JsonUtility

JsonUtility doesn't work on primitive types, arrays, lists, or dictionaries.

As we might expect, JsonUtility only works on the **Serializable type** - MonoVehaviour or the other classes/structs you write onto which you can add the ```[Serializable]``` attribute. If we try to save a class that contains multiple pieces of data and one of them won't save, it's probably because it isn't serializable. 

For example, if we convert a class to JSON that has a dictionary in the middle, the dictionary won't be saved because it's not serializable. If something appears not to be saving properly in a class, check to make sure that it's a serializble type.

*****

To save and load the user's last chosen color, we'll need three things in the MaiinManager class :   
- A **SaveData** class that stores the color   
- A **Save method** that transforms that class into JSON format and writes it to a file   
- A **Load method** that transforms the data from the JSON file back into the SaveData class   

## 4. Add a SaveData class
*****

Let's start with the SaveData class :    
Add the following code at the end of the MainManager class (within the scope of its closing brace)

```c#
[System.Serializable]
class SaveData
{
  public Color TeamColor;
}
```

Also we need to add a new namespace to the script.

```c#
using System.IO;
```

### Review the new code

SaveData is a simple class, which only contains the color that the user selects. And we have added a ```[System.Serializable]``` attribute above it. This line is required for JsonUtility - it will only transform things to JSON if they are tagged as Serializable.

Why are we creating a class and not giving the MainManager Instance directly to the JsonUtility?   
Well, most of the time we won't save everything inside our classes. It's good practice and more efficient to use a small class that only contains the specific data that you want to save.

## 5. Add a SaveColor method
*****

Let's add the SaveColor method after the SaveData class : 

```c#
public void SaveColor()
{
  SaveData data = new SaveData();
  data.TeamColor = TeamColor;

  string json = JsonUtility.ToJson(data);

  File.WrittenText(Application.persistentDataPath + "/savefile.json", json);
}
```

### Review the new code

First, we created a new instance of the save data and filled its team color class member with the TeamColor variable saved in the MainManager.

```c#
SaveData data = new SaveData();
data.TeamColor = TeamColor;
```

And we transformed that instance to JSON with ```JsonUtility.ToJson```.

```c#
string json = JsonUtility.ToJson(data);
```

Finally, we used the special method ```File.WriteAllText``` to write a string to a file.

```c#
File.WrittenText(Application.persistentDataPath + "/savefile.json", json);
```

The first parameter is the path to the file. We've used a Unity method called ```Application.persistentDataPath``` that will give us a folder where we can save data that will survive between application reinstall or update and append to it the filename savefile.json.

## 6. Add a LoadColor method
*****

After the SaveColor method, add the following code.

```c#
public void LoadColor()
{
  string path = Application.persistentDataPath + "/savefile.json";
  if (File.Exists(path))
  {
    string json = File.ReadAllText(path);
    SaveData data = JsonUtility.FromJson<SaveData>(json);

    TeamColor = data.TeamColor;
  }
}
```

### Review the new code

```c#
string path = Application.persistentDataPath + "/savefile.json";
```

This method is a reversal of the SaveColor method.

Next code uses the method ```File.Exists``` to check if a .json file exists. If it doesn't, then nothing has been saved, so no further action is needed. If the file does exist, then the method will read its content wilt ```FileReadAllText```.

```c#
if (File.Exists(path))
{
  string json = File.ReadAllText(path);
  ...
}
```

And this code will then give the resulting text to ```JsonUtility.FromJson``` to transform it back into a SaveData instance.

```c#
SaveData data = JsonUtility.FromJson<SaveData>(json);
```

Finally, it will set the TeamColor to the color saced in that SaveData.

```c#
TeamColor = data.TeamColor;
```

This is our new code : 

```c#
[System.Serializable]
class SaveData
{
  public Color TeamColor;
}

public void SaveColor()
{
  SaveData data = new SaveData();
  data.TeamColor = TeamColor;

  string json = JsonUtility.ToJson(data);

  File.WrittenText(Application.persistentDataPath + "/savefile.json", json);
}

public void LoadColor()
{
  string path = Application.persistentDataPath + "/savefile.json";
  if (File.Exists(path))
  {
    string json = File.ReadAllText(path);
    SaveData data = JsonUtility.FromJson<SaveData>(json);

    TeamColor = data.TeamColor;
  }
}
```

## 7. Load and save the color in the application
*****

We're nearly finished, but first we need to load the saved color (if one exists) when the application starts and save the color on exit. Let's implement that now!

- At the end of the Awake method in MainManager.cs (which we should still have open), call the ```LoadColor``` method.

```c#
private void Awake()
{
  ...
  LoadColor();
}
```

- At the end of the Start method in MenuUIHandler.cs, add the following code : 

```c#
ColorPicker.SelectColor(MainManager.Instance.TeamColor);
```

This line will pre-select the saved color in the MainManager (if there is one) when the menu screen is launched.

- At the beginning of the Exit method, add the following code : 

```c# 
MainManager.Instance.SaveColor();
```

This line will save the user's last selected color when the application exits.

## 8. Add testing functionality
*****

Add some quick testing functionality to the MenuUIHandler so that you can save and load the color from the application instantly : 

```c#
public void SaveColorClicked()
{
  MainManager.Instance.SaveColor();
}

public void LoadColorClicked()
{
  MainManager.Instance.LoadColor();
  ColorPicker.SelectColor(MainManager.Instance.TeamColor);
}
```