# Lab 1 - Social Media Layouts

This lab will help you get familiar with the various MAUI layouts and controls. 

- 📝 **Worth:** 2%
- 📅 **Due:** Friday Feb 7, 2025 @End of class
- 🕑 **Late submissions:** 3 days maximum. 10% penalty per day.
- 📥 **Submission:** Demo In class (1%) + Code submission on Github (1%)

## Objectives

- Create the UI skeleton for a social media app using `XAML`
- Review concepts such as button event handlers, display prompts and action sheets.
- Get familiar with MAUI's basic layouts
  - Grid Layout
  - Vertical and Horizontal Stack Layout
  - Flex Layout




## Create a MAUI Project

- Accept the Github classroom lab

- Clone the repository created for this effect

- Create a .NET MAUI Project

- Name: **MauiSocial**

- ⚠️ IMPORTANT USE **.NET** **8**  

  > Note: This version is installed on the school computer and the one tested for the instructions below.

- Note that the  `Resources > Images` folder contains button images and icons

  >  Note: Place all the images at the root of the `Images` folder, if you wish to keep them organized in subfolders, you must add each subfolder content to app embedded resources 

#### Target platform

For this lab we will be testing the app on :

- Google Pixel 5, 6 or 7
- Tablet 420 dpi 8in

> If you wish to develop on a Mac computer and test on the iOS emulator, the instructions shouldn't be different. But I haven't tested all the steps on iOS.



## Modify the Main Page

- Modify the main page to include:

  - A `Title`: `"MauiSocial"`. This can be set in the opening tag of the `ContentPage`:

    ```xml
    <ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 Title="Maui Social"
                 x:Class="MauiSocial.MainPage">
    ```

    

  - A `Label`: `Text`=`"Learning About Layouts"`, `Style="{StaticResource SubHeadline}"`. Make sure the Label is centered horizontally.

  - A `button`: `Text`=`"Post Page"`, `Clicked` (create a new event handler)

  - A `button`: `Text`=`"Profile Page"`, `Clicked` (create a new event handler)

  - A `button`: `Text`=`"Mosaic Page"`, `Clicked` (create a new event handler)

  

  

  <img src="images/labs_images/Lab1/Lab1_MainPage_threebuttons.png" height=400/>

- Rename your event handlers so that they follow the standard ***Btn_ButtonName_Clicked***

- Remove the unused code in the code behind of the `MainPage.xaml`


## App Navigation

- We will use basic stack navigation.

- Create a folder called Views. 

- Add all newly created pages in this folder.

- Add three new `ContentPage.xaml` with the following names:
  
  - `PostPage.xaml`
  - `ProfilePage.xaml`
  - `MosaicPage.xaml`
  
- Use the event handlers created in the `MainPage.xaml.cs` to push a new instance of each page.

  

## Modify the Post Page

The post page contains all the details of a social media post. To keep it simple, we will only display 1 post and 1 comment for now.

<img src="images/labs_images/Lab1/Lab1_postpage_verticalstack.png" height=400/>



This page should be composed of a main `VerticalStackLayout`. It will contain the following information:

- Information about the owner
- Post image
- Number of likes
- Like, Comment and share buttons
- Comments section

#### Post Owner

The header of the post contains the information about the owner of the post. It is made up of a simple `HorizontalStackLayout` containing the following elements:

- `Image`: 
  - `Source` = "dotnet_bot_jetpack.png"
  - `WidthRequest`=50
- `Label`:
  - `Text` = "DotNet Bot"
  - `FontSize`="Medium"
  - `FontAttribute`="Bold"



#### Post Content: Image and likes

This area is where the image should be added in the main `VerticalStackLayout`:

- `Image`:
  - `Source`="fall.png" (or any other sample photo)
  - `Aspect`="AspectFill"
- `Label`:
  - `x:Name`="LikesLabel"
  - `Text`="0 like"
- `Border`:
  - `BackgroundColor` = "LightGray"
  - `HeightRequest`="3"



#### Like, Comment and Share:

This area is made up of another `HorizontalStackLayout` containing three `ImageButton`, which is an image that is clickable and can be linked to an event handler similar to a Button.

- `ImageButton`:

  - `Source `= "like.png" 

  - `WidthRequest`="25"

    [^1]:  <a href="https://www.flaticon.com/free-icons/like" title="like icons">Like icons created by Freepik - Flaticon</a>

    

  - `Clicked`="Btn_Like_Clicked" (add a new event handler)

- `ImageButton`:

  - `Source`= "comment.png"

  - `WidthRequest`="30"

    [^2]: <a href="https://www.flaticon.com/free-icons/comment" title="comment icons">Comment icons created by onlyhasbi - Flaticon</a>

    

  - `Clicked`: "Btn_Comment_Clicked" (add a new event handler)

- `ImageButton`:

  - `Source` ="share"

  - `WidthRequest`="30"

    [^3]: <a href="https://www.flaticon.com/free-icons/share" title="share icons">Share icons created by Aldo Cervantes - Flaticon</a>

  - `Clicked`: "Btn_Share_Clicked" (add a new event handler)

#### Comment section:

The comment section should contain a single `HorizontalStackLayout` with the following children:

- `Image`:		
  - `Source`="dog.png"

