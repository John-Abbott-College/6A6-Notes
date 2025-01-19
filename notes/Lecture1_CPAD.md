# Cross-Platform App Development Frameworks
The following notes were based on Matt Milner's course on .NET MAUI

## Computer Applications

- Building a single codebase for a software and have it run on different platforms is not a new concept in programming.
- Having a mobile app became a business need. 
- Before smart phones, the focus of application development was developing applications for personal computers.
- Platforms: Windows, Linux, MacOS. 
- The aim of cross platform in these application is to create a compiled (ready) app that would run on all platforms without issues.


## Main Problem

<img src="images/cpad/img1_mainproblem.png" width="400" style="vertical-align: middle;" title="Main issue with native apps"/>


*Main issue with native apps*
- In C\C++, a written code had to be recompiled for each platform.
- Sometimes the code required some modifications to support some OS specific feature. 
  (especially when accessing local resources)
- Question: How can C++ code be used in MacOS if the native language is Swift?

**Summary**

- The process is repeated for each operating system.

| <img src="images/cpad/img4_nativeAppsGen.png"/> |
| :------------------------------------------------: |
|       *Cross platform programming languages*       |

## Mobile Apps - Frameworks

- Having a mobile app became a business need. 

- One of the best ways to grow customer base.

- When developing a Web, Mobile or Hybrid App for multiple platforms, you need to know multiple programing languages, architectures and the specify of each platform.

#### Types of Mobile App Frameworks:

- Native
- Web Apps
- Progressive Web Apps
- Hybrid Apps
- **Cross-Platform Native** Apps (Focus of this course)

##### 	How to Choose a Mobile App Framework?

- Target platform.
- Required app features.
- App performance: 
  - Data display, data analysis, gaming, graphics, etc.
- Timeframe to build the app:
  - Making the app.
  - Marketing the app.
- Budgets:
  - Assigned to development cost.
  - Assigned to maintenance and updates.
- Expertise: Knowledge of programming languages and IDEs. Is hiring required?

### Native App Development

- Know as 1st party native.

- Tools created by the creators of the mobile operating systems: Android and iOS. 

  - Google: Android Studio – Java & Kotlin.

  - Apple: iOS & XCode toolkit – Objective C & Swift.


- A mobile app framework is required to build apps.

- A framework usually includes an IDE and SDK. 
- In App Dev II course you worked with the Android Framework.
  - IDE: Android Studio.
  - SDK: Android SDK.
- In this course we will study other frameworks.



**This can quickly become frustrating**

### Why Native or Why not Native?

| <img src="images/cpad/img2_advantages.png" height="400" /> |
| :----------------------------------------------------------: |
|                     *Advantages of CPAD*                     |

# Cross Platform Types

- The idea behind CPAD is to use interfaces that unify the code development across various platforms.


## Web Apps

- Responsive web pages.
  - Use widely spread (and tested) web technologies.
  - Single codebase created for all platforms. 
- Hosted on web servers 
  - Not available via the app store.
  - It's a dynamic apps (uses an application server) unlike web pages which are static.
- Accessed via **mobile browser**. 
  - Require URL required.
  - Require Internet connection. (Is there Offline access!!)
- **Advantages:**
  - Not using any space on device
  - Convenient to create.
  - Much easier to maintain (no need to send updates to users)
  - Cheaper and faster to develop (great for small businesses)

- **Disadvantages:** 
  - Lack the look and feel of native apps.
  - Lack access to all features a mobile can offer. (not all hardware can be accessed: GPS, Camera, Mic, etc.).

