# Assignment 2 : Maui Email

* **Worth**: 10%
* 📅 **Due**: March 17, 2025 @ 23:59.
* 🕑 **Late Submissions**: Deductions for late submissions is 10%/day. 
  *To a maximum of 3 days. A a grade of 0% will be given after 3 days.*
* 📥**Submission**: Submit through GitHub classroom.



### 🎯  Learning Objectives

- Explore the process of building a cross-platform app using an external library
- Create a model 
- Deepen understanding of event-based programming.
- Understand and use dependency injection. 
- Deepen understanding of data binding 
- Use converters
- (optional) Use templated views. Read more about [ContentView](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/contentview?view=net-maui-8.0)



## Introduction

In this second assignment, you are tasked with creating a user interface for the Email Client App which you started in the previous assignment. This app should allow a user to display a mailbox, reading individual emails, sending and deleting emails. This is the second milestone of the project; you will have to create the various views and models used by your app. 



## Project Preparation

- Accept the `GitHub` classroom assignment:

  [Section 1](https://classroom.github.com/a/ae7KCU1u)

  [Section 2](https://classroom.github.com/a/KV7H7m4W)

- Once your private repo is created, clone it to your computer.

- The repo should contains a  `.NET MAUI App`  starter code using .NET 8.0

- Copy the classes you created in the first part of this project. 

- In the cloned folder created the following folders and organize your classes accordingly:

  - **Services**
  - **Configs**
  - **Interfaces**
  -  **Views**
  - **Models**
  - **DataRepos**
  - **Converters**




## Overall Requirements

The Email app must:

- Display a list of emails.

- Offer a search function to search through the emails titles, contents and senders.

- Differentiate visually between Read/Unread emails 

- Differentiate visually between favorite/ non-favorite emails

- Offer a swipe functionality to perform operations on the mailbox.

- Offer tap functionality to display a detail view of the email

- Offer a view to write new emails

- Offer a functionality to forward emails

- Offer a functionality to send an email

  

**You do NOT have to implement:**

- Detect if a new email is received 
- Detect if an email is sent 
- Switch between various email folders (Archive, Delete, Sent)



## Model: `ObservableMessage`

Although the MimeKit framework provides the `MimeMessage` model, it contains too many properties and is not ideal for UI data binding and might slow down the app as the mailbox gets full. 

Instead, we will create a custom `ObservableMessage` class to serve as a lightweight data container suitable for data binding and that should support lazy loading to prevent slowing down the app when first launched. This can be down by firstly, fetching the message summaries from the mailing server. Start by fetching message summaries from the mail server, and only download the full message when necessary. It's advisable to modify the `EmailService` class while developing `ObservableMessage` to better understand their relationship.

Here are a few guidelines to implement this model. Feel free to design this model in a better way than what is suggested here. 

### Observable Object

We want to ensure that the model is observable so that other objects can subscribe to it. Namely, the Views. 

- Implement `INotifyPropertyChanged` from `System.ComponentModel`

- Define the `PropertyChanged` event.

- Install the **`PropertyChanged.Fody`** NuGet package to automatically generate property change notifications. This eliminates the need to manually invoke `OnPropertyChanged()` in setters, making the code cleaner and more maintainable.

- **UPDATE FEB 28**:

  - You must install the **Fody** NuGet package

  - This will automatically create a `FodyWeavers.xml` file at the root of the project folder.

  - Add the following property to this file:

    ```xml
    <Weavers>
      <PropertyChanged/>
    </Weavers>
    ```

  - Now, any class implementing the `INotifyPropertyChanged` interface will raise the `PropertyChanged` event in the setters of all of its public properties. [Read more here](https://github.com/Fody/PropertyChanged?tab=readme-ov-file#-propertychangedfody)


### Properties 

Various message properties such as the sender address, recipients, subject, etc. should be stored in this class. 

| Name         | Type                     | Description                                                  |
| ------------ | ------------------------ | ------------------------------------------------------------ |
| `UniqueId`   | `MailKit.UniqueId`       | A unique identifier for an email. Optional field.            |
| `Date`       | `DateTimeOffset`         | The date and time of sending the email                       |
| `Subject`    | `string`                 | The subject of the email                                     |
| `Body`       | `string`                 | The body/content of the email. This field must be optional, allowing for lazy loading. |
| `HtmlBody`   | `string`                 | The body/content of the email. This field must be optional, allowing for lazy loading. |
| `From`       | `MimeKit.MailboxAddress` | The email address of the sender                              |
| `To`         | `List<MailboxAddress>`   | The email addresses of the recipients                        |
| `IsRead`     | `bool`                   | A flag used to decipher between read and unread emails       |
| `IsFavorite` | `bool`                   | A flag used to mark some messages as favorites               |

### Constructors

As you modify the Service class, you'll learn that there are two ways of receiving a message , either through a summary `IMessageSummary` or a  full `MimeMessage`. This model should have **at least** two constructors to accommodate for these different use cases. 

- `public ObservableMessage(IMessageSummary message)`

  - the summary contains the `UniqueId`

  - `IMessageSummary.Envelope` contains the sender, recipient, subject, date information. 

  - To set the `IsRead` property, you may use:

    ```csharp
    IsRead = (message.Flags == MessageFlags.Seen);
    ```

    - The `Body` and `HtmlBody` should be `null` 

  > The sender `From` can be extracted using the following:
  >
  > ```python
  > From= (MailboxAddress)summary.Envelope.From[0];
  > ```

  - To set the `IsFavorite` property, use`MessageFlags.Flagged`

- `public ObservableMessage(MimeMessage mimeMessage, UniqueId uniqueId)`:

  - `MimeMessage` doesn't have a `UniqueId` field, the service class must pass this information to the constructor alongside message.
  - All fields can be extracted from the `MimeMessage`

- **UPDATE MARCH 10**:

  - The `MimeMessage` doesn't contain the `MessageFlags`. Keep both flags to default values. This constructor will only be required when downloading an email in full.




### **Methods**:

- `public ToMime()` : This message must return a `MimeMessage` from an `ObservableMessage`. 

  **UPDATE MARCH 10:**

- `public ObservableEmail GetForward()`: This method returns a new Email with a forwarded body and subject as such:

  

| Original Email                                               | Forwarded Email                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Subject: Meeting next week                                   | Subject: FW: Meeting next week                               |
| Body: Hello Team, we will have to meet urgently next week for the .... | Body: <br> ------------Forwarded message----------<br />From: regionalmanager@company.com<br>To: team@company.com<br>Hello Team, we will have to meet urgently next week for the .... |





## Email Service 

The mail service needs to be updated to allow for partial downloads of the emails. The `MailKit` library explains how to partially download messages using the  `FetchAllMessagesAsync()` method. Here is an example on how to use this:

📌[retrieve messages partially](https://github.com/jstedfast/MailKit/tree/master?tab=readme-ov-file#fetching-information-about-the-messages-in-an-imap-folder)

- Add the following methods to the `IEmailService` to fetch the summaries and convert them to `ObservableMessage`s :

```csharp
Task<IEnumerable<ObservableMessage>?> FetchAllMessages();
Task<IEnumerable<MimeMessage>> DownloadAllEmailsAsync();
```

- To download an entire email, use `GetMessageAsync()` which takes the `UniqueId` as input and returns the fully downloaded `MimeMessage`, then convert it into an Observable Message. Here is a sample code:

  📌[download a single message](https://github.com/jstedfast/MailKit/tree/master?tab=readme-ov-file#fetching-information-about-the-messages-in-an-imap-folder)

- Modify the following method so that it takes a `UniqueId` as an input parameter. 

  ```csharp
  Task DeleteMessageAsync(UniqueId uid);
  ```

- **You must add more methods to implement the following functionalities:**

  - Mark a message as Read
  - Mark a message as Favorite
  - Perform a search query on the list of emails, if the `From`, `Subject` or `Body` contains a given search string. Your search query should return the `UniqueId` 

  📌[Set a message flag](https://github.com/jstedfast/MailKit/tree/master?tab=readme-ov-file#setting-message-flags-in-imap)📌[Perform a search query](https://github.com/jstedfast/MailKit/tree/master?tab=readme-ov-file#searching-an-imap-folder)



### Testing 

- Create a public static instance of the `EmailConfig` class in the `App.xaml.cs`.
- Create a public static instance of the `EmailService` class in the `App.xaml.cs`
- Ensure that there are no compilation errors, for now. 

### Testing on the Android 

- The android emulator will throw a `MailKit.Security.SslHandshakeException` saying that the server's certificate could not be verified. This is because, the Root CA doesn't seem to be installed by default on the Android emulator. 
- This is a runtime conditional, verifying the current platform and disabling the CA validation step. 
- Feel free to explore better options.

```python
// Disable SSL validation on Android
if (DeviceInfo.Platform == DevicePlatform.Android)
{
    imapClient.ServerCertificateValidationCallback = (s, c, h, e) => true;
    smtpClient.ServerCertificateValidationCallback = (s, c, h, e) => true;
}
```

> I didn't spend much time trying to setup a CA and a proper Server Certificate call-back, this is why I went with `MailKit`'s workaround, by setting the certificate call-back to true (not checking the validity of the certificate). This isn't the most secure option of course, and is not recommended for production. But, for the purpose of getting this to work done within the timeframe we have, this is an acceptable workaround.



## UI Design 

1. Create the following pages:

   - `InboxPage`  (which will display emails in the inbox)
   - `ReadPage` (displays the details of the email)
   - `WritePage` (displays a form to write a new email)
   - Remove the `MainPage`


## Inbox Page

**MARCH 10:**

**Code behind:**

⚠️ In the constructor of the Inbox page, you need to fetch emails using `App.EmailService.FetchAllMessages()`, which was implemented earlier. However, there's a challenge—constructors in C# cannot be asynchronous, meaning you cannot `await` this call inside the constructor.

So, what are your options? Should you block the thread to load emails? **Absolutely not!** Blocking the thread can freeze the UI and degrade performance.

What about starting a new thread? That approach introduces risks, such as threading conflicts and unhandled exceptions.

The best alternative is to use a private helper method and call it from the constructor, like this:

```csharp
public InboxPage()
{
    InitializeComponent();
    DownloadEmails(); //Not awaited in the constructor
    BindingContext = this;
}
private async void DownloadEmails()
{
    var messages = await App.EmailService.FetchAllMessages();
    //complete the construction of the ObservableCollection...

} 
```

It might seem unusual to call an `async` method without awaiting it, and indeed, this can be risky—especially if something tries to access the email list before it has finished loading. However, in this specific case, the list won’t be accessed until it’s displayed.

For a deeper dive into handling `async` calls in constructors, check out this [blog post](https://www.damirscorner.com/blog/posts/20221021-AvoidAsyncCallsInViewmodelConstructors.html).

**UPDATE MARCH 12**

**Search event handler**

- You should use the collection view's Items Source to modify the list of emails displayed in the InboxView

- If the search box is cleared, the displayed list of emails should reverted back to the original list.

- When performing a search, the email service might throw the following exception:

  *The ImapClient is currently busy processing a command in another thread. Lock the SyncRoot property to properly synchronize your threads.*

- This error occurs if the service is solicited too often because a search is performed on the search's `TextChanged` event. As a user types, each character typed will trigger the search and therefore cause this error.

- To reduce the risk of this happening, you can perform the search only when the search is complete (user hits search button)

- A better solution, would be to use a `bool` flag to keep track of the status of the search and a conditional statement before running a new query. (ex: `IsSearching` or `IsProcessing`)

  ```csharp
  // inside the event handler
  if(!IsLoading)
  {
      IsLoading = true;
      var uids = await App.EmailService.SearchByStringAsync(SearchEntry.Text);
      IsLoading = false;
  }
  ```

  

**XAML** 

1. In the Inbox view, you should include an `SearchBar` within the `NavBar`, this will serve as a search bar to filter out emails:

   > Hint: You can use`Shell.TitleView` to include your search bar as shown in the screenshots. But you are free to implement a better UI design.

   

2. In the inbox view, use a `CollectionView` to display the collection of emails.

3. Use a `<SwipeView>` as a `DataTemplate` of the `CollectionView.ItemTemplate`:

   - `SwipeView.RightItems` should include two `SwipeItem`s titled "Delete" and "Favorite" as shown below.

     **UPDATE MARCH 10**

   - The swipe View is not supported on the windows platform. You must test this on a mobile device.

   - To handle the swipe click, there exists two different ways:


   - **Way 1:** Adding new event handlers for the `Clicked` event of every Swipe Item.

   - Within each event handler you must cast the `sender` object as a `SwipeItem` and use the `BindingContext`:


~~~csharp
 var swipe = (sender as SwipeItem);
 ObservableMessage item = swipe.BindingContext as ObservableMessage;

~~~

- **Way 2**: This strategy has some overhead but can be very useful if you chose to use templated views or view models. Using `Command`s and  `CommandParameter`:

- This method uses `Binding` to bind a command defined in the code behind to the `XAML`:

  ```csharp
  public ICommand DeleteCommand {get; set;}
  ```

  ```xml
  Command="{Binding DeleteCommand}
  ```

- You can pass the swiped email itself as a command parameter

  ```xml
   CommandParameter="{Binding .}
  ```

- To implement a simple handler read [Microsoft's documentation](https://learn.microsoft.com/en-us/dotnet/maui/fundamentals/data-binding/commanding?view=net-maui-8.0#icommands)

  

6. Use a `Grid` to define the layout of the `CollectionView.ItemTemplate`

7. Add a `<GestureRecognizer>` on the `Grid` :

   - Add a `TapGestureRecognizer`

   - Add a new event on the `Tapped` event to get the clicked email

     > Hint: In the event handler, cast the sender into a Grid object similarly to the example of the SwipeItems.

     **UPDATE FEB 28:**

   - When tapped, the email should be **downloaded in full** (using the `DownloadEmailAsync()` method, created earlier. 
   
   - The `ObservableMessage` must be marked as `Read` in the original collection of emails as well as on the email server. 
   
     > Hint: Use the `UniqueId` of the tapped email to modify it's flag and find it's position in the collection. 
   
   - A new `ReadPage` should be created and the selected email should be passed as argument.
   
   - **Note 🛠️** Putting this much logic in an event handler is NOT good practice. Let's add a *TODO* here, for when we have a better app architecture. 
   
     

**Figure 2: Inbox Page** 

<img src="images/assignments_images/assignment1_imgs/as1_inbox.png" Height=400 class="inline-img"/>Height=400 class="inline-img"/><img src="images/assignments_images/assignment1_imgs/as1_Inbox_swipe_right.png" Height=400 class="inline-img"/><img src="images/assignments_images/assignment1_imgs/as1_inbox_search.png" Height=400 class="inline-img"/>



6. Add a `Label` with Text "Favorite" and who's visibility is bound to the `IsFavorite` boolean of the `ObservableMessage` model

7. Use a rounded `Frame` or `Border` with the first character of the `Name` as the user icon. 

   *For example: Letter "O" for Outlook Team*

   <img src="images/assignments_images/assignment1_imgs/User_icon.png" Height=100 class="inline-img"/>

   > Hint: You could use a converter from the Sender emails address to the initial's  label. This converter must return the first character of the display name. 

8. (Optional) Generate a background colour for the `Frame` of the user icon based on the user email address.

   > Hint: You must a converter and find a way to translate letters to colour value. 

9. Unread emails (never tapped) should appear differently. Feel free to use any method to distinguish them 

   > Hint: Use Converters to translate the `IsRead` property of an email into a UI attribute such as a colour or a text style, etc..

   

**Figure 3: Unread emails are marked with a blue line, read emails have a transparent line** 

<img src="images/assignments_images/assignment1_imgs/Read_unread.png" Height=200 class="inline-img"/>

9. The "Compose" button should asynchronously push a new `WritePage`.

   

   

   

   ### ReadPage
   
   **Code behind:**
   
   1. The constructor of the `ReadPage` should receive an `ObservableMessage` object as argument.
   3. Use this ObservableMessage as `BindingContext`
   
   **XAML:**
   
   3. Contains:
   
      - A `Label` to indicate the `Subject` bound to the `ObservableEmail`
   
      - A `Label` to indicate the `From.Name`
   
      - Labels for each recipient email`To`:
   
        > Hint: Use `BindableLayout` or a `CollectionView`with the `ItemsSource` set to the list of recipients.
   
      - A `Label` to indicate the date
   
      - Feel free to improve the design to your linking while ensure the basic information is displayed. 
   
      UPDATE - FEB 28:
   
      - To display HTML content, use a`WebView` with `Html` property bound to the Email's `HtmlBody`. 
      - To ensure the user can scroll through the email, it must have a `ScrollView` as parent.
   
      

**Figure 4: `ReadPage` template**  



<img src="images/assignments_images/assignment1_imgs/as1_readpage.png" Height=400 class="inline-img"/>

4. Implement a "Forward" button to:
   - Use the `GetForward()` method to get the forwarded email. 
   - Push a new `WritePage` providing a forwarded email as argument to the constructor.

### WritePage

**Code behind:**

1. The constructor of the `WritePage` might optionally receives an email as input, if the email was forwarded.

   > Hint: Create a default value for the passed argument and use conditional logic in the constructor.

2. Initialize a public readonly property `MailboxAddress`.  Set its value to a mock email using the Config email. 

3. Initialize a public `Observablemessage` property to bind the various entries named `EditEmail`. Set the sender email (`From`) to the email you defined previously.

4. The `EditEmail` can be used as a Binding context for the various entries.

   

   **XAML:**

5. Contains a Send Button with a clicked event.

6. Contains an entry bound to the `CurrentEmail`

   - Should be `ReadOnly`

   - Bind it to the `SenderEmail`

7. Contains a entry for the recipients emails:
  - The `Keyboard` should be set to `Email`
  - Assume the inputted emails are separated by `"';'" `
  - Use the `Completed` event to parse the emails entered by the user

8. Contains an entry for the Subject

9. Contains an `Editor` bound to the email Body.

   - Set its`HeightRequest` to at least 500:

   **Figure 5: `WritePage` template** (left - forwarded email , right - new email)

<img src="images/assignments_images/assignment1_imgs/foward_writepage.png" Height=400 class="inline-img"/><img src="images/assignments_images/assignment1_imgs/as1_writepage.png" Height=400 class="inline-img"/>

**UPDATE FEB 28:** 

**Event handlers**

- The recipients emails event handler should use `MailboxAddress.TryParse()` to validate the formats of the email. 

- The send button event handler should display alerts if the the Subject or recipients field are empty: 

  <img src="images/assignments_images/assignment1_imgs/alert_write_page.png" height=400/>



## Additional notes

- I suggest drawing a quick wireframe of your app to help you define the views and their interactions with the model

- For all image button icons, download them from [flaticon](https://www.flaticon.com/) or [icon8](https://icons8.com/icons/set/library) 

  

## Helpful Tips

A few new concepts are introduced in this design:

#### **Value Converters**

**What are "Binding Value Converters"?**

- .NET Multi-platform App UI (.NET MAUI) data bindings usually transfer data from a source property to a target property, and in some cases from the target property to the source property.

- This transfer is straightforward when the source and target properties are of the same type, or when one type can be converted to the other type through an implicit conversion.

  **When that is not the case, a type conversion must take place.**

To achieve this task you need to write some specialized code in a class that implements the `IValueConverter` interface. Classes that implement `IValueConverter` are called *value converters*, but they are also often referred to as *binding converters* or *binding value converters*.

***Example***

*(Do not type this code into your project. This is only for explanation purpose and will be discussed in class)*

- Suppose you want to define a data binding where the source property is of type `int` but the target property is a `bool`.

- You want this data binding to produce a `false` value when the integer source is equal to 0, and `true` otherwise. This can be achieved with a class that implements the `IValueConverter` interface:

  ```csharp
  public class IntToBoolConverter : IValueConverter
  {
      public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
      {
          return (int)value != 0;
          /*  - 'value' is the passed property from xaml, hence requires casting
              - Casting could fail, so adding a try/catch with a default return value 
               is a good programming practice to avoid app crashes */ 
      }
      public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
      {
          return (bool)value ? 1 : 0;
          /*    Converting back to original value. 
              Not all conversions are resersable */
      }
  
  ```

  How to use the created converter class in your view `xaml` code?

  ```xml
  <!-- 1. Add the converters namespace to the XAML markup extensions -->
  <ContentPage ...
               xmlns:converters="clr-namespace:ProjectNamespace.Converters"
               ...>
      <StackLayout Padding="10, 0">
          ...
  <!-- 2. Assign the 'Converter' property  -->
          <Button Text="Save"
                  IsEnabled="{Binding Value, Converter={converters:intToBool}}" />
          ...
      </StackLayout>
  </ContentPage>
  ```

  

#### Dependency injection

In this assignment you will be reading/writing emails displayed in a list of email. The list view of the emails must be able to pass data to the read and write pages.  

Given that data must be sent from an origin class *`A`* to a destination class *`B`*.

- **First Approach:** Create a public property or method in destination class `B`. Class `A` would create an instance of of class `B` and use the property or method to set or pass the data to the instance. This approach is acceptable if the data is not required to create the object of type class `B`:

  ```csharp
  var b = new B();
  
  b.Data = data; // b.SetData(data)
  ```

  

- **Second Approach:** Create a constructor in class `B` that accepts the data as an arguments. The constructor will be responsible for saving the data for later use if needed.

  ```csharp
  var b = new B(data);
  ```

  The second approach is preferred if the object B requires the data to be constructed.



## Grading Rubric

UPDATED MARCH 3rd

| Evaluation Criteria   | Details                                                      | Worth (/110) |
| --------------------- | ------------------------------------------------------------ | ------------ |
| **UI Design**         | All requested elements available. 5<br /><br />Use of at least a Tab Bar or a Flyout menu 2<br />Use of at least 1 `CollectionView` 2<br />Use of application resources for UI styling 1<br />Use of converters 5 .<br />Use of swipe view 3 .<br />Use of tap gesture recognizer 2.<br /> | 20           |
| **Views code behind** | Correct use of dependency injection 10.<br />Correct use of asynchronous programming in the event handlers. </br>Use of data binding 20.<br />Proper string formatting when required  5.<br /> | 35           |
| **Model Classes**     | Proper class design and use of OOP pillars.<br />`ObservableEmail` Correct error handling if necessary and validation in the setters. | 20           |
| **Service Class**     | Modified `EmailService` class adding fetching functionality and downloading a single email.  Correct error handling if necessary. | 20           |
| **Functionality**     | App does everything and works as expected.<br />App does not crash..<br />A user can view a list of email, send an email, delete and archive an email, mark as favorite... | 10           |
| **Coding Style**      | Use of comments.<br />Use of naming conventions.<br />Avoid the use of magic numbers: define constants when needed. | 3            |
| **Name & ID**         | At the top of all submitted files: provide your name, student ID and assignment number. | 2            |