- `Border`:

  - `BackgroundColor`="LightGray"

  - `StrokeShape`="RoundRectangle 10,10,10,10"

  - `Padding`="5"

  - Child:

    - `Label`:
      - `Text`="Cool! the colors are so bright!"
      - `FontSize`="Medium" 

#### Like Event Handler

- In the code behind, add a counter for the number of likes and make sure the likes label is updated every time the like button is clicked:



```csharp
int likesCount = 0;
///...
private void Btn_Like_Clicked(object sender, EventArgs e)
{
    ///...
}
```

 

#### Share Event Handler

- Modify the signature of the event handler to make it run asynchronously:

```csharp
private async void Btn_Share_Clicked(object sender, EventArgs e)
{
   ///\TODO...
}
```



- Show a `DisplayActionSheet` to allow the user to select the platform then show `DisplayAlert` to confirm that the post was shared on the selected platform. Read more about [MAUI Pop-ups](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/pop-ups?view=net-maui-7.0)

> Hint: Use the `await` keyword to save the returned user selection in a `string` variable.

<div>
    <img src="images/labs_images/Lab1/Lab1_share_1.png" height=400/>
    <img src="images/labs_images/Lab1/Lab1_share_2.png" height=400/>
</div>

- Show a `DisplayAlert` to confirm that the post is shared.


#### Comment Event Handler

For simplicity:

- Show a `DisplayPromptAsync` to allow the user to type in a comment and click a "Send" button

  <img src="images/labs_images/Lab1/Lab1_comment_popup.png" height=300/>



## Modify the Profile Page

This page should use a `Grid` layout to organize information on a profile page as shown in the wireframe below.

<img src="images/labs_images/Lab1/Lab1_wireframe_1.png" height="300">



The page should contain the following elements:

- Profile Image at the top left

- Profile Name in bold underneath the profile image

- A bio sentence underneath the profile name

- The number of posts, followers and following laid out horizontally at the top right.

- Two buttons: "Edit Profile" and "Share Profile"

  <img src="images/labs_images/Lab1/Lab1_wireframe_2.png" height="200">

- Images of the posts layout out within each cell of the Grid.



### Defining the Grid

- Modify the default `ProfilePage.xaml` to include the following:

  - Remove the default `VerticalStackLayout` and replace it with a `Grid`
  - The `Grid` must have the following specs:
    - Have as parent the `ScrollView`
    - Columns: 3, adaptable to the screen size
    - Rows : 10, adaptable to the screen size
    - The first row must have a height that is 2 x bigger than all the other rows.
    - `Padding`: 10
    - `RowSpacing`: 5
    - `ColumnSpacing`: 5

### Profile information section

- Add the following element in the `Grid` based on the wireframe:
  - **Profile Image**
    - `Source` = "dotnet_bot_jetpack.png"
    - `MaximumHeightRequest`="300"
  - **Profile Name**
    - `Text` = "Dotnet Bot"
  - **Number of Posts, Followers and Followings**:
    - Use another layout to organize these three labels within the main Grid
    - If needed, use  `&#10;` to end a line.
  - **Follow and Message Buttons**:
    - Use another layout to organize these buttons within the main Grid


### Images Grid

- **Post images**:

  - Each image should fill a cell in the Grid

  - Set the `Aspect`="AspectFill" 

  - You may use the following image URL generator: https://picsum.photos/

  - To make different images, simply modify the dimensions of the image as such: 

    - https://picsum.photos/202/202.jpg
    - https://picsum.photos/224/224.jpg
    - https://picsum.photos/256/256.jpg

    



**Here is an example of the final layout:**



<img src="images/labs_images/Lab1/Lab1_grid.png" height=400 class="inline-img"/>

- Test is out on a different screen size.

<img src="images/labs_images/Lab1/Lab1_grid_win.png" height=350 class="inline-img" /> 



> Note: The buttons are labelled differently in the screenshots.



## Modify the Mosaic Page

This page should be designed using a `FlexLayout`. In later milestones of this app, we will use Binding layouts to connect this page with different posts. 

### **Mosaic of Images**

- Create a `ScrollView`
- Add a`FlexLayout` within the scroll view
- Add 5-7 Images 
  - Set each image's `WidthRequest` and `WidthRequest` so that the images are square and of equal sizes.
- Explore the various properties of this layout

The layout should adapt to the available screen size and use multiple lines to display the images if needed:

<img src="images/labs_images/Lab1/Lab1_flexlayout.png" height=300/>

<img src="images/labs_images/Lab1/Lab1_flexlayout_2.png" height=150/>







**End of the lab!**


### Grading Rubric

| Exercise                                   | Expected Results                                             | Points |
| ------------------------------------------ | ------------------------------------------------------------ | ------ |
| Profile Page - Grid definition             | Use of a `Grid` Layout                                       | 0.25   |
| Profile Page - Element position            | Correct positioning of the items on the grid                 | 0.25   |
| Post Page - Vertical and Horizontal Stacks | Use of a `VerticalStackLayout` and three `HorizontalStackLayout`s | 0.25   |
| Post Page - Like Button                    | Implementation of a likes counter in the code behind         | 0.25   |
| Post Page - Share Button                   | Use of DisplayActionSheet and DisplayAction                  | 0.5    |
| Post Page- Comment Button                  | Text Prompt pops up                                          | 0.25   |
| Moisaic Page - Element position and sizing | Correct positioning and sizing flags                         | 0.25   |

