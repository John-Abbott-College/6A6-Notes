# Lab 2 - Tip me with binding

This lab will help you refresh your knowledge of XAML data binding and get familiar a little bit closer to having a proper app architecture.

* **Worth**: 2%
* 📅 **Due**: February 17, 2025 Demo (end of class) , Code (Midnight)
* 🕑 **Late Submissions**: Deductions for late submissions is 10%/day. 
  *To a maximum of 3 days. A a grade of 0% will be given after 3 days.*
* 📥**Submission**: Demo of the UI in class + code submission through GitHub classroom.
* GitHub classroom links:
  * [Section1](https://classroom.github.com/a/qrg82UQU)
  * [Section2](https://classroom.github.com/a/OjCA4Mq7)


## Objective

- Create models to encapsulate the business logic of an app

- Use event-based programming to update the view

- Practice using View to View Binding 

- Practice using View to Model Binding

  

## Project Preparation

- Once your private repo is created, clone it.
- This repo contains an incomplete `.NET MAUI App` 
  - Project Name: `BindingTips`
  - ⚠️ Target Framework: **.NET 8.0**
  - Contains a `Models`,`Views `and `DataRepos` folders to keep the classes organized.

#### Target platform

This lab can be tested on the various platforms:

- Android Emulator
- Windows Machine
- (Optional) iOS Simulator



## Context

The provided starter code is for an app designed for restaurant diners who want to split their bill. It allows users to enter the bill amount before taxes, select their province, and set a tip percentage. The app then calculates and displays the total before taxes, the tax and tip amounts, and the final total after applying both. Lastly, it shows the split amount.

## User Interface

Modify the `MainPage.xaml` to add functionality to the tip calculator App using data binding. 

<img src="images/labs_images/Lab2/m1.png" height="400"/>

## UI - Modifications

- **`ImageButton`s** 

  - All image buttons should adapt to the app's theme (light or dark mode).
  - For instance, the information button should be set to`Source`=`"info.png"` if the app is in Light mode, otherwise `"info_dark.png"`
  - Read more about [App Theme Binding](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/system-theme-changes?view=net-maui-9.0) 

- **Bill Amount Before Tax: `Entry`.**

  - The placeholder text should display **$0.00**

  - Implement a `TextChanged`event handler and give it an appropriate name.

  - Input should allow numeric values only

      <img src="images/labs_images/Lab2/m2.png" height="400"/>

- **Tip Percentage: `Slider` and `Label`** 

  - Assign an  `x:Name` to the slider for reference by other UI elements.

  - Set the tip range to be between 0% and 100%

  - Update the tips label for tip value must be updated using `XAML`(not in the associated `C#` code behind).

  - The displayed tip value should increase by 1. **No decimal should be displayed.**

    > Hint: Explore events that are triggered when the slider is changed. Use the code behind to handle the event triggered by the slider. 

    <img src="images/labs_images/Lab2/m7.png" height="400"/>
    
    

- **Canadian Province: `Picker`** 

  -  Assign a `x:Name` so that other UI elements may reference the Picker.

  -  Use a class model to populate the picker with Canadian provinces. 
    (`Province` class details are provided in the next section).

  -  It’s recommended to complete the picker after finalizing the models and static classes.

  -  Populate the picker using its `ItemsSource` property:
    - An easier way is to add all provinces using an array in the same manner as the`CollectionView`  in the example seen in class.
    - You can then use the `ItemDisplayBinding` to set the attribute to display of each item contained in the List

    <img src="images/labs_images/Lab2/m3.png" height="400"/>

- **Tax Rate `Label`**: 

  - Positioned below the picker, this`Label` displays the HST/GST Tax for the selected province.
  - The value must be updated through **binding only**, without modifying it in the code-behind.

- **Tip Amount `Label`** 

  - Displays calculated the tip amount.
  - Formula: `Tip Amount = (Tip Percent) /100 x Bill Amount before Tax`

- **Total Amount `Label`**:

  -  Displays total bill amount including provincial tax and tip.
  - Formula: `Tax Amount = (Provincial Tax) /100 x Bill Amount before Tax`
  - Formula: `Total Amount = Bill Amount before Tax + Tax Amount + Tip Amount`

- **Split Options:**

  - Allows user to split the total amount based on a split value (number of people sharing the bill).
  - Displays how many people are splitting the bill and increase/decrease this value
  - **Note: The split count must always be 1 or higher**

  

## Models

Organize your project by adding any model in the `Models` folder. Add the following new classes:

- `Province` class: represents each province basic information. In this app you require:
  - the province's name : `string`
  - the province's HST\GST Tax : `decimal`
- `Bill` class: this class should include all the information related to the bill and the total amount calculation:
  - Extract the **concepts** from the app design above, for example: Total amount, amount before taxes, tax amount, tip amount, number of people splitting the bill, etc.
  - Respect the OOP Pillars:
    - **OOP Encapsulation:** avoid exposing unnecessary information in the `Bill` class and hide **all calculations**. 
    - **OOP Abstraction:** all variables related to the `Bill` class should be defined in the class.  Avoid adding these variables in the code behind of the `XAML` page. The code behind should only define an object of the `Bill` class and use its members.
    - **Validation**: You model is not aware of the View and therefore should validate that the value provided to the setters. 
  - (Optional- but recommended) Make the `Bill` class an Observable object that implements the interface `INotifyPropertyChanged` . Read more about it [here](https://learn.microsoft.com/en-us/dotnet/desktop/wpf/data/how-to-implement-property-change-notification?view=netframeworkdesktop-4.8).



> Hint: use calculated properties for the Tax Amount, Tip Amount, Total Amount and the Split Amount.
>
> Note: The classes should be built without having any dependency on the technology used. (on `MAUI` or `XAML`)

## Data Repos

Organize the data you use inside your project by creating a `DataRepos` folder. 

> :information_source: The Repository design pattern creates an abstraction layer between the data access layer and the business logic of an application. This provides a centralized way to access the data across all views in the app and perform certain operations. Separating the data layer from the business logic of the app, makes the design more flexible, easier to test and maintain. 

To keep things simple, add a new static class:

- `Provinces`: Contains a single public attribute which is an iterable (array, list, collection, etc..) of all the Canadian provinces and their respective tax rates: 

  | Province                  | Tax Rate |
  | ------------------------- | -------- |
  | Alberta                   | 5%       |
  | British Columbia          | 12%      |
  | Manitoba                  | 12%      |
  | New Brunswick             | 15%      |
  | Newfoundland and Labrador | 15%      |
  | Northwest Territories     | 5%       |
  | Nova Scotia               | 15%      |
  | Nunavut                   | 5%       |
  | Ontario                   | 13%      |
  | Prince Edward Island      | 15%      |
  | Quebec                    | 14.975%  |
  | Saskatchewan              | 11%      |
  | Yukon                     | 5%       |

## Binding the View with the Model and Repos

- Once the classes are built integrate them to the View. Create an object `Bill` in the code behind (or in `XAML`) and use binding to connect its various properties to the code behind.  

  > Hint: Choose a `BindingContext` for the page and use `{Binding Source=*object*, Path=*attribute*}.

- As for the data repo, simply include its namespace in the code behind and refer to the `Provinces` array, it's a static object!

- I suggest you add breakpoints inside the model's properties setters and validate that they get executed when interacting with the UI elements are modified. 

- If you see some properties not being updated on the screen that is most likely because values are changing without notifying the view:
  - Remember there is many ways of implementing this app. You can use the `INotifyPropertyChanged` interface, or simply call an Update method every time a value has changed.
  
    

## Functionality 

Note that the app design does not include any "Submit" button to invoke the calculation of the tips and total amounts. Use event base programming with the combination of data binding to have the app automatically update the UI elements and models.

## Grading Rubric

| Evaluation Criteria  | Details                                                      | Worth |
| -------------------- | ------------------------------------------------------------ | ----- |
| **Functionality**    | App does everything and works as expected.<br />App does not crash. | 2     |
| **UI Design**        | All requested elements available.<br />Use of at least 2 nested layouts.<br />Use of application resources for UI styling.<br /> | 2     |
| **Data Binding**     | Use of View-to-View binding when possible.<br />Slider--> Tax Rate Label<br />Picker -> HST/GST Label<br />Use of View-to-Code-Behind binding over event handlers:<br />Tip Amount Label to code behind property<br />Tax Amount Label to code behind property<br />Total Amount Label to code behind property<br />Split Amount Label to code behind property | 6     |
| **Model Classes**    | Proper class design and use of OOP pillars.<br />`Province` class (5)<br /> `Bill` class (15) | 5     |
| **App Architecture** | Separation of the logic and the presentation layers of the app<br />*There should be no calculations done in the code behind.* | 2     |
| **Name & ID**        | At the top of all submitted files: provide your name, student ID and assignment number. | 1     |



# References

1. Web agency telorDesign, Calculators and conversion tools:  https://www.calculconversion.com/tip-calculator-canada.html, last accessed on Feb 11 2024

2. Retail Council of Canada, Sales Tax Rates by Province: https://www.retailcouncil.org/resources/quick-facts/sales-tax-rates-by-province/, last accessed on Feb11 2024

3. Hellosafe, [Map] In which countries of the world is it common to tip and how much? :https://hellosafe.ca/en/travel-insurance/tipping-world-map, last accessed on Feb 13