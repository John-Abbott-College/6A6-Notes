# Observer Pattern

The observer-subscriber design pattern is among the fundamental design patterns laid by the famous Gang of Four which addresses a common design problem across all programming languages and frameworks, the need to make responsive applications.  This design pattern defined the original idea behind creating an object, the *subject* being  watched by *observers*. This is where the idea of data binding comes from, where change in one object, the publishercauses a change in another.

Let's first understand the fundamental concepts behind this mechanism:

**Events** are a notification triggered by the publisher object when a specific situation arises.  For example when a Button is pressed, the UI page will fire the event *Button Pressed*. An event is triggered by an object to notify other objects observing it.

**Event-Handlers** are special methods used by the subscriber class to process those events, for example *Button Clicked Handler*. Multiple subscribers with different handlers can subscribe to different events. 

### C# Events

This capacity to send notifications is implemented in C# using the keyword `event`. 

Let's examine the following Subscriber class:



```csharp
class Subscriber
{
    public event EventHandler SomethingHappened; // The event 

    protected virtual void OnSomthingHappened(EventArgs e)
    {
        SomethingHappened?.Invoke(this, e);
    }
    
}
```



```csharp
class Observer
{

}
```



### Additional Notes

- `EventsArgs` is a general class which contains arguments by the event handler. But you can define your own event args class.