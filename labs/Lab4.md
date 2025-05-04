# Lab 4 - Authentication 🔑 API

1. 📝 **Worth:** 4%  
2. 📅 **Due:** Friday April 14, 2024 @End of class
3. 🕑 **Late submissions:** Not accepted.
4. 📥 **Submission:** In class

## 🎯 Objectives

In this lab, we will learn about authentication API to help you setup a similar mechanism in the final project.  Feel free to use your own project in this lab or use the starter project provided. 

- Learn how to use it in a MAUI project within an `MVVM` architecture
- Learn how to setup an authentication API
- Learn more about C# `event`s and how to use event based programming to reinforce separation of concerns.
- Learn more about the `IConnectivity` interface
- Learn more about authentication and connectivity exception handling. 

In this lab, we will examine `Firebase Authentication` and use it to authenticate users to use our `MauiFitness` app. Feel free to use any API in the project.

## 🗳️ Submission

- This lab will be submitted with an in-class demo, verifying the robustness of the implemented functionality and validating your understanding of the proposed architecture. 
- Questions will be asked throughout the demo, pay attention to the ✨questions in the instructions below.  



## ⚙️ Project Settings

For this lab, you have the option of using the starter code provided or use your project directly. 

- Ensure to a Google account, you can use the dummy email created earlier in the course.

