# Project settings

In your project, you will have to use many config strings, secrets and API keys. Those values should be kept in a central location and should not be pushed on your project repo. Each student will have their own keys to login to Azure and/or other services. 

## Example

Below is an example of a MAUI project called MAUI Fitness which uses various APIs: `CaloriesApi`, `PhysicalActivityApi` and `FirebaseAPI`

1- Create an `appsettings.json` file and add your config strings, this file should not be pushed on **Github** as it contains confidential information: 

```json
{
  "Settings": {
    ///\ TODO: Add config strings
    "CaloriesApiKey": "ADD YOURS",
    "CaloriesApiUrl": "https://api.api-ninjas.com/v1/nutrition?query=",
    "ActivitiesApiKey": "ADD YOURS",
    "ActivitiesApiUrl": "https://api.api-ninjas.com/v1/caloriesburned?activity=",
    "FireBaseApiKey": "ADD YOURS",
    "FireBaseAuthDomain": "ADD YOURS",
    "FireBaseDbUrl": "ADD YOURS"
  }
}

```

2- Install the following packages:

- Install the `Nuget Microsoft.Extensions.Configuration.Json`
- Install the `Nuget Microsoft.Extensions.Configuration.Binder`

3- Add the file as an embedded resource file in your `.csproj` :

```xml
<ItemGroup>
	<EmbeddedResource Include="appsettings.json"></EmbeddedResource>
</ItemGroup>
```

4- Create a C# class within your `/Config` folder. This will act as a class to help parse the json file:

```csharp

namespace MauiFitness.Config
{
    public class Settings 
    {
        public string CaloriesApiKey { get; set; }
        public string CaloriesApiUrl { get; set; }

        public string ActivitiesApiKey { get; set; }
        public string ActivitiesApiUrl { get; set; }

        public string FireBaseApiKey { get; set; }
        public string FireBaseAuthDomain { get; set; }

        public string FireBaseDbUrl { get; set; }
    }
}
```

5- From your app for example `App.xaml.cs` constructor, read and parse the json file as such:

```csharp
public partial class App : Application
    {
        public static Settings Settings { get; private set; }

        public App()
        {
            InitializeComponent();
            var a = Assembly.GetExecutingAssembly();
            var stream = a.GetManifestResourceStream("MauiFitness.appsettings.json");

            var config = new ConfigurationBuilder()
                        .AddJsonStream(stream)
                        .Build();
            Settings = config.GetRequiredSection(nameof(Settings)).Get<Settings>();
        }
```

6. Now the `Settings` class contain all the config strings at runtime. Note if your app is using .NET's Dependency Injection service, you may want to register the class as a singleton and parse it from the `MauiProgram.cs` instead. 
