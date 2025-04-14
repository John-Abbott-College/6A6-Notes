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

    

  ## Raising error events 

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
        <img src="images/labs_images/Lab4/img01.png" alt="Snap shot" height="300" class="inline-img"/>

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



## Logout Functionality



- Apply the following UI addition\modifications to the `LoginPage.xaml`:

  - Login Container: wrap the entries for the username and password, the login image button and error label with a container, such as `Frame`, `StackLayout` or a `Grid` and provide it with an `x:Name` value.

    

  - Logout Container: add another container below the above container that includes the following:

    - A label to display the user details.
    - A button to logout with a click event handler.
    - Assign an `x:Name` value to the container. 
    - Hide the container by using `IsVisible="false"`

  


In the View Model:
-------

  - Create a `LoginOut` method and use the `[RelayCommand]` attribute to turn it into a command
  - Bind the command to the Logout button
  - Use the `Client` property to logout `AuthService.Client.SignOut()`
    - Use `try`-`catch` blocks to catch any possible exception.
    - Upon successful logout, raise a public event which can be connected to an event handler in the view
    - Finally, navigate to the login page using the route `await Shell.Current.GoToAsync($"//Login");`

#### In the View

  - Upon receiving a successful logout event:
    - Clear the username and password entry fields.
    - Swap the containers visibility to show the login container and hide the logout container.

## Testing 

  - Test the login and logout functionality of the app.
    - Login using the created username and password.
    - Go to the last tab `Account`.
    - Click Logout. 
    - The app should navigate to the login page. (tabs will not be available after you logout)