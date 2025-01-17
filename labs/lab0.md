---
layout: post
title: "Lab 0 - Getting Started"
permalink: /labs/lab0
categories: labs
---
This first lab or tutorial, will walk you through the tools and the proejct structure that will be used for this course.
## Ponderation: 0%
## Objectives:
 - Creating your first MAUI App
 - Creating an Android Emulator
 - Getting familiar with the .NET MAUI project structure
 - Modifying existent buttons and labels
 - Adding pages
 - Creating basic navigation

### Creating your first .NET MAUI App

It's recommended that you use the school PCs to complete the labs because they have already been correctly configured and have the recommended specs.

#### Steps: 

1. Open Visual Studio 2022

2. Create a MAUI App from the project templates
   <img src="{{site.baseurl}}/images/maui_intro/maui_template.png" height=500 />

3. Choose a name and location for your new MAUI app.

4. Select the latest .NET framework available. It should be .NET 6.0 or 8.0 (Long term Support)

5. Press the Play button to build the project on your Windows computer:
   <img src="{{site.baseurl}}/images/maui_intro/run_windows.png" />

6. You should now see the MAUI template project app:
   <img src="{{site.baseurl}}/images/maui_intro/Maui_windows.png" />

7. On File Explorer, navigate to your `<project folder>/bin/Debug/`, you should see multiple folders on which your app can be deployed. For now, the only folder that contains a deployed solution is the Windows folder:   
   <img src="{{site.baseurl}}/images/maui_intro/debug_folder.png" />


- For each platform used when debugging, an executable is created for the associated platform.

### Debugging on the Android Emulator

The android emulator created by Google will allow us to simlulate the hardware of an Android device on your Windows machine. It's integrated into Visual studio 2022.

- Pre-requisite: Enabling hardware acceleration with Android emulator and install the Android SDK API. Make sure that your PC has the [minimum requirements](https://learn.microsoft.com/en-us/dotnet/maui/android/emulator/hardware-acceleration?view=net-maui-8.0) before proceeding.

- The installation and configuration of the Android Emulator should already be done on the lab PCs.

#### Steps: 

1. From Visual Studio 2022, open the `Tools > Android > Android Device Manager`
2. Create a new device, this should open the New Device pop-up.
3. For this course, most of the labs will be tested on the: `Pixel 5 - API 33 emulator` with the default configuration.
4. Create an emulator of your choice and try debugging the app.



### Modifying the MainPage

#### Steps: 

1. Run your project on the newly created emulator

2. Open the `MainPage.xaml` file and modify the following:

   - `Label` 1 text: "Lab0"
   - `Label` 2 text: "Getting Started"
   - Remove the `CounterBtn`
   - Add a `Button` :
     - `Text`: `"Exercice 1"`
     - `Clicked`: `"Btn_Exercice1_Clicked" `(Create a new event handler)
   - Add a `Button` `Text`: "Exercice 2":
     - `Text`: `"Exercice 1"`
     - `Clicked`: `"Btn_Exercice2_Clicked"`

<img src="{{site.baseurl}}/images/labs_images/lab0_main_page.png" height=300/>

### Adding other pages
#### Steps: 

1. Add a new file to your project
2. Select the template `.NET MAUI ContentPage` 
3. Name it `Exercice1.xaml`
4. Modify the default `Label`: `Text`= "Exercice 1"
5. Add a another file to your project
6. Select the template `.NET MAUI ContentPage` 
7. Name it `Exercice2.xaml`
8. Modify the default `Label`: `Text`= "Exercice 2"

### Basic Navigation
#### PushAsync 

To Navigate to the newly created pages, we will be pushing the pages on the stack using `PushAsync` method as such:

```csharp
Navigation.PushAsync(new Exercice1());
```



Let's observe the signature of this method signature for a minute:

```csharp
public System.Threading.Tasks.Task PushAsync (Microsoft.Maui.Controls.Page page);
```



- This method returns a `Task` asynchronously. This means that the newly pushed page will be pushed in the background without freezing the current application thread. 

  

- What do you think would happen if this method was not async?

- What do you think would happen if the page takes time to load?

#### Steps: 

1. Modify the signature of your event handler to add the keyword `async`:

```csharp
private async void Btn_Exercice1_Clicked(object sender, EventArgs e) 
```



2. You must now await the `Navigation`.`PushAsync` method and provide an instance of the `Exercice1` Page. To do that, you must add the keyword `await` before the call of the method.

``` csharp
private async void Btn_Exercice1_Clicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new Exercice1());
}
```

3. Re-run the app. You should be able to see the `Exercice1` page once you click on the Button:

4. Repeat the same steps to navigate to the `Exercice 2` Page.


#### NavigationPage

- This way of pushing pages on top the of navigation stack is part of a style of navigation called: `NavigationPage`
- In the `NavigationPage`, pages has three events: **pushed**, **popped** or **popped to root** for any pages it contains.
- Other navigation modes: `FlyoutPage`, `TabbedPage` 
- Many [properties](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/pages/navigationpage?view=net-maui-8.0) of the `NavigationPage` can be useful to customize the app.
- You'll notice that once you clicked on a page, you can click at the top left corner (back button) to popped the current page and return to the  `MainPage`

#### Steps: 

Let's modify the Navigation bar back ground color (by default white):

1. Open the `AppShell.xaml` file

2. In the `Shell` tag add the following property (you may choose another color): 

   - `Shell.BackgroundColor`=`"{StaticResource Primary}"`

3. Now as you click on the page, the back button should be clearly visible:

<div style="display: flex; margin-right: 10px;" >
    <img src="{{site.baseurl}}/images/labs_images/Lab0_exo1.png" height=300/>
    <img src="{{site.baseurl}}/images/labs_images/Lab0_exo2.png" height=300/>
</div>   
