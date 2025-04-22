## Assignment 3 : Test Driven Design

- **Worth**: 6%
- 📅 **Due**: May 2nd, 2025 @23:59
- 🕑 **Late Submissions**: Deductions for late submissions is 10%/day. 
  *To a maximum of 3 days. A a grade of 0% will be given after 3 days.*

- 📥**Submission**: Submit through GitHub classroom.

## 🎯  Learning Objectives

- Refactor existing code

- Use an architectural patterns (MVVM)
- Run unit tests 
- Write unit tests 
- Perform manual tests



## Introduction

In this final assignment, you are tasked with refactoring the Maui Email app created in the previous assignment. That version focused on creating a mobile frontend for core email features like sending, downloading, and marking emails as favorites. However, the app wasn’t designed with testability or maintainability in mind — most logic lived inside event handlers, making development **frustrating** 😤.

Now, your goal is to refactor the `MainPage` (inbox), `ReadPage`, and `WritePage` to improve the app's structure and make it more testable. You’ll also need to write several unit tests to improve *test coverage*.

While unit tests won’t cover everything, they’re valuable for catching bugs and improving your code’s design. Avoid simply moving event handler logic into view models without rethinking the structure.

Finally, you should test out all the functionality manually on the target platforms:

- Android 
- iOS (alternatively- if you have a Mac device)



## Project Preparation

