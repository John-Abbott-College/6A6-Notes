---
layout: page
title: Milestone 7
permalink: /project/milestone7


---



### Timeline: From Friday, May 16 till May 23 (project submission)

📝**Worth**: 25%

📅 **Due:** 

- Demos: May 23, May 24 and May 27
- Code submission:  May 28 @ midnight

📥 **Submission:** Code pushed on Repo + 45 minutes Demo in person.

[Booking link will be added soon]()



## Deliverables

- **Team Demo** 

  - This is a 45 minutes live presentation.
  - All functional requirements (features) are expected to be working. 

- **Code and Documentation submission**:

  - You may use the days after the presentation to fix minor bugs, update your documentation and address any comments given to you.
  - The final version of your code is fully committed and pushed to GitHub before the deadline.
  - Ensure to have merged all your branches on the main. A penalty of 5% will be applied if I cannot find your final branch. 

  

### Introduction

This is the final Milestone for the project. By the end of Milestone 7 your team should deliver the following:

- App controls actuator devices via Azure IoT Hub.
- App reads and sets Device Twin desired properties via Azure IoT Hub.
- Advanced views finalized to display historical data.
- App Documentation with app setup instructions, details and features.

## IoT Hub Communication

1. Controlling Actuator Devices
   - The mobile app should communicate with IoT Hub to control the state of all actuators (fan, lights, buzzer and door lock).
2. Device Twin Properties
   - The mobile app should read and set the Device Twin properties.
     - This minimally includes the telemetry interval.
3. The app could either use direct method or Device Twins properties to set any of the thresholds.

## App Advanced Views

- App should display all basic telemetry data. This includes any missed data in Milestone 3.
- **Note:** You should include a placeholder for the *Buzzer* in the Geolocation subsystem, event if you do not have this device. You may simply omit it from the views.
- Display of historical data:
  - Since saving data in a storage is optional, use events in the Event Hub partition as historical data.
  - Finalize all views that display historical data. Data can be displayed in different formats depending on your app design. Here are some examples:
    - Graphs.
    - Data grids.
    - Maps.

## App Robustness

The app should be able to function in an online and offline manner. If app loses connectivity, the app should at least not crash and display the last values. Ideally, a visual indication should appear when there is no internet and the reconnection with the IoT Hub should be re-established once the internet is back.

In the demo, you will be asked to run the app in normal mode (not debug mode). Make sure the app does not crash at any stage. Exceptions should be handled properly and should provide informative error messages. In addition, any sensor disconnection should not lead to the app crashing. Your grade will depend on how much your app is robust to "unexpected" errors. 

## Code Quality & Comments

- Refer to Milestone 2 for the details of [Code Quality](https://arefmourtada.github.io/ADIII-W23/#/Project/Milestone2?id=code-quality) and [Comments](https://arefmourtada.github.io/ADIII-W23/#/Project/Milestone2?id=comments-and-documentation).
- Fix any comments given to the team on the code quality of:
  - Separation of concerns between `Views`, `ViewModels` (if applicable) and `Models`
  - Separation of concerns between `Repos` and `Services`
  - Parsing JSON strings
  - Using magic strings or magic numbers in the app.

## Documentation

In the `README.md` file at the root of your GitHub repo, include the following sections:

- ***Team Information***

  - Team name, team letter and team members’ names and IDs.
  - This must be the first section in the document.

- **Project Description **

  - A brief description (200-300 words) of the project including the used hardware and the developed app.
  - This should be the second section in the document.

- ***Contributions*** section: create a table with team members' names and include the following:

  - What did each team member do?

  - How was the work in the project divided?

- ***Connected Objects*** Section's required by the connected objects course.

- ***Mobile App*** 

  **Include the following subsections in it:**

  1. ***App Overview***: 

     1. Provides a descriptive summary of the app (250 words maximum).
     2. Features and functionality of the app (bullet point format)
     3. This section includes snapshots of the final app showing different features.
     4. Remember to add explanatory captions to the snapshots.

  2. ***App UML diagram***. Display an updated version of your UML diagram to explain your models:

     - Focus on the most important models
     - Clearly identify public and private properties.
     - Clearly identify the nature of the links between models if it is important to understand your app.
     - No need to include code behind classes

  3. ***App Setup***: include all setup instructions and configurations needed to run the app **with any IoT hub.** This includes instructions on used connection strings and **how** to acquire them.

     - IoT Hub.
     - Authentication.
     - Database (if used).

  4. ***Future Work*** section:

     1. Include any features from the Design Document from milestone 1 that you did not have chance to work on.
     2. State bugs that have not resolved and yet to be fixed but time does not permit to fix.
     3. Add any new features you think your app should have.

  5. ***Bonus Features*** section: Refer to the instructions below.

     

### Update of GitHub issues

The progress of the project should be reflected on GitHub.

## *Would-like-to-develop* features

In Milestone 1, you have probably identified features that would be nice to develop such as push notifications, alerts settings, sign-up page, etc. Points will be granted for teams who would have implemented these additional features of the app.

## Bonus Features 

Teams implementing any of the following features will be awarded bonus points (Not included in the grading rubric).

1. #### Use of storage in IoT Hub

   - Save telemetry data in a storage of your choice.
   - The app should access the stored data and display it.
   - Add a section in the ***Project Documentation*** with a header **IoT Hub Storage**:
     - State your choice with a brief explanation: storage type.
     - A brief explanation for why it was chosen over other options.
     - List any technical details or advanced configuration.
     - Include snapshots with captions of any configuration in the app or the used third party tool or API.

   

2. #### Support for multiple containers

   - If you chose to support multiple containers:

   - Add a section in the ***Project Documentation*** with a header **Multiple containers**:

     - Provide a short description of how many devices can be added to the IoT Hub

     - Provide an explanation on how do you manage the messages stream and ensure that the processed messages are associated with the selected farming container (use of partitions, `EventProcessorClient`, etc.). Discuss the limits of your solution.

     - Discuss what would be the requirements of the hub to support hundreds of containers and manage billions of messages per day.

       

3. #### User defined controls

   - This may include personalization of the layouts of certain pages such as the user's landing page
   - This may also include automated rules when certain readings reach thresholds to automatically activate a given actuator. 
   - Add a section in the ***Project Documentation*** with a header **Use defined controls**:
     - Provide a brief explanation why such a feature is an added value to the product
     - Provide instructions on how to test this feature. 
     - Include snapshots with captions. 

   



## Grading Rubric

| Evaluation Criteria                                          | Worth   |
| :----------------------------------------------------------- | :------ |
| **Final Demo**                                               | **5%**  |
| Well prepared and organized                                  | 1       |
| Respecting the time limit                                    | 1       |
| App is functional (no major crashes)                         | 2       |
| Clear explanation of design choices                          | 1       |
| **Project Documentation**                                    | **5%**  |
| App Overview & future works                                  | 1       |
| App Setup                                                    | 2       |
| UML diagram                                                  | 2       |
| **Milestone 7 - App Dev**                                    | **15%** |
| IoT Hub Communication                                        | 5       |
| App Advance Views                                            | 3       |
| App Robustness App does not crash (Exceptions handled properly) App checks for connectivity App provides proper informative error messages. | 3       |
| *Would-like-to-have* features                                | 1       |
| User Roles (clear separation of views)                       | 1       |
| Code Quality & Comments                                      | 0.75    |
| GitHub issues Updates                                        | 0.25    |

