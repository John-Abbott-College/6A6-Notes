# Assignment 1 Mailing Service

* **Worth**: 10%
* 📅 **Due**: February 21, 2024 @ 23:59.
* 🕑 **Late Submissions**: Deductions for late submissions is 10%/day. 
  *To a maximum of 3 days. A a grade of 0% will be given after 3 days.*
* 📥**Submission**: Submit through GitHub classroom. 
* ✅ The use of generative AI is allowed to help improve your solution. But it should not write all your assignment.



## Objective

In this first assignment, you are tasked with creating a simple Email Client Console App with essential email functionalities. This is the first milestone, where you'll create a service class that interfaces with a mail clients to retrieve and send emails **asynchronously**. 

## Requirements 

The Email app must:

- Authenticate the user

- Connect to a mail client 

- Retrieve all emails in the inbox.

- Send an email with a body, title, sender and sender email 

- Delete a given email.

  

**You do NOT have to implement:**

- Detect when an email is sent (no event handlers for now)
- Detect when a new email is received (no monitoring the server for now)
- User interface (this will be done in the next assignment)



#### Used packages

- `MailKit`

  

### MailKit

[MailKit](https://github.com/jstedfast/MailKit) developed by [Jeffrey Stedfast](https://github.com/jstedfast) and is a cross-platform mail client library which uses [MimeKit](https://github.com/jstedfast/MimeKit). It offers authentication functionality as well as emailing functionality using POP3 protocol, Imap and Smtp. It is relatively easy to use for emailing but should be adapted for the needs of your application.

This Nuget package offers a variety of clients for retrieving or sending emails. In this assignment we will be using two: the`ImapClient` which uses the *imap* protocol to retrieve emails, and the `SmtpClient` which uses the *smtp* protocol to send emails. This package uses `MimeMessage` class to hold email information such as the recipients, title, subject, etc.  



### Configuration

To configure the clients, the following values are needed and should be encapsulated in a  `MailConfig` class. 

- Create an interface `IMailConfig` with the public properties below.
- Create a `MailConfig` class which implements the interface

**Required for authentication**

- `string EmailAddress`
- `string Password`

**Retrieving service**

- `string ReceiveHost` 
- `SecureSocketOptions RecieveSocketOptions`
- `int ReceivePort`

**Sending service**

- `string SendHost`
- `int SendPort`
- `SecureSocketOptions SendSocketOptions`

**Optional properties for OAuth 2.0**

- `string` `OAuth2ClientId` 
- `string` `OAuth2ClientSecret`
- `public` string `OAuth2RefreshToken` 



- In the main program, create an instance of the config while setting the values based on the tested mailing host setup in the next section.  

### Testing setup - Gmail

- Create a dummy email address on outlook:

  - Go to [Google  Create Personal Account](https://accounts.google.com/lifecycle/steps/signup/name?continue=http://support.google.com/mail/answer/56256?hl%3Den&ddm=1&dsh=S153843380:1739140042697886&ec=GAZAdQ&flowEntry=SignUp&flowName=GlifWebSignIn&hl=en&ifkv=ASSHykpUGKLOQQZoOowATSzzMO5TYvzJctLuXcX5wodwxYjW5Ic3RPthDBq_2r_cWlpFZb_tDu76oQ&TL=ADgdZ7TVI_5hSfHFkSP4hAQKmqakwXKAxHwR-GGsO_IJipD3-BGJwtEpgmI-N00r)

  - Click Create free personal account

  - Create a new email address

  - Create a new password that you can easily remember

  - Fill in the first name, last name

- Generate a password for the App 

  - Visit the Google Account settings.

  - Select the “Security” tab.

  - Under the “Signing in to Google” section, click on “2-Step Verification” and follow the prompts to enable it.
  - After enabling two-step verification, search for “App Passwords”. You may be asked to re-enter your password.
  - Create a new app, call it "EmailConsoleApp"
  - Copy the 16-character password that is generated into your code. This is the password you should use to test the email client instead of the Google password created earlier.

- Based on [Google's settings for IMAP and SMTP Protocols](https://developers.google.com/gmail/imap/imap-smtp), here are the config values:

  | Config value   | Value                        |
  | -------------- | ---------------------------- |
  | `ImapHost`     | "imap.gmail.com"             |
  | `ImapSocket`   | `SslOnConnect`               |
  | `ImapPort`     | 993                          |
  | `SmptHost`     | "smtp-mail.outlook.com"      |
  | `SmtpSocket`   | `StartTls` or `SslOnConnect` |
  | `SmtpPort`     | 587 (TLS), 465(SSL)          |
  | `EmailAddress` | dummy email created for this |
  | `Password`     | dummy password               |

# `EmailService`

In this part of the assignment, you must implement a class called `EmailService` which provide the basic functionalities described earlier: connecting and authenticating, sending, retrieving all emails, deleting an email. Here is the interface it must implement:

```csharp
public interface IEmailService
{
    	// Connection and authentication
        Task StartSendClientAsync();
        Task DisconnectSendClientAsync();
        Task StartRetreiveClientAsync();
        Task DisconnectRetreiveClientAsync();
		
    	// Emailing functionality
        Task SendMessageAsync(MimeMessage message);
        Task<IEnumerable<MimeMessage>> DownloadAllEmailsAsync();
        Task DeleteMessageAsync(int uid);

}
```

Your implementation should be flexible enough to allow **OAuth 2.0** to be integrated in future iterations of this project as well as using other protocols. Here are a few guidelines to help you with the implementation.

### Authentication

Most mailing services now require **OAuth 2.0** to email access, which involves registering the app with a developer account. However, for simplicity and testing purposes, we will only use **basic authentication** with the generated password. 

Your design should be flexible enough to allow **OAuth 2.0** to be integrated in future iterations of this project.

**Requirements:**

- Before implementing your class, review the following OAuth 2.0 examples:

  📌 [OAuth2 Gmail Example](https://github.com/jstedfast/MailKit/blob/master/Documentation/Examples/OAuth2GMailExample.cs)
  📌 [OAuth2 Exchange Example](https://github.com/jstedfast/MailKit/blob/master/Documentation/Examples/OAuth2ExchangeExample.cs)

- You can authenticate the send and receive client using a similar code: 

  ```csharp
  client.Connect(host, port, socket);
  client.Authenticate(email, generatedPassword);
  ```

- Handle **all exceptions** and print error messages to the console.

- You must do so asynchronously

- Before connecting or authenticating, always verify the state of the `client` to prevent issues:

  - `IsConnected`: Checks if the client is currently connected to the server.
  - `IsAuthenticated`: Checks if the client is successfully authenticated.

- When done using a client, it should be disconnected using similar code:

  ```csharp
  client.Disconnect(true);
  ```

**Testing**

- Create an instance of the class in the `Main()`
- Ensure that the connection to the clients are not throwing exceptions, but if they do, the exception should be handled. 

### Sending emails

A sending client must be **connected and authenticated** before sending emails. Sending clients all implement the `MailKit.IMailTransport` interface.

For reference, here’s a sample code snippet for sending an email using SMTP:
📌 [Sending Messages with SMTP](https://github.com/jstedfast/MailKit?tab=readme-ov-file#sending-messages)

**Requirements:**

- Implement the following methods to send emails of type `MimeMessage`:
  - `SendMessageAsync()`
- Your solution must **handle exceptions** and **log any errors** to the console.

**Testing**

Call the method in the `Main()` and send yourself an email. 

### Downloading and deleting 

A retrieving client must also be **connected** to the email provider's server and **authenticated** before using it. These clients all implement the `MailKit.IMailStore` interface.

For reference, here’s a sample code snippet for retrieving emails using IMAP:
📌 [Retrieving Emails with IMAP](https://github.com/jstedfast/MailKit?tab=readme-ov-file#using-imap)

**Instructions**

- Implement the following method to retrieve emails from the `Inbox` folder:
  - `DownloadAllEmailsAsync()`

- Your solution must **handle exceptions** and **log any errors** to the console.

  

- `Inbox`: A default `MailFolder` that exists on any server

  - `GetMessageAsync(UniqueId)` is the method to use to download an email. 
  - This UniqueId is useful for deleting emails at a later point.

**Testing**

Call the method in the `Main()` and display the title, subject and date of the retrieved emails. Compare with the list of emails in the Gmail app. 

## Deleting emails

As for downloading emails, the retrieve client should be connected and authenticated before deleting an email. To do so, the email is marked as "Deleted", but will not be deleted until the folder is expunged. 

For reference, here’s a sample code snippet:

📌 [Deleting Emails with IMAP](https://github.com/jstedfast/MailKit?tab=readme-ov-file#deleting-messages-in-imap)

**Instructions:** 

- Implement the following methods to delete an email from the `Inbox` folder:
  - `DeleteMessageAsync()`
- You must open the Inbox in Read-Write mode first.
- The `Uid` represents the value of the unique Id assigned by the Imap server when an email appears in a folder.  
- You don't need to worry about this for now, you can simply delete the message with Unique Id 1 to test it out and ensure you have a few emails in the mail box.



# Rubric

| Criteria      | Explanation                                                  | Points |
| ------------- | ------------------------------------------------------------ | ------ |
| Functionality | The Console App provides all the basic emailing functionalities required in this assignment | 5      |
| Modularity    | The service class is designed to be flexible and easily extended if needed. | 5      |
| Async         | All service classes are implemented using asynchronous calls | 10     |
| Code Quality  | Error handling and robustness                                | 10     |
| Coding Style  | Use of comments. </br>Use of naming conventions. </br> Avoid the use of magic strings within the classes definition. | 5      |

