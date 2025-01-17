---
layout: page
title: Milestone 3
permalink: /project/milestone3

---

📝**Worth**: 10%

📅 **Due:** From Monday, April 22 to Monday, Apr 29th @11:59 pm

📥 **Submission:** Code pushed on Repo + UML class diagram + Short presentation video



### Introduction

In milestone 1, you have created a design document which represents a storyboard of how you want your app to function. It will be your roadmap as you proceed to the next step. It is perfectly normal as you proceed in this and the upcoming milestones to have changes and updates on the app design and features.

### App Setup

Create a new `.NET MAUI App` project. Choose a proper name for the project, which will become you app's name. 

> DO NOT CALL IT `Project`. 

#### Link to GitHub

- In the GitHub repository assigned to your team, create a new folder called `Mobile_App`. 
- Link\push the created `.NET MAUI app` to the folder.

#### Project Organization

Create different folders within the app project to keep all the files organized. Use what is relevant to your project from the list below and add any needed ones. 

- Models
- Repos
- ViewModels
- Views
- Resources
- Images
- Files

### Models 

Use the created user stories and hardware devices output to identify possible `models`, `classes`, needed by the app. Building the models first will help you in the next step of building the views. 

In class, we will see one technique used to design classes by extracting concepts and creating a conceptual diagram. Similar to what you've done in the database course. Then you can use a simple [`UML` diagram](https://www.youtube.com/watch?v=6XrL5jXmTwM&ab_channel=LucidSoftware) to help you define classes and their fields, accessors and methods. Add this UML diagram to your design document and make sure it is available in your GitHub repository. 

Based on the provided project requirements, the app should have **at least 3 models** one for each subsystem. Always refer back to your design document and feature list as you might need to have more models. 

As you are adding fields and properties to your models, **do not forget**:

- **Validation**: Your model is not aware of the View and therefore should validate that the value provided to the setters.
- **OOP Encapsulation** : avoid exposing unnecessary information
- **OOP Abstraction**: Hide unnecessary details to other classes. For example if you have a method which updates 

### Views

Use the wireframe layouts created in milestone 1 to start the page design in `xaml`. Build the pages and add basic (stacked) navigation to be able to examine the look and feel of the app. 

If a page cannot be created at this stage because it is dependent on a on a story or a feature that is still not introduced or available, simply add a placeholder for it.

As you are designing your Views, **do not forget**: 

- Reduce the logic from the code behind as much as possible.
- Use data binding whenever you can
- Prioritize having all the essential features (you will have time to go back and improve the look and feel of the app)

### DataRepos

At this stage of the project, you still do not have a access to a back-end server to get real time data. Create test repositories that would provide your app with **dummy data** to be able to test different view designs and over all app navigation. Similar to what we have done in Assignment1.



### Code Quality

You are expected to use good coding practices:

- Code Reusability
- Variable naming conventions
- Descriptive naming
- Indentation
- Use of constants

### Comments and Documentation

- Use the `.NET` official documentation style [XML Documentation Comments](https://msdn.microsoft.com/en-us/library/b2s063f7.aspx) for all the `C#` files. Include the `<param>` and `<returns>` tags for methods. The XML comments provide the ability to automatically create documentation files from these comments at compile time. 
- Each class file should include a header with the following information:
  - Team name and number
  - Semester information and date
  - Course name
  - High level description of the class
- Add additional comments on any non-trivial code.
- The `.xaml` files do not need to include any documentation unless you have used code of the internet and need to provide credit to author.

## Deliverables

- A UML diagram of your classes and their interactions with eachother.

- Create a short video (4-6 minutes) that will show the different page designs, navigation and models.

  - **In each explained page or model, state the name of team member who worked on it.**

- Provide a narration describing

  - Each page: its task, name, role.. etc.
  - Navigation: what is the next page you will be navigating to. 
  - Any additional information you think is relevant. 

  *Example: This is the main landing page. It holds a general dashboard that includes settings from all subsystems. If we click on this button we will navigate to the GPS location page...* 

- Once done with the pages, go to Visual Studio to show your models in the video.

  - Mention any details you believe is relevant for the model design.
  - Ensure that it is coherent with the UML diagram. 

### Video Submission
#### Using MS Teams
- Open your private team's channel
- In the Posts tab, look for Meet  Meet now button (camera button) in the top-right corner.
- Select Meet now
- Give the meeting a title "Milestone 3 submission"
- When your ready start the meeting
- Select the More actions icon
- Click "Record and transcribe"
- Start recording
- Share your screen
- Once you are done, simply exit the meeting.
- The video should appear in the comment section of the meeting.
  
#### (Alternative) Using Steam
Do not perform these steps if you have already validated that the video was added to your team's channel.
  
- Add a copy of the video to the GitHub repo for documentation.

- Upload to www.microsoftstream.com using your college credentials.

- Add a tab to your ***team private*** channel with the link from stream.

  <img src="..\images\project_images\Milestone2.1.png" alt="Screen shot" height="50" class="inline-img"/><br>

  <img src="..\images\project_images\Milestone2.2.png" alt="Screen shot" height="200" class="inline-img"/><br>

  <img src="..\images\project_images\Milestone2.3.png" alt="Screen shot" height="200" class="inline-img"/>


## Grading Rubric (/10 points )

| Evaluation Criteria                                          | Worth                                   |
| ------------------------------------------------------------ | --------------------------------------- |
| **User Stories Finalized** <br>Feedback regarding user stories has been incorporated into Jira board. | **1 point**                             |
| **Project**<br>Name.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>Organization. | **1 point** <br>0.25 <br>0.75           |
| **Models**<br/>Class design (separation of concerns).&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br/>Proper use of OOP pillars.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br/>Models shown in video. | **4 points** <br/>1.5 <br/>2.0 <br/>0.5 |
| **Views:**<br/>Pages Design  <br/>Use of data binding <br/>Navigation | **2.5 points** <br/>1<br/>1<br/>0.5     |
| Use of data repositories.                                    | 1 point                                 |
| **Documentation :** <br/>File headers<br/> Methods summaries<br/> UML diagram. | 0.5 point                               |