- Accept the `GitHub` classroom assignment:
  - [Section1](https://classroom.github.com/a/w67m-Xzl)
  - [Section2](https://classroom.github.com/a/FHCjbQrU)
  
- You can use your own assignment 2 code if you prefer, but a few modifications were added in the starter to help implementing the view models. 

- The `appsettings.json` file contains the Mail Config strings which you can fill out with our own email/password and name:

  ```json
  {
    "MailConfig": {
        
      //gmail configs strings...
        
      "Name": "YOUR NAME",
      "Email": "YOUR EMAIL",
      "Password": "GENERATED PASSWORD"
    }
  }
  ```

  



## Important notes about the starter

### Differences from Assignment 2

The starter code includes a few changes compared to the code from Assignment 2. Please take note of the following:

**Dependency Injection**

- All class instances are now created in `MainProgram.cs`, ensuring that only a single instance of each component exists — including the mail clients, mail service, mail configuration, and main page.

  ```csharp
              // Only one instance
  
  
              ImapClient imapClient = new ImapClient();
              SmtpClient smtpClient = new SmtpClient();
  
              //Disable SSL validation on Android
              if (DeviceInfo.Platform == DevicePlatform.Android)
              {
                  imapClient.ServerCertificateValidationCallback = (s, c, h, e) => true;
                  smtpClient.ServerCertificateValidationCallback = (s, c, h, e) => true;
              }
  
              var a = Assembly.GetExecutingAssembly();
              var stream = a.GetManifestResourceStream("MauiEmail.appsettings.json");
  
              var config = new ConfigurationBuilder()
                          .AddJsonStream(stream)
                          .Build();
              var emailConfig = config.GetRequiredSection(nameof(MailConfig)).Get<MailConfig>();
              builder.Services.AddSingleton<IMailConfig>(emailConfig);
              builder.Services.AddSingleton<MailKit.IMailTransport>(smtpClient);
              builder.Services.AddSingleton<MailKit.IMailStore>(imapClient);
  			builder.Services.AddSingleton<IEmailService, EmailService>();
              builder.Services.AddSingleton<MainPage>();
  ```

- When you're ready to add the view models, be sure to register them with the dependency injection service.

- For the `ReadPage` and `WritePage`, they should be instantiated *on demand*, that is each time the user wants to read or write an email. For this we will use a `Transient`creation pattern:

  ```csharp
              // Creates a copy when requested
              builder.Services.AddTransient<ReadPage>();
              builder.Services.AddTransient<WritePage>();
  ```

  

**Navigation**

- Page Routes registration, this programmatic way of registering routes is easier to maintain: 

  ```csharp
  public partial class AppShell : Shell
  {
      public AppShell()
      {
          InitializeComponent();
  
          Routing.RegisterRoute(nameof(ReadPage), typeof(ReadPage));
          Routing.RegisterRoute(nameof(WritePage), typeof(WritePage));
      }
  }
  ```

- Page navigation is done via the `Shell` :

  ```csharp
  await Shell.Current.GoToAsync(nameof(WritePage));
  ```

  

### Binding Views and View Models

To effectively bind the new properties and commands in your view models to UI elements, keep the following in mind:

- It's recommended to use `CommunityToolkit.Mvvm`'s `ObservableObject`, `ObservableProperty`, and `RelayCommand` attributes to simplify ViewModel creation. 

- However, if a public property includes custom logic in its getter or setter, you should implement it manually.

- These attributes auto-generate the necessary boilerplate code. For example:

  ```csharp
  [ObservableProperty]
  private string myProperty;  //Generates public MyProperty
  
  [RelayCommand]
  private async Task DoSomething() //Generates public DoSomethingCommand
  {
      
  }
  ```

   

- To enable XAML IntelliSense and proper binding, include the ViewModel namespace and specify the `BindingContext` directly in XAML:

  ```xml
  <ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
               xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
               x:Class="MauiEmail.MainPage"
               Shell.NavBarIsVisible="True"
               Shell.BackgroundColor="{StaticResource Primary}"
               xmlns:converters="clr-namespace:MauiEmail.Converters"
               xmlns:viewmodels="clr-namespace:MauiEmail.ViewModels"
               BindingContext="{x:Type viewmodels:MainViewModel}"
               Title="Inbox">
  ```

- Due to a known issue in .NET 8, the `SearchBar`'s `SearchCommandParameter` binding may not work as expected. As a workaround, bind the `Text` property of the `SearchBar` to a public `SearchString` property in the ViewModel.

- The `SwipeView` has a different binding context from their parents. To use commands defined in the View Model, use relative binding and use the `x:Type viewmodels:MainViewModel` as such:

  ```xaml
  <SwipeView>
      <SwipeView.LeftItems>
          <SwipeItems>
              <SwipeItem 
                  Text="Read"
                  Command="{Binding Source={RelativeSource AncestorType={x:Type viewmodels:MainViewModel}}, Path=MarkAsReadCommand}"
                  CommandParameter="{Binding .}"
                  BackgroundColor="AliceBlue"
                  >
  ```

- Set the `BindingContext` of each page to its corresponding view model:

  ```csharp
   public MainPage(MainViewModel vm)
   {
       InitializeComponent();
       BindingContext = vm;
   }
  ```

- ViewModels may raise events when errors occur. Subscribe to these events in your view and display appropriate alerts. For example:

  ```csharp
  public MainPage(MainViewModel vm)
   {
      //Previous lines
      vm.EmailNotFoundError += OnEmailNotFoundError;
  }
  private async void OnEmailNotFoundError(object? sender, EventArgs e)
  {
      await DisplayAlert("Unknown error occured", "The selected email seem to have been removed", "Ok");
  }
  ```

- It's best practice to name event handlers as `On` + `EventName`.

### Passing Data on Navigation

Since we're no longer using stack-based navigation to push the Read and Write pages, we now pass data using [Query Properties](https://learn.microsoft.com/en-us/dotnet/maui/fundamentals/shell/navigation?view=net-maui-9.0#pass-data).

For simple values, data can be passed directly in the route string using:

```csharp
Shell.Current.GoToAsync($"{route}?parameter={value}")
```

However, when passing more complex objects, it's better to use a dictionary:

```csharp

new ShellNavigationQueryParameters
    {
        { "MyProperty", ComplicatedObject }
    };
await Shell.Current.GoToAsync($"{route}", navigationParameter);
```

To receive it in the view model of the page being navigated to add a `QueryProperty` as such:

`[QueryProperty(nameof(MyProperty), nameof(MyProperty))]`



### Unit Tests

- An `xUnit` test project is provided to test the commands in the `MainViewModel`.

  These unit tests focus primarily on input validation, parsing, and error handling. They do **not** cover UI behavior, navigation, or error message display.

  You are still expected to manually test the app as you develop it. You're encouraged to add more unit tests to strengthen your code's robustness

- A few sample unit tests for the `WritePage` are included. You are required to write **at least three** additional tests to ensure recipient list parsing works correctly.

## Main View Model 📩

- Create a `MainViewModel `inside a `ViewModels` folder. 
- If you are using the Mvvm toolkit, this class should be a partial class inheriting from `ObservableObject`

#### Constructor

```csharp
public MainViewModel(IEmailService emailService)
```

#### Private fields

- `IEmailService emailService`
-  You might need additional fields to performing the search filtering. 

#### Public Properties

- `Messages`:  A collection of `ObservableMessages`
- `SearchString`: `string`

#### Public events

```csharp
public event EventHandler DownloadError;
public event EventHandler EmailNotFoundError;
public event EventHandler UnknownError;
```

#### Private methods

- `DownloadEmailsAsync()`

  - fetches the list of emails asynchronously 

  - sets the `IsLoading`flag to `true` while awaiting the fetch operation 

  - should be called in the constructor of the `MainViewModel` without awaiting

  - raises the event `DownloadError` in case of an exception

    

#### Commands

All the commands should be bound to the UI element that calls it (Button or Text). The Commands should handle errors and raise the event `UnknownError` in case of an exception. 

- `MarkAsFavoriteCommand(ObservableMessage email)`

  - Mark a given email as favorite/ not favourite 
  - You should test it to ensure the UI is refreshed after an email is marked as favorite
  - Should raise `EmailNotFoundError` if the provided email is not found in the list of emails.

- `MarkAsReadCommand(ObservableMessage email)`

  - Mark a given email as read/ not favourite 
  - You should test it to ensure the UI is refreshed after an email is marked as read
  - Should raise `EmailNotFoundError` if the provided email is not found in the list of emails.

- `SearchByStringCommand()` 

  - Performs a search using the public property`SearchString`

  - If there are results, **filters** **out** the `Messages` to only display the emails which match the search results

  - If the search results are null, the `Messages` should appear empty until the search bar is cleared.

    > Hint: Save the original list of emails in a separate collection. Use the setter of the `SearchString` to clear out the search results.  

- `DeleteEmailCommand(ObservableMessage email)`

  - Deletes an email from the list of emails. 
  - You should test it to ensure the UI is refreshed after an email is deleted
  - Should raise `EmailNotFoundError` if the provided email is not found in the list of emails.

- `TapEmailCommand(ObservableMessage email)`

  - Should raise `EmailNotFoundError` if the provided email is not found in the list of emails.
  - Navigates to the `ReadPage` using shell navigation. 

> Hint: The `ReadPage` has a route that correspond to "ReadPage".

- `WriteMessageCommand()`
  - Simply navigates to the `WritePage` using shell navigation. 



## Read View Model 📖

#### Constructor

```csharp
public ReadViewModel(IEmailService emailService)
```

#### Public Properties

- `bool IsLoading`: Flag set to `true` when the email is being downloaded in full.
- `ObservableMessage Email`: Bound to the UI to display the email content.

#### Public events

```csharp
 public event EventHandler ErrorOccured; 
```

#### Private Methods

- `LoadEmail()`
  - Method called in the constructor of the view model to mark the email as read, download the remaining parts of the email (`Body` and `HtmlBody`)
  - This method should raise the event `ErrorOccured` if an exception is caught
  - It should also set the `IsLoading` flag while the download process is ongoing.

#### Commands

- `ForwardEmail()`

  - Gets the email forward and navigates to the `WritePage` via shell navigation. The forwarded email should be passed as a query parameter to the write page.

  - Test this method manually by forwarding an email and ensuring that the app navigates to the correct page and displays the forwarded email.  

## Write View Model 🖆

#### Constructor

```csharp
public WriteViewModel(IEmailService emailService, IMailConfig config)
```

#### Private fields

- `IEmailService _emailService` : required to send the email

- `IMailConfig _mailConfig` : required to set the sender email address and name.

#### Public Properties

- `string Recipients`: bound to the recipients input entry 
- `bool SendingEnabled`: bound to the `Enabled` property of the send button
- `ObservableMessage EditEmail`: This property can be received as `QueryProperty` or could be null, in which case you should create a new instance.  It binds to the read page elements to display the email being written. 

#### Public events

```csharp
public event EventHandler MissingRecipient;
public event EventHandler MissingSubject;
public event EventHandler SendError;
public event EventHandler SendSuccess;
```



#### Commands

- `SendCommand()`
  - send the email while ensuring all the required fields such as the recipients, subject, and date of the email are set. 
  - In case of missing fields, this command should raise the `MissingSubject` or `MissingRecipient` events to be handled by the view. 
  - in case of error, this command should raise the `SendError` event

- `ParseRecipientCommand()`
  - Parses the `Recipients` property to extract **valid** email addresses:
    - Use a regex and the `MailboxAddress.TryParse()` to improve the parsing logic.
  - If there are valid recipient emails, the `EditEmail.To` property should be set and the sending should be enabled, otherwise it should be disabled. 
  - The user should also be able to erase the recipients which clears the `EditEmail.To` property.
- You must write unit tests for the `SendCommand()` and `ParseRecipientCommand()` to ensure they are correctly implemented. 

## Unit Tests

- Each class should have its own test file and Test class.
- The project uses `xUnit`, if you wish to set it up for your final project, here is a [video](https://www.youtube.com/watch?v=C9vIDLQwc7M&ab_channel=GeraldVersluis) explaining how
- To facilitate assertion, the project uses `Fluent Assertions` and `Moq` to mock up the various classes.
- You must use at least one repeatable test case (`Theory`) to test out the recipients parsing functionality **with various entries** 
- You must use at least one simple unit test (`Fact`) to validate the send button enabling/disabling. 
- Points will be awarded for how thorough the tests are. Prioritize creating multiple small tests over one big test. Ideally a test is F.R.I.S.T.  **F**ast, **I**solated, **R**epeatable, **S**elf-validating, **T**horough
- Here is a good [reference](https://mookiefumi.com/2019-10-29-unit-testing-the-way-i-test-my-viewmodels) to follow.





## References

1. Miguel angel Martin Hrdez, *unit Testing,the way I test my ViewModels*. October 2019. Access on Apr 2025. [Accessible Here](https://mookiefumi.com/2019-10-29-unit-testing-the-way-i-test-my-viewmodels)