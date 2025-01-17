---
layout: post
title: "Lab 3 - Saving data"
permalink: /labs/lab3
categories: labs

---



1. 📝 **Worth:** 2%  (/5pts)
2. 📅 **Due:** Friday March 22, 2024 @End of class
3. 🕑 **Late submissions:** Not accepted.
4. 📥 **Submission:** In class 



## Objectives

In this lab, we will attempt to make the Maui Social Media App more persistent to user changes.  

- Learn how to use application preferences
- Learn how to use embedded text files
- Learn how to use embedded app settings in a json format
- Learn how to use an API to save images on the cloud.
- Learn how to serialize and deserialize objects and data strucutres.



## Setup

- Clone the following [repo](https://github.com/AppDevIII-W24-Code/Labs-solutions.git)

- Save it on your local drive `C:/Users/<studentId>`, not on One Drive

- Avoid lengthy file directories

- Open the Lab3 folder containing the `MauiSocial` app, feel free to create a copy of the project so you can push it on a personal repo later on.

- You will not submit this at the end, you will only show me your progress in class.

- I have already installed the important package :
  - `Cloudinary` (to upload pictures on the cloud)



#### Target platform

For this lab we will be testing the app on two different form factors:

- Android Emulator: Pixel 5 - API 34

- (optional) iOS, **Note:** I did not configure app entitlements for iOS nor did I test the app on this platform.

  



## Exercise 1 (1 pt) - App Preferences

For this first exercise, you will have to complete the `Settings.xaml` page functionality in order to save three user preferences and then use them to define the Username displayed in `ProfilePage.xaml` and set the `App.Current.UserAppTheme`:

| Preference name | Type     |
| --------------- | -------- |
| "`username`"    | `string` |
| `"profileuri"`  | `string` |
| `"apptheme"`    | `bool`   |

**Setting preferences**

1. Inspect the `Settings.xaml` file and note the `TableView`'s content, these are the entries where the user can set their preferences.
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
6. Get the preference of the username and the user profile to set the username and profile picture inside the `ProfilePage.xaml.cs` and inside the `CommentsPage.xaml.cs`

 **✨ Test your understanding** Is it possible to serialize an object inside the Preferences?

## Exercise 2 (1pt) - Reading an embedded file

1. Open the `InfoPage.xaml`and inspect the UI elements

2. This page will serve as a placeholder for a static text contained in the `Files/InfoText.txt` file

3. Open your `MSBuild` file and add the file as an `EmbeddedResource`

   

4. Implement the private method `LoadFile()`, open the embedded file and 

   > Hint: Use the `Assembly.GetExecutingAssembly().GetManifestResourceNames()` and `GetManifestResourceStream()` methods.	

5. Using a `StreamReader`, read the content of the file

6. Set the `FileEditor.Text` to the content of the file.

7. Display its content in the Editor



**Do it at the end:**

 **✨ Test your understanding**   What other files are included as embdedded files? 



## Exercise 3 (1pt)

1. In the `PostPage.xaml.cs`, implement the `Btn_Save_Clicked()` event handler
2. Save the photo to the `FileSystem.AppDataDirectory`:

> Hint: Read the stream of the file and use `sourceStream.CopyToAsync(destinationStream);` 

3. You'll notice that you cannot validate your work because you do not have access to this directory on the Android Emulator.
4. To validate that the file was correctly saved, we will upload the image on Cloudinary in **Exercise 5**. 



## Exercise 4 (1pt) - Serialization

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



## Exercise 5 (2pt) - Using an API to upload pictures.



***Cloudinary- Setup***

*Cloudinary* is a a cloud management service for images and videos which allows programmatical uploads of media files and enables image and video transformations (cropping, filtered, etc). We will use the free license to upload pictures taken with the ***MauiSocial App***. 

Before using the service, we need to generate some api keys and secrets:

1. Create an [account](https://cloudinary.com/users/register_free) or use GitHub to login

2. Verify your account 

3. Go to the **Dashboard**

4. Get the **Cloud name**, **API Key**, **API Secret** 

5. Save them to an `"appsettings.json"` file inside your project directory.

   ```json
   {
     "CloudinaryConfig": {
       "cloudName": "1234",
       "apiKey": "1234",
       "apiSecret": "1234"
     }
   }
   ```

   

6. If you push your source code somewhere, do not push this `"appsettings.json"` file at the root of the project as these are your confidential keys.

***Using the service***

1. Add the `"appsettings.json"` as an the embedded resources 

2. Install the following packages:

   - Install the `Nuget Microsoft.Extensions.Configuration.Json` 
   - Install the `Nuget Microsoft.Extensions.Configuration.Binder`

   I've already created a Services folder in which I included a Config class to parse the content of the json

3. Complete the constructor of the `CloudinaryService`:

4. Get the `"appsettings.json"` stream and use the following line to parse the config:

   ```csharp
   _config = configuration.GetSection(nameof(CloudinaryConfig)).Get<CloudinaryConfig>();
   ```

5. Now inside the constructor instanciate the API class:

   ```csharp
   Account account = new Account( _config.CloudName, _config.ApiKey, _config.ApiSecret );
   _cloudinary = new Cloudinary( account );
   ```

6. Use a `try/catch` inside the Service to ensure that the embedded file is properly found, parsed and used. 

7. To test it out, go to your `App.xaml.cs` and create a static instance of this class.

8. Run the app and observe any except being thrown. 

 **✨ Test your understanding:**  What's is an `"appsettings.json"` file typically used for? 

 **✨ Test your understanding:** What's the interest of including it as an embedded file. 



9. Implement the method which should call `_cloudinary.UploadAsync()`: use proper exception handling to ensure that exceptions are logged to the console.

```csharp
public async Task<ImageUploadResult> UploadImage(string imageFilePath,string imageName)
```

10. Call the method inside the `Btn_Save_Clicked()` event handler inside the `PostPage`: 
    - Use exception handling and display an alert in case of error. 
    - Get the `secureurl`  from the returned result
    - Add a new post in the `Repo` with this `url`
    - You should see the post appearing in the `MainPage`





## Bonus exercise 1pt  

James Montemagno created an [Plug-in](https://github.com/jamesmontemagno/MediaPlugin) (installed as a Nuget package) which facilitates the action of taking pictures, saving them and saving them to Gallery. 

Create a user preference inside the settings page which allow the user to have the option to automatically save posts to their phone Gallery. Then modify the Save button event handler in the Post Page to save the photo to the Gallery using the Plug-in. Test by insuring that the posts are saved to the gallery and your post page is still functional. 



**✨ Test your understanding**: What can you say about this app's testability? Is it easily testable? What can we do to improve this?



# References

1. [How To Upload Image(Cloudinary/Asp.Net Core)](https://medium.com/@karakizleyligul/how-to-upload-image-cloudinary-asp-net-core-e47023ff2431), [Karakiz Leyligul](https://medium.com/@karakizleyligul?source=post_page-----e47023ff2431--------------------------------), 2022, March 31. Last Access: 2024, March 20.

2. [Dot Net MAUI Configuration](https://github.com/jamesmontemagno/dotnet-maui-configuration/tree/master), [James Montemagno](https://github.com/jamesmontemagno), 2022, March 25. Last Access: 2024, March 20.

   