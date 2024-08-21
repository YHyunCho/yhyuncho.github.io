---
title: "Refactor Code with OOP(1) : Abstraction"
date: 2024-08-21 10:10:00 +09:00
categories: [Unity Learn, Conceptual Learning]
tags:
  [
    Refactoring, abstraction
  ]
---

## Abstraction and script refactoring
*****

**Refactoring** is a process to work through existing code and improve it without changing its external functionality. Refactoring is an inportant step in the development process, in which we can clean up any messy code we wrote earlier, and also reexmine the ways our code will be used in future iterations of the project. In other word, in the development phase you wrote code that "works well for now," and in the refectoring phase we will revise the code to "work well later"

An important part of refactoring code is to improve its functionality without changing the way that other programmers interact with it. Abstraction plays a key role in this. As long as the method call and output doesn't change, we can adjust the contents of the method without the other programmer ever knowing. This means that we can safely refactor our code without risk of breaking anything elsewhere in our project.

Let's look at a simple example : 

```c#
int TripleResult (int inputNumber)
{
  int outputNumber = inputNumber + inputNumber + inputNumber;
  return outputNumber;
}
```

In the above method, the calling script passes in an integer and gets an output that's been tripled. The approach to get the output number is a little ineffective, so we could refactor the code to look like the folling.

```c#
int TripleResult (int inputNumber)
{
  int outputNumber = inputNumber * 3;
  return outputNumber;
}
```

Even though the content of the method has been rewritten, the way the method is called remains exactly the same, as does what gets returned. Therefore, any call to the method throughout the project will continue to work.

## Implementing abstrantion in the project
*****

This code is the Update() method in UserControl script : 

```c#
private void Update()
    {
        Vector2 move = new Vector2(Input.GetAxis("Horizontal"), Input.GetAxis("Vertical"));
        GameCamera.transform.position = GameCamera.transform.position + new Vector3(move.y, 0, -move.x) * PanSpeed * Time.deltaTime;

        if (Input.GetMouseButtonDown(0))
        {
            var ray = GameCamera.ScreenPointToRay(Input.mousePosition);
            RaycastHit hit;
            if (Physics.Raycast(ray, out hit))
            {
                //the collider could be children of the unit, so we make sure to check in the parent
                var unit = hit.collider.GetComponentInParent<Unit>();
                m_Selected = unit;


                //check if the hit object have a IUIInfoContent to display in the UI
                //if there is none, this will be null, so this will hid the panel if it was displayed
                var uiInfo = hit.collider.GetComponentInParent<UIMainScene.IUIInfoContent>();
                UIMainScene.Instance.SetNewInfoContent(uiInfo);
            }
        }
        else if (m_Selected != null && Input.GetMouseButtonDown(1))
        {
            //right click give order to the unit
            var ray = GameCamera.ScreenPointToRay(Input.mousePosition);
            RaycastHit hit;
            if (Physics.Raycast(ray, out hit))
            {
                var building = hit.collider.GetComponentInParent<Building>();

                if (building != null)
                {
                    m_Selected.GoTo(building);
                }
                else
                {
                    m_Selected.GoTo(hit.point);
                }
            }
        }
        MarkerHandling();
    }
```

Abouve Update, define two new public methods with no returns and name them HandleSelection and HandleAction.

```c#
public void HandleSelection ()
{

}

public void HandleAction ()
{

}
```

HandleSelection will manage everything that occurs when the user left-clicks one of the Forklifts in the application. We'll still need to check for the left mouse button ```Input.GetMouseButtonDown(0)``` in Update, but everything else will be moved to our new method.

And next, in the Update method, select the contents and move them to right methods

```c#
    public void HandleSelection()
    {
        var ray = GameCamera.ScreenPointToRay(Input.mousePosition);
        RaycastHit hit;
        if (Physics.Raycast(ray, out hit))
        {
            //the collider could be children of the unit, so we make sure to check in the parent
            var unit = hit.collider.GetComponentInParent<Unit>();
            m_Selected = unit;


            //check if the hit object have a IUIInfoContent to display in the UI
            //if there is none, this will be null, so this will hid the panel if it was displayed
            var uiInfo = hit.collider.GetComponentInParent<UIMainScene.IUIInfoContent>();
            UIMainScene.Instance.SetNewInfoContent(uiInfo);
        }
    }

    public void HandleAction()
    {
        //right click give order to the unit
        var ray = GameCamera.ScreenPointToRay(Input.mousePosition);
        RaycastHit hit;
        if (Physics.Raycast(ray, out hit))
        {
            var building = hit.collider.GetComponentInParent<Building>();

            if (building != null)
            {
                m_Selected.GoTo(building);
            }
            else
            {
                m_Selected.GoTo(hit.point);
            }
        }
    }
```

In Update method, call each methods.

```c#
    private void Update()
    {
        Vector2 move = new Vector2(Input.GetAxis("Horizontal"), Input.GetAxis("Vertical"));
        GameCamera.transform.position = GameCamera.transform.position + new Vector3(move.y, 0, -move.x) * PanSpeed * Time.deltaTime;

        if (Input.GetMouseButtonDown(0))
        {
            HandleSelection();  // method now called from here
        }
        else if (m_Selected != null && Input.GetMouseButtonDown(1))
        {
            HandleAction();     // method now called from here
        }

        MarkerHandling();
    }
```

Now save the script and return to Unity and playtest the scene! The functionality shouldn't have changed. The difference now is that if we or any oher programmer needs this functionality elsewhere in the application later, it's more freely available.