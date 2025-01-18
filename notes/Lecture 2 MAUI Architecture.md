---
layout: post
title: "Lecture 2: .NET MAUI"
permalink: /lectures/intro-maui
categories: notes
---
This lecture is are based on: 
[Microsoft's documentation](https://learn.microsoft.com/en-ca/dotnet/maui/what-is-maui?view=net-maui-8.0) 



[TOC]

[Demo made in class](https://github.com/AppDevIII-W24-Code/Demos/tree/632ce254f018c06db0b29a42a52228b7ad6d41a9/Demo_Intro_MAUI)

### Architecture of .NET MAUI

- .NET MAUI stands for .NET Multi-platform App UI.
- It's a relatively new cross-platform framework from Microsoft.
- Shared code base for iOS, Androidm MacOs and Windows.
- Very well intgrated with Visual Studio. 
- Programming language: C# (already cross-platform!)
- UI page definition uses Xaml which is a declarative language (you've already seen this in previous courses!)




### How does MAUI work?
- Abstracts the details specific to each platform using platform-specific framewors: .NET Android, .NET iOS, .NET macOs, WinUI 3 (Windows)
- Uses a unified interface called .NET Base to interact with each of these frameworks.
- The business logic and common UI elements of your App will be using this interface.

<img src="{{site.baseurl}}/images/maui_intro/maui_architecture.png" height=500 />

- For Android, iOs and MacOs, the execution environment is implemented in Mono.
- For Windows, the execution environement is .NET CoreCLR.

### Supported platforms:

According to Microsoft's latest [documentation](https://learn.microsoft.com/en-us/dotnet/maui/supported-platforms?view=net-maui-8.0):

| Platform | Min Version|
| :--------------------:|
| Windows  |    10.1809    |
| Mac      |    10.15   |
| Android  |    5.0     |
| iOS      |    11      |
| :--------------------:|





#### Pros:

- Single codebase for all platforms which insures maximum reusability and a consistency across the apps deployed on various platforms
- Codebase is easier to maintain when the code is centralized (bug fixes are much easier to deploy).
- The use of XAML/C# makes it easier to code and test your apps.
- Well documented and large .NET Developer community.
- Makes the use of the Model-View-ViewModel (MVVM) pattern very easy to use.
- Provides a solution for platform-specific runtime differences. 

##### Cons:

- Slightly less performant than native apps.

- Relatively new (May 2022), which means there's a few bugs and limitations that are still being addressed by Microsoft.

- The framework can feel limiting if you choose to architect your apps differently (not MVVM), or if you wish to access new features of a given operating system: you depend on Microsoft's integration of new technologies to the framework.


### Overview of a MAUI Project

- Platforms: This folder contains platform specific code:
  <img src="{{site.baseurl}}/images/maui_intro/project_explorer.png" />
  
- Resources: this folder contains all images, fonts, etc. are stored. MAUI will be responsible of embedding this content to each platform with its specificity.  



### XAML

- XAML is a declarative markup language which declares the UI components of the App.
- "Declarative" means it's non-procedural: "The focus is not how you want the job done, but what the result you want to obtain".
- You cannot "debug" it, although the compiler is getting better at helping us find mistakes.
- Other examples of declarative languages: SQL, HTML, XML, etc.
- It's also used in WPF and UWP.
- Extension is `.xaml`
- For every xaml file, you have an associated C# code behind with the same name:
<img src="{{site.baseurl}}/images/maui_intro/MainPage.png" />

Example `MainPage.xaml`:
- Contains the definition of the main page.
- Note that the `ContentPage` is definied using the following attributes:
    - `xmlns` : The XML namespace is the default namespace in every XAML file, it refers to the .NET MAUI classes (`ContentPage`, `Button`, `VerticalStackLayout`)
    - `xmlns:x` : The secondary namespace refered to as `x` in the file, it is associated with elements specific to `XAML`. This allows MAUI apps to support constructs created by other frameworks (WPF, UPF, etc.) - originally created in 2006.
    - `x:Class`: One of the constructs introduced by XAML allowing a binding between the XAML Content Page and the C# class definied in the [Code Behind](#code-behind). 
    - We will see more on data binding on lecture 3.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MauiApp1.MainPage">

    <? Page content definition... ?>
    
</ContentPage>

```

### Code Behind

- Similar to WPF, MAUI uses code behind associated with each XAML file which definies the UI element ("How it should be done").


Example `MainPage.xaml.cs`:

- It contains the constructor of the `ContentPage` inherited page. 
- You'll notice that the `MainPage` (or any user definied page) inherits from the `ContentPage` class.
- The user definied page, will typically contain variables specific to the App that is interfacing with the model (classes and business logic).
- It's generally good practice to extract any business logic away from the code behind and add it to a model class. 



```csharp

namespace MauiApp1;

public partial class MainPage : ContentPage
{
	int count = 0;

	public MainPage()
	{
		InitializeComponent();
	}
    /// Content ...

```

- Note that you can declare and define the UI elements in the code behind without declaring it in the XAML and without calling  `InitializeComponent()` in the constructor.

```csharp

public partial class MainPage : ContentPage
{
    int count = 0;
    
    public MainPage()
    {
        Image image = new Image 
        { 
            Source= "dotnet_bot.png" ,
            HeightRequest=185,
            Aspect = Aspect.AspectFit
        };
        Label label = new Label
        {
            Text = "Hello, World!"
        };
        Label label2 = new Label
        {
            Text = "Welcome to &#10;.NET Multi-platform App UI"
        };


        Button CounterBtn = new Button
        {
            Text = "Click me",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        CounterBtn.Clicked += async (sender, args) => OnCounterClicked(sender, args);


        Content = new VerticalStackLayout
        {
            Children =
                {
                    image,
                    label,
                    label2,
                    CounterBtn
                }
        };
    }

}

 
```

### Hot Reload 

- One on the most convinient features of .NET MAUI Xaml is Hot reload which enables you to visualize modifications of the UI without stopping and re-building the app.
- Topic of Lab0.

<img src="{{site.baseurl}}/images/maui_intro/hot_reload.png" />

The hot reload feature works for debugging on: 
- Windows Machine (WinUI)
- iOS Simulators
- Android Simulators
