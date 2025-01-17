---
layout: post
title: "Lab 5 - Realtime database"
permalink: /labs/lab5
categories: labs


---



1. 📝 **Worth:** 3%  
2. 📅 **Due:** Friday April 19, 2024 @End of class
3. 🕑 **Late submissions:** 3 days maximum
4. 📥 **Submission:** In class



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

-  **✨ Test your understanding**   Why are the CRUD methods asynchronous? 

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





