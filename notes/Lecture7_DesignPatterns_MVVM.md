---
layout: post
title: "Lecture 7 : MVVM"
permalink: /lectures/mvvm/
categories: notes
---
[Demo made in class: MVVM and Unit Testing](https://github.com/AppDevIII-W24-Code/Demos/tree/main/DemoMvvmAndTesting)

The Model-View-ViewModel (MVVM) pattern is a *software design pattern*. Design patterns are sets of rules and templates that help make application code **better**, more **structured** or more **consistent**. 

- The MVVM pattern is used to help separate the app's behavior logic from the user interface-rendering code, and to do so in a way that supports unit testing.

- MVVM is the basis for many frameworks and programming tool-kits. Most of those frameworks also provide other things, like navigation and messaging abstractions, that help MVVM promote unit testing. 

  

  <img src="{{site.baseurl}}/images/mvvm/MVVMPattern.png" />

## MVVM & `.NET MAUI`

- `.NET MAUI` apps that do not use MVVM generally have more code in their *code-behind* files. 
  - The code-behind files follow this pattern: *{something}.xaml.cs*. 
  - Most code in the code-behind file usually controls the user interface (UI) behavior. *UI behavior* can include anything that happens *to* the UI, like changing a color or some text. 
  - Code-behind file can include anything that happens *because of* the UI, including button click handlers.
- What is the problem in using code-behind files?
  - One problem with this approach is that it is hard to write unit tests **against code-behind files**. 
  - Code-behind files in a `.NET MAUI` app derive from a `.NET MAUI` framework type. They often assume an application state that is created by parsing XAML or even created by other pages. These conditions are difficult to handle in a unit test runner that might not even be running on a mobile device, let alone with a user interface. So, unit tests are rarely **able to test the UI behaviors** in these files.
- Why and when to use MVVM?
  - When used correctly, the MVVM pattern solves these problems by moving most UI behavior logic to unit-testable classes that are called `viewmodels`. 
  - The MVVM pattern is most commonly used with frameworks that support data-binding. 
  - In `.NET MAUI`, each UI element can data-bind to a `viewmodel` and eliminate or nearly eliminate code in a view or code-behind.

## Parts of an MVVM application

### Model

In an MVVM application, the *model* refers to your business data and business logic. The model does not concern how the app is presented to the user.

A good guideline for defining what code goes in the model is that you could port your application from mobile app to the web or to a command-line program and use the same *model* in all three places. It has nothing to do with presentation to the user.

When you think about an application, the model would include a class of an item used in a collection. The model could also include things like a `Repository` class that holds persistence logic. Some other software-design patterns would consider things like *repositories* as separate from the model. 

### View

The *view* code controls things that directly interact with the user. Usually that means changing pixels on the screen and receiving clicks and taps. View items include controls like buttons and entry fields, as well as any other purely visual elements like themes, styles, and fonts.

In many `.NET MAUI` apps, you do not need to write any C# code for views yourself. Instead, your views are often defined by your `.xaml` files. Of course, there are situations that call for a custom user control. In those cases, you can create your own view code. 

### ViewModel

The viewmodel is the intermediary between our business logic and the app views.



As shown above the viewmodel exposes properties to the user interface. Viewmodels usually rely on models for most of their data and any business logic. In addition, it is the viewmodel that formats, converts, and enriches the data in whatever way the current view requires.

## Application Testing

Testable parts of an app:

- Views (User interface)
- Services
- Models
- View Models

## How to write unit tests in .NET MAUI?

**Configuration:**

1. Add a new project within the same solution as the MAUI App

2. Select the **xUnit Test** Project 

3. Give it a name ending with `Testing`

4. By default, you should have one Unit Test which you can run by right clicking and clicking `Run Test`

5. This will not work at first, we need to link the projects up first.

6. Right click > Add Project reference > Check the box

7. You also need to ensure that your app targets the netX.0 framework used by the **xUnit** Project.

8. For the **MAUI app** project, modify the MS Build file to add the .net framework used by the xUnit Project: example `net7.0`:

   ```xml
   <!-- xUnit: Add net7.0; here -->
   <TargetFrameworks>net7.0;net7.0-android;net7.0-ios;net7.0-maccatalyst</TargetFrameworks>
   ```

9. Ensure that the output is a .dll not .exe when building the app for testing:

   ```xml
   <!-- xUnit: The condition here only excludes this for the unit test project -->
   <OutputType Condition="'$(TargetFramework)' != 'net7.0'">Exe</OutputType>
   ```

10. Re-build the Maui App. Re-run it on your other target frameworks to ensure it is still functional.

11. To use APIs within a Unit test, add the following in the MSBuild file:

    ```xml
    <PropertyGroup>
    	<UseMaui>true</UseMaui>
    </PropertyGroup>
    ```



## Demo - turning the Billing App into MVVM and unit testing it

**Objectives:**

- How to refactor an existing app to MVVM
- How to use the *MVVM Community Toolkit*
- How to use Commands
- How to use `EventToCommandBehavior` 
- How to create an *xUnitTest* project to test the View Model.

