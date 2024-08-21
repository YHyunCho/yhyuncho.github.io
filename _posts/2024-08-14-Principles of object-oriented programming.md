---
title: "Principles of Object-Oriented Programming"
date: 2024-08-16 13:00:00 +09:00
categories: [Unity Learn, Conceptual Learning]
tags:
  [
    OOP
  ]
---

__*The written content was created by referencing materials from the Unity Learn website.*__   

*****

Object-oriented programming(OOP), is a programming pattern in which the methods and variables are related to one another are grouped together to form what is called an **object**(the "object" in OOP).

> Object named People

```c#
public class People
{
  // Variables
  enum State {Awake, Asleep};
  color hairColor;

  // Method
  void Run()
}
```

There are four main principles of OOP, which are commonly referred to as the pillars of OOP. This time I will check them out.

## 1. Inheritance
*****

Inheritance is the process of creating a primary class from which other classes, referred to as child classes, can be created. **A child class takes on all of the features of the primary, or parent class, automatically.** This reduces the need to rewrite code that both classes would need to make use of.

As an example, let's say that we wanted to create a new class called Dayoming. Like the People class, it can be awake or asleep, has hair that can be a specific color, and can move.

Without inheritance, you'd essentially have to copy all of the code you already wrote and paste it into the new class.

With inheritance, you simply extend the People class, and that functionality is already there and accessible for the Dayoming class. The class could then go on to feature each person-specific functionality, such as active time.

> The child class of People class

```c#
public class Dayoming : People
{
  void ActiveTime()
  {
    State dayomi = State.Awake;
  }
}
```

## 2. Abstraction
*****

Abstraction is the process of removing complex code from the scripts there other programmers will see it, and **only exposing the functionality** other programmers really need. When you "abstract out" the details, you reduce duplicate code and provide easy access to the most useful methods. 

The goal of abstraction is to **keep your code as clean as possible**, and simple for other programmers or yourself to use.

In the People example above, the Run method would be a good example of abstraction. Without the Run method, if a programmer wanted the Dayoming to run, they would have to write code that would access its RigidBody and set its velocity, run speed, and so on.

With the Run method, all of those aspects are abstracted away, allowing the programmer to focus on when the Dayoming should run rather that how.

```c#
public class Dayoming : People
{
  void Move()
  {
    Dayoming.Run();
  }
}
```

## 3. Polymorphism
*****

Polymorphism is one of the most useful aspects of using in heritance. It allows you to create **alternative functionality for code that's been inherited from a parent class**.

As an example, the running speeds of every each person are all different. Dayoming should probably be a little bit faster than other people. 

With polymorphism, you can **override** the contents of the Run method and write custom code that's unique to the Dayoming. The method call remains the same, but the correct code will be called based on which entity it was called on.

```c#
public class Dayoming : People
{
  void Run() 
  {
    float speed = 20f;
    ...
  }
}
```

## 4. Encapsulation
*****

Encapsulation is similar to abstraction in that its overarching purpose is to separate the programmer from code complexity, but the focus here is more on **code safety in the form of accessibility**. Encapsulation gives you tools to code for oher programmers, and make sure that they only use your variables and methods as intended.

In encapsulated code, other programmers can't easily change the values of variables or the properties of objects. It's impossible to account for all of the different ways that other scripts might access your code, so it's far better to encapsulate what you've created so it can only perform as intended. 

As an example, let's say that People's state affects its health ability. Once that value is set, it shouldn't be changed later on. To ensure that the value is protected, you would set is as private, preventing any outside scripts from accessing it.

```c#
public class People
{
  // Variables
  protected enum State {Awake, Asleep};
  private color hairColor;

  // Method
  public void Run()
}
```