- (optional) Use your section's GitHub classroom link:

  - [Section 1](https://classroom.github.com/a/yIAMrI0E)
  - [Section 2](https://classroom.github.com/a/7x_HCrro)

- If you choose to work with your own project, create a new branch in the project called Lab4 and work on this branch:

  - You must ensure that you have a login screen which acts as the landing screen of your app
  - You must ensure that you have at least a flyout or tab bar with a specified `Route` so that you can navigate to after a successful login. 

  

You will have to perform a few setups before starting the lab

- Install the lastest versions of the *NuGet* Packages which are compatible with .NET 8:
  - `Microsoft.Extensions.Configuration.Binder`

  - `Microsoft.Extensions.Configuration.Json`

- In the `MauiFitness` app, add a `"appsettings.json"` file 

- Included it as an embedded file

- Create a C# class called `Settings` class to parse all the configuration strings into a static `Settings` class

- Use this class to add any new API keys you will be using for this lab. 




### (Optional) Meals and workout Apis keys:

If you are working with the start-up app provided, the following steps are required to create the services classes. 

- Let's add API keys used by the `WorkoutService` and `MealService` class

- Create an https://api-ninjas.com/ account. You can use the dummy gmail account created earlier in the course.

- Go into MyAccount

- Click Show API Key

- Copy this **API key** 

- Add the following fields in `appsettings.json` and in the associated class `Config.Settings.cs`

  - `CaloriesApiKey: Your API key`
  - `CaloriesApiUrl`: `"https://api.api-ninjas.com/v1/nutrition?query="`
  - `ActivitiesApiKey: Your API key `
  - `ActivitiesApiUrl`: `"https://api.api-ninjas.com/v1/caloriesburned?activity="`



### Firebase authentication Api keys 🔥:

- Go to [Firebase](https://firebase.google.com/)

- Login using a Google account (use your dummy email and password created earlier in the course.)

- Click on Console: 

  <img src="images/labs_images/Lab4/firebase_console.png" height=300/>

- In the welcome page, click on `Create a Firebase project`

- <img src="images/labs_images/Lab4/firebase_new_proj.png" height=300/>

- Project Name: **MauiFitness** or you own project

- Confirm the project creation

- Setting Authentication

- Once the project is created, you will land in the main Firebase dashboard. 

- Scroll down and click on the `Authentication` block. 

  <img src="images/labs_images/Lab4/firebase_auth.png" alt="Snap shot" height="200"/>

- Click on `Get Started` to enable authentication and display the list of providers.

- Firebase Authentication provides backend services, easy-to-use SDKs, and ready-made UI libraries to authenticate users to your app. It supports authentication using passwords, phone numbers, popular federated identity providers like Google, Facebook and Twitter, and more. 

- In this lab, we will utilize it with a native provider via `Email/Password`. 

- Click `Email/Password` > `Enable` > `Save`

- The `MauiFitness` app does not include a registration functionality, so we will need to create user accounts manually.

- Go to the `Users` tab

- Add two users manually:
  - First user: an account for you (add your email and a password)
  - Second user: for the teacher to access your app
    - email: teacher@jac.ca
    - password: test@1234


> **Notes for the Project**  
>
> - As you may have noticed, Firebase provides options for the user to sign up with an email address or via an email link. You may use any of the options for the project. 
> - You may use other providers such as Google as well.
> - Feel free to use pre-built UI libraries for login and registration pages.

### Adding the keys to `Settings` and `appsettings.json`

- Add the following values as public variables

  - `FirebaseAuthorizedDomain`

    - Get value from Firebase portal >Authentication > `Settings` 
    - Under Authorized domains > copy the Firebase App domain (usually second entry)

    Example: `mauifitness-?????.firebaseapp.com`

  - `FirebaseApikey` 

    - Get value from Firebase portal > Left Navigation > Click on the gear icon ⚙️ next to `Project Overview` 
    - Select `Project Settings` > Under `General` tab > Web API Key

    

###  Preparing App to use Firebase API

- Right-click on the solution and choose `Manage NuGet Packages`

  - Go to `Browse` tab > Search for `FirebaseAuthentication.net`.
  - Install latest version `4.1.0`. (Tested to be working with `MAUI` & `.NET 8.0`)

- In the `Config/Settings.cs`, add the following strings:

  ```csharp
  namespace MauiFitness.Config
  {
      public class Settings 
      {
  		//...other api keys or secrets
          
          // Firebase authentication 
          public string FireBaseApiKey { get; set; }
          public string FireBaseAuthDomain { get; set; }
  
      }
  }
  
  ```

  

  ## Authentication Service 🪪  

  To encapsulate the logic of authentication, we will use a separate class which our models or view models can eventually use. 

- In the existing `Services` folder

  - Create a class `AuthService` that will use the authentication we have setup on the Firebase portal.

  - We will pass the `Settings` class through the constructor of this service class. 

  - As discuss earlier, we will only use an `EmailProvider` for authentication. We need to create a client that accepts   an email provider. 

    📌 The code below is based on the NuGet package [documentation](https://github.com/step-up-labs/firebase-authentication-dotnet) sample code. Check the sample code as it provides pre-built login webpage, which includes a registration link and possibility to login with any of the 3rd party providers.

    ```csharp
    public class AuthService
    {
        private Settings _settings;
        public FirebaseAuthClient Client { get; private set; }
        public UserCredential UserCredential { get; private set; }
        
        public AuthService(Settings settings)
        {
    
            _settings = settings;
            // Creating the auth client usind the configuration strings. 
            var authConfig = new FirebaseAuthConfig()
            {
                ApiKey = _settings.FireBaseApiKey,
                AuthDomain = _settings.FireBaseAuthDomain,
                Providers = new FirebaseAuthProvider[]
                {
                    new EmailProvider()
                }
            };
            Client = new FirebaseAuthClient(authConfig);
        }  
    }
    ```

  - The `FirebaseAuthClient` provides different methods to assist in the login process:

    - `SignInWithEmailAndPasswordAsync("a username", "a password")` attempt to login using the provided username and password.
    - Check the sample code on [GitHub](https://github.com/step-up-labs/firebase-authentication-dotnet).

  - Once authenticated make sure to save the returned `UserCredential` in the `UseCreds` property in the `AuthService` class. We need to use the authentication later to access the database.

  - To instantiate the `AuthService`, we will use the Maui Dependency Injection service builder

  - `.Net Maui` provides a built-in functionality to use `dependency injection` by either creating singleton or transient instance of a class. 

    - When object is created as a `Singleton`, the app will create a single instance of the object which will be remain for the lifetime of the application.
    - When object is created as a `Transient `, a new instance of the object will be created when requested during resolution. Transient objects do not have a pre-defined lifetime, but will typically follow the lifetime of their host.

  - The `builder` object has a `Services` property  that to register any object that is needed in app. 

    - Depending on the needs of your application, you may need to add services with different lifetimes, either singleton or transient. 
      - `AddSingleton<T>`
      - `AddTransient<T>`

  - We need to ensure that a **single instance** of the `AuthService` is created, therefore let's create the settings and register a singleton as shown below:

    ```csharp
                var a = Assembly.GetExecutingAssembly();
                var stream = a.GetManifestResourceStream("MauiFitness.appsettings.json");
    
                var config = new ConfigurationBuilder()
                            .AddJsonStream(stream)
                            .Build();
                var settings = config.GetRequiredSection(nameof(Settings)).Get<Settings>();
    
                builder.Services.AddSingleton(new AuthService(settings));
    ```

    

  

  ## Login View 🪟

  - Go to `LoginPage.xaml.cs` :

  - Ensure that two entries are available, one for the `Email` and one for the `Password`. 

  - Ensure the password text is hidden as the user types it in (set `IsPassword` to `True`)

    

  ### Change Login Navigation Behaviour

  Currently the login page is a tab and will not prevent the user from accessing the app. We need to change the behavior to have a landing page, which is the login page. Once logged in, we navigate to the app tabs:

  ​    <img src="images/labs_images/Lab4/img03.png" alt="Snap shot" height="300" class="inline-img"/>

  - The app should have only two navigation routes: `Login` and `Home` (the app tabs). 
  - Head to `AppShell.xaml` to make the changes:
    - Ensure the landing page has a `Route`, in this case "Login"
    - The tab bar should have a `Route`, "Index"


  ```xaml
  <Shell ...>
      <!-- Landing Page -->
      <ShellContent Route="Login" ContentTemplate="{DataTemplate views:LoginPage}" />
  
      <!-- App Tabs: note the use of FlyoutDisplayOptions="AsMultipleItems"  -->
      <TabBar Route="Index" FlyoutDisplayOptions="AsMultipleItems">
          <ShellContent Title="Workouts" ContentTemplate="{DataTemplate views:WorkoutsPage}" 
                        Icon="gym.png"/>
          <!-- Add the other tab pages .... -->
          
          <!-- Add the login page to be used to display account info and to logout-->
          <ShellContent Title="Account" ContentTemplate="{DataTemplate views:LoginPage}"
                        Icon="home.png"/>
      </TabBar>
  </Shell>
  ```

  - In the above setup, the `LoginPage.xaml` is used twice. We need to insure that only **a single instance** of it is created.

    - Go to `MauiProgram.cs` to register a singleton of the `LoginPage` and all the other pages since we only need a single instance of each. 

      

  ```csharp
  public static MauiApp CreateMauiApp()
  {
      var builder = MauiApp.CreateBuilder();
      ...
  
      builder.Services.AddSingleton<LoginPage>();
      builder.Services.AddSingleton<WorkoutsPage>();
      builder.Services.AddSingleton<MealsPage>();
      builder.Services.AddSingleton<MeasuresPage>();
  
      return builder.Build();
  }
  ```


> Check `.Net Maui` dependency injection [documentation](https://learn.microsoft.com/en-us/dotnet/architecture/maui/dependency-injection) for more details.

- As you run the app, the user, should **only** be able to access the landing page:

  <img src="images/labs_images/Lab4/landingPage.png" alt="Snap shot" height="300" class="inline-img"/>

  After implementing the login functionality, the goal is to navigate to the `"Index"` route, so that the user can access the other page or go back to the login screen which will now only display a logout button:

  <div style="text-align:center;">
      <img src="images/labs_images/Lab4/img01.png" alt="Snap shot" height="300" class="inline-img"/>
  </div>
  
  
  
  


  ✨ **Test your understanding**: For the project, how can you customize the pages that appear to the user based on who they are?

  

  ## 🔗 View Model

  The authentication process is an **IO bound operation** and prone to errors. This will require some error handling and therefore some logic. To decouple this from the login screen, we will use a View Model. The role of this class will be to gather the email, and password inputted by the user, then communicate with the authentication service to attempt login. If the login fails, the view model will raise **events** which can then be used to display error messages to the user. 

  

  ✨ **Test your understanding**: What design principle are we trying to respect by implementing a View Model and why?

  A few things to keep in mind, the authentication will fail if: 

  - A wrong password or wrong username are provided.
  - A wrong API key is used and several other. 

  - To simplify the creation of View Models, .NET has create a community toolkit just for MVVM which autogenerates all the notification of `INotifyPropertyChanged` and much more.
  - Install the NuGet  package: 
    - `CommunityToolkit.Mvvm` , install the latest version compatible with .NET 8

  - Create a `ViewModels` folder (if not already existing), then add a `LoginViewModel.cs`

  - Make use of the `mvvm` toolkit by ensuring that your View Model is a `partial class` which inherits from the base class `ObservableObject`

    >  Note: This class implements the `INotifyPropertyChanged` interface, so no need to implement it twice.

  ```csharp
  using CommunityToolkit.Mvvm.ComponentModel;
  using CommunityToolkit.Mvvm.Input;
  namespace MauiFitness.ViewModels
  {
  	public partial class LoginViewModel : ObservableObject
  ```

  - Create a private `private readonly AuthService _authService;`

  - The `AuthService` will be injected through the constructor of the view model, this is called *constructor injection*:

    ✨ **Test your understanding**: What is the interest of using dependency injection through the constructor? 

    ```csharp
        private readonly AuthService _authService;
    	public LoginViewModel(AuthService authService)
        {
            _authService = authService;
        }
    ```

  - Create two private fields for the email and password which will be bound to the UI.

  - In the MVVM toolkit, the `ObservableProperty` attribute auto-generates the public property and all the *PropertyChanged* notifications (i.e. the `OnPropertyChanged` calls):

    ```csharp
        [ObservableProperty]
        private string _email;
    
        [ObservableProperty] 
        private string _password;
    ```

  - This generates two public properties `Email` and `Password` which can now be used in a two way binding with the view:

    - Modify the constructor of the Login screen to pass the View Model by *construction injection*:

      ```csharp
      public partial class LoginPage : ContentPage
      {
      	public LoginPage(LoginViewModel vm)
      	{
      		InitializeComponent();
              BindingContext = vm;
          }
      }
      ```

      

    - Modify the username and password entries to binding them with the `Email` and `Password`:

      ```xml
      <Frame Margin="10" BackgroundColor="Transparent" BorderColor="Transparent" x:Name="LoginView">
      	<VerticalStackLayout Spacing="10">
              <Entry Text="{Binding Email}" Placeholder="Username" PlaceholderColor="Black" BackgroundColor="Transparent"/>
              <Entry Text="{Binding Password}"  Placeholder="Password"  IsPassword="True"  PlaceholderColor="Black" BackgroundColor="Transparent"/>
      ...
      ```

    - Now, let's register this view model using the dependency injection service, in the `MauiProgram.cs`

      ```csharp
      builder.Services.AddSingleton<LoginViewModel>();
      ```

      

    <u>Back to the ViewModel</u>

  - To allow for login, we will create a private method `Login()` and generate a Command that can then be used in binding. For this, use the `RelayCommand` attribute before the method declaration:

    ```csharp
        [RelayCommand]
        private async Task Login()
        {
            //To complete
        }
    ```

    

  - This will create a public `LoginCommand` which can be bound to the Login button

  - In the LoginPage.xaml,  bind the Login button to this command

    ```xml
    <Button  
        Text="Login"
        Command="{Binding LoginCommand}"/>
    ```

    <u>Back to the ViewModel</u>

  - In this `Login()` method, you must call `_authService.Client.SignInWithEmailAndPasswordAsync(Email, Password)` to authenticate the user

  - Make sure to use a `try`-`catch` blocks.

    - Add multiple `catch` blocks each for specific exception type.
    - Start with more specific exception, then the general one.
    - Firebase exception type is `FirebaseAuthException` which has a `Reason` property which contains the reason of failure.
    - Log the `Reason` in the console

    ```csharp
    try		
    {
        ...   
    }
    catch (FirebaseAuthException ex)
    {
        ...
    }
    catch (OtherExceptionType ex)
    {
        ...
    }
    catch (Exception ex)
    {
        ...
    }
    ```

    

  - Check if internet connectivity is available. Authentication will fail if there is no internet.

    - `.NET MAUI App` Connectivity [documentation](https://learn.microsoft.com/en-us/dotnet/maui/platform-integration/communication/networking?view=net-maui-7.0&tabs=android).

  - Upon successfully logging in, use shell navigation to navigate to the "Index" route:

    ```csharp
    await Shell.Current.GoToAsync("//Index");
    ```

    

### Raising error events 

  We would like to bubble up the errors to the View so that it can be displayed to the user. One possible way of achieving this is to create error labels that are bound to the UI.  Another approach is to Display the error messages from within the View Model. This approach though violates the principle of separation of concerns. 

  The approach suggested in this lab is to raise public events from the View Model and have the View subscribe to each event by implementing event handlers. The View can then handle those events in whichever way "it" wants.

  In the `LoginViewModel.cs`, create the following public events:

  ```csharp
  public event EventHandler LoginSuccessful;
  public event EventHandler<string>? LoginFailed;
  public event EventHandler<string>? UnknownErrorOccured;
  public event EventHandler<string>? InternetDisconnected;
  public event EventHandler LogoutSuccessful;
  ```

  Events of type `EventHandler<string>` will have event handler which expect a string message to be sent and will have the following signature. This will be useful to display the error message or exception:

  ```csharp
  private async void DisplayAuthError(object? sender, string? message)
  ```

  Whereas the default handle has this signature:

  ```csharp
  private void DefaultHandler(object? sender, EventArgs args)
  ```

  In the `LoginPage.xaml.cs` : 

  - Create event handlers for each of these events 
  - Connect the event and event handlers, for example:

  ```csharp
  public LoginPage(LoginViewModel vm)
  {
      InitializeComponent();
      BindingContext = vm;
      vm.LoginFailed += DisplayAuthError;
  ```

  

  - Display an alert if the login was successful (upon receiving an `SucessfulLogin` event)

    

  - <div style="text-align:center;">
          <img src="images/labs_images/Lab4/img01.png" alt="Snap shot" height="300" class="inline-img"/> </div>

  - Additionally,

  - On a successful login:

    - Set the visibility of the login container to `false` to hide it.
    - Set the visibility of the out container to `true` to display it on the page.
      - Display the username and user ID (using `AuthService.UserCreds.User.Uid`)
      - You can bind it to a property set in the View Model. 

  

  <div style="text-align:center;">
     <img src="images/labs_images/Lab4/img04.png" alt="Snap shot" height="300"/>
  </div>




  - Upon an `AuthenticationFailed` event being raised, 
    - Display an Alert showing the error message (for debug purposes)

  

  <img src="images/labs_images/Lab4/img02.png" alt="Snap shot" height="300" class="inline-img"/>

  

- Upon an `InternetDisconnected` event being raised,

  - Display an Alert showing a pop saying there is no Internet

- Upon an `UnknownErrorOccured` event being raised,

  - Display that an "unknown error occurred" and display the error message (for debug purposes)



### Logout Functionality



- Apply the following UI addition\modifications to the `LoginPage.xaml`:

  - Login Container: wrap the entries for the username and password, the login image button and error label with a container, such as `Frame`, `StackLayout` or a `Grid` and provide it with an `x:Name` value.

    

  - Logout Container: add another container below the above container that includes the following:

    - A label to display the user details.
    - A button to logout with a click event handler.
    - Assign an `x:Name` value to the container. 
    - Hide the container by using `IsVisible="false"`

  


### In the View Model:

  - Create a `LoginOut` method and use the `[RelayCommand]` attribute to turn it into a command
  - Bind the command to the Logout button
  - Use the `Client` property to logout `AuthService.Client.SignOut()`
    - Use `try`-`catch` blocks to catch any possible exception.
    - Upon successful logout, raise a public event which can be connected to an event handler in the view
    - Finally, navigate to the login page using the route `await Shell.Current.GoToAsync($"//Login");`

## In the View

  - Upon receiving a successful logout event:
    - Clear the username and password entry fields.
    - Swap the containers visibility to show the login container and hide the logout container.

## Testing 

  - Test the login and logout functionality of the app.
    - Login using the created username and password.
    - Go to the last tab `Account`.
    - Click Logout. 
    - The app should navigate to the login page. (tabs will not be available after you logout)



# Lab4 - Part 2

## Objectives

The next requirement in the app is to save the data.

To achieve this task, you need to implement all the CRUD operations.

> The CRUD operations stand for: *Create*, *Read*, *Update* and *Delete*.

The `MauiFitness`app should add (create), view (read), update and delete workout and meal items.

- We will setup a cloud base database using Firebase to save the dat and perform the CRUD operations.
- The create operation has been developed in milestone 1, but it will be updated here.



## Firebase Realtime Database

The Firebase Realtime Database is a NoSQL cloud base database. It saves data in tree structure using `Json` format. Each object is assigned a unique identifier, a key, which is used for the update and delete operations.

**Example:**

```json
Meal
-NvDPv2vDVpsM8ioM33p
    Calories:386
    Description:"cereal"
    Name : "Breakfast"
    Time : "2024-04-11T09:00:00"
-NvDPyz-cl8ezW8qjfZk
    Calories:262.9
    Description:"Pizza"
    Name:"Lunch"
    Time:"2024-04-11T13:00:00"
```

The installed `FirebaseDatabase.net` NuGet package has a `Xamarin.Forms` [sample project code](https://github.com/step-up-labs/firebase-database-dotnet/tree/master/samples/XamarinForms/XamarinForms). The proposed implementation below is based on the sample code for the offline database example. This sample code will you in building a database for your project.



If you do not have a use case for a database in your project, you can use the `MauiFitness` repo for this part of the lab. Fork **THIS** following repo which had the first part of the lab completed: 

the following steps are required to create the services classes. 

- Let's add API keys used by the `WorkoutService` and `MealService` class

- Create an https://api-ninjas.com/ account. You can use the dummy gmail account created earlier in the course.

- Go into MyAccount

- Click Show API Key

- Copy this **API key** 

- Add the following fields in `appsettings.json` and in the associated class `Config.Settings.cs`

  - `CaloriesApiKey: Your API key`
  - `CaloriesApiUrl`: `"https://api.api-ninjas.com/v1/nutrition?query="`
  - `ActivitiesApiKey: Your API key `
  - `ActivitiesApiUrl`: `"https://api.api-ninjas.com/v1/caloriesburned?activity="`

### Setup - Firebase Realtime database

- Go to https://console.firebase.google.com/

- In the welcome page, click on the **MauiFitness** project. (Created in the previous Lab`Authentication`)

- From the menu (left), click on `All Products` > `Realtime Database` > Click `Create Database` and choose the following settings:

  - Region: `United States (us-central1)`
  - Security rules: `test mode`
  - Once done click `Enable`

- From the Realtime Database page, get the database link.

  - Copy the database URL link, should look like:

    🔗 [https://mauifitness-?????-default-rtdb.firebaseio.com/](https://mauifitness-/?????-default-rtdb.firebaseio.com/)

  - Add the value to the `appsettings.json` 

  - Add an attribute to `Settings` class in the `MauiFitness` app 

    - Do not forget to have it as: `public` 

- Right-click on the solution and choose `Manage NuGet Packages`

  - Go to `Browse` tab > Search for `FirebaseDatabase.net`.

    - Install latest version `4.2.0`. (Tested to be working with `MAUI` & `.NET 7.0`)

    

####  `IHasUKey` and the `IDataStore<T>`  interfaces

In efforts of avoiding code repetition, we will introduce some interfaces that will allow us to treat objects as the interface to help is the CRUD operations.

In the `MauiFitness` app, create a folder called `Interfaces`.

- Create an interface called `IHasUKey` in the `Interfaces` folder:

  ```csharp
  public interface IHasUKey	
  ```

  - Used to ensure that the saved model class has a property when saving to the database.
  - Has public string property `Key` with a getter and setter.

- Implement the `IHasUKey` interface in both model classes `Workout` and `Meal`.

  - Add a private string property `key`
  - Use it within the getter of the public property `Key` 
  - In addition to the currently implemented `Fody` `PropertyChanged`

- For the `Workout` item, update the `Copy()` method to include the `Key`. This method is important to ensure that we can create deep copies of required in the edit form of the `WorkoutPage`.

- Create an interface called `IDataStore<T>` in the `Interfaces` folder:

  ```csharp
  public interface IDataStore<T> 
  ```

  - Will be used by the database service that we will create to provide CRUD methods:

  - Create operation:

    ```csharp
    Task<bool> AddItemAsync(T item); //Create operation
    ```

  - Read operation:

    ```csharp
     Task<IEnumerable<T>> GetItemsAsync(bool forceRefresh = false); //Read operation
    ```

  - To bind the items to a collection of items:

    ```csharp
    public ObservableCollection<T> Items { get; } //local list of items
    ```

  - Update operation:

    ```csharp
     Task<bool> UpdateItemAsync(T item); //Update operation
    ```

  - Delete operation:

    ```csharp
    Task<bool> DeleteItemAsync(T item); //Delete operation
    ```

 **✨ Test your understanding**   What is the interest of having the `Items` property? 

#### `DatabaseService<T>`

Similarly to the `AuthService`, we will create a class that contains all the functionality provided by the Firebase database client. This class should not be static and is created in a generic form so that we can create databases for both `Meal` and `Workout` and possibly other objects. 

- In the `Services` folder, create `DatabaseService<T>` class that implements `IDataStore<T>` interface.

- The class is created in a generic form to allow us to operate both on `Workout` and `Meal` objects.

- In the `Services` folder, create `DatabaseService<T>` class that implements `IDataStore<T>` interface.

- The class is created in a generic form to allow us to operate both on `Workout` and `Meal` objects.

- **Class header**

  ```csharp
  public class DatabaseService<T> : IDataStore<T> where T : class, IHasUKey
  ```

  - This will ensure that any used `T` class has implemented the `IHasUKey` interface and hence has the `Key` property.

- **Private fields**

  - ```csharp
    private readonly RealtimeDatabase<T> _realtimeDb;
    ```

    - Firebase database client which will be use to synchronize the data online.
    - Set as `readonly` to avoid reinitializing it at any time in the app life cycle.

- **Class Constructor**

  - ```csharp
    public DatabaseService(Firebase.Auth.User user, string path, string BaseUrl, string customKey = "")
    {
        FirebaseOptions options = new FirebaseOptions()
        {
            OfflineDatabaseFactory = (t, s) => new OfflineDatabase(t, s),
            AuthTokenAsyncFactory = async () => await user.GetIdTokenAsync()
        };
        var client = new FirebaseClient(BaseUrl, options);
        _realtimeDb =
            client.Child(path)
            .AsRealtimeDatabase<T>(customKey, "", StreamingOptions.LatestOnly, InitialPullStrategy.MissingOnly, true);
    
    
    }
    ```


​            
​    
​    - Requires an authentication token to access the database, which can be acquired from `AuthService` after the user logs in.
​    - Path: a location where to store the data object on the cloud. The easiest implementation is to pass the class name. 
​      - Example: a `Workout` object will be saved under the path of the same name using `nameof(Workout)` 
​      - Will be discussed later in the Repo section.
​    - `BaseUrl` acquired from the `ResourceStrings`
​    - `customKey` a custom string which will get appended to the file name. (not needed in this app)
​    - Note some of the offline database `_realtimeDb` initialization options. These options are enums and can be changed based on the app needs.
​      - `StreamingOptions.LatestOnly` 
​      - `InitialPullStrategy.MissingOnly`

**Interface members `IDataStore<T>`** 

*Note: add `try-catch` blocks for all IO operations.* 

- `AddItemAsync`

  - Uses `_realtimeDb.Post(item)` method to add data to database.
  - This method returns a key which must be used to assign a key to the added item.
  - Code is provided below because of its unconventional approach.
    - When adding a new item to the database, the assigned unique identifier will be returned.
    - We want to save that key in the same object to use it later for updating and deleting.

  ```csharp
  public async Task<bool> AddItemAsync(T item)
  {
      try
      {
          item.Key = _realtimeDb.Post(item); //returns the unique key 
          
          _realtimeDb.Put(key, item); //Update the entry in the database to maintain the key
          Items.Add(item); //place new item in the observable collection for UI display
      }
      catch (Exception)
      {
          return await Task.FromResult(false);
      }
      return await Task.FromResult(true);
  }
  ```

- **✨ Test your understanding**   Why are the CRUD methods asynchronous? 

- `UpdateItemAsync`

  - Uses the `Put` method in the `_realtimeDb` to update.

    *Hint: to update, you need the key; get the item key using `IHasUKey` interface with the `as` keyword.* 

  - Do not forget `try-catch` blocks.

- `DeleteItemAsync`

  - Uses the `Delete` method in the `_realtimeDb` to delete.
  - Remove from the observable collection as well.
  - Do not forget `try-catch` blocks.

- `GetItemsAsync`

  - The following implementation is taken from the sample code. 
  - If the offline database is empty, get data from the cloud.
    - Empty because this is the first run of the app.
    - Empty because the app is installed on a new device.

  ```csharp
  public async Task<IEnumerable<T>> GetItemsAsync(bool forceRefresh = false)
  {
      if (_realtimeDb.Database?.Count == 0)
      {
          try
          {
              await _realtimeDb.PullAsync();
          }
          catch (Exception)
          {
              return null;
          }
      }
      IEnumerable<T> result = _realtimeDb.Once().Select(x => x.Object);
      return await Task.FromResult(result);
  }
  ```

- `Items`

  - This is a tricky task. You need to call a asynchronous method in the getter of the property to fill the observable collection, but the property cannot be marked as `async`. (C# restriction)
  - A work around is to create a `Task` to make the asynchronous method call and use the synchronous `Wait()` method. 
  - But since `Wait()` will simply return a void, we must use a helper method `LoadItems` which will assign the `_items` . And in the property getter, we call the helper method using `Task.Run`:

  ```csharp
  private ObservableCollection<T> _items;
  public ObservableCollection<T> Items
  {
      get
      {
          if (_items == null)
              Task.Run(() => LoadItems()).Wait();
          return _items;
      }
  }
  private async Task LoadItems()
  {
      _items = new ObservableCollection<T>(await GetItemsAsync());
  }
  ```

✨ **Test your understanding**: Are `Wait()` and `await` the same thing? 



## Data Repo

Currently the data repo contains two simple `ObservableCollection`s of `Meal` and `Workout` items. In this step, you will utilize the `DatabaseService<T>` class to create two database instances: `WorkoutDb` and `MealDb`

- The `DateRepo` class will provide properties to utilize the `DatabaseService` class. 

  ```csharp
  public class FitnessRepo
  {
      private DatabaseService<Meal> mealsDb;
      public DatabaseService<Meal> MealsDb
      {
          get
          {
              return mealsDb ??= new DatabaseService<Meal>(...);// complete this
          }
      }
  }
  ```

  - Notes 
    - The generic class `DatabaseService` is created to save workout objects, hence `<Meal>` class type passed to the declaration.
    - `??=` in the getter will check if the backing field is null to initialize the database object. (This same logic is used in the `App.xaml.cs` class to instantiate the repo).
    - Authenticated user is passed to get the authentication token (use the `AuthService.UserCreds` class)
    - The path to save is using `nameof(Meal)` (as discussed earlier)

- Modify the `event` handler's in the `MealsPage` and `MealsForm` to reflect this change

- Run the app.

- At this point you should be able to add `Meal` and they should appear within the firebase console browser:

  - Click: Firebase > Projects> MauiFitness > More Products > Realtime Database 

✨ **Test question**: The data repo currently depends on the `AuthService` class. What would be a better design to increase the testability of this class? What concept have we seen in the past that could help with this?

- Repeat the process for the `Workout` class.
- Test the Add, Delete and update operations to make sure your Realtime database is working properly.

✨ **Test your understanding**: What happens if you modify an item from the firebase console page?





