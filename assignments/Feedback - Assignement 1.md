# Assignment 1 - Grade

Student name: 

Grade: 

| Criteria      | Explanation                                                  | Points |
| ------------- | ------------------------------------------------------------ | ------ |
| Functionality | The Console App provides all the basic emailing functionalities required in this assignment: </br> | 5      |
| Modularity    | The service class is designed to be flexible and easily extended if needed. | 5      |
| Async         | All service classes are implemented using asynchronous calls | 10     |
| Code Quality  | Error handling and robustness                                | 10     |
| Coding Style  | Use of comments. </br>Use of naming conventions. </br> Avoid the use of magic strings within the classes definition. | 5      |



**Functionality (/5)**

The Console App provides all the basic emailing functionalities required in this assignment:

- [ ] Connecting and authenticating the user
- [ ] Sending an email
- [ ] Downloading all emails in the inbox
- [ ] Deleting an email

**Modularity (/5)**

- [ ] The `EmailService` uses the interface `MailKit.IMailTransport` to refer to the sending client
- [ ] The `EmailService` uses `MailKit.IMailStore` to refer to the receiving client 
- [ ] The service class can be used with different send/receive clients, i.e.: no specific instance of the clients are referenced in the `EmailService` implementation
- [ ] Isolating the connection and authentication in a method or class which can be modified if Auth 2.0 is implemented

**Asynchronous (/10)**

- [ ] Using `ConnectAsync()` instead of `Connect()`
- [ ] Using `AuthenticateAsync() `instead of `Authenticate()`
- [ ] Using `SendAsync() `instead of `Send()`
- [ ] Using `GetMessageAsync()` instead of `GetMessage()`
- [ ] Same of  `OpenAsync()` ,`StoreAsync()`, `ExpungeAsync()`
- [ ] Using `await` everywhere the async method is called
- [ ] Method signatures are using `async`

**Code Quality (/10)**

- [ ] Use `try/catch` to handle exceptions (in general) 3 pt
- [ ] Use `try/catch` within the connexion and authenticate method 1 pt
- [ ] Use `try/catch` within the sending method 1 pt
- [ ] Use `try/catch` within the downloading method 1 pt
- [ ] Disconnecting clients after use 1 pt
- [ ] Disconnecting clients within `except` statement 
- [ ] Verifying if the client is connected or authenticated before attempting to connect/authenticate. 1 pt

**Coding Style (/5)**

- [ ] Code is documented when necessary 
- [ ] All variables names are following expected naming conventions
- [ ] All config strings are instantiated outside the `EmailService` class.



**Comments** 