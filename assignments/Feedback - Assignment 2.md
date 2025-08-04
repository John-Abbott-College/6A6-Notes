# Feedback - Assignment 2

Grade: 22/40

- Model should not contain presentation logic -1

**UI Design** 8 pts

- [x] Collection of emails 1 pt
- [x] `SwipeView` 1 pt
- [x] Converter Is read 2pts
- [x] Converter Is favourite 2pts
- [ ] Tap event 1 pts
- [ ] Search bar 1pts

**Data Binding** 4 pts

- [x] `CollectionView` Item source bound to `ObservableCollection` 1 pt
- [x] Dependency injection of `ObservableMessage` to `ReadPage`  1 pt
- [x] Data Binding Email content inside `ReadPage` 1pt
- [ ] Data Binding Email content inside `WritePage` 1 pt 

**Observable Message** 4 pts

- [x] Observable Properties of a Message 1pt
- [x] Construction from `MimeMessage` 0.5 pt
- [x] Construction from `IMessageSummary` 0.5 pt
- [x] Conversion `GetForward()` 1 pt
- [x] Validation  1 pt

**Service Class** 4 pts

Modified `EmailService` class

- [x]  Adding fetching functionality and downloading a single email.  Correct error handling if necessary. 1pt
- [x] Marking message as read and as favourite 1pt
- [x] Search query email subject and body 1pt

**Functionality **20 pts

- [x] Fetch and view all emails 4 pts
- [ ] View full email 3 pts ->not clickable
- [ ] Send email  3 pts
- [ ] Mark as favourite 2 pt -> -1pt not done on server side
- [ ] Mark as read 2 pt- 
- [ ] Delete 2 pt -> -1pt not done on server side
- [ ] Search by string 3 pts
- [x] Clear on search 1pts

**Coding Style and Name/ID**  1pt

- [x] Use of comments.
- [x] Use of naming conventions.
- [x] Avoid the use of magic numbers: define constants when needed.
- [x] Name and Id present