- **Examples of Web Apps**: 

  - [Youtube.com](https://youtube.com)

    

## Progressive Web Apps (PWAs)

- A website that is running locally on the mobile device.

  - UI created using web technology tools.
  - Supports standardized technologies. (responsive design & web push notifications).
  - Single codebase created for all platforms. (Web development)

- App not available **via store**.

  - Can be installed locally to device: downloaded from website. 
  - Runs as installed app: can cash web pages to run offline. 
    (uses manifests & JavaScript service workers)
  - Feels like native app: have an icon and do not run in the browser. 

- **Advantages**:

  - Quick development cycle
  - Much smaller apps

- **Disadvantages:**

  - Special UI elements must be created within web framework to mimic native UI and UX. 
  - Has access to many mobile APIs but not all.
  - **Slower** performance than native apps. 

- Examples of framework:

  - **Flutter**: Open-source UI kit developed by Google. It integrates easily with Android Studio and Visual Studio. The logic is written in Dart, the UI is written in Flutter Widgets. It can be deployed very quickly, dart is also a declarative language which helps developers focus on writing code quickly.  

  - Example of Flutter app:
    - [Google Classroom](https://classroom.google.com/)

  


  | <img src="images/cpad/img3_FlutterGen.png"  height=300 style="margin-bottom: 20px;"/> |
  | :----------------------------------------------------------: |
  |                    *Flutter Architecture*                    |

- To install the App, you must use the browser, then select the option of downloading the app. 

- Examples of PWAs: 

  - [Starbucks App](https://app.starbucks.com)
  - [Pinterest](https://www.pinterest.com/)

  

## Hybrid Native Apps

- Apps are derived from web apps built using HTML, CSS, & JS. 

  - Single codebase for mobile platforms.

- A hybrid app framework converts a web app into a **native app**. 

  - Framework is responsible for building the native app.
  - Packaging the web app properly for the target platform to run locally.
  - Additional plugins are available to provide access to most device native capabilities which WebView cannot access.

- Generated app looks like a native app.

  - Includes all resources needed locally (no download needed).
  - Uses `WebView` component built into all mobile OSs.
  - Can be deployed in an app store.

- **Disadvantages:**

  - Has slower performance as it has to run through the WebView component.

- Examples: 

  - **Cordova**: Open source command line tool, using Web technologies (HTML5, CSS5, JS) to build applications then converts them into native applications.  It enhances the Native Web View components on each supportive platform. It establishes a link between the Web App and the native platform using plugins.

  - Other:s **NativeScript**, **Ionic** (hybrid mobile apps)

    


| <img src="images/cpad/img6_hybridGen.png" height=250/> |
| :-------------------------------------------------------: |
|           *Hybrid Native Apps Building Process*           |



## Cross-Platform Native 

- Cross-Platform App Development `CPAD`

- Use programming languages that are not native to the mobile OS. 

- Cross-platform frameworks’ features:

  - Write a single codebase: for UI and business logic.
  - Provide native UI and UX.
  - Have access to all available APIs in the target platform.
  - Compile code & generate apps that run natively for each supported platform.

- The result is an app that is virtually indistinguishable from any other native app.

- Example of CPADs:

  - **React Native**: Open source. Similar to MAUI, react views are created and react handles it to generate native controls. It was created by facebook using a command-line interface (based on Javascript, React.js, ECMAScript, JSX)


| <img src="images/cpad/img10_ReactNativeGen.png" height=300/> |
| :----------------------------------------------------------: |
|                 *React Native Architecture*                  |

  - **MAUI (previously Xamarin)**: The same business logic  (written in C#) for all platforms, UI code (written in C#/XAML) will be adapted by MAUI for the various targets. (XAML controls -> Native controls). It has an excellent performance, near native app performance.

| <img src = "images/maui_intro/maui_cpad.png" height=300/> |
| :----------------------------------------------------------: |
|           *.NET Multi-platform App UI (.NET MAUI)*           |

  - Progressive Web Apps: CPAD for modern Web Browsers (load content while offline, responsive design)

  - Others: **Blazor** (also uses C# but the UI is defined with html and CSS which is used for Web UI), **Corona SDK** (Gaming CPAD uses LUA programming language with full access to the native system), 

- C# is already cross-platform

- .NET MAUI (previously Xamarin) makes XAML UI cross platform

- Advantages of CPAD:

  - Single unified interface to generate native code on each platform: iOS, Android, Windows, Mac, etc.
  - Code re-use
  - Maintenance
  - Learning only one technology 
  - Cost saving for company (only one team needed)

- Disadvantages of using CPAD: 

  - Security
  - Use cases where CPAD is not the way to go: if we need to directly interface with the hardware, use of CoreOS libraries, Speed requirements



[Additional notes on CPAD](/lectures/cpad/additionalnotes)

- Cross-Platform App Development `CPAD`
- Use programming languages that are not native to the mobile OS. 
- The programming language itself is cross-platform.
- Includes multiple SDKs for the various platforms.

| <img src="images/cpad/CPADprocess.png" /> |
| :--------------------------------------------------------: |
|                  *CPAD Building Process*                   |

| <img src="images/cpad/CPADTypes.png"/> |
| :-----------------------------------------------------: |
|                    *CPAD App Types*                     |

### Drawbacks of using CPAD

- Performance
  - Even though CPAD frameworks claim to have a performance matching a native app, native apps tend to perform faster. 
  - Users will *NOT* notice the difference in most cases.
- App size
  - Native apps tend to have a smaller size after compilation.
  - Apps created using CPAD will be larger in size due to the fact that additional elements are added during the converting process.
- Constant Updating
  - When Google or Apple issue an update, an additional feature or a new API, CPAD frameworks must implement them and in a timely manner. 

## Popular Cross-platform Frameworks

<img src="images/cpad/Cordova.png" width="50%" />

- Opensource `Hybrid` app development framework by Apache.
  - Single codebase that generates native Android and iOS apps.
- Languages used: 
  - JavaScript
  - UI: HTML5 & CSS3 for UI.
- Command line based: 
  - No IDE: web component built using the developer’s editor of choice. 
- Process:
  - Build web application. 
  - Cordova converts web app into native app. 
  - Cordova provides a large set of plugins to access native capabilities via JavaScript. 
- Excellent when a web app already exists and needs to be converted it into an app. 

---

<img src="images/cpad/ReactNative.webp" width="50%" />

- Opensource `Cross-platform Native` app framework.
  - Single codebase that generates native Android and iOS apps.
- Based on Facebook's popular React JavaScript library. 
  - Created by Facebook because the performance of the hybrid mobile apps was not satisfactory. 
- Languages used: 
  - JavaScript & ECMAScript
  - UI: JSX & React Native components.
- Command line based. 
- React Native outputs a Native app that can be opened and extended in Android Studio or XCode.

---

<img src="images/cpad/Flutter.webp" width="40%" />

- Opensource `Cross-platform Native` app framework by Google.
- Single codebase that generates:
  - Native Android and iOS apps.
  - Web Apps.
  - Desktop apps. 
- Languages used: 
  - Dart. (relatively new).
  - UI: Flutter Widgets.
- Integrates easily with Android Studio & Visual Studio.

---

<img src="images/cpad/Ionic.png" width="35%" />

- Opensource `PWA` app framework.
- Single codebase that generates mobile apps.
- Languages used: 
  - Ract.
  - Angular.
  - Vue.
  - HTML5, CSS and JavaScript.
- Utilizes `Cordova` plugins that allow access to in-built device features such as Audio Recorder, GPS, and Camera..

---

<div style="text-align:center;">
    <img src="images/maui_intro/maui.jpg" width="30%" class="inline-img"/>
    <img src="images/cpad/apps.png" width="40%" class="inline-img" />
</div>



- `Cross-platform Native` app framework by Microsoft.
- .NET MAUI is the successor of Xamarin.Forms.
- Many changes brought to Xamarin.Forms.
- Will be discussed in more detail in this course.

---

## Other frameworks worth mentioning

<img src="images/cpad/Solar2D.svg" width="35%" />

- Opensource Cross-platform.
- 2D Gaming engine.
- Singe codebase to create gaming apps for :
  - Mobile.
  - Desktop.
  - Connected TV devices.
- Uses `Lua` programming language.

---

<img src="images/cpad/CodenameOne.png" width="35%" />

- Opensource Cross-Platform app framework. 
- For `Java` lovers.
- Single codebase to build:
  - Native apps for Android, iOS.
  - Desktop apps.
  - Web apps.
- Installs as a plugin in Eclipse, NetBeans, and IntelliJ.


## Additional Resources:

- [Flutter Tutorial](https://codelabs.developers.google.com/codelabs/flutter-codelab-first#3)
- [Mobile App vs Web App: What’s the difference?](https://www.browserstack.com/guide/mobile-app-vs-web-app)
- [Brief History of Apps](https://www.captechu.edu/blog/brief-history-of-mobile-apps)
- [What is a mobile App](https://edu.gcfglobal.org/en/computerbasics/understanding-applications/1/)
- [National App Day](https://www.nationaldayarchives.com/day/national-app-day/)
- [Native vs CPAD](https://www.netsolutions.com/insights/cross-platform-app-frameworks-in-2019/#the-difference-between-native-and-cross-platform-app-development)
- [PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)
- [Flutter](https://flutter.dev/)
- [React Native](https://reactnative.dev/)
- [Cordova](https://cordova.apache.org/)
- [Ionic](https://ionicframework.com/)
- [Solar](https://solar2d.com/)
- [Codename](https://www.codenameone.com/)

