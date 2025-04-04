# Lab 3 - part 1

1. 📝 **Worth:** 1%  (/10pts) 
2. 📅 **Due:** Friday March 21, 2024 @End of class
3. 🕑 **Late submissions:** Not accepted.
4. 📥 **Submission:** In class 



## Objectives

In this lab, we will attempt to add navigation to the Maui Social Media App, add photo pickers, save the user preferences and use an API to save images. 

- Add flyout and tab bar navigation 
- Use application preferences
- Add and use embedded text files



## Setup

- Accept the Github classroom assignment :

  - [Section 1](https://classroom.github.com/a/VkZFIBNE)
  - [Section 2](https://classroom.github.com/a/GFAeEFHi)

  



## Exercise 1 - Flyout Menu

As an inspiration, we will be using Instagram's layout to add navigation as such:

1. Add the `./Views` folder to the `AppShell.xaml` namespace:

   ```xml
   <Shell
       x:Class="DemoNavigation2.AppShell"
       xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:DemoNavigation"
       xmlns:views="clr-namespace:DemoNavigation.Views">
   ```

   This will enable us to refer to the views folder from within xaml

2. Add the pages below to the `ShellContent` in the `AppShell` :

   - Groups 
   - Info 
   - Settings

3. Notice that the `Route` property must be changed for each added page.

4. Add an icon to each flyout item to obtain the following results:

   <img src="images/maui_navigation/flyout_1.png" Height="300" class="inline-img"/>

5. Add a flyout item which will act as a button to switch between dark/light mode:

   ```xml
   <MenuFlyoutItem Text="Switch Theme"  Clicked="Btn_SwitchTheme_Clicked"/>
   
   ```

6. Add the even handler in the `AppShell.xaml.cs`

   ```csharp
   private void Btn_SwitchTheme_Clicked(object sender, EventArgs e)
   {
   
       switch (App.Current.UserAppTheme)
       {
           case AppTheme.Light:
               App.Current.UserAppTheme = AppTheme.Dark;
               break;
           case AppTheme.Dark:
               App.Current.UserAppTheme = AppTheme.Light;
               break;
           default:
               App.Current.UserAppTheme = AppTheme.Light;
               break;	
       }
   }
   
   ```

### Exercise 2 - Tab Bar 

To combine the flyout menu and the tab bar, each `ShellContent` you wish to display in the `Flyout` should be wrapped within a `FlyoutItem` **instead of the `TabBar`** and keep the `ShellContent` you wish to keep in the Flyout as direct children of the `Shell`



1. Modify the `AppShell` to create the following strucutre:

   ```text
   - Home
     - MainPage
     - SearchPage
     - NewPost (tab)
     	- SelectPhotos
     	- CameraPage
     - Videos
     - ProfilePage
   - Groups
   - Info
   - Settings
   ```

   

## Exercise 3 - App Preferences

You will have to complete the `Settings.xaml` page functionality in order to save three user preferences and then use them to define the Username displayed in `ProfilePage.xaml` and set the `App.Current.UserAppTheme`:

| Preference name                                      | Type     |
| ---------------------------------------------------- | -------- |
| "`username`" (You can rename it to something else)   | `string` |
| `"apptheme" (You can rename it to something else)    | `bool`   |
| `"profilepic"` (You can rename it to something else) | `string` |

**Setting preferences**

1. Inspect the `Settings.xaml` file and note the `TableView`'s content, these are the entries where the user can set their preferences.

   > Note: Synchronize your starter repo, I added a TableView and the event handlers described below. Otherwise you can add a [TableView](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/tableview?view=net-maui-9.0).

2. In `Settings.xaml.cs`, implement the event handlers to set each of the preference listed above

> To set a preference use `Preferences.Set("nameofthepreference", value);`

3. For the `AppTheme` inspect the `MenuFlyoutItem` and its event handler added to the `AppShell.xaml.cs` to know how to get the current app theme.
4. In addition to saving the new preference, you should set the app theme to Light if the toggle is on, and set it to Dark if the toggle is off.

**Using preferences**

3. Restart the app to test the next few steps
4. Inside the `Settings.xaml.cs` page constructor, get the preferences saved to set the entries text and the toggle switch state. 

> To get a preference use
>
> `Preferences.Get("nameofthepreference", defaultvalue);`

5. Get the preference of the theme to set the App theme inside the `AppShell.xaml.cs`
6. Get the preference of the username and the user profile to set the username and profile picture inside the `ProfilePage.xaml.cs` ~~and inside the `CommentsPage.xaml.cs`~~(removed 17-03-2025).

 **✨ Test your understanding** Is it possible to serialize an object inside the Preferences?

**Optional Challenge**

Hardcoding strings is not a good practice, especially since preferences are shared across multiple pages (e.g., Settings, ProfilePage). Additionally, adding new settings would require hardcoding new preference keys. You might also notice that pages do not always reload settings immediately. Can you propose a better way to store and retrieve user preferences without hardcoding keys while keeping Get/Set operations centralized? Ideally, your solution should use binding so that labels update automatically in one view when changed in the settings view.

## Exercise 4 - Reading an embedded file

1. Create a text file and save it as  `Files/InfoText.txt`, we will use it as an embedded file. 

   > Note: You can also synchronize your starter repo, I added a sample file. 

2. You can write a short description of the app or have Chat Gpt write some text.

3. The`Info.xaml`will serve as a placeholder for a static text contained in this file

4. Open your `MSBuild` file (`.csproj` file, you can open by double clicking the name of the project and add the file as an `EmbeddedResource`

5. Implement the private method `LoadFile()`, open the embedded file and 

   > Hint: Use the `Assembly.GetExecutingAssembly().GetManifestResourceNames()` and `GetManifestResourceStream()` methods.	

6. Using a `StreamReader`, read the content of the file

7. Set the `FileEditor.Text` to the content of the file.

8. Call the method in the constructor of the `Info` page

9. Display its content in the Editor



 **✨ Test your understanding**   What other files are included as embdedded files? 



# Lab 3 - Part 2

## Exercise 5 - Serialization

To ensure the persistence of the app we would like to save the `ObservableCollection<Post>` to a json file using the `System.Text.Json.JsonSerializer` class. I have already created two methods in the `PostsRepo` which you need to implement: `LoadPosts()` and `SavePosts()`. I have also already added calls to these methods inside the `PostsRepo` class. 

1. Inside the `App.xaml.cs`, create a new static `string`variable called `"DataFile`"

2. Create a file path to a `"posts.json"` file saved in the `AppDataDirectory`

3. Use this variable to initialize the static `PostsRepo` 

4. Observe the constructor of the `PostsRepo` and implement the `SavePosts()` and `LoadPosts()` :

   - Use a `try/catch` to ensure some error handling and logging of errors.
   - Make sure that if the file doesn't exist to not make the app crash, since the first time this file won't exist.

   > Hint: Use `JsonSerializer.Serialize<ClassToSerialize>(stream, ObjectToSerialize); `and `JsonSerializer.Deserialize<ClassToSerialize>(stream);`

5. Test it by adding a few comments and likes to some posts and re-starting the app. Your changes should be saved.



 **✨ Test your understanding:**  What is the interest of using a static repo as opposed to simply initializing a list of posts in the code behind as we've done in the previous demos? 

**✨ Test your understanding:**  Why did I make the `PostsRepo` implement the `INotifyPropertyChanged` interface?

 **✨ Test your understanding**  What is the interest of having an `UpdatePost()` method when I could simply let the Pages code behind change a given Post directly (ex: `Post.Likes++`)?

## Exercise 6 - Selecting and Taking pictures

1. Modify the [app permission](https://john-abbott-college.github.io/6A6-Notes/#/notes/Lecture8_SavingData?id=file-picker) to allow access to the device drive and the camera. On Android the `appmanifest.xml` is found under the *Platforms > Android*, while iOS the `Info.plist` is found under *Platforms > iOS*

2. Since the operation of picking a file or taking a picture is `I/O Bound` we want to avoid blocking the UI thread and instead async methods. 

3. Modify the `FilePage.xaml` page which appears as the Select Photo page

4. Add an Image button to this page with an event Handler *Btn_SelectPhoto_Clicked*

   ```xaml
   <ImageButton Source="{AppThemeBinding Light = select.png, Dark=select_dark.png}" BackgroundColor="Transparent" Clicked="Btn_SelectPhoto_Clicked" HorizontalOptions="Start" WidthRequest="100"/>
   
   ```

5. Create the event handler

6. Use `FilePicker.PickAsync` and `PickOptions()` to help the user select photos on their device.

7. Modify the `NewPostPage.xaml` page which appears as the *Camera* Page in the app.

8. Include two 

# References

1. [How To Upload Image(Cloudinary/Asp.Net Core)](https://medium.com/@karakizleyligul/how-to-upload-image-cloudinary-asp-net-core-e47023ff2431), [Karakiz Leyligul](https://medium.com/@karakizleyligul?source=post_page-----e47023ff2431--------------------------------), 2022, March 31. Last Access: 2024, March 20.

2. [Dot Net MAUI Configuration](https://github.com/jamesmontemagno/dotnet-maui-configuration/tree/master), [James Montemagno](https://github.com/jamesmontemagno), 2022, March 25. Last Access: 2024, March 20.

   