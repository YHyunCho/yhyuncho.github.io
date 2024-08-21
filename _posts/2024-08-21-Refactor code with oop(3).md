---
title: "Refactor Code with OOP(3) : Encapsulation"
date: 2024-08-21 13:54:00 +09:00
categories: [Unity Learn, Conceptual Learning]
tags:
  [
    Refactoring, Encapsulation
  ]
---

__*The written content was created by referencing materials from the Unity Learn website.*__  

## What is Encapsulation?
*****

A major theme of encapsulation is safety in code - in other words, the process of ensuring that code is only used as it is intended to be used, and the values and data we are manipulating can't be corrupted. In encapsulated code, other programmers can't easily change the values of variables of the properties of objects. It's impossible to account for all of the different ways that other scripts might access our code, so it's far better to encapsulate what we've created so it can only perform as intended.

### SerializeField

Making a variable public exposes it to the Inspector, but it also makes it available to be changed in any other method of your application. Fortunately, there is a way to make values available in the Inspector, otherwise known as **serialized**, without exposing them to other code. This gives us the freedom of modifying variables in the inspector, while still keeping them safe.

In fect, the access modifier of a variable (public, private), actually doesn't control serialization - but public variables are serialized by default. We can easily expose our private variables in the Inspector by adding the ```[SerializeField]``` tag.

## Create a Property with a Getter and a Setter
*****

Imagine we added the following public variable in MainManager.cs : 

```c#
public static MainManager Instance;
```

We can't make this variable private because it would cause errors in the other scripts that need access to it. But we need some way of preventing some misuse, while still granting public access to the variable.

Essentially, what we want to do is make the variable "read-only", allowing other classes to **get** the value, but not **set** the value. In order to do this, we can use the C# get and set methods, which will control how and when our variable can be used.

```c#
public static MainManager Instance { get; }
```

As soon as you add a get or set accessor to variable, that variable becomes a **Property** - a special kind of variable that provides access to internal data through these specialized methods.

Without a set accessor, this property is strictly read-only - its value cannot be set anywhere. Because of that, there is now an error on that mischievous line of code we added earilier.

Since the property is strictly read-only, it can't even be set in its own class, which is causing an error lower down in MainManager.cs on the line ```Instance = this;```. To fix this, add a private setter to the property, which should resolve that error.

```c#
public static MainManager Instance { get; private set; }
```

With this code, we can now **set** the property's calue from within the class, but only **get** it from outside the class. It's encapsulated to only accept modifications from its own class, safe from misuse and corruption from the outside world!

> The getter and setter we added above represent the most basic form of encapsulation, where we are simply getting or setting the calue - nothing fancy. This simple implementation is called an **auto-implemented property**   

## Make a Property with a Backing Field
*****

To perform validations or calculations within a getter or setter, we need a **backing field** for our property : a private field (variable) that stores the data exposed by the public property.

In ResourcePile.cs, add a private member variable (backing field), using the same name as ProductionSpeed, but store the value in this new private field instead :

```c#
private float m_ProductionSpeed = 0.5f;
public float ProductionSpeed;
```

With the backing field declared, we can now manually add the get and set functions, which retrieve or assign the backing field when someone accesses the public property.

```c#
private float m_ProductionSpeed = 0.5f;
public float ProductionSpeed
{
  get { return m_ProductionSpeed; }
  set { m_ProductionSpeed = value; }
}
```

Since these are not going to be simple get and set methods, it's not possible to use the ```{ get; set; }``` shorthand we used with the auto-implemented property earlier.

Instead, we are doing it manually. When someone gets the ProductionSpeed property, it will return the backing field. When someone sets the ProductionSpeed property, the backing field is set to the value they've entered.

Now, whenever someone tries to get or set the public property, it accesses the backing field encapsulated in the class throught the getter and setter!

## Implement Setter Validation to Prevent Nagetive Numbers
*****

Finally, with everything configured, we can now implement a validation check within the set method, ensuring the Production Speed is alwaus a positive number. 

Within the set method, add an if-else statement that displays a warning message if the value is nagative, but successfully sets the value if it's positive.

```c#
private float m_ProductionSpeed = 0.5f;
public float ProductionSpeed
{
  get { return m_ProductionSpeed; }
  set 
  { 
    if (value < 0.0f)
    {
      Debug.LogError("You can't set a negative production speed");
    }
    else
    {
      m_ProductionSpeed = value;
    }
  }
}
```