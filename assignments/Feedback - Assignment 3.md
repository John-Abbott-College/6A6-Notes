# Assignment 3 - Feedback 

# Grade: /30 

### Comments:



## Grade Breakdown

## `MainProgram` 1/1

- [x] Registering the `MainViewModel` as a singleton
- [x] Registering the `ReadViewModel` and the `WriteViewModel` as transients 



## Views 3/3

- [x] Using associated View model to bind all UI elements and commands
- [x] Handling raised events to display UI messages.



## `MainViewModel` 10/10 

- [x] Use of `ObservableProperty` 
- [x] Use of public events 
- [x] Dependency Injection of `EmailService` through constructor
- [x] Private `DownloadEmails()` :
  - [x] Error handling
  - [x] Sets the `IsLoading` flag while emails are fetched 
  - [x] Sets the original list of emails.
- [x] Use of Relay Commands:
  - [x] `MarkAsFavorite`
  - [x] `MarkAsRead`
  - [x] `SearchByString`
  - [x] `DeleteEmail`
  - [x] `TapEmail`
  - [x] `WriteMessage`
- [x] Overall Error Handling



## `ReadViewModel` 5/5

- [x] Use of `QueryProperty`
- [x] Dependency Injection of `EmailService` through constructor
- [x] private method `LoadEmail()`
  - [x] Error handling
  - [x] Sets the email as read
  - [x] Downloads the email in full

- [x] Relay Command `ForwardEmail`
  - [x] Navigates to `WritePage`
  - [x] Passes the forward message (calls `GetForward()`) as query parameter
  - [x] Handles errors

## `WriteViewModel` 6/6

- [x] Use of `QueryProperty`
- [x] Dependency Injection of `EmailService` through constructor
- [x] Use of `ObservableProperties`
- [x] Relay Command `Send`
  - [x] Handles errors
  - [x] Verifies subject
  - [x] Verifies recipient
  - [x] calls service sending method
- [x] Relay Command `ParseRecipientCommand`
  - [x] Correct parsing of emails 
  - [x] Enables sending
  - [x] handles errors
- [x] Reset recipients on entry cleared.



## Unit Tests 5/5

- [x]  All 27 tests are passing including 14 tests for `MainViewModel`, 8 tests for the `ReadViewModel` and 2 for the `WriteViewModel`. 

- [x] `WriteViewModelTest`: Three additional unit tests added including 1 `Fact` and `Theory`