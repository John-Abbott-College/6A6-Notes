# Software Design 

Software engineers are often **interested** in analysis to design efficient, object-oriented software solutions that are easier to test and scale. Various approaches exist for creating good software design.

One possible strategy is the *logical approach* to software design, which aims to:

1. Break down the problem into objects and classes
2. Group classes into layers (Models, Views, etc.)
3. Create logical connections between classes (inheritance, abstraction, encapsulation, etc.)

The analysis of user needs and requirements is a fundamental first step in this process. Here are a few exercises that can help in designing classes and finding the relationships between them.  

## Concept Map

A concept map could help identify elements from the context of your project that might require a class to hold this information in the app. 

For example the concept of a *user* and a *device*, a `sensor`, an `actuator`, etc. 

A concept can lead to one or more software classes. 

## Class diagram

[Example of a class diagram](https://drive.google.com/file/d/15cU5A-9hA0LoTDaFjEv8ixl_hf3F32-9/view?usp=sharing)

- The class diagram is part of the UML diagram, which shows the various classes in a project and the relationships between them. 

  <img src="images/software_design/uml_links.png" height=200/>

### Composition  ("has a")

In the composition relationship, the class cannot exist without the sub classes. 

<img src="images/software_design/composition_strong_has_a.png" height=200/>

### Aggregation ("has a")

In the aggregation, the class can exist without the sub-class. This is especially true if the class is made of a list of 0,..,n elements of the sub-class. Such as the car which has 4 wheels, if we remove one wheel, the car continues to exist but might not work as intended. 

<img src="images/software_design/aggregation_weak_has_a.png" height=200/>

### Inheritance ("is a")

The inheritance is a relationship where a class is a more specific case of a more general class. For example a square *is a* polygon. A `TemperatureSensor` *is a* `Sensor`.

<img src="images/software_design/uml_links_inheritence.png" height=100/>

### Association ("uses a")

This is the most common relationship between classes, when a class simply refers to another class, uses another class, is linked to another class. For example the *Mechanic* fixes the *Car*. 

#### Multiplicity 

The class diagram is also a good placeholder to specify the intended multiplicity of a relationship (one to one, one to many, many to many). This could also lead to a design of a well designed database. 

<img src="images/software_design/uml_multiplicity.png" height=120/>



### Principle of single responsibility (SRP)

One key benefit of following SRP is that changes to the codebase become more localized. If a class has a single, clear responsibility, updates or bug fixes related to that responsibility are less likely to impact unrelated parts of the code. This concept also applies to functions—keeping them focused makes the code easier to understand and maintain.

Smaller, more focused classes result in cleaner code that's easier to review, refactor, and test. It also makes it easier to distribute work among team members, since responsibilities are clearly separated.

Let’s take a look at an example. Below is a `SecuritySubsystem` class responsible for monitoring door status, controlling the door lock, and detecting motion. It also handles user authentication and connection to the IoT hub:

```csharp
public class SecuritySubsystem
{
    public Sensor DoorState { get; private set; }
    public Actuator DoorLockControl { get; set; }
    public Sensor MotionSensor { get; private set; }

    public Task ConnectToIoTHubAsync()
    {
        // Do something
    }

    public Task AuthenticateUserAsync(string username, string password)
    {
        // Do something
    }
}
```

The problem here is that the `SecuritySubsystem` is handling too many unrelated responsibilities: device control, connection management, and user authentication. When this happens, it's worth asking: *What is the true role of this class? What should it be responsible for at its core?*

If the primary role of `SecuritySubsystem` is to act as a container for security-related sensors and actuators, then handling connectivity and authentication falls outside its scope. These responsibilities can and should be delegated to separate classes—like `IoTConnectionManager` for managing connections and `AuthService` for authentication.

By doing so, we improve modularity, reduce coupling, and make the system more flexible and maintainable.

```csharp
public class AuthService
{
	public Task SignInAsync(string username, string password)
    {
        //Do something
    }
}
```

```csharp
public class IoTConnectionManager
{

    public Task ConnectAsync()
    {
        //Do something
    }

}
```



What if the class relies on those other classes to get its data? Should the `SecuritySubsystem` be aware of the `IoTConnectionManager`? A better approach might be to use dependency injection to provide the data and update values like this:

```csharp
public class SecuritySubsystem
{
    public Sensor DoorState { get; private set; }
    public Sensor DoorLockControl { get; set; }
    public Sensor MotionSensor { get; private set; }

    public void UpdateValues(IoTConnectionManager manager)
    {
        var data = manager.GetData();
        // Update your sensors using the data here
    }
}

```

For the project, your data repos will be responsible of parsing the data received from the IoT hub and this will be done via event and event handling not through requests from the manager as shown above. 



### Some classes aren't following SRP, is it bad ? 

Take the `EmailService` class from the assignment as an example. It could be improved by separating the concerns of connectivity and email functionality. This becomes especially important when authentication methods differ across email providers. Applying the Single Responsibility Principle (SRP) can help clarify the purpose of this class. Another perspective is to split the logic for sending emails from the logic for retrieving them.

Is it always a problem if SRP isn't strictly followed? Not necessarily. It depends on the developer’s judgment. In some cases, responsibilities may be difficult to separate or may feel naturally connected. Over-applying design principles can lead to overly complex code that's harder to maintain. There's no absolute right or wrong in software design, but some choices can make your code and the experience of working with it, simpler and clearer. 

```csharp
using MailKit;
using MimeKit;

public class EmailService : IEmailService
{
    #endregion
    #region CONNECTION AND AUTHENTICATION
    public async Task StartSendClientAsync()
    {
       //Do Something
    }
    public async Task StartReceiveClientAsync()
    {
        //Do Something
    }

    #endregion
    #region EMAILING FUNCTIONALITIES
    public async Task<IEnumerable<MimeMessage>?> DownloadAllEmailsAsync()
    {
       //Do Something
    }


    public async Task SendMessageAsync(MimeMessage message)
    {
        //Do Something
    }

    public async Task DeleteMessageAsync(int uid)
    {
         //Do Something
    }

}

```



### Principle of separation of concerns (SoC)

The principle reinforces the idea of **Separation of Concerns (SoC)**, which promotes writing clean, modular, and scalable code. In object-oriented programming (OOP), SoC is often applied by assigning classes to specific application layers or functional roles.

For instance, the `MainPage` and `MainViewModel` classes each serve a distinct purpose. `MainPage` belongs to the **View layer** and is responsible for presenting data visually. In contrast, `MainViewModel` resides in the **ViewModel layer** and acts as an intermediary, managing logic and state between the View and the Model.