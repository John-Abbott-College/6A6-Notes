---
layout: post
title: "Lecture 8 : Accessing and Saving Data"
permalink: /lectures/savedata/
categories: notes
---

[Demo made in class: Data](https://github.com/AppDevIII-W24-Code/Demos/tree/main/DemoData)

## Input/Output

Each operating system has its own way of managing storage, but .NET provides the `System.IO` which works on all the platforms and is useful to access files. 

- `File` class: static methods to create, delete, read and write to files.
- `Directory` class: static methods to create, delete, or enumerate the contents of directories.
- `Path` class: performs operations on String instances that contain file or directory path information.
- `Stream` subclasses: provides a sequence of bytes from a given file and provides a greater degree of control over file operations (such as compression or position search within a file).

# Preferences

Preferences are stored with a [String](https://learn.microsoft.com/en-us/dotnet/api/system.string) key. The value of a preference must be one of the following data types:

- [Boolean](https://learn.microsoft.com/en-us/dotnet/api/system.boolean)
- [Double](https://learn.microsoft.com/en-us/dotnet/api/system.double)
- [Int32](https://learn.microsoft.com/en-us/dotnet/api/system.int32)
- [Single](https://learn.microsoft.com/en-us/dotnet/api/system.single)
- [Int64](https://learn.microsoft.com/en-us/dotnet/api/system.int64)
- [String](https://learn.microsoft.com/en-us/dotnet/api/system.string)
- [DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime)

```csharp
// Set a string value:
Preferences.Default.Set("first_name", "John");

// Set an numerical value:
Preferences.Default.Set("age", 28);

// Set a boolean value:
Preferences.Default.Set("has_pets", true);
```

- To Get preferences: 

```csharp
string firstName = Preferences.Default.Get("first_name", "Unknown");
int age = Preferences.Default.Get("age", -1);
bool hasPets = Preferences.Default.Get("has_pets", false);
```



 	

# Loading Local Data



## Accessing Embedded files

Embedded resources are files included and packaged with your apps such as all the image button icons, project's images (inside  `/Resources/Images` folder). 

- Create or add a file in the project (for example in a `Files` folder)

- Open the *MSBuild Project File* (double click on the project name in VS Project Explorer)

- Add the resource file using a *build action*. 

  ```xml
  <ItemGroup>
    <EmbeddedResource Include="Files\MyFile.txt" />
  
    <!-- Images -->
    <MauiImage Include="Resources\Images\*" />
   
  </ItemGroup>
  ```

  > **Note**: 
  >
  > - You can use the * to read multiple files from a folder.
  >
  > - There exists other types of resources files for example CSS style definitions. Refer to [this](https://egvijayanand.in/2021/08/20/net-maui-manage-app-resources/) article.

- To access a file from the embedded resources:

  - Access assembly.

  - `GetManifestResourceStream` is used to access the embedded file using its Resource ID. 

    *Resource ID is the filename prefixed with the default namespace for the project it is embedded in.*

    for example: *`MauiApp4.Files.MyFile.txt`*

  ```csharp
  Assembly.GetExecutingAssembly().GetManifestResourceStream("MauiApp4.Resources.Files.MyFile.txt");
  ```



- If you wish to display to print the name of each embedded file:

  ```csharp
  string[] names =   
  	Assembly  
  	.GetExecutingAssembly()  
  	.GetManifestResourceNames();  
    
  foreach (var name in names)  
  {  
      Console.WriteLine(name);   //will print the name all assets, images, app icon, splash screen, custom fonts.
  }
  ```


## Reading from device storage

- The directory used in each platform to save the app data can be easily acquired using: `Environment.SpecialFolder`

```csharp
Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);    
```



## File Picker

`FilePicker`s is a class which enables selection of a file.

**Android**:

- Using a file picker requires some setup

- Add the following permissions in the Android Manifest:

  ```xml
  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" android:maxSdkVersion="32" />
  <!-- Required only if your app needs to access images or photos that other apps created -->
  <uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
  <!-- Required only if your app needs to access videos that other apps created -->
  <uses-permission android:name="android.permission.READ_MEDIA_VIDEO" />
  <!-- Required only if your app needs to access audio files that other apps created -->
  <uses-permission android:name="android.permission.READ_MEDIA_AUDIO" />
  ```

**iOS:**

- A similar setup is required to enable file picking on iOS via Apple's sandbox

- Refer to [this](https://learn.microsoft.com/en-us/dotnet/maui/mac-catalyst/deployment/publish-app-store?view=net-maui-8.0#add-entitlements) article to learn how to add *app entitlements* 

- Add the following entitlements:

  ```xml
  <key>com.apple.security.assets.movies.read-only</key>
  <true/>
  <key>com.apple.security.assets.music.read-only</key>
  <true/>
  <key>com.apple.security.assets.pictures.read-only</key>
  <true/>
  <key>com.apple.security.files.downloads.read-only</key>
  <true/>
  <key>com.apple.security.files.user-selected.read-only</key>
  <true/>
  <key>com.apple.security.personal-information.photos-library</key>
  <true/>
  ```



Since the operation of picking a file is `I/O Bound` (depends on the read speed of the storage device), the method to pick a file is called asynchronously `PickAsync` to avoid blocking the UI thread. 



**Picking a photo:**

- To use the `PickAsync` method, `PickOptions` are provided specifying the file type

```csharp
FileResult photoFile = await FilePicker.PickAsync(new PickOptions()
{
    FileTypes = FilePickerFileType.Images
});
```

To convert a `stream` to an `Image`:

```csharp
if(photoFile != null)
{
    var stream = await photoFile.OpenReadAsync();
    ImageSource imgSource = ImageSource.FromStream(() => stream);
    ContentImg.Source = imgSource; //ContentImage is the x:Name of the image in this code.
}
```

**Picking a text file:** 

- Text files do not have a default picker file type because they may vary from one OS to another.

- You have to define your own File type: 

  ```csharp
  var customFileType = new FilePickerFileType(
  new Dictionary<DevicePlatform, IEnumerable<string>>
  {
      { DevicePlatform.iOS, new[] { "public.text" } }, // UTType values  
      { DevicePlatform.Android, new[] { "text/plain" } }, // MIME type
      { DevicePlatform.WinUI, new[] { ".txt" } }, // file extension
      { DevicePlatform.macOS, new[] { "txt" } }, 
  }); 
  var result = await FilePicker.PickAsync(new PickOptions() 
  {
      PickerTitle="Please select a text file",
      FileTypes = customFileType 
  });
  if(result != null)
  {
      Stream stream = await result.OpenReadAsync();
      //...
  }
  ```

To convert a `stream` to a string :

```csharp
using(var reader = new StreamReader(stream))
{
    string fileContent = reader.ReadToEnd();
}
```



### Media Picker

- This special picker is designed for selecting pictures, videos or capturing pictures and videos using the device camera. 

##### Android:

- Include the following permissions in your android manifest	

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" android:maxSdkVersion="32" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" android:maxSdkVersion="32" />    
<!-- Required only if your app needs to access images or photos that other apps created -->
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
<!-- Required only if your app needs to access videos that other apps created -->
<uses-permission android:name="android.permission.READ_MEDIA_VIDEO" />
<!-- Required only if your app needs to access audio files that other apps created -->
<uses-permission android:name="android.permission.READ_MEDIA_AUDIO" />   


<queries>
  <intent>
    <action android:name="android.media.action.IMAGE_CAPTURE" />
  </intent>
</queries>
```

##### iOS:

- A similar setup is required to enable file picking on iOS via Apple's sandbox
- Refer to [this](https://learn.microsoft.com/en-us/dotnet/maui/mac-catalyst/deployment/publish-app-store?view=net-maui-8.0#add-entitlements) article to learn how to add *app entitlements* 
- Add the following entitlements:

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs access to the camera to take photos.</string>
<key>NSMicrophoneUsageDescription</key>
<string>This app needs access to microphone for taking videos.</string>
<key>NSPhotoLibraryAddUsageDescription</key>
<string>This app needs access to the photo gallery for picking photos and videos.</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>This app needs access to photos gallery for picking photos and videos.</string>
```

##### Taking a picture

```csharp
FileResult photoFile = await MediaPicker.Default.CapturePhotoAsync();
if(photoFile != null)
{
    var stream = await photoFile.OpenReadAsync();
    ImageSource imgSource = ImageSource.FromStream(() => stream);
    ContentImg.Source = imgSource;
}
```

# Saving Data locally

- Mobile operating systems have a file system with a hierarchical directory structure of folders and files.
- Every application is allocated a directory that the app can use to store data.
  - Stored data can be: text file, images, html files, json objects, etc.
- Local files in `.NET MUAI`  can categorized into:
  - Local Files saved on each target platform: but accessed using code in a .NET Standard library (shared project).
  - Embedded resources.



## Saving  files

As you save data in your app, you should me mindful of OS differences and how the resources will be managed. 

- `FileSystem.CacheDirectory`: This is a temporary cache used by the various platforms to save temporary data used in apps.
  - This directory will be managed in different ways based on the platform
  - Android: [`getCacheDir`](https://developer.android.com/reference/android/content/Context.html#getCacheDir()) which is automatically emptied by the OS when its missing space. Must not exceed a certain quota.
  - iOS: [`Library/Caches/`](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) which lasts longer than temporary directory, but similarly to Android is emptied regularly to free-up space.
  - For Windows this corresponds to `LocalCacheFolder`

- `FileSystem.AppDataDirectory`
  - Android : [`getFilesDir`](https://developer.android.com/reference/android/content/Context.html#getFilesDir()) which is a permanent storage that is backed up.
  - iOS: 

- You may also choose to overwrite a file on the storage: 

  ```csharp
   var customFileType = new FilePickerFileType(
       new Dictionary<DevicePlatform, IEnumerable<string>>
       {
           { DevicePlatform.iOS, new[] { "public.text" } }, // UTType values  
           { DevicePlatform.Android, new[] { "text/plain" } }, // MIME type
           { DevicePlatform.WinUI, new[] { ".txt" } }, // file extension
           { DevicePlatform.macOS, new[] { "txt" } }, 
       }); 
  
   var result = await FilePicker.PickAsync(new PickOptions() 
   {
       PickerTitle="Please select a text file",
       FileTypes = customFileType 
   });
   if(result != null)
   {
       FileName = result.FileName;
       FileOriginalPath = result.FullPath;
  
       Stream stream = await result.OpenReadAsync();
       SetFileContent(stream);
   }
  
  /// Some changes on the file.
  
  using FileStream outputStream = File.OpenWrite(FileOriginalPath);
  using StreamWriter streamWriter = new StreamWriter(outputStream);
  await streamWriter.WriteAsync(FileContent);
  ```

  

Let's go back to the social media app developed in the previous lecture. In the `NewFilePage.xaml.cs`  we have so far been able to take a picture and then passing this file to the `PostPage` via dependency injection. Let's now save this picture on the device, so we can access it later.  A new post is a task that might be eventually cancelled by the user. In this case we might want to save the picture temporarily:	

```csharp
public async void TakePhoto()
{
    if (MediaPicker.Default.IsCaptureSupported)
    {
        FileResult photo = await MediaPicker.Default.CapturePhotoAsync();

        if (photo != null)
        {
            // save the file into local storage
            string localFilePath = Path.Combine(FileSystem.CacheDirectory, photo.FileName);

            using Stream sourceStream = await photo.OpenReadAsync();
            using FileStream localFileStream = File.OpenWrite(localFilePath);

            await sourceStream.CopyToAsync(localFileStream);
        }
    }
}
```





## Serializing objects

Serialization is used when the state of an object has to be preserved in format that can eventually be retrieved from storage. It can also be useful for logging purposes. 

In this course, we will explore JSON serialization, but there exists other types.

There are several third party packages that provide out of the box functionalities to `serialize` objects into `Json` to save it into a text file and `deserialize` `Json` from text in object instances. 

**Next Demo in class...**



# Additional Resources 

[Microsoft's documentaiton on Preferences](https://learn.microsoft.com/en-us/dotnet/maui/platform-integration/storage/preferences?view=net-maui-8.0&tabs=android)

[Serialization](https://learn.microsoft.com/en-us/dotnet/standard/serialization/)



